# 五一赛2023赛后总结

## 广告

十一赛正在命题,如果你有兴趣可以申请管理员.

## 赛后总结

大众分是 $390 \text {pts}$ ,得分情况是:

|A|B|C|D|E|F|
|:---:|:---:|:---:|:---:|:---:|:---:|
100 (574ms)|50 (574ms)||240(1.56min)||

cduck 觉得水平可以再上去一点.

所有人的得分是这样的

<https://www.luogu.com.cn/contest/107241#scoreboard>

这次是第一此 WOT 赛制,所以中途我雀食切了4题.

总体来说大家发挥的还好,该骗分的都骗了.

**但是难题没人想骗呀!**

## 颁发奖项

![](https://raw.githubusercontent.com/mahaoming2022/WOT/pictures/2.bmp)

![](https://raw.githubusercontent.com/mahaoming2022/WOT/pictures/1.bmp)

![](https://raw.githubusercontent.com/mahaoming2022/WOT/pictures/3.bmp)

## 题解

### A

太简单了,签到.

下面是 zhangsenhao6728 的提交:

```cpp
#include<bits/stdc++.h>
using namespace std;
int a[1000006];
int main(){
	int n;
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>a[i];
	}
	sort(a+1,a+n+1);
	for(int i=1;i<=n;i++){
		cout<<a[i]<<" ";
	}
	return 0;
}
```

## B

这其实是清明的题.

这道题的情况不理想.~~为什么都在骗分~~!

这是稳 $50pts$ 的代码:
```cpp
#include <iostream>
using namespace std;
int main() {
	cout << "possible";
	return 0;
}
```

其实满分也很简单.

仔细阅读题目,题目有点长,可以发现其实这道题就是求:

$p_i+\sum_{j=1}^{n_i}q_j > m_i$

>阅读理解,归纳提炼

算出即可.

这是 @cq_zry 的代码:
```cpp
#include<bits/stdc++.h>
using namespace std;
const int N=1e6+10;
int n;
int k[N];
int m[N];
int p[N];
int main()
{
	cin>>n;
	for(int i=1;i<=n;i++)
	{
		int ks;cin>>ks;
		for(int j=1;j<=ks;j++)
		{
			int x;cin>>x;
			k[i]+=x;
		}
	}
	for(int i=1;i<=n;i++) cin>>m[i];
	for(int i=1;i<=n;i++) cin>>p[i],k[i]+=p[i];
	for(int i=1;i<=n;i++) if(k[i]<m[i]) return cout<<"impossible",0;
	return cout<<"possible",0;
}

```

咦?T了两点,加上快读即可.

或者在 ```main``` 中加上

```cpp
std::ios::sync_with_stdio(false);
```

完美AC.

## C

本题阅读量大,正好迎合的教育部的要求:

>阅读理解,归纳提炼

我们发现越到上面人越少,越到下面人越多.

举个例子:

|楼|人数|
|:---:|:---:|
|1|100|
|2|70|
|3|30|
|4|10|

我们算出每一行的差:

|10|20|40|30|
|:---:|:---:|:---:|:---:|

这不是前缀和吗!

那么这个给我的数组 $a$ 正是前缀和数组.

准确的说是后缀和.

那么套一下公式得出:

$ans_{l,r}=a_l-a_{r+1}$.

代码就简单了:

```cpp
// cduck的std代码
#include<bits/stdc++.h>
using namespace std;
int a[1000005];
int main(){
	int n,m;
	cin>>n>>m;
	for(int i=1;i<=n;i++){
		scanf("%d",&a[i]);
	}
	for(int i=1;i<=m;i++){
		int x,y;
		scanf("%d%d",&x,&y);
		printf("%d\n",a[x]-a[y+1]);
	}
	return 0;
}
```

## D

打个随机发现分不多.

其实输出 $1$ 就得 $240$.

纯整活题

## E

关键此数位 $\text {dp}$.

我们可以先写个粗暴的 $\text{dfs}$.

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=15,M=1005;
int q;
int l,r,A,B;
int a[N];
int dfs(int pos,int pre,int li,int s)
{
	if(!pos) return pre==s;
	else
	{
	int ans=0,up=li?a[pos]:9;
			for(int i=0;i<=up;i++) 
			    ans+=dfs(pos-1,pre+i,li&&i==up,s);
			return ans;
	}
}
int check(int x,int s)
{
	int len=0;
	while(x) a[++len]=x%10,x/=10;
	return dfs(len,0,1,s);
}
signed main()
{
    ios::sync_with_stdio(false);
	cin>>q;
	while(q--)
	{
		cin>>l>>r>>A>>B;
		if(l>r) swap(l,r);
		int s=A+B;
		int ans=check(r,s)-check(l-1,s);
		if(ans&1) cout<<"Yes\n";
		else cout<<"No\n";
	}
}
```

呵呵呵, 0 分.

时间复杂度是指数级别的.

怎么办?

关键词:**记忆化搜索**.

就是把搜过的状态记下来,减少运算量.

每个状态只搜了一次,所以是 $\mathcal{O}(nm)$ 的.

```cpp
#include<bits/stdc++.h>
using namespace std;
#define int long long
const int N=15,M=1005;
int q;
int l,r,A,B;
int a[N],mem[N][M];
int dfs(int pos,int pre,int li,int s)
{
	if(!pos) return pre==s;
	else
	{
		if(!li&&mem[pos][pre]!=-1) return mem[pos][pre];
		else
		{
			int ans=0,up=li?a[pos]:9;
			for(int i=0;i<=up;i++) ans+=dfs(pos-1,pre+i,li&&i==up,s);
			return li?ans:mem[pos][pre]=ans;
		}
	}
}
int check(int x,int s)
{
	memset(mem,-1,sizeof(mem));
	int len=0;
	while(x) a[++len]=x%10,x/=10;
	return dfs(len,0,1,s);
}
inline void write(int x) {
    if(x<0){
        putchar('-');
        x=-x;
    }
    if(x>9) 
        write(x/10);
    putchar(x%10+'0');
}

inline int read()
{
    int x=0,f=1;
    char ch=getchar();
    while(ch<'0'||ch>'9')
    {
        if(ch=='-')
            f=-1;
        ch=getchar();
    }
    while(ch>='0' && ch<='9')
        x=x*10+ch-'0',ch=getchar();
    return x*f;
}
signed main()
{
    q=read();
	while(q--)
	{
		l=read();
        r=read();
        A=read();
        B=read();
		if(l>r) swap(l,r);
		int s=A+B;
		int ans=check(r,s)-check(l-1,s);
		if(ans&1) cout<<"Yes\n";
		else cout<<"No\n";
	}
}
```

## F

> Duck 的毒瘤杰作之一,不卡死zry才怪.

---

每道题有两种选择,做和不做.

那么暴力递归就可以了.

```cpp
#include <bits/stdc++.h>

using namespace std;

int p[524],t[524];
int n,m;

int dfs(int d,int tleft){
    int ans=0,ans1=0,ans2=0;
    if(d==n+1)
        return 0;
    ans1=dfs(d+1,tleft);
    if(tleft-t[d]>=0)
        ans2=p[d]+dfs(d+1,tleft-t[d]);
    ans=max(ans1,ans2);
    return ans;
}

int main(){
    // freopen("10.in","r",stdin);
    // freopen("10.out","w",stdout);
    ios::sync_with_stdio(false);
    cin>>n>>m;
    int maxi=0,maxv=-1;
    for(int i=1;i<=n;++i)
        cin>>p[i];
    for(int i=1;i<=n;++i)
        cin>>t[i];
    for(int i=1;i<=m;++i){
        int ret=dfs(1,i);
        if(ret*maxi>maxv*i){
            maxi=i;
            maxv=ret;
        }
    }
    return cout<<maxi<<' '<<maxv,0;
}
```

时间复杂度大概是 $O(2^n)$ .
可以拿到 $37$ 分.

----

我们可以用记忆化搜索或者递推来优化这个暴搜.

跟上面一样的,我们对于每一个 $dp_{d,tleft}$ ,只要 $d,tleft$ .不变,那么函数值就不变,所以:

```cpp

//Author:cq_zry
#include<bits/stdc++.h>
using namespace std;
inline int read()
{
	int x=0,f=0; 
	char ch=0;
	while(isdigit(ch)==0) f|=ch=='-',ch=getchar();
	while(isdigit(ch)==1) x=(x<<3)+(x<<1)+(ch^48),ch=getchar();
	return (f)?(-x):(x);
}
int n,m;
const int N=501;
int a[N],b[N];
int f[N][N];
int max_tim,max_k=-1;
inline int dfs(int now,int last)
{
	if(now>n) return 0;
	if(f[now][last]!=-1) return f[now][last];	
	int k1=0,k2=0;
	k1=dfs(now+1,last);//不选这一个
	if(last-b[now]>=0) k2=dfs(now+1,last-b[now])+a[now];//能选这一个，选了
	int maxx=max(k1,k2);
	f[now][last]=maxx;
	return maxx;
}
inline void check(int x)
{
	memset(f,-1,sizeof(f));
	int ans=dfs(1,x);
	if(ans*max_tim>x*max_k) max_k=ans,max_tim=x;
}
signed main()
{
	ios::sync_with_stdio(false);
	n=read(),m=read();
	for(int i=1;i<=n;i++) a[i]=read();
	for(int j=1;j<=n;j++) b[j]=read();
	for(int i=1;i<=m;i++)
		check(i);
	printf("%d %d",max_tim,max_k);
	return 0;
}
```

哎,只有 $82$ 
分.

普通dp,也只有 $82$ .

时间复杂度 $\mathcal{O(mn^2)}$ .

-----

优化.

看看```check```函数:

```cpp
inline void check(int x)
{
	memset(f,-1,sizeof(f));
	int ans=dfs(1,x);
	if(ans*max_tim>x*max_k) max_k=ans,max_tim=x;
}
```

其实,输入不是多组,这些状态都是不变的,所以没有必要每次都清空状态.

所以:

```cpp

//Author :cduck
#include <bits/stdc++.h>

using namespace std;

int p[524],t[524],mem[524][524];
int n,m;

int dfs(int d,int tleft){
    if(mem[d][tleft]!=-1)
        return mem[d][tleft];
    int ans=0,ans1=0,ans2=0;
    if(d==n+1)
        return 0;
    ans1=dfs(d+1,tleft);
    if(tleft-t[d]>=0)
        ans2=p[d]+dfs(d+1,tleft-t[d]);
    mem[d][tleft]=ans=max(ans1,ans2);
    return ans;
}

int main(){
    ios::sync_with_stdio(false);
    memset(mem,-1,sizeof(mem));//important
    cin>>n>>m;
    int maxi=0,maxv=-1;
    for(int i=1;i<=n;++i)
        cin>>p[i];
    for(int i=1;i<=n;++i)
        cin>>t[i];
    for(int i=1;i<=m;++i){
        int ret=dfs(1,i);
        if(ret*maxi>maxv*i){
            maxi=i;
            maxv=ret;
        }
    }
    return cout<<maxi<<' '<<maxv,0;
}
```

这样优化一下就AC了,其实时间复杂度没变,只是优化常数.

所以**记忆化搜索要根据情况清空状态,没必要不清空**.

递推没有办法优化,如果你用递推A了,请申请投稿题解.

## end
