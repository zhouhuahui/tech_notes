# A1001 A+B Format

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int a,b;

int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>a>>b;
	int c=a+b,i;
	string s=to_string(c);
	i=0;
	if(s[i]=='-'){
		cout<<s[i++];
	}
	int i2=i+(s.size()-i)%3;
	while(i<i2){
		cout<<s[i];
		i++;
		if(i==i2&&i2<s.size())cout<<",";
	}
	//if(i<s.size())cout<<",";
	while(i<s.size()){
		int i2=i+3;
		for(;i<s.size()&&i<i2;i++){
			cout<<s[i];
		}
		if(i<s.size())cout<<",";
	}
}
//注意点：1. 对于-4999999 不要输出"-499,999,9"，而是，"-4,999,999"
//        2. 对于-999999，因为没有不取整的部分，即上面的4，就不要忘了别输出"-,999,999" 
```

# A1002 A+B for Polynomials

```cpp
#include<cstdio>
using namespace std;

const int MAXN = 1111;
double An[MAXN] = {}; //记录第i次的系数，初值为0
bool A[MAXN] = {false}; //记录第i次是否系数为0
int k;

int main(){
    int count = 0; //记录共有多少项
    int n; //记录指数
    double an; //记录系数
    
    for(int i=0;i<2;i++){
        scanf("%d ",&k);
        for(int j=0;j<k;j++){
            scanf("%d %lf",&n,&an);
            An[n] += an;
            if(A[n] == false){ //如果n次项本来不存在
                count++; //有项加进来，count加1
                A[n] = true;
            }else{ //如果n次项本身存在
                if(An[n] == 0){
                    count--; //有一项被抹去，count减1
                    A[n] = false;
				}
            }
        }
    }
    
    printf("%d",count);
    for(int i=MAXN-1;i>=0;i--){
        if(A[i] == true)
        	printf(" %d %.1f",i,An[i]);
    }
}
```

错误分析：

1. 24行的``count--`` 不要忘了

# A1003 Emergency(图论)

**dijkstra解法**

```cpp
#include<cstdio>
#include<cstring>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXV = 510;
const int INF = 1000000000;

struct Node {
	int v;
	int w;
};

int n, m, st, ed; //n是边的数量，m是边的数量，st是起点，sd是终点
vector<Node> G[MAXV]; //邻接表
int Weight[MAXV]; //权重表
int D[MAXV], W[MAXV], Num[MAXV]; //距离表；最大点权表；最短路径数量表
bool vis[MAXV] = { false };

void Dijkstra(int s) { //s为起点
	//初始化
	fill(D, D + MAXV, INF);
	D[s] = 0;
	memset(W, 0, sizeof(W));
	W[s] = Weight[s];
	memset(Num, 0, sizeof(Num));
	Num[s] = 1;

	for (int j = 0; j < n; j++) { //循环n次，可把n个点都变为已访问点
		int u = -1, min = INF; //u使D[u]最小，min存放最小值
		for (int i = 0; i < n; i++) {
			if (vis[i] == false && D[i] < min) { //如果未被确定最短路径且D值小于min值
				u = i; //更新u
				min = D[i]; //更新min
			}
		}
		if (u == -1) return;
		vis[u] = true;
		for (int i = 0; i < G[u].size(); i++) { //看看u的每一个邻居结点
			int v = G[u].at(i).v; //v是邻居点
			if (vis[v] == false && D[u] + G[u].at(i).w < D[v]) { //如果u的邻居结点未访问且可以使其D值更优
				D[v] = D[u] + G[u].at(i).w; //更新v的D值
				W[v] = W[u] + Weight[v]; //更新到v的权值之和
				Num[v] = Num[u]; //更新到v的最短路径数
			}else if (vis[v] == false && D[u] + G[u].at(i).w == D[v]) { //如果u的邻居结点未访问且找到到v的相同长度的路径    
				if (W[u] + Weight[v] > W[v]) {
					W[v] = W[u] + Weight[v];
				}
				Num[v] += Num[u]; //到v的路径之和等于所有路径中经过点u的路径数加上未经过点u的路径数
			}
		}
	}
}

int main() {
	scanf("%d%d%d%d", &n, &m, &st, &ed);
	for (int i = 0; i < n; ++i) {
		scanf("%d", &Weight[i]);
	}
    
	/*int u, v;
	Node node;
	for (int i = 0; i < m; ++i) {
		scanf("%d%d", &u, &v);
		node.v = v;
		scanf("%d", &(node.w));
		G[u].push_back(node);
	}*/
    int u,v,w;
    Node node1,node2;
    for(int i=0;i<m;i++){
        scanf("%d%d%d",&u,&v,&w);
        node1.v = u;
        node1.w = w;
        node2.v = v;
        node2.w = w;
        G[u].push_back(node2);
        G[v].push_back(node1);
    }
    
	Dijkstra(st);
	printf("%d %d\n", Num[ed], W[ed]);
    return 0;
}
```

错误分析：

1. if 和 else if有区别的。看47行
2. 在图论中，采用邻接表法时，无向图情况下，一定不要忘了对每一个输入的<u,v,w>，你要在顶点u和顶点v处添加结点，而不是在其中一个。看62行

**bellman-ford解法**

```cpp
#include<cstdio>
#include<set>
#include<vector>
#include<cstring>
#include<algorithm>
using namespace std;

struct Node{
    int v,dis;
};
const int MAXV = 510;
const int INF = 0x3fffffff;

int n, m, st, ed; //n是边的数量，m是边的数量，st是起点，sd是终点
vector<Node> Adj[MAXV]; //邻接表
int Weight[MAXV]; //权重表
int D[MAXV], W[MAXV], Num[MAXV]; //距离表；最大点权表；最短路径数量表
set<int> Pre[MAXV]; //存放前驱结点

bool Bellman(int s){
    //初始化
    fill(D,D+MAXV,INF);
    D[s] = 0;
    memset(Num,0,sizeof(Num));
    Num[s] = 1;
    memset(W,0,sizeof(W));
    W[s] = Weight[s];
    
    for(int i=0;i<n-1;i++){ //n-1次迭代
        for(int u=0;u<n;u++){ //遍历每一条单向边
            for(int j=0;j<Adj[u].size();j++){
                int v = Adj[u][j].v;
                int dis = Adj[u][j].dis;
                if(D[u]+dis < D[v]){ //如果以u为中介点可以使D[v]更小
                    D[v] = D[u]+dis;
                    W[v] = W[u]+Weight[v];
                    Num[v] = Num[u];
                    Pre[v].clear();
                    Pre[v].insert(u);
                }else if(D[u]+dis == D[v]){ //找到一条相同长度的路径
                    if(W[u]+Weight[v] > W[v]){ //以u为中介点使点权之和更大
                        W[v] = W[u]+Weight[v];
                    }
                    Num[v] = 0; //重新计算Num
                    Pre[v].insert(u);
                    set<int>::iterator it;
                    for(it=Pre[v].begin();it!=Pre[v].end();it++){
                        Num[v]+=Num[*it];
                    }
                }
            }
        } 
    }
    for(int u=0;u<n;u++){
        for(int j=0;j<Adj[u].size();j++){
            int v = Adj[u][j].v;
            int dis = Adj[u][j].dis;
            if(D[u]+dis < D[v]){
                return false;
            }
        }
    }
    return true;
    //return true;
}

int main(){
    scanf("%d%d%d%d", &n, &m, &st, &ed);
	for (int i = 0; i < n; ++i) {
		scanf("%d", &Weight[i]);
	}
    
    int u,v,w;
    Node node1,node2;
    for(int i=0;i<m;i++){
        scanf("%d%d%d",&u,&v,&w);
        node1.v = u;
        node1.dis = w;
        node2.v = v;
        node2.dis = w;
        Adj[u].push_back(node2);
        Adj[v].push_back(node1);
    }
    
    if(Bellman(st)){
        printf("%d %d\n", Num[ed], W[ed]);
    }
    return 0;
}
```

错误分析：

1. 25行，27行的初始化给忘了

**SPFA解法**

```cpp
#include<cstdio>
#include<set>
#include<vector>
#include<cstring>
#include<algorithm>
#include<queue>
using namespace std;

struct Node{
    int v,dis;
};
const int MAXV = 510;
const int INF = 0x3fffffff;

int n, m, st, ed; //n是边的数量，m是边的数量，st是起点，sd是终点
vector<Node> Adj[MAXV]; //邻接表
int Weight[MAXV]; //权重表
int D[MAXV], W[MAXV], Num[MAXV],inq_time[MAXV]; //距离表；最大点权表；最短路径数量表；入队次数
set<int> Pre[MAXV]; //存放前驱结点
bool inq[MAXV] = {false};

