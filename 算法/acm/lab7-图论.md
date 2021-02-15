## A - 小希的迷宫

![image-20210208155041874](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210208155041874.png)

问题的本质就是问一个图里面有没有环

可以用公式(点数=边数-1)来判断

也可以用并查集:

- **对于要合并的两个点,父节点不同**

- **只有一个连通集,不能有两个**

```
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <vector>
#include <algorithm>
#include<queue>
using namespace std;
#define mm(x,b) memset(x,b,sizeof(x))
#define rep(i,a,n) for (int i=a;i<n;i++)
int vis[100000 + 5];
int arr[100000+5];
class Union{
public:
    Union()
    {
    }
    int find(int n)
    {
        if(arr[n]==n)
            return n;
        else
            return arr[n]=find(arr[n]);
    }
    int isConnected(int i,int j)
    {
        return find(i)==find(j);
    }
    int Connect(int i,int j)
    {
        int fai=find(i);
        int faj=find(j);
        //cout<<i<<" "<<j<<" "<<fai<<" "<<faj<<endl;
        if(fai!=faj)
        {
            arr[fai]=faj;
            return true;
        }
        else
        {
            return false;
        }
    }
};
int main()
{
    int start,end;
    Union u;
    while(1)
    {
        bool flag=true;
        rep(i,0,100001)
            arr[i]=i;
        mm(vis, 0);
        while(1)
        {
             cin>>start>>end;
             if(!start&&!end)break;
             if(start==-1&&end==-1)
                 return 0;
             if(!u.Connect(start,end))
             {
                flag=false;
             }else{
                 vis[start]++;
                 vis[end]++;
             }
        }
        int cnt=0;
        rep(i,0,100001)
        {
            if(vis[i]&&arr[i]==i)
                cnt++;
        }
        if(!flag||cnt>1) cout<<"No"<<endl;
        else cout<<"Yes"<<endl;
    }
}
```

## B - Is It A Tree?

和A一样

## C - 食物链

带权并查集(看图论专题的题解)

## D - Wireless Network(记录形并查集)

地震发生在东南亚。ACM(亚洲合作医疗团队)已经与lap计算机建立了无线网络，但是一次意外的余震袭击，网络中的所有计算机都被破坏了。由于硬件的限制，每台计算机只能与距离它不超过几米的计算机直接通信。但是everv计算机可以被看作是另两台计算机之间通信的中介，也就是说，如果计算机A和计算机B可以直接通信，或者有一台计算机C可以同时与计算机A和计算机B通信，那么计算机A和计算机B之间也可以通信在网络修复过程中，工作人员可以随时进行两种操作。修理一台计算机，或测试两台计算机是否能通信。您的工作是回答所有的测试操作。

给你一些点，当前有两种操作，修复和测试。

然后输出测试的结果。。

因为不知道那些计算机已经修复,所以先记录已经修复过的并查集

```
#include <iostream>
#include <stdio.h>
#include <cmath>
#include <cstring>

using namespace std;
#define rep(i,a,n) for(int i=a;i<=n;i++)
class node{
public:
    int x,y;
    node(){}
    node(int x,int y){
        this->x=x;
        this->y=y;
    }
    double dist(node t)
    {
        return sqrt((double)((x-t.x)*(x-t.x)+(y-t.y)*(y-t.y)));
    }
};
const int maxN = 10005;
node arr[maxN];
int parent[maxN];
int vis[maxN];
int cnt=0;
int n,d;
int find(int x )
{
    if(x==parent[x])
        return x;
    else
        return x=find(parent[x]);
}
int canChat(int i,int j)
{
    return find(i)==find(j);
}
void Union(int i,int j)
{
    int fi=find(i);
    int fj=find(j);
    if(fi!=fj)
    {
        parent[fi]=fj;
    }
}
void FindUnion(int x)
{
    for(int i=0; i < cnt; i++)
    {
        if(arr[vis[i]].dist(arr[x]) <= d + 1e-6)
            Union(x, vis[i]);
    }
    vis[cnt++]=x;
}
int main()
{
    cin>>n>>d;
    int x,y;
    rep(i,1,n)
        parent[i]=i;
    memset(vis,0,sizeof(vis));
    for (int i = 1; i <=n ; ++i) {
        cin>>x>>y;
        arr[i]=node(x,y);
    }
    char operate;
    getchar();
    while(cin>>operate)
    {
        if(operate=='S')
        {
            cin>>x>>y;
            if(canChat(x,y))
                cout<<"SUCCESS"<<endl;
            else
                cout<<"FAIL"<<endl;
        }else{
            cin>>x;
            FindUnion(x);
        }
        getchar();
    }
}
```

## E - Heavy Transportation

背景雨果·Heavy很高兴。货物搬运工项目结束后，他现在可以扩大业务了。但他需要一个聪明的人告诉他，是否真的有一个从他的客户建造的巨型钢铁起重机到需要它的地方的所有街道都能承载重量的wav。幸运的是，他已经有了城市的规划，包括所有的街道和桥梁以及所有允许的重量。不幸的是，他不知道如何找到最大载重能力，以便告诉他的客户起重机的重量有多大。但你一定知道。问题你会得到城市的平面图，用十字路口之间的街道(有重量限制)来描述，从1到n编号。你的任务是找出从十字路口1 (Hugo’s place)到十字路口n (customer’s place)可以运输的最大重量。您可以假设至少有一条路径。所有的街道都可以双向通行

