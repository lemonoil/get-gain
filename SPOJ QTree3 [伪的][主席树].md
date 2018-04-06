这道题并不是真正的QTree，真正的QTree系列中QTree3为[query on a tree again！](http://blog.csdn.net/lemonoil/article/details/74682409)
<html>
<body>
<center><h2>1803: Spoj1487 Query on a tree III</h2><span class=green>Time Limit: </span>1 Sec&nbsp;&nbsp;<span class=green>Memory Limit: </span>64 MB<br><span class=green>Submit: </span>579&nbsp;&nbsp;<span class=green>Solved: </span>260<br>[<a href='submitpage.php?id=1803'>Submit</a>][<a href='problemstatus.php?id=1803'>Status</a>][<a href='bbs.php?id=1803'>Discuss</a>]</center><h2>Description</h2><div class=content>You are given a node-labeled rooted tree with n nodes. 

Define the query (x, k): Find the node whose label is k-th largest in the subtree of the node x. Assume no two nodes have the same labels. 



</div><h2>Input</h2><div class=content>The first line contains one integer n (1 <= n <= 10^5). The next line contains n integers li (0 <= li <= 109) which denotes the label of the i-th node. 

Each line of the following n - 1 lines contains two integers u, v. They denote there is an edge between node u and node v. Node 1 is the root of the tree. 

The next line contains one integer m (1 <= m <= 10^4) which denotes the number of the queries. Each line of the next m contains two integers x, k. (k <= the total node number in the subtree of x) 



</div><h2>Output</h2><div class=content>
For each query (x, k), output the index of the node whose label is the k-th largest in the subtree of the node x. 
</div><h2>Sample Input</h2>
            <div class=content><span class=sampledata>5<br />
1 3 5 2 7<br />
1 2<br />
2 3<br />
1 4<br />
3 5<br />
4<br />
2 3<br />
4 1<br />
3 2<br />
3 2<br />
<br />
</span></div><h2>Sample Output</h2>
            <div class=content><span class=sampledata><br />
5<br />
4<br />
5<br />
5<br />
<br />
<br />
<br />
<br />
</span></div><h2>HINT</h2>
            <div class=content><p><br />
Amber的play with tree系列的题.... <br />
</p></div><h2>Source</h2>
            <div class=content><p><a href='problemset.php?search='></a></p></div><center>[<a href='submitpage.php?id=1803'>Submit</a>][<a href='problemstatus.php?id=1803'>Status</a>][<a href='bbs.php?id=1803'>Discuss</a>]</center>﻿<br>
           
</body>
</html>

dfs序+主席树裸题
```cpp
#include<algorithm>
#include<iostream>
#include<cstring>
#include<cstdio>
using namespace std;
#define N 100005
#define logN 30
#define mid ((l+r)>>1)
int n,m,x,y,k,ans,a[N],b[N],mp[N],tot,head[N],in[N],out[N];
int cnt,ls[N*logN],rs[N*logN],root[N],sz,dfn[N],sum[N*logN];
struct data{
    int next,to;
}E[N<<1];
inline int binary_search(int x){
    int l=1,r=n,ret;
    while(l<=r)
        if(b[mid]<=x)ret=mid,l=mid+1;
        else r=mid-1;
    return ret;
}
inline void addedge(int x,int y){
    E[++tot].next=head[x],head[x]=tot,E[tot].to=y;
    E[++tot].next=head[y],head[y]=tot,E[tot].to=x;
}
inline void dfs(int x,int fa){
    dfn[in[x]=++cnt]=x;
    for(register int i=head[x];i;i=E[i].next)
        if(E[i].to!=fa)dfs(E[i].to,x);
    out[x]=cnt;
}
inline void update(int &rt,int l,int r,int w){
    sum[++sz]=sum[rt]+1,ls[sz]=ls[rt],rs[sz]=rs[rt],rt=sz;
    if(l==r)return;
    if(w<=mid)update(ls[rt],l,mid,w);
    else update(rs[rt],mid+1,r,w);
}
inline int query(int l,int r,int rl,int rr,int k){
    if(l==r)return l;
    int t=sum[ls[rr]]-sum[ls[rl]];
    if(t>=k)return query(l,mid,ls[rl],ls[rr],k);
    else query(mid+1,r,rs[rl],rs[rr],k-t);
}
inline void read(int &res){
    static char ch;int flag=1;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
    read(n);
    for(register int i=1;i<=n;++i)
        read(a[i]),b[i]=a[i];
    sort(b+1,b+n+1);
    for(register int i=1;i<=n;++i)
        x=binary_search(a[i]),mp[x]=i,a[i]=x;
    for(register int i=1;i<n;++i)
        read(x),read(y),addedge(x,y);
    dfs(1,0);
    for(register int i=1;i<=n;++i)
        update(root[i]=root[i-1],1,n,a[dfn[i]]);
    read(m);
    for (register int i=1;i<=m;++i){
        read(x),read(k);
        int l=in[x],r=out[x];
        ans=mp[query(1,n,root[l-1],root[r],k)];
        printf("%d\n",ans);
    }
    return 0;
}
```
