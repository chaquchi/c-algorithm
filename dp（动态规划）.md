---
data: 2024-03-31
---
## 组合数
- 确定状态
- 确定转移方程
- 确定起始和边界
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 2023;

ll c[N][N];


void solve()
{
	int n, m; cin >> n >> m;
	for (int i = 0; i < n; ++i)c[i][0] = 1ll;//确定状态起始点


	for (int i = 1; i < n; ++i)
	{
		for (int j = 1; j<=i; ++j)//j<=i 防止出现j大于i的情况出现
		{
			c[i][j] = (c[i - 1][j - 1] + c[i - 1][j])%p;
		}
	}

	for (int i = 0; i < n; ++i)
	{
		for (int j = 0; j < m; ++j)
		{
			cout << c[i][j] << ' ';
		}
		cout << '\n';
	}

}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();
	return 0;
}
```

## 组合数-2
- 使用了前缀思想
- 使用了乘法逆元（[[埃式筛、gcd、lcm、快速幂、乘法逆元#^9e1ef6]] ^caa204
- 使用了快速幂

```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7+9;

ll fac[N];

ll qmi(ll a, ll b)
{
	ll res = 1;
	while (b)
	{
		if (b & 1)res = res * a % p;
		a = a * a % p;
		b >>= 1;
	}
	return res;
}

ll inv(ll x) { return qmi(x, p - 2); }
ll c(int n, int m)
{
	if (n < 0 || m < 0 || n < m)return 0;
	return fac[n] * inv(fac[n - m] * fac[m] % p) % p;
}
void init()
{
	fac[0] = 1;
	for (int i = 1; i <= N; ++i)fac[i] = fac[i - 1] * i%p;
}
void solve()
{
	ll n, m; cin >> n >> m;
	cout<<c(n, m)<<'\n';

}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	init();
	int _; cin >> _;
	while(_--)solve();
	return 0;
}
```

## 采药（01背包）
- 动态规划经典题目
- 确定状态   ``` d[i][j] 中i为第几个草药 j为总消耗时间 ```
- 确定状态转移方程``` d[i][j]是从上个草药来的，上个草药不管采与不采草药都要经过 所以 d[i][j]可以来自两个情况 第i个草药采与不采 d[i-1][j-t[i]]+v[i]   d[i-1][j-0]+0 ```
- 确定边界和起始状态
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;

int T, M;
ll dp[105][1010];

ll t[105] ,v[105];//t记录采药花费时间 v记录药材价值

void solve()
{
	for (int i = 1; i <= M; ++i)cin >> t[i] >> v[i];

	for (int i = 1; i <= M; ++i)
	{
		for (int j = 1; j <= T; ++j)
		{
			if (j >= t[i])dp[i][j] = max(dp[i - 1][j - t[i]] + v[i], dp[i - 1][j]);//先判断是否满足情况 在进行状态转移 再取最大值
			else dp[i][j] = dp[i - 1][j];//不满足上颗采药时间就不采 
		}
	}
	cout << dp[M][T]<<'\n';
}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	
	
	while (cin>>T>>M)
	{
		if (T == 0 && M == 0)break;
		solve();
	}
	return 0;
}
```

- 进一步优化
- 发现始终只在使用本行和上一行的数组
- 于是去掉多余的行，只留下本行和上一行，实现滚动数组，减少空间复杂度
- 1 0 分奇偶 第一行为1，二为0，奇数&1=1 偶数&1=0  异或是不带进位的加法
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;
int T, M;
ll dp[2][1010];

ll t[105] ,v[105];

void solve()
{
	for (int i = 0; i <=M; ++i)dp[0][i] = 0;
	for (int i = 1; i <= M; ++i)cin >> t[i] >> v[i];

	for (int i = 1; i <= M; ++i)
	{ 
		int y = i & 1;
		for (int j = 1; j <= T; ++j)
		{
			if (j >= t[i])dp[y][j] = max(dp[y ^ 1][j - t[i]] + v[i], dp[y ^ 1][j]);
			else dp[y][j] = dp[y ^ 1][j];
		}
	}
	cout << dp[M & 1][T] << '\n';
}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	
	
	while (cin>>T>>M)
	{
		if (T == 0 && M == 0)break;
		solve();
	}
	return 0;
}
```

- 压缩的到一行
- 复杂都没有变
- 从左边转移到右边
- 时间从右边开始循环
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;

int T, M;
ll dp[1010];

ll t[105] ,v[105];

void solve()
{
	for (int i = 0; i <=M; ++i)dp[i] = 0;
	for (int i = 1; i <= M; ++i)cin >> t[i] >> v[i];

	for (int i = 1; i <= M; ++i)
	{ 
		for (int j = T; j >t[i]; --j)
		{
			dp[j] = max(dp[j - t[i]] + v[i], dp[j]);
		}
	}
	cout << dp[T] << '\n';
}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	
	
	while (cin>>T>>M)
	{
		if (T == 0 && M == 0)break;
		solve();
	}
	return 0;
}
```

## 无穷背包
```c
dp[i][j] = max(dp[i][j - v[i]] + w[i], dp[i - 1][j])
选了但不一定为一个  和从上一个点来的
```


```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;

ll dp[505][100005];

ll w[505], v[505];

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	int m, n; cin >> m >> n;
	for (int i = 1; i <= n; ++i)cin >> w[i] >> v[i];

	for (int i = 1; i <= n; ++i)
	{
		for (int j = 1; j <= m; ++j)
		{
			if (j >= v[i])dp[i][j] = max(dp[i][j - v[i]] + w[i], dp[i - 1][j]);
			else dp[i][j] = dp[i - 1][j];
		}
	}

	cout << dp[n][m] << '\n';
	
	return 0;
}
```
- 因为此时的状态都是从上一层转移来的和本层转移来的
- 优化为滚动
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;

ll dp[2][100005];

ll w[505], v[505];

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	int m, n; cin >> m >> n;
	for (int i = 1; i <= n; ++i)cin >> w[i] >> v[i];

	for (int i = 1; i <= n; ++i)
	{
		int k = i & 1;
		for (int j = 1; j <= m; ++j)
		{
			if (j >= v[i])dp[k][j] = max(dp[k][j - v[i]] + w[i], dp[k ^ 1][j]);
			else dp[k][j] = dp[k ^ 1][j];
		}
	}

	cout << dp[n&1][m] << '\n';
	
	return 0;
}
```
- 优化为一维
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int p = 1e9 + 7;
const int N = 1e7 + 9;

ll dp[100005];

ll w[505], v[505];

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	int m, n; cin >> m >> n;
	for (int i = 1; i <= n; ++i)cin >> w[i] >> v[i];

	for (int i = 1; i <= n; ++i)
	{
		for (int j = v[i]; j <= m; ++j)
		{
			dp[j] = max(dp[j - v[i]] + w[i], dp[j]);
		}
	}

	cout << dp[m] << '\n';
	
	return 0;
}
```
