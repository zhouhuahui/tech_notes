# A1101 Quick Sort

```
我专门用了一个不好编程的方法
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005
int n;
int num[MAXN];
int num2[MAXN];
void init(){
	for(int i=0;i<MAXN;i++)num2[i]=i;
}
bool cmp(int a,int b){
	return num[a]<num[b];
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%d",&num[i]);
	}
	if(n==1){
		printf("1\n%d",num[0]);
		return 0;
	}
	sort(num2+1,num2+n,cmp);
	set<int> pivotSet;
	int leftMax=-1,rightMin=num[num2[1]],rightMinIndex=num2[1],num2Index=1;
	for(int i=0;i<n;i++){
		if(i==0){
			if(rightMin>num[i])pivotSet.insert(num[i]);
			if(num[i]>leftMax)leftMax=num[i];
			if(num[i+1]==rightMin){
				while(num2Index<n&&num2[num2Index]<=i+1)num2Index++;
				rightMinIndex=num2[num2Index];
				rightMin=num[rightMinIndex];
			}
		}else if(i==n-1){
			if(leftMax<num[i])pivotSet.insert(num[i]);
		}else{
			if(leftMax<num[i]&&rightMin>num[i])pivotSet.insert(num[i]);
			if(num[i]>leftMax)leftMax=num[i];
			if(num[i+1]==rightMin){
				while(num2Index<n&&num2[num2Index]<=i+1)num2Index++;
				rightMinIndex=num2[num2Index];
				rightMin=num[rightMinIndex];
			}
		}
	}
	printf("%d\n",pivotSet.size());
	bool first=true;
	for(int i:pivotSet){
		if(first)printf("%d",i),first=false;
		else printf(" %d",i);
	}
	printf("\n"); //error: 少了这个，会有一个测试点，格式错误 
	return 0;
}
```

# A1102 Invert A Binary Tree

```cpp
静态树的遍历
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 15
struct Node{
	int left,right;
	Node(){
		left=right=-1;
	}
};
int n;
Node nodes[MAXN];
bool hasFather[MAXN];
void init(){
	for(int i=0;i<MAXN;i++)nodes[i]=Node(),hasFather[i]=false;
}
void levelOrder(int u){
	queue<int> q;
	q.push(u);
	bool first=true;
	while(!q.empty()){
		u=q.front();
		q.pop();
		if(first)printf("%d",u),first=false;
		else printf(" %d",u);
		if(nodes[u].left!=-1)q.push(nodes[u].left);
		if(nodes[u].right!=-1)q.push(nodes[u].right);
	}
}
void inOrder(int u){
	stack<int> s;
	bool first=true;
	do{
		while(u!=-1){
			s.push(u);
			u=nodes[u].left;
		}
		u=s.top();
		s.pop();
		if(first)printf("%d",u),first=false;
		else printf(" %d",u);
		u=nodes[u].right;
	}while(!s.empty()||u!=-1); //error: while(!s.empty())
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d\n",&n); //error:scanf("%d",&n);
	int left,right;
	string input;
	for(int i=0;i<n;i++){
		getline(cin,input);
		stringstream ss(input);
		string temp;
		ss>>temp;
		left=(temp!="-"?stoi(temp):-1);
		ss>>temp;
		right=(temp!="-"?stoi(temp):-1);
		nodes[i].right=left;
		nodes[i].left=right;
		if(left!=-1)hasFather[left]=true;
		if(right!=-1)hasFather[right]=true;
	}
	int root;
	for(int i=0;i<n;i++)
		if(hasFather[i]==false){
			root=i;
			break;
		}
	levelOrder(root);
	printf("\n");
	inOrder(root);
	return 0;
}
```

错误分析：

1. 逻辑错误：44行，写迭代Inorder时，循环退出条件错误

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

2020/3/12更------------------------------------------------------------------------------------------------------------------------------

