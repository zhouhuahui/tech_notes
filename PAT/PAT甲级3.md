# A1040 Longest Symmetric String

```
直接动态规划
用了动态规划后只有O(n)的复杂度，若不用的话，有O()
```

# A1041 Be Unique(难)

```
基本思路：
数据：int bets[10001]=0; int players[MAXN]=0
1).当前玩家i(i>0)接受一个数a，若bets[a]!=0，则players[bets[a]]=0;否则，players[i]=a,bets[a]=i;
2).i++;回到1)
3).找到第一个使players不为0的
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXNUM 10001
#define MAXN 100005
int n;
int bets[MAXNUM],players[MAXN];
void init(){
	memset(bets,0,sizeof(bets));
	memset(players,0,sizeof(players));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	int cnt=1,a=0;
	cin>>a;
	a=0;
	while(cin>>a,a!=0){
		if(bets[a]!=0){
			players[bets[a]]=0;
		}else{
			players[cnt]=a;
			bets[a]=cnt;
		}
		a=0;
		cnt++;
	}
	int i=1;
	while(i<cnt && players[i]==0){
		i++;
	}
	if(i<cnt){
		cout<<players[i];
	}else{
		cout<<"None";
	}
	return 0;
}
```

# A1042 Shuffling Machine（难）

我对题意理解错了，所谓的把第i个牌移动到第j个位置，不是把像排队那样，把第i张牌插入第j张牌那里，然后相应的排移动位置，而是，直接放在第j个位置。

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int poker[54],permu[54];
int k;
void init(){
	for(int i=0;i<54;i++)poker[i]=i;
}
string getStr(int a){
	string s;
	s=(a/13==0?"S":a/13==1?"H":a/13==2?"C":a/13==3?"D":"J");
	int b=a%13+1;
	s+=to_string(b);
	return s;
}
void shuffle(){
	for(int i=0;i<54;i++){
		if(i==permu[i])continue;
		if(i<permu[i]){
			int temp=poker[i];
			for(int j=i+1;j<=permu[i];j++){
				poker[j-1]=poker[j];
			}
			poker[permu[i]]=temp;
		}else{
			int temp=poker[i];
			for(int j=i-1;j>=permu[i];j--){
				poker[j+1]=poker[j];
			}
			poker[permu[i]]=temp;
		} 
	}
}
int main(){
	freopen("D:\\input.txt","r",stdin);
	init();
	cin>>k;
	for(int i=0;i<54;i++)cin>>permu[i];
	//shuffle
	while(k--)shuffle();
	//输出
	for(int i=0;i<54;i++){
		if(i==0)cout<<getStr(poker[i]);
		else cout<<" "<<getStr(poker[i]);
	} 
}
```

以上是错误代码。

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int poker[54],permu[54];
int k;
void init(){
	for(int i=0;i<54;i++)poker[i]=i;
}
string getStr(int a){
	string s;
	s=(a/13==0?"S":a/13==1?"H":a/13==2?"C":a/13==3?"D":"J");
	int b=a%13+1;
	s+=to_string(b);
	return s;
}
void shuffle(){
	int end[54];
	for(int i=0;i<54;i++)end[permu[i]-1]=poker[i];
	for(int i=0;i<54;i++)poker[i]=end[i];
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>k;
	for(int i=0;i<54;i++)cin>>permu[i];
	//shuffle
	while(k--)shuffle();
	//输出
	for(int i=0;i<54;i++){
		if(i==0)cout<<getStr(poker[i]);
		else cout<<" "<<getStr(poker[i]);
	} 
}
```

# A1043 Is it a Binary Search Tree(BST)

```cpp
#include<cstdio>
#include<vector>
using namespace std;

struct Node{
    int data; //数据域
    Node *left,*right;
    Node(int d){data = d; left = right = nullptr;}
};

int n; //n个结点的二叉查找树
vector<int> Origin; //保存原始的输入序列

void Insert(Node* &root, int data){
    if(root == nullptr){ //如果到叶子结点后面了
        root = new Node(data);
        return;
    }
    if(data < root->data) Insert(root->left,data);
    else Insert(root->right,data); //若data大于等于root的，插在右子树
}

void preOrder(Node* root,vector<int>&vi){
    if(!root) return;
    vi.push_back(root->data);
    preOrder(root->left,vi);
    preOrder(root->right,vi);
}
void preOrderMirror(Node* root,vector<int>&vi){ //遍历镜像树，只需更改递归调用的顺序
    if(!root) return;
    vi.push_back(root->data);
    preOrderMirror(root->right,vi);
    preOrderMirror(root->left,vi);
}
void postOrder(Node* root,vector<int>&vi){
    if(!root) return;
    postOrder(root->left,vi);
    postOrder(root->right,vi);
    vi.push_back(root->data);
}
void postOrderMirror(Node* root,vector<int>&vi){
    if(!root) return;
    postOrderMirror(root->right,vi);
    postOrderMirror(root->left,vi);
    vi.push_back(root->data);
}

int main(){
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        int a;
        scanf("%d",&a);
        Origin.push_back(a);
    }
    vector<int> pre;
    vector<int> preM;
    vector<int> post;
    vector<int> postM;
    //创建BST
    Node* root = nullptr;
    for(int i=0;i<n;i++){
        Insert(root,Origin[i]);
    }
    
    preOrder(root,pre); //先序遍历序列
    preOrderMirror(root,preM); //镜像先序遍历序列
    if(Origin == pre){ //如果原序列与先序相同
        printf("YES\n");
        postOrder(root,post);
        //输出
        printf("%d",post[0]);
        for(int i=1;i<n;i++){
            printf(" %d",post[i]);
        }
    }else if(Origin == preM){ //如果原序列与镜像先序相同
        printf("YES\n");
        postOrderMirror(root,postM);
        //输出
        printf("%d",postM[0]);
        for(int i=1;i<n;i++){
            printf(" %d",postM[i]);
        }
    }else{
        printf("NO\n");
    }
}

```

