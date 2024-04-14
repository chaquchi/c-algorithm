---
data: 2024-03-29
---
### 埃式筛法
- 通过去掉质数的倍数，删去合数留下质数
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;

const int N = 2e6 + 9;

bitset<N> vis;//标记去掉的合数
void solve()
{
	int n; cin >> n;
	vis[0] = vis[1] = true;//一二都是合数，打上1的标记
	for (int i = 2; i <= n; i++)
		if (!vis[i])for (int j = 2 * i; j <= n; j += i)vis[j] = true;
		//从二开始         //2*j防止质数被打上标记

	for (int i = 1; i <= n; ++i)if (!vis[i])cout << i << ' ';

}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);

	solve();


	return 0;
}
```

### gcd和lcm
- ``` gcd(lcm(i,i+1),lcm(i,i+2)......lcm(i,n))=lcm(i,gcd(i+1,i+2,.....,n)) ```

```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;

const int N = 2e6 + 9;

ll gcd(ll a, ll b)
{
	return b == 0 ? a: gcd(b,  a% b);
}

ll lcm(ll a, ll b)
{
	return a / gcd(a, b) * b;
}

void solve()
{
	ll a, b; cin >> a >> b;
	cout << gcd(a, b)<<' '<<lcm(a,b)<<'\n';

}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	int _; cin >> _;
	while(_--)
	solve();


	return 0;
}
```

### 快速幂
- 使用二进制思想
- 有一就乘入结果
- 一直左移，直到为零
```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 2e6 + 9;

ll pmi(ll a, ll b, ll c)
{
	ll res = 1;
	while (b)
	{
		if (b & 1)res = res * a % c;
		a = a * a % c;
		b >>= 1;
	}
	return res;
}
void solve()
{
	ll a, b, c; cin >> a >> b >> c;
	cout << pmi(a, b, c) << '\n';
}
int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	int _; cin >> _;
	while (_--)
		solve();
	return 0;
}
```

### [[dp（动态规划）#^caa204]]乘法逆元

^9e1ef6

- 使用费马小定理
```
1/a=x(mod p)  时，ax=1  (mod p)
当p为质数时 a^p-1=1  inv(x)=1/a=a^p-2
```

```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 2e6 + 9;

const ll m = 998244353;

ll qmi(ll a, ll b)
{
	ll res = 1;
	while (b)
	{
		if (b & 1)res = res * a % m;
		a = a * a % m;
		b >>= 1;
	}
	return res;
}

ll inv(ll x) { return qmi(x, m-2); }

ll f(ll a, ll b, ll c, ll x)
{
	return (a * x % m + b) % m * inv(c * x % m) % m;
}

void solve()
{
	ll a, b, c, q; cin >> a >> b >> c >> q;
	while (q--)
	{
		ll x;
		cin >> x;
		cout << f(a, b, c,x) << '\n';
	}
	
}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	int _; cin >> _;
	while (_--)solve();


	return 0;
}
```

## 欧拉筛
- 一开始找出质数2
- 存入数组pre
- 再循环质数数组，求质数与i的乘积必是合数
- 当乘积大于范围时停下
- 当i是质数的倍数时停下——可得到的数都是以此时质数为最小因数
- O（n）复杂度

```c
#include<bits/stdc++.h>
using namespace std;
using ll = long long;
const int N = 1e8 + 9;

int vis[N];
vector<int> pre;

void solve()
{
	int n, q; cin >> n >> q;

	vis[0] = vis[1] = true;
	for (int i = 2; i <= n; ++i)
	{
		if (!vis[i])pre.push_back(i);
		for (auto& j : pre)
		{
			if ((ll)j * i > n)break;
			vis[(ll)j * i] = true;
			if (!(i % j))break;
		}
	}

	/*int s = 0;
	for (int i = 1; i <= n; ++i)if (!vis[i])c[++s] = a[i];*/

	/*for (int i = 1; i <= s; ++i)cout << c[i] << ' ';*/

	while (q--)
	{
		int x; cin >> x;

		cout << pre[x - 1] << '\n';
	}
}

int main()
{
	ios::sync_with_stdio(0), cin.tie(0), cout.tie(0);
	/*int _; cin >> _;
	while(_--)*/
	solve();
	return 0;
}
```