直接上第一道题的模板，
关键是转化颜色到具体数值的思想很重要。
详细关于黑白分析的细节看代码。
吐槽一句：样例乱打都能过。。。

```cpp
#include<algorithm>
#include<cstring>
#include<cstdio>
#include<cmath>
#define clr(x) memset((x),0,sizeof(x))
const int N=200020,inf=0x3f3f3f3f;
using namespace std;
char cmd[10];
struct data{
    int to,next;
}E[N<<1];
int n,m,tot,head[N],deep[N],son[N],sz[N],fa[N],top[N],flag[N],mx[N<<2],dfn[N],cnt,dfw[N];
void addedge(int x,int y){
    E[tot].to=y,E[tot].next=head[x],head[x]=tot++;
    E[tot].to=x,E[tot].next=head[y],head[y]=tot++;
}
void dfsf(int u){
	sz[u]=1;
	for(int v,i=head[u];~i;i=E[i].next){
		if(v=E[i].to,v!=fa[u]){
			deep[v]=deep[u]+1,fa[v]=u,dfsf(v),sz[u]+=sz[v];
			if(sz[son[u]]<sz[v])son[u]=v;
		}
	}
}
void dfss(int u,int f){
	top[u]=f,dfn[++cnt]=u,dfw[u]=cnt;
	if(son[u])dfss(son[u],f);
	for(int v,i=head[u];~i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&v!=son[u])dfss(v,v);
}
#define R rt<<1|1
#define L rt<<1
#define mid ((rc+lc)>>1)
void build(int rt,int lc,int rc){
	if(lc==rc){mx[rt]=inf;return;}
	build(L,lc,mid),build(R,mid+1,rc);
	mx[rt]=min(mx[L],mx[R]);
}
int query(int rt,int lc,int rc,int l,int r){
	if(rc==r&&lc==l)return mx[rt];
	if(r<=mid)return query(L,lc,mid,l,r);
	if(mid<l)return query(R,mid+1,rc,l,r);
	return min(query(L,lc,mid,l,mid),query(R,mid+1,rc,mid+1,r));
}
void update(int x,int rt,int lc,int rc){
	if(lc==rc){if(mx[rt]==inf)mx[rt]=x;else mx[rt]=inf;return;}
	if(x<=mid)update(x,L,lc,mid);
	else update(x,R,mid+1,rc);
	mx[rt]=min(mx[L],mx[R]);
}
int ask(int u){
	int ret=inf;
	while(u)ret=min(ret,query(1,1,n,dfw[top[u]],dfw[u])),u=fa[top[u]];
	if(ret==inf)ret=-1;
	else ret=dfn[ret];
	return ret;
}
inline void read(int &res){
    static char ch;int flag=1;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){
	read(n),read(m);memset(head,-1,sizeof(head));
    for(int u,v,z,i=1;i<n;i++)read(u),read(v),addedge(u,v);
	fa[1]=0,dfsf(1),dfss(1,1),build(1,1,n);
    for(int i=1;i<=m;i++){
		int s,t;
        read(s),read(t);
        if(s)printf("%d\n",ask(t));
        else update(dfw[t],1,1,n);
    }
    return 0;
}
```