# A1044 Shopping in Mars(双指针法)

```cpp
/*
基本思路1：
（1）有两个指针i,j，i=0，
（2）j递增，找一个j使差值diff=num[i]+...+num[j]-15>=0的最小的j或j=n(15是要付的钱)，若
大于等于0，则diff与目前最小的差值比较并进行必要的保存i,j对的操作和数据更新操作，i++，回到(2);若小于0，则说明后面不可能有足够的钱了，则到（3）
（3）看保存的i,j对，输出。
所以思路1可以概括为双指针迭代法，i,j指针不断向前移动
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
#include<vector>
using namespace std;

const int MAXN = 1e5+5;
int n,m;
int num[MAXN];

bool Comp(pair<int,int> a, pair<int,int> b){
    return a.first < b.first;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    for(int i=1; i<=n; i++){
        scanf("%d",&num[i]);
    }

    int i = 1, j = 1; //[i,j)
    vector<pair<int,int> > pairs;
    int summ = 0;
    int minDiff = 1e8+5;
    while(i <=n && j <= n){
        if(j == i){
            summ += num[j++];
        }
        while(j <= n && summ+num[j] < m){
            summ += num[j++];
        }
        if(summ < m){
            if(j <= n){ //
                summ += num[j++];
            }
        }
        //printf("i=%d j=%d num[i]=%d num[j-1]=%d summ=%d\n",i,j,num[i],num[j-1],summ);
        if(summ < m){
            break;
        }else{
            int temp = summ - m;
            if(temp < minDiff){
                minDiff = temp;
                pairs.clear();
                pairs.push_back(pair<int,int>(i,j));
            }else if(temp == minDiff){ //
                pairs.push_back(pair<int,int>(i,j));
            }
            if(summ == m){
                i++;
                if(i<=n && j<=n){
                    summ -= num[i-1];
                }
            }else{
                /*i++; j--;
                summ -= num[i-1];
                summ -= num[j+1];*/
                if(j-i > 1){
                    i++; j--;
                    summ -= num[i-1];
                    summ -= num[j];
                }else{
                    i++;
                    summ -= num[i-1];
                }
            }
        }
    }
    sort(pairs.begin(), pairs.end(), Comp);
    for(i = 0; i < pairs.size(); i++){
        printf("%d-%d\n",pairs[i].first,pairs[i].second-1);
    }
}


```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

/*
基本思路1：
（1）有两个指针i,j，i=0，
（2）j递增，找一个j使差值diff=num[i]+...+num[j]-15>=0的最小的j或j=n(15是要付的钱)，若
大于等于0，则diff与目前最小的差值比较并进行必要的保存i,j对的操作和数据更新操作，i++，回到(2);若小于0，则说明后面不可能有足够的钱了，则到（3）
（3）看保存的i,j对，输出。
所以思路1可以概括为双指针迭代法，i,j指针不断向前移动
*/
const int MAXN = 1e5+5;
int n,m;
int num[MAXN];

bool Comp(pair<int,int> a, pair<int,int> b){
    return a.first < b.first;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    for(int i=1; i<=n; i++){
        scanf("%d",&num[i]);
    }

    int i = 1, j = 1; //[i,j)
    vector<pair<int,int> > pairs;
    int summ = 0;
    int minDiff = 1e8+5;
    while(i<=n){ //error:while(i<=n && j<=n)
        while(j<=n && summ<m){
            summ+=num[j];
            j++;
        }
        //printf("i=%d j=%d num[i]=%d num[j-1]=%d summ=%d\n",i,j,num[i],num[j-1],summ);
        if(summ < m){
            break;
        }else{
            int temp = summ - m;
            if(temp < minDiff){
                minDiff = temp;
                pairs.clear();
                pairs.push_back(pair<int,int>(i,j));
            }else if(temp == minDiff){ 
                pairs.push_back(pair<int,int>(i,j));
            }
            if(i==j){
                i++;
				j++;
            }else{
                summ-=num[i];
				i++;
            }
        }
    }
    sort(pairs.begin(), pairs.end(), Comp);
    for(i = 0; i < pairs.size(); i++){
        printf("%d-%d\n",pairs[i].first,pairs[i].second-1);
    }
}
```

# A1045 Favorite Color Stripe

由于序列中某一个元素选择与否，和序列这个元素前面的元素相关，且如果这个元素前面的元素的优先级大于它并且前面的元素的最大长度加上1大于最大值，则选择这个元素。所以用动态规划方法。

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int pri[201]; //i的顺序,例如:2 3 1 5 6，pri[2]=1,pri[3]=2
int maxLen[10005]; //maxLen[i]表示以i结尾的子串的最大长度
int input[10005];
int n,m,l;

void init(){
	memset(pri,0,sizeof(pri));
	memset(maxLen,0,sizeof(maxLen));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n>>m;
	for(int i=1;i<=m;i++){
		int a;
		cin>>a;
		pri[a]=i;
	}
	cin>>l;
	for(int i=0;i<l;i++){
		int a;
		cin>>a;
		input[i]=a;
		if(i==0){
			if(pri[a]!=0)maxLen[i]=1;
		}else{
			if(pri[a]!=0){
				int mx=1; //error: int mx=0
				for(int j=0;j<i;j++){
					if(pri[input[j]]<=pri[a] && maxLen[j]+1>mx){
						mx=maxLen[j]+1;
					}
				}
				maxLen[i]=mx;
			}
		}
	}
	int mx=0;
	for(int i=0;i<l;i++){
		if(maxLen[i]>mx)mx=maxLen[i];
	}
	cout<<mx<<endl;
}
```

