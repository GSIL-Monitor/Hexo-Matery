---
title: 网络编程(Socket)
date: 2018-10-31 01:02:10
copyright: true
tags: 
    - C++
    - TCP
    - Socket
categories: 
    - 算法
password:
keywords: Socket
description: 使用Socket客户端与服务端通信。
author: XQ
---
<!-- more -->

###客户端代码
```cpp
#include <iostream>
#include <string>

#include <cstdlib>
#include <unistd.h>

#include <sys/stat.h>
#include <fcntl.h>
#include <sys/types.h>

#include <arpa/inet.h>
#include <sys/socket.h>
#include <netinet/in.h>
using namespace std;
int main(int argc, char *argv[]) {
	
	//创建套接字
	int client_socket = socket(AF_INET, SOCK_STREAM, 0);
	if(client_socket<0) {
		cout << "建立套接字失败" << endl;
		return -1;
	}
	else{
		cout << "建立套接字成功" << endl;
	}
	//格式
	cout << "-------------------------" << endl;
	cout << "----------客户端----------" << endl;
	cout << "-------------------------" << endl;
			
	//发起请求
	struct sockaddr_in server_address;
	memset(&server_address, 0, sizeof(server_address));
	server_address.sin_family = AF_INET;  //使用IPv4地址
	cout << "输入远程服务器使用的IP:";
	char ip[20] = {'\0'};
	gets(ip);
	server_address.sin_addr.s_addr = inet_addr(ip);  //具体的IP地址
	cout << "输入本机(服务器)使用port:";
	int port;
	cin >> port;
	server_address.sin_port = htons(port);  //端口
	cout << "使用的IP与port:" << ip << ':' << port << endl;
	int con_flag = connect(client_socket, (struct sockaddr*)&server_address, sizeof(server_address));
	if(con_flag<0) {
		cout << "请求连接失败,服务器可能未开启,请稍等" << endl;
		return -1;
	}
	else{
		cout << "请求连接成功，已连接···" << endl;
	}
	//发送数据
	char buffer[100];
	int len;
	cout << "-------------------------" << endl;
	while(con_flag>=0){
		cout << "待传送的数据:";
		getchar();
		gets(buffer);
		send(client_socket,buffer,sizeof(buffer),0);	
		len = recv(client_socket,buffer,sizeof(buffer), 0);
		buffer[len] = '\0';
		cout << "-------------------------" << endl;
		cout << "接收到的数据:" << buffer << endl;
		cout << "-------------------------" << endl;
	}
	
	close(client_socket);
	return 0;
	
}

```

###服务端代码
```cpp
#include <iostream>
#include <string>

#include <cstdlib>
#include <unistd.h>

#include <sys/stat.h>
#include <fcntl.h>
#include <sys/types.h>

#include <arpa/inet.h>
#include <arpa/inet.h>
#include <sys/socket.h>
#include <netinet/in.h>
using namespace std;
int main(int argc, char *argv[]) {
	
	//创建套接字
	int server_socket = socket(AF_INET, SOCK_STREAM, IPPROTO_TCP);
	if (server_socket < 0){
		cout << "套接字失败" << endl;
		return -1;
	}
	else{
		cout << "建立套接字成功" << endl;
	}
	//格式
	cout << "-------------------------" << endl;
	cout << "----------服务器----------" << endl;
	cout << "-------------------------" << endl;
	
	//套接字，绑定IP端口
	struct sockaddr_in server_address;
	memset(&server_address, 0, sizeof(server_address));
	server_address.sin_family = AF_INET;
	cout << "输入本机(服务器)IP:";
	char ip[20] = {'\0'};
	gets(ip);
	server_address.sin_addr.s_addr = inet_addr(ip);
	cout << "输入本机(服务器)port:";
	int port;
	cin >> port;
	server_address.sin_port = htons(port);
	cout << "使用的IP与port:" << ip << ':' << port << endl;
	int bind_flag = bind(server_socket,(struct sockaddr *)&server_address,sizeof(server_address));
	if (bind_flag < 0){
		cout << "绑定IP与port失败,稍等" << endl;
		return -1;
	}
	else{
		cout << "绑定IP与port成功" << endl;
	}

	//监听
	int lis_flag = listen(server_socket,SOMAXCONN);
	if(lis_flag < 0){
		cout << "监听失败" << endl;
		return -1;
	}
	else{
		cout << "监听中····" << endl;
	}

	//接受请求
	struct sockaddr_in client_address;
	socklen_t client_address_size = sizeof(client_address);
	int client_socket = accept(server_socket, (struct sockaddr*)&client_address, &client_address_size);
	if (client_socket < 0){
		cout << "接受连接失败" << endl;
		return -1;
	}
	else{
		cout << "接受连接成功，连接建立" << endl;
	}

	char buffer[100];
	cout << "-------------------------" << endl;
	while(client_socket >= 0){
		//接收客户端数据
		int len = recv(client_socket, buffer, sizeof(buffer), 0);
		buffer[len] = '\0';	
		cout << "自客户端获得数据:" << buffer << endl;
		
		//处理
		for(int i = 0; buffer[i]!='\0'; i++){
			if(buffer[i]>='a'&&buffer[i]<='z')
				buffer[i] -= 'a' - 'A';
		}

		//回传客户端数据
		send(client_socket, buffer, sizeof(buffer), 0);
		cout << "-------------------------" << endl;		
		cout << "返回客户端的数据:" << buffer << endl;
		cout << "-------------------------" << endl;
		
	}
	close(server_socket);
	close(client_socket);
	
	return 0;
	
	
	
	
	
}

```