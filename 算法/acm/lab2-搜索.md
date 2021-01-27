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

箭指offer

## E - 迷宫问题

弱智bfs....

```
<pre name="code" class="cpp">#include<iostream>
using namespace std;
 
#define QSize 50
 
int a[5][5];
//把迷宫想象成x轴，y轴
//int dx[4]={-1,1,0,0};//x轴方向上的变化
//int dy[4]={0,0,-1,1};//y轴方向上的变化
int dis[4][2]={{-1,0},{1,0},{0,-1},{0,1}};//定义4个方向
struct Node{
	int x,y,pre;
}queue[QSize];//设置一个50个格子的队列
int front=0;
int rear=0;//设置队头和队尾，头指针指向头元素,尾指针指向队尾的下一个位置
int visit[5][5];//记录是否访问过的数组
 
//广度优先遍历
void bfs(int beginX,int beginY,int endX,int endY){
	queue[0].x=beginX,queue[0].y=beginY,queue[0].pre=-1;//将初始结点[0,0]压入队列
	rear=rear+1;
	visit[beginX][beginY]=1;
	while(front<rear){//如果队列不为空
		for(int i=0;i<4;i++){//4个方向搜索可达的方向
			int newx=queue[front].x+dis[i][0];
			int newy=queue[front].y+dis[i][1];
			if(newx<0||newx>5||newy<0||newy>5||a[newx][newy]==1||visit[newx][newy]==1)//是否在迷宫内，是否撞墙，是否已走过
				continue;
                 //进队
			queue[rear].x=newx;
			queue[rear].y=newy;
			queue[rear].pre=front;
			rear++;
			visit[newx][newy]=1;//给走过的位置做标记
			if(newx==endX&&newy==endY){
				return;
			}
		}
		front++;//出队
	}
}
 
void print(Node now){
	if(now.pre==-1)
		cout<<"("<<now.x<<", "<<now.y<<")"<<endl;
	else{
		print(queue[now.pre]);
		cout<<"("<<now.x<<", "<<now.y<<")"<<endl;
	}
}
 
int main(){  
   //初始化迷宫
   for(int i=0;i<5;i++){
	   for(int j=0;j<5;j++){
		    cin>>a[i][j];
	   }	  
   }
   bfs(0,0,4,4);
   print(queue[rear-1]);
   return 0;
}
```

## F - 哈密顿绕行世界问题

一个规则的实心十二面体，它的 20个顶点标出世界著名的20个城市，你从一个城市出发经过每个城市刚好一次后回到出发的城市。

Input

前20行的第i行有3个数,表示与第i个城市相邻的3个城市.第20行以后每行有1个数m,m<=20,m>=1.m=0退出.

Output

输出从第m个城市出发经过每个城市1次又回到m的所有路线,如有多条路线,按字典序输出,每行1条路线.每行首先输出是第几条路线.然后个一个: 后列出经过的城市.参看Sample output

- 

需要注意的是，一开始在main函数里，不能将vis[target]=1

```
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
#include <istream>
#include <math.h>
#include <map>
#include <stack>
#include <vector>
using namespace std;
int  graph [21][4] ={0};
int vis[21]={0};
int target;
int task=0;
void dfs(int cur,vector<int> path)
{
    if(cur==target && path.size()==21)
    {
        cout<<++task<<":  ";
        cout<<target;
        for(int i=1;i<path.size();i++)
            cout<<" "<<path[i];
        cout<<endl;
        return;
    }
    if(path.size()>20)return;
    for(int i=0;i<3;i++)
    {
        int ch=graph[cur][i];
        //cout<<cur<<" "<<ch<<endl;
        if(!vis[ch])
        {
            vis[ch]=1;
            path.push_back(ch);
            dfs(ch,path);
            vis[ch]=0;
            path.pop_back();
        }
    }
}
int main()
{
    for(int i=1;i<=20;i++)
    {
        for(int j=0;j<3;j++)
        {
            cin>>graph[i][j];
        }
    }
    while(cin>>target&&target)
    {
        memset(vis,0,sizeof(vis));
        task=0;
        vector<int> path;
        path.push_back(target);
        dfs(target,path);
    }
}
```

## G - Find a way ()

