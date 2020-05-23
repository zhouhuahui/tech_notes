# *A1120 Friend Numbers

```
直接用set<int>存储friend number
```

# A1121 Damn Single

```
我一开始想错了，我以为只要记录人是否有couple，然后在query中输出没有couple的就行。
但是，题目中要求的是在舞会中lonely的人，意味着要输出没有couple或者couple不在舞会中的人。
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
#define MAXID 100005
using namespace std;
struct Node{
	int couple;
	bool inParty;
	Node(){
		couple=-1;
		inParty=false;
	}
}; 
Node nodes[MAXID];
int n,a,b,m;
void init(){
	for(int i=0;i<MAXID;i++)nodes[i]=Node();
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d",&n);
	for(int i=0;i<n;i++){
		scanf("%d %d",&a,&b);
		nodes[a].couple=b;
		nodes[b].couple=a;
	}
	scanf("%d",&m);
	for(int i=0;i<m;i++){
		scanf("%d",&a);
		nodes[a].inParty=true;
	}
	set<int> single;
	for(int i=0;i<MAXID;i++){
		if(nodes[i].inParty && (nodes[i].couple==-1 || !nodes[nodes[i].couple].inParty))
			single.insert(i);
	}
	bool first=true;
	printf("%d\n",single.size());
	for(int i:single){
		if(first)printf("%05d",i),first=false; //error: %04d
		else printf(" %05d",i);
	}
	return 0;
}
```

错误分析：

1. 细节错误：输出格式错误

# A1122 Hamitonial Cycle

```
找对各种情况，if else就行
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 205
int n,m,k;
int graph[MAXN][MAXN];
void init(){
	memset(graph,0,sizeof(graph));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d",&n,&m);
	for(int i=0;i<m;i++){
		int u,v;
		scanf("%d %d",&u,&v);
		graph[u][v]=graph[v][u]=1;
	}
	scanf("%d",&k);
	for(int i=0;i<k;i++){
		int num;
		scanf("%d",&num);  
		int* cycle=new int[num];
		bool* vis=new bool[num];
		fill(vis,vis+num,false); //error:num未赋值就使用了 
		for(int j=0;j<num;j++)scanf("%d",&cycle[j]);
		if(num!=n+1){
			printf("NO\n");
			goto loop;
		}
		for(int j=0;j<num-1;j++){
			if(vis[cycle[j]]==true){
				printf("NO\n",cycle[j]);
				goto loop;
			}
			vis[cycle[j]]=true;
			if(graph[cycle[j]][cycle[j+1]]==0){
				printf("NO\n");
				goto loop;
			}
		}
		if(cycle[0]!=cycle[num-1]){
			printf("NO\n");
		}else{
			printf("YES\n");
		}
		loop:;
		delete[] cycle;
		delete[] vis;
	}
	return 0;
}
```

错误分析：

1. 未初始化数据却使用

