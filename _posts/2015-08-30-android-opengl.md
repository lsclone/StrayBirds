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
...
GLSurfaceView.setRenderMode(RENDERMODE_WHEN_DIRTY);
...
GLSurfaceView.requestRender();
...
```

####2. GLSurfaceView.Renderer

####3. OpenGL ES

