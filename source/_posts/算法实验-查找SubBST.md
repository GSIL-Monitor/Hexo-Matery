---
title: 算法实验-查找SubBST
categories: [算法]
tags: [算法,C++,数据结构]
copyright: true
comments: true
top: false
author: XQ
date: 2018-11-27 21:21:06
updated: 2018-11-27 21:21:06
keywords:
description: Given the root node of a binary search tree (BST) and a value. You need to find the node in the BST that the node's value equals the given value. Return the subtree rooted with that node. If such node doesn't exist, you should return NULL.
passwords:
img:

---



![](https://pictures-1257961856.cos.ap-shanghai.myqcloud.com/images/blog_images/Algorithm-6-2/1.png)

``` cpp
#include <iostream>

using namespace std;
class Node{
	public:
		int value;
		Node * left;
		Node * right;
		
	public:
		Node(int value);
		~Node();	
};
class Solution {
public:
	Node* searchBST(Node* t, int num) {
		
		if (t == NULL || t->value == num) {
			return t;
		}	
		if(num<t->value) {
			return searchBST(t->left, num);
		}
		else {
			return searchBST(t->right, num);
		}
	}
	
};
int main(int argc, char *argv[]) {
	
}
```