错误分析：

1. 逻辑错误：33行：由于输入a是在喜欢的队列中，所以以a结尾的最长子串是1，所以mx不能设为0；若mx设为0，且前面的输入的pri值都比a的高，那么mx没有变，还是0，不就出错了吗。

# A1046 Shortest Distance(难)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<cmath>
#include<algorithm>
using namespace std;

const int MAXN = 1e4+5;
int n,m;
int dist[MAXN][MAXN];

int getDist(int a, int b){ //b >= a
    if(b < a){ //swap
        int temp = a;
        a = b;
        b = temp;
    }
    if(dist[a][b] != -1){
        return dist[a][b];
    }else if(dist[a+1][b] != -1){
        int res = dist[a][a+1] + dist[a+1][b];
        dist[a][b] = res; //更新dist数组
        return res;
    }else{
        int res = dist[a][a+1] + getDist(a+1,b);
        dist[a][b] = res; //更新dist数组
        return res;
    }
}
int getDist2(int a, int b){ //b >= a
    if(b < a){ //swap
        int temp = a;
        a = b;
        b = temp;
    }
    int next = b == n ? 1 : b+1;
    if(dist[b][a] != -1){
        return dist[b][a];
    }else if(dist[next][a] != -1){
        int res = dist[b][next] + dist[next][a];
        dist[b][a] = res;
        return res;
    }else{
        int res = dist[b][next] + getDist(next,a);
        dist[b][a] = res;
        return res;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=1; i<=n; i++){
        for(int j=1; j<=n; j++){
            if(i == j){
                dist[i][j] = 0;
            }else{
                dist[i][j] = -1;
            }
        }
    }
    for(int i=1; i<=n; i++){
        int a;
        scanf("%d",&a);
        if(i != n){
            dist[i][i+1] = a;
            dist[i+1][i] = a;
        }else{
            dist[1][n] = a;
            dist[n][1] = a;
        }
    }

    scanf("%d",&m);
    int a,b;
    for(int i = 0; i<m; i++){
        scanf("%d %d",&a,&b);
        printf("%d\n",min(getDist(a,b),getDist2(a,b)));
    }
}


```

错误分析：

1. 通过设置MAXN*MAXN的数组来记录i-->j和j-->i的距离，但是这样做仍然运行超时。观察我的核心函数``getDist()``, 有一个缺陷：假设``dist[1][5]记录了，但是要找dist[1][6]，我的函数会先看dist[2][6]有没有记录，再看dist[3][6]有没有，这样造成一个问题是记录的1到5的距离并没有用到，其实，只要算dist[1][5]+dist[5][6]就可以了，导致算法运行超时``.
2. 我的设计有缺陷：这题是考环的性质，若算出了``dist[1][5]``，那么自然地``dist[5][1] = 环长 - dist[1][5]``， 不用再计算``dist[5][1]``；还有，其实不用算``dist[1][2], dist[1][3],dist[4][7]之类的，只用记录dist[1][i],2<=i<=n, dist[i][j]=dist[1][j]-dist[1][i]``

```
基本思路：dist[]表示1到各个点顺时针的距离
则i到j的距离为dist[j]-dist[i]。
还有，1到i的逆时针距离=环长-dist[i]
```

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n,m;
int dist[MAXN] = {0}; //dist[i]是1到i+1的距离,dist[n]是环的总长度

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=1; i<=n; i++){
        int a;
        scanf("%d",&a);
        dist[i] = dist[i-1] + a;
    }

    scanf("%d",&m);
    int a,b;
    for(int i = 0; i<m; i++){
        scanf("%d %d",&a,&b);
        int t = abs(dist[b-1] - dist[a-1]);
        printf("%d\n",min(t, dist[n]-t));
    }
}

```

# A1047 Student List for Course

```cpp
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 40005;
vector<int>course[50002];
char int2name[MAXN][5];

//题意:把每个课程对应的选课人，按选课人名称顺序输出
//考察：stl使用
//注意：  cin cout 超时问题

bool Comp(int a, int b){
    return strcmp(int2name[a],int2name[b]) < 0;
}
int main(){

    int n,k;
    scanf("%d %d",&n,&k);

    for(int i=1;i<=n;++i){
        int c,x;
        string s;
        cin>>s>>c;
        strcpy(int2name[i],s.c_str());
        //选的课程
        for(int j=1;j<=c;++j){
            scanf("%d",&x);
            course[x].push_back(i);
        }
    }
    for(int i=1;i<=k;++i){
        printf("%d %d\n",i,course[i].size());
        sort(course[i].begin(),course[i].end(),Comp);
        for(int j=0;j<course[i].size();++j)
            printf("%s\n",int2name[course[i][j]]);
            //cout<<int2name[course[i][j]]<<endl;
    }
    return 0;
}

```

