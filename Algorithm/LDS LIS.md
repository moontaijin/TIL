<https://www.acmicpc.net/problem/11722>

# 가장 긴 감소하는 부분 수열

## 문제

수열 A가 주어졌을 때, 가장 긴 감소하는 부분 수열을 구하는 프로그램을 작성하시오.

예를 들어, 수열 A = {10, 30, 10, 20, 20, 10} 인 경우에 가장 긴 감소하는 부분 수열은 A = {10, **30**, 10, **20**, 20, **10**}  이고, 길이는 3이다.

## 입력

첫째 줄에 수열 A의 크기 N (1 ≤ N ≤ 1,000)이 주어진다.

둘째 줄에는 수열 A를 이루고 있는 Ai가 주어진다. (1 ≤ Ai ≤ 1,000)

## 출력

첫째 줄에 수열 A의 가장 긴 감소하는 부분 수열의 길이를 출력한다.



# 풀이

위 문제를 본 후 든 생각은 dp를 이용하면 간단하게 풀 수 있을것 같다는 생각들었다.

```c++
#include <iostream>
#include <algorithm>
using namespace std;

struct PRMT {
	int cnt;
	int num;
};

PRMT dp[1010];
int arr[1010];

int main()
{
	cin.tie(0);
	ios::sync_with_stdio(0);

	int n;
	int ans = 1;
	cin >> n;

	for (int i = 0; i < n; i++)
	{
		cin >> arr[i];
	}

	dp[0].cnt = 1;
	dp[0].num = arr[0];

	for (int i = 1; i < n; i++)
	{
		dp[i].cnt = 1;
		for (int j = 0; j < i; j++)
		{
			if (dp[j].num > arr[i] && dp[j].cnt >= dp[i].cnt)
				dp[i].cnt = dp[j].cnt + 1;
		}
		dp[i].num = arr[i];

		ans = max(ans, dp[i].cnt);
	}

	cout << ans;

	return 0;
}
```



위 코드처럼 dp로 구현하였을 경우 시간복잡도가 O(N^2)이 나오게 된다.

위 문제는 N이 작기때문에 가능하지만 N의 크기가 100,000이상이 되버리면 위 코드는 사용 할 수 없게된다.



위 문제를 해결하고자 검색을 해보니 lower_bound를 이용하여 시간을 대폭 줄이는 방법이 있었다.

위 방식을 이용할 경우 dp를 사용할 때 처럼 현재 값이 들어갈 수 있는 최적의 위치를 찾는 시간복잡도가 O(N)이 걸리는 것이 아닌 이분탐색을 통한 O(logN)이 걸리게 되는데, 어째서 lower_bound를 쓸 경우 찾아지는지에 대한 공부는 좀 더 필요해보인다.



위 방식이 제대로 작동하는지를 확인해보기 위해  다른 문제를 풀어보았다.

<https://www.acmicpc.net/problem/12015>

# 가장 긴 증가하는 부분 수열 2

## 문제

수열 A가 주어졌을 때, 가장 긴 증가하는 부분 수열을 구하는 프로그램을 작성하시오.

예를 들어, 수열 A = {10, 20, 10, 30, 20, 50} 인 경우에 가장 긴 증가하는 부분 수열은 A = {**10**, **20**, 10, **30**, 20, **50**} 이고, 길이는 4이다.

## 입력

첫째 줄에 수열 A의 크기 N (1 ≤ N ≤ 1,000,000)이 주어진다.

둘째 줄에는 수열 A를 이루고 있는 Ai가 주어진다. (1 ≤ Ai ≤ 1,000,000)

## 출력

첫째 줄에 수열 A의 가장 긴 증가하는 부분 수열의 길이를 출력한다.



# 풀이

두 문제의 차이점은 감소하는 수열과 증가하는 수열을 찾는다는 점도 있겠지만 주어지는 수열의 크기인 N의 범위가 확연히 다르다.

이 문제는 O(N^2)으로 풀 경우 TO가 뜨므로 lower_bound 방식을 이용하여 코딩하였다.

```c++
#include <iostream>
#include <vector>
#include <algorithm>
using namespace std;

int main()
{
	cin.tie(0);
	ios::sync_with_stdio(0);

	int n;

	cin >> n;
	vector <int> v1,v2;

	v1.resize(n);


	for (int i = 0; i <n; i++)
	{
		cin >> v1[i];

		if (v2.empty())
			v2.push_back(v1[i]);
		else if (v2.back() < v1[i])
			v2.push_back(v1[i]);
		else
		{
			auto it=lower_bound(v2.begin(), v2.end(), v1[i]);
			*it = v1[i];
		}
	}

	cout << v2.size();

	return 0;
}
```

이 방식을 쓸 때 주의할 점은 벡터에 들어있는 수열이 실제 최장 수열은 아니라는 점이다. 위 방식은 **최장수열의 길이** 만 측정할 수 있고 최장수열이 실제로 어떤 요소로 이루어져 있는지는 알 수 없다. 
