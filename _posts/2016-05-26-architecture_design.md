---
layout: post
title: 架构设计(随笔)
category: 技术
---

* 模拟IOS异步网络通信(NSOperationQueue)

```
@interface URLOperation : NSOperation {
  NSURLRequest* _request;
  NSURLConnection* _connection;
}
- (id)initWithURLString: (NSString*)url;
@property (readonly) NSData* data;
end

@implementation URLOperation

/*
**  work thread enter function
*/
- (void)start {
  if (![self isCancelled]) {
    _connection = [[NSURLConnection connectionWithRequest:_request deleget:self] retain];
    while(_connection != nil) {
      [[NSRunLoop currentRunLoop] runMode:NSDefaultRunLoopMode beforeDate:[NSDate distantFuture]];
    }
  }
}

#pragma mark NSURLConnection delegate Method
- (void)connecton: (NSURLConnection*)connection
  didReceiveData: (NSData*)data {
  [_data appendData:data];
}
@end
```

```
/*
**  asynchronous http request
**  threadpool will pull operation from queue to work thread
*/
- (IBAction)pushButton: (id)sender { // main thread
  NSOperationQueue* _queue = [[NSOperationQueue alloc] init];
  URLOperation* operation = [[URLOperation alloc] initWithURLString:@"http://google.com"];
  [_queue addOperation:operation];
}
```

后续分析：

接受服务器response数据后，回调以观察者(observer)模式添加到主线程消息处理队列实现。

1. [operation addObserver ...];
2. 将operation添加到main thread消息队列中
3. main thread遍历消息队列，执行[operation notify]
4. -(void)notify { loop: [observer update:_data]; }

代码实现：

```
#include <boost/asio.hpp>
#include <thread>
#include <vector>
#include <string>
#include <iterator>
#include <iostream>
#include <deque>
#include <mutex>
#include <set>
#include <memory>

class Observer {
protected:
	virtual ~Observer() {}
public:
	virtual void update(const std::string& data) = 0;
};

class ControlDelegate1 : public Observer {
public:
	virtual void update(const std::string& data) {
		std::cout << "ControlDelegate1 process data: " << data << std::endl;
	}
};

class ControlDelegate2 : public Observer {
public:
	virtual void update(const std::string& data) {
		std::cout << "ControlDelegate2 process data: " << data << std::endl;
	}
};

class Message {
public:
	void notify() {
		for(auto& obser: m_obsers) {
			obser->update(m_data);
		}
	}

	void addObserver(Observer* obser) {
		if(m_obsers.end() == m_obsers.find(obser)) {
			m_obsers.insert(obser);
		}
	}

	void eraseObserver(Observer* obser) {
		m_obsers.erase(obser);
	}

	inline void setData(const std::string& data) { m_data = data; }
	inline const std::string& data() const { return m_data; }

private:
	std::set<Observer*> m_obsers;
	std::string m_data;
};

class MsgQueue {
public:
	MsgQueue() {}

	void push(std::shared_ptr<Message> msg) { 
		std::unique_lock<std::mutex> lck(m_msgMtx); 
		m_msgQue.push_back(msg); 
	}

	std::shared_ptr<Message> pop() {
		std::shared_ptr<Message> msg;
		std::unique_lock<std::mutex> lck(m_msgMtx);
		if(!m_msgQue.empty()) {
			msg = m_msgQue.front();
			m_msgQue.pop_front();
		}
		return msg;
	}

private:
	std::mutex m_msgMtx;
	std::deque<std::shared_ptr<Message>> m_msgQue;
};

typedef std::function<void (const boost::system::error_code&)> timer_callback;
void main(int argc, char **argv) {  

	ControlDelegate1* deleagate1 = new ControlDelegate1;
	ControlDelegate2* deleagate2 = new ControlDelegate2;

	MsgQueue* inqueue = new MsgQueue;
	MsgQueue* outqueue = new MsgQueue;

	std::thread proctd([&]() { //need to be optimized with thread pool
        boost::asio::io_service io;
        boost::asio::deadline_timer timer(io, boost::posix_time::seconds(1));

        timer_callback callback = [&](const boost::system::error_code& err) {
			/*-------- on timer ---------*/
			for(;;) {
				std::shared_ptr<Message> msg = inqueue->pop();
				if(nullptr == msg.get()) {
					break;
				}
				std::string data = msg.get()->data();
				msg.get()->setData(data.append(" end"));
				outqueue->push(msg);
			}
			/*-------- on timer ---------*/

            timer.expires_at(timer.expires_at() + boost::posix_time::seconds(1));
            timer.async_wait(callback);
        };

        timer.async_wait(callback);
        io.run();
	});
	proctd.detach();

	boost::asio::io_service io;
	boost::asio::deadline_timer timer(io, boost::posix_time::seconds(1));

	timer_callback callback = [&](const boost::system::error_code& err) {
		/*-------- on timer ---------*/
		for(int i=0; i<2; i++) {
			std::shared_ptr<Message> msg(new Message);
			msg.get()->setData(std::string("hello ").append(std::to_string(i)));
			if(i%2) {
				msg.get()->addObserver(deleagate1);
			} else {
				msg.get()->addObserver(deleagate2);
			}
			inqueue->push(msg);
		}
		
		for(;;) {
			std::shared_ptr<Message> msg = outqueue->pop();
			if(nullptr == msg.get()) {
				break;
			}
			msg.get()->notify();
		}
		/*-------- on timer ---------*/

		timer.expires_at(timer.expires_at() + boost::posix_time::seconds(1));
		timer.async_wait(callback);
	};

	timer.async_wait(callback);
	io.run();
}
```

