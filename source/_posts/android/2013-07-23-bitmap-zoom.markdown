---
layout: post
title: "对Bitmap进行缩放"
date: 2013-07-23 20:13
comments: true
categories: android
---
使用以下方法对Bitmap进行缩放  

{% codeblock lang:java %}
  public static Bitmap zoomBitmap(Bitmap bitmap, int width, int height) {
         if (bitmap == null) {
             return null;
         }

         int w = bitmap.getWidth();
         int h = bitmap.getHeight();
         Matrix matrix = new Matrix();
         float scaleWidth = ((float) width / w);
         float scaleHeight = ((float) height / h);
         matrix.postScale(scaleWidth, scaleHeight);
         Bitmap newbmp = Bitmap.createBitmap(bitmap, 0, 0, w, h, matrix, true);
         return newbmp;
    }
{% endcodeblock %}

或者直接使用
{% codeblock lang:java %}
import android.media.ThumbnailUtils;
//... ...
ThumbnailUtils.extractThumbnail(Bitmap bitmap, int width, int height)方法
{% endcodeblock %}
