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

# A1003 Emergency

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



# **A1004** **Counting Leaves**

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

# A1006 Sign In and Sign Out

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

```cpp
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

# A1007 MSS

**动态规划**

最大子序列大小

Maximum Subsequence is the continuous subsequence.

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

# A1013 Battle over Cities

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

    

# A1014 Waiting in Line

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



```cpp
/*
基本思想：计算每个客户的结束时间，与17:00比较
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



# A1018 Public Bike Management

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
void SPFA(){
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

# A1020 Tree Traversals

```cpp
#include<cstdio>
#include<queue>
using namespace std;

struct Node{
    int data;
    Node* lchild;
    Node* rchild;
    Node(int d){data = d;lchild = rchild = nullptr;}
};

const int MAXN = 50;
int n; //结点个数
int Post[MAXN],In[MAXN]; //后序遍历，先序遍历

Node* Create(int postL,int postR,int inL,int inR){ //当前二叉树的后序序列区间为[postL,postR]，先序序列区间为[inL,inR]
    //递归出口
    if(postL > postR){
        return nullptr;
    }
    
    int k; //中序遍历序列中根节点的位置
    for(k=inL;k<=inR;k++){
        if(In[k] == Post[postR])
            break;
    }
    Node* root = new Node(In[k]); //根节点
    int numLeft = k-inL;
    root->lchild = Create(postL,postL+numLeft-1,inL,k-1);
    root->rchild = Create(postL+numLeft,postR-1,k+1,inR);
    return root;
}

void BFS(Node* root){
    queue<Node*> q;
    q.push(root);
    int num = 0; //表示正在访问第num个结点
    while(!q.empty()){
        Node* now = q.front();
        q.pop();
        num++;
        printf("%d",now->data); //输出结点
        if(num < n){
            printf(" ");
        }
        if(now->lchild)
            q.push(now->lchild);
        if(now->rchild)
            q.push(now->rchild);
    }
}

int main(){
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        scanf("%d",&Post[i]);
    }
    for(int i=0;i<n;i++){
        scanf("%d",&In[i]);
    }
    Node* root = Create(0,n-1,0,n-1);
    BFS(root);
}
```

# A1021 Deepest Root

```cpp
/*
思路：
1 由于连通、边数为n-1的图一定是一棵树，因此需要判断给定数据是否能使图连通。使用并查集判断方法：
每读入一条边的两个端点，判断这两个端点是否属于相同的集合，如果不同，则将它们合并到一个集合中，当处理完所有边后根据最终产生的集合个数是否为1来判断给定的图是否连通。
2 确定图连通后，则确定了树，选择合适根结点使树高最大的做法为：
先任意选择一个结点，从该节点开始遍历整棵树，获取能达到的最深的结点，记为集合A；然后从集合A中任意一个结点出发遍历整棵树，获取能达到的最深顶点，记为结点集合B。集合A与B的并集就是所求结果。
*/
#include<cstdio>
#include<vector>
#include<set>
#include<cstring>
using namespace std;

struct Edge{
    int v1,v2;
    Edge(int _v1, int _v2){v1=_v1;v2=_v2;}
};
const int MAXN = 10005;
int n;
vector<int> Adj[MAXN];
int father[MAXN];
bool vis[MAXN] = {false};

