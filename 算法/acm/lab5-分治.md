## A - Tree Recovery

给出前序遍历和中序遍历,恢复后序遍历

此类题目总结

- 找到root
- build left
- build right
- arr[cnt++]=root
- 需要变换后三步顺序(根据题目给的而定)

```
#include <iostream>
#include <math.h>
#include <algorithm>
#include <cstring>

using namespace std;
typedef long long int ll;
char pre[100];
char in[100];
char arr [100];
int cnt=0;
void build(int lb,int le,int rb,int re)
{
    //边界
    if(lb>le)
    {
        return;
    }
    int root=rb;
    while(pre[lb]!=in[root])root++;
    int size = root-rb;
    build(lb+1,lb+size,rb,root-1);
    build(lb+size+1,le,root+1,re);
    arr[cnt++]=in[root];
}
int main()
{
    while(~scanf("%s %s",pre,in))
    {
        cnt=0;
        int size=strlen(pre)-1;
        build(0,size,0,size);
        for(int i=0;i<=size;i++)
            cout<<arr[i];
        cout<<endl;
    }
}
```

## B - Bridge

人们希望在夜里过桥。一次最多两个人通过，每组必须有手电筒。在n个人中只有一个手电筒可用，所以必须安排某种穿梭，以便归还手电筒，以便更多的人可以通过。每个人都有不同的穿越速度;一组的速度是由较慢的成员的速度决定的。你的任务是确定一个策略，让所有n个人在最短时间内通过这座桥。

Input

The first line of input contains n, followed by n lines giving the crossing times for each of the people. There are not more than 1000 people and nobody takes more than 100 seconds to cross the bridge.

Output

The first line of output must contain the total number of seconds required for all n people to cross the bridge. The following lines give a strategy for achieving this time. Each line contains either one or two integers, indicating which person or people form the next group to cross. (Each person is indicated by the crossing time specified in the input. Although many people may have the same crossing time the ambiguity is of no consequence.) Note that the crossings alternate directions, as it is necessary to return the flashlight so that more may cross. If more than one strategy yields the minimal time, any one will do.

Sample Input

```
4
1
2
5
10
```

Sample Output

```
17
1 2
1
5 10
2
1 2
```







## C - BD String

![image-20210203150620089](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210203150620089.png)

解题思路:从字符串规则可以看出以下几点