# A1123 Is It A Complete AVL Tree(难)

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
struct Node{
	int data,height;
	Node *left,*right;
	Node(){
		left=right=NULL;
		height=1;
	}
};
int n;
Node*root=NULL;
void updateHeight(Node*r){
	if(r==NULL)return;
	//if(r->left)updateHeight(r->left);
	//if(r->right)updateHeight(r->right);
	int lh=(r->left?r->left->height:0);
	int rh=(r->right?r->right->height:0);
	r->height=max(lh,rh)+1;
}
int getBal(Node*r){
	int lh=(r->left?r->left->height:0);
	int rh=(r->right?r->right->height:0);
	return lh-rh;
}
void R(Node*&r){
	Node*temp1=r->left;
	Node*temp2=r->left->right;
	temp1->right=r;
	r->left=temp2;
	r=temp1;
	updateHeight(r->right);
	updateHeight(r);
}
void L(Node*&r){
	Node*temp1=r->right;
	Node*temp2=r->right->left;
	temp1->left=r;
	r->right=temp2;
	r=temp1;
	updateHeight(r->left);
	updateHeight(r);
}
void insert(int x,Node*&r){
	if(r==NULL){
		r=new Node();
		r->data=x;
	}else{
		if(x<r->data){
			insert(x,r->left);
		}else{
			insert(x,r->right);
		}
	}
	updateHeight(r); //error
	if(getBal(r)>1){
		if(getBal(r->left)>0){ //LL
			R(r);
		}else{ //LR
			L(r->left);
			R(r);
		}
	}else if(getBal(r)<-1){
		if(getBal(r->right)<0){ //RR
			L(r);
		}else{ //RL
			R(r->right);
			L(r);
		}
	}
}
//用BFS法来判断是否为complete 
bool BFS(Node*r){
	bool flag=true; //判断是否为complete 
	int cnt=0;
	queue<Node*>q;
	q.push(r);
	cnt++;
	bool first=true;
	while(!q.empty()){
		r=q.front();
		q.pop();
		if(first)printf("%d",r->data),first=false;
		else printf(" %d",r->data);
		if(r->left){
			q.push(r->left);
			cnt++; //error:忘了更新了 
		}else{
			if(cnt!=n)flag=false; //出现第一个非空结点，判断cnt==n
		}
		if(r->right){
			q.push(r->right);
			cnt++;
		}else{
			if(cnt!=n)flag=false;
		}
	}
	printf("\n");
	return flag;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++){
		int x;
		cin>>x;
		insert(x,root);
	}
	if(BFS(root)){
		printf("YES\n");
	}else{
		printf("NO\n");
	}
	return 0;
}
```

错误分析：

1. 算法错误：我先实现判断是否为complete的算法，真正的思路为：当出现第一个空结点时，当前压入队列的元素应该为结点个数，否则，不是complete

# A1124 Raffle for Weibo Followers

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int m,n,s;
vector<string> winners;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>m>>n>>s;
	string nick;
	int j=1;
	for(int i=1;i<=m;i++){
		cin>>nick;
		if(j<s){
			j++;
		}else if((j-s)%n==0){
			if(find(winners.begin(),winners.end(),nick)==winners.end()){
				winners.push_back(nick);
				j++;
			}
		}else{
			j++; //error: 忘了更新 
		}
	}
	for(string&i:winners){
		cout<<i<<endl;
	}
	if(winners.empty())cout<<"Keep going..."<<endl;
	return 0;
}
```

# A1125 Chain the Ropes

```
排序+贪心
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
int*num;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	num=new int[n];
	for(int i=0;i<n;i++)cin>>num[i];
	sort(num,num+n);
	double ans=num[0];
	for(int i=1;i<n;i++){
		ans=(ans+num[i])/2;
	}
	printf("%d",(int)ans);
	delete[] num;
	return 0;
}
```

# A1126 Eulerian Path

```
判断是否存在欧拉路(对于无向图而言)
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 505
int n,m;
vector<int> graph[MAXN];
int degree[MAXN];
bool vis[MAXN];
void init(){
	memset(degree,0,sizeof(degree));
	memset(vis,0,sizeof(vis));
}
void dfs(int u,int&cnt){
	cnt++;
	vis[u]=true;
	for(int i=0;i<graph[u].size();i++){
		int v=graph[u][i];
		if(!vis[v]){
			dfs(v,cnt);
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n>>m;
	int a,b;
	for(int i=0;i<m;i++){
		cin>>a>>b;
		graph[a].push_back(b);
		graph[b].push_back(a);
		degree[a]++;
		degree[b]++;
	}
	int odds=0,evens=0;
	for(int i=1;i<=n;i++){
		if(i==1)printf("%d",degree[i]);
		else printf(" %d",degree[i]); 
		if(degree[i]%2==0){
			evens++;
		}else{
			odds++;
		} 
	}
	printf("\n");
	int cnt=0; //1所联通的图的结点数量 
	dfs(1,cnt);
	if(evens==n && cnt==n){
		printf("Eulerian");
	}else if(odds==2 && cnt==n){
		printf("Semi-Eulerian");
	}else{
		printf("Non-Eulerian");
	}
	return 0;
}
```

错误分析：

1. 业务逻辑错误：我不知道输入可能是非联通图，可能有几个联通分量

# A1127 ZigZagging on a Tree(难)