```
完全背包解法
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 405
#define SQRTMAXN 21
int n,k,p;
int dp[SQRTMAXN][MAXN];
void init(){
	memset(dp,0,sizeof(dp));
}
int pow2(int x,int p){
	int ans=1;
	for(int i=0;i<p;i++)ans*=x;
	return ans;
}
//x>=1, p>1
int kaifang(int x,int p){ 
	if(x==1)return 1;
	int temp,i;
	for(i=1;i<=x;i++){
		if((temp=pow2(i,p))>=x)
			break;
	}
	if(temp>x){
		return i-1;
	}else{
		return i;
	}
}
bool larger(vector<int>&v1,vector<int>&v2){
	for(int i=0;i<v1.size();i++){
		if(v1[i]>v2[i]){
			return true;
		}else if(v1[i]==v2[i]){
			continue;
		}else{
			return false;
		} 
	}
}
int maxSum=0;
vector<int> maxPath;
int curSum=0;
vector<int> path;
//left是剩余的大小，knum是剩余的k, w是正在考虑的基数 
void DFS(int left,int knum,int w){
	if(knum==0 && left!=0){
		return;
	}else if(left==0 && knum!=0){
		return;
	}else if(left==0 && knum==0){
		if(curSum>maxSum){
			maxSum=curSum;
			maxPath=path;
		}else if(curSum==maxSum && larger(path,maxPath)){
			maxPath=path;
		}
		return;
	}
	if(w==0)return;
	int wp=pow2(w,p);
	vector<int> vec;
	for(int i=0;i*wp<=left&&i<=knum;i++){
		if(dp[w][left]==dp[w-1][left-i*wp]+i*wp){
			vec.push_back(i);
		}
	}
	for(int i=0;i<vec.size();i++){
		curSum+=w*vec[i];
		for(int j=0;j<vec[i];j++)path.push_back(w);
		DFS(left-vec[i]*wp,knum-vec[i],w-1);
		for(int j=0;j<vec[i];j++)path.pop_back();
		curSum-=w*vec[i];
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d %d",&n,&k,&p);
	int num=kaifang(n,p); //可以考虑的基数的最大值 
	for(int i=1;i<=num;i++){
		int w=pow2(i,p);
		for(int j=w;j<=n;j++){
			dp[i][j]=max(dp[i-1][j],dp[i][j-w]+w);
		}
	}
	if(dp[num][n]==n)
		DFS(n,k,num);
	if(maxSum==0){
		printf("Impossible");
	}else{
		printf("%d = ",n);
		for(int i=0;i<maxPath.size();i++){
			if(i==0)printf("%d^%d",maxPath[i],p);
			else printf(" + %d^%d",maxPath[i],p);
		}
	}
	return 0;
}
```

# A1104 Sum of Number Segments

```
数学问题，找规律
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005
double num[MAXN];
int n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%lf",&num[i]);
	}
	double ans=0.0;
	for(int i=0;i<n;i++){
		ans+=(double)(i+1)*(n-i)*num[i];
	}
	printf("%.2lf",ans);
	return 0;
}
```