bool SPFA(int s){
    //初始化
    fill(D,D+MAXV,INF);
    D[s] = 0;
    memset(W,0,sizeof(W));
    W[s] = Weight[s];
    memset(Num,0,sizeof(Num));
    Num[s] = 1;
    memset(inq_time,0,sizeof(inq_time));
    
    queue<int> Q; //队列
 	Q.push(s); //s入队
    inq_time[s]++; //入队次数加1
    inq[s] = true; //设为已入队
    while(!Q.empty()){
        int u = Q.front(); //取队首元素
        Q.pop();
        inq[u] = false; //u设为未入队
        for(int i=0;i<Adj[u].size();i++){ //遍历u的邻接点
            int v = Adj[u][i].v;
            int dis = Adj[u][i].dis;
            if(D[u]+dis < D[v]){ //如果以u为中介点使D[v]更小
                D[v] = D[u]+dis;
                W[v] = W[u]+Weight[v];
                Num[v] = Num[u];
                Pre[v].clear();
                Pre[v].insert(u);
                //更新Q
                if(inq[v] == false){ //如果v未入对
                    Q.push(v);
                    inq[v] = true;
                    inq_time[v]++;
                    if(inq_time[v] >= n)
                        return false;
                }
            }else if(D[u]+dis == D[v]){ //找到相同路径
                if(W[u]+Weight[v] > W[v]) //如果以u未中介点使v的点权和较大
                	W[v] = W[u]+Weight[v];
                Num[v] = 0;
                Pre[v].insert(u);
                set<int>::iterator it;
                for(it=Pre[v].begin();it!=Pre[v].end();it++){
                    Num[v]+=Num[*it];
                }
                //更新Q
                if(inq[v] == false){ //如果v未入对
                    Q.push(v);
                    inq[v] = true;
                    inq_time[v]++;
                    if(inq_time[v] >= n)
                        return false;
                }
            }
        }
    }
    return true;
}

int main(){
    scanf("%d%d%d%d", &n, &m, &st, &ed);
	for (int i = 0; i < n; ++i) {
		scanf("%d", &Weight[i]);
	}
    
    int u,v,w;
    Node node1,node2;
    for(int i=0;i<m;i++){
        scanf("%d%d%d",&u,&v,&w);
        node1.v = u;
        node1.dis = w;
        node2.v = v;
        node2.dis = w;
        Adj[u].push_back(node2);
        Adj[v].push_back(node1);
    }
    
    if(SPFA(st)){
        printf("%d %d\n", Num[ed], W[ed]);
    }
    return 0;
}
```

# *A1004  Counting Leaves(DFS/BFS)

题意：在一个树中，找出每一层的叶子结点数量

```cpp
#include<cstdio>
#include<string>
#include<vector>
using namespace std;

struct Node{
    int id;
    vector<Node*> child;
    Node(int a){id = a;}
};

const int MAXLN = 105; //最大层数

int n,m; //表示树中结点的数量，非叶子结点的数量
int id,k,Id[MAXLN]; //保存父亲，父亲的儿子数量，父亲的儿子编号
int Num[MAXLN] = {}; //表示每一层的叶子结点数量
int layerNum = 0; //层数
Node* root; //根节点

void DFS(Node* root,int layer){ //深度优先遍历，记录每一层中叶子结点的数量。layer初始为0
    if(layer+1 > layerNum)
        layerNum = layer+1;
    if(root->child.empty()){ //如果结点的孩子只有0个
        Num[layer]++;
        return;
    }
    for(int i=0;i<root->child.size();i++){
        DFS(root->child.at(i),layer+1);
    }
}

void Insert(Node* root){ //在root中查找id,并把结点Id插入到id下面
    if(root->id == id){ //如果找到了这个id
        for(int i=0;i<k;++i){ //k个孩子插入到id下
            Node* node = new Node(Id[i]);
            root->child.push_back(node);
        }
        return;
    }
    for(int i=0;i<root->child.size();i++){
        Insert(root->child.at(i));
    }
}

int main(){
    scanf("%d%d",&n,&m);
    
    //初始化root，读入第二行数据（n,m下一行）
    scanf("%d%d",&id,&k); //读入父亲结点，儿子数量
    for(int i=0;i<k;i++){
        scanf("%d",&Id[i]); //读入多个儿子
    }
    root = new Node(id);
    Insert(root);
    
    for(int i=0;i<m-1;i++){
        scanf("%d%d",&id,&k); 
    	for(int j=0;j<k;j++){
    	    scanf("%d",&Id[j]);
    	}
        Insert(root);
    }
    
    DFS(root,0);
    
    printf("%d",Num[0]);
    for(int i=1;i<layerNum;++i){
        printf(" %d",Num[i]);
    }
}
```

错误分析：

1. 59行中Id写成Num了
2. 这个设计不太完美：我以为题目给的每一行（父亲，儿子），父亲都是已经结点创立好的，除了第一行的根节点，但有可能不是这样。所以：**用图的邻接表法较为合适** 

```cpp
#include<cstdio>
#include<vector>
using namespace std;

const int MAXLN = 105;
int n,m; //结点总数，非叶子结点总数
int id,k,child; //父亲，儿子总数，儿子结点
int root; //根节点
vector<int> G[MAXLN]; //邻接表
bool vis[MAXLN] = {false}; //是否被访问过
int Num[MAXLN] = {}; //每一层的叶子结点数
int layerNum = 0; //层数

void DFS(int s,int layer){ //从起始点s开始深度优先遍历，layer初始为0
    vis[s] = true; //将此结点设为已经访问
    if(G[s].empty()){ //若没有儿子，则更新每一层的叶子结点数
        Num[layer]++;
        if(layer+1 > layerNum) //更新层数
        	layerNum = layer+1;
        return;
    }
    for(int i=0;i<G[s].size();i++){
        int v = G[s].at(i);
        if(!vis[v]){
            DFS(v,layer+1);
        }
    }
}

int main(){
	scanf("%d%d",&n,&m);
    for(int i=0;i<m;i++){ //构建邻接表
        scanf("%d%d",&id,&k);
        if(i == 0)
            root = id;
        for(int j=0;j<k;j++){
            scanf("%d",&child);
            G[id].push_back(child);
        }
    }
    DFS(1,0);
    
    printf("%d",Num[0]);
    for(int i=1;i<layerNum;i++){
        printf(" %d",Num[i]);
    }
    
    return 0;
}
```

**图+DFS**

错误点：

1. 根节点是1，而不是第一行的父亲。因为题目中说：``For the sake of simplicity, let us fix the root ID to be `01`.``

# A1005 Spell It Right

```cpp
#include<cstdio>
#include<iostream>
#include<stack>
#include<map>
#include<string>
using namespace std;

string c; //保存输入的字符串
int sum = 0;
stack<int> s; //栈自底向上保存字符串中自右到左的信息

int main() {
	cin >> c;
	for (int i = 0; i < c.size(); i++) {
		sum += c.at(i) - 48;
	}

	while (sum != 0) {
		int a = sum % 10;
		sum = sum / 10;
		s.push(a);
	}

	map<int, string> m;
	m[0] = string("zero");
	m[1] = string("one");
	m[2] = string("two");
	m[3] = string("three");
	m[4] = string("four");
	m[5] = string("five");
	m[6] = string("six");
	m[7] = string("seven");
	m[8] = string("eight");
	m[9] = string("nine");
	
    if(!s.empty()){
		cout << m[s.top()];
        s.pop();
    }else{
        cout<<"zero";
    }
	while (!s.empty()) {
		cout << " " << m[s.top()];
		s.pop();
	}
	return 0;
}
```

错误分析：

1. 如果要一个一个读入字符，遇到回车就停止，则

    ```cpp
    while (cin >> c) {
    		sum += c - 48;
    }
    或
    cin >> c;
    while (c != '\n') {
    	sum += c - 48;
    	cin >> c;
    }
    ```

    的写法是错误的，因为**cin会自动丢弃缓冲区中使得输入结束的结束符(Enter、Space、Tab)**

2. ```cpp
    string str;
    scanf("%s",str)；
    //错误的写法，因为scanf不能访问string类型
    //可以这样写
    char str[105];
    scanf("%s",str);
    ```

3. 36行。以前没有考虑一种情况：输入数据是0，然后如果不判断堆栈是否为空，直接出栈，则会造成**段错误**

# A1006 Sign In and Sign Out(排序)

找最大值与最小值问题，采用迭代更新法，时间复杂度O(n)

```cpp
#include<cstdio>
#include<string>
#include<vector>
#include<iostream>
using namespace std;

int m; //m行记录
string unlockP,lockP; //开门人和锁门人
string p,time1,time2; //输入的记录

//根据字符串c模式，来分割字符串s，结果放在v中
void SplitString(const string& s,vector<string>& v,const string& c){
    string::size_type pos1,pos2;
    pos1 = 0;
    pos2 = s.find(c); //找到字符串c在s中的起始位置
    while(string::npos != pos2){ //如果存在c子串
        v.push_back(s.substr(pos1,pos2-pos1)); //分割
        pos1 = pos2+c.size();
        pos2 = s.find(c,pos1); //从pos1位置开始寻找c子串
    }
    if(pos1 != s.length()){ //pos1未到s的末尾(最后一个字符后面的位置)
        v.push_back(s.substr(pos1)); //pos1处及以后形成的子串
    }
}