错误分析：

1. 运行超时：36行，使用cout输出比printf慢了很多，因此在有超多输出的情况下，如题目中的最多40000*50000个输出，会导致超时。

# A1048 Find Coins(动态规划)

```cpp
/*
这种问题虽然不是动态规划，但可以用解决动态规划的dp法则去解决这类问题。比如要将一段输入排序为
num = 1 2 2 4 7 8 11 15。
dp[i]=j(j>=i),如果num[i]+num[j]==15，或者num[i]+num[j]<15的最大j，或者num[i]+num[j]>15的
最小的j
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n,m;
int num[MAXN];
int dp[MAXN]; //存储使num[i]+num[j]<=15的使j>=i的最小的j

int  main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    for(int i=0;i <n; i++){
        scanf("%d",&num[i]);
    }
    sort(num, num+n);

    int i = 0;
    for(i=0; i<n; i++){
        int j;
        if(i == 0){
            j = 1;
            while(j < n && num[i]+num[j]<m)
                j++;
            if(j == n){
                dp[i] = j - 1;
            }else if(num[i]+num[j] == m){
                dp[i] = j;
            }else{
                dp[i] = j - 1;
            }

        }else{
            j = dp[i-1];
            if(num[i]+num[j] < m){
                while(j < n && num[i]+num[j]<m)
                    j++;
                if(j == n){
                    dp[i] = j - 1;
                }else if(num[i]+num[j] == m){
                    dp[i] = j;
                }else{
                    dp[i] = j - 1;
                }
            }else if(num[i]+num[j] == m){
                dp[i] = j;
            }else{
                j = j - 1;
                while(j>=i && num[i]+num[j]>m)
                    j--;
                if(j < i){
                    dp[i] = i;
                }else{
                    dp[i] = j;
                }
            }
        }
        if(j > i && num[i] + num[dp[i]] == m){
            break;
        }
    }
    if(i < n-1){
        printf("%d %d",num[i],num[dp[i]]);
    }else{
        printf("No Solution");
    }
}
```

```cpp
/*
这种问题虽然不是动态规划，但可以用解决动态规划的dp法则去解决这类问题。比如要将一段输入排序为
num = 1 2 2 4 7 8 11 15。
dp[i]=j(j>=i),如果num[i]+num[j]==15，或者num[i]+num[j]<15的最大j，或者num[i]+num[j]>15的
最小的j
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n,m;
int num[MAXN];
int dp[MAXN]; //存储使num[i]+num[j]<=15的使j>=i的最小的j

int  main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    for(int i=0;i <n; i++){
        scanf("%d",&num[i]);
    }
    sort(num, num+n);

    int i = 0;
    for(i=0; i<n; i++){
        int j;
        if(i == 0){
            j = 1;
            while(j < n && num[i]+num[j]<m)
                j++;
            if(j == n){
                dp[i] = j - 1;
            }else if(num[i]+num[j] == m){
                dp[i] = j;
            }else{
                dp[i] = j - 1;
            }

        }else{
            j = dp[i-1];
            if(num[i]+num[j] < m){
                while(j < n && num[i]+num[j]<m)
                    j++;
                if(j == n){
                    dp[i] = j - 1;
                }else if(num[i]+num[j] == m){
                    dp[i] = j;
                }else{
                    dp[i] = j - 1;
                }
            }else if(num[i]+num[j] == m){
                dp[i] = j;
            }else{
                j = j - 1;
                while(j>=i && num[i]+num[j]>m)
                    j--;
                if(j < i){
                    dp[i] = i;
                }else{
                    dp[i] = j;
                }
            }
        }
        if(j > i && num[i] + num[dp[i]] == m){
            break;
        }
    }
    if(i < n-1){
        printf("%d %d",num[i],num[dp[i]]);
    }else{
        printf("No Solution");
    }
}
```

# A1049 Counting Ones(难)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<set>
using namespace std;

