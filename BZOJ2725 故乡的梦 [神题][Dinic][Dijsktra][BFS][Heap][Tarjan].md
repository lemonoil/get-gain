
<h2>2725: [Violet 6]故乡的梦</h2><span class=green>Time Limit: </span>20 Sec&nbsp;&nbsp;<span class=green>Memory Limit: </span>128 MB<br><span class=green>Submit: </span>715&nbsp;&nbsp;<span class=green>Solved: </span>222<br>[<a href='submitpage.php?id=2725'>Submit</a>][<a href='problemstatus.php?id=2725'>Status</a>][<a href='bbs.php?id=2725'>Discuss</a>]</center><h2>Description</h2><div class=content><p><img height="405" alt="" width="663" src="http://www.lydsy.com/JudgeOnline/upload/201204/T3des(5).gif" /></p></div><h2>Input</h2><div class=content><p><img height="278" alt="" width="661" src="http://www.lydsy.com/JudgeOnline/upload/201204/T3input(5).gif" /></p></div><h2>Output</h2><div class=content><p><img height="119" alt="" width="398" src="http://www.lydsy.com/JudgeOnline/upload/201204/T3output(5).gif" /></p></div><h2>Sample Input</h2>
6 7 <br />
1 2 1 <br />
2 3 1 <br />
3 4 2 <br />
4 5 1 <br />
5 6 1 <br />
1 3 3 <br />
4 6 3 <br />
1 6 <br />
4 <br />
1 2 <br />
1 3 <br />
4 3 <br />
6 5 </span></div><h2>Sample Output</h2>
			<div class=content><span class=sampledata>7<br />
6<br />
Infinity<br />
7</span></div><h2>HINT</h2>
			<div class=content><p><p><img height="188" alt="" width="661" src="http://www.lydsy.com/JudgeOnline/upload/201204/T3hint(5).gif" /></p></p></div><h2>Source</h2>
			<div class=content><p><a href='problemset.php?search=interviewstreet--Going office '>interviewstreet--Going office </a></p></div>