bool Comp(string time1,string time2){ //若time1比time2早，则返回true
    vector<string> v1,v2;
    SplitString(time1,v1,string(":")); 
    SplitString(time2,v2,string(":"));
    //由于字符串可以直接比较大小，且结果与对应的数字比较大小结果相同
    /*for(int i=0;i<3;i++){
        if(v1[i].compare(v2[i]) == -1)
            return true;
        else if(v1[i].compare(v2[i]) == 1)
            return false;
    }*/
    for(int i=0;i<3;i++){
        if(v1[i] < v2[i])
            return true;
        else if(v1[i] > v2[i])
            return false;
    }
    
    int h1,h2,m1,m2,s1,s2;
    sscanf(v1[0].c_str(),"%d",&h1);
    sscanf(v1[1].c_str(),"%d",&m1);
    sscanf(v1[2].c_str(),"%d",&s1);
    sscanf(v2[0].c_str(),"%d",&h2);
    sscanf(v2[1].c_str(),"%d",&m2);
    sscanf(v2[2].c_str(),"%d",&s2);
    /*int t1,t2;
    t1 = h1*60*60+m1*60+s1;
    t2 = h2*60*60+m2*60+s2;
    if(t1<t2)
        return true;
    else
        return false;)*/
    if(h1<h2){
        return true;
    }
    else{
        return false;
    }
    if(m1<m2){
        return true;
    }
    else{
        return false;
    }
    if(s1<s2){
        return true;
    }
    else{
        return false;
    }
}

int main(){
    scanf("%d",&m);
    
    string earlyIn,lateOut; //最早到达时间，最迟离开时间
    for(int i=0;i<m;i++){
        cin>>p>>time1>>time2;
        if(i == 0){
            earlyIn = time1;
            lateOut = time2;
            unlockP = p;
            lockP = p;
        }else{
            if(Comp(time1,earlyIn)){ //比较
                earlyIn = time1;
                unlockP = p;
            }
            if(!Comp(time2,lateOut)){
                lateOut = time2;
                lockP = p;
            }
        }
    }
    cout<<unlockP<<" "<<lockP;
}
```

错误分析：

1. 31行到36行的代码可能会有问题。

本质问题就是找出序列中的最大值与最小值

```cpp
/*这种思路应该是最好的。用scanf("%s %d:%d:%d %d:%d:%d" 的形式得到2进制的h,m,s，然后可以方便地比较
*/
#include<cstdio>
#include<cstring>

int main(){
    int m;
    int earlyIn = 23*60*60+59*60+59,lateOut = 0; //最早进来的时间，最迟出去的时间
    char unlockP[20],lockP[20];
    
    scanf("%d",&m);
    for(int i=0;i<m;i++){
        char p[20];
        int h1,h2,m1,m2,s1,s2;
        scanf("%s %d:%d:%d %d:%d:%d",p,&h1,&m1,&s1,&h2,&m2,&s2);   
        int inTime,outTime;
        inTime = h1*60*60+m1*60+s1;
        outTime = h2*60*60+m2*60+s2;
        if(inTime < earlyIn){
            earlyIn = inTime;
            strcpy(unlockP,"");
            strcpy(unlockP,p);
        }
        if(outTime > lateOut){
            lateOut = outTime;
            strcpy(lockP,"");
            strcpy(lockP,p);
        }
    }
    printf("%s %s",unlockP,lockP);
}
```

# A1007 MSS(DP)

**动态规划**

最大子序列大小

Maximum Subsequence is the continuous subsequence.

如果暴力求解，时间复杂度很可能达到O(n^4)，但是用DP法，就只有线性复杂度

```cpp
/*
基本思路：找到一个最优解容易，但是找到最优解对应的决策序列不简单。对于MSS动态规划问题，状态转移方程是
dp[i]=max{dp[i-1]+seq[i], seq[i]}。若找到了dp[j]最大，则如何回溯找到前面的解序列呢：看dp[j-1]，
若dp[j-1]+seq[j]>=seq[j]说明j-1是在MSS序列中的，再看dp[j-2]+seq[j-1]>=seq[j-1]是否成立，以此类推
*/
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXK = 10005;
const int INF = 0x3fffffff;

int k; //k个数字
int seq[MAXK];
int dp[MAXK]; //dp[i]表示以第i个数字结尾的最大连续子序列和,dp[0]为0
bool flag = true; //判断是否全为负数

int main(){
    scanf("%d",&k);
    for(int i=1;i<=k;i++){
        scanf("%d",&seq[i]);
        if(seq[i]>=0)
            flag = false;
    }
    
    //初始化
    dp[0] = 0;
    
    if(flag == false){ 
    	//动态规划部分
    	for(int i=1;i<=k;i++){
    	    dp[i] = max(dp[i-1]+seq[i],seq[i]);//状态转移方程
    	}
    	
    	int lsum=-INF,first,last,j; //最优化值，连续子序列中的第一个数，最后一个数,最后一个数对应的下标   
    	for(int i=1;i<=k;i++){
    	    if(dp[i]>lsum){
    	        lsum = dp[i];
    	        j = i;
    	    }
    	}
    	last = seq[j];
    	//回溯，找到那个最大子序列(最大子序列不唯一，找到一个序列，使first和last的下标最小的那个)
    	/*int u = lsum;
    	while(u != seq[j]){
    	    u = u-seq[j];
    	    j--;
    	}
    	first = seq[j];*/
    	while(j>=1 && dp[j-1]>=0){ //若j-1下标处的值在最大子串中，则必然满足dp[j-1]+seq[j]>=seq[j]
    	    j--;
    	}
    	first = (j==0)?seq[1]:seq[j];
    	printf("%d %d %d",lsum,first,last);
    }else{ //如果输入全为负数
        printf("0 %d %d",seq[1],seq[k]);
    }
}
```

错误分析：

1. 17行的业务逻辑错了，验证一个数非负，应该用``if(seq[i]>=0)`` 
2. 39行到44行的代码。实现38行的业务逻辑失败，只能找到使first的下标最大的那个最大子串。

# A1008 Elevator

```cpp
//freopen("r","D:\\input.txt",stdin);
#include<bits/stdc++.h>
using namespace std;

int n;
 