int findFather(int x){
    if(x == father[x])
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
void DFS(int s,int layer,int& maxL,set<int>& se){ //从s遍历，找到最深的结点，放到se中
    if(layer > maxL){
        maxL = layer;
        se.clear();
        se.insert(s);
    }else if(layer == maxL){
        se.insert(s);
    }
    for(int i=0;i<Adj[s].size();i++){
        int v = Adj[s][i]; //邻接点
        if(vis[v] == false){
            vis[v] = true;
        	DFS(v,layer+1,maxL,se);
        }
    }
}
int main(){
    scanf("%d",&n);
    //初始化
    for(int i=0;i<=n;i++)
        father[i] = i;
    
    //输入及并查集操作
    int u = 1; //第一个输入的结点号
    for(int i=1;i<=n-1;i++){
        int v1,v2;
        scanf("%d%d",&v1,&v2);
        if(i==1)
            u = v1;
        Adj[v1].push_back(v2);
        Adj[v2].push_back(v1);
        unionTwo(v1,v2);
    }
    set<int> roots; //存储并查集树的根节点
    for(int i=1;i<=n;i++){
        roots.insert(findFather(i));
    }
    
    if(roots.size() != 1){ //说明有环路，联通块数大于1
        printf("Error: %d components\n",roots.size());
    }else{ //Adj是个树
        set<int> A,B;
        
        int maxL = 0;
        fill(vis,vis+n+1,false);
        vis[u] = true;
        DFS(u,0,maxL,A); //从u出发所能到达的最深的结点
        
        set<int>::iterator it;
        for(it = A.begin(); it != A.end(); it++){
            maxL = 0;
            fill(vis,vis+n+1,false);
            vis[(*it)] = true;
            DFS((*it),0,maxL,B);
            break; //只运行一次
        }
        
        A.insert(B.begin(),B.end());
        for(it = A.begin(); it != A.end(); it++){
            printf("%d\n",(*it));
        }
    }
}
```

错误分析：

1. 函数运行前初始化
2. 56行。使用未输入值的n
3. 60行。一开始为``int u=0`` 当输入的n只有1时，u作为第一个结点，是不合法的（因为结点从1开始编号）
4. 算法逻辑错误。91行，先从一个点找最深节点，结果在A中，再从A中任意一个结点出发找最深结点，结果啊在B中，而无需对A中所有结点进行DFS，否则会超时。

# A1022 Digital Library

```cpp
/*
基本思路：用map保存string到int的映射关系，这样，每次查询时只用比较int，不用重复比较string，节省时间，
信息块中存储的信息段也都用int表示，节省空间
*/
#include<cstdio>
#include<map>
#include<string>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXN = 10005;
int n,m;
int cntTit = 0,cntAut = 0,cntKey = 0,cntPub=0;
map<string,int> mapTit,mapAut,mapKey,mapPub; //title-int, author-int, keywords-int, publisher-int

struct Node{
    int id;
    int title;
    int author;
    vector<int> keywords;
    int publisher;
    int year;
};

Node lib[MAXN];

void erase_n(char* s){ //去掉换行符
    int i=0;
    while(s[i] != '\n' && s[i] != '\0')
        i++;
    s[i] = '\0';
}
//根据字符串c模式，来分割字符串s，结果放在v中
void SplitString(const string s,const string c,vector<string>& v){
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
void input(int i){ //输入一个信息块：id,title,author......
    char tit[85],aut[85],key[10005],pub[85];

    scanf("%d\n",&lib[i].id); //输入id
    fgets(tit,85,stdin); //输入title
    erase_n(tit);
    if(mapTit.find(string(tit)) == mapTit.end()){
        mapTit[string(tit)] = cntTit++;
        lib[i].title = cntTit - 1;
    }else{
        lib[i].title = mapTit[string(tit)];
    }
    fgets(aut,85,stdin); //输入author
    erase_n(aut);
    if(mapAut.find(string(aut)) == mapAut.end()){
        mapAut[string(aut)] = cntAut++;
        lib[i].author = cntAut - 1;
    }else{
        lib[i].author = mapAut[string(aut)];
    }
    //输入keywords
    /*while(scanf("%s",&key) != EOF){
        if(mapKey.find(string(key)) == mapKey.end()){
            mapKey[string(key)] = cntKey++;
            lib[i].keywords.push_back(cntKey-1);
        }else{
            lib[i].keywords.push_back(mapKey[string(key)]);
        }
    }*/
    fgets(key,11005,stdin); 
    erase_n(key);
    vector<string> vec_res;
    SplitString(key," ",vec_res);
    for(int j=0;j<vec_res.size();j++){
        if(mapKey.find(vec_res[j]) == mapKey.end()){
            mapKey[vec_res[j]] = cntKey++;
            lib[i].keywords.push_back(cntKey-1);
        }else{
            lib[i].keywords.push_back(mapKey[vec_res[j]]);
        }
    }
    fgets(pub,85,stdin); //输入publisher
    erase_n(pub);
    if(mapPub.find(string(pub)) == mapPub.end()){
        mapPub[string(pub)] = cntPub++;
        lib[i].publisher = cntPub - 1;
    }else{
        lib[i].publisher = mapPub[string(pub)];
    }
    scanf("%d\n",&lib[i].year); //输入年份
}

vector<int> res; //查询结果的集合，并排序
void query(int info){ //根据年份查找
    //清空res
    res.clear();

    for(int i=0;i<n;i++){
        if(lib[i].year == info){
            res.push_back(lib[i].id);
        }
    }
    sort(res.begin(),res.end());
}
void query2(int num, string str){ //num表示输入的数据类型：title,pub...
    //清空res
    res.clear();

    int info;

    if(num == 1){ //根据title查询
        if(mapTit.find(str) == mapTit.end())
            return;
        info = mapTit[str];
        for(int i=0;i<n;i++){
            if(lib[i].title == info){
                res.push_back(lib[i].id);
            }
        }
    }else if(num == 2){ //根据author查询
        if(mapAut.find(str) == mapAut.end())
            return;
        info = mapAut[str];
        for(int i=0;i<n;i++){
            if(lib[i].author == info){
                res.push_back(lib[i].id);
            }
        }
    }else if(num == 3){ //根据关键词查询
        if(mapKey.find(str) == mapKey.end())
            return;
        info = mapKey[str];
        for(int i=0;i<n;i++){
            if(std::count(lib[i].keywords.begin(),lib[i].keywords.end(),info) > 0){
                res.push_back(lib[i].id);
            }
        }
    }else{ //根据出版商查询
        if(mapPub.find(str) == mapPub.end()) 
            return;
        info = mapPub[str];
        for(int i=0;i<n;i++){
            if(lib[i].publisher == info){
                res.push_back(lib[i].id);
            }
        }
    }
    sort(res.begin(),res.end());
}

int main(){
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        input(i); //out:lib
    }

    scanf("%d",&m); //输入query的个数
    for(int i=0;i<m;i++){
        int num;
        int info;
        char info2[85];
        scanf("%d: ",&num);
        if(num == 5){ //后面是年份
            scanf("%d\n",&info);
            query(info); //查询
        }else{
            //scanf("%s",&info2);
            fgets(info2,85,stdin);
            erase_n(info2);
            query2(num, string(info2)); //查询
        }
        //输出
        if(num == 5)
        	printf("%d: %d\n",num,info);
        else
            printf("%d: %s\n",num,info2);
        if(res.size() != 0){
            for(int j=0;j<res.size();j++){
                printf("%07d\n",res[j]);
            }
        }else{
            printf("Not Found\n");
        }
    }

}

```

错误分析：

1. 27行。scanf的用法错误
2. 42行到50行。读取一行中的所有单词，不能用scanf，具体见我记的c++笔记，只能用gets，然后分割字符串
3. 输出格式错误。若输入为0000111，输出不应为111，因此用``printf("%07d")`` 的形式
4. 输入错误。由于关键词不多于10个字符，共有1000个不同的字符，所以一行所有关键词的最大大小为11000+5
5. stl使用错误。用map进行映射时，要先用map.find()函数判断map中是否有对应的键，然后再查询，否则，直接进行映射，即使map中没有对应的键，也会返回一个默认值，比如0，但是有些键的值也是0，产生错误

**别人的思路**

https://blog.csdn.net/A_Aria/article/details/86558688

这里有个思路比较好。由于题目只要关键词对应的所有书本ID，因此在数据输入时就建立，关键词-书本ID集的对应关系，如``悲惨世界-{0000001, 0000002, 0000003}`` 。这样既简单，又高效

# A1025 PAT Ranking

```CPP
/*
基本思路1：有n个vector，每个存储各个赛区的选手信息。对每个vector进行排序，完成后遍历，算local rank。
把所有vector进行merge，完成后遍历，算final rank
*/
/*
基本思路2：用一个数组存储所有选手信息，并按照（赛区号，成绩，id）排序，之后算local rank。
第1个和第2个合并，第3个和第4个合并，....，第1，2个的结果和3，4的结果合并
*/
#include<cstdio>
#include<iostream>
#include<string>
#include<algorithm>
#include<vector>
using namespace std;

const int MAXN = 105;
const int MAXK = 305;

struct Node{
    string id;
    int score;
    int area;
    int localRank;
    int finalRank;
};
int n;
Node testees[MAXN*MAXK];
vector<int> Size; //记录要合并的块的大小，Size的大小表示要合并的块的个数
int totalNum = 0;

bool Comp(Node a, Node b){
    if(a.score != b.score){
        return a.score > b.score;
    }else{
        return a.id < b.id;
    }
}
void Sort(){ //赛区内排序
    int a=0,b;
    for(int i=0;i<Size.size();i++){
        b = a + Size[i] - 1;
        sort(testees+a, testees+b+1, Comp);
        a = b + 1;
    }
}
void LocalRank(){ //testees中至少有一个人,且排序好
    int rank = 1; //人数计数
    int area = 1;
    int score = testees[0].score;
    testees[0].localRank = 1;
    for(int i=1;i<totalNum;i++){
        if(testees[i].area != area){ //赛区变化
            area = testees[i].area;
            rank = 1;
            score = testees[i].score;
            testees[i].localRank = 1;
        }else{
        	if(testees[i].score == score){
        	    testees[i].localRank = testees[i-1].localRank;
        	    ++rank;
        	}else{
        	    testees[i].localRank = ++rank;
        	    score = testees[i].score;
        	}
        }
    }
}
void Merge(int a, int b, int c){ //合并[a,b]与[b+1,c]
    Node* temp = new Node[c-a+1];
    int i=a,j=b+1,k=0;
    while(i<=b && j<=c){
        if(Comp(testees[i],testees[j]) == true){
            temp[k] = testees[i];
            i++;
        }else{
            temp[k] = testees[j];
            j++;
        }
        k++;
    }
    if(i <= b){
        while(i <= b){
            temp[k] = testees[i];
            k++;
            i++;
        }
    }else if(j <= c){
        while(j <= c){
            temp[k] = testees[j];
            k++;
            j++;
        }
    }
    k = 0;
    for(i=a;i<=c;i++){
        testees[i] = temp[k];
        k++;
    }
}
void FinalRank(){
    int rank = 1;
    int score = testees[0].score;
    testees[0].finalRank = 1;
    for(int i=1;i<totalNum;i++){
        if(testees[i].score == score){
            testees[i].finalRank = testees[i-1].finalRank;
            ++rank;
        }else{
            testees[i].finalRank = ++rank;
            score = testees[i].score;
        }
    }
}
int main(){
    scanf("%d",&n);
    for(int i=0;i<n;i++){
        int k;
        scanf("%d",&k);
        Size.push_back(k); //记录人数
        for(int j=0;j<k;j++){
            //int num = i*n + j;
            int num = totalNum + j;
            cin>>testees[num].id;
            cin>>testees[num].score;
            testees[num].area = i + 1;
        }
        totalNum += k;
    }
    //赛区内的排序
    Sort();

    //计算每个赛区的localRank
    LocalRank();

    //合并
    while(Size.size() != 1){ //未合并完
        int a=0,b,c;
        int i;
        vector<int> temp;
        for(i=0;i<=Size.size()-2;i=i+2){
            b = a + Size[i] - 1;
            c = b + Size[i+1];
            Merge(a,b,c);
            temp.push_back(c-a+1);

            a = c + 1;
        }
        if(i == Size.size()-1){
            temp.push_back(Size[i]);
        }

        Size = temp;
    }
    //sort(testees,testees+totalNum,Comp);

    //计算finalRank
    FinalRank();
	
    printf("%d\n",totalNum);
    for(int i=0;i<totalNum;i++){
        printf("%s %d %d %d\n",testees[i].id.c_str(),testees[i].finalRank,testees[i].area,testees[i].localRank);
    }
}

```

错误分析：

1. Comp函数写错了，若按照成绩由大到小排序，则应该写``return a.score > b.score`` 
2. 归并排序的Merge函数写的有问题。判断一个数组没有merge完的标准是``if(i <= b)`` 而不是 ``if(i != b)`` 
3. 121行。我想记录是第几个人，但是121行写的是什么鬼。

# A1026 Table Tennis

```cpp
//v1.1
/*
基本思路1：一个桌子结构体有：是否VIP，poptime。一个vector1保存排队队列，一个vipNum保存队列中VIP的数量。
先找最先pop的桌子，若是VIP桌子，则优先让VIP进来，若不是，则让队列中最前面的进来
每个玩家最多玩2h

基本思路2：维护两个队列：桌子队列和玩家队列（vector表示），桌子队列表示已经空闲但是暂时没有人来玩的桌子，
玩家队列表示已经到来但是没有桌子可以用。通过移动时间的方式更新两个队列。
1. 移动到一个时间点（玩家到来或桌子空闲），若时间点过了21:00:00则推出循环
2. 若玩家到来，则玩家进入玩家队列，若桌子空闲，则桌子进入桌子队列，处理两个队列（出队），直到有至少一个队列为空，
    回到1
*/
#include<cmath>
#include<cstdio>
#include<vector>
#include<algorithm>
#include<queue>
using namespace std;

const int MAXN = 10005; //最大的玩家对的个数
const int MAXK = 105; //最大的桌子个数
const int INF = 1e8;

int n,k,m; //m是VIP桌子个数

struct Table{
    bool isVIP;
    int popTime; //second
    int servePairNum;
};
struct Pair{
    int arriveTime;
    int serveTime; //默认为0，表示未接受服务
    int playTime; //in second
    bool isVIP;
};
Table tables[MAXK];
Pair pairs[MAXN];
vector<int> pairs2; //保存排序好的pairs副本, 静态指针数组

vector<int> tablesQue;
vector<int> pairsQue;
bool inq[MAXK] = {false};

bool Comp(int a, int b){
    return pairs[a].arriveTime < pairs[b].arriveTime;
}
bool Comp2(int a, int b){
    return pairs[a].serveTime < pairs[b].serveTime;
}
/*
in: pairs2, a, tables
out: tablesQue, pairsQue, a
return: false表示不会再有入队的元素了
*/
/*bool Inque(int& a){ //pairs2[a]开始选择
    //选择桌子中最早pop的
    int minpop = INF;
    vector<int> mintables;
    for(int i=1; i<=k; i++){
        if(inq[i] == false && tables[i].popTime < minpop){
            minpop = tables[i].popTime;
            mintables.clear();
            mintables.push_back(i);
        }else if(inq[i] == false && tables[i].popTime == minpop){
            mintables.push_back(i);
        }
    }
    //if(minpop > 21*3600)
    //    return false;

    //选择玩家
    int player = pairs2[a];
    if(pairs[player].arriveTime < minpop){
        pairsQue.push_back(player);
        a++;
    }else if(pairs[player].arriveTime == minpop){
        pairsQue.push_back(player);
        a++;
        for(int j=0; j<mintables.size(); j++){
            tablesQue.push_back(mintables[j]);
            inq[mintables[j]] = true;
        }
        sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    }else{
        for(int j=0; j<mintables.size(); j++){
            tablesQue.push_back(mintables[j]);
            inq[mintables[j]] = true;
        }
        sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    }
    return true;
}*/

/*
in: pairs2, a, tables
out: tablesQue, pairsQue, a
return: false表示不会再有入队的元素了
*/
bool Inque(int& a){
    int minpop = INF;
    vector<int> mintables;
    for(int i=1; i<=k; i++){
        if(inq[i] == false && tables[i].popTime < minpop){
            minpop = tables[i].popTime;
            mintables.clear();
            mintables.push_back(i);
        }else if(inq[i] == false && tables[i].popTime == minpop){
            mintables.push_back(i);
        }
    }
    
    if(minpop >= 17*3600 && a == n){
        return false;
    }else if(a < n){
    	int player = pairs2[a];
    	if(pairs[player].arriveTime < minpop){
    	    pairsQue.push_back(player);
    	    a++;
    	}else if(pairs[player].arriveTime == minpop){
    	    pairsQue.push_back(player);
    	    a++;
    	    for(int j=0; j<mintables.size(); j++){
    	        tablesQue.push_back(mintables[j]);
    	        inq[mintables[j]] = true;
    	    }
    	    sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    	}else{
    	    for(int j=0; j<mintables.size(); j++){
    	        tablesQue.push_back(mintables[j]);
    	        inq[mintables[j]] = true;
    	    }
    	    sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    	}
    }else if(minpop < 17*3600){
        for(int j=0; j<mintables.size(); j++){
    	    tablesQue.push_back(mintables[j]);
    	    inq[mintables[j]] = true;
    	}
    	sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    }
    return true;
}

/*
in: tablesQue
return: -1:没有, 否则，是vip的下标
*/
int VipInTablesQue(){
    int i ;
    for(i = 0; i < tablesQue.size(); i++){
        if(tables[tablesQue[i]].isVIP == true)
            return i;
    }
    return -1;
}
int VipInPairsQue(){
    int i;
    for(i = 0; i < pairsQue.size(); i++){
        if(pairs[pairsQue[i]].isVIP == true)
            return i;
    }
    return -1;
}
/*
tables[tablesQue[c]]和pairs[pairsQue[d]]配对了，更新操作,pairs,tables,pairsQue,tablesQue
*/
void Update(int c, int d){
    int a = tablesQue[c];
    int b = pairsQue[d];
    pairs[b].serveTime = max(pairs[b].arriveTime, tables[a].popTime);
    tables[a].popTime = pairs[b].serveTime + min(pairs[b].playTime,7200); //最多玩2h
    tables[a].servePairNum++;
    tablesQue.erase(tablesQue.begin()+c);
    inq[a] = false;
    pairsQue.erase(pairsQue.begin()+d);
}

int main(){
    //freopen("D:\\input.txt", "r", stdin);

    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        int hh,mm,ss,time,flag;
        //scanf("%d:%d:%d %d:%d:%d %d %d\n",&hh,&mm,&ss,&time,&flag);
        scanf("%d:%d:%d %d %d\n",&hh,&mm,&ss,&time,&flag);
        pairs[i].arriveTime = hh*3600 + mm*60 + ss;
        pairs[i].playTime = time*60;
        pairs[i].isVIP = (flag == 1) ? true : false;
        pairs[i].serveTime = 0;
        pairs2.push_back(i);
    }
    scanf("%d %d\n",&k,&m);
    for(int i=1; i<=k; i++){
        tables[i].isVIP = false;
        tables[i].popTime = 8*3600;
        tables[i].servePairNum = 0;
    }
    for(int i=0;i<m;i++){
        int a;
        scanf("%d",&a);
        //tables[i].isVIP = true;
        tables[a].isVIP = true;
    }

    sort(pairs2.begin(),pairs2.end(),Comp); //pairs副本按照到达时间由小到大排序

    /*int i = 0;
    while(i <= n){ //pairs2中的玩家没有遍历完
        Inque(i);
        while(!tablesQue.empty() && !pairsQue.empty()){
            int a = VipInTablesQue();
            if(a != -1){ //队列中有VIP桌子
                int b = VipInPairsQue();
                if(b != -1){ //队列中有VIP
                    Update(a,b);
                }else{
                    //Update(a,0)
                    Update(0,0);
                }
            }else{
                Update(0,0);
            }
        }
    }*/
    int i = 0;
    while(Inque(i)){
        while(!tablesQue.empty() && !pairsQue.empty()){
            int a = VipInTablesQue();
            if(a != -1){ //队列中有VIP桌子
                int b = VipInPairsQue();
                if(b != -1){ //队列中有VIP
                    Update(a,b);
                }else{
                    //Update(a,0);
                    Update(0,0);
                }
            }else{
                Update(0,0);
            }
        }
    }

    sort(pairs2.begin(),pairs2.end(),Comp2); //按照serveTime排序
    for(i=0; i<pairs2.size();i++){
        int a = pairs2[i];
        if(pairs[a].serveTime != 0){
            int time1 = pairs[a].arriveTime;
            int time2 = pairs[a].serveTime;
            //int h1 = time1/3600, m1 = (time1-time1/3600)/60, s1 = (time1-time1/3600)%60;
            //int h2 = time2/3600, m2 = (time2-time2/3600)/60, s2 = (time2-time2/3600)%60;
            int h1 = time1/3600, m1 = (time1%3600)/60, s1 = (time1%3600)%60;
            int h2 = time2/3600, m2 = (time2%3600)/60, s2 = (time2%3600)%60;
            printf("%02d:%02d:%02d %02d:%02d:%02d %.0f\n",h1,m1,s1,h2,m2,s2,round((time2-time1)/60.0));
        }
    }
    for(i=1; i<=k; i++){
        if(i == 1){
            printf("%d",tables[i].servePairNum);
        }else{
            printf(" %d",tables[i].servePairNum);
        }
    }
}

```

有两个case没过

错误分析：

1. 细节错误：187, 184，201
2. 业务逻辑错误：120，题目中要求最多玩2h；166，在有VIP桌子空闲时且没有VIP时，第一个玩家不能选择VIP桌子，除非VIP桌子是在编号最小的那个。
3. 数据更新错误：174，在该更新inq数组的时候没有更新，桌子从等待队列出去后，置inq值为false；
4. 逻辑错误：68---69；我对于循环（两个队列的入队操作，pairs tables的更新以及两个队列的出队操作）的循环逻辑搞错了。以前，我是这样想的，当没有玩家可以入队时退出循环，推出循环后也不更新pairs tables了，但实际上，这个时候退出循环，仍然可能有在21:00前pop的桌子入队，所以推出循环的逻辑错了。正确的做法是，当最早pop的桌子是在21:00之后且没有玩家可以入队时推出循环，因为这个时候，才真正不可能有入队的操作。
5. 逻辑错误：112行退出循环的逻辑还是错了。虽然``minpop >= 17*3600 && a == n`` 时肯定要退出大循环，但是万一所有玩家都在21:00:00之前进来和玩完，这就意味着minpop<17*3600时也会有退出循环的情况。

```cpp
//v1.2
#include<cmath>
#include<cstdio>
#include<vector>
#include<algorithm>
#include<queue>
using namespace std;

const int MAXN = 10005; //最大的玩家对的个数
const int MAXK = 105; //最大的桌子个数
const int INF = 1e8;

int n,k,m; //m是VIP桌子个数

struct Table{
    bool isVIP;
    int popTime; //second
    int servePairNum;
};
struct Pair{
    int arriveTime;
    int serveTime; //默认为0，表示未接受服务
    int playTime; //in second
    bool isVIP;
};
Table tables[MAXK];
Pair pairs[MAXN];
vector<int> pairs2; //保存排序好的pairs副本, 静态指针数组

vector<int> tablesQue;
vector<int> pairsQue;
bool inq[MAXK] = {false};

bool Comp(int a, int b){
    return pairs[a].arriveTime < pairs[b].arriveTime;
}
bool Comp2(int a, int b){
    return pairs[a].serveTime < pairs[b].serveTime;
}

/*
in: tablesQue
return: -1:没有, 否则，是vip的下标
*/
int VipInTablesQue(){
    int i ;
    for(i = 0; i < tablesQue.size(); i++){
        if(tables[tablesQue[i]].isVIP == true)
            return i;
    }
    return -1;
}
int VipInPairsQue(){
    int i;
    for(i = 0; i < pairsQue.size(); i++){
        if(pairs[pairsQue[i]].isVIP == true)
            return i;
    }
    return -1;
}
/*
tables[tablesQue[c]]和pairs[pairsQue[d]]配对了，更新操作,pairs,tables,pairsQue,tablesQue
*/
void Update(int c, int d){
    int a = tablesQue[c];
    int b = pairsQue[d];
    pairs[b].serveTime = max(pairs[b].arriveTime, tables[a].popTime);
    tables[a].popTime = pairs[b].serveTime + min(pairs[b].playTime,7200); //最多玩2h
    tables[a].servePairNum++;
    tablesQue.erase(tablesQue.begin()+c);
    inq[a] = false;
    pairsQue.erase(pairsQue.begin()+d);
}