long long n;

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%lld",&n);

    long long res = 0;

    long long left,right,cur; //如86573的cur=5时，left=86, right=73
    long long a = 10;
    while(true){
        cur = (n%a)/(a/10);
        left = n/a;
        right = (n%a)%(a/10);

        if(cur == 0){
            res += left*(a/10);
        }else if(cur == 1){
            res += left*(a/10)+right+1;
        }else if(cur > 1){
            res += (left+1)*(a/10);
        }

        if(n/a == 0){ //对于n = 86576,使循环退出的a是100000,但如果n=1073741824(2^30)，则使得循环退出的a=10000000000,a超过了int的界限
            break;
        }else{
            a = a*10;
        }
    }

    printf("%lld",res);
}
```

错误分析：

1. 语法错误：int溢出, 29行

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
typedef long long lld;
lld num;
lld summ=0;
lld a[10];
lld tenfang[11];
void init(){
	for(int i=0;i<10;i++){
		a[i]=-1;
	}
	a[0]=0;
	for(int i=0;i<11;i++){
		if(i==0)tenfang[i]=1;
		else tenfang[i]=tenfang[i-1]*10;
	}
}
//计算left中1的个数，如，left=11，则返回2 
int getOneNum(lld left){ 
	int res=0;
	int b=1;
	do{
		b*=10;
		int c=(left%b)/(b/10);
		if(c==1)res++;
	}while(left/b>0);
	return res;
}
//计算长度为len的数（首部可以为0），有多少个1，如len=1，返回1，因为从0到9只有1这个数里面含有一个1 
lld computea(int len){
	if(len==0)return 0;
	if(a[len]!=-1)return a[len];
	a[len]=tenfang[len-1]+10*computea(len-1);
	return a[len];
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>num;
	if(num==0){
		cout<<0<<endl;
		return 0;
	}
	lld i=10,left,right,cur;
	int rightLen=0;  //对于56874，若cur=8,则rightlen=2,leftOneNum=0,rightNum=100; 
	int leftOneNum;
	do{
		left=num/i;
		cur=(num%i)/(i/10);
		right=num%(i/10);
		leftOneNum=getOneNum(left);
		if(right==0){
			if(cur>=1){
				summ+=leftOneNum*(cur+1)+1;
			}else{
				summ+=leftOneNum*(cur+1);
			}
		}else{
			if(cur>1){
				summ+=(leftOneNum*tenfang[rightLen]*cur+tenfang[rightLen]+computea(rightLen)*cur);
			}else{
				summ+=(leftOneNum*tenfang[rightLen]*cur+computea(rightLen)*cur);
			}
		}
		i=i*10;
		rightLen++;
	}while(left>0);
	cout<<summ<<endl;
}
```

27分

# A1050 String Subtraction

```cpp
/*
基本思路：输入str1, str2, 删除str1中在str2出现的字符。把str2中的字符插入到set中，把str1组成链表，
然后遍历链表，遇到一个字符，就检查是否在set中，时间复杂度为O(nlogn)。
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<set>
using namespace std;

struct Node{
    char c;
    Node* next;
};
const int MAXN = 1e4+5;
char str1[MAXN], str2[MAXN];
set<char> se; //保存str2中出现的字符

int main(){
    //freopen("D:\\input.txt","r",stdin);
    fgets(str1, MAXN, stdin);
    fgets(str2, MAXN, stdin);

    int i = 0;
    while(str2[i] != '\n' && str2[i] != '\0'){
        se.insert(str2[i]);
        i++;
    }

    Node* head = new Node;
    i = 0;
    Node* pre = head;
    set<char>::iterator it;
    while(str1[i] != '\n' && str1[i] != '\0'){
        it = se.find(str1[i]);
        if(it == se.end()){
            Node* node = new Node;
            node->c = str1[i];
            pre->next = node;
            pre = node;
        }
        i++;
    }
    pre->next = NULL;

    //输出
    Node* cur = head->next;
    while(cur != NULL){
        printf("%c",cur->c);
        cur = cur->next;
    }
}
```

# A1051 Pop Sequence

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int m,n,k;
vector<int> vec;
int input[1005]; 

int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>m>>n>>k;
	while(k--){
		vec.clear(); //error:
		for(int j=1;j<=n;j++)cin>>input[j];
		int i=1;
		for(int j=1;j<=n;j++){
			int a=input[j];
			if(a>n){
				cout<<"NO"<<endl;
				goto loop;
			}
			if(a>=i){
				for(int p=i;p<=a;p++)vec.push_back(p);
				if(vec.size()>m){
					cout<<"NO"<<endl;
					goto loop;
				}
				i=a+1;
				vec.pop_back();
			}else{
				if(vec.size()>0 && vec.back()==a){
					vec.pop_back();
				}else{
					cout<<"NO"<<endl;
					goto loop;
				}
			}
		}
		cout<<"YES"<<endl;
		loop:;
	}
}
```

错误分析：

1. 数据更新错误：14行，每次循环，vec都要清空
2. 如果不采用输入缓冲的形式，即一个输入一个处理，则注意有可能在退出的时候，输入缓冲区内有应该要接受却没有接受的数据。例如：1 2 3 4 5 6\n 1 2 3 4 5 6，如果处理到3了,continue，则下次接受的是4，但是本应该接受下一个1

# A1052 Linked List Sorting(排序)

静态链表排序。和链表排序不一样，排序过程中，只单纯地按照数据域排序结点，破坏了最初的结点下标与结点addr域的相等关系。

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;

struct Node{
    int addr,key,next;
    int flag; //false表示结点未被链表占用
};

const int MAXN = 1e5+5;
int n,begin1; //n个结点，首节点的位置
int addr,key,next1; //结点地址，数据，下一个地址
Node nodes[MAXN];

//比较规则：若有一个结点的flag为flase，则排在后面，否则，数据大的排在后面
bool cmp(Node a,Node b){
    if(a.flag==false || b.flag==false){
        return a.flag>b.flag;
    }else{
        return a.key<b.key;
    }
}
int main(){
    //初始化
    for(int i=0;i<MAXN;i++){
        nodes[i].flag = false;
    }
    
    scanf("%d %d",&n,&begin1);
    for(int i=0;i<n;i++){
        scanf("%d %d %d",&addr,&key,&next1);
        nodes[addr].addr = addr; //不要忘了
        nodes[addr].key = key;
        nodes[addr].next = next1;
        nodes[addr].flag = true; //对链表进行标记
    }
    sort(nodes,nodes+MAXN,cmp); //排序
    printf("%d %05d\n",n,nodes[0].addr);
    for(int i=0;i<n;i++){
        if(i!=n-1){
            printf("%05d %d %05d\n",nodes[i].addr,nodes[i].key,nodes[i].next);
        }else{
            printf("%05d %d -1\n",nodes[i].addr,nodes[i].key);
        }
    }
}
```

