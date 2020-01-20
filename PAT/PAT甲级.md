# A1002 A+B for Polynmials

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