int main(int argc, char** argv) {
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	int cur=0,next,ans=0;
	for(int i=0;i<n;i++){
		cin>>next;
		if(next>cur){
			ans+=(next-cur)*6+5;
		}else{
			ans+=(cur-next)*4+5;
		}
		cur=next;
	}
	cout<<ans;
}
```

# A1009 product of polynomials

```cpp
/*直接设立两个大小为1001的数组，然后两个数组进行全连接相乘
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1000

double poly1[MAXN+1],poly2[MAXN+1],ans[2*MAXN+1];

void init(){
	for(int i=0;i<=MAXN;i++){
		poly1[i]=0.0;
		poly2[i]=0.0;
	}
	for(int i=0;i<=2*MAXN;i++){
		ans[i]=0.0;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	for(int i=0;i<2;i++){
		int k,exp;
		double coef;
		cin>>k;
		while(k--){
			cin>>exp>>coef;
			if(i==0)poly1[exp]=coef;
			else poly2[exp]=coef;
		}
	}
	//计算 
	for(int i=0;i<=MAXN;i++){
		for(int j=0;j<=MAXN;j++){
			ans[i+j]+=poly1[i]*poly2[j];
		}
	}
	//计算结果有多少项 
	int num=0;
	for(int i=0;i<=2*MAXN;i++){
		if(ans[i]!=0.0)num++;
	}
	//输出 
	cout<<num;
	for(int i=2*MAXN;i>=0;i--){
		if(ans[i]!=0.0){
			printf(" %d %.1f",i,ans[i]);
		}
	}
}
```

# *A1010 Radix

```cpp
/*吉本思想：用long long 存储计算的值，来进行比较
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

string num1,num2;
int tag,radix;

long long compute(string num,int rad){
	long long res=0;
	for(int i=0;i<num.size();i++){
		int a=(num[i]<='9'?num[i]-'0':num[i]-'a'+10);
		res=res*rad+a;
	}
	return res;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>num1>>num2>>tag>>radix;
	if(tag!=1){
		swap(num1,num2);
	}
	long long value=compute(num1,radix);
	//计算未知radix的数的最小radix
	int radixMin=2;
	for(int i=0;i<num2.size();i++){
		int a=(num2[i]<='9'?num2[i]-'0':num2[i]-'a'+10);
		if(a+1>radixMin){
			radixMin=a+1;
		}
	}
	//找到radix 
	long long value2;
	if(!(num2.size()==1 && num2[0]=='1')){
		while((value2=compute(num2,radixMin))<value){ //error: 当num2=1时，value>=2时，不会结束循环的 
			radixMin++;
		}
		if(value2==value){
			cout<<radixMin;
		}else{
			cout<<"Impossible";
		}
	}else{
		if(value==1){
			cout<<"2";
		}else{
			cout<<"Impossible";
		}
	}
} 
```

有一个case超时了，24分。

# A1011 World Cup Betting

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

double odds[3][3];
int bets[3];

int main(){
	//freopen("D:\\input.txt","r",stdin);
	for(int i=0;i<3;i++){
		int a;
		for(int j=0;j<3;j++){
			cin>>odds[i][j];
			if(j==0)a=j;
			else if(odds[i][j]>odds[i][a])a=j;
		}
		bets[i]=a;
	}
	for(int i=0;i<3;i++){
		cout<<(bets[i]==0?"W":bets[i]==1?"T":"L")<<" ";
	}
	printf("%.2f",(odds[0][bets[0]]*odds[1][bets[1]]*odds[2][bets[2]]*0.65-1)*2);
}
```

# A1012 The Best Rank(排序)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

struct Stud{
	string id;
	vector<pair<int,int>> ranks; //pair<tag,rank>
	vector<double> grades;//0:A 1:C 2:M 3:E
	Stud(){
		ranks.resize(4);
		grades.resize(4);
	}
	Stud(string&_id,double _a,double _c,double _m,double _e){
		ranks.resize(4); //error: 如果结构体中有vector，构造函数种一定要resize 
		grades.resize(4);
		id=_id;grades[0]=_a;grades[1]=_c;grades[2]=_m;grades[3]=_e;
	}
};
vector<Stud> studs; //按照id从小到大排序
vector<int> studs2; //studs[i]表示studs[i]号的学生的排名为i
int n,m;

void init(){
	studs.resize(n);
	studs2.resize(n);
	for(int i=0;i<n;i++){
		studs[i]=Stud();
		studs2[i]=i;
	}
}
bool cmpId(const Stud& s1,const Stud& s2){
	return s1.id<s2.id;
}
bool cmpA(int s1,int s2){
	return studs[s1].grades[0]>studs[s2].grades[0]; //error: <
}
bool cmpC(int s1,int s2){
	return studs[s1].grades[1]>studs[s2].grades[1];
}
bool cmpM(int s1,int s2){
	return studs[s1].grades[2]>studs[s2].grades[2];
}
bool cmpE(int s1,int s2){
	return studs[s1].grades[3]>studs[s2].grades[3];
}
bool cmpRank(pair<int,int>p1, pair<int,int>p2){
	return (p1.second!=p2.second)?p1.second<p2.second:p1.first<p2.first;
}
int Search(string id){
	int low=0,high=n-1,mid;
	while(high>=low){
		mid=(low+high)/2;
		if(studs[mid].id==id)return mid;
		else if(studs[mid].id>id){
			high=mid-1;
		}else{
			low=mid+1;
		}
	}
	return -1;
} 
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n>>m;
	init();
	string id;
	double ag,cg,mg,eg;
	for(int i=0;i<n;i++){
		cin>>id>>cg>>mg>>eg;
		ag=(cg+mg+eg)/3.0;
		studs[i]=Stud(id,ag,cg,mg,eg);
	}
	//按照id排序
	sort(studs.begin(),studs.end(),cmpId);
	//按照成绩排序
	for(int i=0;i<4;i++){
		sort(studs2.begin(),studs2.end(),(i==0?cmpA:i==1?cmpC:i==2?cmpM:cmpE));
		//将rank结果记录下来 
		for(int j=0;j<n;j++){
			int index=studs2[j];
			if(j==0){
				studs[index].ranks[i]={i,j+1};
				//cout<<studs[index].id<<":ranks["<<i<<"]="<<j+1<<endl;///////
			}else{
				double lastGrade=studs[studs2[j-1]].grades[i];
				double curGrade=studs[index].grades[i];
				studs[studs2[j]].ranks[i]=(lastGrade==curGrade ? studs[studs2[j-1]].ranks[i] : pair<int,int>(i,j+1));
				//cout<<studs[index].id<<":ranks["<<i<<"]="<<studs[studs2[j]].ranks[i].second<<endl;///////
			}
		}
		//cout<<"----------"<<endl;//////
	}
	//查询
	while(m--){
		cin>>id;
		int index;
		if((index=Search(id)) != -1){
			sort(studs[index].ranks.begin(),studs[index].ranks.end(),cmpRank);
			int tag=studs[index].ranks[0].first;
			cout<<studs[index].ranks[0].second<<" "<<(tag==0?"A":tag==1?"C":tag==2?"M":"E")<<endl;
		}else{
			cout<<"N/A"<<endl;
		}
	} 
} 
```

错误分析：

1. 只有14行和35行两个错误，且都是细节错误，但是花了20分钟调试出来

# A1013 Battle over Cities(并查集+连通分量)

```cpp
/*
基本思路：把所有边放到一个集合中，然后用并查集来找图的联通块个数
*/
#include<cstdio>
#include<set>
#include<vector>
using namespace std;

const int MAXN = 1005;

struct edge{
    int v1;
    int v2;
    edge(int a,int b){v1=a;v2=b;}
};
int n,m,k; //城市的个数，边的个数，攻占城市的个数
int father[MAXN];
vector<edge> ve; //边的集合
set<int> f; //存储并查集的根节点

int findFather(int x){
    if(x==father[x])
        return x;
    int f = findFather(father[x]);
    father[x] = f;
    return f;
}
void unionTwo(int a,int b){
    int af = findFather(a);
    int bf = findFather(b);
    if(af != bf)
    	father[bf] = af;
}

int main(){
    scanf("%d%d%d",&n,&m,&k);
    for(int i=0;i<m;i++){
        int v1,v2;
        scanf("%d%d",&v1,&v2);
        ve.push_back(edge(v1,v2));
    }
    
    for(int i=0;i<k;i++){
        //初始化
        for(int j=1;j<=n;j++)
            father[j] = j;
        f.clear();
        
        int lost_city;
        scanf("%d",&lost_city); //输入攻占的城市
        vector<edge>::iterator it;
        for(it=ve.begin();it!=ve.end();it++){
            edge e = (*it);
            if(e.v1 != lost_city && e.v2 != lost_city){
                unionTwo(e.v1,e.v2);
            }
        }
        for(int j=1;j<=n;j++){
            if(j != lost_city)
            	f.insert(findFather(j));
        }
        printf("%d\n",f.size()-1);
    }
}
```

错误分析：

1. 31行。再union之前，先看看af和bf是否相等。

2. 60行。向set中插入某个结点的根节点，而不是它的父亲

    ```cpp
    f.insert(father[j]); //错误的写法
    ```


# A1014 Waiting in Line（难）

```cpp
/*
基本思想：不断向前推移time，看在合法的time时，有哪些客户可以接受服务，即在合法的time时，计算每个
窗口第一个客户的业务结束时间，那些没有计算结束时间的客户就是当天不可完成的
*/
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<vector>
using namespace std;

const int MAXN = 25;
const int MAXM = 15;
const int MAXK = 1005;
const int INF = 0x3fffffff;
int n,m,k,q; //窗口数，每个窗口最大排队人数，顾客数，要问的顾客数
int Line[MAXN][MAXM]; //Line[i][j]表示第i个窗口的第j个顾客；Line[i][0]表示第i个窗口的人数；初始为0      
//Line可以用queue的数组代替

struct Node{
    char state[12];//表示状态，"wait","stand","finished"
    int leftTime; //这个顾客的剩余时间，初始为业务办理时间
    int finishedTime; //业务完成时间,如,8:05为8*60+5,初始为0
};
Node cust[MAXK]; //每个顾客的信息；从1开始
int Q[MAXK]; //从1开始
int time = 8*60; //初始时间为8:00
int stands = 0; //已经进来stand过的人数

bool haveCust(){ //是否有顾客未完成业务了
    for(int i=1;i<=n;i++)
        if(Line[i][0] != 0)
            return true;
    return false;
}
int Min(){ //寻找正在服务的顾客中剩余时间最少的那个顾客的剩余时间（返回编号最小的）
    int min = INF;
    for(int i=1;i<=n;i++){
        if(Line[i][1]!=0 && cust[Line[i][1]].leftTime<min){
            min = cust[Line[i][1]].leftTime;
        }
    }
    return min;
}
int main(){
    scanf("%d%d%d%d",&n,&m,&k,&q);
    for(int i=1;i<=k;i++){
        scanf("%d",&cust[i].leftTime);
        strcpy(cust[i].state,"wait"); //每个顾客初始状态为wait
   		cust[i].finishedTime = 0; //为0表示当天业务无法完成
    }
    for(int i=1;i<=q;i++){
        scanf("%d",&Q[i]);
    }
    
    //初始化
    fill(Line[0],Line[0]+MAXN*MAXM,0);
    /*for(int i=1;i<=k;i++){
        if(i <= n*m){
            int a = (i-1)/m+1; //第几个窗口
            int b = (i-1)%m+1; //排在第几个位置
            Line[a][b] = i; //第a个窗口的第b个位置为顾客i
            Line[a][0]++; //第a个窗口的人数加1
            strcpy(cust[i].state,"stand");
       		stands++; 
        }
    }*/
    for(int i=1;i<=k;i++){
        if(i <= n*m){
            int a = (i-1)/n+1; //排在第几个位置
            int b = (i-1)%n+1; //排在第几个窗口
            Line[b][a] = i; //第b个窗口的第a个位置为顾客i
            Line[b][0]++; //第a个窗口的人数加1
            strcpy(cust[i].state,"stand");
       		stands++; 
        }
    }
    
    while(haveCust()){ //如果还有顾客未完成业务（前提是未到17:00）
        int min = Min(); //寻找正在服务的顾客中剩余时间最少的那个顾客的剩余时间（返回编号最小的）
        if(time>=17*60)
            break;
        time += min; //更新系统时间
        for(int i=1;i<=n;i++){
            int a = Line[i][1];
            if(a!=0 && (cust[a].leftTime-=min)==0){ //如果业务完成了
                //把这个窗口的其他顾客移动到前面
                for(int j=2;j<=Line[i][0];j++)
                	Line[i][j-1] = Line[i][j];
                Line[i][Line[i][0]] = 0;
                Line[i][0]--; //顾客数减1
                strcpy(cust[a].state,"finished");
                cust[a].finishedTime = time;
                //看有没有正在wait的顾客，让他进来stand
               	if(stands < k){
               	    Line[i][++Line[i][0]]=++stands;
               	    strcpy(cust[stands].state,"stand");
               	}
            }
        }
    }
    
    for(int i=1;i<=q;i++){
        int t = cust[Q[i]].finishedTime;
        if(t!=0)
        	printf("%02d:%02d\n",t/60,t%60); //格式化输出
        else
            printf("Sorry\n");
    }
}
```

设计了20分钟，写代码写了60分钟，我吐了。有两个测试点没过

错误分析：

1. 82行，要访问的是第a个客户，而不是第i个，所以应该是cust[a]
2. 52-61行。业务逻辑写错了，初始时顾客一个一个排在第1个位置的各个窗口，第2个位置的各个窗口，但是我写反了
3. 81行。对``Line[i][1]``进行操作时，要判断``Line[i][1] > 0``是否成立，也就是，第i个window是否有人
4. 75行；业务逻辑搞错了，是每个顾客的开始服务时间在17:00之前都可以在当天完成业务，而不是服务完成时间

```
基本思路：
当前时间:time
1) if(time<17:00)进入窗口等待队列知道窗口队列满和人已经入完
2) 找到所有窗口中最早有人办完交易的，time更新到那个时候，并把该窗口的客户pop出来，更新该顾客的finishTime;
   如果没有人办完交易（所有窗口没有人），则goto 4)