# A1105 Spiral Matrix(难)

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXX 10005
int m,n,x;
int input[MAXX];
int** matrix;
void findmn(int x){
	int sqrtx=sqrt(x);
	for(int i=1;i<=sqrtx;i++){
		if(x%i==0){
			n=i;
			m=x/i;
		}
	}
}
int getLayer(int r,int c){
	int layer=min(r,c);
	layer=min(layer,m-r-1);
	layer=min(layer,n-c-1);
	return layer;
}
int row,col;
bool findNext(int r,int c){
	int layer=getLayer(r,c);
	if(layer==min(m,n)/2&&r==m-r-1&&n>m){ //error:if(r==m-r-1)
        //layer要进入最里层且左右两边距离相等且列维度大于行维度
		if(c+1<n&&getLayer(r,c+1)==layer){
			row=r;
			col=c+1;
			return true;
		}else{
			return false;
		}
	}
	if(layer==min(m,n)/2&&c==n-c-1&&m>n){
        //layer要进入最里层且上下两边距离相等且行维度大于列维度
		if(r+1<m&&getLayer(r+1,c)==layer){
			row=r+1;
			col=c;
			return true; 
		}else{
			return false;
		}
	}
	if(r==layer&&n-c-1>layer){
		row=r;
		col=c+1;
		return true; 
	}
	if(r==layer&&n-c-1==layer){
		row=r+1;
		col=c;
		return true;
	}
	if(n-c-1==layer&&m-r-1>layer){
		row=r+1;
		col=c;
		return true; 
	}
	if(n-c-1==layer&&m-r-1==layer){
		row=r;
		col=c-1;
		return true;
	}
	if(m-r-1==layer&&c>layer){
		row=r;
		col=c-1;
		return true;
	}
	if(r-1!=layer){
		row=r-1;
		col=c;
		return true; 
	}else{
		if(getLayer(r,c+1)>layer){
			row=r;
			col=c+1;
			return true;
		}else return false;
	}
}
void initMatrix(){
	matrix=new int*[m];
	for(int i=0;i<m;i++)
		matrix[i]=new int[n];
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&x);
	findmn(x);
	initMatrix();//初始化matrix
	for(int i=0;i<x;i++){
		scanf("%d",&input[i]);
	}
	sort(input,input+x,greater<int>());
	row=col=0;
	for(int i=0;i<x;i++){
		matrix[row][col]=input[i];
		findNext(row,col);
	}
	//输出
	for(int i=0;i<m;i++){
		for(int j=0;j<n;j++){
			if(j==0)printf("%d",matrix[i][j]);
			else printf(" %d",matrix[i][j]);
		}
		printf("\n");
	} 
	return 0;
}
```

错误分析：

1. 逻辑错误：27行，总是写错if条件

```
有一个更好的思路：
定义direct={{0,1},{1,0},{0,-1},{-1,0}},directIndex=0;
当按照direct[directIndex]的方向变化时，
	若可以成功（不越界且要填充的位置没有被访问过）则可以，
	否则，directIndex=(directIndex+1)%4
```

# *A1106 Lowest Price in Supply Chain

做过类似的

# *A1107 Social Cluster

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
struct Node{
	int f,num;
};
Node nodes[MAXN];
vector<int> cvecs[MAXN];
int n;
void init(){
	for(int i=0;i<MAXN;i++){
		nodes[i].f=i;
		nodes[i].num=1;
	}
}
int findFather(int x){
	if(x==nodes[x].f){
		return x;
	}
	int f=findFather(nodes[x].f); //error: f=findFather(x)
	nodes[x].f=f;
	return f;
}
void union2(int a,int b){
	int fa=findFather(a);
	int fb=findFather(b);
	if(fa!=fb){
		if(nodes[fa].num>=nodes[fb].num){
			nodes[fb].f=fa;
			nodes[fa].num+=nodes[fb].num;
		}else{
			nodes[fa].f=fb;
			nodes[fb].num+=nodes[fa].num;
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d",&n);
	for(int i=1;i<=n;i++){
		int k;
		scanf("%d:",&k);
		while(k--){
			int h;
			scanf("%d",&h);
			cvecs[h].push_back(i);
		}
	}
	for(int i=1;i<=n;i++){
		if(cvecs[i].size()>1){
			for(int j=0;j<cvecs[i].size()-1;j++){
				int a=cvecs[i][j],b=cvecs[i][j+1];
				union2(a,b);
			}
		}
	}
	set<int> pset;
	vector<int> nums;
	for(int i=1;i<=n;i++){
		int root=findFather(i);
		if(pset.find(root)==pset.end()){
			pset.insert(root);
			nums.push_back(nodes[root].num);
		}
	}
	sort(nums.begin(),nums.end(),greater<int>());
	printf("%d\n",pset.size());
	for(int i=0;i<nums.size();i++){
		if(i==0)printf("%d",nums[i]);
		else printf(" %d",nums[i]);
	}
	return 0;
}
```

