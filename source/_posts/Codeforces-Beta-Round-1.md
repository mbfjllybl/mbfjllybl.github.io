---
title: Codeforces Beta Round 1
date: 2023-12-27 10:17:21
tags: Codeforces
categories: Codeforces
---

# [Codeforces Beta Round 1](https://codeforces.com/contest/1)

## [A. Theatre Square 1000](https://codeforces.com/contest/1/problem/A)

由于地板只能整块的铺，所以可以只考虑长宽两条边上的情况，长宽的结果相乘即得答案。

```c++
#include <bits/stdc++.h>
using namespace std;
#define int long long
 
int32_t main() {
	int n, m, a;
	cin >> n >> m >> a;
	cout << ((n + a - 1) / a) * ((m + a - 1) / a) << endl;
	return 0;
}
```

## [B. Spreadsheet 1600](https://codeforces.com/contest/1/problem/B)

首先判断数据属于哪种格式，如果开头为R且第二个位置为数字，且在2和末尾之间存在C，则为`R_C_`格式；否则为`letter_number`格式。

`letter_number`转化为`R_C_`：number直接取就行，主要是letter的转换，其实就是26进制转10进制，如下。

```c++
while (sp[i] >= 'A' && sp[i] <= 'Z') {
    a *= 26;
    a += (sp[i] - 'A' + 1);
    i++;
}
```

`R_C_`转化为`letter_number`：主要是10进制转26进制，如下。

```c++
int k1;
vector<char> v;
while (1) {
    //先算26进制的最后一位
    k1 = b % 26;
    if (k1) 
        v.push_back((b % 26) + 'A' - 1);
    else 
        v.push_back('Z'); //如果为0，代表被整除，最后一位一定为Z
    b /= 26;
    //如果最后一位为Z，代表减少了一个26
    if (v.back() == 'Z') b -= 1;
    if (!b) break;
}
//倒序输出
for (i = v.size() - 1; i >= 0; i--) cout << v[i];
```

完整代码如下。

```c++
#include <bits/stdc++.h>
using namespace std;
#define int long long
string sp;

void toNumber() {
    int i = 0, a = 0, b = 0, tmp = 1;
    while (sp[i] >= 'A' && sp[i] <= 'Z') {
        a *= 26;
        a += (sp[i] - 'A' + 1);
        i++;
    }
    while (i < sp.size()) {
        b *= 10;
        b += (sp[i] - '0');
        i++;
    }
    cout << "R" << b << "C" << a << endl;
}

void toExcel() {
    int i = 1, a = 0, b = 0;
    while (sp[i] != 'C') {
        if (i == sp.size()) {
            toNumber();
            return;
        }
        a *= 10;
        a += (sp[i] - '0');
        i++;
    }

    while (1) {
    	i++;
        if (i >= sp.size()) break;
        b *= 10;
        b += (sp[i] - '0'); 
    }
    
    int k1;
    vector<char> v;
    while (1) {
        k1 = b % 26;
        if (k1) 
            v.push_back((b % 26) + 'A' - 1);
        else 
            v.push_back('Z');
        b /= 26;
        if (v.back() == 'Z') b -= 1;
        if (!b) break;
    }
    for (i = v.size() - 1; i >= 0; i--) cout << v[i];
    cout << a << endl;

}

int32_t main() {
    int n;
    cin >> n;
    while (n--) {
        cin >> sp;
        if (sp[0] == 'R' && sp[1] < 'A') toExcel();
        else toNumber();
    }
    return 0;
}
```

## [C. Ancient Berland Circus 2100](https://codeforces.com/contest/1/problem/C)

我们考虑到这个正多边形必然是内接在某个圆里面，所以从圆心向这三个点连线，我们马上可以得到三个角，这三个角可以看作是正多边形内角的某个倍数，所以我们找到这三个角的最大公因数是多少就相当于找到了内角，找到了内角就相当于找到了正多边形的面积，期间用到了余弦定理，三角形外接圆半径和三角形面积的关系，海伦公式。

```c++
#include<bits/stdc++.h>
using namespace std;
double pi=3.141592653589793238462643;
double pf(double a){
    return a*a;
}
double gcd(double a, double b){
   if (a < b)
      return gcd(b, a);
   if (fabs(b) < 0.001)
      return a;
   else
      return (gcd(b, a - floor(a / b) * b));
}
int main(){
    double x1,y1,x2,x3,y2,y3;
    cin>>x1>>y1;
    cin>>x2>>y2;
    cin>>x3>>y3;
    double a = sqrt(pf(x1-x2)+pf(y1-y2));
    double b = sqrt(pf(x1-x3)+pf(y1-y3));
    double c = sqrt(pf(x2-x3)+pf(y2-y3));
    double p = (a+b+c)/2;
    double r=a*b*c/4/sqrt(p*(p-a)*(p-b)*(p-c));
    double alpha=acos((2*pf(r)-pf(a))/2/r/r);
    double beta=acos((2*pf(r)-pf(b))/2/r/r);
    double gama=2*pi-alpha-beta;
    double theta=gcd(gcd(alpha,beta),gama);
    printf("%.8lf",sin(theta)*(pi/theta)*r*r);
}
```

