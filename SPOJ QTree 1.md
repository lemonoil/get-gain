树链剖分+线段树裸题，**注意边权维护的细节。**

```cpp
#include<algorithm>
#include<cstring>
#include<cstdio>
#include<cmath>
#define clr(x) memset((x),0,sizeof(x))
const int N=200020,inf=~0U>>1;
using namespace std;
char cmd[10];
struct data{
    int to,next,w;
}E[N<<1];
int n,tot,head[N],deep[N],son[N],sz[N],fa[N],top[N],dfw[N],mx[N<<2],dfn[N],w[N],cnt,b[N];
void addedge(int x,int y,int z){
    E[tot].to=y,E[tot].next=head[x],E[tot].w=z,head[x]=tot++;
    E[tot].to=x,E[tot].next=head[y],E[tot].w=z,head[y]=tot++;
}
void dfsf(int u){
	sz[u]=1;
	for(int v,i=head[u];~i;i=E[i].next){
		if(v=E[i].to,v!=fa[u]){
			b[i/2+1]=v,w[v]=E[i].w,deep[v]=deep[u]+1,fa[v]=u,dfsf(v),sz[u]+=sz[v];
			if(sz[son[u]]<sz[v])son[u]=v;
		}
	}
}
void dfss(int u,int f){
	top[u]=f,dfn[u]=++cnt,dfw[cnt]=u;
	if(son[u])dfss(son[u],f);
	for(int v,i=head[u];~i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&v!=son[u])dfss(v,v);
}
#define R rt<<1|1
#define L rt<<1
#define mid ((rc+lc)>>1)
void build(int rt,int lc,int rc){
	if(lc==rc){mx[rt]=w[dfw[lc]];return;}
	build(L,lc,mid),build(R,mid+1,rc);
	mx[rt]=max(mx[L],mx[R]);
}
int query(int rt,int lc,int rc,int l,int r){
	if(rc==r&&lc==l)return mx[rt];
	if(r<=mid)return query(L,lc,mid,l,r);
	if(l>mid)return query(R,mid+1,rc,l,r);
	return max(query(L,lc,mid,l,mid),query(R,mid+1,rc,mid+1,r));
}
void update(int x,int rt,int val,int lc,int rc){
	if(lc==rc){mx[rt]=val;return;}
	if(x<=mid)update(x,L,val,lc,mid);
	else update(x,R,val,mid+1,rc);
	mx[rt]=max(mx[L],mx[R]);
}
int ask(int u,int v){
	int ret=-inf;
	while(top[u]!=top[v]){
		if(deep[top[u]]>deep[top[v]])swap(u,v);
        ret=max(ret,query(1,1,n,dfn[top[v]],dfn[v]));
        v=fa[top[v]];
	}
	if(deep[u]>deep[v])swap(u,v);
    if(u!=v)ret=max(ret,query(1,1,n,dfn[u]+1,dfn[v]));
	return ret;
}
void init(){
	tot=cnt=0;
	memset(head,-1,sizeof(head)),clr(deep),clr(sz),clr(fa),clr(w),clr(mx),clr(son),clr(dfw),clr(dfn),clr(b);
}
inline void read(int &res){
    static char ch;int flag=1;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
	int t;read(t);
	while(t--){
		init();
		read(n);
	    for(int u,v,z,i=1;i<n;i++)read(u),read(v),read(z),addedge(u,v,z);
		fa[1]=-1,dfsf(1),dfss(1,1),build(1,1,n);
	    while(scanf("%s",cmd)&&cmd[0]!='D'){
	    	int s,t;
	        read(s),read(t);
	        if(cmd[0]=='C')update(dfn[b[s]],1,t,1,n);
	        else printf("%d\n",ask(s,t));
	    }
	}
    return 0;
}
```