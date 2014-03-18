---
layout: post
title: "Data Structure--图搜索算法"
date: 2014-03-16 21:53:03 +0800
comments: true
categories: 
---

#图深度优先搜索算法
``` c++
#include <iostream>

using namespace std;
//-------图的定义和初始化-----------
const int MaxLen=20;//设定图最多包含20个顶点

class Map
{
private:
	bool Visited[MaxLen];//访问标识数组，标识每个顶点是否已访问
	int Matrix[MaxLen][MaxLen];//图的领接矩阵
	int Vexnum;//图的顶点数量

	void DFS(int v);//深度优先，私有函数，内部调用

public:
	void SetMatrix(int vnum,int mx[MaxLen][MaxLen]);
	void DFSTraverse();//公有含糊，被main调用
};
//设置领接矩阵
void Map::SetMatrix(int vnum,int mx[MaxLen][MaxLen])
{
	int i,j;
	Vexnum = vnum;//设置图的结点数
	for(i=0; i<MaxLen; i++)//领接矩阵初始化为0
		for(j=0; j<MaxLen; j++)
			Matrix[i][j] = 0;

	for(i=0; i<Vexnum; i++)//领接矩阵初始化
		for(j=0; j<Vexnum; j++)
			Matrix[i][j] = mx[i][j];
}
//--------------深度优先遍历函数-----------
void Map::DFSTraverse()
{//对图g作深度优先遍历，公有函数
	int v;

	for(v=0; v<Vexnum; ++v)
		Visited[v] = false;//把访问标识数组的值全部设为false

	for(v=0; v<Vexnum; ++v)//从v=0开始，对所有的顶点进行检查
	{
		if(Visited[v] == false)//如果该顶点未被访问，则调用DFS函数进行深度优先搜索
			DFS(v);
	}

	cout<<endl;
}
//深度优先搜索
void Map::DFS(int v)//从第v个顶点出发递归的深度优先遍历图G
{//私有函数，v表示当前顶点
	int w,i,k;
//先对顶点v进行访问
	Visited[v] = true;//设置visit数组的第v个位置为true，表示已访问
	cout<<v<<" ";//输出数值v再输出一个空格，表示当前顶点已访问，并用空格隔开
//找出与v相连接的其他所有顶点
	int *AdjVex = new int[Vexnum];//存放v连接的顶点编号

	for (i=0; i<Vexnum; i++)//把数组AdjVex的值初始化为-1
		AdjVex[i]=-1;

	k=0;//k表示AdjVex的实际使用长度，也就是和v连接的顶点数量
	for(i=0; i<Vexnum; i++)//搜索领接矩阵找出与顶点v连接的其他顶点编号
	{
		if(Matrix[v][i] == 1)//如果顶点i与顶点v连接，那么领接矩阵的第v行第i个位置的值为1
		{
			AdjVex[k]=i;//如果找到i与v连接，那么把i放入数组AdjVex的第k个位置，然后k++
		    k++;
		}
	}

	i=0;
	for(i=0; i<Vexnum; i++)//对v的领接顶点进行深度优先遍历
	{
		w=AdjVex[i];//把AdjVex中的第i个顶点编号放入变量w中
		if(w!=-1)//如果w不等于-1，则说明还有与v相连的顶点，继续循环
		{
			if(Visited[w]==false)//如果顶点w尚未访问，对w调用DFS函数进行遍历
				DFS(w);
		}
	}

	delete []AdjVex;


}

int main()
{
	int i,j,k,t;
	int vnum;
	int mx[MaxLen][MaxLen];
	Map myMap;

	cin>>t;
	for(k=0; k<t; k++)
	{
		for(i=0; i<MaxLen; i++)//输入图的初始化数据
			for(j=0; j<MaxLen; j++)
				mx[i][j]=0;
			cin>>vnum;
		for(i=0; i<vnum; i++)
			for(j=0; j<vnum; j++)
				cin>>mx[i][j];
		myMap.SetMatrix(vnum,mx);//设置图的领接矩阵
		myMap.DFSTraverse();//深度优先遍历
	}
	return 0;
}


   }
```
#图广度优先搜索算法
```c++
#include <iostream>
#include <queue>

using namespace std;
//-------图的定义和矩阵初始化-----------
const int MaxLen=20;//设定图最多包含20个顶点

class Map
{
private:
	bool Visited[MaxLen];//访问标识数组，标识每个顶点是否已访问
	int Matrix[MaxLen][MaxLen];//图的领接矩阵
	int Vexnum;//图的顶点数量

	void BFS(int v);//广度优先，私有函数，内部调用

public:
	void SetMatrix(int vnum,int mx[MaxLen][MaxLen]);
	void BFSTraverse();//公有函数，被main调用
};
//设置领接矩阵
void Map::SetMatrix(int vnum,int mx[MaxLen][MaxLen])
{
	int i,j;
	Vexnum = vnum;//设置图的结点数
	for(i=0; i<MaxLen; i++)//领接矩阵初始化为0
		for(j=0; j<MaxLen; j++)
			Matrix[i][j] = 0;

	for(i=0; i<Vexnum; i++)//领接矩阵接受外来参数
		for(j=0; j<Vexnum; j++)
			Matrix[i][j] = mx[i][j];
}
//--------------广度优先遍历函数-----------
void Map::BFSTraverse()
{//对图g作深度优先遍历，公有函数
	int v=0;
	BFS(v);
}

void Map::BFS(int v)//v是当前顶点
{//按广度优先非递归遍历图G，使用辅助队列Q和访问标识数组visited
	int w,u;//这些变量用于存放顶点编号
	int i,k;
	int *AdjVex = new int[Vexnum];//存放与顶点v相连的其他顶点编号
	queue<int> Q;

	for(v=0; v<Vexnum; ++v)//把访问标志数组的值全设为false
		Visited[v] = false;

	for(v=0; v<Vexnum; ++v)//遍历所有未访问的顶点
	{
		if(Visited[v] == false)//如果v未访问
		{
			Visited[v] = true;//设访问标志数组中的v顶点为ture
			cout<<v<<" ";//输出v和空格，表示该顶点已被访问，并用空格隔开
			Q.push(v);//把v压入队列

			while (!Q.empty())//当队列非空，执行以下循环
			{
				u=Q.front();//取队头元素放入u中
				Q.pop();//把队头元素弹出
                //找出与顶点v相连的所有其他顶点，顶点编号放入数组AdjVex中
				for (i=0; i<Vexnum; i++)//把数组AdjVex的值初始化为-1
				{
					AdjVex[i]=-1;
				}

				k=0;//k表示AdjVex的存放顶点的位置下标
				for(i=0; i<Vexnum; i++)//搜索领接矩阵找出与顶点v连接的其他顶点编号
				{
					if(Matrix[u][i] == 1)
					{
						AdjVex[k]=i;//把编号放入数组AdjVex的第k个位置，然后k++
						k++;
					}
				}

				i=0;//i表示AdjVex数组下标
				for(i=0; i<Vexnum; i++)//对u的领接顶点进行遍历
				{
					w=AdjVex[i];//把AdjVex中的第i个顶点编号放入变量w中
					if(w!=-1)//如果w不等于-1，则执行循环
					{
						if(Visited[w]==false)//如果顶点w尚未访问
						{
							Visited[w] = true;//设置第w个顶点已访问
						    cout<<w<<" ";//输出w和空格，表示该顶点已被访问，并用空格隔开
							Q.push(w);//把w压入队列
						}
					}
				}
			}//while
		}//if
	}//for
	cout<<endl;

}

int main()
{
	int i,j,k,t;
	int vnum;
	int mx[MaxLen][MaxLen];
	Map myMap;

	cin>>t;
	for(k=0; k<t; k++)
	{//输入图的初始化数据
		for(i=0; i<MaxLen; i++)
			for(j=0; j<MaxLen; j++)
				mx[i][j]=0;
			cin>>vnum;
		for(i=0; i<vnum; i++)
			for(j=0; j<vnum; j++)
				cin>>mx[i][j];
		myMap.SetMatrix(vnum,mx);//设置图的领接矩阵
		myMap.BFSTraverse();//广度优先遍历
	}
	return 0;
}
```