通过在杭州一年的学习，vifenfei终于到达了家乡宁波。离开宁波一年，薇薇菲遇到了很多人。尤其是我的好朋友梅塞奇。Yifenfei的家在乡下，而mercedes的家在citv的中心。所以薇薇和梅塞奇约好在KEC见面。宁波有很多肯德基，我们想要选择一个让它的总时间最小的。现在给你一张宁波地图，vifenfei和mercedes都可以上、下、左、右移动到相邻的道路，只需11分钟。

![image-20210127151049101](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210127151049101.png)



- 方法一

对每个KFC，分别进行两次bfs，但肯定会超时

- 方法二

有没有办法进行一次bfs?当然有

从Y ,M开始，进行两次暴力bfs,记录所有能够达到的路径的长度，然后再遍历一次图，

看看这个kfs对两个人是否都能到达即可

```
#include<stdio.h>
#include<iostream>
#include<string.h>
#include<algorithm>
#include <istream>
#include <math.h>
#include <map>
#include <stack>
#include <vector>
#include <queue>
using namespace std;
int vis[201][201]={0};
char graph[201][201];
int m,n;
typedef  pair<int,int> ll ;
int directions[4][2]={1,0,0,1,-1,0,0,-1};
struct node{
    int x,y;
};
void bfs(int a,int b,int step[][201])//暴力枚举能到的地方各点的步数
{
    int i;
    queue<node> q;
    q.push(node{a,b});
    vis[a][b]=1;
    while(!q.empty())
    {
        node t=q.front();
        q.pop();
        for(i=0;i<4;i++)
        {
            int dx=t.x+directions[i][0];
            int dy=t.y+directions[i][1];
            if(dx>=0&&dy>=0&&dx<m&&dy<n&&!vis[dx][dy]&&graph[dx][dy]!='#'&&!vis[dx][dy])//基本搜索
            {
                vis[dx][dy]=1;
                step[dx][dy]=step[t.x][t.y]+1;
                q.push(node{dx,dy});
            }
        }
    }
}
int main()
{
    int step1[201][201]={0};
    int step2[201][201]={0};
    int ans=0;
    int x1,y1,x2,y2;
    char c;
    while(cin>>m>>n)
    {
        getchar();
        for(int i=0;i<m;i++)
        {
            for(int j=0;j<n;j++)
            {
                c=getchar();
                graph[i][j]=c;
                if(c=='Y')
                {
                    x1=i,y1=j;
                }
                else if(c=='M')
                {
                    x2=i,y2=j;
                }
            }
            getchar();
        }
        memset(step1,0,sizeof(step1));
        memset(step2,0,sizeof(step2));
        ans=1000000;
        memset(vis,0,sizeof(vis));
        bfs(x1,y1,step1);
        memset(vis,0,sizeof(vis));
        bfs(x2,y2,step2);
        for(int i=0;i<m;i++)
        {
            for(int j=0;j<n;j++)
            {
                if(graph[i][j]=='@')
                {
                    if(step1[i][j]&&step2[i][j])
                    {
                        ans=min(ans,step1[i][j]+step2[i][j]);
                    }
                }
            }
        }
        cout<<ans*11<<endl;
    }
}
```

## H - Dearboy's Puzzle （）

《Dearboy》是一个游戏爱好者。最近，他喜欢玩连看游戏。这个游戏是在一个有N*M个格子的棋盘上进行的，棋盘上的格子里放着许多卡片。你应该找到一对相同的卡片，如果不超过三个片段可以连接这对卡片而不传递任何其他卡片，你可以把这对从板上拿走。(你可能对不超过3段的含义感到困惑，请参考下面的图，其中我们表达了一些允许的链接)。继续上面的过程，如果你能清掉所有的牌，你就赢了游戏，否则你就输了。

