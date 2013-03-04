---
layout: post
title: "冒泡排序"
date: 2013-02-27 13:22
comments: true
categories: java
---
###问题

　　有一数组a，长度为n，把数组中的元素从小到大重新排列

###思路

　　从0到n-1，两两比较数组中的元素，如果前者大于后者，则交换之(如a[0]>a[1]，则交换a[0]和a[1])。作一趟冒泡排序后，最大值就在最后一个位置a[n-1]上了。然后对余下的0到n-2个元素作第二趟冒泡排序，次最大值就去到倒数第二个位置a[n-2]上了，如此类推。
<!-- more -->
###JAVA代码

　　传入参数：未排序的数组

　　返回参数：排序后的数组

{% codeblock lang:java %}
	public int[] sort(int[] array) {
		int length = array.length;
		int[] sortedArray = new int[length];
		for (int i = 0; i < length; i++) {
			sortedArray[i] = array[i];
		}
		int temp;
		boolean isSort;
		for (int i = 1; i < length; i++) {
			isSort = false;
			for (int j = 0; j < length - i; j++) {
				if (sortedArray[j] > sortedArray[j + 1]) {
					temp = sortedArray[j];
					sortedArray[j] = sortedArray[j + 1];
					sortedArray[j + 1] = temp;
					isSort = true;
				}
			}
			if (!isSort)
				break; // 如果没有发生交换，则退出循环
		}
		return sortedArray;
	}
{% endcodeblock %}

　　由于JAVA数组是引用类型，为了保护原数组，开始时将原数组赋值给另一数组，对后者进行排序。

　　复杂度是O(n^2)。