3) 回到1)
4) 输出
优化的基本思路：
若所有窗口有200人的空间，总共有1000人，则可以把前面200人直接填满窗口队列。
每个顾客在入队列的时候就已经确定了finishTime,所以在1)步没有人入队时即可退出循环。
```

```cpp
/*
基本思想：
*/
#include<cstdio>
#include<queue>
using namespace std;

struct Window{
    int poptime,endtime;
    queue<int> Q; //保存排队队列
    Window(){poptime=endtime=0;}
};

const int MAXN = 25;
const int MAXM = 15;
const int MAXK = 1005;
const int INF = 0x3fffffff;

int n,m,k,q,index=1;
Window WS[MAXN]={Window()};
int Endtime[MAXK]={0};
int Durtime[MAXK];
int Query[MAXK];

int main(){
    //输入
    scanf("%d%d%d%d",&n,&m,&k,&q);
    for(int i=1;i<=k;i++){
        scanf("%d",&Durtime[i]);
    }
    for(int i=1;i<=q;i++){
        scanf("%d",&Query[i]);
    }
    
    //初始化
    for(int i=1;i<=m;i++){
        for(int j=1;j<=n;j++){
            if(index<=k){
                WS[j].Q.push(index);
                if(WS[j].endtime<540)
                    Endtime[index]=WS[j].endtime+Durtime[index];
                WS[j].endtime += Durtime[index];
                if(i==1) 
                    WS[j].poptime+=Durtime[index];
                index++;
            }
        }
    }
    //考虑剩下的客户
    for(;index<=k;index++){
        int minwin,minpop=INF;
        for(int i=1;i<=n;i++){
            if(WS[i].poptime < minpop){
                minpop = WS[i].poptime;
                minwin = i;
            }
        }
        if(WS[minwin].endtime < 540) 
            Endtime[index] = WS[minwin].endtime+Durtime[index];
        WS[minwin].Q.pop();
        WS[minwin].Q.push(index);
        WS[minwin].endtime += Durtime[index];
        WS[minwin].poptime += Durtime[WS[minwin].Q.front()];
    }
    //输出
    for(int i=1;i<=q;i++){
        int t = Endtime[Query[i]];
        if(t!=0)
        	printf("%02d:%02d\n",(t+480)/60,(t+480)%60); //格式化输出
        else
            printf("Sorry\n");
    }
}
```

错误分析：

1. 44行的循环计数忘更新了
2. 41行。注意下标不要用错了

```cpp
/*
基本思路：每次把最先pop的窗口放进队列中，然后把要进入黄线以内的客户放入另一个队列，然后处理这两个
队列，并更新相应的数据。
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<queue>
using namespace std;

struct Window{
    queue<int> q;
    int popTime;
    int endTime;
    Window(){popTime = endTime = 8*60;}
};
struct Customer{
    int finishTime;
    int pTime;
    Customer(){finishTime = pTime = -1;}
};
const int MAXN = 25; //1-n
const int MAXK = 1005;
const int INF = 1e8+5;

int n,m,k,q;
Window windows[MAXN];
Customer customers[MAXK];
vector<int> vacancy;
queue<int> custs; //顾客在黄线外的等待队列
int finalTime = 17*60;

int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        windows[i] = Window();
    }
    for(int i=0; i<MAXK; i++){
        customers[i] = Customer();
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d %d",&n,&m,&k,&q);
    for(int i=1; i<=k; i++){
        scanf("%d",&customers[i].pTime); //没有加&
        custs.push(i);
    }
    for(int i=1; i<=m; i++){
        for(int j=1; j<=n; j++){
            if(!custs.empty()){
                int a = custs.front(); custs.pop();
                windows[j].q.push(a);
                int temp = windows[j].endTime;
                windows[j].endTime += customers[a].pTime;
                if(i == 1){
                    windows[j].popTime += customers[a].pTime;
                }
                if(temp < finalTime){
                    customers[a].finishTime = windows[j].endTime;
                }
            }
        }
    }

    while(true){
        int minpop = INF; vector<int> temp;
        for(int i=1; i<=n; i++){
            if(!windows[i].q.empty()){
                if(windows[i].popTime < minpop){
                    minpop = windows[i].popTime;
                    temp.clear();
                    temp.push_back(i);
                }else if(windows[i].popTime == minpop){
                    temp.push_back(i);
                }
            }
        }
        if(minpop >= finalTime) break; //

        vacancy = temp;
        for(int i=0; i<temp.size(); i++){ //更新windows
            int index = temp[i];
            windows[index].q.pop();
            if(!windows[index].q.empty()){
                windows[index].popTime += customers[windows[index].q.front()].pTime;
            }
        }

        while(!vacancy.empty() && !custs.empty()){
            int vindex = vacancy.front(); vacancy.erase(vacancy.begin());
            int cindex = custs.front(); custs.pop();
            int temp = windows[vindex].endTime;
            windows[vindex].endTime += customers[cindex].pTime;
            if(windows[vindex].q.empty()){ //
                windows[vindex].popTime += customers[cindex].pTime;
            }
            windows[vindex].q.push(cindex);
            if(temp < finalTime){
                customers[cindex].finishTime = windows[vindex].endTime;
            }
        }
    }

    for(int i=0; i<q; i++){
        int query;
        scanf("%d",&query);
        if(customers[query].finishTime != -1){
            int hh,mm;
            hh = customers[query].finishTime/60;
            mm = customers[query].finishTime%60;
            printf("%02d:%02d\n",hh,mm);
        }else{
            printf("Sorry\n");
        }
    }
}
```

我发现若是思路和设计正确，则肯定可以做对，除非有一些细节错误。

# A1015 Reversible Primes(质数)

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

bool isPrime(int x){
	int sqrtx=sqrt(x);
	if(x==1)return false;
	for(int i=2;i<=sqrtx;i++){
		if(x%i==0)return false;
	}
	return true;
}
//如果是可以处理的数据，则返回true 
bool dealLine(){
	int num,radix;
	cin>>num;
	if(num<0)return false;
	cin>>radix;
	if(!isPrime(num)){
		cout<<"No"<<endl;
		return true;
	}
	vector<int> num2; //保存num转换为radix进制后的反转的数 
	while(num!=0){
		num2.push_back(num%radix);
		num=num/radix;
	}
	//计算num2的值
	int value=0;
	for(int i=0;i<num2.size();i++){
		value=value*radix+num2[i];
	} 
	if(isPrime(value)){
		cout<<"Yes"<<endl;
		return true;
	}else{
		cout<<"No"<<endl;
		return true;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	while(dealLine());
}
```

# A1016 Phone Bills

```cpp
#include<cstdio>
#include<cstring>
#include<algorithm>
#include<queue>
#include<stack>
using namespace std;

const int MAXN = 1005;

int rate[24] = {0}; //一天中各个小时段的收费率
struct Record{
    char name[30];
    int time[4]; //月，日，时，分
    bool isOnline; 
    Record(){time[0]=-1;}; //默认time[0]=-1
};
int n = 0; //记录的数量
Record Records[MAXN] = {Record()}; //记录的集合

bool Comp(Record r1,Record r2){
    if(strcmp(r1.name,r2.name)==0){
        for(int i=0;i<4;i++){
        	if(r1.time[i] != r2.time[i]){
            	return r1.time[i]<r2.time[i];
        	}
        }
    }else{
        return strcmp(r1.name,r2.name)<0;
    }
}

//堆排序中的UpAdjust
void UpAdjust(int low,int high){ //low一般为1，high最小可为1
    int j=high,i=j/2;
    while(i!=0){ //i=0意味着已经结束
        if(Comp(Records[i],Records[j])==true){ //如果树下面的大于上面的，则交换
            Record temp=Records[i];
            Records[i] = Records[j];
            Records[j] = temp;
            j = i;
			i = j / 2;
        }else{
            break;
        }
    }
}
void DownAdjust(int low, int high){
    int i=low,j=i*2;
    while(j<=high){
        int m;
        if(j+1>high)
            m = j;
        else{
            if(Comp(Records[j],Records[j+1])==true)
                m = j+1;
            else
                m = j;
        }
        if(Comp(Records[m],Records[i])==false){
            Record temp = Records[m];
            Records[m] = Records[i];
            Records[i] = temp;
        }else{
            break;
        }
        i = m;
        j = m*2;
    }
}
void HeapSort(int low,int high){
    int i=low,j=high;
    while(i!=j){
    	Record temp = Records[j];
    	Records[j] = Records[i];
    	Records[i] = temp;
        
        DownAdjust(i,--j);
    }
}

int ComputeCost(int a[],int b[]){ //计算这个通话过程的电话费,单位是美分
    int ret = 0;
    int start[4],end[4];
    for(int i=0;i<4;i++){ //复制
        start[i] = a[i];
        end[i] = b[i];
    }
    
    while(!(start[1]==end[1] && start[2]==end[2] && start[3]==end[3])){ //如果没有计算完花费
        if(start[1]!=end[1] || start[2]!=end[2]){ //例如：02:23:56--03:00:14
            ret += (60-start[3])*rate[start[2]];
            start[3] = 0;
            if((start[2]+=1)==24){
                start[2]=0;
                start[1] += 1;
            }
        }else{ //02:14:01--02:14:25
            ret += (end[3]-start[3])*rate[start[2]];
            start[3]=end[3];
        }
    }
    return ret;
}

int ComputeMin(int a[],int b[]){
    int t1 = a[1]*24*60+a[2]*60+a[3];
    int t2 = b[1]*24*60+b[2]*60+b[3];
    return t2-t1;
}

void Bill(){ //遍历已经排序好的Records，计算每个人这个月的账单
    char name0[30]; //保存上一个记录的名字
    int totalCost = 0;
    queue<int> recPair; //保存合法的记录对
    stack<int> s;
    strcpy(name0,Records[1].name);
    int i = 1;
    while(i<=n+1){ //要在n+1的时候，输出保存的上一个顾客的记录对
        if(i==n+1 || strcmp(name0,Records[i].name)!=0){ //这个时候相当于处理完上一个顾客的记录
            //输出
            if(!recPair.empty()){
                int a = recPair.front();
                printf("%s %02d\n",Records[a].name,Records[a].time[0]);
                while(!recPair.empty()){
                	int a=recPair.front();recPair.pop();
                	int b=recPair.front();recPair.pop();
                	int cost = ComputeCost(Records[a].time,Records[b].time);
                	totalCost += cost;
                	printf("%02d:%02d:%02d %02d:%02d:%02d %d $%.2f\n",Records[a].time[1],Records[a].time[2],Records[a].time[3],Records[b].time[1],Records[b].time[2],Records[b].time[3],ComputeMin(Records[a].time,Records[b].time),(double)cost/100); 
            	}
                printf("Total amount: $%.2f\n",(double)totalCost/100);
            }
            
            if(i==n+1)return;
            strcpy(name0,Records[i].name);
            totalCost = 0;
            while(!recPair.empty())recPair.pop();
            while(!s.empty())s.pop(); //清空栈
        }
        if(Records[i].isOnline==true){ //如果是on-line
            while(!s.empty())s.pop(); //清空栈
            s.push(i);
        }else{
            if(!s.empty()){ //如果栈非空，说明有了合法的记录对
                recPair.push(s.top());s.pop();
                recPair.push(i);
            }
        }
        i++;
    }
}
int main(){
    for(int i=0;i<24;i++)
        scanf("%d",&rate[i]);
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%s",&Records[i].name);
        scanf("%d:%d:%d:%d",&Records[i].time[0],&Records[i].time[1],&Records[i].time[2],&Records[i].time[3]); 
        char flag[10];
        scanf("%s",&flag);
        if(strcmp(flag,"on-line")==0)
            Records[i].isOnline=true;
        else
            Records[i].isOnline=false;
        //堆插入
        UpAdjust(1,i);
    }
    HeapSort(1,n);
    //sort(Records+1,Records+n+1,Comp);
    
    //遍历已经排序好的Records，计算每个人这个月的账单
    char name[30];
    int totalCost = 0;
    bool headTime = false; //true,表示这个人是否输出过头
    bool last = false; //是否到这个顾客的最后一条记录
    strcpy(name,Records[1].name);
    for(int i=1;i<=n;i++){ //要考虑到两点：什么时候输出头（name month），什么时候输出尾（total amount）
        if(i==n || strcmp(name,Records[i+1].name) != 0){
            last = true;
        }
        
        if(headTime==false && last==false && Records[i].isOnline==true && Records[i+1].isOnline==false){ //如果是第一次，则要输出姓名，月份
            //如果还没有输出头部，且到了可以输出头部的时候
            printf("%s %02d\n",name,Records[i].time[0]);
            headTime = true;
        }
        if(last==false && Records[i].isOnline==true && Records[i+1].isOnline==false){ //遇到有效的通话记录
            int cost = ComputeCost(Records[i].time,Records[i+1].time);
            printf("%02d:%02d:%02d %02d:%02d:%02d %d $%.2f\n",Records[i].time[1],Records[i].time[2],Records[i].time[3],Records[i+1].time[1],Records[i+1].time[2],Records[i+1].time[3],ComputeMin(Records[i].time,Records[i+1].time),(double)cost/100);     
        	totalCost += cost;
        }
        if(headTime==true && last){ //如果输出过头部，且到了末尾
            printf("Total amount: $%.2f\n",(double)totalCost/100);
        }
        
        if(last){
            strcpy(name,Records[i+1].name);
            totalCost = 0;
            headTime = false;
            last = false;
        }
    }
    //Bill();
}
```

错误分析：

1. 184行。缺少换行符

2. 102行。没有return 结果

3. 169行。如果要对Records[1]和Records[n]之间的数排序，则要``sort(Records+1,Records+n+1,Comp)`` 

    我之前写错了，导致最后两个结构体不是左边小于右边的，导致ComputeCost()函数死循环

4. 区分if和if else的用的时机

5. 对于像137行到168行这样的复杂业务逻辑实现，要先写好流程图

6. ```cpp
    if(strcmp(name,Records[i+1].name) != 0 || i==n-1){
        last = true;
    }
    ```

    这是错误的写法，正确的在178行。本来定义的last表示是否是这个顾客的最后一条记录，但若最后两条记录是(JFF, JFF)，则在倒数第2个记录（Records[n-1]）则认定次是last，造成错误
    
7. 77行。忘了更新循环计数

# A1017 Queueing at Bank

```
基本思路：
数据结构：custs(保存顾客信息); wins(保存窗口信息); time(当前时间)
1)对custs按照到达时间排序
2)1.考虑custs[0],找当前最小空闲时刻的窗口win,更新time为最小空闲时刻与custs[0]到达时间的最大值,更新
    custs[0]的等待时间为time-到达时间,更新win的空闲时刻为time+custs[0]办理手续的时间
  2.custs[1]同理.
  3. ...... 考虑最后一个cust
3).考虑所有更新了等待时间的顾客，计算平均等待时间
```

```cpp
/*
基本思路：对所有用户按照到达时间排序
考虑用户到达时间和窗口poptime，
*/
#include<cstdio>
#include<algorithm>
#include<cstring>
#include<cmath>
using namespace std;