```
基本思路：
定义两个队列outq(输出队列)，q(普通队列，功能类似于普通BFS的q)
1)r->data入outq, r入q,zig=0（此时q中是第0层的结点，只有一个）
2)若zig=0，表示要从左到右输出，即依次出队q到r中，然后r->left,r->right入输出队列，但是此时入输出队列
      的序列反转后入普通队列，因为要考虑到下一层的输出与这一层相反
  若zig=0,表示要从左到右输出，即依次出队q到r中，然后r->right,r->left入输出队列，但是此时入输出队列
      的序列反转后入普通队列，因为要考虑到下一层的输出与这一层相反
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
Node* root=NULL;
int in[MAXN],post[MAXN];
void createTree(int inl,int inr,int postl,int postr,Node*&r){
	if(inl>inr){
		r=NULL;
		return;
	}
	r=new Node();
	r->data=post[postr];
	int k=inl;
	while(in[k]!=post[postr])k++;
	createTree(inl,k-1,postl,postl+k-inl-1,r->left);
	createTree(k+1,inr,postl+k-inl,postr-1,r->right);
}
void BFS(Node*r){
	vector<int> outq;
	vector<Node*> q;
	outq.push_back(r->data);
	q.push_back(r);
	int zig=0; //0表示那一层从左到右输出，1表示那一层从右到左输出
	while(!q.empty()){
		vector<Node*> q2;
		if(zig==0){
			while(!q.empty()){
				r=q.front();
				q.erase(q.begin());
				if(r->left){
					q2.push_back(r->left);
					outq.push_back(r->left->data);
				}
				if(r->right){
					q2.push_back(r->right);
					outq.push_back(r->right->data);
				}
			}
		}else{
			while(!q.empty()){
				r=q.front();
				q.erase(q.begin());
				if(r->right){
					q2.push_back(r->right);
					outq.push_back(r->right->data);
				}
				if(r->left){
					q2.push_back(r->left);
					outq.push_back(r->left->data);
				}
			}
		}
		reverse(q2.begin(),q2.end());
		q=q2;
		zig=zig^1;
	}
	for(auto i=outq.begin();i!=outq.end();i++){
		if(i==outq.begin())printf("%d",*i);
		else printf(" %d",*i);
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++)cin>>in[i];
	for(int i=0;i<n;i++)cin>>post[i];
	createTree(0,n-1,0,n-1,root);
	BFS(root);
	return 0;
}
```

