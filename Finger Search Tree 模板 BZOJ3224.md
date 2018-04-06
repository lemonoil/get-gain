# [BZOJ3224 传送门](http://www.lydsy.com/JudgeOnline/problem.php?id=3224)

## 模板而已。。

```cpp
#include<cstdio>
#define update(cur)if(cur->left->size)cur->size=cur->left->size+cur->right->size,cur->value=cur->right->value
#define new_Node(s,v,a,b)(&(*st[cnt++]=Node(s,v,a,b)))
#define merge(a,b)new_Node(a->size+b->size,b->value,a,b)
#define ratio 4
int n,cnt,s,a;
struct Node{
    int size,value;
    Node *left,* right;
    Node(int s,int v,Node *a,Node *b):size(s),value(v),left(a),right(b){}
    Node(){}
}*root,*null,*father,t[200005],*st[200005];
inline int min(int a,int b){
	return a<b?a:b;
}
inline int max(int a,int b){
	return a>b?a:b;
}
inline void maintain(Node *cur){
    if(cur->left->size>cur->right->size *ratio)
		cur->right=merge(cur->left->right,cur->right),
		st[--cnt]=cur->left,cur->left=cur->left->left;
    else if(cur->right->size>cur->left->size *ratio)
		cur->left=merge(cur->left,cur->right->left),
		st[--cnt]=cur->right,cur->right=cur->right->right;
}
inline int find(int x,Node *cur){
    if(cur->size==1)return cur->value;
    return x>cur->left->size?find(x - cur->left->size,cur->right):find(x,cur->left);
}
inline int rank(int x,Node *cur){
    if(cur->size==1)return 1;
    return x>cur->left->value ? rank(x,cur->right)+ cur->left->size:rank(x,cur->left);
}
inline void insert(int x,Node *cur){
    if(cur->size==1)
		cur->left=new_Node(1,min(cur->value,x),null,null),
		cur->right=new_Node(1,max(cur->value,x),null,null);
    else insert(x,x>cur->left->value ? cur->right:cur->left);
    update(cur),maintain(cur);
}
inline void erase(int x,Node *cur){
    if(cur->size==1)*father=cur==father->left?*father->right:*father->left;
    else father=cur,erase(x,x>cur->left->value?cur->right:cur->left);
    update(cur),maintain(cur);
}
inline int read(){
    int x=0,f=1;char ch=getchar();
    while(ch<'0'||ch>'9'){if(ch=='-')f=-1;ch=getchar();}
    while(ch>='0' && ch <= '9'){x=x*10+ch-'0';ch=getchar();}
    return x*f;
}
int main(){
    n=read();
    for(register int i=0;i<200005;i++)st[i]=&t[i];
    null=new Node(0,0,0,0);
    root=new Node(1,2147483647,null,null);
    while(n--){
        s=read(),a=read();
        if(s==1)insert(a,root);
        else if(s==2)erase(a,root);
        else if(s==3)printf("%d\n",rank(a,root));
        else if(s==4)printf("%d\n",find(a,root));
        else if(s==5)printf("%d\n",find(rank(a,root)-1,root));
        else printf("%d\n",find(rank(a+1,root),root));
    }
    return 0;
}
```