const int MAXN = 10005;
const int MAXK = 105;
const int INF = 0x3fffffff;

struct Cust{
    int arriveTime,startTime,dur; //到达时间(s)，开始时间(s)，持续时间(min)
    Cust(){startTime=0;}
};
int n,k;
int popTime[MAXK]; //窗口的下一次空闲时间,初始为8*60*60
Cust custs[MAXN]={Cust()};

bool Comp(Cust a,Cust b){
    return a.arriveTime<b.arriveTime;
}

int main(){
    scanf("%d%d",&n,&k);
    for(int i=0;i<n;i++){
        int hh,mm,ss;
        scanf("%d:%d:%d %d",&hh,&mm,&ss,&custs[i].dur);
        custs[i].arriveTime = hh*60*60+mm*60+ss;
    }
    sort(custs,custs+n,Comp);
    //初始化
    fill(popTime,popTime+k,8*60*60);
    
    for(int i=0;i<n;i++){
        //找到窗口中popTime最小的那个
        int minwin,minpop=INF;
        for(int j=0;j<k;j++){
            if(popTime[j]<minpop){
                minpop = popTime[j];
                minwin = j;
            }
        }
        //考虑窗口的popTime可能小于顾客的到达时间
        /*int time = max(minpop,custs[i].arriveTime);
        if(time<=17*60*60)
        	custs[i].startTime = time;
        else
            break;
        //更新窗口的popTime
        popTime[minwin] = (custs[i].startTime!=0)?custs[i].startTime+min(custs[i].dur*60,3600):minpop;
    	*/
        if(custs[i].arriveTime < 17*60*60){ //不管popTime会不会超过17:00:00，只要到达时间不超过就行
            int time = max(minpop,custs[i].arriveTime);
            custs[i].startTime = time;
            popTime[minwin] = custs[i].startTime+min(custs[i].dur*60,3600);
        }
    }
    //计算平均等待时间
    int count = 0;
    int sec = 0;
    for(int i=0;i<n;i++){
        //if(custs[i].startTime!=0){
        if(custs[i].arriveTime <= 17*60*60){
            sec += (custs[i].startTime-custs[i].arriveTime);
            count++;
        }
    }
    if(count!=0)
    	printf("%.1f\n",(double)sec/(60*count));
    else
        printf("0.0\n");
}
```

错误分析：

1. 73行。输出浮点数时用``%d`` 了
2. 业务逻辑的细节。看56行，67行

```cpp
/*
我觉得代码版本v1是v2也就是这一版本的简洁版，但是v2更能体现算法思想，我更喜欢
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<queue>
#include<vector>
#include<algorithm>
using namespace std;

struct Window{
    int popTime;
    bool inq;
    Window(){popTime = 8*3600; inq = false;}
};

struct Customer{
    int arriveTime;
    int pTime;
    int startTime;
    Customer(){startTime = -1;}
};

const int MAXK = 105;
const int MAXN = 1e4+5;
const int INF = 1e8+5;
int n,m;
Window windows[MAXK];
Customer customers[MAXN];
vector<int> custQue; //初始化为0 1 2 3...

vector<int> winWaitQue;
queue<int> custWaitQue;

bool Comp(int a, int b){
    return customers[a].arriveTime < customers[b].arriveTime;
}
int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        customers[i].startTime = -1;
    }
    for(int i=0; i<MAXK; i++){
        windows[i].inq = false;
        windows[i].popTime = 8*3600;
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    int hh,mm,ss,time;
    for(int i=0; i<n; i++){
        scanf("%d:%d:%d %d",&hh,&mm,&ss,&time);
        customers[i].arriveTime = hh*3600 + mm*60 + ss;
        customers[i].pTime = time*60; //
        custQue.push_back(i);
    }
    sort(custQue.begin(),custQue.end(),Comp);

    while(true){
        /*if(custQue.empty())
            break;*/
        //找到下一个时间节点，窗口空闲或顾客到来
        int minpop = INF;
        vector<int> minwins;
        for(int i=0; i<m; i++){
            if(windows[i].inq == false){ //
                if(windows[i].popTime < minpop){
                    minpop = windows[i].popTime;
                    minwins.clear();
                    minwins.push_back(i);
                }else if(windows[i].popTime == minpop){
                    minwins.push_back(i);
                }
            }
        }
        int minarrive = INF;
        if(!custQue.empty())
            minarrive = customers[custQue.front()].arriveTime;
        /*if(minarrive > 17*3600)
            break;*/
        if(minarrive > 17*3600){
            while(!custQue.empty()){
                custQue.pop_back();
            }
        }
        /*if(minarrive <= 17*3600 && minarrive < minpop){
            custWaitQue.push(custQue.front());
            custQue.erase(custQue.begin());
        }else if((minarrive <= 17*3600 && minarrive > minpop) || (minarrive > 17*3600)){
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                if(windows[index].inq == false){
                    winWaitQue.push_back(index);
                    windows[index].inq = true;
                }
            }
        }else if(minarrive <= 17*3600 && minarrive == minpop){
            custWaitQue.push(custQue.front());
            custQue.erase(custQue.begin());
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                if(windows[index].inq == false){
                    winWaitQue.push_back(index);
                    windows[index].inq = true;
                }
            }
        }*/
        if(minarrive > 17*3600){
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                if(windows[index].inq == false){
                    winWaitQue.push_back(index);
                    windows[index].inq = true;
                }
            }
        }else{
            if(minarrive < minpop){
                custWaitQue.push(custQue.front());
                custQue.erase(custQue.begin());
            }else if(minarrive > minpop){
                for(int i=0; i<minwins.size(); i++){
                    int index = minwins[i];
                    if(windows[index].inq == false){
                        winWaitQue.push_back(index);
                        windows[index].inq = true;
                    }
                }
            }else if(minarrive == minpop){
                custWaitQue.push(custQue.front());
                custQue.erase(custQue.begin());
                for(int i=0; i<minwins.size(); i++){
                    int index = minwins[i];
                    if(windows[index].inq == false){
                        winWaitQue.push_back(index);
                        windows[index].inq = true;
                    }
                }
            }
        }

        if(custWaitQue.empty() && custQue.empty()) //退出循环的逻辑太复杂
            break;
        sort(winWaitQue.begin(),winWaitQue.end());


        //处理两个队列
        while(!winWaitQue.empty() && !custWaitQue.empty()){
            int windex = winWaitQue.front();
            winWaitQue.erase(winWaitQue.begin());
            windows[windex].inq = false;
            int cindex = custWaitQue.front();
            custWaitQue.pop();

            customers[cindex].startTime = max(customers[cindex].arriveTime, windows[windex].popTime);
            //windows[windex].popTime += min(customers[cindex].pTime, 3600);
            windows[windex].popTime = customers[cindex].startTime + customers[cindex].pTime;
        }
    }

    int cnt = 0;
    int summ = 0;
    for(int i=0; i<n; i++){
        if(customers[i].startTime != -1){
            cnt++;
            summ += customers[i].startTime - customers[i].arriveTime;
        }
    }
    printf("%.1f",summ/(1.0*cnt*60)); //in minutes
}
```

错误分析：

1. 逻辑复杂：59行到156行。退出循环的逻辑很复杂。84到105的代码可以被106到137的代码替换，因为后者更简洁明了。
2. 运行超时（逻辑错误）：66行，在寻找最早pop的窗口时，要注意要考虑的窗口必须是inq=false的，加入忘了加这个条件：有一个window的poptime比minarrive小，然后它加入winWaitQue，然后此时custQue非空，custWaitQue为空，则进入下个循环，又把考虑刚才的那个window，它的poptime依然最小，但是它的inq=false，所以未加入winWaitQue，然后又循环，又考虑那个window。。。死循环，导致超时。这是非常严重的逻辑错误。

```
基本思路：
数据：custs; wins; winWaitQue(空闲的窗口的队列); custWaitQue(还没有玩游戏的顾客的队列); time(当前时间)
1).对custs按照到达时间排序
2).1.从时间线上找超过time的最近一个时间点（客户到来或者窗口空闲）（不考虑到达时间超过17:00:00的顾客），把顾客或窗口压入
	 对应的队列，若cust进入顾客等待队列或超过17:00:00则从custs中删除，更新time为最近的时间点
   2.若所有custs为空且顾客等待队列为空，则说明结束了，回到3)
   3.若顾客等待队列和窗口等待队列不是全为空，则考虑队首的顾客和编号最小的窗口，更新顾客等待时间为time-到达时间,
     窗口空闲时刻为time+顾客业务处理时间，一直处理直到有一个队列为空
   4.回到2)