添加线程池后的代码实现：

```
/*
** thread pool source: 
** http://threadpool.sourceforge.net/
*/

#include <boost/asio.hpp>
#include <boost/bind.hpp>
#include <boost/threadpool.hpp>

#include <thread>
#include <vector>
#include <string>
#include <iterator>
#include <iostream>
#include <deque>
#include <mutex>
#include <set>
#include <memory>

class Observer {
protected:
	virtual ~Observer() {}
public:
	virtual void update(const std::string& data) = 0;
};

class ControlDelegate1 : public Observer {
public:
	virtual void update(const std::string& data) {
		std::cout << "ControlDelegate1 process data: " << data << std::endl;
	}
};

class ControlDelegate2 : public Observer {
public:
	virtual void update(const std::string& data) {
		std::cout << "ControlDelegate2 process data: " << data << std::endl;
	}
};

class Message;
class MsgQueue {
public:
	void push(std::shared_ptr<Message> msg) { 
		std::unique_lock<std::mutex> lck(m_msgMtx); 
		m_msgQue.push_back(msg); 
	}

	std::shared_ptr<Message> pop() {
		std::shared_ptr<Message> msg;
		std::unique_lock<std::mutex> lck(m_msgMtx);
		if(!m_msgQue.empty()) {
			msg = m_msgQue.front();
			m_msgQue.pop_front();
		}
		return msg;
	}

private:
	std::mutex m_msgMtx;
	std::deque<std::shared_ptr<Message>> m_msgQue;
};

static MsgQueue outqueue;

class Message {
public:
	void notify() {
		for(auto& obser: m_obsers) {
			obser->update(m_data);
		}
	}

	void addObserver(Observer* obser) {
		if(m_obsers.end() == m_obsers.find(obser)) {
			m_obsers.insert(obser);
		}
	}

	void eraseObserver(Observer* obser) {
		m_obsers.erase(obser);
	}

	inline void setData(const std::string& data) { m_data = data; }
	inline const std::string& data() const { return m_data; }

	void process() {
		m_data = m_data.append(" end");
		std::shared_ptr<Message> msg(this);
		outqueue.push(msg);
	}

private:
	std::set<Observer*> m_obsers;
	std::string m_data;
};

typedef std::function<void (const boost::system::error_code&)> timer_callback;
void main(int argc, char **argv) {

	boost::threadpool::pool tp(2); // thread pool initializes with 2 threads.

	ControlDelegate1* deleagate1 = new ControlDelegate1;
	ControlDelegate2* deleagate2 = new ControlDelegate2;

	boost::asio::io_service io;
	boost::asio::deadline_timer timer(io, boost::posix_time::seconds(1));

	timer_callback callback = [&](const boost::system::error_code& err) {
		/*-------- on timer ---------*/
		for(int i=0; i<4; i++) {
			Message* msg = new Message;
			msg->setData(std::string("hello ").append(std::to_string(i)));
			if(i%2) {
				msg->addObserver(deleagate1);
			} else {
				msg->addObserver(deleagate2);
			}
			tp.schedule(boost::bind(&Message::process, msg)); // add task(message) to thread pool
		}
		
		for(;;) {
			std::shared_ptr<Message> msg = outqueue.pop();
			if(nullptr == msg.get()) {
				break;
			}
			msg.get()->notify();
		}
		/*-------- on timer ---------*/

		timer.expires_at(timer.expires_at() + boost::posix_time::seconds(1));
		timer.async_wait(callback);
	};

	timer.async_wait(callback);
	io.run();
}
```

参考： [boost线程池](http://lsclone.github.io/blog/%E6%8A%80%E6%9C%AF/2015/06/29/boost-work.html "boost")
