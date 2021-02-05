## A - Black Box





## B - Battle City(优先队列+bfs)

我们现在讨论的是这个游戏的一个简单版本。给定一张地图，它只包含空白的空间、河流、钢墙和砖墙。你的任务是在没有敌人干扰的情况下尽快获得奖励(见下图)。

![img](https://vj.z180.cn/1b11a91353c239ffa20bebd4151d2e71?v=1612227491)

Your tank can't move through rivers or walls, but it can destroy brick walls by shooting. A brick wall will be turned into empty spaces when you hit it, however, if your shot hit a steel wall, there will be no damage to the wall. In each of your turns, you can choose to move to a neighboring (4 directions, not 8) empty space, or shoot in one of the four directions without a move. The shot will go ahead in that direction, until it go out of the map or hit a wall. If the shot hits a brick wall, the wall will disappear (i.e., in this turn). Well, given the description of a map, the positions of your tank and the target, how many turns will you take at least to arrive there?

> 简单说就是
>
> 有river/still brick  /brick/empty
>
> 问多少个步骤(移动,或者设计brick),到达target

### 第一种方法：改进过的BFS：

**有些节点需要耗费2个单位时间，要想用BFS就得改一下，由于BFS每次只能操作一步，要不就是扩展，要不就是破坏砖墙。所以只需检查该点是不是'B'，是的话就得停一步，不是的话，继续扩展，也就是说某些点的扩展慢了一拍，所以从队列里出来的点就判断一下再看执行哪个操作。
从这道题，我也对bfs有了更深的理解，“bfs之所以能最快找到最优解，就是因为它每次操作一步（这里的操作一步，很灵活，例如题目中的破坏砖墙），而while（）里面的语句就是一次操作了！”

![image-20210205203740047](https://gitee.com/zisuu/picture/raw/master/img/20210205203740.png)



### 第二种方法：优先队列＋BFS法

**也是用到了广搜的思想，只是在出队时做了处理，利用优先队列让队列中到起点的时间值最小的点先出队。该方法会用到优先队列的STL。
**首先需要了解优先队列的使用规则：
**优先队列中元素的比较规则默认是按元素的值从大到小排序的，就是说队列中最大的元素总是位于队首，所以出队时，并非按先进先出的原则进行，而是将当前队列中最大的元素出队。这点类似于给队列里的元素进行了从大到小的排序。当然，可以通过重载“<”操作符来重新定义比较规则。
重载“<”操作符的函数可以写在结构体里面，也可以写在结构体外面，写在结构体外面的时候，记得函数的参数要使用引用

```
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <vector>
#include <algorithm>
#include<queue>
using namespace std;
int n,m;
char arr[305][305]={0};
int vis[305][305]={0};
class node{
public:
    int x,y,step;
    node(int x,int y,int step){
        this->x=x;
        this->y=y;
        this->step=step;
    }
    node(){}
    bool operator < (node n1) const
    {
        return step>n1.step;
    }
};
priority_queue<node>q;
int directions[4][2]={1,0,0,1,-1,0,0,-1};
bool isValid(int x,int y)
{
    return x>=0&&x<n&&y>=0&&y<m&&!vis[x][y]&&arr[x][y]!='S'&&arr[x][y]!='R';
}
int bfs(int x,int y)
{
    node root(x,y,0);
    q.push(root);
    node temp;
    vis[x][y]=1;
    while(!q.empty())
    {
        node cur = q.top();
        q.pop();
      //  cout<<cur.x<<" "<<cur.y<<" "<<cur.step<<endl;
        if(arr[cur.x][cur.y]=='T')
            return cur.step;
        for (int i = 0; i < 4; ++i) {
            temp.x = cur.x+directions[i][0];
            temp.y = cur.y+directions[i][1];
            if(!isValid(temp.x,temp.y))continue;
            temp.step = cur.step+1;
            if(arr[temp.x][temp.y]=='E'||arr[temp.x][temp.y]=='T')
                q.push(temp);
            else if(arr[temp.x][temp.y]=='B')
            {
                temp.step++;
                q.push(temp);
            }
            vis[temp.x][temp.y]=1;
        }
    }
    return -1;
}
int main()
{
    while(cin>>n>>m&&n&&m)
    {
        memset(vis,0,sizeof(vis));
        while(!q.empty())q.pop();
        getchar();
        int x,y;
        for (int i = 0; i < n; ++i) {
            for (int j = 0; j <m ; ++j) {
                arr[i][j]=getchar();
                if(arr[i][j]=='Y')
                {
                    x=i,y=j;
                }
            }
            getchar();
        }
        cout<<bfs(x,y)<<endl;
    }
}
```

## C - Rescue

Angel was caught by the MOLIGPY! He was put in prison by Moligpy. The prison is described as a N * M (N, M <= 200) matrix. There are WALLs, ROADs, and GUARDs in the prison.

Angel's friends want to save Angel. Their task is: approach Angel. We assume that "approach Angel" is to get to the position where Angel stays. When there's a guard in the grid, we must kill him (or her?) to move into the grid. We assume that we moving up, down, right, left takes us 1 unit time, and killing a guard takes 1 unit time, too. And we are strong enough to kill all the guards.

You have to calculate the minimal time to approach Angel. (We can move only UP, DOWN, LEFT and RIGHT, to the neighbor grid within bound, of course.)



这题和上一题差不多

不同的是,有多个朋友救援,求最短的那个路径

那可以反过来,从angle开始搜,找最近的一个朋友

## D - Throw nails(优先队列数组)

一年一度的学校自行车比赛开始了。ZL是这所学校的学生。他太无聊了，因为他不会骑自行车!!所以他决定干涉比赛。他已经在之前的比赛视频中得到了选手的信息。运动员一秒可以跑F米，然后每秒钟可以跑S米。每个玩家都有一条直线跑道。ZL会每隔一秒向最远的选手的跑道扔一颗钉子。在“轰”的一声之后，这个刨将被淘汰。如果多于一个球员是1号，他总是选择ID最小的球员。

> The annual school bicycle contest started. ZL is a student in this school. He is so boring because he can't ride a bike!! So he decided to interfere with the contest. He has got the players' information by previous contest video. A player can run F meters the first second, and then can run S meters every second.
> Each player has a single straight runway. And ZL will throw a nail every second end to the farthest player's runway. After the "BOOM", this player will be eliminated. If more then one players are NO.1, he always choose the player who has the smallest ID.

Sample Input

```
2
3
100 1
100 2
3 100
5
1 1
2 2
3 3
4 1
3 4
```

Sample Output

```
Case #1:
1 3 2
Case #2:
4 5 3 2 1
```

用优先队列暴力是肯定超时的,这里我们可以注意到因为s最大只有100,所以可以用优先队列数组,数组下标即使相同的si.

```
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <vector>
#include <algorithm>
#include<queue>
using namespace std;
int n;
class node{
public:
    int id;
    int fi;
    node(int id,int fi)
    {
        this->id=id;
        this->fi=fi;
    }
    node(){}
    bool operator < (node n1) const
    {
        if(fi == n1.fi)
            return id>n1.id;
        else
            return fi < n1.fi;
    }
};
priority_queue<node>q [101];
int main()
{
    int task;
    cin>>task;
    int nums=0;
    while(task--)
    {
        cin>>n;
        cout<<"Case #"<<++nums<<":"<<endl;
        int fi,si;
        for (int i = 1; i <= n; ++i) {
            cin>>fi>>si;
            q[si].push(node(i,fi));
        }
        int maxn=0;
        int target=0;
        int targetId=0;
        for (int i = 0; i < n; ++i) {
            maxn=0;
            for (int j = 1; j <=100 ; ++j) {
                if(!q[j].empty())
                {
                    node cur = q[j].top();
                    int curMiles=j*i+cur.fi;
                    if(curMiles>maxn||curMiles==maxn&&cur.id<targetId)
                    {
                        maxn=curMiles;
                        target=j;
                        targetId=cur.id;
                    }
                }
            }
            q[target].pop();
            if(i!=0)
                cout<<" "<<targetId;
            else
                cout<<targetId;
        }
        cout<<endl;
    }
}
```

