26分

# A1108 Finding Average(难)

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

```
有限自动机真的太好用了
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
//利用有限自动机来解决 
bool convert(string&s,double&num){
	int state=1;
	for(int i=0;i<s.size();i++){
		if(state==1){
			if(isdigit(s[i])){
				state=3;
			}else if(s[i]=='+' || s[i]=='-'){
				state=2;
			}else{
				return false;
			}
		}else if(state==2){
			if(isdigit(s[i])){
				state=3;
			}else{
				return false;
			}
		}else if(state==3){
			if(s[i]=='.'){
				state=4;
			}else if(!isdigit(s[i])){
				return false;
			}
		}else if(state==4){
			if(isdigit(s[i])){
				state=5;
			}else{
				return false;
			}
		}else if(state==5){
			if(isdigit(s[i])){
				state=6;
			}else{
				return false;
			}
		}else if(state==6){
			return false;
		}
	}
	if(state==3 || state==4 || state==5 || state==6){
		double temp;
		sscanf(s.c_str(),"%lf",&temp);
		if(temp>=-1000.0&&temp<=1000.0){
			num=temp;
			return true;
		}else{
			return false;
		}
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
        if(convert(str,num)){
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

注意：123.也算合法的

# A1109 Group Photo 

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

# A1110 Complete Binary Tree

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 25
struct Node{
	int left,right,leftNum,rightNum;
	Node(){
		left=right=-1;
		leftNum=rightNum=0;
	}
}; 
Node nodes[MAXN];
bool isRoot[MAXN];
int n,root;
void init(){
	for(int i=0;i<MAXN;i++)
		nodes[i]=Node(),isRoot[i]=true;
}
void getNum(int root){
	if(nodes[root].left==-1)nodes[root].leftNum=0;
	else{
		getNum(nodes[root].left);
		nodes[root].leftNum=nodes[nodes[root].left].leftNum+nodes[nodes[root].left].rightNum+1;
	}
	if(nodes[root].right==-1)nodes[root].rightNum=0;
	else{
		getNum(nodes[root].right);
		nodes[root].rightNum=nodes[nodes[root].right].leftNum+nodes[nodes[root].right].rightNum+1;
	}
}
bool isCBT(int s){
	if(s==-1)return true;
	int num=nodes[s].leftNum+nodes[s].rightNum+1;
	if(num==1)return true;
	int a=1,b,c;
	while(a*2-1<=num){
		a*=2;
	}
	b=(num-a+1);
	c=a/2;
	if(nodes[s].leftNum==(min(b,c)+a/2-1) && nodes[s].rightNum==(b-min(b,c)+a/2-1)){
		return isCBT(nodes[s].left)&&isCBT(nodes[s].right);
	}else{
		return false;
	}
}
int BFS(int s){
	queue<int> q;
	q.push(s);
	int ans;
	while(!q.empty()){
		ans=q.front();
		q.pop();
		if(nodes[ans].left!=-1)q.push(nodes[ans].left);
		if(nodes[ans].right!=-1)q.push(nodes[ans].right);
	}
	return ans;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d",&n);
	string s1,s2;
	for(int i=0;i<n;i++){
		cin>>s1>>s2;
		nodes[i].left=(s1=="-"?-1:stoi(s1));
		nodes[i].right=(s2=="-"?-1:stoi(s2));
		if(s1!="-")isRoot[stoi(s1)]=false;
		if(s2!="-")isRoot[stoi(s2)]=false;
	}
	for(int i=0;i<n;i++)
		if(isRoot[i]==true){
			root=i;
			break;
		}
	getNum(root);
	if(isCBT(root)){
		int last=BFS(root);
		printf("YES %d",last);
	}else{
		printf("NO %d",root);
	}
}
```

```
对于一棵有N个节点完全二叉树来说，第一个空节点的编号就是N，根据这个性质就可判断一棵树是不是完全二叉树。
```