错误分析：

1. 我以为输入的n个结点就是链表，其实不是的，输入的n个节点的一部分是链表。错误体现在35行

```cpp
#include<cstdio>
#include<algorithm>
using namespace std;

struct Node{
    int addr,key,next;
    bool flag; //false表示结点未被链表占用
};

const int MAXN = 100005;
int n,head; //n个结点，首节点的位置
int addr,key,next_addr; //结点地址，数据，下一个地址
Node nodes[MAXN];
int cnt = 0; //链表中结点数量

//比较规则：若有一个结点的flag为flase，则排在后面，否则，数据大的排在后面
bool cmp(Node a,Node b){
    if(a.flag==false || b.flag==false){
        return a.flag>b.flag;
    }else{
        return a.key<b.key; //升序排列
    }
}

int main(){
    //初始化
    for(int i=0;i<MAXN;i++){
        nodes[i].flag = false;
    }
    
    scanf("%d%d",&n,&head);
    for(int i=0;i<n;i++){
        scanf("%d %d %d",&addr,&key,&next_addr);
        nodes[addr].addr = addr; //不要忘了
        nodes[addr].key = key;
        nodes[addr].next = next_addr;
    }
    //对链表进行标记,flag
    for(int p=head;p!=-1;p=nodes[p].next){
        nodes[p].flag = true;
        cnt++;
    }
    
    if(cnt == 0){ //考虑链表中没有结点的情况
        printf("0 -1");
    }else{
        sort(nodes,nodes+MAXN,cmp); //排序
    	printf("%d %05d\n",cnt,nodes[0].addr);
    	for(int i=0;i<cnt;i++){
    	    if(i!=cnt-1){
    	        printf("%05d %d %05d\n",nodes[i].addr,nodes[i].key,nodes[i+1].addr);
    	    }else{
    	        printf("%05d %d -1\n",nodes[i].addr,nodes[i].key); //最后一个结点的输出
    	    }
    	}
    }
    
    return 0;
}
```

错误分析：

1. 51行中：第三个数字不能输出nodes[i].next，因为排序后next域已经无效了，真正的next是nodes[i+1].addr。

# A1053 Path of Equal Weight(DFS)

```cpp
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXN = 105;

//静态树表示法
struct Node{
    int weight; //权重(数据域)
    vector<int> child; //孩子结点的编号
}nodes[MAXN];

int n,m,s; //结点数量，非叶子结点数量
int id,k,child; //父亲，孩子数量，孩子结点
int Path[MAXN]; //记录路径

void DFS(int index,int layer,int sum){ //index表示正在访问的结点的编号，layer是层数，sum是目前权值总和
    if(sum == s){ //权值总和等于给定值
        if(nodes[index].child.empty()){ //如果为叶子结点
            //输出路径
            printf("%d",Path[0]);
            for(int i=1;i<=layer;i++){
                printf(" %d",Path[i]);
            }
            printf("\n");
            return;
        }
    }
    if(sum > s){ //如果大于s
        return;
    }
    //如果小于或等于s
    for(int i=0;i<nodes[index].child.size();i++){
        int v = nodes[index].child.at(i); //儿子结点编号
        Path[layer+1] = nodes[v].weight; //保存路径
        DFS(v,layer+1,sum+nodes[v].weight);
    }
}

bool cmp(int a, int b){
    return nodes[a].weight > nodes[b].weight; //非升序排列
}

int main(){
    scanf("%d%d%d",&n,&m,&s);
    for(int i=0;i<n;i++){ //输入权重
        scanf("%d",&nodes[i].weight);
    }
    for(int i=0;i<m;i++){
    	scanf("%d%d",&id,&k);
    	for(int j=0;j<k;j++){
            scanf("%d",&child);
            nodes[id].child.push_back(child);
        }
        sort(nodes[id].child.begin(),nodes[id].child.end(),cmp); //排序，保证路径输出是非升的。
    }
    
    Path[0] = nodes[0].weight;
    DFS(0,0,nodes[0].weight);
}

```

错误分析：

1. 忘了写排序函数了。55行

# A1054 The Dominant Color

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int* cover;
int m,n;

int pow2(){
	int res=1;
	for(int i=0;i<24;i++)res*=2;
	return res;
}
void init(){
	int siz=pow2();
	cover=new int[siz];
	for(int i=0;i<siz;i++)cover[i]=0;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>m>>n;
	int mn=m*n,ans;
	for(int i=0;i<mn;i++){
		int a;
		cin>>a;
		cover[a]++;
		if(cover[a]>mn/2){
			ans=a;
			break;
		}
	}
	cout<<ans;
	delete[] cover;
	return 0;
}
```

错误，内存超限。10^8个int就已经有100MB了。

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

unordered_map<int,int> mp;
int m,n;

int main(){
	//freopen("D:\\input.txt","r",stdin);
    ios::sync_with_stdio(false);
	cin>>m>>n;
	int mn=m*n,ans;
	for(int i=0;i<mn;i++){
		int a;
		cin>>a;
		unordered_map<int,int>::iterator it;
		if((it=mp.find(a))!=mp.end()){
			it->second++;
			if(it->second>mn/2){
				ans=a;
				break;
			}
		}else{
			mp[a]=1;
		}
	}
	cout<<ans<<endl;
}
```

