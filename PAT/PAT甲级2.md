# A1103 Integer Factorization

```cpp
/*
基本思路：利用完全背包的思路，更新dp数组。然后从dp[cnt][n]开始回溯。每次回溯到叶子节点，就和最优的
路径比较，更新最优路径。
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
#include<iostream>
#include<cstring>
using namespace std;

const int MAXN = 405;
int n,k,p;
vector<int> numpo; //1 2 3
vector<int> nump; //保存1^p, 2^p, 3^p
int cnt = 0; //记录nump的元素个数

//int dp[int(sqrt(MAXN))+1][MAXN];
int dp[21][MAXN];

int pow2(int x, int y){
    int res = 1;
    for(int i=0; i<y; i++){
        res *= x;
    }
    return res;
}

bool better(vector<int>& path1, vector<int>& path2){
    int sum1 = 0, sum2 = 0;
    for(int i=0; i<path1.size(); i++)
        sum1 += path1[i];
    for(int i=0; i<path2.size(); i++)
        sum2 += path2[i];
    if(sum1 > sum2){
        return true;
    }else if(sum1 < sum2){
        return false;
    }else{
        int i = 0;
        while(i < path1.size()){
            if(path1[i] > path2[i]){
                return true;
            }else if(path1[i] < path2[i]){
                return false;
            }
            i++; //
        }
    }
}

vector<int> optPath, path;
bool findFlag = false;
void DFS(int i, int j, int value, int num){
    if(num == k && value == 0){
        /*for(int r=0; r<path.size(); r++) //////
            printf("%d ",path[r]);
        printf("\n");*/

        findFlag = true;
        if(optPath.empty()){
            optPath = path;
        }else{
            if(better(path, optPath)){
                optPath = path;
            }
        }
        return;
    }else if(num >= k){
        return;
    }else if(value == 0){
        return;
    }else if(i == 0){
        return;
    }
    /*int a = 0;
    while(dp[i-1][j-a*nump[i-1]] + a*nump[i-1] == dp[i][j])
        a++;*/
    vector<int> vec; //例如: 0 1 3，表示nump[i-1]可以选择0次，1次，3次
    if(dp[i-1][j] == dp[i][j])
        vec.push_back(0);
    int a = 1;
    while(j-a*nump[i-1] >= 0){
        //if(dp[i-1][j-a*nump[i-1]] + a*nump[i-1] == dp[i][j]){
        if(dp[i][j-a*nump[i-1]] + a*nump[i-1] == dp[i][j] && dp[i][j-a*nump[i-1]] == dp[i-1][j-a*nump[i-1]]){
            vec.push_back(a);
        }
        a++;
    }

    /*for(int i=0; i<a; i++){
        optPath.push_back(sqrt(nump[i-1]));
    }
    DFS(i-1, j-a*nump[i-1], value-a*nump[i-1], num+a);*/
    for(a=vec.size()-1; a>=0; a--){
        for(int b=0; b<vec[a]; b++){
            //path.push_back(sqrt(nump[i-1]));
            path.push_back(numpo[i-1]);
        }
        DFS(i-1, j-vec[a]*nump[i-1], value-vec[a]*nump[i-1], num+vec[a]);
        for(int b=0; b<vec[a]; b++){
            path.pop_back();
        }
    }
}
int main(){
    //初始化
    //fill(dp[0], dp[0]+(int(sqrt(MAXN))+1)*MAXN, 0);
    fill(dp[0], dp[0]+21*MAXN, 0);

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d",&n,&k,&p);
    int temp;
    while((temp=pow2(cnt+1, p)) <= n){
        cnt++;
        nump.push_back(temp);
        numpo.push_back(cnt);
    }

    //cnt = int(sqrt(n));
    /*cnt = int(pow(n,1.0/p));
    for(int i=1; i<=cnt; i++){
        nump.push_back(pow(i,p));
    }*/

    for(int i=1; i<=cnt; i++){
        for(int j=0; j<nump[i-1]; j++){ //
            dp[i][j] = dp[i-1][j];
        }
        for(int j=nump[i-1]; j<=n; j++){
            //dp[i][j] = max(dp[i][j-nump[i-1]]+nump[j-1], dp[i-1][j]);
            dp[i][j] = max(dp[i][j-nump[i-1]]+nump[i-1], dp[i-1][j]);
        }
    }

    if(dp[cnt][n] == n){
        DFS(cnt, n, n, 0);
        if(findFlag){
            printf("%d = ",n);
            for(int i=0; i<optPath.size(); i++){
                if(i == 0)
                    printf("%d^%d",optPath[i],p);
                else
                    printf(" + %d^%d",optPath[i],p);
            }
        }else{
            printf("Impossible");
        }
    }else{
        printf("Impossible");
    }
}

//慎用pow函数
```

错误分析：

1. 如果要对整数进行乘方操作，不要用pow函数，自己写函数来。因为不知为何，pow(5，2)的结果是24，可能是因为都是用double算的原因吧。
2. 动态规划写错了。129行到131行的dp更新操作一开始没有写。

看看别人的方法：

