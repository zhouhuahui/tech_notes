# *A1140 Look-and-say Sequence

```
基本思路：
对一个数D将他操作N-1次则得到答案。操作方法如下：
输入：str[]
1)i=0,j
2)j从i开始找最后一个跟str[i]相同的str[j],将str[i]和[i,j]区间的大小存储
3)i=j+1;如果i越界，则退出，否则，返回2)
```

# *A1141 PAT Ranking of Institutions

```
struct Node{string name;double TWS;int ns;int rank;vector<string>testees;}
map<string,Node> inst存储每个institution的信息。
从输入接受信息，并把成绩累加到inst中（如果有则累加，如果没有，则创建一个映射），
看输入的testee是否在testees中，若在，则不动，若不在，则加入到testees中，并累加ns
输入完成后，将inst的值部分(将testees域清空)放到vector中，并排序，将rank域赋值好，输出。
注意：学校名称不区分大小写;累加时要注意学生考的PAT是什么级别的;排序时按照TWS的整数部分进行比较；
我担心A1234与B1234是同一个人，所以计算每个学校的人数时用的方法比较复杂
```

```
由于map比较慢，所以最好用unordered_map来存储
```

# *A1142 Maximal Clique（难）

```
基本思路1：
int flag[200]为0表示不在set中，为1表示在set中。
对于每个顶点，看它的邻接点是否都在set中，若是则为符合要求的set，若有一个顶点的邻接点不在set中，则该set
不是符合要求的。
```

```
基本思路2：
基本思路1是错误的，因为第一，我没有判断set是否里面的每两个点都相连，第二，即使存在一个顶点，它有邻接点
不在set中，该顶点也可能不应该在set中，因为该邻接点不与set中剩余的所有元素相连。基本思路1完全搞错了。

先判断是否是clique，即判断是否任意两边都相连；之后判断是否是maximal，即遍历所有不在集合中的剩余的点，
看是否存在一个点满足和集合中所有的结点相连，最后如果都满足，那就输出Yes
```

# *A1143 Lowest Common Ancestor

```
基本思路1：
struct Node{
	int id,data;  //每个结点有0-n-1的编号id
	Node *left,*right,father;
}
bool inq[n];BFS时，若入队了，则inq赋为1
首先，创建树，存储每个结点的父亲结点；然后，在树中找U,V，若有一个或两个都没找到，则输出error，否则，
将结点指针赋给nodeu,nodev; 用nodeu和nodev进行BFS，广播它们的父亲结点，若在要给父亲结点入队时，发现
其已经入队，则此父亲结点就是LCA。若LCA是nodeu,nodev中的一个，则输出...,若不是，则输出...
```

```
基本思路2：
根据LCA的性质，访问一个BST的前根遍历序列，若遇见第一个node，使得node.data>u&&node.data<v或者
node.data<u&&node.data>v，则就是我们要的LCA
```

# *A1144 The Missing Number

```
基本思路1：
输入:num[]
先对num进行排序，然后找第一个正数num[i],找在num[i-1]与num[i]之间（如果i-1>=0的话）的最小正数，
若找到了，则输出，否则，继续i++,向后找。若到最后一个数都没找到，则输出比最后一个数大的最小正数。
```

# *A1145 Hashing - Average Search Time（难）

```
我一开始搞错了一些东西：平方探查的的结束是要么找到所需要的值，要么递增的j达到tsize；若一开始,hash为a
,则探查的索引为a+0*0,a+1*1,a+2*2...。还有，在这道题中，平方探查的结束还有一个条件,所探查到的值为0，
因为题目中说存储的数字全为正数。
```

```
真的很不理解，为什么在所有探查位置都被不是要查询的数字占用时，最后比较次数要额外+1
```

# *A1146 Topological Order

```
建立图时，计算每个顶点的入度indegree[].
读入序列，没遇到一个顶点，则检查它的入度是否为0，若不为0，则不是topo，否则，将它所连的邻接点的入度减1，
继续读下一个顶点。
```

# *A1147 Heap

```
数据读入到heap[]中，从1开始。
int maxn=1,minn=1; 如果否定了大根堆的可能则maxn=0,如果否定了小根堆的可能则minn=0。
从heap[2]开始往后看，让其与其父亲比较，进而判断是否否定大根堆或小根堆。
```

# A1148 Werewolf - Simple Version(难)