# A1111 Online Map

```
繁琐的Dijkstra+DFS
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 505
struct Edge{
	int u,v,length,time;
	Edge(int _u,int _v,int _l,int _t){
		u=_u;v=_v;length=_l;time=_t;
	}
};
vector<Edge> graph[MAXN];
int n,m;

int dist[MAXN];
bool vis[MAXN];
set<int> Pre[MAXN];
void Dijkstra(int s,int type){ //type=0:找length最小的, type=1: 找time最小的 
	//init
	memset(dist,0x3f,sizeof(dist));
	memset(vis,0,sizeof(vis));
	//run
	dist[s]=0;
	for(int i=0;i<n;i++){
		int u=-1;
		int mi=INT_MAX;
		for(int j=0;j<n;j++){
			if(vis[j]==false && dist[j]<mi){
				mi=dist[j];
				u=j;
			}
		}
		if(u==-1)return;
		vis[u]=true;
		for(int j=0;j<graph[u].size();j++){
			int v=graph[u][j].v;
			int d=graph[u][j].length;
			int t=graph[u][j].time;
			if(type==0){
				if(vis[v]==false && dist[u]+d<dist[v]){
					dist[v]=dist[u]+d;
					Pre[v].clear();
					Pre[v].insert(u);
				}else if(vis[v]==false && dist[u]+d==dist[v]){
					Pre[v].insert(u);
				}
			}else{
				if(vis[v]==false && dist[u]+t<dist[v]){
					dist[v]=dist[u]+t;
					Pre[v].clear();
					Pre[v].insert(u);
				}else if(vis[v]==false && dist[u]+t==dist[v]){
					Pre[v].insert(u);
				}
			}
		}
	}
}
int minTime;
vector<int> minPath;
int tim;
vector<int> path;
void initDFS(){
	minTime=INT_MAX;
	minPath.clear();
	tim=0;
	path.clear();
}
void DFS(int s,int d,int type){
	path.push_back(d);
	if(d==s){
		if(type==0){
			tim=0;
			for(int i=path.size()-1;i>=1;i--){
				int u=path[i];
				int v=path[i-1];
				for(int j=0;j<graph[u].size();j++){
					if(graph[u][j].v==v){
						tim+=graph[u][j].time;
						break;
					}
				}
			}
			if(tim<minTime){
				minTime=tim;
				minPath=path;
			}
		}else{
			if(minPath.empty() || path.size()<minPath.size()){
				minPath=path;
			}
		}
		return;
	}
	for(int i:Pre[d]){
		DFS(s,i,type);
		path.pop_back();
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d",&n,&m);
	for(int i=0;i<m;i++){
		int u,v,one_way,length,t;
		scanf("%d %d %d %d %d",&u,&v,&one_way,&length,&t);
		graph[u].push_back(Edge(u,v,length,t));
		if(one_way!=1){
			graph[v].push_back(Edge(v,u,length,t));
		}
	}
	int s,d;
	scanf("%d %d",&s,&d);
	//距离最短的
	int minLen;
	vector<int> minLenPath;
	Dijkstra(s,0);
	minLen=dist[d];
	initDFS();
	DFS(s,d,0);
	minLenPath=minPath;
	//时间最小的
	int minTime2;
	vector<int> minTimePath;
	Dijkstra(s,1);
	minTime2=dist[d];
	initDFS();
	DFS(s,d,1);
	minTimePath=minPath;
	//输出
	if(minLenPath!=minTimePath){
		printf("Distance = %d: %d",minLen,minLenPath.back());
		for(int i=minLenPath.size()-2;i>=0;i--){
			printf(" -> %d",minLenPath[i]);
		}
		printf("\n");
		printf("Time = %d: %d",minTime2,minTimePath.back());
		for(int i=minTimePath.size()-2;i>=0;i--){
			printf(" -> %d",minTimePath[i]);
		}
	}else{
		printf("Distance = %d; Time = %d: %d",minLen,minTime2,minLenPath.back());
		for(int i=minLenPath.size()-2;i>=0;i--){
			printf(" -> %d",minLenPath[i]);
		}
	}
	return 0;
}
```

