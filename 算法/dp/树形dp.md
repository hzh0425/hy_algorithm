## 一 入门

一个问题，什么是树形dp？
树形dp顾名思义就是在“树”上进行动态规划，通过遍历树等一些操作来完成题目要求。（啥！！，你说你不知道啥是树，那......~~放弃吧~~，自行百度）
在树形动态规划中是建立在树上的，由树中的父节点和子节点的关系推出来的。一般有两种，
1、就是有子节点推到父节点，即跟的子节点传递有用的信息给父节点，最终有父节点再求出最优解。（此形式较为常用）
2、从父节点推至子节点，这种情况的意思是需要取所有点来求出父节点的值，再减去要除的子节点的dp状态，再将其转移，这样就可由根推至叶。
好了说了这么多了，我们先来看一道题，
题目描述

某大学有N个职员，编号为1~N。他们之间有从属关系，也就是说他们的关系就像一棵以校长为根的树，父结点就是子结点的直接上司。现在有个周年庆宴会，宴会每邀请来一个职员都会增加一定的快乐指数Ri，但是呢，如果某个职员的上司来参加舞会了，那么这个职员就无论如何也不肯来参加舞会了。所以，请你编程计算，邀请哪些职员可以使快乐指数最大，求最大的快乐指数。

题目分析：
我们从题目中可以看出这是一道非常明显的树形dp，我们可以根据题目所给关系进行构建员工树，将所有人的上司（老板）作为树的根节点，其余的根据题目关系构建子节点。建树的方式也非常简单，就是使用链式前向星进行建树。
说完如何建树，我们来看这道题的状态转移方程。
不难看出，本题分为三种情况
1、就是一个职员的上司去，而剩下的这个上司的职员就不会去。
2、一个职员的上司不去，而那个职员去
3、一个职员的上司不去，而那个职员也不去。
理清了这个转移思路后，我们就十分简单的推出了状态转移方程。

```
void dfs(int u,int fa){
    dp[u][0]=0,dp[u][1]=v[u];//0代表u这个人不去，1代表u这个人去 
    for(int i=head[u];i;i=e[i].next){
        int v=e[i].to;
        if(v==fa)   continue;
        dfs(v,u);
        dp[u][0]+=max(dp[v][1],dp[v][0]);//若u不去，那么他的员工v可去可不去，选一个最优的 
        dp[u][1]+=dp[v][0];//如果u去，那v一定不去 
    }
}
```

这样，这道题我们就非常简单的解决了，下面是AC代码

```
#include<bits/stdc++.h>
using namespace std;
const int N=6005;

int n,cnt,s;//cent代表编号 
int v[N],head[N],dp[N][2],ct[N];//ct标记是否为谁的上司 ，v【】标记给员工的快乐指数 ，dp[][]记录当前状态的最大快乐值 
struct node{int to,next;}e[N<<1];
inline void add(int u,int v){e[++cnt].to=v;e[cnt].next=head[u];head[u]=cnt;}
void dfs(int u,int fa){
    dp[u][0]=0,dp[u][1]=v[u];//0代表u这个人不去，1代表u这个人去 
    for(int i=head[u];i;i=e[i].next){
        int v=e[i].to;
        if(v==fa)   continue;
        dfs(v,u);
        dp[u][0]+=max(dp[v][1],dp[v][0]);//若u不去，那么他的员工v可去可不去，选一个最优的 
        dp[u][1]+=dp[v][0];//如果u去，那v一定不去 
    }
}
int main(){
    scanf("%d",&n);
    for(int i=1;i<=n;i++)   scanf("%d",&v[i]);
    for(int i=1;i<n;i++){
        int u,v;     
        scanf("%d%d",&u,&v);
        ct[u]=1;//代表u有上司 
        add(v,u); 
    }
    for(int i=1;i<=n;i++){
        if(!ct[i]){//如果i没有上司，i即为根节点，从他开始遍历 
            s=i;
            dfs(i,0);
            break;
        }   
    }
    printf("%d",max(dp[s][1],dp[s][0]));
}
```

这道题就这样结束了。~~~~

## 二题目集

### 1.战略游戏

**题目描述**

Bob喜欢玩电脑游戏，特别是战略游戏。但是他经常无法找到快速玩过游戏的办法。现在他有个问题。

他要建立一个古城堡，城堡中的路形成一棵树。他要在这棵树的结点上放置最少数目的士兵，使得这些士兵能了望到所有的路。

注意，某个士兵在一个结点上时，与该结点相连的所有边将都可以被了望到。

请你编一程序，给定一树，帮Bob计算出他需要放置最少的士兵.

