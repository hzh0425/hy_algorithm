## Lipshitz Sequence(单调栈)

![image-20210218154204492](https://gitee.com/zisuu/picture/raw/master/img/20210218154204.png)

![image-20210218154211645](https://gitee.com/zisuu/picture/raw/master/img/20210218154211.png)

![image-20210218154215820](https://gitee.com/zisuu/picture/raw/master/img/20210218154215.png)

![image-20210218172159336](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210218172159336.png)

```
#include<cstdio>
#include<iostream>
#include<algorithm>
#include<queue>
#include<map>
#include<vector>
#include<stack>
#include<set>
#include<string>
#include<cmath>
#include<cstring>
#define FF first
#define SS second
#define ll long long
#define pb push_back
#define pm make_pair
using namespace std;
typedef pair<int,int> PII;
const int MAX = 2e5 + 5;
int L[MAX],R[MAX]; 
int n,q;
int a[MAX],b[MAX];
stack<int> sk;
ll ans;
int main()
{
	cin>>n>>q;
	for(int i = 1; i<=n; i++) cin>>a[i];
	for(int i = 1; i<n; i++) b[i] = abs(a[i+1] - a[i]);
	n--;
	for(int i = 1; i<=n; i++) {
		while(!sk.empty() && b[sk.top()] <= b[i]) sk.pop();
		L[i] = sk.empty() ? 0 : sk.top();
		sk.push(i);
	}
	while(sk.size()) sk.pop();
	for(int i = n; i>=1; i--) {
		while(!sk.empty() && b[sk.top()] < b[i]) sk.pop();
		R[i] = sk.empty() ? n+1 : sk.top();
		sk.push(i);
	}
	while(q--) {
		ans=0;
		int l,r,LL,RR;
		scanf("%d%d",&l,&r);
		for(int i = l; i<=r-1; i++) {
			LL=max(L[i],l-1);RR=min(R[i],r);
			ans += 1LL*b[i]*(RR-i)*(i-LL);//b[i] * (RR - LL - 1);
		}
		printf("%lld\n",ans);
	}
	return 0 ;
}
```

## 最大矩形面积

**Description**

```
在X轴上水平放置着 N 个条形图，这 N 个条形图就组成了一个柱状图，每个条形图都是一个矩形，每个
矩形都有相同的宽度，均为1单位长度，但是它们的高度并不相同。
例如下图，图1包含的矩形的高分别为2，1，4，5，1，3，3 单位长度，矩形的宽为1单位长度。
```

![img](https://gitee.com/zisuu/picture/raw/master/img/20210218172433.jpeg;charset=UTF-8)

```
你的任务就是计算柱状图中以X轴为底边的最大矩形的面积。图2阴影部分就是上述例子的最大矩形面积。
```

```
class Solution {
public:
int largestRectangleArea(vector<int>& heights) {
    stack<int> st;
    
    int ans=0;
    heights.insert(heights.begin(),0);
    heights.push_back(0);
    for (int i = 0; i <heights.size() ; ++i) {
        while(!st.empty()&&heights[st.top()]>heights[i]){
            int h=heights[st.top()];
            st.pop();
            int r=i-1;
            if(st.empty())break;
            int l=st.top();
            ans=max(ans,(r-l)*h);
        }
        st.push(i);
    }
    return ans;
}
};
```

## Maximum Xor Secondary（单调栈）

题意：

给一个长度为 n 的序列，元素不重复，求 max {a[i] ^ a[j]} （a[i] 和 a[j] 为某个区间中的最大值和次大值，区间长度任意）。

**解题思路：**

接上篇。

考虑虽然区间的个数为 O(n ^ 2) 的，但是很多区间的最大值和次大值是重复的。

类似的思路，考虑 a[i] 作为次大值时，向前能扩展到的最大值，也需要同样向前找到第一个比它大的元素。

同样维护一个单调栈，可以 O(n) 解决掉。

然后别忘了还要向后扩展一次。

```
#include <stdio.h>
#include <string.h>
#include <algorithm>
 
using namespace std;
 
const int N = 1e5 + 5;
 
int n,a[N],s[N];
 
int solve()
{
    int ret = 0 , top = 0;
    for(int i=0;i<n;i++)
    {
        while(top && a[s[top]] < a[i])
            --top;
        if(top)
            ret = max(ret,a[s[top]] ^ a[i]);
        s[++top] = i;
    }
    return ret;
}
 
int main()
{
    scanf("%d",&n);
    for(int i=0;i<n;i++)
        scanf("%d",&a[i]);
    int ans = solve();
    reverse(a,a+n);
    ans = max(ans,solve());
    printf("%d\n",ans);
    return 0;
}
```

## Mike and Feet

### 题目大意：

给定 n*n* 个元素，对于一个长度为 x*x* 的区间，其区间的力量值为区间内的最小值。求对于跨度为 11 到 n*n* 的区间，所有区间力量值的最大值。

### 思路：

首先还是先想到暴力，对于每个长度进行枚举，再用 O(n)*O*(*n*) 时间找起点和终点，最后再遍历一遍区间，就可得到最小值，时间复杂度 O(n^3)*O*(*n*3) ，会超时。

然后我们就可以想想怎么优化：

显然可知，最外面的 for*f**o**r* 循环是不能省的，然后就考虑如何优化剩下两个 for*f**o**r* 循环。

我们可以维护一个数往左和往右延伸的终点，使这个数在区间中是最小的，这里可以用单调栈去做，就可以做到 O(n)*O*(*n*) 预处理。

接着就可以循环一遍，因为在区间 (l[i],r[i])(*l*[*i*],*r*[*i*]) 中， a[i]*a*[*i*] 是最小值，所以可以把答案数组 ans[]*a**n**s*[] 更新为与 a[i]*a*[*i*] 的较大值

最后可以在倒序循环一遍，把每个长度的 ans[i]*a**n**s*[*i*] 更新为它与 ans[i+1]*a**n**s*[*i*+1]的较大值

这样做的原因也很简单：如果这个区间的最小值是 a*a* ，那么我把它缩小一点，最小值也可以为 a*a*

然后，这题基本就可以了

### 代码：

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=200005;
int n,ans[N],a[N],l[N],r[N],s[N];
stack<int> s1;
stack<int> s2;
signed main(){
	ios::sync_with_stdio(false);
	cin>>n;
	for(int i=1;i<=n;i++)
		cin>>a[i];
	for(int i=1;i<=n;i++){
		while(!s1.empty() && a[i]<a[s1.top()]){
			r[s1.top()]=i;
			s1.pop();
		}
		s1.push(i);
	}
	while(!s1.empty()){
		r[s1.top()]=n+1;
		s1.pop();
	}
	for(int i=n;i>=1;i--){
		while(!s2.empty() && a[i]<a[s2.top()]){
			l[s2.top()]=i;
			s2.pop();
		}
		s2.push(i);
	}
	for(int i=1;i<=n;i++){
		int len=(r[i]-1)-(l[i]+1)+1;
		ans[len]=max(ans[len],a[i]);
	}
	for(int i=n;i>=1;i--)
		ans[i]=max(ans[i],ans[i+1]);
	for(int i=1;i<=n;i++) cout<<ans[i]<<' ';
	return 0;
}
```

 

## Shurikens

腾腾开了一家忍者武器店。今天她愿意卖n个1美元的飞镖。2.. .， n ryo(当地货币)。在一天中，Tenten会把shurikens放在展柜上，展柜在一天开始时是空的。她的工作相当简单:有时，Tenten会在展示架上放置另一个飞镖(从现有的飞镖中挑选)。有时候，一个忍者会从橱窗里买一个忍者飞镖。由于忍者们很节俭，他们总是从展柜里买最便宜的飞镖Tenten为所有事件做记录。最后她列出了以下几种类型的记录:+表示她在展示架上放置了另一个飞镖-x表示价格飞镖被购买了今天是个幸运的日子，所有的飞镖都被买走了。现在，腾想知道她的列表是否一致，以及在展示台上放置飞镖的可能顺序。帮她弄清楚!输入第一行包含唯一的整数n (1 < n< 10)，表示神社的数量。下面2n行以上面描述的格式描述事件。它保证第一种类型的事件恰好有n个，而从1到n的每个价格在第二种类型的事件中恰好出现一次输出如果列表一致，则打印“YEs”。否则(即，如果列表是矛盾的，并且没有有效的shurikens放置顺序)，打印“no”在第一种情况下，第二行必须包含n个用空格分隔的整数，这些整数表示shurikens的价格。如果有多个答案，请打印任意一个

Examples

```
请注意在第一个例子中，Tenten将shurikens放置在价格为4和2的位置。之后，一个顾客进来，买了最便宜的飞镖，只花了2美元。下一个。腾腾在已经放置的4-rvo的基础上增加了一个价格为3的飞镖。然后一个新顾客买了这个3ryo shuriken。在这之后，她添加了一个1-亮的shuriken。最后。最后两个顾客买了飞镖1号和4号。分别。注意顺序[2.4,3,1]也是有效的。在第二个例子中，第一个顾客在任何东西被放置之前就购买了一个飞镖。这显然是不可能的。在第三个例子中，Tenten把她所有的shuriken放在了展示架上，然后一位顾客进来以2的价格购买了一个shuriken。这是不可能的，因为飞镖不是最便宜的，我们知道1-亮飞镖也在那里。
```

input

Copy

```
4
+
+
- 2
+
- 3
+
- 1
- 4
```

output

Copy

```
YES
4 2 3 1 
```

input

Copy

```
1
- 1
+
```

output

Copy

```
NO
```

input

Copy

```
3
+
+
+
- 2
- 1
- 3
```

output

Copy

```
NO
```

从后往前维护一个单调栈即可：

```
#include <bits/stdc++.h>
using namespace std;
int n,m;
stack<int> s;
vector<int> result;
int main()
{
    cin>>n;
    vector<char> operate(2*n);
    vector<int> index(2*n);
    bool flag=true;
    for(int i=0;i<2*n;i++)
    {
        cin>>operate[i];
        if(operate[i]=='-')
            cin>>index[i];
    }
    for(int i=2*n-1;i>=0;i--)
    {
        if(operate[i]=='-')
        {
            if(s.empty()||index[s.top()]>index[i])
                s.push(i);
            else{
                flag=false;
                break;
            }
        }
        else{
            if(s.empty())
            {
                flag=false;
                break;
            }
            else{
                result.push_back(index[s.top()]);
                s.pop();
            }
        }
    }
    if(flag)
    {
        cout<<"YES"<<endl;
        for(int i=result.size()-1;i>=0;i--)
            cout<<result[i]<<" ";
    }
    else{
        cout<<"NO";
    }
}

```

