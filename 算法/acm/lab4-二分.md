## A - Cable master

仙境的居民们决定举行一场区域性的程序设计竞赛。评审委员会已经自愿参加，并承诺组织一场有史以来最诚实的比赛。他们决定用“星形”l型拓扑为参赛者连接电脑，即把所有电脑连接到一个中央枢纽。为了组织一场真正诚实的比赛，评审委员会主席已经下令让所有参赛者均匀地围绕中心，并与中心保持相等的距离。为了购买网线，评审委员会已经联系了当地的网络解决方案供应商，要求为他们销售相同长度的指定数量的网线。评审委员会希望电缆尽可能长，以便参赛者之间坐得尽可能远。公司的电报主管被指派了这项任务。他知道库存中每根电缆的长度，最高可达一厘米，他可以以一厘米的精度切割它们，因为他知道他必须切割的电缆的长度。然而，这一次，长度不知道，电报主人完全迷惑你要帮助电缆管理员，通过编写一个程序来确定可以从库存电缆上剪下的电缆的最大长度，以获得指定的数量。

将电缆管理员可以从库存电缆中截取的最大长度(以米为单位)写入输出文件，以获得所需的电缆数量。数字的书写精度必须达到厘米，小数点后恰好是两位数字如果不可能将所要求的数量切割成每片至少一厘米长，那么输出文件必须包含单独的数字“o.oo”(不带引号)。

- 

这题就是普通的二分查找,但要注意,右边界是sum/k!!!

其次,答案是精确到米

```
#include <iostream>
#include <math.h>
using namespace std;
double a[10010];
int n,k;
int canFinish(double x){
    int ans=0;
    for(int i=0;i<n;i++)
        ans+=(int)(a[i] / x);
    return ans>=k;
}
int main() {
    while (~scanf("%d %d", &n, &k)) {
        double sum = 0;
        for (int i = 0; i < n; i++) {
            scanf("%lf", &a[i]);
            sum += a[i];
        }
        double left = 0.0, right = sum / k, mid = 0;
        while (right - left >= 1e-8) {
            mid = (right + left) / 2;
            if (canFinish(mid)) {
                left = mid;
            } else {
                right = mid;
            }
        }
        printf("%0.2lf", floor(right * 100) / 100);
    }
}
```

## B - Hamburgers

Polycarpus非常喜欢汉堡包。他特别喜欢自己亲手做的汉堡包。波利卡普斯认为只有三种合适的材料可以制作汉堡包:面包、香肠和奶酪。他把自己最喜欢的“Le Hamburger de Polycarpus”的食谱写成了一串字母“B”(面包)，“s”(香肠)，“c  ”(奶酪)。食谱中的配料从下往上排列，例如，食谱“BSCBS”代表汉堡，配料从下往上排列为面包、香肠、奶酪、面包和香肠。Polycarpus的厨房里有nb块面包，ns块香肠和nc块奶酪。除了。附近的商店有这三种食材，价格是pb卢布一片面包，ps一片香肠，pc一片奶酪。波吕卡普斯有r卢布，他准备买这些卢布。他最多能做多少个汉堡包?你可以假设Polycarpus不能折断或切片任何一片面包、香肠或奶酪。此外，每一种食材都有无限的数量可供选择。

Print the maximum number of hamburgers Polycarpus can make. If he can't make any hamburger, print 0.

> ```
> BBBSSC
> 6 4 1
> 1 2 3
> 4
> ```
>
> Output
>
> ```
> 2
> ```
>
> Input
>
> ```
> BBC
> 1 10 1
> 1 10 1
> 21
> ```
>
> Output
>
> ```
> 7
> ```
>
> Input
>
> ```
> BSC
> 1 1 1
> 1 1 3
> 1000000000000
> ```
>
> Output
>
> ```
> 200000000001
> ```

这题主要就是思想能不能转变过来

在canFinish中,计算当前要做的数量总共需要多少钱即可

```
#include <iostream>
#include <math.h>
using namespace std;
typedef long long int ll;
int must[4]={0};
int have[4]={0};
int price[4]={0};
int sum=0;
int minv=0;
string s;
ll nums;
int canFinish(ll cur){
    ll cost=0;
    for(int i=0;i<3;i++)
    {
        if(have[i]<must[i]*cur)
            cost+=(must[i]*cur-have[i])*price[i];
    }
    return cost<=nums;
}
int main() {
    cin>>s;
    for(int i=0;i<s.size();i++)
    {
        if(s[i]=='B')must[0]++;
        else if(s[i]=='S')must[1]++;
        else if(s[i]=='C')must[2]++;
    }
    for (int j = 0; j < 3; ++j) {
        cin>>have[j];
    }
    for (int j = 0; j < 3; ++j) {
        cin>>price[j];
        sum+=price[j];
        if(price[j]<price[minv])minv=j;
    }
    cin>>nums;

    ll left=0;
    ll right=((nums/price[minv])+have[minv])+1;
    while(left<right)
    {
        ll mid=left+(right-left+1)/2;
        if(canFinish(mid))
            left=mid;
        else
            right=mid-1;
    }
    cout<<left;
}
```

## C - Monthly Expense

农夫约翰是一个惊人的会计奇才，他已经意识到他可能会用完所有的钱来经营他的农场。他已经计算并记录了钱数的准确数字;在接下来的N(1个sN s 10万)天里，他将需要每天花费。FJ想要为M (1 sM sN)个被称为“fajmonths”的连续财政时期创建一个预算。每个fajmonths都包含一组连续的1天或更多天。每一天都包含在一个fajmonth中。Fl的目标是安排fajmonth，以最大限度地减少fajmonth的开支，从而确定他的每月开支限额。输入第1行:两个用空格分隔的整数:N和M行2 . .N+1:第一行是农民约翰在第i天花费的美元数输出第1行:最小的月限农民约翰能承受的。

Sample Input

```
7 5
100
400
300
100
500
101
400
```

Sample Output

```
500
```

Hint

> If Farmer John schedules the months so that the first two days are a month, the third and fourth are a month, and the last three are their own months, he spends at most $500 in any month. Any other method of scheduling gives a larger minimum monthly limit.

主要还是左右边界的取值

右边界肯定是总数

左边界是最大值

```
typedef long long int ll;
int n,m;
int arr[100005]={0};
ll nums;
int canFinish(ll cur){
    int ans=0;
    for(int i=0;i<n;i++)
    {
        int j=i+1;
        int cost = arr[i];
        while(cost+arr[j]<=cur)
        {
            cost+=arr[j];j++;
        }
        i=j-1;
        ans++;
    }
    return ans<=m;
}
int main() {
    cin>>n>>m;
    int sumn=0;
    int maxn=0;
    for (int i = 0; i < n; ++i) {
        cin>>arr[i];
        sumn+=arr[i];
        maxn=max(maxn,arr[i]);
    }
    int left=maxn;
    int right=sumn+1;
    while(left<right)
    {
        int mid=left+(right-left)/2;
        if(canFinish(mid))
            right=mid;
        else left=mid+1;
        //cout<<mid<<endl;
    }
    cout<<left;
}
```













