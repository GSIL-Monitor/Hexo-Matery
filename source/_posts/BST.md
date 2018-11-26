---
title: 二叉搜索树(BST)
tags:
  - 数据结构
  - 二叉搜索树
  - C++
categories:
  - 数据结构
copyright: true
date: 2018-10-31 21:29:08
password:
keywords: BST
description: 二叉搜索树的基本操作
author: XQ
---

```cpp
#include <iostream>

using namespace std;
class BSTNode{
	public:
		BSTNode(int _val = 0){
			val = _val;
			left = NULL;
			right = NULL;
		}
		
		
	public:
		int val;
		BSTNode * left;
		BSTNode * right;
	
		
};
```
<!-- more -->

```cpp
BSTNode * BSTInsert(BSTNode * _root,int _val){
	if(_root == NULL){
		_root = new BSTNode(_val);
		return _root;
	}
	else if(_val > _root->val){
		_root->right = BSTInsert(_root->right, _val);
	}
	else if(_val < _root->val){
		_root->left = BSTInsert(_root->left, _val);
		
	}
	return _root;
}
BSTNode * findValue(BSTNode * _root, int _val){
	BSTNode * res = NULL;
	if(_root==NULL) return _root;
	
	if(_root->val == _val) res = _root;
	else if(_val > _root->val){
		 res = findValue(_root->right, _val);
	}
	else if(_val < _root->val){
		 res = findValue(_root->left, _val);
	}
	return res;
}
BSTNode * findMix(BSTNode * _root){
	if(!_root) return NULL;
	else if(!_root->left) return _root;
	else return findMix(_root->left);
}
BSTNode * deleteNode(BSTNode * _root,int _val){
	BSTNode * p = NULL;
	if(!_root) cout << "can't find" << endl;
	else if(_val < _root->val){
		_root->left = deleteNode(_root->left, _val);
	}
	else if(_val > _root->val){
		_root->right = deleteNode(_root->right, _val);
	}
	else{
		if(_root->left&&_root->left){
			p = findMix(_root->right);
			_root->val = p->val;
			_root->right = deleteNode(_root->right, _root->val);//
		}
		else{
			if(!_root->left){
				_root = _root->right;
			}
			else{
				_root = _root->left;
			}
		}
	}
	return _root;
	
}
void preOrder(BSTNode * _root){
	cout << _root->val << ' ';
	if(_root->left!=NULL)preOrder(_root->left);
	if(_root->right!=NULL)preOrder(_root->right);
}
void inOrder(BSTNode * _root){
	if(_root->left!=NULL)inOrder(_root->left);
	cout << _root->val << ' ';
	if(_root->right!=NULL)inOrder(_root->right);
}
void postOrder(BSTNode * _root){
	if(_root->left!=NULL)postOrder(_root->left);
	if(_root->right!=NULL)postOrder(_root->right);
	cout << _root->val << ' ';
}

int main(int argc, char *argv[]) {
	int n = 0;
	int *temp = new int[n]; 
	while(cin >> n){
		//8 33 88 22 55 90 11 66 99
		for(int i = 0; i < n; i++){
			cin >> temp[i];
		}
		BSTNode * res = new BSTNode(temp[0]);
		for(int i = 1; i < n; i++){
			res = BSTInsert(res, temp[i]);	
		}
		cout << "前序遍历:";
		preOrder(res);
		cout << endl;
		cout << "中序遍历:";
		inOrder(res);
		cout << endl;
		cout << "后序遍历:";
		postOrder(res);
		cout << endl;
		int value = 0;
		cin >> value;
		if(findValue(res, value)!=NULL) cout << "YES" << endl;
		else cout << "NO" << endl;
		
		int d = 0;
		cin >> d;
		deleteNode(res, d);
		
		cout << "前序遍历:";
		preOrder(res);
		cout << endl;
		cout << "中序遍历:";
		inOrder(res);
		cout << endl;
		cout << "后序遍历:";
		postOrder(res);
		cout << endl;
		
		delete res;	
	}	
}
```