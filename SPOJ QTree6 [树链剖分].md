啊啊啊！！！
maintain的时候忘记~~+1s~~+1，于是就。。。累死了，只剩下最后一个Qtree7.
这道题与上道题类似，关键在于路径上面判定同色，关键点在findpath上，至于线段树，我又一次惊叹于线段树在序列问题上近乎无敌的维护效率。中间的mid与mid+1的判定神来之笔。
看来我就是一只菜鸡。

```cpp
#include<deque>
#include<vector>
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
	int l,r,ret;
	node(int l=0,int r=0,int s=1):l(l),r(r),ret(s){}
}tree[N<<2];
bool G,H;
int n,q,x,y,z;
int head[N],sz[N],top[N],dfn[N],tot,cnt,fa[N],dft[N],sum[N],son[N];
int ls[N<<2],rs[N<<2],root[N],val[N][2],col[N],bct;
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
void maintain(int rt,int x){
	tree[rt].l=tree[rt].r=val[x][col[x]]+1;
}
inline node merge(node &x,node &y,bool b){
	node rt=node(x.l,y.r);
	if(b&&x.ret)rt.l=x.l+y.l;
	if(b&&y.ret)rt.r=y.r+x.r;
	rt.ret=b&&y.ret&&x.ret;
	return rt;
}
inline void build(int rt,int l,int r){
	if(l==r){
		int u=dft[l];
		for(register int v,i=head[u];i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&top[u]!=top[v]){
				build(root[v]=++bct,dfn[v],dfn[v]+sum[v]-1),
				val[u][col[v]]+=tree[root[v]].l;
			}
		maintain(rt,u);
	}else{
		build(ls[rt]=++bct,l,mid),build(rs[rt]=++bct,mid+1,r);
		tree[rt]=merge(tree[ls[rt]],tree[rs[rt]],col[dft[mid]]==col[dft[mid+1]]);
	}
}
inline bool judge(int rt,int l,int r,int L,int R,int ret=1){
	if(l==r)return ret;
	if(L<=mid)ret&=judge(ls[rt],l,mid,L,R);
	if(mid<R)ret&=judge(rs[rt],mid+1,r,L,R);
	if(L<=mid&&R>mid)ret&=col[dft[mid]]==col[dft[mid+1]];
	return ret;
}
deque<int> Q;
inline void findpath(int x,bool flag){
	Q.clear();
	while(x){
		int f=top[x];
		if(flag){
			if(!judge(root[f],dfn[f],dfn[x]+sum[f]-1,dfn[f],dfn[x])||col[fa[f]]!=col[f]||!fa[f]){
				Q.push_front(x),Q.push_front(f);break;
			}
		}else Q.push_front(x),Q.push_front(f);
		x=fa[f];
	}
}
inline void update(int rt,int l,int r,int x){
	if(l==r){
		int f=dft[l];
		if(x+2<Q.size()){
			int t=Q[x+1];
            val[f][col[t]]-=tree[root[t]].l;
            update(root[t],dfn[t],dfn[t]+sum[t]-1,x+2);
            val[f][col[t]]+=tree[root[t]].l;
		}else col[f]=1-col[f];
		maintain(rt,f);
	}else{
		if(dfn[Q[x]]<=mid)update(ls[rt],l,mid,x);
        else update(rs[rt],mid+1,r,x);
        tree[rt]=merge(tree[ls[rt]],tree[rs[rt]],col[dft[mid]]==col[dft[mid+1]]);
	}
}
inline int query(int rt,int l,int r,int x,bool &L,bool &R,int res=0){
	if(l==r)return tree[rt].l;
	if(dfn[Q[x]]<=mid){
		res+=query(ls[rt],l,mid,x,L,R);
        if(R&&col[dft[mid+1]]==col[dft[mid]])res+=tree[rs[rt]].l;
        R=R&&tree[rs[rt]].ret&&col[dft[mid+1]]==col[dft[mid]];
    }else{
        res+=query(rs[rt],mid+1,r,x,L,R);
        if(L&&col[dft[mid+1]]==col[dft[mid]])res+=tree[ls[rt]].r;
        L=L&&tree[ls[rt]].ret&&col[dft[mid+1]]==col[dft[mid]];
    }
    return res;
}
inline void change(int u){
	findpath(u,0);
	update(1,1,sum[1],1);
}
inline void ask(int u){
	findpath(u,1),u=Q[0];
	printf("%d\n",query(root[u],dfn[u],dfn[u]+sum[u]-1,1,G=1,H=1));
}
inline void read(int &res){
	static char ch;int flag=1;
	while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
	while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
	read(n);
	for(register int i=1;i<n;i++)read(x),read(y),addedge(x,y);
	read(q),dfsf(1),dfss(1,1);
	build(root[1]=++bct,1,sum[1]);
	for(register int i=1;i<=q;++i){
		read(x),read(y);
		if(x)change(y);
		else ask(y);
	}
	return 0;
}
```