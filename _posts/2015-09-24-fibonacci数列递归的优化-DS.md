---
layout: post
date: 2015-9-24
title: fibonacci数列递归的优化
categories: DS
---

fibonacci用递归写效率极低，很多次重复计算，如果计算f(50),到后面计算f(3),f(2)的数量会极其庞大，几乎是指数增长，而且计算结果都一样，这里产生了严重的浪费，开销极大，是整个递归的弱点所在
	
	//普通递归求fabonacci
	long long fabonacciBad(int a)   //效率极低
	{
    	if(a == 0)
        	return 0;
    	else if (a == 1)
        	return 1;
    	else
        	return fabonacciBad(a - 1) + fabonacciBad(a - 2);
	}

以上用递归写，尽管很容易理解，但效率极其低下，计算fabonacciBad(50)会花费大约20分钟左右

	//迭代求fabonacci
	long long f[100];
	f[0] = 0;
	f[1] = 1;
	for (int i = 2; i < n; ++i)
		f[i] = f[i - 1] + f[i - 2];

而用一个for循环来写fibonacci则效率会高不少，测任何数据都轻轻松松，但会溢出，这里可以用一个大数模版来弥补

	//记忆化递归
	long long arr[100];
	memset(arr, -1, sizeof(arr));
	long long fabonacci(int a)      //优化后的递归，可以和for写的效率差不多
	{
    	if(arr[a] == -1)
    	{
        	if(a == 0)
            	arr[0] = 0;
        	else if(a == 1)
            	arr[1] = 1;
        	else
            	arr[a] = fabonacci(a - 1) + fabonacci(a - 2);
    	}
    	return arr[a];
	}

再看这个代码，经过优化的fabonacci递归，我们可以用一个数组arr[100]来记录是否访问过f(n),如果没有访问过f(n)的值为－1，如果访问过，则可以把f(n)的值记录下来放于数组中，避免了重复计算，实现了记忆化，这个的效率和第二个迭代的fabonacci代码差不多，主要的差距在于递归有不断的函数调用，这里消耗了一些时间

记忆化还可以用于dfs等，可以剪枝提高程序运行效率，避免重复计算