int main(){
    //freopen("D:\\input.txt", "r", stdin);

    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        int hh,mm,ss,time,flag;
        //scanf("%d:%d:%d %d:%d:%d %d %d\n",&hh,&mm,&ss,&time,&flag);
        scanf("%d:%d:%d %d %d\n",&hh,&mm,&ss,&time,&flag);
        pairs[i].arriveTime = hh*3600 + mm*60 + ss;
        pairs[i].playTime = time*60;
        pairs[i].isVIP = (flag == 1) ? true : false;
        pairs[i].serveTime = 0;
        pairs2.push_back(i);
    }
    scanf("%d %d\n",&k,&m);
    for(int i=1; i<=k; i++){
        tables[i].isVIP = false;
        tables[i].popTime = 8*3600;
        tables[i].servePairNum = 0;
    }
    for(int i=0;i<m;i++){
        int a;
        scanf("%d",&a);
        //tables[i].isVIP = true;
        tables[a].isVIP = true;
    }

    sort(pairs2.begin(),pairs2.end(),Comp); //pairs副本按照到达时间由小到大排序

    int i = 0;
    while(true){
		int minpop = INF;
    	vector<int> mintables;
    	for(int j=1; j<=k; j++){
    	    if(inq[j] == false && tables[j].popTime < minpop){
    	        minpop = tables[j].popTime;
    	        mintables.clear();
    	        mintables.push_back(j);
    	    }else if(inq[j] == false && tables[j].popTime == minpop){
    	        mintables.push_back(j);
    	    }
    	}
    	
        //如何退出循环
    	if(minpop >= 21*3600 && i == n){ //说明一些用户在21点之前不能得到桌子; minpop>=17*3600
    	    //return false;
            break;
    	}else if(i < n){
    		int player = pairs2[i];
    		if(pairs[player].arriveTime < minpop){
    		    pairsQue.push_back(player);
    		    i++;
    		}else if(pairs[player].arriveTime == minpop){
    		    pairsQue.push_back(player);
    		    i++;
    		    for(int j=0; j<mintables.size(); j++){
    		        tablesQue.push_back(mintables[j]);
    		        inq[mintables[j]] = true;
    		    }
    		    sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    		}else{
    		    for(int j=0; j<mintables.size(); j++){
    		        tablesQue.push_back(mintables[j]);
    		        inq[mintables[j]] = true;
    		    }
    		    sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    		}
    	}else if(minpop < 21*3600){ //minpop < 17*3600
    	    for(int j=0; j<mintables.size(); j++){
    		    tablesQue.push_back(mintables[j]);
    		    inq[mintables[j]] = true;
    		}
    		sort(tablesQue.begin(),tablesQue.end()); //按照编号从小到大排序
    	}
    	//return true;
        
        //如何退出循环
		if(i == n && pairsQue.empty()) //说明所有玩家都可以顺利玩完游戏
            break;
                       
        while(!tablesQue.empty() && !pairsQue.empty()){
            int a = VipInTablesQue();
            if(a != -1){ //队列中有VIP桌子
                int b = VipInPairsQue();
                if(b != -1){ //队列中有VIP
                    Update(a,b);
                }else{
                    //Update(a,0);
                    Update(0,0);
                }
            }else{
                Update(0,0);
            }
        }
    }
    
    sort(pairs2.begin(),pairs2.end(),Comp2); //按照serveTime排序
    for(i=0; i<pairs2.size();i++){
        int a = pairs2[i];
        if(pairs[a].serveTime != 0){
            int time1 = pairs[a].arriveTime;
            int time2 = pairs[a].serveTime;
            //int h1 = time1/3600, m1 = (time1-time1/3600)/60, s1 = (time1-time1/3600)%60;
            //int h2 = time2/3600, m2 = (time2-time2/3600)/60, s2 = (time2-time2/3600)%60;
            int h1 = time1/3600, m1 = (time1%3600)/60, s1 = (time1%3600)%60;
            int h2 = time2/3600, m2 = (time2%3600)/60, s2 = (time2%3600)%60;
            printf("%02d:%02d:%02d %02d:%02d:%02d %.0f\n",h1,m1,s1,h2,m2,s2,round((time2-time1)/60.0));
        }
    }
    for(i=1; i<=k; i++){
        if(i == 1){
            printf("%d",tables[i].servePairNum);
        }else{
            printf(" %d",tables[i].servePairNum);
        }
    }
}
```

还是有两个测试点没过

错误分析：

1. 逻辑错误：如何退出循环，117和150行。
2. 细节错误：119行, 142行

```cpp
//v2.1
/*
基于迭代客户数组的方法，业务逻辑比v1复杂，但是退出循环的逻辑比v1简单。
遍历一个player，并且取出所有table中最早pop的，然后分情况：(vip,vip)(vip,no vip)(n0 vip,vip)(np vip,vip)
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<queue>
#include<vector>
#include<algorithm>
#include<cmath>
using namespace std;

struct Table{
    int popTime;
    bool vip;
    int serveNum;
    Table(){popTime = 8*3600; vip = false; serveNum = 0;}
};
struct Player{
    int arriveTime;
    int pTime;
    int startTime;
    bool vip;
    bool played; //只针对vip=true的player
    Player(){startTime = -1; played = false;}
};

const int MAXN = 1e4+5;
const int MAXK = 105;
const int INF = 1e8+5;
int n,k;

Player players[MAXN]; //0--n-1
Table tables[MAXK]; //1-n

bool Comp(Player a, Player b){
    return a.arriveTime < b.arriveTime;
}

bool Comp2(Player a, Player b){
    return a.startTime < b.startTime;
}

void Update(int pindex, int tindex){ //更新players[pindex],tables[tindex]
    players[pindex].startTime = max(players[pindex].arriveTime, tables[tindex].popTime);
    players[pindex].played = true;
    tables[tindex].popTime = players[pindex].startTime + min(players[pindex].pTime, 7200); //tables[tindex].popTime = players[pindex].arriveTime + players[pindex];
    tables[tindex].serveNum++;
}

int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        players[i] = Player();
    }
    for(int i=0; i<MAXK; i++){
        tables[i] = Table();
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int hh,mm,ss,time,flag;
    for(int i=0; i<n; i++){
        scanf("%d:%d:%d %d %d",&hh,&mm,&ss,&time,&flag);
        players[i].arriveTime = hh*3600 + mm*60 + ss;
        players[i].pTime = time*60;
        players[i].vip = flag == 1 ? true : false;
    }
    sort(players, players+n, Comp);
    int m;
    scanf("%d %d",&k,&m);
    for(int i=0; i<m; i++){
        int a;
        scanf("%d",&a);
        tables[a].vip = true;
    }

    int i = 0;
    while(i < n){
        if(players[i].arriveTime >= 21*3600)
            break;
        int minpop = INF, mintable;
        for(int j=1; j<=k; j++){
            if(tables[j].popTime < minpop){
                minpop = tables[j].popTime;
                mintable = j;
            }
        }
        if(minpop >= 21*3600) //if(minpop >= 17*3600)
            break;
        if(players[i].vip && tables[mintable].vip){
            if(players[i].played == false)
                Update(i, mintable);
            i++;
        }else if(players[i].vip && !tables[mintable].vip){
		    //要考虑table.poptime < player.arriveTime的情况，此时table和player之间可能夹杂着
            //VIP table，例如table table_vip player，此时要把player和table_vip匹配
            if(players[i].played == false){
                /*int vipTableIndex = -1;
                for(int j=1; j<=k; j++){ //for(int j=mintable+1; j<=k; j++)
                    if(tables[j].vip && tables[j].popTime <= players[i].arriveTime){
                        vipTableIndex = j;
                        break;
                    }
                }
                if(vipTableIndex != -1){
                    Update(i, vipTableIndex);
                }else{
                    Update(i, mintable);
                }*/
                //上面的写法是错的。考虑一个例子：table1在1点空闲，table3_vip在2点空闲, table4_vip在2点空闲，table2_vip在3点空闲，player1在4点到来
                //若按照上面的写法，则player1和table2_vip匹配，是错误的，player1应该和table3_vip匹配，也就是：player1应该找目前最早pop的VIP且编号最小的VIP table
                //在原文的体现是：When a VIP table is open, the first VIP pair in the queue will have the priviledge to take it
                //说实话，这个条件是在太隐形了，不考虑也没有关系。
                int vipTableIndex = -1, minVipTablePop = INF;
                for(int j=1; j<=k; j++){
                    if(tables[j].vip && tables[j].popTime < minVipTablePop){
                        vipTableIndex = j;
                        minVipTablePop = tables[j].popTime;
                    }
                }
                if(vipTableIndex != -1 && tables[vipTableIndex].popTime <= players[i].arriveTime){
                    Update(i, vipTableIndex);
                }else{
                    Update(i, mintable);
                }
            }
            i++;
        }else if(!players[i].vip && tables[mintable].vip){
            /*int vipIndex = -1;
            for(int j=i+1; j<n; j++){
                if(players[j].vip && players[j].arriveTime<=tables[mintable].popTime && players[j].played==false){

                }
            }*/
            if(players[i].arriveTime >= tables[mintable].popTime){
                Update(i, mintable); //Update(i, minpop)
                i++;
            }else{
                int vipIndex = -1;
                for(int j=i+1; j<n; j++){
                    if(players[j].arriveTime > tables[mintable].popTime)
                        break;
                    if(players[j].vip && players[j].played == false){
                        vipIndex = j;
                        break;
                    }
                }
                if(vipIndex != -1){
                    Update(vipIndex, mintable);
                }else{
                    Update(i, mintable);
                    i++;
                }
            }
        }else{
            Update(i, mintable);
            i++;
        }
    }

    sort(players, players+n, Comp2);
    for(i=0; i<n; i++){
        if(players[i].startTime != -1){
            int time1 = players[i].arriveTime;
            int time2 = players[i].startTime;
            //int h1 = time1/3600, m1 = (time1-time1/3600)/60, s1 = (time1-time1/3600)%60;
            //int h2 = time2/3600, m2 = (time2-time2/3600)/60, s2 = (time2-time2/3600)%60;
            int h1 = time1/3600, m1 = (time1%3600)/60, s1 = (time1%3600)%60;
            int h2 = time2/3600, m2 = (time2%3600)/60, s2 = (time2%3600)%60;
            printf("%02d:%02d:%02d %02d:%02d:%02d %.0f\n",h1,m1,s1,h2,m2,s2,round((time2-time1)/60.0));
        }
    }
    for(i=1; i<=k; i++){
        if(i == 1){
            printf("%d",tables[i].serveNum);
        }else{
            printf(" %d",tables[i].serveNum);
        }
    }
}
```

错误分析：

1. 细节错误：48行，90行

# A1029 Median

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXN = 2e5+5;
int n1,n2;
int num[2*MAXN];

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n1);
    for(int i=1; i<n1+1; i++){
        scanf("%d",&num[i]);
    }
    scanf("%d",&n2);
    for(int i=n1+1; i<n1+n2+1; i++){
        scanf("%d",&num[i]);
    }
    sort(num+1,num+n1+n2+1);
    printf("%d",num[(1+n1+n2)/2]);
}

```

# A1030 Travel Plan

```cpp
#include<cstdio>
#include<vector>
#include<cstring>
#include<algorithm>
using namespace std;

const int MAXV = 510;
const int INF = 1e9;

int n, m, st, ed; //城市个数，高铁（无向边）个数，起点，终点
int city1, city2, dist, cost;
int G[MAXV][MAXV] = { INF }, Cost[MAXV][MAXV] = { 0 }; //邻接表，花费表
int Dist[MAXV] = { INF }; //最短距离表
vector<int> Pre[MAXV]; //前驱节点表

int optCost = INF; //最优cost
vector<int> path, tempPath; //cost度量下最优路径，临时路径

void Dijkstra(int s) {
	/*s是起点，找所有最短路径，以前驱表的形式输出:Pre*/
	bool vis[MAXV] = { false }; //结点是否被访问过
    fill(Dist, Dist + MAXV, INF);
    Dist[s] = 0;
	
    for(int k=0;k<n;k++){
		int u = -1; //正在访问的结点
		int min = INF;
		for (int i = 0; i < n; i++) {
			if (vis[i] == false && Dist[i] < min) {
				u = i;
				min = Dist[i];
			}
		}
		if (u == -1) return; //结束
    	vis[u] = true;
		for (int i = 0; i < n; i++) {
			if (vis[i] == false && G[u][i] != INF) { //如果没有被访问且可到达
				if (Dist[u] + G[u][i] < Dist[i]) { //如果经过u的路径较优
					Dist[i] = Dist[u] + G[u][i];
					Pre[i].clear();
					Pre[i].push_back(u);
				}
				else if (Dist[u] + G[u][i] == Dist[i]) { //如果经过u的路径与原来的一样优
					Pre[i].push_back(u);
				}
			}
		}
    }
}

void DFS(int v) { //v为当前结点
	/*输出在optCost,path*/
	if (v == st) { //如果到达叶子结点
		tempPath.push_back(v); //将当前结点放到tempPath最后

		int value = 0; //计算二次度量标准cost
		int pre = tempPath[tempPath.size() - 1];
		for (int i = tempPath.size() - 2; i >= 0; i--) {
			value += Cost[pre][tempPath.at(i)];
			pre = tempPath.at(i);
		}

		if (value < optCost) { //如果这个路径的较优
			optCost = value; //更新optCost
			path = tempPath; //更新最优路径
		}
		tempPath.pop_back();
		return;
	}
	tempPath.push_back(v);
	for (int i = 0; i < Pre[v].size(); i++) {
		DFS(Pre[v].at(i));
	}
	tempPath.pop_back();
}

int main() {
	//初始化
	fill(G[0], G[0] + MAXV * MAXV, INF);
	fill(Cost[0], Cost[0] + MAXV * MAXV, 0);

	scanf("%d%d%d%d", &n, &m, &st, &ed);
	for (int i = 0; i < m; i++) {
		scanf("%d%d%d%d", &city1, &city2, &dist, &cost);
		G[city1][city2] = G[city2][city1] = dist;
		Cost[city1][city2] = Cost[city2][city1] = cost;
	}
	Dijkstra(st);
	DFS(ed);
	for (int i = path.size() - 1; i >= 0; i--) {
		printf("%d ", path.at(i));
	}
	printf("%d %d", Dist[ed], optCost);
}
```