# A1128 N Queens Puzzle

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int k;
int* num;
bool isSol(int*chess,int n){
	int* vis=new int[n+1];
	for(int i=1;i<=n;i++)vis[i]=0;
	for(int i=1;i<=n;i++){
		if(vis[chess[i]]==0)vis[chess[i]]=1;
		else return false;
	}
	for(int i=1;i<n;i++){
		for(int j=i+1;j<=n;j++){
			if(abs(i-j)==abs(chess[i]-chess[j])){
				return false;
			}
		}
	}
	delete[] vis;
	return true;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>k;
	while(k--){
		int n;
		cin>>n;
		num=new int[n+1];
		for(int i=1;i<=n;i++)cin>>num[i];
		if(isSol(num,n)){
			printf("YES\n");
		}else{
			printf("NO\n");
		}
		delete[] num;
	}
	return 0;
}
```

# A1129 Recommendation System

```
每次都对排序好的序列采用插入排序，插入排序时间复杂度为,O(n)
总的时间复杂度为O(n^2)
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 50005
int n,k;
int item[MAXN];
bool cmp(const pair<int,int>&p1,const pair<int,int>&p2){
	if(p1.second!=p2.second)return p1.second>p2.second;
	else return p1.first<p2.first;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n>>k;
	for(int i=0;i<n;i++)cin>>item[i];
	vector<pair<int,int>> vis;
	for(int i=0;i<n;i++){
		if(i!=0){
			printf("%d:",item[i]);
			for(int j=0;j<vis.size()&&j<k;j++){
				printf(" %d",vis[j].first);
			}
			printf("\n");
		}
		if(i==0){
			vis.push_back({item[i],1});
		}else{
			int j;
			for(j=0;j<vis.size();j++){ //error: j<i
				if(vis[j].first==item[i]){
					vis[j].second++;
					break;
				}
			}
			if(j==vis.size())vis.push_back({item[i],1});
			int p=j-1;
			pair<int,int> cur=vis[j];
			while(p>=0&&cmp(cur,vis[p])){
				vis[p+1]=vis[p]; //vis[p]=vis[p-1];
				p--;
			}
			vis[p+1]=cur;
		}
	}
	return 0;
}
```

18分。超时

```
这题果然还是要用stl呢，不然就手撸AVL,时间复杂度可达到O(nlogn)
int m[MAXN]来存储每个物品的访问次数
set<pair<int,int>> 存储排序好的{item,次数},这个是O(logn)的复杂度来查询和插入，比我的自己写的插入
算法要快的多。
```

# A1130 Infix Expression

```
中序遍历，在根节点和叶子结点时时不输出左括号和右括号
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 25
struct Node{
	string data;
	int left,right;
	Node(){
		left=right=-1;
	}
}; 
Node nodes[MAXN];
bool isRoot[MAXN];
int root,n;
void init(){
	for(int i=0;i<MAXN;i++)nodes[i]=Node();
	for(int i=0;i<MAXN;i++)isRoot[i]=true;
}
vector<string> infix;
void inOrder(int r){
	if(nodes[r].left==-1 && nodes[r].right==-1){
		infix.push_back(nodes[r].data);
	}else{
		infix.push_back("(");
		if(nodes[r].left!=-1){
			inOrder(nodes[r].left);
		}
		infix.push_back(nodes[r].data);
		if(nodes[r].right!=-1){
			inOrder(nodes[r].right);
		}
		infix.push_back(")");
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n;
	string data;
	int left,right;
	if(n==1){ //特判 
		cin>>data>>left>>right;
		cout<<data;
		return 0;
	}
	for(int i=1;i<=n;i++){
		cin>>data>>left>>right;
		nodes[i].data=data;
		nodes[i].left=left;
		nodes[i].right=right;
		if(left!=-1)isRoot[left]=false;
		if(right!=-1)isRoot[right]=false;
	}
	for(int i=1;i<=n;i++)
		if(isRoot[i])root=i;
	inOrder(root);
	for(int i=1;i<infix.size()-1;i++){
		cout<<infix[i];
	}
	return 0;
}
```

注意特判：42行

# A1131 Subway Map

```
BFS+DFS.
BFS用于更新Pre数据结构，不过要注意inq数组的设置和一般的BFS不一样，原因是我们不仅要找最短路径，还要找
所有最短路径中最优的那个，所以要用Pre数据结构保存所有路径
DFS用于找最优的路径
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXNUM 10005
struct Edge{
	int u,v;
	int line;
	Edge(int _u,int _v,int _l){
		u=_u;
		v=_v;
		line=_l;
	}
};
vector<Edge> graph[MAXNUM];
int n,k;
vector<Edge> Pre[MAXNUM]; 
int inq[MAXNUM]; //-1表示没入过队列, inq[i]=j表示i在第j层入队 
void BFS(int s,int d){
	//init
	memset(inq,-1,sizeof(inq)); 
	for(int i=0;i<MAXNUM;i++)Pre[i].clear();
	//run
	queue<pair<int,int>> q;
	q.push({s,0});
	inq[s]=0;
	int maxLayer=-1;
	while(!q.empty()){
		int u=q.front().first,layer=q.front().second;
		q.pop();
		if(u==d)maxLayer=layer;
		if(maxLayer!=-1 && layer>=maxLayer)break;
		for(int i=0;i<graph[u].size();i++){
			int v=graph[u][i].v;
			if(inq[v]==-1 || layer+1==inq[v]){
				q.push({v,layer+1});
				inq[v]=layer+1;
				Pre[v].push_back(Edge(u,v,graph[u][i].line));
			}
		}
	}
}
int getTran(vector<Edge>&v){
	int cnt=0,preline=v[0].line;
	for(int i=1;i<v.size();i++){
		if(v[i].line!=preline){
			cnt++;
			preline=v[i].line;
		}
	}
	return cnt;
}
vector<Edge> optPath;
vector<Edge> path;
void initDFS(){
	optPath.clear();
	path.clear();
}
void DFS(int s,int d){
	if(d==s){
		if(optPath.empty())optPath=path;
		else if(getTran(optPath)>getTran(path))optPath=path;
		return;
	}
	for(int i=0;i<Pre[d].size();i++){
		path.push_back(Pre[d][i]);
		DFS(s,Pre[d][i].u);
		path.pop_back();
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=1;i<=n;i++){
		int m,s,pres;
		cin>>m;
		for(int j=0;j<m;j++){
			cin>>s;
			if(j==0)pres=s;
			else{
				graph[pres].push_back(Edge(pres,s,i));
				graph[s].push_back(Edge(s,pres,i));
				pres=s;
			}
		}
	}
	cin>>k;
	for(int i=0;i<k;i++){
		int start,dest;
		cin>>start>>dest;
		BFS(start,dest);
		initDFS();
		DFS(start,dest);
		cout<<optPath.size()<<endl;
		int j1=optPath.size()-1,j2;
		while(j1>=0){
			j2=j1;
			printf("Take Line#%d from %04d to ",optPath[j1].line,optPath[j1].u);
			while(j2-1>=0&&optPath[j2-1].line==optPath[j2].line)j2--;
			printf("%04d.\n",optPath[j2].v); //error: %d
			j1=j2-1;
		}
	}
	return 0;
}
```

# A1132 Cut Integer

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
string n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	int k;
	cin>>k;
	while(k--){
		cin>>n;
		int siz=n.size();
		string n1=n.substr(0,siz/2);
		string n2=n.substr(siz/2);
		int num=stoi(n);
		int num1=stoi(n1);
		int num2=stoi(n2);
		if(num1!=0&&num2!=0&&num%num1==0&&(num/num1)%num2==0){
			printf("Yes\n"); 
		}else{
			printf("No\n");
		}
	}
	return 0;
}
```

错误分析：

1. 虽然很简单，但是我却没有做出来，原因是我没有考虑情况：num1和num2可能是0
2. 注意输出YES还是Yes

# *A1133 Splitting A Linked List

```
基本思路1：将数分为3组，遍历链表，把结构体分别存储在这三组中，然后每组的数依次输出
```

```
基本思路2：用稳定的排序算法，比如冒泡，但是又麻烦，又慢，不如思路1好
```

# A1134 Vertex Cover

```
基本思路：
存储所有边到edges中，遍历所有的边，看是否有边，它的两个顶点都没有在set中，那么这个set就不是vertex cover
时间复杂度：O(kn)
```

```
基本思路2：
vector<int> vec[MAXN]存储每个点所开头的边的编号集合
遍历set中的所有顶点元素a，将a所开头的所有边的编号都置为1
若有边的位不是1，那么就不是vertex cover，否则，就是
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005
struct Edge{
	int u,v,next;
	Edge(int _u,int _v,int _n){
		u=_u;
		v=_v;
		next=_n;
	}
	Edge(){};
};
Edge edges[MAXN*2];
int head[MAXN];
int cover[MAXN];
int n,m,k,cnt=0; //cnt是边的编号 
void init(){
	memset(head,-1,sizeof(head));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n>>m;
	for(int i=0;i<m;i++){
		int u,v;
		cin>>u>>v;
		edges[cnt]=Edge(u,v,head[u]);
		head[u]=cnt++;
		edges[cnt]=Edge(v,u,head[v]);
		head[v]=cnt++;
	}
	cin>>k;
	while(k--){
		memset(cover,0,sizeof(cover));
		int nv,v;
		cin>>nv;
		while(nv--){
			cin>>v;
			cover[v]=1;
		}
		//遍历每一个边
		for(int i=0;i<cnt;i++){
			int u=edges[i].u,v=edges[i].v;
			if(cover[u]==0&&cover[v]==0){
				printf("No\n");
				goto loop;
			}
		} 
		printf("Yes\n");
		loop:;
	}
}
```

# A1135 Is It A Red-Black Tree(难)

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
int num[MAXN];
Node *root;
int k,n;
//[a,b]是要考虑的num的区间 
void createTree(Node*&r,int a,int b){
	if(a>b){
		r=NULL;
		return;
	}
	int c=a+1; //error: 若c=a,则下一行的循环永远不会执行循环体,因为abs(num[c])==abs(num[a]) 
	while(c<=b&&abs(num[c])<abs(num[a]))c++;
	r=new Node();
	r->data=num[a];
	createTree(r->left,a+1,c-1);
	createTree(r->right,c,b);
}
//若r是红黑树，则返回从r到叶子结点的黑色结点的个数，否则返回-1 
int deal(Node*r){
	if(r==root && r->data<0)return -1; //error:这个条件忘了判段 
	if(r->data<0){ //error:if(r<0)
		//error: 这部分逻辑以前写错了 
		if(r->left&&r->right&&!(r->left->data>0&&r->right->data>0)){
			return -1;
		}else if((r->left&&!r->right) || (!r->left&&r->right)){
			return -1;
		}
	}
	int leftNum=(r->left?deal(r->left):0);
	if(leftNum==-1)return -1;
	int rightNum=(r->right?deal(r->right):0);
	if(rightNum==-1)return -1;
	if(leftNum==rightNum){
		return leftNum+(r->data<0?0:1);
	}else{
		return -1;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>k;
	while(k--){
		root=NULL;
		cin>>n;
		for(int i=0;i<n;i++)cin>>num[i];
		createTree(root,0,n-1);
		if(deal(root)!=-1){
			printf("Yes\n");
		}else{
			printf("No\n");
		}
	}
	return 0;
}
```

错误分析：

1. 这部分设计做好了，但是在写代码的时候，不太细心或者状态不好，有些逻辑搞错了，如22，33；有些判断忘了加，如31行

# *A1136 A Delayed Palindrome

```
用字符串存储数据而不是long long，然后自己写字符串相加函数
```

# *A1137 Final Grading

```
输入中包含每个学生的G(mid-term)和G(final)
如果G(mid-term)<=G(final): G=G(final);否则,G=一个公式
取得证书的条件：G(p)>=200 and G(final)>=60
我可以用两个map存储姓名到学生考试信息的转换。首先保存所有G(p)>=200的学生的信息，不保存G(p)<200的信息，
然后读取学生的G(mid-term)和G(final),若该学生不在map中，则不考虑；若G(final)<60则删除该学生；否则，
保存信息。最后，遍历map，将学生信息读到vector中，排序，输出
```

# A1139 First Contact（难）

```
基本思路1：
我一开始想用BFS来做，但是写完代码后发现BFS不对，因为非常不好判断下一个邻接点是否应该入队。
于是就用DFS来做，将路径上的点的vis值设为true，并设置递归深度最多为4，最后找所有符合条件的路径
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXNUM 10005
int n,m,k;
bool gender[MAXNUM];//0 for male, 1 for female
vector<int> graph[MAXNUM];
vector<pair<int,int>> pairs;
vector<int> path;
bool vis[MAXNUM];
void initDFS(){
	pairs.clear();
	path.clear();
	memset(vis,0,sizeof(vis));
}
void DFS(int s,int d,int layer){
	if(s==d&&layer<3)return; //error: 在第0,1,2层的d不考虑 
	path.push_back(s);
	vis[s]==true;
	if(layer==3&&d==s){ //error:if(d==s)
		int lover1=path.front(),lover2=path.back();
		if(gender[lover1]==false && gender[lover2]==true){
			if(gender[path[1]]==false && gender[path[2]]==true){
				pairs.push_back({path[1],path[2]});
			}
		}
		if(gender[lover1]==true && gender[lover2]==false){
			if(gender[path[1]]==true && gender[path[2]]==false){
				pairs.push_back({path[1],path[2]});
			}
		}
		if(gender[lover1]==false && gender[lover2]==false){
			if(gender[path[1]]==false && gender[path[2]]==false){
				pairs.push_back({path[1],path[2]});
			}
		}
		if(gender[lover1]==true && gender[lover2]==true){
			if(gender[path[1]]==true && gender[path[2]]==true){
				pairs.push_back({path[1],path[2]});
			}
		}
		goto ret;
	}
	if(layer<3){
		for(int u:graph[s]){
			if(vis[u])continue;
			DFS(u,d,layer+1);
		}
	}
	ret:;
	path.pop_back();
	vis[s]=false;
}
bool cmp(const pair<int,int>&p1,const pair<int,int>&p2){
	if(p1.first!=p2.first)return p1.first<p2.first;
	else return p1.second<p2.second;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n>>m;
	for(int i=0;i<m;i++){
		string u,v;
		int a,b;
		cin>>u>>v;
		if(u[0]=='-'){
			a=stoi(u.substr(1));
			gender[a]=true;
		}else{
			a=stoi(u);
			gender[a]=false;
		}
		if(v[0]=='-'){
			b=stoi(v.substr(1));
			gender[b]=true;
		}else{
			b=stoi(v);
			gender[b]=false;
		}
		graph[a].push_back(b);
		graph[b].push_back(a);
	}
	cin>>k;
	while(k--){
		int u,v;
		cin>>u>>v;
		u=(u<0?-u:u);
		v=(v<0?-v:v);
		initDFS();
		DFS(u,v,0); 
		if(pairs.empty()){
			printf("0\n");
		}else{
			printf("%d\n",pairs.size());
			sort(pairs.begin(),pairs.end(),cmp);
			for(auto&i:pairs){
				printf("%04d %04d\n",i.first,i.second);
			}
		}
	}
}
```

25分，一个case错误，一个case超时

```
基本思路2：
对于lover1,lover2，考虑lover1的所有朋友，lover2的所有朋友，找符合条件的。如果性别不符合要求，直接跳过，
如果这两个朋友没有联系，也跳过。
```