没有办法开出很大的空间，所以，只能用map了，但是又超时了。最后发现，是使用cin的原因，见11行。因为这题设计最多800*600=480000个输入，可能这个数值已经比较大了吧

# A1055 The World's Richest

```
基本思路：
数据：Record recs[];
1).排序recs，排序规则是<财富，姓名>
2).对于<min,max>,从头找在这个范围内的Record并输出，直到遍历完或者到达最大输出个数

改进：
由于每个query的输出个数最多是100，所以对于排序好的recs，每个年龄的人最多存储100个就好了，这样规模就从
100000变为200*100=20000了。
核心代码：
for(int i=0;i<n;i++){
    if(book[recs[i].age]<=100){
        recs2.push_back(recs[i]);
        book[recs[i].age]++;
    }
}
```

# A1056 Mice and Rice（难）

```
记录每个玩家在第几轮淘汰，lunNum，根据这个指标来排序
```

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;

struct Node{
    int id, w, lunNum; //lunNum是在第几轮被淘汰
    int rankNum;
};
const int MAXNP = 1005;
Node players[MAXNP];
int players2[MAXNP];
int np, ng;
vector<int> lun; //表示一轮的比赛人员
int lunNum = 0;

int findMax(int a, int b){ //返回最胖老鼠在lun中的下标
    int maxm = -1, m;
    for(int i=a; i<=b; i++){
        int index = lun[i];
        if(players[index].w > maxm){
            maxm = players[index].w;
            m = i;
        }
    }
    return m;
}
bool Comp(int a, int b){
    if(players[a].lunNum != players[b].lunNum){
        return players[a].lunNum > players[b].lunNum;
    }else{
        return players[a].id < players[b].id;
    }
}
int main(){
    //初始化
    for(int i=0; i<MAXNP; i++){
        players2[i] = i;
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&np,&ng);
    for(int i=0; i<np; i++){
        players[i].id = 0;
        scanf("%d",&players[i].w);
    }
    for(int i=0; i<np; i++){
        int a;
        scanf("%d",&a);
        lun.push_back(a);
    }

    lunNum = 0;
    while(lun.size() > 1){
        vector<int> tempLun; //暂存下一轮的人员
        int i = 0, j;
        while(i < lun.size()){
            j = i+ng-1 < lun.size() ? i+ng-1 : lun.size()-1;
            int m = findMax(i, j);
            tempLun.push_back(lun[m]);
            for(int k=i; k<=j; k++){
                int index = lun[k];
                players[index].lunNum = lunNum;
            }
            i = j + 1;
        }
        lun = tempLun;
        lunNum++;
    }
    players[lun.front()].lunNum = lunNum;

    sort(players2, players2+np, Comp);

    for(int i=0; i<np; i++){
        int index = players2[i];
        if(i == 0){
            players[index].rankNum = 1;
        }else{
            int lastIndex = players2[i-1];
            if(players[index].lunNum == players[lastIndex].lunNum){
                players[index].rankNum = players[lastIndex].rankNum;
            }else{
                players[index].rankNum = i+1;
            }
        }
    }
    for(int i=0; i<np; i++){
        if(i == 0){
            printf("%d",players[i].rankNum);
        }else{
            printf(" %d",players[i].rankNum);
        }
    }
}
```

# *A1057 Stack（树状数组，难）

```
正常入栈出栈，一旦要求mid时，就把栈复制到另一个栈，排序，输出，O(n^2logn)
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

vector<int> stk;
int n;

int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d\n",&n);
	char input[20];
	while(n--){
		fgets(input,20,stdin);
		input[strlen(input)-1]='\0';
		stringstream ss(input);
		string s1;
		ss>>s1;
		if(s1=="Pop"){
			if(!stk.empty()){
				printf("%d\n",stk.back());
				stk.pop_back();
			}
			else printf("Invalid\n");
		}else if(s1=="PeekMedian"){
			if(stk.size()==0){
				printf("Invalid\n");
			}else{
				vector<int> stk2=stk;
				sort(stk2.begin(),stk2.end());
				if(stk2.size()%2==0){
					printf("%d\n",stk2[stk2.size()/2-1]);
				}else{
					printf("%d\n",stk2[stk2.size()/2]);
				}
			}	
		}else{
			ss>>s1;
			int a=stoi(s1);
			stk.push_back(a);
		}
	}
} 
```

错误：超时。

```
基本思路2：
注意到题目中说操作的数的最大值为10^5,因此就开10^5的数组arr，每次操作入栈a,入栈a,arr[a]++;每次出栈，就
取栈顶元素a，出栈,arr[a]--；如果要求mid，就累加前k个值，使得结果>大于总值的一半，则对应的arr[k]就是中位数
O(n^2)
```

```
基本思路3: 基于基本思路2，采用树状数组存储累加和，使得查询前n项和的时间复杂度为O(logn),总的时间复杂度
为O(n(logn)^2)，若对于n=10^5,比思路2节省1000倍的时间
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int n;
stack<int> stk;
int Sum[MAXN]; //Sum[i]表示i这个数字及以前的数字入栈了多少次 
int arr[MAXN]; //arr[i]=2表示i这个数字入栈了2次 