错误:

1. 35行，忘了更新vis数组
2. fill(Dist, Dist + MAXV, INF);而不是fill(Dist[0], Dist[0] + MAXV, INF);因为Dist[0]是数字，而不是地址
3. Dijkstra()算法中，对距离表的初始化出现问题，忘了加23行那段代码。
4. 56行定义的value必须初始化，否则value的初值不一定。

# A1032 Sharing

```cpp
#include<cstdio>
using namespace std;

struct Node{
    char data;
    int next;
    bool flag; //表示第一条链表是否占用过这个结点
};

const int MAXN = 1e5+5;
int addr1,addr2,n; //首地址1，首地址2，n行
int addr,next; //结点地址，下一个结点地址，next=-1表示NULL
char data; //数据
Node nodes[MAXN]; //存放结点
int p; //存放迭代的地址

int main(){
    //初始化
    for(int i=0;i<MAXN;i++){
        nodes[i].flag = false;
    }
    
    scanf("%d%d%d",&addr1,&addr2,&n);
    for(int i=0;i<n;i++){
        scanf("%d %c %d",&addr,&data,&next);
        nodes[addr].data = data;
        nodes[addr].next = next;
    }
    for(p=addr1;p!=-1;p=nodes[p].next){
        nodes[p].flag = true;
    }
    for(p=addr2;p!=-1;p=nodes[p].next){
        if(nodes[p].flag == true)
            break;
    }
    if(p!=-1)
        printf("%05d\n",p);
    else
        printf("-1\n");
    
    return 0;
}
```

错误分析：

1. 37行，没有按照5位数字的格式输出



# A1033 To Fill or Not to Fill

```cpp
/*
基本思想：贪心方法。
*/
#include<cstdio>
#include<cmath>
#include<algorithm>
#include<vector>
using namespace std;

struct Station{
    double p; //单价
    int d; //距离杭州的距离
};

const int MAXN = 505;
int cmax, d, darg, n; //最大容量，与终点的距离，每单位的油气跑多远，加油站个数
Station stations[MAXN];

bool Comp(Station a, Station b){
    return a.d < b.d;
}
int main(){
    //freopen("D:\\input.txt", "r", stdin);

    scanf("%d %d %d %d\n",&cmax,&d,&darg,&n);
    for(int i = 1; i <= n; i++){
        double p;
        int dist;
        scanf("%lf %d",&p,&dist);
        stations[i].p = p;
        stations[i].d = dist;
    }
    sort(stations+1,stations+n+1,Comp);

    double money = 0.0, dist = 0.0, leftGas = 0.0, maxDist = cmax*darg;
    int staNum = 1;
    while(dist < (double)d){
        //测试
        //printf("staNum=%d dist=%.2f money=%.2f leftGas=%.2f\n",staNum,dist,money,leftGas);

        if(stations[1].d != 0){
            break;
        }
        vector<int> v; //存储所能到达的站点
        for(int i=staNum+1; i<=n; i++){
            if(stations[i].d - stations[staNum].d <= maxDist){
                v.push_back(i);
            }else{
                break;
            }
        }

        if(v.size() == 0){
            if(stations[staNum].d + maxDist < d){
                dist += maxDist;
                break;
            }else{ //下一个站点就是终点
                double a = max((d - stations[staNum].d)/(double)darg - leftGas, 0.0); //需要加的油
                money += a*stations[staNum].p;
                dist = d;
            }
        }else{
            int i;
            for(i=0; i<v.size(); i++){
                if(stations[v[i]].p < stations[staNum].p)
                    break;
            }
            if(i < v.size()){ //可达的站点中有价格比现在的低的
                double a = max((stations[v[i]].d - stations[staNum].d)/(double)darg - leftGas, 0.0);
                //测试
                //printf("staNum=%d I need gas=%.2f\n",staNum,a);

                money += a*stations[staNum].p;
                leftGas = leftGas + a - (stations[v[i]].d - stations[staNum].d)/(double)darg;
                dist = stations[v[i]].d;
                staNum = v[i];
            }else{ //可达的站点中没有价格比现在低的
                 if(stations[staNum].d + maxDist >= d){ //如果能到达终点
                    double a = max((d - stations[staNum].d)/(double)darg - leftGas, 0.0);
                    money += a*stations[staNum].p;
                    dist = d;
                 }else{ //如果不能到达终点
                    double a = max(cmax - leftGas, 0.0);
                    money += a*stations[staNum].p;
                    leftGas = leftGas + a - (stations[staNum+1].d - stations[staNum].d)/(double)darg;
                    dist = stations[staNum+1].d;
                    staNum = staNum+1;
                 }
            }
        }
    }

    if(dist < (double)d){
        printf("The maximum travel distance = %.2f\n",dist);
    }else{
        printf("%.2f\n",money);
    }
}

```

错误分析：

1. 细节错误：29行。对double类型的输入用``scanf("%lf",&d)`` 而不是 ``%f`` ，否则会接受不到值

# A1034 head of gang

为什么不用邻接表法，因为邻接表法对删除边的操作不方便。一般情况下用邻接矩阵法。

```cpp
#include<cstdio>
#include<string>
#include<map>
#include<iostream>
using namespace std;

const int MAXN = 2005;
int n, k; //n行数据，k表示threshold
string name1, name2; //输入的具有联系的两个人的姓名
int time1; //两个人的联系时间
int weight[MAXN] = {0}; //存储点权
int G[MAXN][MAXN] = {0}; //邻接表
int numPerson = 0; //总人数
map<int, string> intToString; //编号到姓名
map<string, int> stringToInt; //姓名到编号
map<string, int> gang; //头目和帮会人数
bool vis[MAXN] = { false };

int change(string str) {
	/*核心函数，根据str返回对应的编号：若能找到编号，则返回；否则，插入编号-姓名对应关系，顺便更改总人数numPerson*/
	if (stringToInt.find(str) != stringToInt.end()) { //如果能在map中找到str
		return stringToInt[str]; //返回编号
	}
	else {
		stringToInt[str] = numPerson; //str的编号为总人数
		intToString[numPerson] = str; //numPerson对应str
		return numPerson++;
	}
}

void DFS(int nowVisit, int& head, int& numMember, int& totalValue) {
	/*遍历过程中，记录头目是谁，帮会总人数，总边权*/
	numMember++; //帮会人数加1
	if (weight[nowVisit] > weight[head]) //更新帮会的头目head
		head = nowVisit;
	vis[nowVisit] = true; //标记此结点已访问
	for (int i = 0; i < numPerson; i++) {
		if (G[nowVisit][i] > 0) { //如果与相邻点联系的边存在
			totalValue += G[nowVisit][i]; //更新总边权
			G[nowVisit][i] = G[i][nowVisit] = 0; //删除双向边
			if (vis[i] == false) { //如果这个点未被访问，则访问它
				DFS(i, head, numMember, totalValue);
			}
		}
	}
}

void DFSTrave() {
	/*遍历整个图，获取联通块的信息*/
	for (int i = 0; i < numPerson; i++) {
		if (vis[i] == false) { //如果这个点未被访问,则通过它获得一个帮会的信息
			int head = i, numMember = 0, totalValue = 0;
			DFS(i, head, numMember, totalValue);
			if (numMember > 2 && totalValue > k) { //如果这个cluster的总边权大于阈值
				gang[intToString[head]] = numMember; //保存这个帮会
			}
		}
	}
}

int main() {
	cin >> n >> k;
	for (int i = 0; i < n; i++) {
		cin >> name1 >> name2 >> time1;
		int id1 = change(name1);
		int id2 = change(name2);
		weight[id1] += time1;
		weight[id2] += time1;
		G[id1][id2] += time1;
		G[id2][id1] += time1;
	}
	DFSTrave();
	printf("%d\n", gang.size());
	map<string, int>::iterator it;
	for (it = gang.begin(); it != gang.end(); it++) {
		cout << it->first << " " << it->second << endl;
	}

	return 0;
}
```

错误分析：

1. 53行：gang是string 到 int的map，所以在更新gang时，要把编号换成姓名
2. 39行：更新总边权totalValue出错，将点权累加进去了
3. 54行：if条件少了，少考虑了帮会人数大于2的条件
4. 69和70行：更新邻接图时，应该将通话时间累加上去，而不是单纯地赋值，因为在输入中，可能出现相同的名字对，表示两个罪犯不同的通话记录。

# A1038 Recover the smallest number

```cpp
#include<algorithm>
#include<cstdio>
#include<string>
#include<vector>
#include<iostream>
using namespace std;

vector<string> vs;

bool Comp(string& s1, string& s2){
    return s1 + s2 < s2 + s1;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);

    int n;
    scanf("%d ",&n);
    for(int i=0; i<n; i++){
        string s;
        cin >> s;
        vs.push_back(s);
    }
    sort(vs.begin(), vs.end(), Comp);

    /*vector<string>::iterator it;
    for(it = vs.begin(); it != vs.end(); it++){
        if(it == vs.begin()){
            string s = (*it);
            int i;
            for(i=0; i<s.size(); i++){
                if(s[i] != '0')
                    break;
            }
            s = s.substr(i);
            cout << s;
        }else{
            cout << (*it);
        }
    }*/
    string result;
    for(int i=0;i<vs.size();i++){
        result += vs[i];
    }
    int i = 0;
    while(result[i] == '0')
        i++;
    result = result.substr(i);
    if(result.size() == 0){
        cout << "0";
    }else{
        cout << result;
    }
}

```

# A1039 Course List for Student

```cpp
/*
基本思路1：用map<string,vector<int>>存储每个学生的所有课程
基本思路2：由于string查询会慢，再加上题目中的学生姓名是只有4个字符的，所以用简单的hash来转换字符串
为数字，然后建立vector<int> couList[MAXN],couList[hash(nameStr)]来得到学生的课程列表
*/
```



# A1043 Is it a Binary Search Tree

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

# *A1044 Shopping in Mars

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



# A1046 Shortest Distance

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

# A1048 Find Coins

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



# A1049 Counting Ones

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

# A1052 Linked List Sorting

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

# A1053 Path of Equal Weight

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

# A1056 Mice and Rice

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

# A1060 Are They Equal

```cpp
/*
基本思路：先判断数字等于0.0还是大于0.0还是小于0.0。若大于0.0，则先去掉前导0（考虑到02.02这种
情况），再记录小数点前面有多少数字，则为指数，同时把数字一个一个输出直到达到significant digit的
数量；若小于0.0，则找到小数点后面的位置，记录小数点后面第一个非0数前面有多少个0，则为-exp，然后把后面
的数字一个一个输出，直到达到significant digit的数量
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<string>
#include<vector>
using namespace std;

int n;
char str1[200], str2[200];

void convert(char str[], vector<int>& vec, int& exp){
    double d;
    sscanf(str,"%lf",&d);
    if(d == 0.0){
        for(int i=0; i<n; i++)
            vec.push_back(0);
        exp = 0;
        return;
    }else if(d >= 1.0){
        int i = 0;
        while(str[i] == '0') //把前导0去掉
            i++;
            
        int cnt = 0; //记录已经输入了多少significant digit
        while(str[i] != '.' && str[i] != '\0'){
            if(cnt < n){
                vec.push_back(str[i]-48);
                cnt++;
            }
            exp++;
            i++;
        }
        if(str[i] == '.'){
            i++;
            while(cnt < n && str[i] != '\0'){
                vec.push_back(str[i]-48);
                i++; cnt++;
            }
        }
        while(cnt < n){
            vec.push_back(0);
            cnt++;
        }
    }else{
        //int i = 2; //小数部分从2-th开始
        int i = 0;
        while(str[i] != '.')
            i++;
        i++;
        exp = 0; //小数点后第一个非0数前，0的个数就是-exp
        while(str[i] == '0'){
            exp--;
            i++;
        }
        int cnt = 0;
        while(cnt < n && str[i] != '\0'){
            vec.push_back(str[i]-48);
            cnt++;
            i++;
        }
        while(cnt < n){
            vec.push_back(0);
            cnt++;
        }
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %s %s",&n,&str1,&str2);

    vector<int> vec1, vec2;
    int exp1, exp2;
    convert(str1, vec1, exp1);
    convert(str2, vec2, exp2);

    if(exp1 == exp2 && vec1 == vec2){
        printf("YES 0.");
        for(int i=0; i<n; i++){
            printf("%d",vec1[i]);
        }
        printf("*10^%d",exp1);
    }else{
        printf("NO 0.");
        for(int i=0; i<n; i++){
            printf("%d",vec1[i]);
        }
        printf("*10^%d 0.",exp1);
        for(int i=0; i<n; i++){
            printf("%d",vec2[i]);
        }
        printf("*10^%d",exp2);
    }
}
```

错误分析：

1. 情况考虑不全：没有考虑00.02或02.02这种情况

# A1062 Talent and Virtue

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<set>
#include<algorithm>
#include<string>
using namespace std;

struct Node{
    char id[10];
    int virtue, talent;
    int flag; //0是sage，1是nobleman, 2是foolman, 3是small man, 4表示不考虑
};
const int MAXN = 1e5+5;
int n,l,h;
Node persons[MAXN];
int persons2[MAXN];
int res;

