---
layout: post
title: "【转载】android多种方式实现异步加载图片 "
date: 2013-03-14 14:12
comments: true
categories: android
---
转载来源 <http://www.cnblogs.com/Jaylong/archive/2012/08/08/handler.html>
<!-- more -->
如果异步加载网络图片，要在非UI线程中进行。通常有以下四种方式：

1.handler+runnable方式：

在activity中定义handler,然后用handler.post（Runnable）方法，此时会在主线程中执行，如果是sdk3.0以上会阻塞UI线程，报异常

2.handler+thread+message模式：

在handler中重写handMessage方法，加载网络图片的操作在thread中执行，通过handler发送消息，在handlerMessage中更新UI

3.handler+threadpool(线程池)+message

定义一个线程池，存放5个线程，然后使用handler的post方法执行UI更新操作

4.handler+threadpool+message+softreference缓存

先从缓存中加载图片，如果没有，就从网络中加载，然后保存到缓存，其他步骤同3

获取网络图片的相关代码如下：

{% codeblock %}

package com.allen.utils;

import java.io.IOException;
import java.io.InputStream;
import java.lang.ref.SoftReference;
import java.net.HttpURLConnection;
import java.net.MalformedURLException;
import java.net.URL;
import java.util.Map;

import android.graphics.Bitmap;
import android.graphics.BitmapFactory;
import android.graphics.drawable.Drawable;
import android.util.Log;

public class Utils {    
    /**
     * 获取网络中图片资源
     * @param src 图片地址
     * @return drawable对象
     */
    public static Drawable loadImage(String src){
        URL url=null;
        try {
            url = new URL(src);
        } catch (MalformedURLException e) {
            Log.e("-Utils-->MalformedURLExcetion--", e.toString());
        }
        InputStream is=null;
        try {
            is = url.openStream();
        } catch (IOException e) {
            Log.e("-Utils-->IOException--", e.toString());
        }
        Drawable drawable=Drawable.createFromStream(is, "img");
        return drawable;
    }
    /**  
     * 从网络上下载  
     * @param url  
     * @return  
     */  
    public static Bitmap getBitMapFromUrl(String url) {   
        Bitmap bitmap = null;   
        URL u =null;   
        HttpURLConnection conn = null;   
        InputStream is = null;   
        try {   
            u = new URL(url);   
            conn = (HttpURLConnection)u.openConnection();   
            is = conn.getInputStream();   
            bitmap = BitmapFactory.decodeStream(is);   
        } catch (Exception e) {   
            e.printStackTrace();   
        }   
        return bitmap;   
    }   

    /**  
     * 从缓存中读取  
     * @param url  
     * @return  
     * @throws Exception   
     */  
    public static Bitmap getImgFromCache(String url,Map<String,SoftReference<Bitmap>> imgCache) throws Exception {   
        Bitmap bitmap = null;   
        //从内存中读取   
        if(imgCache.containsKey(url)) {   
            synchronized (imgCache) {   
                SoftReference<Bitmap> bitmapReference = imgCache.get(url);   
                if(null != bitmapReference) {   
                    bitmap = bitmapReference.get();                                  
                }   
            }   
        } else {//從網絡中下載  
            bitmap = getBitMapFromUrl(url);   
            //将图片保存进内存中   
            imgCache.put(url, new SoftReference<Bitmap>(bitmap));             
        }   
        return bitmap;   
    }   
   
}
{% endcodeblock %}

所有源码下载：<http://files.cnblogs.com/Jaylong/loadImage.zip>；

当然还有其他的方式，比如使用anysctask，或者timer，timertask……