**输入格式**

第一行 N，表示树中结点的数目。

第二行至第N+1行，每行描述每个结点信息，依次为：该结点标号i，k(后面有k条边与结点I相连)。

接下来k个数，分别是每条边的另一个结点标号r1，r2，...，rk。

对于一个n(0<n<=1500)个结点的树，结点标号在0到n-1之间，在输入数据中每条边只出现一次。

**输出格式**

输出文件仅包含一个数，为所求的最少的士兵数目。

例如，对于如下图所示的树：

```
       2 
      / 
0---1
      \
       3      
```

答案为1（只要一个士兵在结点1上）。

**输入输出样例**

**输入 #1**复制

```
4
0 1 1
1 2 2 3
2 0
3 0
```

**输出 #1**复制

```
1
```

【分析】

题目相当于需要求覆盖这颗树需要的最小点数

用 ![Dp_{i,0/1}Dpi,0/1](https://private.codecogs.com/gif.latex?Dp_{i%2C0/1}Dpi%2C0/1) 表示在这棵树中，**以 ii 为根节点的子树**在**选/不选根节点**的情况下，覆盖这棵树所有边需要的**最小点数**

所以，当不选这个节点 ii 时，则所有 以**其子节点为根节点**的子树 都必选根节点

当选择这个节点 ii 时，它能连接到所有的子节点，所以 以**其子节点为根节点**的子树 可以选则其根节点，也可以不选

归纳成方程组，可能更容易理解：

![\begin{cases}Son_i\neq \varnothing\\\ \\ \displaystyle Dp_{i,0}=\sum_{j\in Son_i}Dp_{j,1}\\\ \\\displaystyle Dp_{i,1}=1+\sum_{j\in Son_i}min(Dp_{j,0},Dp_{j,1})\end{cases}⎩⎪⎪⎪⎪⎪⎪⎪⎪⎪⎨⎪⎪⎪⎪⎪⎪⎪⎪⎪⎧Soni≠∅ Dpi,0=j∈Soni∑Dpj,1 Dpi,1=1+j∈Soni∑min(Dpj,0,Dpj,1)](https://private.codecogs.com/gif.latex?\begin{cases}Son_i\neq \varnothing\\\ \\ \displaystyle Dp_{i%2C0}%3D\sum_{j\in Son_i}Dp_{j%2C1}\\\ \\\displaystyle Dp_{i%2C1}%3D1&plus;\sum_{j\in Son_i}min(Dp_{j%2C0}%2CDp_{j%2C1})\end{cases}%u23A9%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23A8%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23AA%u23A7%u200BSoni%u200B%u2260%u2205 Dpi%2C0%u200B%3Dj%u2208Soni%u200B%u2211%u200BDpj%2C1%u200B Dpi%2C1%u200B%3D1&plus;j%u2208Soni%u200B%u2211%u200Bmin(Dpj%2C0%u200B%2CDpj%2C1%u200B)%u200B)

其中，Son_iSoni 为 ii 的子节点集合

显然，边界为

![\begin{cases}Son_i=\varnothing\\Dp_{i,0}=0\\Dp_{i,1}=1\end{cases}⎩⎪⎨⎪⎧Soni=∅Dpi,0=0Dpi,1=1](https://private.codecogs.com/gif.latex?\begin{cases}Son_i%3D\varnothing\\Dp_{i%2C0}%3D0\\Dp_{i%2C1}%3D1\end{cases}%u23A9%u23AA%u23A8%u23AA%u23A7%u200BSoni%u200B%3D%u2205Dpi%2C0%u200B%3D0Dpi%2C1%u200B%3D1%u200B)

如果定义 ![Son_i=\varnothing Soni=∅](https://private.codecogs.com/gif.latex?Son_i%3D\varnothing Soni%u200B%3D%u2205)时![\displaystyle \sum_{j\in Son_i}Dp_{j,1}=\sum_{j\in Son_i}min(Dp_{j,0},Dp_{j,1})=0j∈Soni∑Dpj,1=j∈Soni∑min(Dpj,0,Dpj,1)=0](https://private.codecogs.com/gif.latex?\displaystyle \sum_{j\in Son_i}Dp_{j%2C1}%3D\sum_{j\in Son_i}min(Dp_{j%2C0}%2CDp_{j%2C1})%3D0j%u2208Soni%u200B%u2211%u200BDpj%2C1%u200B%3Dj%u2208Soni%u200B%u2211%u200Bmin(Dpj%2C0%u200B%2CDpj%2C1%u200B)%3D0)

递推式及其边界便能写在一起了：

![\begin{cases}\displaystyle Dp_{i,0}=0+\sum_{j\in Son_i}Dp_{j,1}\\\ \\\displaystyle Dp_{i,1}=1+\sum_{j\in Son_i}min(Dp_{j,0},Dp_{j,1})\end{cases}⎩⎪⎪⎪⎪⎨⎪⎪⎪⎪⎧Dpi,0=0+j∈Soni∑Dpj,1 Dpi,1=1+j∈Soni∑min(Dpj,0,Dpj,1)](https://private.codecogs.com/gif.latex?\begin{cases}\displaystyle Dp_{i%2C0}%3D0&plus;\sum_{j\in Son_i}Dp_{j%2C1}\\\ \\\displaystyle Dp_{i%2C1}%3D1&plus;\sum_{j\in Son_i}min(Dp_{j%2C0}%2CDp_{j%2C1})\end{cases}%u23A9%u23AA%u23AA%u23AA%u23AA%u23A8%u23AA%u23AA%u23AA%u23AA%u23A7%u200BDpi%2C0%u200B%3D0&plus;j%u2208Soni%u200B%u2211%u200BDpj%2C1%u200B Dpi%2C1%u200B%3D1&plus;j%u2208Soni%u200B%u2211%u200Bmin(Dpj%2C0%u200B%2CDpj%2C1%u200B)%u200B)

答案看了以上大牛的解析

```cpp
#include<iostream>
#include<algorithm>
#include<queue>
#include<cstring>
using namespace std;
vector <int> edge[1510];
int dp[2][1510],n,x,y,sum[1510],z;
void solve(int u,int fa)
{
    for(int i=0;i<edge[u].size();i++)
    {
        int v=edge[u][i];
        if(v==fa)continue;
        solve(v,u);
        dp[0][u]+=dp[1][v];
        dp[1][u]+=min(dp[1][v],dp[0][v]);
    }
    dp[1][u]++;
}
int main()
{
    cin>>n;
    for(int i=1;i<=n;i++)
    {
        cin>>x>>sum[x];
        for(int i=1;i<=sum[x];i++)
        {
            cin>>z;
            edge[x].push_back(z);
            edge[z].push_back(x);
        }
    }
    solve(0,0);
    int ans=min(dp[0][0],dp[1][0]);
    cout<<ans;
    return 0;
}
```

###  2.选课（树形背包问题）

**题目描述**

在大学里每个学生，为了达到一定的学分，必须从很多课程里选择一些课程来学习，在课程里有些课程必须在某些课程之前学习，如高等数学总是在其它课程之前学习。现在有N门功课，每门课有个学分，每门课有一门或没有直接先修课（若课程a是课程b的先修课即只有学完了课程a，才能学习课程b）。一个学生要从这些课程里选择M门课程学习，问他能获得的最大学分是多少？

**输入格式**

第一行有两个整数N,M用空格隔开。(1<=N<=300,1<=M<=300)

接下来的N行,第I+1行包含两个整数ki和si, ki表示第I门课的直接先修课，si表示第I门课的学分。若ki=0表示没有直接先修课（1<=ki<=N, 1<=si<=20）。

**输出格式**

只有一行，选M门课程的最大得分。

**输入输出样例**

**输入 #1**复制

```
7  4
2  2
0  1
0  4
2  1
7  1
7  6
2  2
```

**输出 #1**复制

13

```cpp
#include<iostream>
#include<algorithm>
#include<queue>
#include<cstdio>
#include<cstring>
using namespace std;
vector<int>edge[1000];
int val[1000],dp[1000][1000],n,m;
void dfs(int u,int t)
{
    if (t<=0) return ;
    for (int i=0;i<edge[u].size(); i++)
    {
        int v = edge[u][i];
        for (int k=0; k<t; ++k) 
            dp[v][k] = dp[u][k]+val[v];
        dfs(v,t-1);
        for (int k=1; k<=t; ++k) 
            dp[u][k] = max(dp[u][k],dp[v][k-1]);
    }
}
int main()
{
    scanf("%d%d",&n,&m);
    for(int i=1,a;i<=n;i++)
    {
        scanf("%d%d",&a,&val[i]);
        edge[a].push_back(i);
    }
    dfs(0,m);
    printf("%d",dp[0][m]);
    return 0;
}
```

###  3.Game

Problem Description
 　　Nowadays, there are more and more challenge game on TV such as ‘Girls, Rush Ahead’. Now, you participate int a game like this. There are N rooms. The connection of rooms is like a tree.  In other words, you can go to any other room by one and only one way.  There is a gift prepared for you in Every room, and if you go the room,  you can get this gift. However, there is also a trap in some rooms.  After you get the gift, you may be trapped. After you go out a room, you can not go back to it any more. You can choose to start at any room  ,and when you have no room to go or have been trapped for C times, game  overs. Now you would like to know what is the maximum total value of  gifts you can get.

Input
 　　The first line contains an integer T, indicating the number of testcases.
 　　For each testcase, the first line contains one integer N(2 <= N  <= 50000), the number rooms, and another integer C(1 <= C <=  3), the number of chances to be trapped. Each of the next N lines  contains two integers, which are the value of gift in the room and  whether have trap in this rooom. Rooms are numbered from 0 to N-1. Each  of the next N-1 lines contains two integer A and B(0 <= A,B <=  N-1), representing that room A and room B is connected.
 　　All gifts’ value are bigger than 0.

Output
 　　For each testcase, output the maximum total value of gifts you can get.

Sample Input
 2 3 1 23 0 12 0 123 1 0 2 2 1 3 2 23 0 12 0 123 1 0 2 2 1

Sample Output
 146 158

Author
 SYSU

Source
 2013 Multi-University Training Contest 2

给定一棵树，每一个节点有一个权值，有的节点可能存在陷阱，经过一个顶点可以能获得权值但也经历陷阱。找出一条路径，起点自选，
 使在经历陷阱数不超过C的情况下，获得的最大的权值是多少？
 解题思路：一道很好的树形dp。如果把陷阱去掉，应该算一个经典的题目。其实加上一样：dp[u][j][k]表示到节点u经过j个陷阱，并要往根节点方向
 或要往叶子节点方向去，所获得最大权值（1表示往叶子节点，0往根节点）
 有一点值得一提：就是如果u点有陷阱，那么dp[u][1][1]不能由dp[v][0][1]转移过来，因为一旦限制为1，那么就不能由u往叶子方向走了
 这一点很难理解。我们在求最大值是会用到dp[u][0][1]的，怎么办呢？我们发现dp[u][0][1]是可以由dp[u][0][0]代替的。

```cpp
#include <algorithm>
#include <cstdio>
#include <cstring>
#include <iostream>
#define Max 1000001
#define MAXN 1001009
#define MOD 1000000007
#define rin freopen("in.txt","r",stdin)
#define rout freopen("1.out","w",stdout)
#define Del(a,b) memset(a,b,sizeof(a))
typedef long long LL;
using namespace std;
const int N = 100005;
int T;
struct Node {
    int next;
    int to;
} e[MAXN];
int tot, ans, c;
int head[N], vis[N];
int dp[N][4][3];
int trap[N];
int gift[N];
void Init(int n) {
    Del(head, -1);
    ans = 0;
    tot = 0;
    Del(dp,0);
}
void addedge(int from, int to) {
    e[tot].to = to;
    e[tot].next = head[from];
    head[from] = tot++;
}
void dfs(int u, int father) {
    dp[u][trap[u]][0]=gift[u];
    dp[u][trap[u]][1]=gift[u];
    for (int i = head[u]; i != -1; i = e[i].next) {
        int v = e[i].to;
        if (v == father)
            continue;
        dfs(v, u);
        for (int j = 0; j <= c; j++) {
            for (int k = 0; j + k <= c; k++) {
                if (j != c)
                    ans = max(ans, dp[u][j][0] + dp[v][k][1]);
                if (k != c)
                    ans = max(ans, dp[u][j][1] + dp[v][k][0]);
                if (j + k < c)
                    ans = max(ans, dp[u][j][0] + dp[v][k][0]);  //起点和终点都可以为非陷阱
                if (k + j <= c)
                    ans = max(ans, dp[u][j][1] + dp[v][k][1]);  //起点和终点都可以为陷阱
            }
        }

        for (int j = 0; j + trap[u] <= c; j++) {
            dp[u][j + trap[u]][0] = max(dp[u][j + trap[u]][0],
                    dp[v][j][0] + gift[u]);
            if (j != 0) {
                dp[u][j + trap[u]][1] = max(dp[u][j + trap[u]][1],
                        dp[v][j][1] + gift[u]);
            }
        }
    }

}
int main() {
    int n, T;
    while (cin >> T) {
        while (T--) {
            scanf("%d%d", &n, &c);
            Init(n);
            for (int i = 0; i < n; i++) {
                scanf("%d%d", &gift[i], &trap[i]);
            }
            for (int i = 1; i < n; i++) {
                int x, y;
                scanf("%d%d", &x, &y);
                addedge(y, x);
                addedge(x, y);
            }
            dfs(0, -1);
            printf("%d\n", ans);
        }
    }
    return 0;
}
```