bool Comp(int a, int b){
    if(persons[a].flag != persons[b].flag){ //
        return persons[a].flag < persons[b].flag;
    }else if(persons[a].talent + persons[a].virtue != persons[b].talent + persons[b].virtue){
        return (persons[a].talent + persons[a].virtue) > (persons[b].talent + persons[b].virtue);
    }else if(persons[a].virtue != persons[b].virtue){
        return persons[a].virtue > persons[b].virtue;
    }else{
        return string(persons[a].id) < string(persons[b].id);
    }
}
int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        persons2[i] = i;
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d",&n,&l,&h);
    res = n;
    for(int i=0; i<n; i++){
        scanf("%s %d %d",&persons[i].id,&persons[i].virtue,&persons[i].talent);
        if(persons[i].virtue >= h && persons[i].talent >= h){
            persons[i].flag = 0;
        }else if(persons[i].virtue >= h && persons[i].talent >= l && persons[i].talent < h){
            persons[i].flag = 1;
        }else if(persons[i].virtue >= l && persons[i].virtue < h && persons[i].talent >= l && persons[i].talent < h && persons[i].virtue >= persons[i].talent){
            persons[i].flag = 2;
        }else if(persons[i].virtue < l || persons[i].talent < l){
            persons[i].flag = 4;
            res--;
        }else{
            persons[i].flag = 3;
        }
    }
    sort(persons2, persons2+n, Comp);

    printf("%d\n",res);
    for(int i=0; i<n; i++){
        int index = persons2[i];
        if(persons[index].flag < 4){
            printf("%s %d %d\n",persons[index].id,persons[index].virtue,persons[index].talent);
        }else{
            break;
        }
    }
}
```

错误分析：

1. 没有搞清楚排序逻辑

# A1063 Set Similarity

```cpp
/*
基本思想：将两个set合并为1个set，算总元素数（时间复杂度高）；用双指针法算两个set的共同distinct元素
数。
基本思想2：算总元素数也可以用双指针法
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<set>
#include<algorithm>
using namespace std;

const int MAXN = 51;
int n, k; //1-n
set<int> sets[MAXN];

double getRate(int a, int b){
    int Nc = 0, Nt = 0;
    /*set<int> temp;
    temp.insert(sets[a].begin(), sets[a].end());
    temp.insert(sets[b].begin(), sets[b].end());
    Nt = temp.size();*/

    set<int>::iterator ita = sets[a].begin(), itb = sets[b].begin();
    while(ita != sets[a].end() && itb != sets[b].end()){
        Nt++;
        if((*ita) == (*itb)){
            Nc++;
            ita++; itb++;
        }else if((*ita) < (*itb)){
            ita++;
        }else if((*ita) > (*itb)){
            itb++;
        }
    }
    if(ita != sets[a].end()){
        while(ita != sets[a].end()){
            Nt++;
            ita++;
        }
    }else if(itb != sets[b].end()){
        while(itb != sets[b].end()){
            Nt++;
            itb++;
        }
    }
    return Nc/(1.0*Nt);
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int m, x;
    for(int i=1; i<=n; i++){
        scanf("%d",&m);
        for(int j=0; j<m; j++){
            scanf("%d",&x);
            sets[i].insert(x);
        }
    }
    scanf("%d",&k);
    int a, b;
    for(int i=0; i<k; i++){
        scanf("%d %d",&a,&b);
        double rate = getRate(a, b);
        rate *= 100;
        printf("%.1f%%\n",rate);
    }
}
```

错误分析：

1. 超时：17-20行很高复杂度

# A1064 Binary Search Tree

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思路：考BST和完全二叉树，分治法：先将输入序列排序，再按照完全二叉树的性质来找到这段序列的根是什么，
然后对根左边的序列执行上述过程，根右边的序列执行上述过程。
如何找根：对于输入0 1 2 3 4 5 6 7 8 9。有7（8-1，且16-1>10）个元素是在树的前7号结点，且组成满
二叉树，然后将剩下的3个元素从左到右放到满二叉树的下一层。则有(4-1)+3个元素在最终完全BST的左边，有
(4-1)+0个元素在右边，所以根元素是输入序列的第(4-1)+3+1个元素
*/
#include<cstdio>
#include<queue>
#include<algorithm>
using namespace std;

struct Node{
    int data;
    Node *lchild, *rchild;
    Node(int _data){data = _data; lchild = rchild = NULL;}
};
const int MAXN = 1005;
int n;
int num[MAXN];

int findRoot(int left, int right){
    int len = right - left + 1;
    int a = 1, b, c = 2; //10 = 2^a -1 + b, c = 2^a则a = 3, b = 3, c = 8
    while(2*c - 1 <= len){
        a++;
        c = c*2;
    }
    b = len - (c-1);

    if(b <= c/2){ 
        return left + c/2 - 1 + b;
    }else{
        return left + c - 1;
    }
}
void CreateBST(int left, int right, Node* &root){
    if(left == right){
        Node* node = new Node(num[left]);
        root = node;
        return;
    }else if(left > right){ //
        root = NULL;
        return;
    }
    int index = findRoot(left, right);
    Node* node = new Node(num[index]);
    root = node;
    CreateBST(left, index-1, root->lchild);
    CreateBST(index+1, right, root->rchild);
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%d",&num[i]);
    }
    sort(num, num+n);

    Node* root = NULL;
    CreateBST(0, n-1, root);

    //level order
    queue<Node*> q;
    q.push(root);
    while(!q.empty()){
        Node* node = q.front(); q.pop();
        if(node == root){
            printf("%d",node->data);
        }else{
            printf(" %d",node->data);
        }
        if(node->lchild)
            q.push(node->lchild);
        if(node->rchild)
            q.push(node->rchild);
    }
}
```

# A1065 A+B and C

```cpp
/*
基本思想：不能通过输入double来直接比较大小，因为double对大整数的表示有误差，所以只能通过输入long long
的方式来比较：判断a+b是否大于c。这里需要注意的是a+b可能溢出，如果a大于0且b大于0，
但相加得到的却是小于等于0的说明是正溢出，这时肯定比c大（因为c肯定在long long的范围内）。
如果a小于0且b小于0，但相加得到的却是大于等于0的说明是负溢出，这是肯定比c小。
其他情况就和平常计算一样。这里还要注意a+b要赋值给一个变量再和c比较。
*/
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
using namespace std;

int n;
long long a, b, c;

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%lld %lld %lld",&a,&b,&c);
        long long temp = a + b;
        if(a > 0 && b > 0 && temp <= 0){
            printf("Case #%d: true\n",i+1);
        }else if(a < 0 && b < 0 && temp >= 0){
            printf("Case #%d: false\n",i+1);
        }else if(temp > c){
            printf("Case #%d: true\n",i+1);
        }else if(temp <= c){
            printf("Case #%d: false\n",i+1);
        }
    }
}
```

# A1066 Root of AVL Tree

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
#include<cmath>
using namespace std;

struct Node{
    int height;
    int data;
    Node *left, *right;
    Node(int _data){data = _data; height = 1; left = right = NULL;}
    int getBalanceFact(){
        int leftHeight = left ? left->height : 0;
        int rightHeight = right ? right->height : 0;
        return leftHeight - rightHeight;
    }
    void updateHeight(){
        int leftHeight = left ? left->height : 0;
        int rightHeight = right ? right->height : 0;
        height = max(leftHeight, rightHeight)+1;
    }
};

int n;
Node* root = NULL;

void R(Node* &root){
    if(root->left->right){
        Node* temp = root->left;
        root->left = temp->right;
        temp->right = root;
        root = temp;
    }else{
        Node* temp = root->left;
        temp->right = root;
        root->left = NULL;
        root = temp;
    }
    //root->left->updateHeight();
    root->right->updateHeight();
    root->updateHeight();
}
void L(Node* &root){
    if(root->right->left){
        Node* temp = root->right;
        root->right = temp->left;
        temp->left = root;
        root = temp;
    }else{
        Node* temp = root->right;
        temp->left = root;
        root->right = NULL;
        root = temp;
    }
    //root->right->updateHeight();
    root->left->updateHeight();
    root->updateHeight();
}
void Insert(int x, Node* &root){
    if(root == NULL){
        Node* node = new Node(x);
        root = node;
        return;
    }
    if(x < root->data){
        Insert(x, root->left);
    }else{
        Insert(x, root->right);
    }
    root->updateHeight();
    int balanceFact = root->getBalanceFact();
    if(balanceFact > 1 && root->left->getBalanceFact() > 0){ //LL
        R(root);
    }else if(balanceFact > 1 && root->left->getBalanceFact() < 0){ //LR
        L(root->left);
        R(root);
    }else if(balanceFact < -1 && root->right->getBalanceFact() < 0){ //RR
        L(root);
    }else if(balanceFact < -1 && root->right->getBalanceFact() > 0){ //RL
        R(root->right);
        L(root);
    }
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int x;
    for(int i=0; i<n; i++){
        scanf("%d",&x);
        Insert(x, root);
    }
    printf("%d",root->data);
}

```

# *A1067 Sort With Swap(0,1)

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思想：
（1）. 若0在0-th位置，则若剩下的数字都排序好，则不用交换，
若剩下的位置未排序好，则和任意一个不在其位置的而数字交换，cnt++；
（2）. 接下来考虑0不在0-th的情况该怎么办，若zeroPos与num[0]不同，例如:
0 1 2 3 4
1 3 4 0 2
则直接将zeroPos与0交换，直到zeroPos与num[0]相同，例如：
0 1 2 3 4
1 0 4 3 2
（3）. 则看除了0与zeroPos外是否是否有数字不在其位置，若有，
则0与其交换，并回到2，否则，则交换一次就结束了
*/
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n;
int num[MAXN];
int zeroPos; //数字0所在的位置
int cnt = 0;

int swap2(int a){ //0和a交换
    for(int i=0; i<n; i++){
        if(num[i] == a){
            num[i] = 0;
            num[zeroPos] = a;
            return i;
        }
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%d",&num[i]);
        if(num[i] == 0)
            zeroPos = i;
    }
    if(zeroPos == 0){
        int i = 1;
        while(i < n && num[i] == i)
            i++;
        if(i < n){
            int temp = num[i];
            num[i] = 0;
            num[0] = temp;
            cnt++;
            zeroPos = i;
        }
    }
    if(zeroPos != 0){
        while(true){
            while(num[0] != zeroPos){
                zeroPos = swap2(zeroPos);
                cnt++;
            }
            int i = 0;
            while(i < n){
                if(i!=0 && i != zeroPos && num[i]!=i)
                    break;
                i++;
            }
            if(i < n){
                int temp = num[i];
                num[i] = num[zeroPos];
                num[zeroPos] = temp;
                zeroPos = i;
                cnt++;
            }else{
                cnt++;
                break;
            }
        }
    }else{
        cnt = 0;
    }

    printf("%d",cnt);
}
```

错误分析：

1. 超时：将两个数交换，需要遍历整个数组，来找到两个数的位置，总的时间复杂度O(n*n)

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思想：
（1）. 若0在0-th位置，则若剩下的数字都排序好，则不用交换，
若剩下的位置未排序好，则和任意一个不在其位置的而数字交换，cnt++；
（2）. 接下来考虑0不在0-th的情况该怎么办，若zeroPos与num[0]不同，例如:
0 1 2 3 4
1 3 4 0 2
则直接将zeroPos与0交换，直到zeroPos与num[0]相同，例如：
0 1 2 3 4
1 0 4 3 2
（3）. 则看除了0与zeroPos外是否是否有数字不在其位置，若有，
则0与其交换，并回到2，否则，则交换一次就结束了
*/
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n;
int num[MAXN];
int num2[MAXN];
int zeroPos; //数字0所在的位置
int cnt = 0;

void swap2(int index){
    int temp = num[index];
    num[index] = num[zeroPos];
    num[zeroPos] = temp;
}
void swap3(int index1, int index2){
    int temp = num2[index1];
    num2[index1] = num2[index2];
    num2[index2] = temp;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%d",&num[i]);
        num2[num[i]] = i;
        if(num[i] == 0)
            zeroPos = i;
    }
    if(zeroPos == 0){
        int i = 1;
        while(i < n && num[i] == i)
            i++;
        if(i < n){
            swap2(i);
            swap3(num[zeroPos], num[i]);
            cnt++;
            zeroPos = i;
        }
    }
    if(zeroPos != 0){
        while(true){
            while(num[0] != zeroPos){
                int index = num2[zeroPos];
                swap2(index);
                swap3(num[zeroPos],num[index]);
                cnt++;
                zeroPos = index;
            }
            int i = 0;
            while(i < n){
                if(i!=0 && i != zeroPos && num[i]!=i)
                    break;
                i++;
            }
            if(i < n){
                swap2(i);
                swap3(num[zeroPos],num[i]);
                zeroPos = i;
                cnt++;
            }else{
                cnt++;
                break;
            }
        }
    }else{
        cnt = 0;
    }

    printf("%d",cnt);
}

```

修改的，还是超时

# A1068 Find More Coins

```cpp
/*
这题就是0/1背包问题
*/
#include<cstdio>
#include<vector>
#include<algorithm>
#include<cmath>
using namespace std;

const int MAXN = 10005; //有多少种硬币
const int MAXM = 105; //要付的钱

int n,m;
int dp[MAXN][MAXM] = {0}; //dp[i][j]表示考虑前i个物品,背包剩余容量为j, 则最大效益为
int value[MAXN];

