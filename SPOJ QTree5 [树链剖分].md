感觉之前拿点分治水过心里过意不去，。。。。。mdzz，我还是打了一份树链剖分的code，具体方法与QTree4相同（详见代码），维护线段树的合并时的值。

```cpp
#include<bits/stdc++.h>
#define mid ((l+r)>>1)
#define pf push_front
using namespace std;
const int N=1e5+1e2;
const int INF=0x3f3f3f3f;
struct node{
	int l,r;
	node(int l=0,int r=0):l(l),r(r){}
}seg[N<<2];
int n,dfs_cnt,cnt,cs,tot,Q,xx,yy;
int fa[N],head[N],id[N],dfn[N],top[N],sz[N],s[N],c[N];
struct data{
	int to,next;
}E[N<<1];
int ls[N<<2],rs[N<<2],root[N];
deque<int> pat;
multiset<int> ch[N];
void addedge(int u,int v){
	E[++tot].to=v,E[tot].next=head[u],head[u]=tot;
	E[++tot].to=u,E[tot].next=head[v],head[v]=tot;
}
inline void dfsf(int u){
    sz[u]=1;for(register int v,i=head[u];i;i=E[i].next)
        if(v=E[i].to,v!=fa[u])fa[v]=u,dfsf(v),sz[u]+=sz[v];
}
inline void dfss(int u,int f){
    dfn[id[u]=++dfs_cnt]=u;
    top[u]=f,s[f]++;
    register int mx=0,w;
	for(register int v,i=head[u];i;i=E[i].next)
        if(v=E[i].to,v!=fa[u])if(mx<sz[v])mx=sz[v],w=v;
    if(mx)dfss(w,f);
    for(register int v,i=head[u];i;i=E[i].next)
        if(v=E[i].to,v!=fa[u]&&v!=w)dfss(v,v);
}
inline node merge(node& a,node& b,int l1,int l2,int l3){
    node res;
    res.l=min(a.l,l1+l2+b.l);
    res.r=min(b.r,l2+l3+a.r);
    return res;
}
inline void maintain(int rt,int x){
    int d1=INF,d2=INF;
	if(c[x])d1=d2=0;
    if(ch[x].size())d1=min(d1,*ch[x].begin());
    if(ch[x].size()>1)d2=min(d2,*(++ch[x].begin()));
    seg[rt].l=seg[rt].r=d1;
}
inline void build(int rt,int l,int r){
    if(l==r){
        register int u=dfn[l];
        for(register int v,i=head[u];i;i=E[i].next)
        	if(v=E[i].to,v!=fa[u]&&top[v]!=top[u])
        		build(root[v]=++cnt,id[v],id[v]+s[v]-1),
            	ch[u].insert(seg[root[v]].l+1);
        maintain(rt,u);
	}else{
        build(ls[rt]=++cnt,l,mid);
        build(rs[rt]=++cnt,mid+1,r);
        seg[rt]=merge(seg[ls[rt]],seg[rs[rt]],mid-l,1,r-mid-1);
    }
}
inline void find(int u){
    pat.clear();
    register int l=0;
    while(u){
        register int f=top[u];
        pat.pf(l),pat.pf(u),pat.pf(f);
        l+=id[u]-id[f]+1,u=fa[f];
    }
}
inline void update(int rt,int l,int r,int i){
    if(l==r){
        register int u=dfn[l];
        if(i+3<pat.size()){
            register int nd=pat[i+2];
            ch[u].erase(ch[u].find(seg[root[nd]].l+1));
            update(root[nd],id[nd],id[nd]+s[nd]-1,i+3);
            ch[u].insert(seg[root[nd]].l+1);
        }
        maintain(rt,u);
    }else{
        if(id[pat[i]]<=mid)update(ls[rt],l,mid,i);
        else update(rs[rt],mid+1,r,i);
        seg[rt]=merge(seg[ls[rt]],seg[rs[rt]],mid-l,1,r-mid-1);
	}
}
inline int query(int rt,int l,int r,int i){
	int res=INF;
    if(l==r){
        if(i+3<pat.size()){
            register int nd=pat[i+2];
            res=query(root[nd],id[nd],id[nd]+s[nd]-1,i+3);
        }
        res=min(res,seg[rt].l+pat[i+1]);
        return res;
    }else{
        if(id[pat[i]]<=mid)res=min(query(ls[rt],l,mid,i),mid+1-id[pat[i]]+seg[rs[rt]].l+pat[i+1]);
        else res=min(query(rs[rt],mid+1,r,i),seg[ls[rt]].r+id[pat[i]]-mid+pat[i+1]);
        return res;
    }
}
inline void read(int &res){
    static char ch;int flag=1;
    while((ch=getchar())<'0'||ch>'9')if(ch=='-')flag=-1;res=ch-48;
    while((ch=getchar())>='0'&&ch<='9')res=res*10+ch-48;res*=flag;
}
int main(){

    read(n);
    for(register int a,b,i=1;i<n;i++)
        read(a),read(b),addedge(a,b);
    dfsf(1),dfss(1,1);read(Q);
    build(root[1]=++cnt,1,s[1]);
    while(Q--){
        read(xx),read(yy),find(yy);
        if(xx){
            if(!cs){puts("-1");continue; }
            printf("%d\n",query(1,1,s[1],1));
    	}else{
            cs+=1-c[yy]*2,c[yy]=1-c[yy];
            update(1,1,s[1],1);
        }
    }
    return 0;
}
```