3).考虑所有计算了等待时间的顾客，计算结果。
```

```cpp
/* v2.2
既然用基于时间节点的方式来编程，而不是v1的基于顾客顺序的编程，以下的代码更好。
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<queue>
#include<vector>
#include<algorithm>
using namespace std;

struct Window{
    int popTime;
    Window(){popTime = 8*3600;}
};

struct Customer{
    int arriveTime;
    int pTime;
    int startTime;
    Customer(){startTime = -1;}
};

const int MAXK = 105;
const int MAXN = 1e4+5;
const int INF = 1e8+5;
int n,m;
Window windows[MAXK];
Customer customers[MAXN];
vector<int> custQue; //初始化为0 1 2 3...
int curTime = -1; //当前时间

vector<int> winWaitQue;
queue<int> custWaitQue;

bool Comp(int a, int b){
    return customers[a].arriveTime < customers[b].arriveTime;
}
int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        customers[i].startTime = -1;
    }
    for(int i=0; i<MAXK; i++){
        windows[i].popTime = 8*3600;
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    int hh,mm,ss,time;
    for(int i=0; i<n; i++){
        scanf("%d:%d:%d %d",&hh,&mm,&ss,&time);
        customers[i].arriveTime = hh*3600 + mm*60 + ss;
        customers[i].pTime = time*60; //
        custQue.push_back(i);
    }
    sort(custQue.begin(),custQue.end(),Comp);

    while(true){
        /*if(custQue.empty())
            break;*/
        //找到下一个时间节点，窗口空闲或顾客到来
        int minpop = INF;
        vector<int> minwins;
        for(int i=0; i<m; i++){
            if(windows[i].popTime > curTime && windows[i].popTime < minpop){
                minpop = windows[i].popTime;
                minwins.clear();
                minwins.push_back(i);
            }else if(windows[i].popTime > curTime && windows[i].popTime == minpop){
                minwins.push_back(i);
            }
        }
        int minarrive = INF;
        if(!custQue.empty())
            minarrive = customers[custQue.front()].arriveTime;
        /*if(minarrive > 17*3600)
            break;*/
        if(minarrive > 17*3600){
            while(!custQue.empty()){
                custQue.pop_back();
            }
        }
        /*if(minarrive <= 17*3600 && minarrive < minpop){
            custWaitQue.push(custQue.front());
            custQue.erase(custQue.begin());
        }else if((minarrive <= 17*3600 && minarrive > minpop) || (minarrive > 17*3600)){
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                if(windows[index].inq == false){
                    winWaitQue.push_back(index);
                    windows[index].inq = true;
                }
            }
        }else if(minarrive <= 17*3600 && minarrive == minpop){
            custWaitQue.push(custQue.front());
            custQue.erase(custQue.begin());
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                if(windows[index].inq == false){
                    winWaitQue.push_back(index);
                    windows[index].inq = true;
                }
            }
        }*/
        if(minarrive > 17*3600){
            for(int i=0; i<minwins.size(); i++){
                int index = minwins[i];
                winWaitQue.push_back(index);
            }
            curTime = minpop;
        }else{
            if(minarrive < minpop){
                custWaitQue.push(custQue.front());
                custQue.erase(custQue.begin());
                curTime = minarrive;
            }else if(minarrive > minpop){
                for(int i=0; i<minwins.size(); i++){
                    int index = minwins[i];
                    winWaitQue.push_back(index);
                }
                curTime = minpop;
            }else if(minarrive == minpop){
                custWaitQue.push(custQue.front());
                custQue.erase(custQue.begin());
                for(int i=0; i<minwins.size(); i++){
                    int index = minwins[i];
                        winWaitQue.push_back(index);
                }
                curTime = minarrive;
            }
        }

        if(custWaitQue.empty() && custQue.empty()) //退出循环的逻辑太复杂
            break;
        sort(winWaitQue.begin(),winWaitQue.end());


        //处理两个队列
        while(!winWaitQue.empty() && !custWaitQue.empty()){
            int windex = winWaitQue.front();
            winWaitQue.erase(winWaitQue.begin());
            int cindex = custWaitQue.front();
            custWaitQue.pop();

            customers[cindex].startTime = max(customers[cindex].arriveTime, windows[windex].popTime);
            //windows[windex].popTime += min(customers[cindex].pTime, 3600);
            windows[windex].popTime = customers[cindex].startTime + customers[cindex].pTime;
        }
    }

    int cnt = 0;
    int summ = 0;
    for(int i=0; i<n; i++){
        if(customers[i].startTime != -1){
            cnt++;
            summ += customers[i].startTime - customers[i].arriveTime;
        }
    }
    printf("%.1f",summ/(1.0*cnt*60)); //in minutes
}
```

# A1018 Public Bike Management(图论)

```
1).用Dijkstra算法算set<int> Pre[n]的值
2).DFS Pre，找到叶子节点，计算send值和bring值，并和minSend和minBring比较来更新optPath.
```

```cpp
/*
基本思路：这道题就是取出所有最短路径，然后比较最优的那个
*/
#include<cstdio>
#include<set>
#include<vector>
#include<cstring>
#include<stack>
#include<queue>
#include<cmath>
using namespace std;