Input

The first line contains the number of scenarios (city plans). For each city the number n of street crossings (1 <= n <= 1000) and number m of streets are given on the first line. The following m lines contain triples of integers specifying start and end crossing of the street and the maximum allowed weight, which is positive and not larger than 1000000. There will be at most one street between each pair of crossings.

Output

The output for every scenario begins with a line containing "Scenario #i:", where i is the number of the scenario starting at 1. Then print a single line containing the maximum allowed weight that Hugo can transport to the customer. Terminate the output for the scenario with a blank line.

Sample Input

```
1
3 3
1 2 3
1 3 4
2 3 5
```

Sample Output

```
Scenario #1:
4
```

简单最短路,dijkstra即可

```
#include<cmath>
#include<cstdio>
#include<cstring>
#include<algorithm>
#include <queue>
#include <iostream>

#define INF 0x3f3f3f3f
using namespace std;
const int maxn=2000;
int weight[maxn][maxn];
int dist[maxn];
bool vis[maxn];
int n,m;
void dijkstra()
{
    for(int i=1; i<=n; i++)
    {
        dist[i]=weight[1][i];
        vis[i]=0;
    }
    for(int i=1; i<=n; i++)
    {
        int m=-1;
        int x=-1;
        for(int j=1; j<=n; j++)
            if(!vis[j] && dist[j] > m)
                m=dist[x=j];
        if(x!=-1)
        {
            vis[x]=1;
            for(int j=1; j<=n; j++)
                if(!vis[j] && dist[j] < min(dist[x], weight[x][j]))
                    dist[j]=min(dist[x], weight[x][j]);//维护d数组
        }
    }
}
int main()
{
    int T,t=1;
    cin>>T;
    while(T--)
    {
        cout<<"Scenario #"<<t++<<":"<<endl;
        cin>>n>>m;
        for(int i=1; i<=n; i++)
            for(int j=1; j<=n; j++)
                weight[i][j]= i == j ? 0 : -1;
        for(int i=1; i<=m; i++)
        {
            int x,y,z;
            cin>>x>>y>>z;
            if(weight[x][y] < z)
                weight[x][y]= weight[y][x]=z;
        }
        dijkstra();
        cout<<dist[n]<<endl;
        cout<<endl;
    }
}

```

## G - Candies

班上有n个同学，现在有一些糖要分给他们，设第i个同学得到的糖为p[i]，分糖必须满足条件：第i个同学要求第j个同学的糖不能超过自己k个，即p[j] - p[i] <= k，k >= 0。要求在满足这些条件的情况下，求出p[n] - p[1]的最大值。

由p[j] - p[i] <= k可得p[j] <= p[i] + k

在单源最短路径的[算法](http://lib.csdn.net/base/datastructure)中有一步是“若mindis[j] > mindis[i] + dis[i][j]，则mindis[j] = mindis[i] + dis[i][j]，这样就满足mindis[j] <= mindis[i] + dis[i][j]”。因此本题可以使用单源最短路径的算法来解决，对于“第i个同学要求第j个同学的糖不能超过自己k个，即p[j] - p[i] <= k，k >= 0”这个条件，建立一条边(i->j)=k，由于不含负权路径，因此建立完所有边之后以第1个同学为起点，采用Dijkstra+Heap求最短路径即可。除了Dijkstra也可以利用Spfa+Stack算法求解，但由于数据原因必须用Stack，如果用Queue则会超时。

```
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;
const int N = 30000 + 100;
const int M = 150000 + 100;
int u[M],v[M],c[M];
int head[N],nexth[M];
int dist[N];
const int inf = 0x3f3f3f3f;
struct edge
{
    int vv,cc;
    edge(int vv, int cc): vv(vv), cc(cc){}
    edge(){}
    bool operator < (const edge &t1)const
    {
        return cc>t1.cc;
    }
};
priority_queue<edge>que;
int n,m;
void dijkstra()
{
    int i,j;
    int uu,vv,cc;
    fill(dist, dist + n + 1, inf);
    que.push(edge(1, 0));
    dist[1]=0;
    while(!que.empty()) {
        edge p = que.top();
        que.pop();
        uu = p.vv;
        if(dist[uu] < p.cc) continue;
        for(i=head[uu]; i != -1; i=nexth[i]) {
            vv = v[i],cc = c[i];
            if(dist[vv] > dist[uu] + cc) {
                dist[vv]= dist[uu] + cc;
                que.push(edge(vv, dist[vv]));
            }
        }
    }
    return ;
}
int main()
{
    int i,j;
    scanf("%d%d",&n,&m);
    memset(head, -1, sizeof(head));
    memset(nexth, -1, sizeof(nexth));
    for(i=1;i<=m;i++) {
        scanf("%d%d%d",&u[i],&v[i],&c[i]);
        nexth[i] = head[u[i]];
        head[u[i]] = i;
    }
    dijkstra();
    printf("%d\n", dist[n]);
    return 0;
}
```