①S(n)的串长度为![img](https://img-blog.csdn.net/20160521221159681);

②S(n)中B的个数为![img](https://img-blog.csdn.net/20160521221434215)个(以正中间的B为中心,假设左边有x个B,那么右边有![img](https://img-blog.csdn.net/20160521221650403)个,即左边B的个数+右边B的个数=S(n-1)的串长度)

③![img](https://img-blog.csdn.net/20160521220446382)远大于![img](https://img-blog.csdn.net/20160521220632398),故![img](https://img-blog.csdn.net/20160521220446382)只是吓唬我们的

由上述几点可知,此题并没有表面上那么吓人

要求区间[l,r]内B的个数,我们可以转化为求[1,r]-[1,l-1]

而求区间[1,x]内B的个数,又可以如上述第②条所说,拆成3段,递归求解

比如x=12时,S=BBDBBDDBBBDD……

拆成3段如下:

![img](https://img-blog.csdn.net/20160521222811173)

右边4个是多余的,但是因为右边是由reverse+flip得到的,求右边4个中B的个数可以转化成求4-([1,7]中B的个数-[1,3]中B的个数)

而[1,3]中的个数又是得递归求解的

对②点补充一下： s(n)中B的个数为S(n-1)的长度+1

前面的我都想到了，只是一直犹豫到不知道怎么求[1,x]中B的个数，递归确实很好，最多就是64层，可能题目数据比较水，都是0MS过。这题巧妙递归想法值得体会

解题思路:从字符串规则可以看出以下几点

①S(n)的串长度为![img](https://img-blog.csdn.net/20160521221159681);

②S(n)中B的个数为![img](https://img-blog.csdn.net/20160521221434215)个(以正中间的B为中心,假设左边有x个B,那么右边有![img](https://img-blog.csdn.net/20160521221650403)个,即左边B的个数+右边B的个数=S(n-1)的串长度)

③![img](https://img-blog.csdn.net/20160521220446382)远大于![img](https://img-blog.csdn.net/20160521220632398),故![img](https://img-blog.csdn.net/20160521220446382)只是吓唬我们的

由上述几点可知,此题并没有表面上那么吓人

要求区间[l,r]内B的个数,我们可以转化为求[1,r]-[1,l-1]

而求区间[1,x]内B的个数,又可以如上述第②条所说,拆成3段,递归求解

比如x=12时,S=BBDBBDDBBBDD……

拆成3段如下:

![img](https://img-blog.csdn.net/20160521222811173)

右边4个是多余的,但是因为右边是由reverse+flip得到的,求右边4个中B的个数可以转化成求4-([1,7]中B的个数-[1,3]中B的个数)

而[1,3]中的个数又是得递归求解的

对②点补充一下： s(n)中B的个数为S(n-1)的长度+1

前面的我都想到了，只是一直犹豫到不知道怎么求[1,x]中B的个数，递归确实很好，最多就是64层，可能题目数据比较水，都是0MS过。这题巧妙递归想法值得体会

```
/* ***********************************************
Author        :angon
************************************************ */
#include <stdio.h>
#include <string.h>
#include <iostream>
#include <algorithm>
#include <stack>
#include <vector>
#include <queue>
#include <set>
#include <map>
#include <string>
#include <math.h>
#include <stdlib.h>
#include <time.h>
using namespace std;
#define REP(i,k,n) for(int i=k;i<n;i++)
#define REPP(i,k,n) for(int i=k;i<=n;i++)
#define scan(d) scanf("%d",&d)
#define scann(n,m) scanf("%d%d",&n,&m)
#define mst(a,k)  memset(a,k,sizeof(a));
#define LL long long
#define maxn 1005
#define mod 100000007
 
 
 
LL dfs(LL x)
{
    if(x==0) return 0;
    LL m=0,ans,res;
    while(m<x) m=m*2+1;  // m为字符串（设为s1）的长度，该s1长度超过了x
    m=(m-1)/2;          //回到上一个字符串（设为s0），因为长度不能超过x才能精确求到x
    ans=(m+1)/2;        //s1的第一部分的B为 (s0的长度+1）/2
    res=x-m-1;          //已经求得第一部分的B，res为剩余部分的长度
    return ans + 1 + res -(ans - dfs(m-res));
}
 
 
int main()
{
    //freopen("in.txt","r",stdin);
    //freopen("out.txt","w",stdout);
    int t;
    scan(t);
    while(t--)
    {
        LL l,r;
        scanf("%I64d%I64d",&l,&r);
        printf("%I64d\n",dfs(r)-dfs(l-1));
    }
    return 0;
}
```

## D - 吉哥系列故事(LCIS)

　　吉哥这几天对队形比较感兴趣。
　　有一天，有n个人按顺序站在他的面前，他们的身高分别是h[1], h[2] ... h[n]，吉哥希望从中挑出一些人，让这些人形成一个新的队形，新的队形若满足以下三点要求，则称之为完美队形：
　　
　　1、挑出的人保持他们在原队形的相对顺序不变；
　　2、左右对称，假设有m个人形成新的队形，则第1个人和第m个人身高相同，第2个人和第m-1个人身高相同，依此类推，当然，如果m是奇数，中间那个人可以任意；
　　3、从左到中间那个人，身高需保证递增，如果用H表示新队形的高度，则H[1] < H[2] < H[3] .... < H[mid]。

　　现在吉哥想知道：最多能选出多少人组成完美队形？



最长公共上升子序列,需要判断一下是否重复

```
#include <stdio.h>
#include <string.h>
#include <iostream>
#include <algorithm>
#include <stack>
#include <vector>
#include <queue>
#include <set>
#include <map>
#include <string>
#include <math.h>
#include <stdlib.h>
#include <time.h>
using namespace std;
#define REP(i,k,n) for(int i=k;i<n;i++)
#define REPP(i,k,n) for(int i=k;i<=n;i++)
#define scan(d) scanf("%d",&d)
#define scann(n,m) scanf("%d%d",&n,&m)
#define mst(a,k)  memset(a,k,sizeof(a));
#define ll long long
#define maxn 1005
#define mod 100000007
int a[205]={0};
int b[205]={0};
int dp[205]={0};
int main()
{
    int t;
    scan(t);
    while(t--)
    {
        mst(dp,0);
        mst(a, 0);
        int n;
        cin>>n;
        REPP(i,1,n)
        {
            cin >> a[i];
            b[n - i + 1]=a[i];
        }
        int ans=1;
        for (int i=1;i<=n;i++)
        {
            int maxLen=0;
            for (int j = 1; j <=(n-i+1) ; ++j) {
                if(a[i] > b[j])
                    maxLen=max(maxLen,dp[j]);
                else if(a[i] == b[j])
                    dp[j]=max(dp[j],maxLen+1);
                if(j!=n-i+1)
                    ans=max(ans,dp[j]*2);
                else
                    ans=max(ans,dp[j]*2-1);
            }
        }
        cout<<ans<<endl;
    }
    return 0;
}

```

## E - Tree (树形dp+点分治)

Give a tree with n vertices,each edge has a length(positive integer less than 1001).
Define dist(u,v)=The min distance between node u and v.
Give an integer k,for every pair (u,v) of vertices is called valid if and only if dist(u,v) not exceed k.
Write a program that will count how many pairs which are valid for a given tree.

**大意**

计算Pair不超过k的数

Pair表示定点u,v之间的最短距离

### 1.树的重心

代码定义：[树](https://baike.baidu.com/item/树/2699484)的重心也叫[树](https://baike.baidu.com/item/树/2699484)的质心。对于一棵树n个节点的无根树，找到一个点，使得把树变成以该点为根的有根树时，最大[子树](https://baike.baidu.com/item/子树)的结点数最小。换句话说，删除这个 [1] 点后最大连通块（一定是树）的结点数最小。

性质：

1. 树中所有点到某个点的距离和中，到重心的距离和是最小的，如果有两个距离和，他们的距离和一样。
2. 把两棵树通过一条边相连，新的树的重心在原来两棵树重心的连线上。
3. 一棵树添加或者删除一个节点，树的重心最多只移动一条边的位置。
4. 一棵树最多有两个重心，且相邻。

算法分析：

和树的最大独立问题类似，先任选一个结点作为根节点，把无根树变成有根树，然后设d(i)表示以i为根的子树的结点的个数。不难发现d(i)=∑d(j)+1，j∈s（i）。s（i）为i结点的所有儿子结点的编号的集合。程序也十分简单：只需要DFS一次，在无根树有根数的同时计算即可，连记忆化都不需要——因为本来就没有重复计算。

那么，删除结点i后，最大的连通块有多少个呢？结点i的子树中最大有max{d（j）}个结点，i的“上方子树”中有n-d（i）个结点

![img](https://img2018.cnblogs.com/blog/1396791/201907/1396791-20190709103002538-558102446.png)

代码：

```
#include<iostream>
#include<vector>
#include<cstdio>
#include<cstring>
using namespace std;
typedef long long ll;
const int maxn=200005;
vector<int> tree[maxn];
int n,minNode,minBalance;
//minNode当前重心节点
//minBalance当前重心节点的最大子树节点个数 
int d[maxn];
//d[i]表示以i为根的子树节点个数 
void dfs(int u,int fa){
    d[u]=1; //节点本身 
    int maxSub=0,size=tree[u].size(); //maxSub为节点u的最大子树节点个数 
    for(int i=0;i<size;i++){
        int v=tree[u][i];
        if(v!=fa){
            dfs(v,u);
            d[u]+=d[v];
            maxSub=max(maxSub,d[v]);
        }
    }
    maxSub=max(maxSub,n-d[u]);
    if(maxSub<minBalance){
        minNode=u;
        minBalance=maxSub;
    }
}
int main(){
    int t;
    cin>>t;
    while(t--){
        cin>>n;
        for(int i=1;i<=n;i++) tree[i].clear();
        memset(d,0,sizeof(d));
        for(int i=1;i<n;i++){
            int u,v;
            cin>>u>>v;
            tree[u].push_back(v);
            tree[v].push_back(u);
        }
        minNode=0; minBalance=0x3f3f3f3f;
        dfs(1,0);
        printf("%d %d\n",minNode,minBalance);
    }
    return 0;
} 
```

acm分治专题前四题题解,l

### 2.点分治

```
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <vector>
#include <algorithm>
using namespace std;
const int N = 1e5+20, M = 1e2+10, mod = 1e9+7, inf = 1e9+1000;
typedef long long ll;

int n,m,root,t,ans,allnode,siz[N],K,head[N],vis[N],d[N];
int deep[N];
int f[N];

struct edg{int to,next,v;}e[N * 4];
void ADD_EDGE(int u, int v, int w) { e[t].to=v;e[t].next=head[u];e[t].v=w;head[u]=t++;}


void getroot(int x,int fa) {
    siz[x] = 1;
    f[x] = 0;
    for(int i=head[x];i;i=e[i].next) {
        int to = e[i].to;
        if(to == fa || vis[to]) continue;
        getroot(to,x);
        siz[x] += siz[to];
        f[x] = max(f[x] , siz[to]);
    }
    f[x] = max(allnode-siz[x] , f[x]);
    if(f[x] < f[root]) root = x;
}

void cal_dist(int x, int fa) {//获取子树所有节点与根的距离
    deep[++deep[0]] = d[x];
    for(int i=head[x];i;i=e[i].next) {
        int to = e[i].to;
        if(to == fa || vis[to]) continue;
        d[to] = d[x] + e[i].v;
        cal_dist(to, x);
    }
}
int cal(int x,int now) {//计算当前以重心x的子树下，所有情况的答案
    d[x]=now;deep[0]=0;
    cal_dist(x, 0);
    int size=deep[0];
    sort(deep+1,deep+size+1);
    int all = 0;
    for(int l=1,r=size;l<r;) {
        if(deep[l]+deep[r] <= K) {all += r-l;l++;}
        else r--;
    }
    return all;
}

void calculate(int x) {//以x为重心进行计算
    vis[x] = 1;
    ans+=cal(x,0);
    for(int i=head[x];i;i=e[i].next) {
        int to = e[i].to;
        if(vis[to]) continue;
        ans -= cal(to,e[i].v);
        allnode = siz[to];
        root=0;
        getroot(to,x);
        calculate(root);
    }
}

int main()
{
    while(~scanf("%d%d",&n,&K)) {
        if(!n&&!m) break;
        memset(head,0,sizeof(head));
        memset(vis,0,sizeof(vis));
        t = 1;
        for(int i=1;i<n;i++) {
            int a,b,c;
            scanf("%d%d%d",&a,&b,&c);
            ADD_EDGE(a, b, c) , ADD_EDGE(b, a, c);
        }
        root=ans=0;
        allnode=n;f[0]=inf;
        getroot(1,0);
        calculate(root);
        printf("%d\n",ans);
    }
}
```



