int lowbit(int x){
	return x&(-x);
}
void init(){
	memset(Sum,0,sizeof(Sum));
	memset(arr,0,sizeof(arr));
}
void update(int index,int value){
	while(index<MAXN){ //error:while(index<=n)
		Sum[index]+=value;
		index+=lowbit(index);
	}
}
int getSum(int x){
	int s=0;
	while(x>=1){
		s+=Sum[x];
		x-=lowbit(x);
	}
	return s;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d\n",&n);
	char input[20];
	for(int i=1;i<=n;i++){ //error:while(n--)
		fgets(input,20,stdin);
		input[strlen(input)-1]='\0';
		stringstream ss(input);
		string s1;
		ss>>s1;
		if(s1=="Pop"){
			if(stk.size()>0){
				int a=stk.top();
				stk.pop();
				update(a,-1);
				arr[a]--; //error:arr[a]=0
				printf("%d\n",a);
			}else{
				printf("Invalid\n");
			}
		}else if(s1=="Push"){
			ss>>s1;
			int a=stoi(s1);
			stk.push(a);
			update(a,1);
			arr[a]++; //error:arr[a]=1
		}else{
			if(stk.size()==0){
				printf("Invalid\n");
				continue;
			}
			//二分法求middle
			int left=1,right=MAXN-1,mid; //error:right=n
			int b=(stk.size()%2==0?stk.size()/2:(stk.size()+1)/2);
			while(left<=right){
				mid=(left+right)/2;
				int a=getSum(mid);
				if(a>=b && (mid==1 || (mid>1 && getSum(mid-1)<b))){ //要考虑边界条件：mid==1
					break;
				}else if(a<b){
					left=mid+1;
				}else{
					right=mid-1;
				}
			} 
			//while(mid>=1 && arr[mid]==0)
			//	mid--;
			printf("%d\n",mid);
		}
	}
}
```

注意：

1. 49，59.同一个数字可能入栈多次，而不仅仅是一次
2. 71行，较难写的代码
3. 20，66.由于输入的数字的范围是[1,100000]，而不是[1,n]，所以更新和检索的范围是[1,100000]

# A1058 A+B in Hogward

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<string>
#include<vector>
using namespace std;

char time1[15], time2[15];

void getBit(char s[],int& a,int& b,int& c){
    int num = 0;
    int i = 0;
    int d = 0;
    while(s[i] != '\0'){
        if(s[i] == '.'){
            if(num == 0){
                a = d;
            }else if(num == 1){
                b = d;
            }
            num++;
            d = 0;
        }else{
            d = d*10 + s[i]-48;
        }
        i++;
    }
    c = d;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    /*scanf("%s %s",&time1,&time2);
    int a1,b1,c1,a2,b2,c2;
    getBit(time1,a1,b1,c1);
    getBit(time2,a2,b2,c2);*/
    int a1,b1,c1,a2,b2,c2;
    scanf("%d.%d.%d %d.%d.%d",&a1,&b1,&c1,&a2,&b2,&c2);

    int a,b,c;
    int d = 0;
    if(c1 + c2 >= 29){
        c = c1 + c2 - 29;
        d = 1;
    }else{
        c = c1 + c2;
    }
    if(b1 + b2 + d >= 17){
        b = b1 + b2 + d - 17;
        d = 1;
    }else{
        b = b1 + b2 + d;
        d = 0;
    }
    /*if(a1 + a2 + d > 1e7+1){
        a = a1 + a2 + d - (1e7+1);
        d = 1;
    }else{
        a = a1 + a2 + d;
        d = 0;
    }*/
    a = a1 + a2 + d;
    printf("%d.%d.%d",a,b,c);
}
```

# A1059 Prime Factors

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<cmath>
using namespace std;

int n;
struct Node{
    int prime;
    int cnt;
};
Node nodes[100];
int num = 0;

bool isPrime(int x){
    if(x <= 1){
        return false;
    }
    int sqrtx = sqrt(x);
    for(int i=2; i<=sqrtx; i++){
        if(x%i == 0)
            return false;
    }
    return true;
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    printf("%d=",n);
    if(n != 1){
        int sqrtn = sqrt(n);
        for(int i=2; i<=sqrtn; i++){
            if(isPrime(i) == true){
                if(n%i == 0){
                    int temp = num++;
                    nodes[temp].prime = i;
                    nodes[temp].cnt = 0;
                    while(n%i == 0){
                        nodes[temp].cnt++;
                        n = n/i;
                    }
                }
            }
            if(n == 1){
                break;
            }
        }
        if(n != 1){
            nodes[num].prime = n;
            nodes[num].cnt = 1;
            num++;
        }
        for(int i=0; i<num; i++){
            if(i != 0){
                printf("*");
            }
            printf("%d",nodes[i].prime);
            if(nodes[i].cnt > 1){
                printf("^%d",nodes[i].cnt);
            }
        }
    }else{
        printf("1");
    }
}
```