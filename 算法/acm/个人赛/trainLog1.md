# 2-18

## A. Two Bases

![image-20210218151741215](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210218151741215.png)

![image-20210218151749071](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210218151749071.png)

第一题一般是思维题,并且会有note提醒

看不懂题目就看note,可以很明确发现就是比较不同进制的大小

那就同意转化为十进制即可

```
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;
typedef long long int ll;
double getNums(int arr[],int type,int length)
{
    double nums=0;
    for(int i=0;i<length;i++)
    {
        int j=length-i-1;
        nums+=pow(type,j)*arr[i];
    }
    return nums;
}
void compareD(int arr1[],int arr2[],int length1,int length2,int type1,int type2)
{
    double l1= getNums(arr1,type1,length1);
    double l2 = getNums(arr2,type2,length2);
    if(l1>l2)
    {
        cout<<">"<<endl;
    }
    else if(l1<l2)
    {
        cout<<"<"<<endl;
    }
    else
        cout<<"="<<endl;
}
int main()
{
    int  arr1[11]={0};
    int  arr2[11]={0};
    int t1,t2,l1,l2;
    cin>>l1>>t1;
    for (int i = 0; i <l1 ; ++i) {
        cin>>arr1[i];
    }
    cin>>l2>>t2;
    for (int j = 0; j < l2; ++j) {
        cin>>arr2[j];
    }
    compareD(arr1,arr2,l1,l2,t1,t2);
}
```

## B. Approximating a Constant Range(线段树)

![image-20210218152123242](https://gitee.com/zisuu/picture/raw/master/img/20210218152123.png)

翻译:

找到一个子串,最大值-最小值<=1

求最长长度



既然是范围性质的题目,求区间最大值,最小值,肯定想到线段树

其次,对于求最长长度,就用取尺法

```
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<queue>
#include<vector>
using namespace std;
typedef long long int ll;
const int numN=100001;
int minTree [numN*4];
int maxTree [numN*4];
class SegmentTree{
public:
#define ls k<<1
#define  rs k<<1|1
    int size;
    SegmentTree(int size)
    {
        this->size=size;
    }
    SegmentTree()
    {
 
    }
    void build(int k,int l,int r,int nums[])
    {
        if(l==r)
        {
            minTree[k]=nums[l];
            maxTree[k]=nums[l];
        }
        else
        {
            int m=l+(r-l)/2;
            build(ls,l,m,nums);
            build(rs,m+1,r,nums);
            pushUp(k);
        }
    }
    void pushUp(int k)
    {
        minTree[k]=min(minTree[ls], minTree[rs]);
        maxTree[k]=max(maxTree[ls],maxTree[rs]);
    }
    int query(int l,int r,int L,int R,int k,int type)
    {
        if(l>=L&&r<=R)
        {
            return type==0?minTree[k]:maxTree[k];
        }
        else
        {
            int m=l+(r-l)/2;
            if(type==0)
            {
                int left=10000000,right=10000000;
                if(L<=m)
                    left=query(l,m,L,R,ls,type);
                if(R>m)
                    right=query(m+1,r,L,R,rs,type);
                return min(left,right);
            }
            else
            {
                int left=0,right=0;
                if(L<=m)
                    left=query(l,m,L,R,ls,type);
                if(R>m)
                    right=query(m+1,r,L,R,rs,type);
                return max(left,right);
            }
        }
    }
};
int arr[numN]={0};
int main()
{
    int n;
    cin>>n;
    memset(minTree,1000000,sizeof(minTree));
    memset(maxTree,0,sizeof(maxTree));
    for (int i = 0; i <n ; ++i) {
        cin>>arr[i];
    }
    SegmentTree tree (n);
    tree.build(1,0,n-1,arr);
    int l=1,r=2,ans=1;
    while(r<=n)
    {
        while(r<=n)
        {
            int l1=tree.query(0,n-1,l-1,r-1,1,0);
            int l2=tree.query(0,n-1,l-1,r-1,1,1);
            if(abs(l2-l1)<=1)
                r++;
            else
                break;
        }
        ans=max(ans,r-l);
        l++;
    }
    cout<<ans;
}C
```

## C. The Two Routes (dijkstra)

![image-20210218153404912](https://gitee.com/zisuu/picture/raw/master/img/20210218153405.png)



![image-20210218153541720](https://gitee.com/zisuu/picture/raw/master/img/20210218153541.png)



![image-20210218153547325](https://gitee.com/zisuu/picture/raw/master/img/20210218153547.png)

> 翻译:
>
> 有汽车和动车,从1出发,开往n
>
> 汽车和动车不能到达同一个城镇
>
> 城镇1-n都可以建立公路,前提是没有铁路
>
> 现在给出哪些城市之间已经建立了铁路,问最短用时是多少(汽车和动车最长用时的最小一个)

首先要学会看题意的隐藏条件,题目说城镇1-n都可以建立公路,前提是没有铁路

说明这是一个完全图,也即汽车是可以直接从1-n,前提是没有铁路1->n

现在考虑两种情况:

- 没有铁路1->n,这种情况汽车直接通过,所以答案就是求动车从1->n的最短用时(dijkstra)
- 由铁路1->,这种情况就是求汽车从1->n最短用时

所以只需要一遍dijkstra求某一个最短路即可,因为只有两种情况,所以直接用type判断即可:

```
#include<iostream>
#include<cstdio>
#include<algorithm>
#include<cmath>
#include<cstring>
#include<queue>
#include<vector>
#include <map>
using namespace std;
typedef long long int ll;
const int N=1000;
const int INF = 0x3f3f3f3f;
typedef pair<int, int> PII;
int graph[N+1][N+1]={0};
bool vis[N+1];
int dist[N+1];
int dijkstra( int n, int K,int type) {
    priority_queue<PII, vector<PII>, greater<PII>> heap;
    heap.push({0, K});
    dist[K] = 0;
    while(heap.size()){
        auto t = heap.top();
        heap.pop();
        int ver = t.second, distance = t.first;
        if (vis[ver]) continue;
        vis[ver] = true;
        for (int i = 1; i <=n ; ++i) {
            if(graph[ver][i]==type)
            {
                if(dist[i]>distance+1)
                {
                    dist[i]=distance+1;
                    heap.push({dist[i],i});
                }
            }
        }
    }
    int ans = dist[n];
    return ans == INF ? -1: ans;
}
 
int main()
{
    memset(dist,INF,sizeof(dist));
    memset(vis, false,sizeof(vis));
    int n,m;
    cin>>n>>m;
 
    for (int i = 0; i <m ; ++i) {
        int l1,l2;
        cin>>l1>>l2;
        graph[l1][l2]=graph[l2][l1]=1;
    }
    int dist1=dijkstra(n,1,1-graph[1][n]);
    cout<<dist1<<endl;
}
```

## D. Lipshitz Sequence(单调栈)

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

