---
layout: post
title: "Android view刷新"
date: 2013-06-07 17:24
comments: true
categories: 
---
Android中对View的更新有很多种方式，使用时要区分不同的应用场合。我感觉最要紧的是分清：多线程和双缓冲的使用情况。 

转载 <http://blog.csdn.net/zreodown/article/details/7301509>

###1.不使用多线程和双缓冲 

这种情况最简单了，一般只是希望在View发生改变时对UI进行重绘。你只需在Activity中显式地调用View对象中的invalidate()方法即可。系统会自动调用 View的onDraw()方法。 

###2.使用多线程和不使用双缓冲 

这种情况需要开启新的线程，新开的线程就不好访问View对象了。强行访问的话会报：android.view.ViewRoot$CalledFromWrongThreadException：Only the original thread that created a view hierarchy can touch its views. 

这时候你需要创建一个继承了android.os.Handler的子类，并重写handleMessage(Message msg)方法。android.os.Handler是能发送和处理消息的，你需要在Activity中发出更新UI的消息，然后再你的Handler（可以使用匿名内部类）中处理消息（因为匿名内部类可以访问父类变量， 你可以直接调用View对象中的invalidate()方法 ）。也就是说：在新线程创建并发送一个Message，然后再主线程中捕获、处理该消息。 

###3.使用多线程和双缓冲 

Android中SurfaceView是View的子类，她同时也实现了双缓冲。你可以定义一个她的子类并实现SurfaceHolder.Callback接口。由于实现SurfaceHolder.Callback接口，新线程就不需要android.os.Handler帮忙了。SurfaceHolder中lockCanvas()方法可以锁定画布，绘制玩新的图像后调用unlockCanvasAndPost(canvas)解锁（显示），还是比较方便得。 

<!-- more -->

。invalidate()函数重绘

{% codeblock lang:java %}
 public class PuzzleView extends View {
     @Override
     protected void onDraw(Canvas canvas) {
      //...
     }
   
     @Override
     public boolean onTouchEvent(MotionEvent event) {
       invalidate();
       //处理逻辑
       invalidate();//刷
    }
}
{% endcodeblock %}

当调用线程处于空闲状态时，会调用onDraw，刷新界面,也就是说，该函数仅是标记当前界面过期，并不直接负责刷新界面（）

{% codeblock lang:java %}
 public class PuzzleView extends SurfaceView implements SurfaceHolder.Callback{
     private SurfaceHolder surfaceHolder;
   
     public PuzzleView(Context context){
       //....
       surfaceHolder = this.getHolder();//获取holder
       surfaceHolder.addCallback(this);
     }
   
     protected void paint(Canvas canvas) {
       //这里的代码跟继承View时OnDraw中一样
     }
   
     public void repaint() {
        Canvas c = null;
        try {
          c = surfaceHolder.lockCanvas();
          paint(c);
        } finally {
          if (c != null) {
            surfaceHolder.unlockCanvasAndPost(c);
          }
        }
     }
 }
{% endcodeblock %}
  