# A1112 Stucked Keyboard

```
关键是第8行的设置，这样条件:" in the order of being detected"比较好实现。
只要是出现在字符串中的字符，且被断定为stucked，则keyboard值为非负，且表示最早在第几个字符被发现
这样就好排序了.
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int k;
string input;
int keyboard[256]; //-2 for not exist, >=0 for stucked, -1 for exist and normal
int keyboard2[256]; //存储keyboard的索引,实现例如{{'a',2},{'b',0},{'c',-2}}的对于second项的排序
void init(){
	for(int i=0;i<256;i++){
		keyboard[i]=-2;
		keyboard2[i]=i;
	}
}
bool mayStucked(int i,string&s){
	if(i+k-1>=s.size()){
		return false;
	}
	for(int j=0;j<k;j++){
		if(s[i+j]!=s[i]){
			return false;
		}
	}
	return true;
}
bool cmp(int a,int b){
	if(keyboard[a]>=0 && keyboard[b]>=0){
		return keyboard[a]<keyboard[b];
	}else if(keyboard[a]>=0 && keyboard[b]<0){
		return true;
	}else if(keyboard[a]<0){
		return false;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d",&k);
	cin>>input;
	for(int i=0;i<input.size();i++){
		keyboard[input[i]]=(keyboard[input[i]]>=0?keyboard[input[i]]:i);
	}
	for(int i=0;i<input.size();){
		if(!mayStucked(i,input)){
			keyboard[input[i]]=-1;
			i++;
		}else{
			i=i+k;
		}
	}
	sort(keyboard2,keyboard2+256,cmp);
	//输出 
	for(int i=0;i<256;i++){
		if(keyboard[keyboard2[i]]<0){
			break;
		}
		printf("%c",keyboard2[i]);
	}
	printf("\n");
	for(int i=0;i<input.size();){
		printf("%c",input[i]);
		if(keyboard[input[i]]>=0){
			i=i+k; 
		}else{
			i++;
		}
	}
	return 0;
}
```

# A1113 Integer Set Partition

```
排序
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int n;
int num[MAXN];
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++)cin>>num[i];
	sort(num,num+n);
	if(n%2==1){
		printf("1");
		int diff=0;
		diff+=num[n/2];
		for(int i=0;i<n/2;i++){
			diff+=(num[i+n/2+1]-num[i]);
		}
		printf(" %d",diff);
	}else{
		printf("0");
		int diff=0;
		for(int i=0;i<n/2;i++){
			diff+=(num[i+n/2]-num[i]);
		}
		printf(" %d",diff);
	}
	return 0;
}
```

# A1114 Family Property(难)

