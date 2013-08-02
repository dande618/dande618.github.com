---
layout: post
title: "自定义widget上的字体类型"
date: 2013-08-02 20:13
comments: true
categories: android
---
对于Activity中的TextView之类的现实文字的控件，如果我们要使用自定义的字体，不能在XML里定义，只能在代码中实现。

一般的做法是将字体文件保存在assets/fonts/目录下，然后使用代码

{% codeblock %}
TextView textView = (TextView) findViewById(R.id.textview);
Typeface typeFace = Typeface.createFromAsset(getAssets(),"fonts/Cambira.ttf");
textView.setTypeface(typeFace);
{% endcodeblock %}

而widget使用RemoteView更新界面，无法使用这种方法设置自定义字体。

可以使用以下方法

参见 <http://stackoverflow.com/questions/4318572/how-to-use-a-custom-typeface-in-a-widget>

即使用ImageView代替TextView，自己绘制自定义字体的图片。

{% codeblock %}
public Bitmap buildUpdate(String time) 
{
    Bitmap myBitmap = Bitmap.createBitmap(160, 84, Bitmap.Config.ARGB_4444);
    Canvas myCanvas = new Canvas(myBitmap);
    Paint paint = new Paint();
    Typeface clock = Typeface.createFromAsset(this.getAssets(),"Clockopia.ttf");
    paint.setAntiAlias(true);
    paint.setSubpixelText(true);
    paint.setTypeface(clock);
    paint.setStyle(Paint.Style.FILL);
    paint.setColor(Color.WHITE);
    paint.setTextSize(65);
    paint.setTextAlign(Align.CENTER);
    myCanvas.drawText(time, 80, 60, paint);
    return myBitmap;
}
{% endcodeblock %}

{% codeblock %}
String time = (String) DateFormat.format(mTimeFormat, mCalendar);
RemoteViews views = new RemoteViews(getPackageName(), R.layout.main);
views.setImageViewBitmap(R.id.TimeView, buildUpdate(time));
{% endcodeblock %}
