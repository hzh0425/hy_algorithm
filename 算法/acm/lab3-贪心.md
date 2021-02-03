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

## D - Game Prediction

假设有M个人，包括你，在玩一种特殊的纸牌游戏。一开始，每个玩家收到N张卡片。一张牌的pip是一个最大N*M的正整数。没有两张相同的牌。在一轮游戏中，每个玩家选择一张纸牌与其他纸牌进行比较。牌尖最大的玩家赢得这一轮，然后下一轮开始。N轮之后，当每个牌手的牌都被选中时，赢得最多回合的牌手就是这场游戏的赢家给定一开始收到的vour卡片，编写一个程序告诉你在整个游戏中至少可以赢得的最大回合数

Sample Input

```
2 5
1 7 2 10 9

6 11
62 63 54 66 65 61 57 56 50 53 48

0 0
```

Sample Output

```
Case 1: 2
Case 2: 4
```

**基本思路：**

从题目就可以看出来，至少能赢多少局，从贪心的角度，每个人肯定选最大的出

结果无非两种，我赢，敌人赢

所以可以分成我和敌人，而不用管有多少个人

对于当前这局我能不能必赢，取决于之前还有没有比我大的数

如果有，那这局就抵消掉了，没有，就说明我可以赢

```
using namespace std;
int card[1200]={0};
int main()
{
    int n,m;
    int task=0;
    while(cin>>n>>m&&n&&m)
    {
        int j=0;
        memset(card,0,sizeof(card));
        for(int i=0;i<m;i++)
        {
            cin>>j;
            card[j]++;
        }
        int ans=0,compareMax=0;
        for(int i=n*m;i>0;i--)
        {
            if(!card[i])compareMax++;
            else{
                if(!compareMax)ans++;
                else compareMax--;
            }
        }
        printf("Case %d: %d\n",++task,ans);
    }
}
```

## E - Radar Installation（区间交集问题）

假设海岸是一条无限的直线。海岸的一边是陆地，另一边是海洋。每个小岛都是海边的一个点。而位于海岸边的anv雷达装置只能覆盖d个距离，因此半径装置可以覆盖海面上的一个岛屿，两者之间的距离最多为d。我们使用笛卡尔坐标系统，用x轴来定义滑行。海这边在x轴上方。以及下面的陆地面。已知海面上每个岛屿的位置，以及雷达装置覆盖的距离，vour的任务是编写一个程序，找出覆盖所有岛屿的最小数量的雷达装置。注意，岛屿的位置是由它的x-y坐标表示的