bool Comp(int a,int b){
    return a > b;
}
int main(){
    freopen("D:\\input.txt","r",stdin);

    scanf("%d %d\n",&n,&m);
    for(int i=1; i<=n; i++){
        scanf("%d",&value[i]);
    }
    sort(value+1,value+n+1,Comp);

    //初始化
    for(int j=value[1]; j<=m; j++){
        dp[1][j] = value[1];
    }
    //动态规划
    for(int i=2; i<=n; i++){
        for(int j=value[i]; j<=m; j++){
            dp[i][j] = max(dp[i-1][j], dp[i-1][j-value[i]] + value[i]);
        }
    }

    //找出路径
    int num = dp[n][m], i = n, j = m;
    vector<int> path; //保存硬币的下标
    if(num != m){
        printf("No Solution\n");
    }else{
        while(i >= 2 && num != 0){
            if(num >= value[i] && dp[i-1][num-value[i]] + value[i] == num){ //第i个硬币是可以考虑的
                path.push_back(i);
                num = num - value[i];
            }
            i--;
        }
        if(num != 0)
            path.push_back(i);

        vector<int>::iterator it;
        for(it = path.begin(); it != path.end(); it++){
            if(it == path.begin()){
                printf("%d",(value[*it]));
            }else{
                printf(" %d",(value[*it]));
            }
        }
    }
}

```

# A1073 Scientific Notation

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
using namespace std;

const int MAXN = 10005;
char sci[MAXN];
char result[MAXN];

void erase_n(){
    int i = 0;
    while(sci[i] != '\n' && sci[i] != '\0')
        i++;
    sci[i] = '\0';
}

int getExp(){ //得到指数
    int i = 0;
    while(sci[i] != 'E')
        i++;
    i++;

    char exp[10];
    int j = 0, res;
    while(sci[i] != '\0'){
        exp[j] = sci[i];
        j++; i++;
    }
    sscanf(exp,"%d",&res);
    return res;
}
int main(){
    freopen("D:\\input.txt","r",stdin);
    fgets(sci,MAXN,stdin);

    int exp = getExp(), i=0, j=0; //i是sci的指针，j是result的指针
    //处理正负号
    if(sci[0] == '+'){
        result[0] = '\0';
        i++;
    }else if(sci[0] == '-'){
        result[j] = '-';
        j++;
        i++;
    }else{
        result[0] = '\0';
    }
    //处理指数问题
    if(exp < 0){
        //如果指数小于0，则先压入exp个0，压入第一个0后压入.
        for(int k=0; k<-exp; k++){
            result[j] = '0';
            if(k == 0){
                result[++j] = '.';
                ++j;
            }else{
                ++j;
            }
        }
        //压入其他数字部分，例如对于+1.23400E-03就压入123400
        while(sci[i] != 'E'){
            if(sci[i] != '.'){
                result[j] = sci[i];
                j++;
            }
            i++;
        }
    }else if(exp > 0){
        //如果指数大于0
        int a = 0; //记录小数点后输出了几位数字，对于-1.2E+10, 小数点后输出了1位数字，则再输出10-1=9个0
        bool flag = false; //记录是否在输出小数点后的数
        while(sci[i] != 'E'){
            if(sci[i] != '.'){
                result[j] = sci[i];
                j++;
                if(flag)
                    ++a;
            }else{
                flag = true;
            }
            if(a == exp && sci[i+1] != 'E'){ //判断输出.的逻辑
                result[j++] = '.';
            }
            i++;
        }
        for(int k=0; k<exp-a; k++){
            result[j++] = '0';
        }
    }
    result[j] = '\0';

    //输出
    printf("%s\n",result);
}

```

错误分析
细节错误：23行，是sci[i] != '\0'  而不是 i != '\0'; 51行，k<-exp 而不是 k<exp

# A1076 Forwards on Weibo

```cpp
#include<cstdio>
#include<vector>
#include<queue>
#include<cstring>
using namespace std;

struct Node {
	int id;
	int layer;
};

const int MAXV = 1005;
int n, l; //用户数，最大层数
vector<Node> Adj[MAXV]; //如何初始化
bool inq[MAXV] = { false };

int BFS(int s, int maxL) {
	/*若s发送一个消息，不超过maxL层的用户有多少个*/
	int numForwards = 0; //最大潜在转发量，只考虑maxL层的
	Node start; //初始结点
	Node v; //后续结点
	start.id = s;
	start.layer = 0;

	queue<Node> q;
	q.push(start); //将初始结点压入队列
	inq[start.id] = true;
	while (!q.empty()) {
		start = q.front(); //出队列
		q.pop();
		for (int i = 0; i < Adj[start.id].size(); i++) { //考虑start结点的下一层所有结点
			v = Adj[start.id].at(i);
			v.layer = start.layer + 1;
			if (inq[v.id] == false && v.layer <= maxL) { //如果v未被访问，且层数未超过maxL     
				q.push(v);
				inq[v.id] = true;
                numForwards++; //更新最大潜在转发量
			}
		}
	}
	return numForwards;
}

int main() {
	scanf("%d%d", &n, &l);
	Node user; //i号用户
	int num; //i号用户的关注数量
	int follow; //关注的用户的编号
	for (int i = 1; i <= n; i++) {
		user.id = i;
		scanf("%d", &num);
		for (int j = 0; j < num; j++) {
			scanf("%d", &follow);
            Adj[follow].push_back(user);
		}
	}
	int numQuery, s, numForwards; //要问的用户数，用户编号
	scanf("%d", &numQuery);
	for (int i = 0; i < numQuery; i++) {
        memset(inq,false,sizeof(inq)); //inq数组初始化
		scanf("%d", &s);
		numForwards = BFS(s, l);
		printf("%d\n", numForwards);
	}
}
```

错误分析：

1. 59行，由于inq是隶属于BFS函数的全局变量，所以每次运行BFS之前都要初始化inq。

# A1080 Graduate Admission

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<algorithm>
using namespace std;

const int MAXN = 40005;
const int MAXM = 105;
const int MAXK = 5;

struct School{
    int quota;
    vector<int> studs; //保存接受的学生的编号
};
struct Student{
    int ge, gi;
    vector<int> schls; //志愿
};
int n,m,k;
School schools[MAXM];
Student students[MAXN];
int students2[MAXN]; //初始为{0,1,2...,n-1}

bool Comp(int a, int b){
    int aFinal = students[a].ge + students[a].gi;
    int bFinal = students[b].ge + students[b].gi;
    if(aFinal == bFinal){
        //return students[a].gi < students[b].gi;
        return students[a].ge > students[b].ge;
    }
    return aFinal > bFinal;
}

int main(){
    //初始化
    for(int i=0; i<MAXN; i++){
        students2[i] = i;
    }

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d\n",&n,&m,&k);
    for(int i=0; i<m; i++){
        scanf("%d",&schools[i].quota);
    }
    for(int i=0; i<n; i++){
        scanf("%d %d",&students[i].ge,&students[i].gi);
        int a;
        for(int j=0; j<k; j++){
            scanf("%d",&a);
            students[i].schls.push_back(a);
        }
    }
    sort(students2,students2+n,Comp);

    for(int i=0; i<n; i++){
        int index = students2[i];
        for(int j=0; j<k; j++){
            int schlIndex = students[index].schls[j];
            if(schools[schlIndex].quota != 0){
                schools[schlIndex].studs.push_back(index);
                schools[schlIndex].quota--;
                break; //这个学生已经被录取
            }else{
                int index2 = schools[schlIndex].studs.back();
                if(students[index].ge == students[index2].ge && students[index].gi == students[index2].gi){ //两人排名一样
                    schools[schlIndex].studs.push_back(index);
                    break;
                }
            }
        }
    }

    //输出
    for(int i=0; i<m; i++){
        sort(schools[i].studs.begin(),schools[i].studs.end());
        for(int j=0; j<schools[i].studs.size();j++){
            if(j == 0){
                printf("%d",schools[i].studs[j]);
            }else{
                printf(" %d",schools[i].studs[j]);
            }
        }
        printf("\n");
    }
}

```

# A1082 Read Number in Chinese

```cpp
#include<cstdio>
#include<cstring>
#include<queue>
#include<string>
#include<iostream>
using namespace std;

char s[15];
queue<string> q; //把要输出的字符串（负，万，二之类的）入队
string bit[10] = { string(""),string("Shi"),string("Bai"),string("Qian"),string("Wan"),string(""),string(""),string(""),string("Yi") };
string bit2[10] = { string("ling"),string("yi"),string("er"),string("san"),string("si"),string("wu"),string("liu"),string("qi"),string("ba"),string("jiu") };

int main() {
	//scanf("%s", &s);
	cin >> s;

	int begin, end; //s中数字的开始下标，末尾下标
	end = strlen(s) - 1;
	if (s[0] < 48 || s[0]>57) { //是负数
		q.push(string("Fu"));
		begin = 1;
	}
	else {
		begin = 0;
	}

	//int cur1 = begin, cur2 = (end - cur1 + 1) % 4 + cur1 - 1; //考虑的一个区间，如123456789中1算一个区间，2345算一个区间
	int cur1 = begin, cur2 = (end-cur1+1)/4!=0 ? ((end-cur1+1)%4 != 0 ? (end - cur1 + 1) % 4 + cur1 - 1 : cur1+4-1) : end; //考虑的一个区间，如1 2345 6789中1算一个区间，2345算一个区间; 2000 0000中2000算一个区间
    bool zero = false; //在遇到这种情况：1 0000 0100。第二个区间（处理完时，不做什么），标识zero=true，意思是要考虑到前面0000的情况
	while (true) { //循环，处理完所有区间后推出循环,每个循环都处理一个区间
		int i = cur1; 
		while (i <= cur2) { //处理这个区间内应该输出什么。比如1022 0001 的第一个区间输出"yi Qian ling er Shi er"
			if (s[i] == 48) { //如果数字为0
				while (s[i] == 48 && i != cur2) {//s[i]为0并且未到达区间的末尾
					i++;
				}
				if (s[i] != 48 && i != cur2) { //s[i]不为0且s[i]不在末尾
                    if(!zero){
						q.push("ling");
                    }else{
                        q.push("ling");
						zero = false;
                    }
					q.push(bit2[s[i] - 48]); //比如，将"five"压进去
					q.push(bit[cur2 - i]); //比如，将"Bai"压进去
				}
				else if (s[i] == 48 && i == cur2) { //s[i]为0且在区间末尾。有4种情况：0000,1000,1010,0,只有第一种情况，考虑下一个区间第一次入队时，不管什么情况，先入个"ling"，且这个"ling"覆盖下一区间可能入的"ling"
					if (cur2 - cur1 == 3 && s[cur1] - 48 == 0 && s[cur1 + 1] - 48 == 0 && s[cur1 + 2] - 48 == 0 && s[cur1 + 3] - 48 == 0) {
						//如果区间长度为4，且区间内数字都为0
						zero = true;
					}else if(cur1==cur2){
                        q.push("ling");
                    }
				}
				else { //如果s[i]不为0且在末尾
                    if(!zero){
                        q.push("ling");
                    }
                    else{
                        q.push("ling");
                        zero = false;
                    }
					q.push(bit2[s[i] - 48]);
				}
				i++;
			}
			else { //如果数字不为0
                if(zero){
                    q.push("ling");
                    zero = false;
                }
				q.push(bit2[s[i] - 48]);
				if (bit[cur2 - i] != string(""))//如果不是在区间的个位
					q.push(bit[cur2 - i]);
				i++;
			}
		}
		//输出这个区间的位.如1000 0000 0800的第一个区间的位就是"Yi"，但是第二个区间不输出"Wan"
		if (bit[end - cur2] != string("") && zero==false)//如果不是在区间的个位且区间内不是全为0
			q.push(bit[end - cur2]);

		if (cur2 == end) { //如果处理完最后一个区间
			break;
		}
		else {
			cur1 = cur2 + 1;
			cur2 = cur1 + 4 - 1;
		}
	}

	if (!q.empty()) {
		cout << q.front();
		q.pop();
	}
	while (!q.empty()) {
		cout << " " << q.front();
		q.pop();
	}
	return 0;
}
```

错误分析：

1. 没有考虑2000 0000的情况，第一，输出了"er Qian Wan ling"，错误的；第二，把初始区间搞错成[0,-1]，其实是[0,3]
2. 把%和/符号写混了。
3. 考虑输入为0的情况

# A1085 Perfect Sequence

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

const int MAXN = 1e5+5;
int n;
long long p;
long long num[MAXN];
int dp[MAXN];

int main(){
    //初始化
    fill(dp,dp+MAXN,0);

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&p);
    for(int i=0; i<n; i++){
        scanf("%d",&num[i]);
    }
    sort(num, num+n);
    for(int i=0; i<n; i++){
        if(i == 0){
            dp[i] = 1;
        }else{
            int j = i-dp[i-1];
            while(j<=i && num[i]>num[j]*p)
                j++;
            dp[i] = i-j+1;
        }
    }
    int MAX = -1;
    for(int i=0; i<n; i++){
        if(dp[i] > MAX){
            MAX = dp[i];
        }
    }
    printf("%d",MAX);
}

```

# A1086 Tree Traversals Again

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<queue>
using namespace std;

struct Node{
    int data;
    Node* left, *right;
    Node(){left = right = NULL;}
};
const int MAXN = 35;
int n;
int input[MAXN*2]; //

