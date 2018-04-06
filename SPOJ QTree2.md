仍然是上一道题的模板，
这道题重点是要学会倍增来查找路径上的点。即kfa()
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
int n,tot,head[N],deep[N],son[N],sz[N],top[N],dfw[N],mx[N<<2],ft[24][N],dis[N];
void addedge(int x,int y,int z){
    E[tot].to=y,E[tot].next=head[x],E[tot].w=z,head[x]=tot++;
    E[tot].to=x,E[tot].next=head[y],E[tot].w=z,head[y]=tot++;
}
void dfsf(int u){
	sz[u]=1;
	for(int v,i=head[u];~i;i=E[i].next){
		if(v=E[i].to,v!=ft[0][u]){
			dis[v]=E[i].w+dis[u],deep[v]=deep[u]+1,ft[0][v]=u,dfsf(v),sz[u]+=sz[v];
			if(sz[son[u]]<sz[v])son[u]=v;
		}
	}
}
/*
void dfss(int u,int f){
	top[u]=f,dfn[u]=++cnt,dfw[cnt]=u;
	if(son[u])dfss(son[u],f);
	for(int v,i=head[u];~i;i=E[i].next)
		if(v=E[i].to,v!=fa[u]&&v!=son[u])dfss(v,v);
}
int lca(int u,int v){
	while(top[u]!=top[v]){
		if(deep[top[u]]>deep[top[v]])swap(u,v);
        v=fa[top[v]];
	}
	if(deep[u]>deep[v])swap(u,v);
	return u;
}
*/
void lca_init() {  
    for(int k=0;k <21;++k){  
        for(int i=1;i<=n;++i){  
            if(ft[k][i]==0)ft[k+1][i]=0;  
            else ft[k+1][i]=ft[k][ft[k][i]];  
        }  
    }  
}  
int lca(int u,int v) {  
    if(deep[u]>deep[v])swap(u,v);  
    for(int k=0;k<22;++k)  
        if((deep[v]-deep[u])>>k&1)v=ft[k][v];
    if(v==u)return u;  
    for(int k=21;k>=0;--k)  
        if(ft[k][u]!=ft[k][v])  
            u=ft[k][u],v=ft[k][v];  
    return ft[0][u];  
}  
int kfa(int u,int k) {  
    for(int i=0;i<22;++i)  
        if(k>>i&1)u=ft[i][u];  
    return u;
}  
void init(){
	tot=0;
	memset(head,-1,sizeof(head)),clr(deep),clr(sz),clr(son),clr(dfw),clr(ft);
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
		ft[0][1]=-1,dfsf(1),lca_init();
	    while(scanf("%s",cmd)&&cmd[1]!='O'){
	    	int s,t,q,x;
	        read(s),read(t),q=lca(s,t);
	        if(cmd[0]=='K'){
	        	int w;read(w);
	        	x=deep[s]-deep[q];
	        	if(x+1>=w)
	        		printf("%d\n", kfa(s, w - 1));
	        	else  
                    printf("%d\n", kfa(t, deep[t] + deep[s] - 2 * deep[q] + 1 - w));  
	        }
	        else printf("%d\n",dis[s]+dis[t]-dis[q]*2);
	    }
	}
    return 0;
}
```