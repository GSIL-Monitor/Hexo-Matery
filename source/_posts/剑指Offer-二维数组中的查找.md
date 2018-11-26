---
title: 剑指Offer(二维数组中的查找)
categories: [算法]
tags: [算法,代码,查找]
copyright: true
comments: true
top: false
date: 2018-11-05 15:03:37
updated: 2018-11-05 15:03:37
keywords: 算法,代码,查找
description: 《剑指Offer》P44面试题4:在一个二维数组中，每一行按照从左到右递增的顺序排序，每一列都按照从上到下递增的顺序排序。请完成一个函数，输入这样一个二维数组和一个整数，判断数组中是否含有该整数。
passwords:
author: XQ
---

```cpp
#include <iostream>
#include <vector>
using namespace std;
//找到一个严格递增矩阵中的特定值，矩阵每一行递增，每一列递增
static int sum = 0;
bool findANumber(vector<vector<int> > & in,int num){
	int length = in.size();
	for(int i = 0,j = length - 1; i < length && j < length; ){
		if(in[i][j]==num) return true;
		else if(in[i][j]>num) {
			j--;

			sum ++;
		}
		else{
			i++;

			sum ++;
		}
	}
	return false;
}

/*
	用例:
		1 2 3 4
		11 22 33 44
		111 222 333 444
		1111 2222 3333 4444

*/

int main(int argc, char *argv[]) {
	//
	int n;
	cout << "输入格式:" << endl;
	cout << "矩阵维度," << "比如:4" << "." << endl;
	cout << "输入递增矩阵," << "比如" << endl  << "1 2 3 4" << endl << 
								"11 22 33 44" << endl <<
								"111 222 333 444"<< endl << 
								"1111 2222 3333 4444" << "." << endl;
	cout << "查找的数," << "比如:2." << endl << "可得结果:Find.并输出比较次数验证一下." << endl;
	while(cin >> n){
		vector<vector<int> > res;
		if(n<=0) break;
		for(int i = 0; i < n; i++){
			vector<int> temp;
			for(int j = 0; j < n; j++){
				int a;
				cin >> a;
				temp.push_back(a);
			}
			res.push_back(temp);
		}
		int num;
		cin >> num;
		if(findANumber(res, num)) {
			cout << "Find" << endl;
		}
		else{
			cout << "Can`t find" << endl;
		}
		cout << sum << endl;
		sum = 0;
		
	}
	//
	return 0;
}
```

