点分治模板题，我有个max细节没注意到，root一直取错，小数据AC，大数据就TLE了。。。。找了半天才发现。。。
MD
点分治+堆维护路径值（与QTree4很像）。
code

```cpp
#include<algorithm>
#include<cstring>
#include<cstdio>
#include<cmath>
#include<queue>
const int N=200220;
using namespace std;
inline int read(){
    static char ch;int flag=1,res=0;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;
	return res*=flag;
}
int to[N],nxt[N];
struct node{
	int u,dis;
	bool operator < (const node &rhs)const{
		return dis>rhs.dis;
	}
};
priority_queue<node> q[N];
int n,tot=1,cnt,x,y,zx,zy,t,tal,dt,root;
int deep[N],head[N],sz[N],mx[N],fa[N][20],dis[N][20],tail[N];
bool vis[N],col[N];
inline void dfs1(int u,int f){
	sz[u]=1;for(int i=head[u];i;i=nxt[i])
		if(to[i]!=f&&!vis[to[i]])dfs1(to[i],u),sz[u]+=sz[to[i]];
}
inline void dfs2(int u,int f){
	mx[u]=tal-sz[u];for(int i=head[u];i;i=nxt[i])
		if(to[i]!=f&&!vis[to[i]])dfs2(to[i],u),mx[u]=max(mx[u],sz[to[i]]);
	if(mx[u]<dt)dt=mx[u],root=u;
}
inline void dfs3(int u,int f,int dep,int d){
	fa[u][dep]=root,dis[u][dep]=d;
	for(int i=head[u];i;i=nxt[i])
		if(to[i]!=f&&!vis[to[i]])dfs3(to[i],u,dep,d+1);
}
inline void dfs(int u,int dep){
	dfs1(u,0);
	tal=dt=sz[u];dfs2(u,0);
	vis[u=root]=true;tail[u]=dep;
	dfs3(u,0,dep,0);
	for(int i=head[u];i;i=nxt[i])
		if(!vis[to[i]])dfs(to[i],dep+1);
}
int main(){
	n=read();
	for(int i=1;i<n;i++){
		x=read();y=read();
		to[++tot]=y,nxt[tot]=head[x],head[x]=tot,
	    to[++tot]=x,nxt[tot]=head[y],head[y]=tot;
	}
	dfs(1,0);n=read();zx=0;
	while(n--){
		int i;
		if(read()){
			y=read();
			if(zx){
				zy=1<<30;
				for(i=tail[y];i>=0;i--){
					t=fa[y][i];
					while(!q[t].empty()&&!col[q[t].top().u])q[t].pop();
					if(!q[t].empty())zy=min(zy,q[t].top().dis+dis[y][i]);
				}
				printf("%d\n",zy);
			}else puts("-1");
		}else if(col[y=read()]^=1)for(i=tail[y],zx++;i>=0;i--)q[fa[y][i]].push((node){y,dis[y][i]});else zx--;
	}
	return 0;
}
```