![img](https://gitee.com/zisuu/picture/raw/master/img/20210127155609.jpeg)



如果你玩过这个游戏，你可能知道有时候这个游戏没有解决方案，你肯定会输。对于没有解决方案的游戏，Dearbov非常无聊，所以他让你这个著名的程序员告诉他，他是否能够在给予游戏阶段获胜。

**思路**

首先想到的肯定是dfs,对于每一个要替换的点，从这个点开始，搜索周围<=3的同类

但是，可能会出现下面的错误：

```
6 8
***AA***
*A*AC***
**B***C*
***B*D**
****D***
********
```

对于A而言，若第一个A往下搜，那么剩下的A就无法匹配

所以不能直接规定方向

而是要枚举，对一个A，先用bfs搜索其附近的A，

然后枚举删除其附近的某一个A，只要成功了，就返回

算法的本质就在于枚举

```
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#include <map>
#include <queue>
#define mem(t, v)   memset ((t) , v, sizeof(t))
#define pf          printf
using namespace std;
struct node
{
    int x,y,turn,w,d; //turn是走到当前（x，y）处已经转了多少个弯，d是（x，y）是有上一步哪个方向来的
};
int directions[4][2]={1, 0, 0, 1, 0, -1, -1, 0};
int graph[11][11],num[4];
int n,m;
char str[11];
bool flag;
bool isok(int x,int y)
{
    if (x>=0&&x<n&&y>=0&&y<m) return true;
    return false;
}
bool isOK()
{
    for (int i=0;i<n;i++)
    {
        for (int j=0;j<m;j++)
        {
            if (graph[i][j] != -1 && graph[i][j + 1] != -1 && num[graph[i][j]] == num[graph[i][j + 1]] && num[graph[i][j]] == 2)
            {
                if (graph[i][j] == graph[i + 1][j + 1] && graph[i][j + 1] == graph[i + 1][j])
                    return true;
            }
        }
    }
    return false;
}

void bfs(int x, int y, int v, int target[25][2], int &cnt)
{
    cnt=0;
    queue<node>Q;
    node st,now;
    st.x=x; st.y=y; st.w=-1; st.turn=0; st.d=-1;
    bool vis[11][11];
    memset(vis,false,sizeof(vis));
    vis[x][y]=true;
    Q.push(st);
    while (!Q.empty())
    {
        st=Q.front();Q.pop();
        if (st.w==v)
        {
            target[cnt][0]=st.x;      //记录下找到的可以相消的格子的坐标
            target[cnt++][1]=st.y;
            continue;
        }
        for (int i=0;i<4;i++)
        {
            now.x= st.x + directions[i][0];
            now.y= st.y + directions[i][1];
            if (isok(now.x,now.y)&&!vis[now.x][now.y])
            {
                if (graph[now.x][now.y] != -1 && graph[now.x][now.y] != v) continue;
                if (st.d==i||st.d==-1)
                    now.turn=st.turn;
                else
                    now.turn=st.turn+1;
                if (now.turn<=2)
                {
                    now.w=graph[now.x][now.y];
                    now.d=i;
                    vis[now.x][now.y]=true;
                    Q.push(now);
                }
            }
        }
    }
}

void dfs(int cnt)
{
    if (flag) return ;
    if (cnt==0)
    {
        flag=true;
        return;
    }
    if (isOK()) return ;        //剪枝
    for (int i=0;i<n;i++)
    {
        for (int j=0;j<m;j++)
        {
            if (graph[i][j] != -1)
            {
                int s[25][2];
                int sum;
                int v=graph[i][j];
                bfs(i,j,v,s,sum);
                num[v]-=2;
                graph[i][j]=-1;
                for (int k=0;k<sum;k++)
                {
                    int x=s[k][0];
                    int y=s[k][1];
                    graph[x][y]=-1;
                    dfs(cnt-2);
                    graph[x][y]=v;
                }
                num[v]+=2;
                graph[i][j]=v;
            }
        }
    }
}

int main()
{
    int i,j;
    while (cin>>n>>m)
    {
        if (n==0&&m==0) break;
        flag=false;
        mem(num,0);
        mem(graph, -1);
        int all=0;
        for (i=0;i<n;i++)
        {
            scanf("%s",str);
            for (j=0;j<m;j++)
            {
                if (str[j]=='*') graph[i][j]=-1;
                else {
                    num[str[j]-'A']++;
                    all++;
                    graph[i][j]=str[j]-'A';
                }
            }
        }
        if (num[0]%2||num[1]%2||num[2]%2||num[3]%2) //存在奇数时肯定不合要求
        {
            pf("no\n");
            continue;
        }
        dfs(all);
        if (flag)
            pf("yes\n");
        else
            pf("no\n");
    }
    return 0;
}
```