```
并查集，但是理解题意有点难
首先，每个结点保存没人成员的个人信息。
后来(82行开始)，选出每个并查集的根节点，然后遍历这个并查集的每个结点，对应的信息累加到根节点上
最后对所有根节点fams进行排序
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005

struct Member{
	int estate_num,tot_area,num;
	int father; 
	bool exist;
	Member(){
		estate_num=tot_area=0;
		num=1;
		exist=false;
	}
};
Member mems[MAXN];
bool isRoot[MAXN];
int n;
void init(){
	for(int i=0;i<MAXN;i++){
		mems[i]=Member();
		mems[i].father=i;
		isRoot[i]=false;
	}
}
int getFather(int x){
	if(x==mems[x].father){
		return x;
	}
	int f=getFather(mems[x].father);
	mems[x].father=f;
	return f;
}
void union2(int a,int b){
	//注意，一定要使编号较小的做root 
	int fa=getFather(a);
	int fb=getFather(b);
	if(fa!=fb){
		if(fa<fb){
			mems[fb].father=fa;
		}else{
			mems[fa].father=fb;
		}
	}
}
bool cmp(int a,int b){
	double avg_area_a=(double)mems[a].tot_area/mems[a].num;
	double avg_area_b=(double)mems[b].tot_area/mems[b].num;
	if(avg_area_a!=avg_area_b){
		return avg_area_a>avg_area_b;
	}else{
		return a<b;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n;
	int id,idf,idm,cnum,child,estate_num,area;
	for(int i=0;i<n;i++){
		scanf("%d %d %d %d",&id,&idf,&idm,&cnum);
		mems[id].exist=true;
		if(idf!=-1){
			mems[idf].exist=true;
			union2(id,idf);
		}
		if(idm!=-1){
			mems[idm].exist=true;
			union2(id,idm);
		}
		while(cnum--){
			scanf("%d",&child);
			mems[child].exist=true;
			union2(id,child);
		}
		scanf("%d %d",&estate_num,&area);
		mems[id].estate_num=estate_num;
		mems[id].tot_area=area;
	}
	vector<int> fams;
	for(int i=0;i<MAXN;i++){
		if(mems[i].exist){
			int f=getFather(i);
			if(i==f){
				fams.push_back(f);
			}else{
				mems[f].estate_num+=mems[i].estate_num;
				mems[f].tot_area+=mems[i].tot_area;
				mems[f].num++;
			}
		}
	}
	sort(fams.begin(),fams.end(),cmp);
	//shuchu
	printf("%d\n",fams.size());
	for(int i=0;i<fams.size();i++){
		int id=fams[i];
		printf("%04d %d %.3f %.3f\n",id,mems[id].num,(double)mems[id].estate_num/mems[id].num,(double)mems[id].tot_area/mems[id].num);
	} 
	return 0;
}
```

# A1115 Counting Nodes in a BST

```
BST的插入+BFS
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
struct Node{
	int data;
	Node *left,*right;
	Node(){
		left=right=NULL;
	}
};
int n;
Node* root;
int maxLayer=-1;
void insertBST(int x,int layer,Node*&root){
	if(root==NULL){
		root=new Node();
		root->data=x;
		if(layer>maxLayer)maxLayer=layer;
		return;
	}
	if(x<=root->data){
		insertBST(x,layer+1,root->left);
	}else{
		insertBST(x,layer+1,root->right);
	}
}
int n1=0,n2=0;
void BFS(){
	queue<pair<Node*,int>> q;
	q.push({root,0});
	while(!q.empty()){
		Node*node=q.front().first;
		int layer=q.front().second;
		q.pop();
		if(layer==maxLayer){
			n1++;
		}else if(layer==maxLayer-1){
			n2++;
		}
		if(node->left)q.push({node->left,layer+1});
		if(node->right)q.push({node->right,layer+1}); 
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	int data;
	for(int i=0;i<n;i++){
		scanf("%d",&data);
		insertBST(data,0,root);
	}
	BFS();
	printf("%d + %d = %d",n1,n2,n1+n2);
	return 0;
}
```

# A1116 Come on!Let's C

```
开数组，记录状态值，根据状态值进行处理
如：award_cls记录每个人的奖品类型
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005
int n,k;
int award_cls[MAXN]; //记录对应id的奖品类型 
void init(){
	memset(award_cls,0,sizeof(award_cls));
}
bool isPrime(int x){
	if(x==1)return false;
	int sqrtx=sqrt(x);
	for(int i=2;i<=sqrtx;i++){
		if(x%i==0)return false;
	}
	return true;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	int id;
	for(int i=1;i<=n;i++){
		scanf("%d",&id);
		if(i==1)award_cls[id]=1;
		else if(isPrime(i)) award_cls[id]=2;
		else award_cls[id]=3;
	}
	scanf("%d",&k);
	int query;
	for(int i=0;i<k;i++){
		scanf("%d",&query);
		if(award_cls[query]==0){
			printf("%04d: Are you kidding?\n",query);
		}else if(award_cls[query]==-1){
			printf("%04d: Checked\n",query);
		}else{
			printf("%04d: ",query);
			int flag=award_cls[query];
			printf((flag==1?"Mystery Award\n":flag==2?"Minion\n":"Chocolate\n"));
			award_cls[query]=-1;
		}
	}
	return 0;
}
```