![img](https://gitee.com/zisuu/picture/raw/master/img/20210129150535.jpeg)



```
3 2
1 2
-3 1
2 1

2
```

这是一道典型的区间调度问题，但是要先对题目进行转化

对于当前的点p,想要覆盖它，有一个范围：
【p-x,p+x】[x=d^2-y^2]

所以就转化成，有多少个相交的区间了。。

求相交区间有两种方法：

- 对end排序，比较简单

```
#include<algorithm>
#include<iostream>
#include<cstdio>
#include<cmath>
using namespace std;
struct Node{
    double s,e;
};
int n,d,k;
Node points[2000];
int cmp(Node a,Node b){
    return a.e<b.e;
}
int findMinMax(int x, int y){
    if(y>d)return 0;
    double m=sqrt(float(d*d-y*y));
    points[k].s=x-m;points[k].e=x+m;k++;
    return 1;
}
int main(){int t,x,y,flag,temp,num,task=0;
    while(scanf("%d%d",&n,&d),n||d){
        k=0;flag=1;temp=0;num=1;task++;
        for(int i=0;i<n;i++){
            scanf("%d%d",&x,&y);
            t= findMinMax(x, y);
            if(!t)flag=0;
        }
        if(!flag)
        {
            printf("Case %d: %d\n",task,-1);
            continue;
        }
        sort(points, points + k, cmp);
        for(int i=0;i<k;i++){
            if(points[i].s > points[temp].e)temp=i,num++;
        }
        printf("Case %d: %d\n",task,num);
    }
    return 0;
}
```



- 对start排序，要分情况讨论

![这里写图片描述](https://img-blog.csdn.net/20180811195826559?watermark/2/text/aHR0cHM6Ly9ibG9nLmNzZG4ubmV0L3FxXzM3NzA4NzAy/font/5a6L5L2T/fontsize/400/fill/I0JBQkFCMA==/dissolve/70)

```
       for(int i = 1;i < n;i++){
            if(radar[i].start >= l && radar[i].end <= r){//对应情况1
                l = radar[i].start;
                r = radar[i].end;
            }
            else if(radar[i].start <= r && radar[i].end >= r)//对应情况2
                l = radar[i].start;
            else if(radar[i].start > r){//对应情况3
                l = radar[i].start;
                r = radar[i].end;
                num++;
            }
        }
```

## F - Communication System(枚举)

我们已收到来自Pizoor通信公司的一份特殊通信系统的订单。该系统由多个设备组成。对于每个设备，我们可以从几个制造商中自由选择。来自两个制造商的相同设备在最大带宽和价格上有所不同。总带宽(B)是指通信系统中所选设备带宽的最小值，而总价格(P)是所有所选设备价格的总和。我们的目标是为每个设备选择一个制造商，以最大化B/P。

输入文件的第一行包含一个单一的整数t (1 st s 10)，测试用例的数量，后面是每个测试用例的输入数据。每个测试用例开始一行包含一个整数n (1 sn 100)通信系统中设备的数量,其次是n行以下格式:i线(1 si sn)始于mi (1 s mi s 100),制造商的第i个设备的数量,其次是mi对正整数在同一行,每个指示设备的带宽和价格分别对应于一个制造商。跳跃您的程序应该为每个测试用例生成一行，其中包含一个单独的数字，这是测试用例的最大可能B/P。将输出的数字四舍五入到小数点后的3位。



> 枚举，将带宽的范围限制在所有带宽的最小和最大值之间。关键在于降低复杂度。由于B/P中间的B，P两个都是变量，首先固定住B，然后对于每个设备中寻找大于B时的最小价格，然后把所有设备的价格加起来，求最大值。
>

```
# include<stdio.h>
# include<algorithm>
using namespace std;
 
int a[200][200],b[200][200];
int num[200];
 
int main()
{
	int t,i,j;
	while(scanf("%d",&t)!=EOF)
	{
		while(t--)
		{
			int n;
			scanf("%d",&n);
			int maxv=0,minv=123123123;
 
			for(i=0;i<n;i++)
			{
				scanf("%d",&num[i]);
				for(j=0;j<num[i];j++)
				{
					scanf("%d%d",&a[i][j],&b[i][j]);   
					if(a[i][j]>maxv)
						maxv=a[i][j];
					if(a[i][j]<minv)
						minv=a[i][j];
				}
			}
			
			double ans=0.0;
			for(int B=minv;B<=maxv;B++)
			{
				int sum=0;
				for(i=0;i<n;i++)
				{
				  int minCost=123123123;
				  for(j=0;j<num[i];j++)
				  {
				    if(a[i][j]>=B&&b[i][j]<minCost)
						minCost=b[i][j];
				  }
				   sum+=minCost;
				}
                
				if(1.0*B/sum>ans)
					ans=1.0*B/sum;
			}
 
			printf("%.3lf\n",ans);
			
		}	
	}
	return 0;
}
```

## H - Painter

![image-20210131151515746](https://gitee.com/zisuu/picture/raw/master/img/20210131151515.png)

思路：贪心。需要的灰色颜料用需要量最小的颜料合成，所以每次用最少的三种颜色合成1ml灰色。因为n很小，所以每合成1ml快排一次。

```
using namespace std;
int nums[15]={0};
int n;
int main(){
    while(cin>>n&&n)
    {
        for (int i = 0; i <= n ; ++i) {
            cin>>nums[i];
        }
        int gray = nums[n];
        while(gray>0)
        {
            sort(nums,nums+n);
            for(int i=0;i<3;i++)nums[i]++;
            gray--;
        }
        sort(nums,nums+n);
        int maxn=nums[n-1];
        cout<<maxn/50+(maxn%50>0?1:0)<<endl;
    }
}
```

## I - Crossing River

N个人想要过河,每个人过河的速度有快又慢,每次最多两个人过河,取决于慢的那个人的速度,问:

最少需要多少时间

- 贪心思想:每次想办法,借助最快的那两个人,将最慢的那两个人送过河

```
将过河时间由小到大排序后，取a0,a1,ai-1,ai，这


四个人组成一组。有两种方案 1.a0先送ai过河然后回来

，再送ai-1，再回来。2. a0和a1过河，a0回去，然后

ai，ai-1过河，a1将船开回去。选择用时少的，

然后重复这个过程
```

```
#include <iostream>
#include <math.h>
#include <algorithm>
#include <cstring>
using namespace std;
int speed[1010]={0};
int n;
void handle(int num,int & sumn)
{
    if(num==1)
    {
        sumn+=speed[0];
    }
    else if (num==2)
    {
        sumn+=speed[1];
    }
    else if(num==3)
    {
        sumn+=speed[0]+speed[1]+speed[2];
    }
}
int main()
{
    int task;
    cin>>task;
    while(task--)
    {
        cin>>n;
        for (int i = 0; i <n ; ++i) {
            cin>>speed[i];
        }
        sort(speed,speed+n);
        int sumn=0;
        if(n<=3)
            handle(n,sumn);
        else
        {
            int i=0;
            for( i=n-1;i>=3;i-=2)
            {
                int t1 = speed[1] + speed[0] + speed[i] + speed[1];
                int t2 = speed[i] + speed[0]*2 + speed[i-1];
                if(t1<t2)
                {
                    sumn+=t1;
                }
                else {
                    sumn+=t2;
                }
            }
            handle(i+1,sumn);
        }
        cout<<sumn<<endl;
    }
}
```

