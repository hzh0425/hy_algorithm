## A - 小希的迷宫

![image-20210208155041874](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210208155041874.png)

问题的本质就是问一个图里面有没有环

可以用公式(点数=边数-1)来判断

也可以用并查集:

- **对于要合并的两个点,父节点不同**

- **只有一个连通集,不能有两个**

```
#include <iostream>
#include <cstdio>
#include <cmath>
#include <cstring>
#include <vector>
#include <algorithm>
#include<queue>
using namespace std;
#define mm(x,b) memset(x,b,sizeof(x))
#define rep(i,a,n) for (int i=a;i<n;i++)
int vis[100000 + 5];
int arr[100000+5];
class Union{
public:
    Union()
    {
    }
    int find(int n)
    {
        if(arr[n]==n)
            return n;
        else
            return arr[n]=find(arr[n]);
    }
    int isConnected(int i,int j)
    {
        return find(i)==find(j);
    }
    int Connect(int i,int j)
    {
        int fai=find(i);
        int faj=find(j);
        //cout<<i<<" "<<j<<" "<<fai<<" "<<faj<<endl;
        if(fai!=faj)
        {
            arr[fai]=faj;
            return true;
        }
        else
        {
            return false;
        }
    }
};
int main()
{
    int start,end;
    Union u;
    while(1)
    {
        bool flag=true;
        rep(i,0,100001)
            arr[i]=i;
        mm(vis, 0);
        while(1)
        {
             cin>>start>>end;
             if(!start&&!end)break;
             if(start==-1&&end==-1)
                 return 0;
             if(!u.Connect(start,end))
             {
                flag=false;
             }else{
                 vis[start]++;
                 vis[end]++;
             }
        }
        int cnt=0;
        rep(i,0,100001)
        {
            if(vis[i]&&arr[i]==i)
                cnt++;
        }
        if(!flag||cnt>1) cout<<"No"<<endl;
        else cout<<"Yes"<<endl;
    }
}
```

## B - Is It A Tree?

和A一样





