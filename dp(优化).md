---
data: 2024-04-03
---
## 常见dp
1. 线性dp（背包问题）
2. 状压dp 
	1. 用一个二进制数表达一个子集
3. 期望dp
4. 存在dp
5. 树形dp
6. 数位dp
	1. 【1，n】之间有多少数字包含69


## 多重背包 
+ 将每一种商品数拆开，形成01背包
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 105;

ll dp[N * N];

void solve()
{
	int m, n; cin >> m >> n;
	for (int i = 1; i <= n; ++i)
	{
		int s, w, v; cin >> s >> w >> v;
		while (s--)//实现了拆开
		{
			for (int j = m; j >= v; --j)
				dp[j] = max(dp[j], dp[j - v] + w);//01背包的一维是从后向前的
		}
	}
	cout << dp[m]<<'\n';
}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();

	return 0;
}
```

+ 使用二进制优化，原本的一一拆分，需要的次数太多了
```c

#include<bits/stdc++.h>
using namespace std;

using ll = long long;

const int N = 2050;

ll dp[N];//代表背包

void solve()

{

    int m, n; cin >> m >> n;

    for (int i = 1; i <= n; ++i)

    {

        int s, w, v; cin >> s >> w >> v;

        vector<ll> ver;

        ll x = 1;

        while (s >= x)ver.push_back(x), s-=x,x<<=1;

        if (s)ver.push_back(s);

        for(auto &k:ver)

            for (int j = m; j >=k*v; --j)

                dp[j] = max(dp[j], dp[j - k*v] + k*w);//注意体积和价值都变化了

    }
    cout << dp[m]<<'\n';

}

int main()

{

    ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

    solve();

    return 0;

}
```

## 最长非降子序列
- 每一个点都有一个基本子序列长度为本身
- 每个点只要在他之前存在一个小于或等于他的点，他就可以从上一个点转移过来
- 先跑循环，将没个点列出，再看在这个点之前是否有比他小得点，进行从起点到本点之前的查询
```c
// StarryCoding P76 多重背包二周目 by 上升至
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 1e3+9;

ll a[N];

ll dp[N];//到此点时的最长子序列
void solve()
{
	int  n; cin >> n;
	
	for (int i = 1; i <= n; ++i)cin >> a[i];
	for (int i = 1; i <= n; ++i)//外循环为到此点时的最大子序列
	{
		dp[i] = 1;
		for (int j = 1; j < i ; ++j)//在此点之前寻找小于等于此点的点
		{
			if (a[j] <= a[i])dp[i] = max(dp[i], dp[j] + 1);
		}
	}

	ll ans = 0;

	for (int i = 1; i <= n; ++i)ans = max(ans, dp[i]);

	cout << ans << '\n';
}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();

	return 0;
}
```
- 二分优化
- 单调栈方式
- 这种==特殊的去尾操作==
- 原因是==贪心==，我们要求的是最长的上升子序列，当然不愿意越算子序列长度反而越小了。因此，如果我们目前算出的结果还没以前的长，会暂时保留以前的结果，当然也不丢弃目前的结果，因为之后继续计算的话，目前的结果可能更优。
- 当==新序列长度 <原序列长度时==，原序列没有被完全覆盖，因此保证长度不减小；
- 当==新序列长度 >=原序列长度时==，原序列已经被完全覆盖，现在就是以新序列为基础进行计算了。
```c
// StarryCoding P76 多重背包二周目 by 上升至
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 2e5 + 9;

ll a[N];

ll dp[N];

ll stk[N], top, pos,ans;
void solve()
{
	int  n; cin >> n;

	for (int i = 1; i <= n; ++i)cin >> a[i];
	for (int i = 1; i <= n; ++i)
	{
		pos = upper_bound(stk + 1, stk + 1 + top, a[i]) - stk;//查找最后一个大于a[i]的数的位置

		if (pos == top + 1)top++;

		stk[pos] = a[i];

		ans = max(ans, top);
	}
	cout << ans << '\n';

}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();

	return 0;
}
```

## 摆花（线性dp）
+ 分析到第i种朵花时放了j朵花--```dp[i][j]```
- 分析```dp[i][j]``` 从何处转移来-----
```c
dp[i][j]=dp[i-1][j],dp[i-1][j-1].....dp[i-1][j-k]
```
- 使用集合分析 ——每一个状态都可以分成几种，无限细分到初始点开始
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e6 + 7;
const int N = 105;

ll dp[N][N],a[N];

void solve()
{
	int n, m; cin >> n >> m;

	for (int i = 1; i <= n; ++i)cin >> a[i];
	dp[0][0] = 1;
	for (int i = 1; i <= n; ++i)
	{
		for (int j = 0; j <= m; ++j)
		{
			for (int k = 0; k <= a[i] && k <= j; ++k)
			{
				dp[i][j] = (dp[i][j] + dp[i - 1][j - k]) % p;
			}
		}
	}
	cout << dp[n][m] << '\n';
}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();
	return 0;
}
```