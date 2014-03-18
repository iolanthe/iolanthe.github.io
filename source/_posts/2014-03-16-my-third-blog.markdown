---
layout: post
title: "Data Structure--赫夫曼编码"
date: 2014-03-16 16:50:42 +0800
comments: true
categories: 
---


``` c++
   //-----------赫夫曼树和结点定义----------
#include <iostream>
#include <string>
using namespace std;

const int MaxW = 99999;//假设结点权值不超过99999


class HuffNode
{
public:
	int weight;//权值
	int parent;//父节点下标
	int leftchild;//左孩子下标
	int rightchild;//右孩子下标

};
//定义哈夫曼树类
class HuffMan
{
private:
	void MakeTree ();//建树，私有函数，被公有函数调用
	void SelectMin ( int pos,int *s1,int *s2 );
public:
	int len;//结点数量
	int lnum;//叶子数量
	HuffNode *huffTree;//赫夫曼树，用数组表示
	string *huffCode;//每个字符对应的赫夫曼编码
	void MakeTree(int n,int wt[]);//公有函数，被main调用
	void Coding();//公有函数，被main调用
	void Destory();
};
//-----------构建huffman树--------------
void HuffMan::MakeTree(int n,int wt[])//参数是叶子结点数和叶子权值
{
//公有函数，对外接口，被main函数调用
	int i;
	lnum=n;//设置lnum的值为叶子数量
	len=2*n-1;//设置len的值为叶子数量*2-1

	huffCode = new string[lnum+1];//位置从1开始计算
	//huffCode实质是个二维字符数组，第i行表示第i个字符对应的编码
	huffTree = new HuffNode[2*n];
    //赫夫曼树huffTree初始化
	for( i=1;i<=len;i++)//把huffman前n个结点的权值设为wt的权值，从1开始编号
	huffTree[i].weight=wt[i-1]; 
	

	for( i=1;i<=len;i++)
	{
		if(i>n) huffTree[i].weight = 0;//前n个结点是叶子已经设值
		huffTree[i].parent = 0;
		huffTree[i].leftchild = 0;
		huffTree[i].rightchild = 0;
	}
	MakeTree();//调用私有函数建树
}

void HuffMan::SelectMin(int pos,int *s1,int *s2)
//找出的下标会保存在s1和s2中
{
	int w1,w2,i;
	w1=w2=MaxW;//初始化w1和w2为最大值，在比较中会被实际的权值替换
	*s1 = *s2 =0;
	for(i=1;i<=pos;i++)
	{
		if(huffTree[i].weight < w1 && huffTree[i].parent == 0)
		{
			w2 = w1;
			*s2 = *s1;
			w1 = huffTree[i].weight;
			*s1 = i;
		}
		else if(huffTree[i].weight < w2 && huffTree[i].parent == 0)
		{
			w2 = huffTree[i].weight;
			*s2 = i;
		}
	}
}
//--------建树与销毁函数-----------
void HuffMan::MakeTree()
{//私有函数，被公有函数调用
	int i,s1,s2;
	for(i=lnum+1; i<=len; i++)
	{
		SelectMin(i-1,&s1,&s2);//找出的下标值会放入s1和s2中
		//设置s1和s2的父亲，并作相关修改
		huffTree[s1].parent = i; //设置s1的父亲 
		huffTree[s2].parent = i;//设置s2的父亲
		huffTree[i].weight = huffTree[s1].weight + huffTree[s2].weight;//设置父亲的权值
		huffTree[i].leftchild = s1;  //设置父亲的左孩子
		huffTree[i].rightchild = s2;//设置父亲的右孩子
	}
}
//销毁赫夫曼树
void HuffMan::Destory()
{
	len = 0;
	lnum = 0;
	delete []huffTree;
	delete []huffCode;
}
//-------------赫夫曼编码------------
void HuffMan::Coding()
{
	char *cd;
	int i,c,f,start;
//求n个叶结点的哈夫曼编码
	cd = new char[lnum];//cd是临时字符串，临时保存每个字符的编码
	cd[lnum-1] = '\0';//编码结束符
	for (i=1;i<=lnum;++i)//逐个字符求哈夫曼编码
	{
		start = lnum-1;//编码结束符位置
		c=i;//当前结点
		f=huffTree[i].parent;//当前节点的父亲

		//从叶子到根逆向求编码
		while(f!=0)
		{//如果父亲f的左孩子等于c,则编码取0，这里包含2行代码
			if (huffTree[f].leftchild==c)
				cd[--start] = '0';
		    else cd[--start] = '1';//否则，编码取1
			c = f;//c指向父亲
			f = huffTree[f].parent;//f指向上一层父亲
		}
	   
	//把cd中从start到末尾的编码复制到huffCode中
		huffCode[i].assign(&cd[start]);
	}
	delete []cd;//释放工作空间
}

//-------------------主函数------------
int main()
{
	int t,n,i,j;
	int wt[100];
	HuffMan myHuff;
	cin>>t;
	for (i=0;i<t;i++)
	{
		cin>>n;
		for (j=0;j<n;j++)
			cin>>wt[j];
		myHuff.MakeTree(n,wt);//建树
		myHuff.Coding();//编码
		for(j=1;j<=n;j++)
		{
			cout<<myHuff.huffTree[j].weight<<'-';//输出各权值
			cout<<myHuff.huffCode[j]<<endl;//输出各编码
		}
		myHuff.Destory();//销毁
	}
	return 0;
}

```
	 