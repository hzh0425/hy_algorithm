## A. Finding Sasuke

鸣人潜入大蛇丸的巢穴，正在寻找佐助。那里有很多房间。每个房间都有一扇门，每扇门都可以用n个封条和它们的整数能量a1 a2来描述。一个。所有能量ai都是非零的，绝对值不超过100。同样，n是偶数为了打开一扇门。鸣人必须找到具有整数能量b, b, n的n个封条，下面的等式为a1-b+a2.bt…an。bn = 0。所有的bi;必须和a一样非零;是，也不能超过100的绝对值。请找到每个房间所需的封条输入第一行包含唯一的整数T (1<T < 1000)，代表大蛇丸巢穴的房间数。其他几行是对门的描述每个描述以唯一包含偶数n (2 <n < 100)的一行开始，n表示密封的数量。下一行包含由非零整数a1, a2，..an (la <100, ai 0)表示封印的能量。输出对于每个门打印一个由非零整数b, b2, bn (lb;l <100, b0)组成的空格分隔的序列，表示可以打开门的封条。如果有多个有效答案，打印任意一个。可以证明至少有一个答案总是存在的

大概意思：

每个房间有n个条数，找出bi

是的∑ai*bi=0

0<绝对值ai<=100  0<绝对值bi<=100

模拟，回溯

```
#include<cstdio>
#include<cstring>
#include<iostream>
#include<string>
#include <vector>
using namespace std;
int arr[101]={0};
int n;
bool flag;
bool dfs(vector<int> temp,int pos)
{
    if(pos==n&&!flag)
    {
        //cout<<temp[0]<<" "<<temp[1]<<endl;
        int ans=0;
        for(int i=0;i<n;i++)
        {
            ans+=temp[i]*arr[i];
        }
        if(ans==0)
        {
            flag=true;
            for(int i=0;i<n;i++)
                cout<<temp[i]<<" ";
            return true;
        }
    }
    if(flag)
        return true;
    if(pos>=n)
        return false;
    for(int i=1;i<=100;i++)
    {
        temp.push_back(i);
        if(dfs(temp,pos+1))return true;
        temp.pop_back();
        temp.push_back(-i);
        if(dfs(temp,pos+1))return true;
        temp.pop_back();
    }
    return false;
}
int main()
{
    int t;
    cin>>t;
    while(t--)
    {
        flag=false;
        cin>>n;
        for(int i=0;i<n;i++)
        {
            cin>>arr[i];
        }
        dfs({},0);
        cout<<endl;
    }
}

```

但其实这是一道智力题。。

```
#include<cstdio>
#include<cstring>
#include<iostream>
#include<string>
#include <vector>
using namespace std;
int arr[101]={0};
int n;
bool flag;
int main()
{
    int t;
    cin>>t;
    while(t--)
    {
        flag=false;
        cin>>n;
        for(int i=0;i<n;i++)
        {
            cin>>arr[i];
        }
        for(int i=0;i<n;i+=2)
        {
            cout<<-arr[i+1]<<" "<<arr[i]<<" ";
        }
        cout<<endl;
    }
}

```

## B. A New Technique

忍者世界中的所有技术都是由手印组成的。目前火影忍者正在学习一项新技术。由n *m个不同的密封件组成，用不同的数字表示。它们都写在一个n x m表中。现在表丢了，鸣人从左到右记住了每一行的元素。以及从上到下每一列的元素，但他不记得行和列的顺序。请恢复与此数据一致的表，让鸣人能够学习新技术。输入输入的第一行包含唯一的整数(1 <t< 100 000)，表示测试用例的数量。它们的描述。每个测试用例描述的第一行由两个用空格分隔的整数n和m (1<n, m< 500)分别代表表中的行数和列数。所有手印都用从1到n. m的正整数进行编码下面n行包含m个以空格分隔的整数，表示从左到右表中任意一行的元素。下面的m行包含n个以空格分隔的整数，表示从上到下表中任意列的元素。所有测试用例的nm之和不超过250000。它保证输入中的每一行和每一列都只出现一次。它还保证从1到nm的每个数字在所有行和所有列中都只出现一次。最后，保证存在与输入一致的表。输出对于每个测试用例，输出n行，每行m个用空格分隔的整数，表示恢复的表。我们可以证明答案总是唯一的。



大概意思：

告诉一个N*m表的行和列，但是顺序打乱，组成原先的表：

```
6 5 4
1 2 3
1 6
2 5
3 4

1 2 3 
6 5 4 
```

本题考查hash表

```
#include<cstdio>
#include<cstring>
#include<iostream>
#include<string>
#include <vector>
#include <map>
using namespace std;
int arr[507][507]={0};
int  row[270000]={0};
int n,m;
int main()
{
    int t;
    cin>>t;
    while(t--)
    {
        cin>>n>>m;
        int temp;
        for(int i=0;i<n;i++)
        {
            for(int j=0;j<m;j++)
            {
                cin>>temp;
                row[temp]=j;
            }
        }
        for(int j=0;j<m;j++)
        {
            for(int i=0;i<n;i++)
            {
                cin>>temp;
                arr[i][row[temp]]=temp;
            }
        }
        for(int i=0;i<n;i++)
        {
            for (int j = 0; j <m ; ++j) {
                cout<<arr[i][j]<<" ";
            }
            cout<<endl;
        }
    }
}

```

## C. Perform Easily

后battlinga Shikamaru。Tayuya觉得她的长笛太容易预测了，于是用吉他代替了长笛。吉他有6根弦和无数个从1开始的弦。在第i弦上动扰数j产生音符ai +j。Tayuva想要演奏n个音符的旋律。每个音符都可以用不同的弦乐器组合演奏。性能的简单性取决于所使用的微位移的最大和最小指标之间的差异。这种差别越小，执行其他技术就越容易。请确定最小可能的差异。例如，a =1, 1, 2, 2, 3,3]，音符的顺序是4,11,11,12,12,13,13(对应第二个例子)，我们可以在第一个弦上演奏第一个音符，在第六个弦上演奏所有其他音符。然后最大的fret将是10，最小的将是3，答案是10-3=7，如图所示。

![image-20210123100140807](C:\Users\86180\AppData\Roaming\Typora\typora-user-images\image-20210123100140807.png)

![img](https://gitee.com/zisuu/picture/raw/master/img/20210123100618.png)





## D. Shurikens

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