int num = 0;
Node* CreateTree(){
    if(input[num] == -1){
        return NULL;
    }
    Node *node = new Node;
    node->data = input[num];
    num++;
    node->left = CreateTree();
    num++;
    node->right = CreateTree();
    return node;
}
void PostTrave(Node* root, int& a){
    if(root == NULL)
        return;
    PostTrave(root->left, a);
    PostTrave(root->right, a);
    /*if(root == a){
        printf("%d",root->data);
    }else{
        printf(" %d",root->data);
    }*/
    if(a == 0){
        printf("%d",root->data);
        a++;
    }else{
        printf(" %d",root->data);
    }
}
int main(){
    //初始化
    for(int i=0; i<MAXN*2; i++)
        input[i] = -1;

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d\n",&n); //
    char s[20];
    for(int i=0; i<n*2; i++){
        fgets(s, 20, stdin); //
        if(s[1] == 'u'){
            int data = 0;
            int j = 5;
            while(s[j] != '\0' && s[j] != '\n'){ //
                data = data*10 + s[j]-48;
                j++;
            }
            input[i] = data;
        }else{
            input[i] = -1;
        }
    }
    Node* root = CreateTree();
    /*queue<Node*> q;
    q.push(root);
    while(!q.empty()){
        Node* node = q.front();
        q.pop();
        if(node == root){
            printf("%d",node->data);
        }else{
            printf(" %d",node->data);
        }
        if(node->left){
            q.push(node->left);
        }
        if(node->right){
            q.push(node->right);
        }
    }*/
    int a = 0;
    PostTrave(root,a);
}
/*
基本思路：创建树的输入正好比中根遍历的栈操作序列多一个pop，例如只有一个结点的树，创建树的输入是
1 # #, 而中根遍历的栈操作序列为push(1) pop
*/
```

错误分析：

1. 细节错误：51和54行：当要输入一行时，用fgets，同时不要忘了检查fgets期望要输入的字符前面有没有换行符和空格之类的

# A1087 All Roads to Rome

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<vector>
#include<map>
#include<set>
#include<string>
#include<iostream>
#include<stack>
using namespace std;

struct Node{
    int v, dist;
    Node(int _v, int _dist){v = _v; dist = _dist;}
};
const int MAXN = 205;
const int INF = 1e8;
int n,k;
string start;
vector<Node> Adj[MAXN];
int happiness[MAXN] = {0};
map<string,int> str2int;
map<int,string> int2str;

int dist[MAXN];
bool vis[MAXN];
set<int> Pre[MAXN]; //前驱结点集合，用于寻找路径
void Dijkstra(){ //默认从0开始
    //初始化
    fill(dist,dist+n,INF);
    dist[0] = 0;
    fill(vis,vis+n,false);

    for(int i=0; i<n; i++){
        int u = -1;
        int minDist = INF;
        for(int i=0; i<n; i++){
            if(vis[i] == false && dist[i] < minDist){ //
                minDist = dist[i];
                u = i;
            }
        }
        if(u == -1) return;
        vis[u] = true;
        for(int j=0; j<Adj[u].size(); j++){
            int v = Adj[u][j].v;
            int d = Adj[u][j].dist;
            if(vis[v] == false){
                if(dist[u]+d < dist[v]){
                    dist[v] = dist[u] + d;
                    Pre[v].clear();
                    Pre[v].insert(u);
                }else if(dist[u]+d == dist[v]){
                    Pre[v].insert(u);
                }
            }
        }
    }
}

int maxHappy = -1;
int maxAvgHappy = -1;
int routeNum = 0;
stack<int> temp;
stack<int> path;
void DFS(int s, int happy){
    if(s == 0){
        if(happy > maxHappy){
            maxHappy = happy;
            maxAvgHappy = happy/(temp.size()-1); //
            path = temp;
            //while(!temp.empty())temp.pop();
        }else if(happy == maxHappy){ //
            int avgHappy = happy/(temp.size()-1);
            if(avgHappy > maxAvgHappy){
                maxAvgHappy = avgHappy;
                path = temp;
            }
        }
        routeNum++;
        return; //
    }
    set<int>::iterator it;
    for(it = Pre[s].begin(); it != Pre[s].end(); it++){
        temp.push((*it));
        //DFS((*it), happy+happiness[(*it)]);
        DFS((*it), happy+happiness[s]);
        temp.pop();
    }
}

int main(){
    freopen("D:\\input.txt","r",stdin);
    cin >> n >> k >> start;
    str2int[start] = 0;
    int2str[0] = start;
    string locate;
    for(int i=1; i<=n-1; i++){
        cin >> locate >> happiness[i];
        str2int[locate] = i;
        int2str[i] = locate;
    }
    for(int i=0; i<k; i++){
        //string locate1, locate2, dist;
        string locate1, locate2;
        int dist;
        cin >> locate1 >> locate2 >> dist;
        int v1 = str2int[locate1], v2 = str2int[locate2];
        Adj[v1].push_back(Node(v2,dist));
        Adj[v2].push_back(Node(v1,dist));
    }

    Dijkstra();
    temp.push(str2int["ROM"]);
    DFS(str2int["ROM"], 0);

    printf("%d %d %d %d\n",routeNum,dist[str2int["ROM"]],maxHappy,maxAvgHappy);
    int v = path.top(); path.pop();
    cout << int2str[v];
    while(!path.empty()){
        v = path.top(); path.pop();
        cout << "->" << int2str[v];
    }
}
```

错误分析：

1. 细节错误：带有空注释的那一块

# *A1089 Insert or Merge

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
判断是否是归并排序的思路：若n=10, 从step=8,4,2,1总找到一个最大的step,使得每step个元素都满足
以下两个条件：1.是排序好的 2.Num里对应区间的元素与PartSortNum中对应区间的元素是一个集合的。
O(nlogn)
*/
#include<cstdio>
#include<set>
using namespace std;

/*
基本思路：判断是否是归并排序，但我的算法涉及到多个排序判别和集合相等判别，复杂度很高
*/
const int MAXN = 105;
int n;
int Num[MAXN], PartSortNum[MAXN];

bool isSort(int i, int j){ //判断PartSortNum[i]--[j]是否已经排序好
    if(i == j)
        return true;
    int k = i+1;
    while(k <= j){
        if(PartSortNum[k] < PartSortNum[k-1])
            return false;
        k++;
    }
    return true;
}
bool isSame(int i, int j){ //判断PartSortNum[i]--[j]与Num[i]--[j]的元素的集合是否一样
    set<int> s1,s2;
    for(int k=i; k<=j; k++){
        s1.insert(Num[k]);
        s2.insert(PartSortNum[k]);
    }
    //s1.erase(s2.begin(),s2.end());
    set<int>::iterator it;
    for(it = s2.begin(); it != s2.end(); it++){
        s1.erase((*it));
    }
    if(s1.empty()){
        return true;
    }else{
        return false;
    }
}
int isMergeSort(){ //判断是否为merge sort, 若是，则返回step，若不是，返回-1
    int step = 1;
    while(step*2 <= n)
        step *= 2;
    while(step >= 1){
        int i = 0;
        bool flag = true; //是否断定为merge sort
        while(i < n){
            int j = i+step-1 < n ? i+step-1 : n-1;
            if(!(isSort(i,j) && isSame(i,j))){
                flag = false;
                break;
            }
            i = j + 1;
        }
        if(flag){
            return step;
        }
        step = step/2;
    }
    return -1;
}
void Merge(int a, int b, int c){
    int* temp = new int[c-a+1];
    int k = 0, i = a, j = b+1;
    while(i<=b && j<=c){
        if(PartSortNum[i] <= PartSortNum[j]){
            temp[k] = PartSortNum[i];
            i++;
        }else{
            temp[k] = PartSortNum[j];
            j++;
        }
        k++;
    }
    if(i <= b){
        while(i <= b){
            temp[k] = PartSortNum[i];
            i++;
            k++;
        }
    }else if(j <= c){
        while(j <= c){
            temp[k] = PartSortNum[j];
            j++;
            k++;
        }
    }

    k = 0;
    i = a;
    while(i <= c){
        PartSortNum[i] = temp[k];
        k++;
        i++;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);

    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%d",&Num[i]);
    }
    for(int i=0; i<n; i++){
        scanf("%d",&PartSortNum[i]);
    }

    int step = isMergeSort();
    if(step != -1){
        int a=0, b, c;
        while(a < n){
            b = a + step - 1;
            if(b < n-1){
                c = b + step < n ? b + step : n-1;
                Merge(a,b,c);
                a = c + 1;
            }else{
                break;
            }
        }
        //输出
        printf("Merge Sort\n");
        for(int i=0; i<n; i++){
            if(i == 0){
                printf("%d",PartSortNum[i]);
            }else{
                printf(" %d",PartSortNum[i]);
            }
        }
    }else{
        if(n == 1){
            printf("%d",PartSortNum[0]);
        }else{
            int i = 1; //默认n>1
            while(i < n && PartSortNum[i] >= PartSortNum[i-1]){
                i++;
            }
            int temp = PartSortNum[i];
            while(i-1 >= 0 && temp < PartSortNum[i-1]){
                PartSortNum[i] = PartSortNum[i-1];
                i--;
            }
            PartSortNum[i] = temp;
            //输出
            printf("Insertion Sort\n");
            for(i=0; i<n; i++){
                if(i == 0){
                    printf("%d",PartSortNum[i]);
                }else{
                    printf(" %d",PartSortNum[i]);
                }
            }
        }
    }
}

//错误分析：
//1. 语法错误: 30行中对set.erase()的用法错了

```

错误分析:

1. 语法错误: 30行中对set.erase()的用法错了。不应该set1.erase(set2.begin())，对set1的删除不能用set2的迭代器

# A1092 To Buy or Not to Buy

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思路：用数组Num记录eva想要的颜色及其个数，然后访问商店的str，
若遍历一个char，则让相应的Num值减1
*/
#include<cstdio>
using namespace std;

char str1[1005];
char str2[1005];
int Num[125] = {0} ; //96+26 = 122
int more = 0, les = 0;

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%s",&str1);
    scanf("%s",&str2);

    int i = 0;
    while(str2[i] != '\0'){
        Num[str2[i]]++;
        i++;
    }
    i = 0;
    while(str1[i] != '\0'){
        --Num[str1[i]];
        i++;
    }

    bool yes = true;
    for(i=0; i<125; i++){
        if(Num[i] > 0){
            yes = false;
            les += Num[i];
        }else if(Num[i] < 0){
            more += -Num[i];
        }
    }
    if(yes){
        printf("Yes %d\n",more);
    }else{
        printf("No %d\n",les);
    }
}
```

# A1093 count PAT's

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思路：记录前i个字符中存在p的个数，PA的个数，PAT的个数
*/
#include<cstdio>
using namespace std;

const int MAXN = 1e5+5;
char str[MAXN];
long long ps[MAXN] = {0};
long long as[MAXN] = {0};
long long ts[MAXN] = {0};

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%s",&str);

    int i = 0;
    while(str[i] != '\0'){
        if(i == 0){
            if(str[i] == 'P'){
                ps[i]++;
            }
        }else{
            if(str[i] == 'P'){
                ps[i] = ps[i-1] + 1;
                as[i] = as[i-1];
                ts[i] = ts[i-1];
            }else if(str[i] == 'A'){
                ps[i] = ps[i-1];
                as[i] = ps[i-1] + as[i-1];
                ts[i] = ts[i-1];
            }else if(str[i] == 'T'){
                ps[i] = ps[i-1];
                as[i] = as[i-1];
                ts[i] = ts[i-1] + as[i-1];
            }
        }
        i++;
    }

    printf("%d",ts[i-1]%1000000007);
}
```

错误分析：

1. 由于结果可能很大，所以要用long long，而不是int

# A1095 Cars on Campus

```cpp
struct Node{
    string id;
    int inTime; //second
    int outTime; //second
    int interval;
    Node(){id = ""; inTime = outTime = -1; interval = -1;}
};
int n,k;
Node cars[MAXN] = {Node()};
map<string,int> string2int;
int num = 0; //每遇到一个不同的车，就建立车id与num的map，然后num++

bool Comp(Node a, Node b){
    if(b.inTime == -1){
        return true;
    }else if(a.inTime == -1){
        return false;
    }else{
        return a.inTime < b.inTime;
    }
}
bool Comp2(Node a, Node b){
    if(b.interval == -1){
        return true;
    }else if(a.interval == -1){
        return false;
    }else{
        return a.interval > b.interval;
    }
}
int main(){
    freopen("D:\\input.txt","r",stdin);

    scanf("%d %d\n",&n,&k);
    char id[10], inOut[10];
    int hh,mm,ss;
    for(int i=0; i<n; i++){
        scanf("%s %d:%d:%d %s",&id,&hh,&mm,&ss,&inOut);
        if(string2int.find(id) != string2int.end()){
            int index = string2int[id];
            int time = hh*3600 + mm*60 + ss;
            if(strcmp(inOut,"in") == 0){
                cars[index].inTime = time;
                if(cars[index].outTime != -1){
                    cars[index].interval = cars[index].outTime - cars[index].inTime;
                }
            }else{
                cars[index].outTime = time;
                if(cars[index].inTime != -1){
                    cars[index].interval = cars[index].outTime - cars[index].inTime;
                }
            }
        }else{
            cars[num].id = id;
            int time = hh*3600 + mm*60 + ss;
            if(strcmp(inOut,"in") == 0){
                cars[num].inTime = time;
            }else{
                cars[num].outTime = time;
            }
            string2int[id] = num;
            num++;
        }
    }

    sort(cars,cars+num,Comp); //按照outTime排序，outTime不合法的排在后面

    //看每个时间点都有多少车在停车, 遍历每个Node, 找在这个时间点之前in和在这个时间点后out的车
    int qhh,qmm,qss;
    for(int i=0; i<k; i++){
        scanf("%d:%d:%d",&qhh,&qmm,&qss);
        int query = qhh*3600 + qmm*60 + qss;
        int cnt = 0;
        for(int j=0; j<num; j++){
            if(cars[j].inTime != -1 && cars[j].inTime < query && cars[j].outTime > query){
                cnt++;
            }else if(cars[j].inTime == -1 || cars[j].inTime >= query){
                break;
            }
        }
        printf("%d\n",cnt);
    }

    sort(cars,cars+num,Comp);
    int temp = cars[0].interval;
    for(int i=0; i<num; i++){
        if(cars[i].interval != temp){
            break;
        }
        if(i == 0){
            cout << cars[i].id;
        }else{
            cout << " " << cars[i].id;
        }
    }
    printf(" %02d:%02d:%02d\n",temp/3600, (temp%3600)/60, (temp%3600)%60);
}