```
基本思路1：
只有一个狼人撒谎;有2个撒谎者，有2个狼人。
int isWolf[N];记录每个玩家是否是狼人,-1表示不确定
bool isLier[N];记录每个玩家是否撒谎
写DFS(int id,int lier) id表示当前正在访问的玩家,从1开始,lier表示已经确定撒谎的人数,当id==n+1时，
判断是否满足各种限定条件，若满足，则保存，若不满足，则还要用DFS2来继续确定一些玩家的身份。要根据id是否是
lier来分支。
也可能在没有到达id==n+1时就已经判断不满足了
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 105
int statements[MAXN],n,isWolf[MAXN]; //isWolf=-1表示不确定，0表示不是狼人，1表示是狼人 
bool isLier[MAXN];
void init(){
	memset(isLier,0,sizeof(isLier));
	memset(isWolf,-1,sizeof(isWolf));
} 
int getWolfNum(){
	int ans=0;
	for(int i=1;i<=n;i++){
		if(isWolf[i]==1)ans++;
	}
	return ans;
}
int getWolfLierNum(){
	int ans=0;
	for(int i=1;i<=n;i++){
		if(isWolf[i]==1&&isLier[i])ans++;
	}
	return ans;
}
vector<pair<int,int>> wolfs;
//a表示狼人的数量,b表示说谎的狼人数量,id表示玩家。
//遍历一些不确定的玩家（isWolf==-1的），让他们确定为狼或不为狼，则看有没有结果
void DFS2(int a,int b,int id){
	if(a==2&&b==1){
		pair<int,int> p;
		bool first=true;
		for(int i=1;i<=n;i++){
			if(isWolf[i]==1){
				if(first){
					p.first=i;
					first=false;
				}else{
					p.second=i;
				}
			}
		}
		wolfs.push_back(p);
		return;
	}
	if(!(a<2&&b<=1))return;
	if(id>n)return;
	if(isWolf[id]==-1){
		if(isLier[id]){
			if(a<2&&b<1){
				isWolf[id]=1;
				DFS2(a+1,b+1,id+1);
				isWolf[id]=-1;
				DFS2(a,b,id+1);
			}else{
				DFS2(a,b,id+1);
			}
		}else{
			isWolf[id]=1;
			DFS2(a+1,b,id+1);
			isWolf[id]=-1;
			DFS2(a,b,id+1); 
		}
	}else{
		DFS2(a,b,id+1);
	}
}
//遍历所有玩家，让他们说谎或不说谎，看能够确定多少玩家的身份，剩下的不能确定的玩家通过DFS2来确定
void DFS(int id,int lier){
	if(id==n+1){
		if(lier!=2)return;
		//if(getWolfNum()>2)return;
		//if(getWolfLierNum()!=1)return;
		int a=getWolfNum();
		int b=getWolfLierNum();
		if(a<2&&b<=1){ //还要继续确定某些玩家的身份
			DFS2(a,b,1);
		}else if(a==2&&b==1){ //找到了
			DFS2(a,b,1); 
		}
        return;
	}
	if(lier<2){
		int a=statements[id];
		bool isw=(a<0?false:true); 
		if(isWolf[abs(a)]==-1||(isWolf[abs(a)]==1&&isw)||(isWolf[abs(a)]==0&&!isw)){
			int temp=isWolf[abs(a)];
			isWolf[abs(a)]=(isw?1:0);
			isLier[id]=true;
			DFS(id+1,lier+1);
			isWolf[abs(a)]=temp;
		}
	}
	int a=statements[id];
	bool isw=(a<0?true:false); 
	if(isWolf[abs(a)]==-1||(isWolf[abs(a)]==1&&isw)||(isWolf[abs(a)]==0&&!isw)){
		int temp=isWolf[abs(a)];
		isWolf[abs(a)]=(isw?1:0);
		isLier[id]=false;
		DFS(id+1,lier);
		isWolf[abs(a)]=temp;
	}
}
bool cmp(pair<int,int>p1,pair<int,int>p2){
	return p1.first!=p2.first?p1.first<p2.first:p1.second<p2.second;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n;
	for(int i=1;i<=n;i++){
		cin>>statements[i];
	}
	DFS(1,0);
	if(wolfs.size()==0){
		cout<<"No Solution"<<endl;
	}else if(wolfs.size()==1){
		cout<<wolfs[0].first<<" "<<wolfs[0].second<<endl;
	}else{
		sort(wolfs.begin(),wolfs.end(),cmp);
		cout<<wolfs[0].first<<" "<<wolfs[0].second<<endl;
	}
	return 0;
}
```

```
基本思路2：
由题目可知，最后肯定有2个说谎者，且一个是狼人，一个是好人，而且必有两个狼人。
则应该，遍历每两个人，假设他们是狼人，然后由他们是狼人，推出说谎者，这样如果说谎者有2个，且一个是狼人，
一个是好人，则符合题意。
```

# *A1149 Dangerous Goods Packaging

```
基本思路1：
set<int> inc[MAXNUM]存储每个物品的与它不相容的物品。
将一列物品输入到set<int> items中，对于items中的每个物品，看它的不相容物品是否在items中，若都不在，
则可以items中的物品可以放在一起，否则，不能放在一起。
```

# *A1150 Travelling Salesman Problem

```
基本思路1：
对于一段路径，判断是否是联通的，若联通，则保存总距离，否则，就属于"Not a TS Cycle";然后，判断是否是
一条简单回路且经过所有的点，若是，则属于"TS Simple Cycle"；然后，判断是否是回路且经过所有的点，若是
则属于"TS Cycle"；否则，就属于"NOt a TS Cycle"
```

```
基本思路2：
先判断是否是非TS cycle，如果给出的路径存在某两个连续的点不可达或者第一个结点和最后一个结点不同或者这个路径
没有访问过图中所有的点，则就不是TS cycle；若是TS cycle，则如果共有n+1个点，则就是simple cycle，若不
是，则就是TS cycle.
```

# *A1151 LCA in a Binary Tree

见A1143

# *A1152 Google Recruitment

```
int num[],i=0,j=i+k;
看num[i]到num[j]是否是质数，若是，则输出，若不是，则递增i和j,继续，直到j越界
```

# *A1153 Decode Registration Card of PAT

```
查询问题。
必要时用unordered_map，其他按正常思路来就可以了
```

# *A1154 Vertex Coloring

```
int color[MAXN]存储每个点的颜色。set<int> cset保村颜色的种类
访问每个边，看是否有一条边，它的两个顶点是同一个中颜色，若有，则不是k-coloring，若没有，则是k-coloring
```

# *A1155 Heap Paths

```
镜像先序序列+判断是否为heap
```

