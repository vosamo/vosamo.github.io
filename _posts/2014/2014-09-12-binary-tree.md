---
layout: post
title: 二叉树的遍历
categories:
- Programming
tags:
- iOS
- handoff
---

## 1、二叉树的建立及遍历

```C++
#include<iostream>
#include<stdio.h>
#include<malloc.h> // malloc()等
#include<stdlib.h>
using namespace std;

typedef struct BiTNode
 {
    char data; // 结点的值
    BiTNode *lchild,*rchild; // 左右孩子指针
 }BiTNode,*BiTree;

//创建二叉树
BiTree CreateBiTree()
 { // 算法6.4：按先序次序输入二叉树中结点的值(可为字符型或整型，在主程中定义)，
   // 构造二叉链表表示的二叉树T。'#'表示空(子)树。
   char ch;
   BiTree root;
   scanf("%c",&ch);
   if(ch == '#')
    return NULL;
   else
   {
       root = (BiTree)malloc(sizeof(BiTNode));
       root -> data = ch;
       root -> lchild = CreateBiTree();
       root -> rchild = CreateBiTree();
       return root;
   }

 }

/********************三种递归遍历算法********************/
//先序递归遍历二叉树
void PreOrderTraverse(BiTree root)
{
    if(root != NULL)
    {
        printf("%c ",root -> data);
        PreOrderTraverse(root -> lchild);
        PreOrderTraverse(root -> rchild);
    }
}

//中序递归遍历二叉树
void InOrderTraverse(BiTree root)
{
    if(root != NULL)
    {
        InOrderTraverse(root -> lchild);
        printf("%c ",root -> data);
        InOrderTraverse(root -> rchild);
    }
}

//后序递归遍历二叉树
void PostOrderTraverse(BiTree root)
{
    if(root != NULL)
    {
        PostOrderTraverse(root -> lchild);
        PostOrderTraverse(root -> rchild);
        printf("%c ",root -> data);
    }
}

/********************三种非递归遍历算法********************/


 int main()
 {
   BiTree T;
   printf("按先序次序输入二叉树中结点的值,输入‘#’表示节点为空\n");
   T = CreateBiTree(); // 建立二叉树T
   PreOrderTraverse(T);
   return 0;
 }

```

## 2、根据前序遍历/中序遍历恢复后序遍历
假设某二叉树的前序遍历为：DBACEGF，中序遍历为：ABCDEFG，求其后序遍历。
后序遍历：

1. 后序遍历左子树
2. 后序遍历右子树
3. 访问根节点

关键在于**根据前序遍历和中序遍历找出根节点并正确划分左右子树**，然后就是递归了，递归的条件和位置很重要。

```C++
#include<stdio.h>
char post[7];
void postFromPreMid(char *pre,char *mid,int length)
{
    if(pre == NULL || mid == NULL || length <=0)
        return NULL;

    char rootVal = pre[0];
    char *rootPosInMid = mid;
    while(rootPosInMid <= mid+length-1 && *rootPosInMid != rootVal)
        rootPosInMid++;
    int leftLength = (int)(rootPosInMid-mid);//×ó×ÓÊ÷³¤¶È

    if(leftLength > 0)
        postFromPreMid(pre+1,mid,leftLength);
    if(leftLength < length - 1)
        postFromPreMid(pre+leftLength+1,rootPosInMid+1,length-leftLength-1);
    printf("%c",rootVal);
}

int main()
{
    char pre[] = {'D','B','A','C','E','G','F'};
    char mid[] = {'A','B','C','D','E','F','G'};
    postFromPreMid(pre,mid,7);

    return 0;
}
```

## 3、判断一棵二叉树是否是二叉搜索树
所谓二叉搜索树就是，二叉树要么为空，要么满足：如果左子树不为空，则左子树节点值都小于根节点；如果右子树不为空，则右子树节点值都大于根节点。二叉搜索树也叫二叉查找树，也叫二叉排序树，中序遍历一个二叉搜索树会得到一个有序的序列。

```C++
bool isBST(BiTree p)
{
	if(NULL == p)
		return true;
	if(p->lchild != NULL && p->rchild != NULL)		//左右子树均非空 
	{
		if(p->lchild->data < p->data && p->rchild->data > p->data)
			return (isBST(p->lchild) && isBST(p->rchild));
		else
			return false;
	}
	else if(p->lcild != NULL && p->rchild == NULL)		//左子树非空，右子树空 
	{
		if(p->lcild->data < p->data)
			return (isBST(p->lcild));
		else
	 		return false;
	}
	else if(p->rcild != NULL && p->lchild == NULL)		//右子树非空，左子树空 
	{
		if(p->rcild->data > p->data)
			return (isBST(p->rcild));
		else
	 		return false;
	}
	else		//左右子树均为空 
		return true;
} 
```

## 4、求两个节点的最近公共祖先
如果两个节点分别位于当前节点的左右两侧，则当前节点就是最近公共祖先，如果两个节点位于当前节点的一侧，则在该侧继续查找，直到两个节点不在某节点同一侧，就找到了公共祖先。

```C++
BiTree FindCommAncestor(BiTree root,BiTree p,BiTree q)
{
	if(root == NULL || p == NULL || q == NULL)
		return NULL;
	if(Contain(root->lchild,p) && Contain(root->lchild,q))
		return FindCommAncestor(root->lchild,p,q);
	if(Contain(root->rchild,p) && Contain(root->rchild,q))
		return FindCommAncestor(root->rchild,p,q);
	return root;
}

bool Contain(BiTree root,BiTree p)
{
	if(root == NULL)
		return false;
	if(root == p)
 		retutn true;
	else
		return Contain(root->lchild,p) || Contain(root->rchild,q);
}
```












