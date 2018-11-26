---
title: 炸弹人poq
tags:
  - 简单算法
  - C++
categories:
  - 算法
copyright: true
date: 2018-10-31 21:51:14
password:
keywords: 算法
author: XQ
description: 给定一个m×n矩阵，如果某个元素为0，则 将其整个行和列都设置为0。
---

```cpp
#include <iostream>
#include <vector>
using namespace std;

//打印数组
void printVector(vector<vector<int> > res,int m , int n){
	for(int i = 0; i < m; i++){
		for(int j = 0; j < n; j++){
			cout << res[i][j]<< ' ';
		}
		cout << endl;

	}
}
//第一种转换方法
void convert(vector<vector<int> > &res,int m , int n){
	for(int i = 0; i < m; i ++){
		for(int j = 0; j < n; j++){
			if(res[i][j]==0){//使用矩阵本身存储为零的信息,有个不能用的数的bug
				for(int k = 0; k < n; k++){
					res[i][k] = res[i][k]==0? 0:INT_MAX;
				}
				for(int k = 0;k < m; k++){
					res[k][j] = res[k][j]==0? 0:INT_MAX;
				}	
			}

		}
	}
	for(int i = 0; i < m; i++){
		for(int j = 0;j < n; j++){
			if(res[i][j]==INT_MAX){
				res[i][j]=0;
			}
		}
	}
	
	
	
}	
//第二种转换方法
void convert2(vector<vector<int> > &res,int m , int n){
	bool rowZero = false;
	bool culZero = false;
	//O(1)
	for(int i = 0; i < m; i++){
		for(int j=0;j<m; j++){
			if(i != 0 && j != 0 && res[i][j]==0){
						res[i][0]=0;
						res[0][j]=0;
			}
			else if(res[i][j]==0){
					rowZero = i==0? true:rowZero;
					culZero = j==0? true:culZero;
				}
			}
		}
		for(int i=1;i<m;i++){
			for(int j=1;j<n;j++){
				if(res[0][j]==0 || res[i][0] == 0){
					res[i][j]=0;
				}
			}
		}
		if(culZero){
			for(int i = 0 ;i < m; i++){
					res[i][0] = 0;
			}
		}
		if(rowZero){
			for(int j = 0;j < n; j++){
				res[0][j] = 0;
			}
		}
}
int main(int argc, char *argv[]) {
	int m = 0,n = 0;
	while(cin >> m >> n){
		int input;
		cout << "输入初始矩阵:" << endl;
		cout << "---------" << endl;
		vector<vector<int> > res;
		for(int i = 0; i < m; i++){
			vector<int> temp;
			for(int j = 0; j < n; j++){
				cin >> input;
				temp.push_back(input);
			}
			res.push_back(temp);
		}
		cout << "---------" << endl;
		//printVector(res,m,n);
		cout << "结果矩阵:" << endl;
		cout << "---------" << endl;
		convert2(res, m, n);
		printVector(res,m,n);
		cout << "---------" << endl;		
	}
}

```