```cpp
/**
题意: 给定N, K, P; 将N表示成K个正整数的P次方的和，如果有多中方案，那么选择n1 + .. nk的最大的方案；
如果还有多种方案，选择序列的字典序最大的方案；
**/
/*
基本思路：直接用DFS来暴力搜索序列，DFS的复杂度应该比我的方法要高，但我的方法要多动态规划的部分，因此
总体上复杂度更高。
*/
#include <bits/stdc++.h>
using namespace std;
const int INF = 0x3f3f3f3f;
int n, k, p;
int maxFacSum = -INF;//最大底数之和
vector<int> fac, ans, temp;//最优底数序列和临时序列
/**
求n^p
**/
int power(int x) {
    int ans = 1;
    for (int i = 0; i < p; i++) {
        ans *= x;
    }
    return ans;
}
/**
初始化fac数组注意0
**/
void init() {
    int i = 0, tmp = 0;
    while (tmp <= n) {
        fac.push_back(tmp);
        tmp = power(++i);
    }
}
/**
DFS函数
index: 当前访问；
nowK:当前选中个数
sum:当前的数的和
facSum:当前的底数的和, n1 + n2 +... + nk;
**/
 
void DFS(int index, int nowK, int sum, int facSum) {
    if (sum > n || nowK > k) return;
    if (sum == n && nowK == k) {
        if (facSum > maxFacSum) {
            maxFacSum = facSum;
            ans = temp;
        }
        return;
    }
    if (index - 1 >= 0) {//fac[0]不需要
        //选index
        temp.push_back(index);
        DFS(index, nowK + 1, sum + fac[index], facSum + index);
        //不选index
        temp.pop_back();
        DFS(index - 1, nowK, sum, facSum);
    }
}
int main() {
    cin >> n >> k >> p;
    init();
    DFS(fac.size() - 1, 0, 0, 0);
    if (maxFacSum == -INF) cout << "Impossible" << endl;
    else {
        printf("%d = %d^%d", n, ans[0], p);
        for (int i = 1; i < ans.size(); i++) {
            printf(" + %d^%d", ans[i], p);
        }
        printf("\n");
    }
	return 0;
}
```



# A1108 Finding Average(20)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
#include<iostream>
using namespace std;

int n;

bool invert(string& str, double& num){
    int i = 0; //指针
    bool haveDot = false;
    int accurate = 0;
    while(i < str.size()){
        if(i == 0){
            //第一个字符必须是digit或者'+''-'
            if(str[i]!='+' && str[i]!='-' && (str[i]<'0' || str[i]>'9')){
                return false;
            }
        }else{
            if(str[i]=='.'){
                if(haveDot){
                    return false;
                }else if(str[i-1]>='0' && str[i-1]<='9'){
                    haveDot = true;
                }else{
                    return false;
                }
            }else if(str[i]<'0' || str[i]>'9'){
                return false;
            }else{ //正常
                if(haveDot){
                    accurate++; //
                }
            }
        }
        i++; //
    }
    if(accurate > 2){
        return false;
    }
    double temp;
    sscanf(str.c_str(),"%lf",&temp);
    if(temp>=-1000.0 && temp<=1000.0){
        num = temp;
        return true;
    }else{
        return false;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    string str;
    double num;
    double summ = 0.0;
    int cnt = 0;
    for(int i=0; i<n; i++){
        cin >> str;
        if(invert(str,num)){
            cnt++;
            summ += num;
        }else{
            printf("ERROR: %s is not a legal number\n",str.c_str());
        }
    }
    if(cnt == 0){
        printf("The average of 0 numbers is Undefined\n");
    }else if(cnt == 1){
        printf("The average of 1 number is %.2f\n",summ);
    }else{
        printf("The average of %d numbers is %.2f",cnt,summ/cnt);
    }
}
```

# A1109 Group Photo (25分)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
#include<iostream>
#include<cstring>
using namespace std;

struct Student{
    char name[10];
    int height;
};
const int MAXN = 10005;
int n,k;
Student students[MAXN];

bool Comp(Student a, Student b){
    if(a.height != b.height)
        return a.height > b.height;
    else
        return strcmp(a.name,b.name) < 0;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&k);
    for(int i=0; i<n; i++){
        scanf("%s %d",&students[i].name,&students[i].height);
    }
    sort(students, students+n, Comp);

    int i = 0, ed;
    int* form = new int[n/k + n%k + 1]; //从1开始
    while(i < n){
        if(i == 0){
            ed = i + n/k + n%k;
        }else{
            ed = i + n/k;
        }
        int num = ed - i;
        int central = num/2 + 1;
        for(int j=i; j<ed; j++){
            if(j == i){
                form[central] = j;
            }else{
                if((j-i)%2 == 1){ //说明应该放在左边
                    int pos = central - (1 + (j-i)/2); //190,188.186,175-->188 190
                    form[pos] = j;
                }else{
                    int pos = central + (j-i)/2; //190,188,186,175。-->188 190 186
                    form[pos] = j;
                }
            }
        }
        //输出
        for(int j=1; j<=num; j++){
            if(j == 1){
                printf("%s",students[form[j]].name);
            }else{
                printf(" %s",students[form[j]].name);
            }
        }
        printf("\n");

        i = ed;
    }
}
```