```

这种做法的设计不好，没有考虑同一个人可能有不止一个in记录，不止一个out记录，同时可能有不止一个in/out pair对

```cpp
/*
基本思路：这题是考栈的应用的，先把Record按照id,time进行排序，顺便建立id到int的map，
然后从第一个记录遍历到最后一个记录：考虑record和其id对应的栈，若和栈内的元素都为in，则弹栈，压入这个record，并考虑更新各个时间点的停车数量；
若此元素为为out，则不考虑；若栈内元素为in，此元素为out，则弹栈，更新最大停车时间，更新各个时间点的停车数量
*/
#include<cstdio>
#include<string>
#include<cstring>
#include<map>
#include<set>
#include<stack>
#include<algorithm>
#include<iostream>
using namespace std;

const int MAXN = 1e4+5;
const int MAXK = 8e4+5;

struct Rec{
    string id;
    int time;
    bool in; //是否是in
};
int n,k;
Rec recs[MAXN];
int queries[MAXK];
map<string,int> str2int;
int num = 0; //记录不同姓名的个数
stack<int> stks[MAXN/2]; //不同用户对应的栈, 压入的是recs下标
int parks[MAXK] = {0}; //queries[i]时间点的停车数量
int intervals[MAXN/2] = {0}; //记录各个人的总停车时间

bool Comp(Rec a, Rec b){
    if(a.id == a.id){
        return a.time < b.time;
    }else{
        return a.id < b.id;
    }
}
int main(){
    freopen("D:\\input.txt","r",stdin);

    scanf("%d %d",&n,&k);
    char id[10], inOut[10];
    int hh, mm, ss;
    for(int i=0; i<n; i++){
        scanf("%s %d:%d:%d %s",&id,&hh,&mm,&ss,&inOut);
        recs[i].id = id;
        recs[i].time = hh*3600 + mm*60 + ss;
        if(strcmp(inOut,"in") == 0){
            recs[i].in = true;
        }else{
            recs[i].in = false;
        }
        if(str2int.find(id) == str2int.end()){
            str2int[id] = num;
            num++;
        }
    }
    sort(recs, recs+n, Comp);
    for(int i=0; i<k; i++){
        scanf("%d:%d:%d",&hh,&mm,&ss);
        queries[i] = hh*3600 + mm*60 + ss;
    }

    int maxInterval = -1; //要和每个人的停车总时间比较，比如：5:10:33-12:23:42 18:00:01--18:07:01的总和是7:20:09
    set<string> se; //保存最大停车时间的车的id
    for(int i=0; i<n; i++){
        int idNum = str2int[recs[i].id]; //id对应的num
        if(stks[idNum].empty()){
            if(recs[i].in == true){
                stks[idNum].push(i);
                for(int j=0; j<k; j++){
                    if(recs[i].time <= queries[j]){ //边界问题
                        parks[j]++;
                    }
                }
            }
        }else{
            int recsIndex = stks[idNum].top();
            if(recs[recsIndex].in == true && recs[i].in == true){
                stks[idNum].pop();
                stks[idNum].push(i);
                for(int j=0; j<k; j++){
                    /*if(queries[j] > recs[recsIndex].time && queries[j] <= recs[i].time){ //边界问题
                        parks[j]++;
                    }*/
                    if(recs[recsIndex].time <= queries[j]){
                        parks[j]--;
                    }
                    if(recs[i].time <= queries[j]){
                        parks[j]++;
                    }
                }
            }else if(recs[recsIndex].in == true && recs[i].in == false){
                stks[idNum].pop();
                int interval = recs[i].time - recs[recsIndex].time;
                intervals[idNum] += interval;
                if(intervals[idNum] > maxInterval){
                    maxInterval = intervals[idNum];
                    se.clear();
                    se.insert(recs[i].id);
                }else if(intervals[idNum] == maxInterval){
                    se.insert(recs[i].id);
                }
                for(int j=0; j<k; j++){
                    if(recs[i].time <= queries[j]){ //边界问题
                        parks[j]--;
                    }
                }
            }
        }
    }
    for(int i=0; i<num; i++){
        if(!stks[i].empty()){
            int index = stks[i].top(); stks[i].pop();
            for(int j=0; j<k; j++){
                if(recs[index].time < queries[j]){
                    parks[j]--;
                }
            }
        }
    }

    //输出
    for(int i=0; i<k; i++){
        printf("%d\n",parks[i]);
    }
    set<string>::iterator it;
    for(it = se.begin(); it != se.end(); it++){
        cout << (*it) << " ";
    }
    printf("%02d:%02d:%02d\n",maxInterval/3600, (maxInterval%3600)/60, (maxInterval%3600)%60);
}
```

错误分析：

1. 业务逻辑错误：85到87行：若在5:00:00 in了一个车，在6:00:00又In了同一辆车，则考虑把由于5:00:00点in的车导致的各个时间点的停车数量的更新撤销掉
2. 运行超时：以前是一遇到一个record就更新parks，时间复杂度为O(1e4 * 8e4)，但是，只在遇到一对pair时进行更新，可以大大降低时间复杂度

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思路：这题是考栈的应用的，先把Record按照id,time进行排序，顺便建立id到int的map，
然后从第一个记录遍历到最后一个记录：考虑record和其id对应的栈，若和栈内的元素都为in，则弹栈，压入这个record，并考虑更新各个时间点的停车数量；
若此元素为为out，则不考虑；若栈内元素为in，次元素为out，则弹栈，更新最大停车时间，更新各个时间点的停车数量
*/
#include<cstdio>
#include<string>
#include<cstring>
#include<map>
#include<set>
#include<stack>
#include<algorithm>
#include<iostream>
using namespace std;

const int MAXN = 1e4+5;
const int MAXK = 8e4+5;

struct Rec{
    string id;
    int time;
    bool in; //是否是in
};
int n,k;
Rec recs[MAXN];
int queries[MAXK];
map<string,int> str2int;
int num = 0; //记录不同姓名的个数
stack<int> stks[MAXN/2]; //不同用户对应的栈, 压入的是recs下标
int parks[MAXK] = {0}; //queries[i]时间点的停车数量
int intervals[MAXN/2] = {0}; //记录各个人的总停车时间

bool Comp(Rec a, Rec b){
    if(a.id == a.id){
        return a.time < b.time;
    }else{
        return a.id < b.id;
    }
}
int main(){
    freopen("D:\\input.txt","r",stdin);

    scanf("%d %d",&n,&k);
    char id[10], inOut[10];
    int hh, mm, ss;
    for(int i=0; i<n; i++){
        scanf("%s %d:%d:%d %s",&id,&hh,&mm,&ss,&inOut);
        recs[i].id = id;
        recs[i].time = hh*3600 + mm*60 + ss;
        if(strcmp(inOut,"in") == 0){
            recs[i].in = true;
        }else{
            recs[i].in = false;
        }
        if(str2int.find(id) == str2int.end()){
            str2int[id] = num;
            num++;
        }
    }
    sort(recs, recs+n, Comp);
    for(int i=0; i<k; i++){
        scanf("%d:%d:%d",&hh,&mm,&ss);
        queries[i] = hh*3600 + mm*60 + ss;
    }

    int maxInterval = -1; //要和每个人的停车总时间比较，比如：5:10:33-12:23:42 18:00:01--18:07:01的总和是7:20:09
    set<string> se; //保存最大停车时间的车的id
    for(int i=0; i<n; i++){
        int idNum = str2int[recs[i].id]; //id对应的num
        if(stks[idNum].empty()){
            if(recs[i].in == true){
                stks[idNum].push(i);
            }
        }else{
            int recsIndex = stks[idNum].top();
            if(recs[recsIndex].in == true && recs[i].in == true){
                stks[idNum].pop();
                stks[idNum].push(i);
            }else if(recs[recsIndex].in == true && recs[i].in == false){
                stks[idNum].pop();
                int interval = recs[i].time - recs[recsIndex].time;
                intervals[idNum] += interval;
                if(intervals[idNum] > maxInterval){
                    maxInterval = intervals[idNum];
                    se.clear();
                    se.insert(recs[i].id);
                }else if(intervals[idNum] == maxInterval){
                    se.insert(recs[i].id);
                }
                for(int j=0; j<k; j++){
                    if(queries[j] >= recs[recsIndex].time && queries[j] < recs[i].time){ //边界问题
                        parks[j]++;
                    }
                }
            }
        }
    }

    //输出
    for(int i=0; i<k; i++){
        printf("%d\n",parks[i]);
    }
    set<string>::iterator it;
    for(it = se.begin(); it != se.end(); it++){
        cout << (*it) << " ";
    }
    printf("%02d:%02d:%02d\n",maxInterval/3600, (maxInterval%3600)/60, (maxInterval%3600)%60);
}
```

对运行时间进行了优化，其中有一个测试点以280ms的时间过了，narrow pass

# *A1097 Deduplication on a Linked List

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<set>
#include<vector>
using namespace std;

struct Node{
    int addr, data, nextAddr;
    Node(){addr = -1;}
};
const int MAXN = 100005;
int head, n;
Node llist[MAXN] = {Node()};

int main(){
    freopen("D:\\input.txt","r",stdin);
    scanf("%d %d\n",&head,&n);
    int addr, data, nextAddr;
    for(int i=0;i<n;i++){
        scanf("%d %d %d",&addr,&data,&nextAddr);
        llist[addr].addr = addr;
        llist[addr].data = data;
        llist[addr].nextAddr = nextAddr;
    }

    set<int> se; //存放已经出现在链表中的元素
    vector<Node> removed; //存放已删除的元素
    addr = head;
    int pre;
    while(addr != -1){
        if(addr == head){
            se.insert(llist[addr].data);
            pre = addr;
            addr = llist[addr].nextAddr;
        }else{
            if(se.find(llist[addr].data) != se.end() || se.find(-llist[addr].data) != se.end()){ //有重复元素
                //删除操作
                llist[pre].nextAddr = llist[addr].nextAddr;
                removed.push_back(llist[addr]);
                addr = llist[addr].nextAddr;
            }else{
                se.insert(llist[addr].data);
                pre = addr;
                addr = llist[addr].nextAddr;
            }
        }
    }

    //输出
    addr = head;
    while(addr != -1){
        if(llist[addr].nextAddr != -1)
            printf("%05d %d %05d\n",llist[addr].addr,llist[addr].data,llist[addr].nextAddr);
        else
            printf("%05d %d -1\n",llist[addr].addr,llist[addr].data);
        addr = llist[addr].nextAddr;
    }
    if(removed.size() != 0){
        vector<Node>::iterator it;
        for(it=removed.begin(); it != removed.end(); it++){
            if(it == removed.begin())
                printf("%05d %d ",(*it).addr,(*it).data);
            else{
                printf("%05d\n%05d %d ",(*it).addr,(*it).addr,(*it).data);
            }
        }
        printf("-1\n");
    }
}

//错误分析：
//1. 细节错误：55行，对-1输出的特判; 58行，对vector空间的特判

```

错误分析：

1. 细节错误：55行，对-1输出的特判; 58行，对vector空间的特判

# A1098 Insertion or HeapSort

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
using namespace std;

const int MAXN = 105;

int n; //数的个数
int Num[MAXN];
int PartSortNum[MAXN]; //部分排序的数组

void DownAdjust(int a, int b){
    int i = a, j = i*2+1;
    while(j <= b){
        if(j+1 <= b){
            if(PartSortNum[j+1] > PartSortNum[j])
                j = j+1;
        }
        if(PartSortNum[j] > PartSortNum[i]){
            int temp = PartSortNum[i];
            PartSortNum[i] = PartSortNum[j];
            PartSortNum[j] = temp;
            i = j;
            j = i*2+1;
        }else{
            break;
        }
    }
}
int main(){
    freopen("D:\\input.txt","r",stdin);
    scanf("%d\n",&n);
    for(int i=0;i<n;i++){
        scanf("%d",&Num[i]);
    }
    for(int i=0;i<n;i++){
        scanf("%d",&PartSortNum[i]);
    }

    bool flag = true; //标识是否是Insertion Sort
    int i = 0;
    for(i=0; i<n-1; i++){ //得到已经排序好的序列的后一个位置,如1 2 3 7 8 5 9 4 6 0 中的5
        if(PartSortNum[i] > PartSortNum[i+1])
            break;
    }
    i++;
    for(int j=i; j<n; j++){
        if(PartSortNum[j] != Num[j]){
            flag = false;
            break;
        }
    }

    if(flag){ //如果是Insertion Sort
        //多进行一步插入排序
        if(i < n-1){
            int temp = Num[i];
            while(i > 0 && temp < PartSortNum[i-1]){
                PartSortNum[i] = PartSortNum[i-1];
                i--;
            }
            PartSortNum[i] = temp;
        }
        //输出
        printf("Insertion Sort\n");
        for(int j=0; j<n; j++){
            if(j == 0){
                printf("%d",PartSortNum[j]);
            }else{
                printf(" %d",PartSortNum[j]);
            }
        }
    }else{ //如果是Heap Sort
        //找到第二个序列最后排序好的连续子序列前面的为位置，如6 4 5 1 0 3 2 7 8 9中的2
        int maxn = -1;
        for(int j=0; j<n; j++){
            if(Num[j] > maxn)
                maxn = Num[j];
        }
        if(PartSortNum[n-1] != maxn){
            i = n-1;
        }else{
            i = n-2;
            while(i >= 0 && PartSortNum[i+1] == PartSortNum[i]+1)
                i--;
        }

        //进行一步Heap Sort
        int temp = PartSortNum[0];
        PartSortNum[0] = PartSortNum[i];
        PartSortNum[i] = temp;
        DownAdjust(0,i-1);
        //输出
        printf("Heap Sort\n");
        for(int j=0; j<n; j++){
            if(j == 0){
                printf("%d",PartSortNum[j]);
            }else{
                printf(" %d",PartSortNum[j]);
            }
        }
    }
}
```