const int MAXN = 505;
const int INF = 0x3fffffff;

struct Edge{
    int v,t;
    Edge(int _v,int _t){v=_v;t=_t;}
};
int cmax,n,sp,m; //最大容量，station个数，problem station,边的个数
int bikeNum[MAXN]; //从1开始
vector<Edge> Adj[MAXN]; //邻接表

set<int> Pre[MAXN]; //前驱结点
bool vis[MAXN] = {false};
int dist[MAXN];

bool inq[MAXN] = {false};

vector<int> path; 
vector<int> optPath;
int minSend = INF;
int minBring = INF;

void Dijkstra(){ //默认从0出发
    //初始化
    fill(dist,dist+n+1,INF);
    fill(vis,vis+n+1,false);
    dist[0] = 0;
    
    for(int i=0;i<=n;i++){
        int u = -1;
    	int min = INF;
        for(int i=0;i<=n;i++){
        	if(dist[i]<min && vis[i]==false){
            	min = dist[i];
            	u = i;
        	}
    	}
    	if(u==-1)return;
   	    vis[u] = true;
        
        vector<Edge>::iterator it;
        for(it=Adj[u].begin();it != Adj[u].end();it++){
            if(!vis[(*it).v]){ //如果邻接点未被访问过
                if(dist[u]+(*it).t < dist[(*it).v]){
                    dist[(*it).v] = dist[u]+(*it).t;
                    Pre[(*it).v].clear();
                    Pre[(*it).v].insert(u);
                }else if(dist[u]+(*it).t == dist[(*it).v]){
                    Pre[(*it).v].insert(u);
                }
            }
        }
    }
}
void SPFA3(){
    //初始化
    fill(dist,dist+n+1,INF);
    dist[0] = 0;
    
    int u = 0;
    queue<int> q;
    q.push(u);
    inq[u] = true;
    while(!q.empty()){
        u = q.front(); q.pop();
        inq[u] = false;
        for(int i=0;i<Adj[u].size();i++){
            int v = Adj[u][i].v;
            int t = Adj[u][i].t;
            if(dist[u]+ t < dist[v]){
                dist[v] = dist[u]+t;
                Pre[v].clear();
                Pre[v].insert(u);
                if(inq[v] == false){
                    q.push(v);
                    inq[v] = true;
                }
            }else if(dist[u]+t == dist[v]){
                Pre[v].insert(u);
            }
        }
    }
}
void DFS(int s){
    if(s==0){ //如果到了叶子结点
        /*if(value==0 || (value>0 && value<optValue) || (value<0 && optValue>0) || (value<0 && optValue<0 && abs(value)<abs(optValue))){
            optValue = value;
            optPath.clear();
            optPath = path;
        }
        return;*/
        int send=0,bring=0;
        for(int i=path.size()-1;i>=0;i--){
            if(i==path.size()-1)
                continue;
            if(cmax/2 < bikeNum[path[i]]){
                bring += (bikeNum[path[i]]-cmax/2);
            }else if(cmax/2 > bikeNum[path[i]]){
                if(bring >= cmax/2-bikeNum[path[i]]){
                    bring -= (cmax/2-bikeNum[path[i]]);
                }else{
                    //bring = 0;
                    send += (cmax/2-bikeNum[path[i]]-bring);
                    bring = 0;
                }
            }
        }
        if(send<minSend || (send==minSend && bring < minBring)){
            minSend = send;
            minBring = bring;
            optPath.clear();
            optPath = path;
        }
    }
    
    set<int>::iterator it;
    for(it=Pre[s].begin();it!=Pre[s].end();it++){
        path.push_back((*it));
        DFS((*it));
        path.erase(--path.end());
    }
}

int main(){
    //输入
    scanf("%d%d%d%d",&cmax,&n,&sp,&m);
    for(int i=1;i<=n;i++){
        scanf("%d",&bikeNum[i]);
    }
    for(int i=0;i<m;i++){
        int v1,v2,t;
        scanf("%d%d%d",&v1,&v2,&t);
        Adj[v1].push_back(Edge(v2,t));
        Adj[v2].push_back(Edge(v1,t));
    }
    //Dijkstra();
    SPFA();
    
    path.push_back(sp);
    DFS(sp);
    
    printf("%d ",minSend);
    int v = optPath.back(); optPath.erase(--optPath.end());
    printf("%d",v);
    while(!optPath.empty()){
        v = optPath.back(); optPath.erase(--optPath.end());
        printf("->%d",v);
    }
    printf(" %d",minBring);
}
```

错误分析：

1. path一个一个pop到optPath中后，path中的数据没了。所以，path和optPath最好用vector表示
2. 累加value时，用了``DFS(s,value+=c)`` 这样做，会把次状态中的输入参数value改变，循环第2次后再用的value就不是期望的那个value了
3. 业务逻辑错误。若一条路径上的权重分别为*->1->8->9->0。（10为最大容量）则从PBMC出发时需要带4个,回去时，需要带2个，而不是单纯地出发是带2个，回去时带0个。因为路径后面的车子虽然多，但是无法从路径后面的带到路径前面的。
4. 114行。数据更新顺序错了
5. 120行到125行。对两种情况的分别对minSend和minBring单个处理了，其实，不管哪种情况，都应该更新minSend和minBring两个。

# A1019 General Palindromic Number

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
int n,b;
int main(){
	cin>>n>>b;
	vector<int> num;
	while(n!=0){
		num.push_back(n%b);
		n=n/b;
	}
	int siz=num.size();
	for(int i=0;i<=siz/2;i++){
		if(num[i]!=num[siz-i-1]){
			cout<<"No"<<endl;
			goto output;
		}
	}
	cout<<"Yes"<<endl;
	output:;
	for(int j=siz-1;j>=0;j--){
		if(j==siz-1){
			cout<<num[j];
		}else{
			cout<<" "<<num[j];
		}
	}
} 
```

