#include <iostream>
#include <string>
#include <vector>

using namespace std;

const int N = 101;
int ans[3][N];
int remain[10][N];

int cti(char c) { return (c - '0'); }

void showA(int p) {
	int i = ans[p][0];
	if (i == 0) cout << 0;
	for (; i >= 1; i--) cout << ans[p][i];
	cout << endl;
}

void showR(int p) {
	int i = remain[p][0];
	if (i == 0) cout << 0;
	for (; i; i--) cout << remain[p][i];
	cout << endl;
}

void get(string& str, int p) {
	int i, j, n = str.size();
	ans[p][0] = n;
	for (i = n - 1, j = 1; i >= 0; i--, j++)
		ans[p][j] = cti(str[i]);
}

int pos(int head, int tail) {
	int i, j, k, len = head - tail + 1, tag;
	for (i = 9; i >= 1; i--) {
		j = remain[i][0];
		if (j == len) {
			k = head;
			for (; k >= tail; k--, j--) {
				if (ans[0][k] > remain[i][j]) return i;
				else if (ans[0][k] == remain[i][j]) tag = 1;
				else {
					tag = 0;
					break;
				}
			}
			if (tag) return i;
		}
		else if (j < len) return i;
	}
	return 0;
}

void del(int h, int t, int p) {
	if (p == 0) return;
	int i, j, carry = 0, len = remain[p][0];
	for (i = 1, j = t; i <= len; i++, j++) {
		carry += ans[0][j] - remain[p][i];
		if (carry < 0) {
			ans[0][j] = carry + 10;
			carry = -1;
		}
		else {
			ans[0][j] = carry;
			carry = 0;
		}
	}
	if (carry) ans[0][j] += carry;
}

void show(int i, int j) {
	for (; i >= j; i--) cout << ans[0][i];
}

void divide() {
	vector<int> res;
	int i = ans[0][0], j = i - ans[1][0] + 1, p;
	for (;j>=1; j--) {
		//show(i, j);//test
		p = pos(i, j);
		//cout << ": " << p << endl;//test
		del(i, j, p);
		res.push_back(p);
		for (; ans[0][i] == 0 && i >= j - 1; i--);
	}
	p = res.size();
	for (i = 0; i < p && res[i] == 0; i++);
	if (i == p) cout << 0;
	for (; i < p; i++) cout << res[i];
	cout << endl;
}

void mul(int n) {
	if (remain[n][0]) return;
	int i, carry = 0;
	int len = ans[1][0];
	for (i = 1; i <= len; i++) {
		carry += ans[1][i] * n;
		remain[n][i] = carry % 10;
		carry /= 10;
	}
	if (carry) remain[n][i++] = carry;
	remain[n][0] = i - 1;
}

int main()
{
	int i;
	string str;
	cin >> str;
	get(str, 0);
	//showA(0);//test
	cin >> str;
	get(str, 1);
	//showA(1);//test
	for (i = 1; i < 10; i++) {
		remain[i][0] = 0;
		mul(i);
		//showR(i);//test
	}
	divide();
	return 0;
}