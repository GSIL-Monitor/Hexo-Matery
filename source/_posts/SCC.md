---
title: SCC(有向图的强连通分量)
date: 2018-10-31 01:00:29
copyright: true
tags: 
    - 强连通分量
    - C++
    - 图算法
categories: 
    - 算法
password:
keywords: 图算法
description: 有向图强连通分量:在有向图G中，如果两个顶点vi,vj间(vi>vj)有一条从vi到vj的有向路径，同时还有一条从vj到vi的有向路径，则称两个顶点强连通(strongly connected)。如果有向图G的每两个顶点都强连通，称G是一个强连通图。有向图的极大强连通子图，称为强连通分量(strongly connected components)。
author: XQ
---

###有向图的强连通分量

<!-- more -->


```cpp
//
//  main_ListMap.cpp
//  Map
//
//  Created by XQ on 2017/4/5.
//  Copyright © 2017年 XQ. All rights reserved.

//  2018年9月修改，加入SCC

/*

    10 6 0 A 1 B 2 C 3 D 4 E 5 F 8 0 1 1 1 4 1 4 5 1 1 2 1 2 5 1 0 3 1 2 0 1 3 2 1
    6 5 0 A 1 B 2 C 3 D 4 E 5 0 1 1 1 2 1 2 0 1 1 3 1 3 4 1
    10 8 0 A 1 B 2 C 3 D 4 E 5 F 6 G 7 H 8 0 1 1 1 2 1 2 0 1 3 4 1 4 5 1 5 3 1 6 3 1 6 5 1
*/
#include <iostream>
#include <stdio.h>
#include <stack>
typedef char valuetype;
using namespace std;
static int num = 0;


class AdjoinListArc{
public:
    AdjoinListArc();
public:
    int index;
    int data;
    
    AdjoinListArc* next_arc;
};

class AdjoinListNode{
public:
    bool visited;//遍历作为访问标记，SCC中作为访问入栈标记
    int index;
    valuetype data;
    AdjoinListArc * first_next_arc;
    int dfn;
    int low;
    bool flag;//输出标记


};

stack<AdjoinListNode*> stack_temp;
class AdjoinListMap{
public:
    AdjoinListMap(int);
    ~AdjoinListMap();
    bool Add_Node_AdjoinListMap(int,valuetype);
    bool Add_Arc_AdjoinListMap(int,AdjoinListNode* ,int);
    bool Reset_AdjoinListMap();
    void DFS_AdjoinListMap(AdjoinListNode*);
    void BFS_AdjoinListMap(AdjoinListNode*);
    void SCC_AdjoinListMap(AdjoinListNode*);//2018.9
    
    AdjoinListNode* Get_Node_AdjoinListMap(int);
    
private:
    int node_count;
    int arc_count;
    int capcity;
    
    AdjoinListNode* node_matrix;
};



AdjoinListArc::AdjoinListArc(){
    this->next_arc = NULL;
}

AdjoinListMap::AdjoinListMap(int capcity){
    this->capcity = capcity;
    arc_count = 0;
    node_count = 0;
    node_matrix = new AdjoinListNode[capcity];
    for(int i = 0 ; i < capcity ; i ++){
        node_matrix[i].index = i;
        node_matrix[i].visited = false;
        node_matrix[i].first_next_arc = NULL;
    }
}

AdjoinListMap::~AdjoinListMap(){
    delete []node_matrix;
}

bool AdjoinListMap::Add_Node_AdjoinListMap(int index, valuetype e){
    node_matrix[index].data = e;
    node_matrix[index].flag = false;
    node_count ++;
    return true;
}

bool AdjoinListMap::Add_Arc_AdjoinListMap(int index,AdjoinListNode* p,int value){
    AdjoinListArc *temp = p->first_next_arc;
    if(temp == NULL){
        AdjoinListArc * s = new AdjoinListArc;
        p->first_next_arc = s;
        s->next_arc = NULL;
        s->data = value;
        s->index = index;
        return true;
    }
    while(temp->next_arc != NULL){
        temp = temp->next_arc;
    }
    if(temp->next_arc == NULL){
        AdjoinListArc * s = new AdjoinListArc;
        temp->next_arc = s;
        s->next_arc = NULL;
        s->data = value;
        s->index = index;
    }
    arc_count ++;
    return true;
}

bool AdjoinListMap::Reset_AdjoinListMap(){
    for(int i = 0 ; i  < capcity ; i ++){
        node_matrix[i].visited = false;
    }
    return true;
}

void AdjoinListMap::DFS_AdjoinListMap(AdjoinListNode* p){
    if(p->visited == false){
        cout << p->data << " ";
        p->visited = true;
    }
    while(p->first_next_arc != NULL){
        AdjoinListArc * s = p->first_next_arc;
        if(node_matrix[s->index].visited == false){
            DFS_AdjoinListMap(&node_matrix[s->index]);
        }
        if(node_matrix[s->index].visited == true){
            return;//important
        }
    }
}

void AdjoinListMap::BFS_AdjoinListMap(AdjoinListNode * p){
    if(p->visited == false){
        cout << p->data << " ";
        p->visited = true;
    }
    AdjoinListArc * temp = p->first_next_arc;
    if(temp == NULL) return;
    while(temp != NULL){
        if(node_matrix[temp->index].visited == false)
        cout << node_matrix[temp->index].data << " ";
        node_matrix[temp->index].visited = true;
        temp = temp->next_arc;
    }
}

void AdjoinListMap::SCC_AdjoinListMap(AdjoinListNode *p){
    
    if(p->visited==false){
        p->dfn = num;
        p->low = num;
        num++;
        stack_temp.push(p);
        p->visited = true; 
    }//
    AdjoinListArc * s = p->first_next_arc;
    while(s!=NULL){
        if(node_matrix[s->index].visited == false){
            SCC_AdjoinListMap(&node_matrix[s->index]);
        }
        if(node_matrix[s->index].flag==false) {
            p->low = min(p->low,node_matrix[s->index].low);
            //出现bug的地方,没有这个标记出现第三个用例，G输出不了，原因是dfn！=low，多个已经输出的指向，说明已经没有其他通路
        }
        s = s->next_arc;
      
    }//寻找缩点？
    if(p->low == p->dfn){
        while(!stack_temp.empty()){
            AdjoinListNode* s = stack_temp.top();
            stack_temp.pop();
            cout << s->data << ' ' ;
            s->flag = true;//输出标记
            if(s->low==s->dfn) break;
        }
        cout << endl;
        return;
    }
  
}

AdjoinListNode* AdjoinListMap::Get_Node_AdjoinListMap(int index){
    return &node_matrix[index];
}



int main(){
    cout << "Please input the capcity." << endl;
    int capcity;
    cin >> capcity;
    AdjoinListMap Listmap =  AdjoinListMap(capcity);
    cout << "Please input the count of node." << endl;
    int count_node;
    cin >> count_node;
    cout << "Please input the index and value of node." << endl;
    for(int i = 0 ; i < count_node ; i ++){
        int index_node;
        valuetype value_node;
        cin >> index_node >> value_node;
        Listmap.Add_Node_AdjoinListMap(index_node, value_node);
    }
    cout << "Please input the count of arc." << endl;
    int count_arc;
    cin >> count_arc;
    cout << "Please input the index_from , index_to and value of arc." << endl;
    int index_arc_from;
    int index_arc_to;
    int value_arc;
    for(int i = 0 ; i < count_arc ; i ++){
        cin >> index_arc_from >> index_arc_to>> value_arc;
        Listmap.Add_Arc_AdjoinListMap(index_arc_to, Listmap.Get_Node_AdjoinListMap(index_arc_from), value_arc);
    }
    for(int i = 0 ; i < count_node ; i ++){
        cout << i << ":";
        Listmap.BFS_AdjoinListMap(Listmap.Get_Node_AdjoinListMap(i));
        Listmap.Reset_AdjoinListMap();
        cout << endl;
    }
    cout << "DFS:";
    for(int i = 0 ; i < count_node ; i ++){
        Listmap.DFS_AdjoinListMap(Listmap.Get_Node_AdjoinListMap(i));
    }
    cout << endl;
    Listmap.Reset_AdjoinListMap();
    cout << "BFS:";
    for(int i = 0 ; i < count_node ; i ++){
        Listmap.BFS_AdjoinListMap(Listmap.Get_Node_AdjoinListMap(i));
    }
    cout << endl;
    Listmap.Reset_AdjoinListMap();
    cout << "SCC:" << endl;
    
    
    for(int i = 0 ; i < count_node ; i ++){
        Listmap.SCC_AdjoinListMap(Listmap.Get_Node_AdjoinListMap(i));
    }
    
    cout << endl;
    
    return 0;
}
```