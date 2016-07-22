---
layout: post
title: boost::asio并发服务器设计
category: 技术
---

####待解决问题：

1. 以epoll为基础， 设计类似boost::asio并发架构模型。
2. 以epoll为例， 如何在handle\_read()函数中，将收到数据添加到ctpl线程池之后，解决socket\_fd挂起问题。
因为异步，所以handle\_read返回后，此时还未修改fd由监听读改为监听写，所以是否仍然会从fd读取数据且再次调用handle\_read。(boost不会，可以放心)
3. 可以参考 [boost高并发网络框架+线程池](http://blog.chinaunix.net/uid-28163274-id-4984766.html "asio")、[Boost.Asio C++ 网络编程](https://mmoaay.gitbooks.io/boost-asio-cpp-network-programming-chinese/content/Chapter5.html "asio")及
*boost_1_59_0\libs\asio\example\cpp03\http\server2*

####设计思路

* 1、参考boost::asio example，子线程执行asyn_read、asyn_write后，回调asyn_read_callback、asyn_write_callback会在主线程执行。

* 2、使用std::deque分发recv到的数据到threadpool。

* 3、使用boost::threadpool或者CTPL线程池处理数据。每条数据加时间戳，超时(eg,.10s)直接关闭连接。

* 4、参考boost::asio example/cpp03/timeouts，优化超时时间。

* 5、使用libcds优化std::deque，使用Lock-free技术。[lock-free探究](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/08/13/lock-free.html "lock-free")

* 6、消息队列deque进一步优化，除了添加时间戳，使用队列优先级和使用磁盘文件缓存队列部分数据。参考：[FIFO消息队列（内存、文件双缓冲模式）](http://blog.163.com/wwxwb_913/blog/static/97685362010851174237 "asio")

* 7、[协程](http://www.cppblog.com/jinq0123/archive/2016/05/20/213553.html "asio")



###初步实现(未考虑分发deque及时间戳)：

*boost\_1\_59\_0/libs/asio/example/cpp03/echo/async\_tcp\_echo\_server.cpp 添加如下代码：*

```
...
#include <ctpl.h>
ctpl::thread_pool tp(8 /* 8 threads in the pool */);
...

class session
{
...
private:
  void handle_read(const boost::system::error_code& error,
      size_t bytes_transferred)
  {
    if (!error)
    {
	  tp.push([this, bytes_transferred](int id){
		  // process task with this->data_
		  printf("recv data: %s\n", this->data_);

		  boost::asio::async_write(this->socket_,
			  boost::asio::buffer(this->data_, bytes_transferred),
			  boost::bind(&session::handle_write, this,
				boost::asio::placeholders::error));
	  });
    }
    else
    {
      delete this;
    }
  }
...
};

...

```

###实现类似http的并发服务(未考虑分发deque及时间戳)：

server:

```
/*
** thread pool source:
** http://threadpool.sourceforge.net/
**
** thread pool features:
**  Policy-based thread pool implementation
**  Scheduling policies: fifo, lifo and priority
**  Size policies: static_size
**  Size policy controller: empty_controller, resize_controller
**  Shutdown policies: wait_for_all_tasks, wait_for_active_tasks, immediately
**  Smooth integration into STL and boost
**
*/

#include <cstdlib>
#include <iostream>
#include <boost/bind.hpp>
#include <boost/asio.hpp>
#include <boost/threadpool.hpp>

using boost::asio::ip::tcp;
using namespace std; // For atoi.

/*
**	thread number = number of cpu core * 2
**	initialize threadpool with 8 threads.
*/
static boost::threadpool::pool tp(8);

class session
{
public:
	session(boost::asio::io_service& io_service) : 
		socket_(io_service), 
		reqContent(nullptr),
		response(nullptr) {}

	~session() {
		if (reqContent)
			free(reqContent);
		if (response)
			free(response);
	}

	inline tcp::socket& socket() { return socket_; }

	void start() {
		// read request header
		boost::asio::async_read(socket_, boost::asio::buffer(&reqHeader, sizeof(Header)),
			boost::bind(&session::read_callback, this,
				boost::asio::placeholders::error,
				boost::asio::placeholders::bytes_transferred, HEADER));
	}

private:
	/*
	**	callback function
	**	call after reading data from socket receive buffer.
	*/
	void read_callback(const boost::system::error_code& error,
		size_t bytes_transferred, size_t type) {
		if (!error) {
			if (type == HEADER) {
				// read request content 
				if (!reqContent) {
					reqContent = (char*)malloc(reqHeader.contLenth);
				} else {
					reqContent = (char*)realloc(reqContent, reqHeader.contLenth);
				}
				assert(reqContent);

				boost::asio::async_read(socket_, boost::asio::buffer(reqContent, reqHeader.contLenth),
					boost::bind(&session::read_callback, this,
						boost::asio::placeholders::error,
						boost::asio::placeholders::bytes_transferred, CONTENT));
			} else { // CONTENT
				// add task to thread pool
				tp.schedule(boost::bind(&session::process_work, this, bytes_transferred));
			}
		} else {
			delete this;
		}
	}

	/*
	**	thread function
	**	process http request content data.
	*/
	void process_work(size_t bytes_transferred) {
		// simulate the process with content according to content type.
		std::string str;
		switch (reqHeader.contType) {
		case MIME_TEXT:
			// simulate the process with text content
			str = std::string("receive text： ").append(reqContent);
			break;
		case MIME_HTML:
			// simulate the process with html content
			str = std::string("receive html.").append(reqContent);;
			break;
		case MIME_JSON:
			// simulate the process with json content
			str = std::string("receive json.").append(reqContent);
			break;
		default:
			str = std::string("receive invalid.");
			break;
		}

		if (!response) {
			response = (char*)malloc(sizeof(size_t)+str.size());
		} else {
			response = (char*)realloc(response, sizeof(size_t)+str.size());
		}

		size_t len = str.size();
		memcpy(response, &len, sizeof(size_t));
		memcpy(response+sizeof(size_t), str.c_str(), len);

		boost::asio::async_write(socket_,
			boost::asio::buffer(response, sizeof(size_t)+len),
			boost::bind(&session::write_callback, this,
				boost::asio::placeholders::error));
	}

	/*
	**	callback function
	**	call after writing data to socket send buffer.
	*/
	void write_callback(const boost::system::error_code& error) {
		if (!error) {
			// read request header
			boost::asio::async_read(socket_, boost::asio::buffer(&reqHeader, sizeof(Header)),
				boost::bind(&session::read_callback, this,
					boost::asio::placeholders::error,
					boost::asio::placeholders::bytes_transferred, HEADER));
		} else {
			delete this;
		}
	}

private:
	tcp::socket socket_;

	/*
	**	simulate http request
	*/
	enum Data_Type {
		HEADER = 1,
		CONTENT,
	};
	enum Mime_Type {
		MIME_TEXT = 1,
		MIME_HTML,
		MIME_JSON,
	};
	typedef struct _Header {
		size_t contType;
		size_t contLenth;
	}Header, *PHeader;

	Header reqHeader;
	char* reqContent;

	/*
	**	simulate http resqonse(reply)
	**	response: header and content
	*/
	char* response;
};

class server
{
public:
	server(boost::asio::io_service& io_service, short port)
		: io_service_(io_service),
		acceptor_(io_service, tcp::endpoint(tcp::v4(), port)) {
		start_accept();
	}

private:
	void start_accept() {
		session* new_session = new session(io_service_);
		acceptor_.async_accept(new_session->socket(),
			boost::bind(&server::handle_accept, this, new_session,
				boost::asio::placeholders::error));
	}

	void handle_accept(session* new_session,
		const boost::system::error_code& error) {
		if (!error) {
			new_session->start();
		} else {
			delete new_session;
		}

		start_accept();
	}

	boost::asio::io_service& io_service_;
	tcp::acceptor acceptor_;
};

int main(int argc, char* argv[]) {
	try {
		if (argc != 2) {
			std::cerr << "Usage: async_tcp_echo_server <port>\n";
			return 1;
		}

		boost::asio::io_service io_service;

		server s(io_service, atoi(argv[1]));

		io_service.run();
	} catch (std::exception& e) {
		std::cerr << "Exception: " << e.what() << "\n";
	}

	return 0;
}
```

client:

```
/*
**	client.cpp
*/

#include <memory>
#include <thread>
#include <iostream>
#include <boost/array.hpp>
#include <boost/asio.hpp>

using boost::asio::ip::tcp;

enum Mime_Type {
	MIME_TEXT = 1,
	MIME_HTML,
	MIME_JSON,
};

typedef struct _Header {
	size_t contType;
	size_t contLenth;
}Header, *PHeader;

typedef std::function<void(const boost::system::error_code&)> timer_callback;

int main(int argc, char* argv[]) {
	try {
		std::shared_ptr<char> response; // TODO std::vecotr<uint8_t>
		boost::asio::io_service io_service;

		tcp::resolver resolver(io_service);
		tcp::resolver::query query(tcp::v4(), "127.0.0.1", "30019");
		tcp::resolver::iterator endpoint_iterator = resolver.resolve(query);

		tcp::socket socket(io_service);
		boost::asio::connect(socket, endpoint_iterator);

		boost::asio::deadline_timer timer(io_service, boost::posix_time::seconds(1));
		timer_callback callback = [&](const boost::system::error_code& err) {
			/*-------- on timer ---------*/
			char content_[] = "hello world";
			Header header_;
			header_.contType = MIME_TEXT;
			header_.contLenth = sizeof(content_);

			boost::asio::write(socket, boost::asio::buffer(&header_, sizeof(Header)));
			boost::asio::write(socket, boost::asio::buffer(content_, sizeof(content_)));

			size_t reslen;
			boost::asio::read(socket, boost::asio::buffer(&reslen, sizeof(size_t)));
			
			response.reset(new char[reslen+1], [](char* p) {
				if(p) delete[] p;
			});
			response.get()[reslen] = '\0';

			boost::asio::read(socket, boost::asio::buffer(response.get(), reslen));

			std::cout << response << std::endl;
			/*-------- on timer ---------*/

			timer.expires_at(timer.expires_at() + boost::posix_time::seconds(1));
			timer.async_wait(callback);
		};

		timer.async_wait(callback);
		io_service.run();

	} catch (std::exception& e) {
		std::cerr << e.what() << std::endl;
	}

	return 0;
}
```
