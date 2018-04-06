4.5hours的艰苦奋斗。。。。。
好想爆粗口。。。堆转手写set，树链剖分上加点入set，线段树维护set信息。。我想死。。。

```cpp
#include<bits/stdc++.h>
const int N=100020,inf=1<<29;
using namespace std;
char cmd[8];
struct Set{
	priority_queue<int> a,b;
	void erase(int x){b.push(x);}
	void insert(int x){a.push(x);}
	void slnew(){
		while(!a.empty()&&!b.empty()&&a.top()==b.top())a.pop(),b.pop();
	}
	int top(){
		slnew();return a.empty()?-inf:a.top();
	}
	int query(){
		int x,y;slnew();
		if(a.empty())return -inf;
		x=a.top(),a.pop(),slnew();
		y=a.empty()?-inf:x+a.top(),a.push(x);
		return y;
	}
}heap[N],lis;
struct data{
    int to,next,w;
}E[N<<1];
struct node{
	int L,R,ret;
	node *ch[2];
	void setting(int u){
		ret=heap[u].query();
		L=R=heap[u].top();
	}
	void update(int lc,int rc,int rt);
}*root[N],pool[N<<2],*pool_tail=pool;
int n,tot,head[N],son[N],sz[N],fa[N],top[N],dfw[N],dfn[N],w[N],cnt;
int dis[N<<2],tail[N],col[N],tal,se,m;
void addedge(int x,int y,int z){
    E[++tot].to=y,E[tot].next=head[x],E[tot].w=z,head[x]=tot;
    E[++tot].to=x,E[tot].next=head[y],E[tot].w=z,head[y]=tot;
}
#define mid ((rc+lc)>>1)
void merge(node &rt,const node &l,const node &r,int lc,int rc){
	rt.ret=max(l.ret,r.ret);
	rt.ret=max(rt.ret,l.R+dis[dfw[mid+1]]-dis[dfw[mid]]+r.L);
	rt.L=max(l.L,dis[dfw[mid+1]]-dis[dfw[lc]]+r.L);
	rt.R=max(r.R,dis[dfw[rc]]-dis[dfw[mid]]+l.R);
}
void node::update(int lc,int rc,int rt){
	if(lc==rc)return setting(dfw[rt]);
	if(rt<=mid)ch[0]->update(lc,mid,rt);
	else ch[1]->update(mid+1,rc,rt);
	merge(*this,*ch[0],*ch[1],lc,rc);
}
node *build(int lc,int rc){
	node *rt=pool_tail++;
	if(lc==rc)rt->setting(dfw[lc]);
	else{
		rt->ch[0]=build(lc,mid),rt->ch[1]=build(mid+1,rc);
		merge(*rt,*rt->ch[0],*rt->ch[1],lc,rc);
	}
	return rt;
}
void dfsf(int u){
	col[u]=sz[u]=1;
	for(int v,i=head[u];i;i=E[i].next){
		if(v=E[i].to,v!=fa[u]){
			dis[v]=dis[u]+E[i].w,fa[v]=u,dfsf(v),sz[u]+=sz[v];
			if(sz[son[u]]<sz[v])son[u]=v;
		}
	}
}
void dfss(int u,int f){
	top[u]=f,dfw[dfn[u]=++cnt]=u;
	tail[f]=max(tail[f],dfn[u]);
	if(son[u])dfss(son[u],f);
	for(int v,i=head[u];i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&v!=son[u])dfss(v,v),
		heap[u].insert(root[v]->L+dis[v]-dis[u]);
	heap[u].insert(0);
	if(top[u]==u){
		root[u]=build(dfn[u],tail[u]);
		lis.insert(root[u]->ret);
	}
}
void ask(int u){
	static int anc[22],tc;tc=0;
	for(int i=u;i;i=fa[top[i]])anc[tc++]=i;
	for(int i=tc-1;i>=0;--i){
		lis.erase(root[top[anc[i]]]->ret);
		if(i)heap[anc[i]].erase(root[top[anc[i-1]]]->L+dis[top[anc[i-1]]]-dis[anc[i]]);
	}
	if(col[u])heap[u].insert(0);
	else heap[u].erase(0);
	for(int i=0;i<tc;++i){
		int x=anc[i],t=top[x];
		root[t]->update(dfn[t],tail[t],dfn[x]);
		lis.insert(root[t]->ret);
		if(i+1<tc)heap[anc[i+1]].insert(root[t]->L+dis[t]-dis[anc[i+1]]);
	}
}
inline void read(int &res){
    static char ch;int flag=1;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
	read(n);
    for(int u,v,z,i=1;i<n;i++)read(u),read(v),read(z),addedge(u,v,z);
	dfsf(1),dfss(1,1),read(m),tal=n;
    while(m--){
    	scanf("%s",cmd);
        if(cmd[0]=='A'){
        	if(tal==0)puts("They have disappeared.");
        	else if(tal==1)puts("0");
        	else printf("%d\n",max(lis.top(),0));
        }else{
        	read(se);
        	if(!(col[se]^=1))--tal;
			else ++tal;ask(se);
        }
    }
    return 0;
}
```

这才第4道，不知道之后的5~7会如何的GG。。。