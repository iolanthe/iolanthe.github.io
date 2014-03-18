---
layout: post
title: "Data Structure--二叉排序树"
date: 2014-03-18 13:13:26 +0800
comments: true
categories: 
---
```c++
#include <iostream>
using namespace std;
#define ok 1
#define error -1
	//-------------树结点和树定义---------------
class BiTreeNode//二叉树结点
{
public:
	int data;//结点数据
	BiTreeNode *LeftChild;//左子树指针
	BiTreeNode *RightChild;//右子树指针
   //构造函数和析构函数
	BiTreeNode():LeftChild(NULL),RightChild(NULL){}
	~BiTreeNode(){};
};

class BST//排序二叉树
{
private:
	BiTreeNode *Root;//根节点指针
	int Pnum;  //树节点数量
	int Stimes;//比较次数
	void Destroy(BiTreeNode * &t);
	void Traverse(BiTreeNode * &t);
	void Create(BiTreeNode * &T,const int val[]);
	int Insert(BiTreeNode * &T,int key);
    int Search(BiTreeNode * &T,int key);//
	int Del(BiTreeNode * &T,int key);
	void DelP(BiTreeNode * &T);//真正执行删除操作的函数



public:
	BST(void):Root(NULL){};//构造函数
	~BST(void){};//析构函数
	void Destroy();//销毁函数
	void Traverse();//使用中序遍历将数据有序输出
	void Create(int n,const int val[]);//根据原始数据序列创建树
	int Insert(int key);//在树中插入新结点
	int Search(int key);//查找数据是否存在，成功返回比较次数，失败返回-1
    int Del(int key);//删除指定数据，成功返回ok，失败则返回error

};
//----------删除函数----------
//二叉排序树删除
int BST::Del(int key)//公有函数，对外接口
{
	return Del(Root,key);
}

int BST::Del(BiTreeNode * &T,int key) 
{ //删除成功返回ok,删除失败返回error
    if(!T) //T为空，删除失败，返回error
        return error; 
    else
    { //比较T的data值与key值
        if(key == T->data) 
            DelP(T);        
        else if(key < T->data) 
            return Del(T->LeftChild,key); 
        else
			return Del(T->RightChild,key); 
    } 
      
    return ok;
} 

void BST::DelP(BiTreeNode * &T)
{//真正执行删除的函数
	BiTreeNode *q,  *s;
	if (!T->RightChild)   // T右子树空，则重接它的左子树
	{
		q = T;
		T = T->LeftChild;
		delete q;
	} 
	else if(!T->LeftChild)   // T左子树空，则重接它的右子树
	{ 
		q = T;  
		T = T->RightChild;  
		delete q;  
	}
	else
	{  //T左右子树都不空，则找出T左子树最大的右孩子，进行替换，然后重接最大右孩子的左子树
		q = T; 
		s = T->LeftChild;
		while (s->RightChild)   // ×a×ó￡?è?oó?òóòμ???í•
		{ 
			q = s;  
			s = s->RightChild; 
		}
		T->data = s->data;                  // s???ò±?é??áμ?μ?ì????áμ?
		if (q != T) 
			q->RightChild = s->LeftChild;  // ???ó*qμ?óò×óê÷
		else 
			q->LeftChild = s->LeftChild;         // ???ó*qμ?×ó×óê÷
		delete s;    
	}

} // Delete


//---------------------查找函数---------------
//二叉排序树查找
int BST::Search(int key)//公有函数，对外接口
{//如果成功返回比较次数Stimes，如果失败返回-1
	Stimes =0;
	Search(Root,key);
	return Stimes;
}

int BST::Search(BiTreeNode * &T,int key)//私有函数类内实现 
{ //在算法理论中的比较一次，在实质代码中是比较3次，但我们仍当一次处理
    if(!T) //T为空
        Stimes = -1; //查找失败，，把Stimes设为-1
    else
    { //比较T的data值与key值，注意Stimes放在合适位置
        if(key == T->data) 
            Stimes++;        
        else if(key < T->data) 
        {    
            Stimes++; 
            Search(T->LeftChild,key); 
        } 
        else if(key > T->data) 
        {    
            Stimes++; 
            Search(T->RightChild,key); 
        } 
    } 
      
    return Stimes; 
} 

//----------------销毁和创建函数-----------------
//销毁二叉树
void BST::Destroy(void)//公有函数，对外接口
{  
	Destroy(Root);  
}

void BST::Destroy(BiTreeNode * &t)//私有函数，类内实现
{
	if(t !=NULL && t->LeftChild != NULL)
		Destroy(t->LeftChild);
	if(t !=NULL && t->RightChild != NULL)
		Destroy(t->RightChild);
    if(t != NULL)
		delete t;
}
//根据数据序列，创建二叉排序树
void BST::Create(int n,const int val[])//公有函数，对外接口
{
	Pnum = n;
	Root = NULL;
	Create(Root,val);
}

void BST::Create(BiTreeNode * &T,const int val[])
{//私有函数，类内实现
	int i;

	for (i=0; i<Pnum ;i++)//创建树实际就是把序列val的数值逐个插入
		Insert (Root,val[i]);
}
//----------------插入和遍历函数-----------------
//插入新结点
int BST::Insert(int key)//公有函数，对外接口
{
	return Insert(Root,key);
}

int BST::Insert(BiTreeNode * &T,int key)
{//私有函数，类内实现
	if(!T)//T为空，创建新结点
	{//把T实体化，把key的值放入T中,设置T的左右孩子为空
		T= new BiTreeNode();
	
		T->data = key;
		T->LeftChild=NULL;
		T->RightChild=NULL;
	}

	else//T不为空，进行比较，找寻要插入的位置
	{//比较T的data值与key值
		if ( key == T->data ) //如果等于则说明数值已经存在树中，返回error表示报错
			return error;
		else if (T->data > key)//如果是不等，从左子树或右子树递归查找
			Insert(T->LeftChild, key) ;
		else   
			Insert(T->RightChild, key) ;   
	}
	return ok;
}
//使用中序遍历将数据有序输出
void BST::Traverse()//公有函数，对外接口
{
	Traverse(Root);
	cout<<endl;
}

void BST::Traverse(BiTreeNode * &t)
{//私有函数，类内实现
	if(t)
	{//中序遍历代码
		Traverse(t->LeftChild);
		cout<<t->data <<" ";
		Traverse(t->RightChild);
	}
}

int main(void)
{
	int t,i ,j ,n,tkey;
	int val[800];
	BST myTree;
	cin>>t;
	for(i=0; i<t; i++)//创建二叉排序树
	{
		cin>>n;//输入n个数据，放在数组val中
		for(j=0;j<n;j++)
			cin>>val[j];
		myTree.Create(n,val);//创建
		myTree.Traverse();//遍历树，输出有序序列

		//----------插入---------
		cin>>n;
		for(j=0;j<n;j++)
		{
			cin>>tkey;
			myTree.Insert(tkey);//每输入一个数据就调用
			myTree.Traverse();//输出有序序列
		}
		//---------查找----------
		cin>>n;//输入要查找的数据个数
		for(j=0;j<n;j++)
		{
			cin>>tkey;//每输入一个数据就调用
			cout<<myTree.Search(tkey)<<endl;//输出查找次数
		}
		//------------删除----------
        cin>>n;//输入要删除的数据个数
		for(j=0;j<n;j++)
		{
			cin>>tkey;//每输入一个数据就调用Del
			myTree.Del(tkey);
			myTree.Traverse();//输出有序序列
		}



		myTree.Destroy();
	}
	return 0;
}

```