别的不说光是这代码量我就服了，追求严谨，我写了网络流与Tarjan，各种花式作死。dinic只搞两次加了，当前弧没什么效果。。。还是用了优先队列，毕竟空间使用都是线性的，总的时间复杂度也很优，只有\\(O((n+m)+nlogn)\\),算法的时间瓶颈上线是dijsktra带来的，换成SPFA是否有更好的表现？
直接无耻地引用题解 by [DaD3zZ大佬%%%%](http://www.cnblogs.com/DaD3zZ-Beyonder/p/5787906.html)
>首先我们考虑，如果ST不连通，那显然全部输出Infinity

>我们先做两遍\\(Dijsktra\\)，求出S出发到所有点的单源最短路\\(ds[]\\)，以及T出发到所有点的单源最短路\\(dt[]\\)

>那么我们发现，如果删除的边不存在最短路径上，那么显然答案全都是\\(ds[T]\\)

>考虑利用\\(ds[]\\)和\\(dt[]\\)的信息，构建一种新的图  最短路径图Gs 即满足\\(ds[u]+val(u-->v)=ds[v]\\)的边所构成的图，同理做出Gt

>那么我们需要找到\\(Gs Gt\\)中的割边，因为只有割这种边，才会使得最短路径发生变化

>那么问题在于如何求，\\(Tarjan\\)的方法，正确性位置，不妨考虑最小割，
>所以我们假定每条边容量为1，我们在\\(Gs\\)上进行增广，如果\\(MinCut>=2\\)，那么任意删一条边对结果不造成影响，因为只需要知道是否>=2所以增广两次即可
>那么当最小割为1时，我们需要求割边，这时候利用\\(Tarjan\\)，
>在残余网络上跑tarjan求出所有SCC，记\\(belong[u]\\)为点u所在SCC的编号。显然有\\(belong[s]!=belong[t]\\)（否则s到t有通路，能继续增广）。
>①对于任意一条满流边\\((u,v)\\)，\\((u,v)\\)能够出现在某个最小割集中，当且仅当\\(belong[u]!=belong[v]\\)；
>②对于任意一条满流边\\((u,v\\)，\\((u,v)\\)必定出现在最小割集中，当且仅当\\(belong[u]==belong[s]\\)且\\(belong[v]==belong[t]\\)。  应用:BZOJ1797

>求出这些割边后，我们知道，删除非割边并不影响答案，所以输出$ds[T]$，只有割边会对答案有影响

>同样的，Gs中的割边反向就是Gt中的割边
那么我们求出这些割边，\\(cut(1~K) \\)，显然我们可以离线的处理出每条割边的答案，然后\\( O（1）\\)询问
我们对最短路径图\\(Gs\\)再进行改动，其中的割边我们设\\(val=1\\)，非割边\\(val=0\\)，然后我们可以求出\\(Gs\\)，\\(Gt\\)中\\(S/T\\)到每个点的最短路\\(（0/1）\\)构成
这个实现起来可以利用 BFS+双端队列 
然后我们可以离线的处理出每个\\(cut\\)的答案，这个过程可以利用\\(multiset/heap/线段树\\) 来实现
堆的方法：
维护一个小根堆，关键字是\\(ds[u]+val(u-->v)+dt[v]\\)，具体的方法就是 当前计算的是now，先将之前的状态\\(（Dt[v]>=Sum-now)\\)弹出，然后把当前的所有出边加入到堆中，然后用堆顶答案去更新接下来的值
线段树的方法：
区间覆盖取最小的经典模型，先修改，再DFS一遍整棵线段树即可
multiset的方法：
扫描cut的时候，记录所有可行的答案，然后取最小来更新即可
```cpp
#include<bits/stdc++.h>
#define RG
using namespace std;
template<class T>inline void read(T &res){
	static char ch;T flag=1;
	while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
	while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
//------------------init---------------------
const int N = 200005;
const long long INF = (1LL<<50);
int S,T,n,m,k,head[N],cnt=1;
//----------------dijsktra-------------------
long long ds[N],dt[N];
struct Edge{
	int from,to,w,nxt;
}E[N<<1];
void addedge(int x,int y,int z){
	E[++cnt].nxt=head[x],head[x]=cnt,E[cnt].to=y,E[cnt].w=z,E[cnt].from=x;
	E[++cnt].nxt=head[y],head[y]=cnt,E[cnt].to=x,E[cnt].w=z,E[cnt].from=y;
}
#define pairs pair<long long ,int>
priority_queue<pairs,vector<pairs>,greater<pairs> > Q;
void dijsktra(int s,int t,long long dis[N]){
	fill(dis,dis+N,INF);
	Q.push(make_pair(0,s)),dis[s]=0;
	while(!Q.empty()){
		long long dist=Q.top().first;
		int u=Q.top().second;Q.pop();
		if(dist>dis[u])continue;
		for(RG int v,i=head[u];i;i=E[i].nxt){
			if(v=E[i].to,dis[u]+E[i].w<dis[v]){
				dis[v]=dis[u]+E[i].w;
				Q.push(make_pair(dis[v],v));
			}
		}
	}
}
//------------------dinic--------------------
int last[N],first[N],num=1,tot=1;
struct List{
	int nxt,to,from,w;
}L[N<<1];
struct Road{
	int nxt,to,from,cap,w;
}G[N<<1];
void addroad(int x,int y,int w){
	G[++tot].nxt=last[x],last[x]=tot,G[tot].to=y,G[tot].from=x,G[tot].cap=w;
	G[++tot].nxt=last[y],last[y]=tot,G[tot].to=x,G[tot].from=y,G[tot].cap=0;
}
void addlist(int x,int y,int w){
	L[++num].nxt=first[x],first[x]=num,L[num].to=y,L[num].from=x,L[num].w=w;
}
void createGraph(){
	for(RG int i=1;i<=n;i++){
		for(RG int j=head[i];j;j=E[j].nxt){
			if(ds[i]+E[j].w==ds[E[j].to])
				addroad(i,E[j].to,1);
			if(dt[E[j].to]==dt[i]+E[j].w)
				addlist(i,E[j].to,0);
		}
	}
}
int h[N],cur[N];
bool bfs(){
	queue<int> que;
	memset(h,-1,sizeof(h));
	que.push(S),h[S]=0;
	while(!que.empty()){
		int u=que.front();que.pop();
		for(RG int v,i=last[u];i;i=G[i].nxt){
			if(v=G[i].to,G[i].cap&&h[v]==-1)
			 h[v]=h[u]+1,que.push(v);
		}
	}
	return h[T]!=-1;
}
int dfs(int u,int low){
	if(u==T||low==0)return low;
	int w,used=0;
	for(RG int &i=cur[u];i;i=G[i].nxt){
		if(G[i].cap&&h[G[i].to]==h[u]+1){
			w=dfs(G[i].to,min(low-used,G[i].cap));
			used+=w;
			G[i].cap-=w,G[i^1].cap+=w;
			if(used==low)return low;
		}
	}
	if(!used)h[u]=-1;
	return used;
}
int dinic(){
	int tmp=0,tim=2;
	while(bfs()&&tim--){
		for(register int i=1;i<=n;i++)cur[i]=last[i];
		tmp+=dfs(S,1);
	}
	return tmp;
}
//------------------Tarjan-------------------
int dfn[N],low[N],dft,st[N],sccon[N],scc,top,size[N];
bool insv[N];
void Tarjan(int u){
	low[u]=dfn[u]=++dft;
	insv[u]=1,st[++top]=u;
	for(RG int v,i=last[u];i;i=G[i].nxt){
		if(v=G[i].to,G[i].cap==0)continue;
		if(!dfn[v])Tarjan(v),low[u]=min(low[u],low[v]);
		else if(insv[v])low[u]=min(low[u],dfn[v]);
	}
	if(dfn[u]==low[u]){
		scc++;int v=0;
		while(u!=v)v=st[top--],size[scc]++,insv[v]=0,sccon[v]=scc;
	}
}
void Tarjan(){
	for(RG int i=1;i<=n;i++)
		if(!dfn[i])Tarjan(i);
}
//--------------double bfs-------------------
long long DS[N],DT[N];
bool markvis[N];
deque<int> dq;
void BFSF(){
	fill(DS,DS+N,INF);
	memset(markvis,0,sizeof(markvis));
	dq.push_back(S);DS[S]=0;
	while(!dq.empty()){
		int u=dq.front();dq.pop_front();
		if(markvis[u])continue;
		else markvis[u]=1;
		for(RG int v,i=last[u];i;i=G[i].nxt)if(v=G[i].to,!(i&1)){
			DS[v]=min(DS[u]+G[i].w,DS[v]);
			if(G[i].w)dq.push_back(v);
			else dq.push_front(v);
		}
	}
}
void BFSS(){
	fill(DT,DT+N,INF);
	memset(markvis,0,sizeof(markvis));
	dq.push_back(T);DT[T]=0;
	while(!dq.empty()){
		int u=dq.front();dq.pop_front();
		if(markvis[u])continue;
		else markvis[u]=1;
		for(RG int v,i=first[u];i;i=L[i].nxt){
			v=L[i].to,DT[v]=min(DT[u]+L[i].w,DT[v]);
			if(L[i].w)dq.push_back(v);
			else dq.push_front(v);
		}
	}
}
//------------------heap---------------------
struct node{
	int u,v;
	long long w;
	node(int u=0,int v=0,long long w=0):u(u),v(v),w(w){}
	inline bool operator  < (const node &rhs)const{
		return w>rhs.w;
	}
};
priority_queue<node> heap;
vector<int> VC[N];
int cut[N];
long long ans[N];
void work(){
	for(RG int i=1;i<=n;i++)
		if(ds[i]!=INF)VC[DS[i]].push_back(i);
	for(RG int i=0;i<scc;++i){
		int sz=VC[i].size();
		while(!heap.empty()&&DT[heap.top().v]>=DS[T]-i)heap.pop();
		for(RG int j=0;j<=sz-1;++j){
			int u=VC[i][j];
			for(RG int k=head[u];k;k=E[k].nxt)
				if(DS[E[k].to]>i&&cut[u]!=E[k].to)
					heap.push(node(u,E[k].to,ds[u]+E[k].w+dt[E[k].to]));
		}
		if(!heap.empty())ans[i]=heap.top().w;
	}
}
//------------------main---------------------
int main(){
	//freopen("in.txt","r",stdin);
	read(n),read(m);
	for(RG int x,y,z,i=1;i<=m;i++)
		read(x),read(y),read(z),addedge(x,y,z);
	read(S),read(T);
	dijsktra(S,T,ds),dijsktra(T,S,dt);
	read(k);
	if(ds[T]==INF){
		while(k--)puts("Infinity");return 0;
	}
	createGraph();
	if(dinic()>=2){
		while(k--)printf("%lld\n",ds[T]);return 0;
	}
	Tarjan();
	for(RG int i=2;i<=tot;i+=2)
		if(!G[i].cap&&sccon[G[i].from]!=sccon[G[i].to])
			cut[G[i].from]=G[i].to,G[i].w=1;
	for(RG int i=2;i<=num;i++)
		if(cut[L[i].to]==L[i].from)L[i].w=1;
	BFSF();BFSS();work();
	for(RG int x,y,i=1;i<=k;i++){
		read(x),read(y);
		if(cut[x]==y){
			if(ans[DS[x]])printf("%lld\n",ans[DS[x]]);
			else puts("Infinity");
		}else if(cut[y]==x){
			if(ans[DS[y]])printf("%lld\n",ans[DS[y]]);
			else puts("Infinity");
		}else if(cut[x]!=y&&cut[y]!=x)printf("%lld\n",ds[T]);
	}
	return 0;
}
```


![这里写图片描述](http://img.blog.csdn.net/20170922115811109?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVtb25vaWw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)

![这里写图片描述](http://img.blog.csdn.net/20170922115823239?watermark/2/text/aHR0cDovL2Jsb2cuY3Nkbi5uZXQvbGVtb25vaWw=/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70/gravity/SouthEast)