## A - Lake Counting

由于最近的降雨，水在农民约翰的田地的不同地方聚集，用NxM (1<=N<= 100;1 <=M <= 100)正方形。每个正方形包含水(W')或旱地(')。农民约翰想算出他的田地里已经形成了多少个池塘。池塘是一组相连的有水的广场，一个广场被认为是相邻的所有8个邻居。给农夫罗恩的田地一张图，确定他有多少池塘。

普通的连通集dfs:

```c++
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
#include <istream>
using namespace std;
int n;
int graph[10][10]={0};
int k=0;
int cnt=0;
//int directions[8][2]={1,0,0,1,-1,0,0,-1,1,1,-1,1,-1,-1,1,-1};
void dfs(int cur,int target,int pos[])
{
    if(target==0){
        cnt++;
        return;
    }
    if(cur>=n)return;
    for(int i=0;i<n;i++)
    {
        if(graph[cur][i])
        {
            bool flag=true;
            for(int j=0;j<cur;j++)
            {
                if(pos[j]==i){
                    flag=false;break;
                }
            }
            if(flag)
            {
                pos[cur]=i;
                dfs(cur+1,target-1,pos);
                pos[cur]=-1;
            }
        }
    }
    dfs(cur+1,target,pos);
}
int main()
{
    int pos[10]={0};
    while(cin>>n>>k)
    {
        if(n==-1)break;
        memset(pos,0,sizeof(pos));
        memset(graph,0,sizeof(graph));
        cnt=0;
        getchar();
        char c;
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<n;j++)
            {
                c=getchar();
                if(c=='#')graph[i][j]=1;
                else graph[i][j]=0;
//            cout<<graph[i][j]<<" ";
            }
            //cout<<endl;
            getchar();
        }
        dfs(0,k,pos);
        printf("%d\n",cnt);
    }
}
```

## B - Prime Ring Problem

如图所示，一个环由n个圆组成。将自然数1、2、n分别放入每个圆中，相邻两个圆的数字之和应该是一个素数注意:第一个圆的个数应始终为1。

![image-20210126170116319](https://gitee.com/zisuu/picture/raw/master/img/20210126170116.png)

**output**

```
输出格式如下所示。每一行代表从1开始的一组圆圈数字，顺时针方向和逆时针方向。编号的顺序必须满足上述要求。按字典顺序打印解决方案。你要写一个程序来完成上述过程。在每个case后面打印一个空行。
```

```c++
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
#include <istream>
#include <math.h>
using namespace std;
int n;
int vis[21]={0};
int isSu(int num)
{
    for(int i=2;i<=sqrt(num);i++)
    {
        if(num%i==0)return false;
    }
    return true;
}
void dfs(int cur,int nums[])
{
    if(cur==n)
    {
        int l=nums[cur-1]+nums[0];
        if(isSu(l))
        {
            cout<<nums[0];
            for(int i=1;i<cur;i++)
                cout<<" "<<nums[i];
            cout<<endl;
        }
    }
    if(cur>=n)
        return;
    for(int i=2;i<=n;i++)
    {
        if(!vis[i])
        {
                int l=nums[cur-1]+i;
                if(isSu(l))
                {
                    vis[i]=1;
                    nums[cur]=i;
                    dfs(cur+1,nums);
                    vis[i]=0;
                    nums[cur]=-1;
                }
        }
    }
}
int main()
{
    int k=1;
    int nums[21];
    while(cin>>n)
    {
        printf("Case %d:\n",k++);
        memset(nums,-1,sizeof(nums));
        memset(vis,0,sizeof(vis));
        nums[0]=1;
        dfs(1,nums);
        cout<<endl;
    }
}
```



## C - 棋盘问题

普通dfs

![image-20210126165957114](https://gitee.com/zisuu/picture/raw/master/img/20210126170012.png)

```c++
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
#include <istream>
using namespace std;
int n;
int graph[10][10]={0};
int k=0;
int cnt=0;
//int directions[8][2]={1,0,0,1,-1,0,0,-1,1,1,-1,1,-1,-1,1,-1};
void dfs(int cur,int target,int pos[])
{
    if(target==0){
        cnt++;
        return;
    }
    if(cur>=n)return;
    for(int i=0;i<n;i++)
    {
        if(graph[cur][i])
        {
            bool flag=true;
            for(int j=0;j<cur;j++)
            {
                if(pos[j]==i){
                    flag=false;break;
                }
            }
            if(flag)
            {
                pos[cur]=i;
                dfs(cur+1,target-1,pos);
                pos[cur]=-1;
            }
        }
    }
    dfs(cur+1,target,pos);
}
int main()
{
    int pos[10]={0};
    while(cin>>n>>k)
    {
        if(n==-1)break;
        memset(pos,0,sizeof(pos));
        memset(graph,0,sizeof(graph));
        cnt=0;
        getchar();
        char c;
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<n;j++)
            {
                c=getchar();
                if(c=='#')graph[i][j]=1;
                else graph[i][j]=0;
//            cout<<graph[i][j]<<" ";
            }
            //cout<<endl;
            getchar();
        }
        dfs(0,k,pos);
        printf("%d\n",cnt);
    }
}
```

