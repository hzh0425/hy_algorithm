## A - River Hopscotch

每年牛们都会举行一场特殊的“跳房子”活动，包括小心翼翼地从一块岩石跳到另一块岩石上。令人兴奋的事情发生在一条又长又直的河流上，起点是一块石头，终点是另一块石头，距离起点有1个单位的距离(1ls 1000,000,000)。在河流的起点和终点岩石之间，出现了N (O <Ns 50,000)多块岩石，每个岩石的积分距离为D;从开始(0 < Di <L)为了玩这个游戏，每头牛轮流从起始岩石开始，并试图到达结束岩石，只从一块岩石跳到另一块岩石。当然，不那么敏捷的牛永远也爬不到最后一块岩石前，而是被扔进河里。农民约翰为他的牛感到骄傲，并每年都观看这一活动。但随着时间的流逝，他厌倦了看着其他农民胆小的奶牛在石头靠得太近的岩石之间的短距离上一瘸一拐地走。他计划移走几块石头，以便增加一头牛跳到终点的最短距离。他知道他不能移走开始和结束的石头，但他计算出他有足够的资源移走最多M块石头(o sM sN)。F想要知道在开始移动石头之前，他能增加多少最短距离。帮助农夫约翰确定在移除M块最佳岩石后，一头牛必须跳跃的最大可能的最短距离。

```c++
int l,n,m;
int dist[50005]={0};
int isOk(int curLen)
{
    int ans=0;
    int last=0;
    for(int i=1;i<=n+1;i++)
    {
        if(dist[i]-dist[last]<curLen)
        {
            ans++;
            if(ans>m)return false;
        }
        else{
            last=i;
        }
    }
    return true;
}
int main()
{
    cin>>l>>n>>m;
    for (int i = 1; i <=n ; ++i) {
        cin>>dist[i];
    }
    dist[0]=0;
    dist[n+1]=l;
    sort(dist+1,dist+n+1);
    int i=1,j=l+1;
    int ans=0;
    while(i<j)
    {
        int mid= i+(j-i+1)/2;
        if(isOk(mid))
        {
            i=mid;
        }
        else j=mid-1;
    }
    cout<<i;
}
```

当然，二分查找还可以这样做：

```
    int i=0,j=l;
    int ans=0;
    while(i<=j)
    {
        int mid= i+(j-i)/2;
        if(isOk(mid))
        {
            i=mid+1;
            ans=mid;
        }
        else j=mid-1;
    }
    cout<<ans;
```

## B - Pie

我的生日快到了，传统上我吃派。不仅仅是一个派，不，我有N个，不同口味，不同大小。F我的朋友要来参加我的聚会，每人得到一块派。这应该是一个派的一小块，而不是几个小块，因为看起来很乱。这一块可以是一整块。我的朋友很烦人，如果其中一个得到的比其他的大，他们就开始抱怨。因此，所有的馅饼都应该是同样大小的(但不一定是同样形状的)，即使这会导致一些馅饼被破坏(这比破坏聚会要好)。当然，我自己也想要一块派，那一块也应该是一样的大小我们所有人能得到的最大件尺寸是多少?所有的饼都是圆柱形的，它们都有相同的高度1，但是饼的半径可以不同。

这种精度问题，就不要考虑+1,-1了，直接=mid就好

另外，要记得给自己也分一份

```
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#include <map>
#include <queue>
#include <cmath>

#define mem(t, v)   memset ((t) , v, sizeof(t))
#define pf          printf
using namespace std;
int l,n,m;
double radii[50005]={0.0};
const double pai = acos(-1.0);
int isOk(double cur)
{
    int ans=0;
    for(int i=0;i<n;i++)
    {
        ans+=(int)(radii[i]/cur);
    }
    return ans>=m;
}
int main()
{
    int task;
    cin>>task;
    while(task--)
    {
        cin>>n>>m;
        double sumn=0;
        for(int i=0;i<n;i++)
        {
            cin>>radii[i];
            radii[i]=pai*radii[i]*radii[i];
            sumn+=radii[i];
        }
        m++;
        double left=0,right=sumn/m;
        double ans=0;
        while(right-left>=1e-6)
        {
            double mid=left+(right-left)/2;
            if(isOk(mid))
            {
                left=mid;
                ans=mid;
            }else{
                right=mid;
            }
        }
        printf("%.4f\n",ans);
    }
}
```

## C - Yogurt factory

> The cows have purchased a yogurt factory that makes world-famous Yucky Yogurt. Over the next N (1 <= N <= 10,000) weeks, the price of milk and labor will fluctuate weekly such that it will cost the company C_i (1 <= C_i <= 5,000) cents to produce one unit of yogurt in week i. Yucky's factory, being well-designed, can produce arbitrarily many units of yogurt each week.
>
> Yucky Yogurt owns a warehouse that can store unused yogurt at a constant fee of S (1 <= S <= 100) cents per unit of yogurt per week. Fortuitously, yogurt does not spoil. Yucky Yogurt's warehouse is enormous, so it can hold arbitrarily many units of yogurt.
>
> Yucky wants to find a way to make weekly deliveries of Y_i (0 <= Y_i <= 10,000) units of yogurt to its clientele (Y_i is the delivery quantity in week i). Help Yucky minimize its costs over the entire N-week period. Yogurt produced in week i, as well as any yogurt already in storage, can be used to meet Yucky's demand for that week.



> Input
>
> \* Line 1: Two space-separated integers, N and S.
>
> \* Lines 2..N+1: Line i+1 contains two space-separated integers: C_i and Y_i.
>
> Output
>
> \* Line 1: Line 1 contains a single integer: the minimum total cost to satisfy the yogurt schedule. Note that the total might be too large for a 32-bit integer.
>
> Sample Input
>
> ```
> 4 5
> 88 200
> 89 400
> 97 300
> 91 500
> 
> 
> 126900
> ```

**大概意思**

每一行：

这周产一瓶奶的价格ci 至少需要送多少瓶si

多产的可以存放在仓库，每一瓶要付费s元

问最少需要的经费

对于样例的解释：

> OUTPUT DETAILS:
> In week 1, produce 200 units of yogurt and deliver all of it. In week 2, produce 700 units: deliver 400 units while storing 300 units. In week 3, deliver the 300 units that were stored. In week 4, produce and deliver 500 units.

不就动态规划嘛，当前的奶要么当天生产，要么前一天生产

但是因为不具备连续性，所以就，用贪心做法

当天的价格=min(当天价格，前一天价格)

```c++
#include <iostream>
#include <cstdio>
#include <cstring>
#include <algorithm>
#include <string>
#include <map>
#include <queue>
#include <cmath>
#define mem(t, v)   memset ((t) , v, sizeof(t))
#define pf          printf
struct node{
    int price;
    int nums;
}arr[10005];
using namespace std;
int main()
{
    int n,m;
    cin>>n>>m;
    long long int sum=0;
    for(int i=0;i<n;i++)
    {
        cin>>arr[i].price>>arr[i].nums;
    }
    for (int i= 0; i <n ; ++i) {
        sum+=arr[i].price*arr[i].nums;
        if(i!=n-1)
        {
            arr[i+1].price=min(arr[i+1].price,arr[i].price+m);
        }
    }
    cout<<sum;
}
```