# A1117 Eddington Number(难)

```
二分法。
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005
int n;
int dist[MAXN];
int search(int s,int d){
	if(s==d){
		int i=s;
		while(i<=n && dist[i]==dist[s]){
			i++;
		}
		if(n-i+1==dist[s])return dist[s];
		else return min(dist[s]-1,n-s+1); //error: else return dist[s]-1;考虑case:6 7 8 9
	}
	int mid=(s+d)/2;
	int i=mid;
	while(i<=n && dist[i]==dist[mid]){
		i++;
	}
	if(n-i+1==dist[mid])return dist[mid];
	else if(n-i+1>dist[mid]){
		return search(mid+1,d);
	}else{
		if(n-mid>=dist[mid] || mid==s){ //error:if(n-mid>=dist[mid]) 考虑case:7 8 9 10 11
			return min(dist[mid]-1,n-mid+1); //error: return dist[mid]-1; 
		}else return search(s,mid-1);
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=1;i<=n;i++)scanf("%d",&dist[i]);
	sort(dist+1,dist+n+1);
	printf("%d",search(1,n));
	return 0;
}
```

注意：这题的边界条件比较难考虑

```
有更好的思路
```

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int N;
    scanf("%d",&N);
    int A[N];
    for(int i=0;i<N;++i)
        scanf("%d",&A[i]);
    sort(A,A+N);//排序
    int k=N-1;//记录当前最大的<=尝试的数在数组中的位置
    for(int i=N;i>=0;--i){//对N到0的数进行逐一尝试
        while(k>=0&&A[k]>i)//如果当前数，大于尝试的数，递减k，直至遇到最大的<=尝试的数
            --k;
        if(N-1-k>=i){//如果大于i的数>=i（即超过E英里的天数>=E）
            printf("%d",i);//这个数就是爱丁顿数，输出
            break;
        }
    }
    return 0;
}
```

# A1118 Birds in Forest

```
并查集
```

# A1119 Pre- and Post-order Traversals

```
根据前根遍历序列和后根遍历序列创建一棵二叉树
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 35
struct Node{
	int data;
	Node *left,*right;
	Node(){
		left=right=NULL;
	}
};
int n;
int pre[MAXN];
int post[MAXN];
Node* root;
void createTree(int prel,int prer,int postl,int postr,Node*&root,bool&flag){
	//printf("prel=%d prer=%d postl=%d postr=%d\n",pre[prel],pre[prer],post[postl],post[postr]);//////
	root=new Node();
	root->data=pre[prel];
	if(prel==prer){
		return;
	}
	int i=postr-1;
	while(post[i]!=pre[prel+1]){
		i--;
	}
	createTree(prel+1,prel+i-postl+1,postl,i,root->left,flag);
	if(i==postr-1){ //error: if(i==postr)
		flag=false;
	}else{
		createTree(prel+i-postl+2,prer,i+1,postr-1,root->right,flag);
	}
}
void inOrder(Node*r){
	stack<Node*>q;
	bool first=true;
	do{
		while(r){
			q.push(r);
			r=r->left;
		}
		r=q.top();
		q.pop();
		if(first)printf("%d",r->data),first=false;
		else printf(" %d",r->data);
		r=r->right;
	}while(!q.empty()||r!=NULL);
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++)cin>>pre[i];
	for(int i=0;i<n;i++)cin>>post[i];
	bool flag=true;
	createTree(0,n-1,0,n-1,root,flag);
	if(flag){
		printf("Yes\n");
		inOrder(root);
	}else{
		printf("No\n");
		inOrder(root);
	} 
	printf("\n");
	return 0;
}
```

