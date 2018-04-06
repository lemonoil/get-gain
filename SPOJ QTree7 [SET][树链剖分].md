[题目传送门](https://cn.vjudge.net/problem/SPOJ-QTREE7)

为什么突出了SET呢？因为用了set后就可以用端点写法$O(1)$维护路径。。。。然后花费$log^n$的时间插入删除。。。。。
还是感觉时间复杂度很GG啊，一大**堆**大神拿着LCT就开刷。。。不行了，我要刷QTree的二周目了，就叫拿着LCT拯救世界。

这道题相比Qtree6没什么区别，就查找一个max，用Qtree的线段树写法就行了，剩下的用Qtree6的思路就AC了。
一个l打成了r，GG好久。
```cpp
#include<set>
#include<deque>
#include<cstdio>
#include<cstring>
#include<iostream>
#include<algorithm>
using namespace std;
const int N = 100100;
#define mid ((l+r)>>1)
struct data{
	int next,to;
}E[N<<2];
struct node{
	int l,r,lc,rc;
	node(){}
	node(int l,int r,int lc,int rc):l(l),r(r),lc(lc),rc(rc){}
}tree[N<<2];
template<typename T>
struct dis_set{
	inline bool operator () (const T &l,const T &r)const{return l>r;}
};
set<int,dis_set<int> >val[N][2];
int n,q,x,y,z;
int head[N],sz[N],top[N],dfn[N],tot,cnt,fa[N],dft[N],sum[N],son[N];
int ls[N<<2],rs[N<<2],root[N],col[N],bct,w[N];
inline void addedge(int x,int y){
	E[++tot].to=y,E[tot].next=head[x],head[x]=tot;
	E[++tot].to=x,E[tot].next=head[y],head[y]=tot;
}
inline void dfsf(int u){
	sz[u]=1;
	for(register int v,i=head[u];i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]){
			fa[v]=u,dfsf(v),sz[u]+=sz[v];
			if(sz[son[u]]<sz[v])son[u]=v;
		}
}
inline void dfss(int u,int f){
	top[u]=f,dft[dfn[u]=++cnt]=u,sum[f]++;
    if(son[u])dfss(son[u],f);
	for(register int v,i=head[u];i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&v!=son[u])dfss(v,v);
}
inline void maintain(int rt,int x){
	int hrt=w[x];
	if(val[x][col[x]].size())hrt=max(hrt,*val[x][col[x]].begin());
	tree[rt].l=tree[rt].r=hrt;
	tree[rt].lc=tree[rt].rc=1;
}
node merge(node &x,node &y,int lu,int ru,bool b){
	node rt=node(x.l,y.r,x.lc,y.rc);
	if(b&&x.lc==lu)rt.l=max(rt.l,y.l),rt.lc+=y.lc;
	if(b&&y.rc==ru)rt.r=max(rt.r,x.r),rt.rc+=x.rc;
	return rt;
}
inline void build(int rt,int l,int r){
	if(l==r){
		int u=dft[l];
		for(register int v,i=head[u];i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&top[u]!=top[v]){
			build(root[v]=++bct,dfn[v],dfn[v]+sum[v]-1),
			val[u][col[v]].insert(tree[root[v]].l);
		}
		maintain(rt,u);
	}else{
		build(ls[rt]=++bct,l,mid),build(rs[rt]=++bct,mid+1,r);
		tree[rt]=merge(tree[ls[rt]],tree[rs[rt]],mid-l+1,r-mid,col[dft[mid]]==col[dft[mid+1]]);
	}
}
deque<int> Q;
inline void findpath(int x,bool flag){
	Q.clear();
	while(x){
		int f=top[x];
		if(flag){
			if(dfn[f]+tree[root[f]].lc-1<dfn[x]||col[fa[f]]!=col[f]||!fa[f]){
				Q.push_front(x),Q.push_front(f);break;
			}
		}else Q.push_front(x),Q.push_front(f);
		x=fa[f];
	}
}
inline void update(int rt,int l,int r,int x,bool fb){
	if(l==r){
		int f=dft[l];
		if(x+2<Q.size()){
			int t=Q[x+1];
            val[f][col[t]].erase(val[f][col[t]].find(tree[root[t]].l));
            update(root[t],dfn[t],dfn[t]+sum[t]-1,x+2,fb);
            val[f][col[t]].insert(tree[root[t]].l);
		}else if(fb)col[f]=1-col[f];
		maintain(rt,f);
	}else{
		if(dfn[Q[x]]<=mid)update(ls[rt],l,mid,x,fb);
        else update(rs[rt],mid+1,r,x,fb);
        tree[rt]=merge(tree[ls[rt]],tree[rs[rt]],mid+1-l,r-mid,col[dft[mid]]==col[dft[mid+1]]);
	}
}
inline int query(int rt,int l,int r,int x,int res=0){
	if(l==r)return tree[rt].l;
	if(dfn[Q[x]]<=mid){
		res=query(ls[rt],l,mid,x);
        if(mid-tree[ls[rt]].rc+1<=dfn[Q[x]]&&col[dft[mid+1]]==col[dft[mid]])
			res=max(res,tree[rs[rt]].l);
    }else{
        res=query(rs[rt],mid+1,r,x);
        if(mid+tree[rs[rt]].lc>=dfn[Q[x]]&&col[dft[mid+1]]==col[dft[mid]])
			res=max(res,tree[ls[rt]].r);
    }
    return res;
}
inline void change(int u,bool v){
	findpath(u,0);
	update(1,1,sum[1],1,v);
}
inline void ask(int u){
	findpath(u,1),u=Q[0];
	printf("%d\n",query(root[u],dfn[u],dfn[u]+sum[u]-1,1));
}
inline void read(int &res){
	static char ch;int flag=1;
	while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
	while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
	read(n);
	for(register int i=1;i<n;i++)read(x),read(y),addedge(x,y);
	for(register int i=1;i<=n;i++)read(col[i]);
	for(register int i=1;i<=n;i++)read(w[i]);
	read(q),dfsf(1),dfss(1,1);
	build(root[1]=++bct,1,sum[1]);
	for(register int i=1;i<=q;++i){
		read(x),read(y);
		if(x==1)change(y,1);
		else if(x==2)read(z),w[y]=z,change(y,0); 
		else ask(y);
	}
	return 0;
}
```
断断续续，历时7day，大概13个小时，QTree系列的一周目达成！！！！