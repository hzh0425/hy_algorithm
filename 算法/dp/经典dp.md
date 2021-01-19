## 一 股票买卖系列

这 6 道题目是有共性的，本文通过对第四道题的分析，逐步解决所有问题。因为第四题是一个最泛化的形式，其他的问题都是这个形式的简化。看下题目：



![图片](https://mmbiz.qpic.cn/mmbiz_png/map09icNxZ4nPicwNq5syrSwnBc02yxG3aTWM5qlnc1ic8rc2v4ibuquTDUWOu7GVYqFAib4iaFxekON2AqKAPnAzicNw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



第一题是只进行一次交易，相当于 k = 1；第二题是不限交易次数，相当于 k = +infinity（正无穷）；第三题是只进行 2 次交易，相当于 k = 2；剩下两道也是不限交易次数，但是加了交易「冷冻期」和「手续费」的额外条件，其实就是第二题的变种，都很容易处理。

### 1.框架

- 状态

题目问的是买卖股票的最大价格,所以需要一个变量来表示当前是否拥有股票

dp(i,j,k):当前在第i天,j=1(当前拥有股票),j=0(当前无股票),k次交易次数

- 选择

因为变量j表示当前是否拥有股票,所以肯定要根据j进行分类讨论

**若j=0**

当前的状态由之前变化而来,一种可能是之前就没有股票,一种是之前有股票,但是在今天卖掉了

所以:

```
dp(i,0,k)=max(dp(i-1,0,k),dp(i-1,1,k)+values[i])
```

**若j=1**

一种可能是之前就拥有股票,一种是之前没有股票,但是在今天买入股票

所以:

```
dp(i,1,k)=max(dp(i-1,1,k),dp(i-1,0,k-1)-values[i])
```

- 边界

边界无非就是考虑每个变量为0等特殊情况的时候该怎么取值:

**若i=-1**

dp(i,0,k)=0,dp(i,1,k)=- inf

**若k=0**

dp=(i,j,0)=0 dp(i,j,1)=-inf

- 枚举框架

```
for(i:每一天):
	for(j:是否有股票):
		for(k:从0->k)
		  dp[i][j][k]=max(...,...);
		  
```

![图片](https://gitee.com/zisuu/picture/raw/master/img/20210119191738.png)

### 第一题:k=1

直接套状态转移方程，根据 base case，可以做一些化简：



![图片](https://mmbiz.qpic.cn/mmbiz_png/map09icNxZ4nPicwNq5syrSwnBc02yxG3aIO5XoTDAoKiajdPuJ01ysZo8TxkefNMwu6MWF4r82qZ77Q3lBQQKiaew/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



直接翻译成代码：



![图片](https://mmbiz.qpic.cn/mmbiz_png/map09icNxZ4nPicwNq5syrSwnBc02yxG3av8TAoT0mb4LFdmNNoNDEeqo1ylkzJdjNQeia3vLlos4ewn9gKkc1TTw/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



显然 i = 0 时 dp[i-1] 是不合法的。这是因为我们没有对 i 的 base case 进行处理。那就简单粗暴地处理一下：



![图片](https://mmbiz.qpic.cn/mmbiz_png/map09icNxZ4nPicwNq5syrSwnBc02yxG3ajuXPgiaAurIiadqSu1NXTRT3Y60xuYdGxsAOiciaPZ0fMKzpceick1WdMicQ/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



第一题就解决了，但是这样处理 base case 很麻烦，而且注意一下状态转移方程，新状态只和相邻的一个状态有关，其实不用整个 dp 数组，只需要两个变量储存所需的状态就足够了，这样可以把空间复杂度降到 O(1):



![图片](https://mmbiz.qpic.cn/mmbiz_png/map09icNxZ4nPicwNq5syrSwnBc02yxG3aKcD7yXPDtaxqs4nWLo1KblfHJeSAF8KsWVe803VlwY8WXpicwhJ30wA/640?wx_fmt=png&tp=webp&wxfrom=5&wx_lazy=1&wx_co=1)



两种方式都是一样的，不过这种编程方法简洁很多。但是如果没有前面状态转移方程的引导，是肯定看不懂的。后续的题目，我主要写这种空间复杂度 O(1) 的解法。



### 第二题:k=∞

这种情况下,k与k-1是一样的,所以需要更改状态方程:

![图片](https://gitee.com/zisuu/picture/raw/master/img/20210119192127.png)

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int size=prices.size();
        if(size==0)return 0;
        int dp_0=0;
        int dp_1=INT_MIN;
        for(int i=0;i<size;i++)
        {
            int temp=dp_0;
            dp_0=max(dp_0,dp_1+prices[i]);
            dp_1=max(dp_1,temp-prices[i]);
        }
        return dp_0;
    }
};
```



### 第三题:含冷冻期

- 卖出股票后，你无法在第二天买入股票 (即冷冻期为 1 天)。

![image-20210119192426884](https://gitee.com/zisuu/picture/raw/master/img/20210119192427.png)

相当于状态方程更改成:

```
dp(i,k,1)=dp(i-1,k,1)/dp(i-2,k-1,0)+value[i]
```

也即我们还需要一个变量来记录dp(i-2)的值

```
class Solution {
public:
    int maxProfit(vector<int>& prices) {
        int size=prices.size();
        if(size==0)return 0;
        int dp_0=0;
        int dp_1=INT_MIN;
        int dp_2=0;
        for(int i=1;i<size;i++)
        {
            int temp=dp_0;
            dp_0=max(dp_0,dp_1+prices[i]);
            dp_1=max(dp_1,dp_2-prices[i]);
            dp_2=temp;
        }
        return dp_0;
    }
};
```



### 第四题:含手续费

在买股票的时候扣除即可

```
class Solution {
public:
int maxProfit(vector<int>& prices, int fee) {
    int len=prices.size();
    if(len==0)return 0;
    int dp_0=0;
    int dp_1=-100000;
    for (int i = 0; i <len ; ++i) {
        int temp=dp_0;
        dp_0=max(dp_0,dp_1+prices[i]);
        dp_1=max(dp_1,temp-prices[i]-fee);
    }
    return dp_0;
}
};
```



### 第五题:k=2

```
class Solution {
public:
int maxProfit(vector<int>& prices,int k=2) {
    int len=prices.size();
    if(len==0)return 0;
    int dp[len][k+1][2];
    for(int i=0;i<len;i++){
        for(int j=0;j<=k;j++){
            dp[i][j][0]=dp[i][j][1]=0;
        }
    }
    for(int i=0;i<len;i++){
        for (int j =1; j <=k ; ++j) {
            if(i==0){
                dp[i][j][0]=0;
                dp[i][j][1]=-prices[i];
                continue;
            }
            dp[i][j][0]=max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
            dp[i][j][1]=max(dp[i-1][j][1],dp[i-1][j-1][0]-prices[i]);
        }
    }
    return dp[len-1][k][0];
}
};
```



### 第六题:k=m

```
class Solution {
public:
    int maxProfit_inf(vector<int>& prices) {
        int len=prices.size();
        if(len==0)return 0;
        int dp_0=0;
        int dp_1=-prices[0];
        for (int i = 0; i <len ; ++i) {
            int temp=dp_0;
            dp_0=max(dp_0,dp_1+prices[i]);
            dp_1=max(dp_1,temp-prices[i]);
        }
        return dp_0;
    }
    int maxProfit(int k, vector<int>& prices) {
        int len=prices.size();
        if(len==0)return 0;
        if(k>len/2){
            return maxProfit_inf(prices);
        }
        int dp[len+1][k+1][2];
        for(int i=0;i<=len;i++)
        {
            for(int j=0;j<=k;j++)
                dp[i][j][0]=dp[i][j][1]=0;
        }
        for(int i=0;i<len;i++){
            for (int j =1; j <=k ; ++j) {
                if(i==0){
                    dp[i][j][0]=0;
                    dp[i][j][1]=-prices[i];
                    continue;
                }
                dp[i][j][0]=max(dp[i-1][j][0],dp[i-1][j][1]+prices[i]);
                dp[i][j][1]=max(dp[i-1][j][1],dp[i-1][j-1][0]-prices[i]);
            }
        }
        return dp[len-1][k][0];
    }
};
```

## 二 [一和零](https://leetcode-cn.com/problems/ones-and-zeroes/)

![image-20210119195332823](https://gitee.com/zisuu/picture/raw/master/img/20210119195333.png)

### 1.框架

- **状态**

dp(i,j,k):当前在第i个序列,想要组成i个0和k个1所需要的最少序列数目

- **选择**

对于i之前的每个t

dp(i,j,k)=min(dp(t,j,k)+1,dp(i,j,k))













