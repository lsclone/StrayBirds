---
layout: post
title: Android OpenGL 相关总结
category: 技术
---

####1. GLSurfaceView

* Activity Life-cycle

GLSurfaceView clients are required to call **onPause()** when the **activity** pauses 
and **onResume()** when the **activity** resumes. 

* Handling events

```
class MyGLSurfaceView extends GLSurfaceView {

   private MyRenderer mMyRenderer;

   public void start() {
       mMyRenderer = new MyRenderer();
       setRenderer(mMyRenderer);
   }

   public boolean onKeyDown(int keyCode, KeyEvent event) {
       if (keyCode == KeyEvent.KEYCODE_DPAD_CENTER) {
           queueEvent(new Runnable() {
               // This method will be called on the rendering thread(GLThread)
               public void run() {
                   mMyRenderer.func();
               }});
           return true;
       }
       return super.onKeyDown(keyCode, event);
   }
}
```

* Rendering Mode

renderer draws on-demand by calling **setRenderMode(RENDERMODE_WHEN_DIRTY)**

```
public class MySurfaceView extends GLSurfaceView {
   private MyRenderer mMyRenderer;
   public MySurfaceView(Context context, AttributeSet attrs) {
      this.setRenderer(mMyRenderer);
      this.setRenderMode(RENDERMODE_WHEN_DIRTY);
   }
   public void request() {
      // call mMyRenderer.onDrawFrame() on the rendering thread
      this.requestRender();
   }
}

public class MyRenderer implements GLSurfaceView.Renderer {
   @Override
   public void onDrawFrame(GL10 gl) {
      gl.glClear(GL10.GL_COLOR_BUFFER_BIT | GL10.GL_DEPTH_BUFFER_BIT);
   }
}
```

####2. GLSurfaceView.Renderer

GLSurfaceView.Renderer的三个接口函数都在rendering thread(GLThread)中被调用。

```
@Override
public void onDrawFrame(GL10 gl) {
}
@Override
public void onSurfaceCreated(GL10 gl, EGLConfig config) {
}
@Override
public void onSurfaceChanged(GL10 gl, int width, int height) {
}
```

####3. OpenGL ES

NDK使用opengles，需要添加：

```
#include <GLES/gl.h>
#include <GLES/glext.h>
```

```
android.mk
LOCAL_CPPFLAGS    += -D_OPENGLES
LOCAL_LDLIBS      := -ljnigraphics -lGLESv1_CM -lGLESv2
```

*参考网址*：[OpenGL ES](http://developer.android.com/guide/topics/graphics/opengl.html "Markdown")
