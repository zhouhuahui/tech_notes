# *201512-3 画图

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXM 205
#define MAXN 205

char canvas[MAXM][MAXN];
int m,n,q;

void init(){
    memset(canvas,'.',sizeof(canvas));
}
void drawLine(int x1,int y1,int x2,int y2){
    if(x1==x2){
        if(y1>y2){
            swap(y1,y2);
        }
        for(int i=y1;i<=y2;i++){
            canvas[x1][i]=(canvas[x1][i]=='-')?'+':'|';
        }
    }else{
        if(x1>x2){
            swap(x1,x2);
        }
        for(int i=x1;i<=x2;i++){
            canvas[i][y1]=(canvas[i][y1]=='|')?'+':'-';
        }
    }
}
bool isLegal(int x,int y){
    if(x>=0 && x<m && y>=0 && y<n && canvas[x][y]!='-' && canvas[x][y]!='|' && canvas[x][y]!='+')
        return true;
    return false;
}

int inq[MAXM][MAXN];
void initFill(){
    memset(inq,0,sizeof(inq));
}
void fillChar(int x,int y,char c){
    int dirx[4]={-1,0,1,0};
    int diry[4]={0,1,0,-1};
    queue<pair<int,int>> q;
    q.push({x,y});
    inq[x][y]=1; //error: 忘了设置入队flag
    while(!q.empty()){
        int x1=q.front().first;
        int y1=q.front().second;
        q.pop();
        canvas[x1][y1]=c;
        //printf("x=%d y=%d c=%c\n",x,y,canvas[x][y]);///////
        for(int i=0;i<4;i++){
            int x2=x1+dirx[i];
            int y2=y1+diry[i];
            if(inq[x2][y2]==0 && isLegal(x2,y2)){
                q.push({x2,y2});
                inq[x2][y2]=1;
            }
        }
    }
}
void printCanvas(){
    for(int i=n-1;i>=0;i--){
        for(int j=0;j<m;j++){
            printf("%c",canvas[j][i]); //error: 我思维惯性了，不用输出空格的
        }
        printf("\n");
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    init();
    cin>>m>>n>>q;
    int p,x1,y1,x2,y2,x,y;
    char c;
    while(q--){
        cin>>p;
        if(p==0){
            cin>>x1>>y1>>x2>>y2;
            drawLine(x1,y1,x2,y2);
        }else{
            cin>>x>>y>>c;
            initFill();
            fillChar(x,y,c);
        }
    }
    printCanvas();
}
```

90分。

错误分析：

我思维惯性了，导致代码虽然正确，但是0分，因为我在不该输出空格的时候输出了空格，这在考试中是致命的，因为即使在线下发现了出错也很难找出这个错误，何况在考试中不知道得分且坚信代码是正确的情况下。

# *201512-4 送货

```
无向图存在欧拉路径的充要条件是：
1. 图各个顶点连通（是连通图） 
2. 各顶点的度都为偶数，或者至多存在两个顶点度为奇数（则这两个顶点为起点或终点）
```

```cpp
vector<int> path;
void DFS(int v){//深度优先遍历
    for(int i=0;i<graph[v].size();++i){//遍历该点能到达的结点
        int w=graph[v][i];
        if(!visit[v][w]){//该边没有被访问过
            visit[v][w]=visit[w][v]=true;//该边已被访问
            DFS(w);//递归遍历
        }
    }
    path.push_back(v);//加入欧拉路径中
}
//这个DFS函数比较特殊，因为欧拉路的性质：若一个图存在欧拉路，那么1在面对2,3这两个邻接点时，选择任意一个继续走下去
//就可以找到欧拉路，不存在从2走可以找到欧拉路，但是从3走就找不到。
```

#  *201509-3 模板生成系统

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int n,m;
vector<string> input;
map<string,string> mp;

int isvar(string&line,int pos){
    pos+=2;
    int a=0;
    while(a<2){
        if(line[pos++]=='}')
            a++;
    }
    return pos-1;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>m;
    getchar();
    for(int i=0;i<n;i++){
        string line;
        getline(cin,line);
        input.push_back(line);
    }
    for(int i=0;i<m;i++){
        string str;
        getline(cin,str);
        //建立映射
        int k1=str.find(' ');
        int k2=k1+2;
        while(str[k2]!='\"')
            k2++;
        mp[str.substr(0,k1)]=str.substr(k1+2,k2-k1-2);
    }
    for(int i=0;i<n;i++){ //访问input[i]
        int j=0;
        while(j<input[i].size()){
            if(input[i][j]!='{'){
                printf("%c",input[i][j]);
                j++;
            }else{
                int j2=isvar(input[i],j);
                //cout<<"input[i][j2]="<<input[i][j2]<<endl;//////
                string var=input[i].substr(j+3,j2-j-5);
                //cout<<"\nvar="<<var<<endl;//////
                map<string,string>::iterator it;
                if((it=mp.find(var)) != mp.end()){
                    printf("%s",it->second.c_str());
                }
                j=j2+1;
            }
        }
        printf("\n");
    }
}
```

90分。错误的原因可能是我找到了{就默认找到了var，准确来说，我必须找到"{{ "才行。

# 201509-4 高速公路(难)

``` cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 1e4+5;
vector<int> Adj[MAXN];
int n,m;
int ans = 0;

stack<int> stk;
int index = 0;
int dfn[MAXN], low[MAXN];
bool ins[MAXN];
void Tarjan(int u){
    dfn[u]=low[u]=++index;
    stk.push(u);
    ins[u]=true;
    for(int i=0;i<Adj[u].size();i++){
        int v = Adj[u][i];
        if(!dfn[v]){
            Tarjan(v);
            low[u]=min(low[u],low[v]);
        }else if(ins[v]){
            low[u]=min(dfn[v],low[u]);
        }
    }
    if(low[u]==dfn[u]){
        int cnt=0;
        while(stk.top()!=u){
            ins[stk.top()]=false; //error
            stk.pop();
            cnt++;
        }
        ins[stk.top()]=false;
        stk.pop(); cnt++;
        ans += (cnt>=2) ? cnt*(cnt-1)/2 : 0;
    }
}
void initTarjan(){
    while(!stk.empty())stk.pop();
    index = 0;
    fill(ins,ins+MAXN,false);
}
void init(){
    memset(dfn,0,sizeof(dfn));
    memset(low,0,sizeof(low));
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>m;
    for(int i=0; i<m; i++){
        int u,v;
        cin>>u>>v;
        Adj[u].push_back(v);
    }
    init();
    for(int i=1; i<=n; i++){
        if(!dfn[i]){
            initTarjan();
            Tarjan(i);
        }
    }
    cout << ans;
}
```

# *201503-3 节日

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define DAYS_OF_PINGNIAN 365
#define DAYS_OF_RUNNIAN 366

int daysOfMonth[13]={0,31,28,31,30,31,30,31,31,30,31,30,31};

bool isrun(int y){
    if(y%400==0 || (y%4==0&&y%100!=0))
        return true;
    return false;
}
//计算y年m月的第一天是星期几
int determineWeekdayOfMonthbegin(int y,int m){
    int i=1850, days=0;
    for(;i<y;i++){
        days+=(isrun(i)?DAYS_OF_RUNNIAN:DAYS_OF_PINGNIAN);
    }
    for(i=1;i<m;i++){
        days+=((i!=2)?daysOfMonth[i]:(isrun(y)?29:28));
    }
    return (2-1+days)%7+1;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int a,b,c,y1,y2;
    cin>>a>>b>>c>>y1>>y2; //a月的第b个星期c
    if(y2<y1)swap(y1,y2);
    for(int y=y1;y<=y2;y++){
        int k=determineWeekdayOfMonthbegin(y,a); //a月第一天是星期k
        int days=(b-1)*7+c-k; //第b个星期c与第一天相差多少天
        int d=days+1;
        int d2=((a!=2)?daysOfMonth[a]:(isrun(y)?29:28));
        if(d>=1 && d<=d2){
            printf("%04d/%02d/%02d\n",y,a,d);
        }else{
            printf("none\n");
        }
    }
    //test//////
    /*printf("241 is ");
    if(isrun(241))
        printf("run\n");
    else
        printf("ping\n");*/
    //printf("%d",determineWeekdayOfMonthbegin(2020,3));
}
```

40分。

# 201503-4 网络延时

**动态规划解法**

核心代码

```cpp
int dp1[20005],dp2[20005];
void DP(int v){
    int max1=0,max2=0;
    for(int i:tree[v]){
        DP[i];
        if(dp1[i]+1>max1){
            max2=max1;
            max1=dp1[i]+1;
        }else if(dp1[i]+1>max2){
            max2=dp1[i]+1;
        }
    }
    dp1[v]=max1;
    dp2[v]=max1+max2;
}
```

# *201412-1 门禁系统

```
int time[1005]; //time[i]表示i号来的次数
while(输入a){
	cout<<++time[a];
}
```

# 201412-2 Z字形扫描

```
bool flag=true;//表示向右
bool flag2=true; //向左下方走
int x=1,y=1,n;
while(!(x==n&&y==n)){
	cout<<[x][y];
	if(flag&&不能向右走了)flag=false;
	else if(!flag&&不能向下走了)flag=true;
	if(flag)向右走，更新x,y,flag反转
	else 向下走,更新x,y,flag反转
	if(x==n&&y==n)break;
	do{
		cout<<[x][y];
		if(flag2)向左下方走,更新x,y
		else 向右上方走，更新x,y
	}while(x,y不在边缘)
	flag2反转
}
cout<<[x][y];
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 505
int square[MAXN][MAXN];
bool flag=true,flag2=true;
int x=1,y=1,n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=1;i<=n;i++){
		for(int j=1;j<=n;j++){
			cin>>square[i][j];
		}
	}
	bool first=true;
	while(!(x==n&&y==n)){
		if(first){
			cout<<square[y][x];
			first=false;
		}else cout<<" "<<square[y][x];
		if(flag&&x==n)flag=false;
		else if(!flag&&y==n)flag=true;
		if(flag){
			x++;
			flag=false;
		}else{
			y++;
			flag=true;
		}
		if(x==n&&y==n)break;
		do{
			if(first){
				cout<<square[y][x];
				first=false;
			}else cout<<" "<<square[y][x];
			if(flag2){
				x--;
				y++;
			}else{
				x++;
				y--;
			}
		}while(!(x==1||x==n||y==1||y==n));
		flag2=(flag2?false:true);
	}
	if(first){
		cout<<square[y][x];
		first=false;
	}else cout<<" "<<square[y][x];
	return 0;
} 
```

# 201412-3 集合竞价

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 5005
typedef long long lld;

struct Record{
    int type;//0:卖 1:买
    double price;
    int num;
    bool exist;
};
Record recs[MAXN];

bool cmp(Record r1,Record r2){
    if(r1.exist!=r2.exist && !(r1.exist==false && r2.exist==false))
        return r1.exist;
    if(r1.price != r2.price)
        return r1.price<r2.price;
    return r1.type<r2.type;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int n=0;
    string rec="",temp;
    while(rec="",getline(cin,rec)){
        if(rec=="")
            break;
        n++; //n表示当前正在输入的行数
        stringstream ss(rec);
        ss>>temp;
        if(rec[0]=='b'){
            recs[n].type=1;
            ss>>recs[n].price;
            ss>>recs[n].num;
            recs[n].exist=true;
        }else if(rec[0]=='s'){
            recs[n].type=0;
            ss>>recs[n].price;
            ss>>recs[n].num;
            recs[n].exist=true;
        }else{
            int a;
            ss>>a;
            recs[a].exist=false;
            recs[n].exist=false;
        }
    }
    sort(recs+1,recs+n+1,cmp);

    lld a=0,b=0,c=-1; //小于某个价格的卖家数量，大于某个价格的买家数量，较小值
    int i;
    for(i=1;i<=n&&recs[i].exist;i++){
        if(i==1){//初始化
            if(recs[i].type==0){
                a+=recs[i].num;
            }
            for(int j=i;j<=n&&recs[j].exist;j++){ //error:for(int j=i;j<=n.exist;j++)没有考虑到后面可能有失效的记录
                if(recs[j].type==1){
                    b+=recs[j].num;
                }
            }
            c=min(a,b);
            continue;
        }
        if(recs[i].type==0){
            a+=recs[i].num;
        }
        if(recs[i-1].type==1){
            b-=recs[i-1].num;
        }
        if(min(a,b)>=c){
            c=min(a,b);
        }else{
            break;
        }
    }
    i--;
    printf("%.2f %lld",recs[i].price,c);
}

```

错误分析：

1. 逻辑错误：58行

# 201412-4 最优灌溉

```
struct Edge{
	int u,v;
}
Edge edges[]; //记录所有的边
int cnt=0;
//并查集部分
//pass
每输入一个边，就插入到edges中。
排序edges
kruskal算法
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXM 100005
#define MAXN 1005
struct Edge{
	int u,v,c;
};
int n,m;
Edge edges[MAXM]; //记录所有的边
int cnt=0;
int father[MAXN];
long long ans=0;
int findFather(int x){
	if(x==father[x])return x;
	int f=findFather(father[x]);
	father[x]=f;
	return f;
}
bool union2(int a,int b){
	int fa=findFather(a),fb=findFather(b);
	if(fa!=fb){
		father[fa]=fb;
		return true;
	}else{
		return false;
	}
}
bool cmp(const Edge&e1,const Edge&e2){
	return e1.c<e2.c;
}
void init(){
	for(int i=0;i<MAXN;i++)father[i]=i;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n>>m;
	for(cnt=0;cnt<m;cnt++){
		int u,v,c;
		cin>>u>>v>>c;
		edges[cnt]={u,v,c};
	}
	sort(edges,edges+m,cmp);
	int edge_cnt=0,i=0;
	while(edge_cnt<n-1&&i<m){
		if(union2(edges[i].u,edges[i].v)){
			edge_cnt++;
			ans+=edges[i].c;
		}
		i++;
	}
	cout<<ans;
	return 0;
}

```

# *201409-1 相邻数对

```
输入数据到num[]
排序
看num[0]与num[1],num[1]与num[2]......num[n-2]与num[n-1]
```

# *201409-2 画图

```
基本思路1：
int paper[101][101]; //为0表示没有涂色，1表示已涂色
对于所有的输入的矩形bound，更新paper数组
最后统计paper中有多少个为1的元素
```

# 201409-3 字符串匹配

```
输入字符串s
输入大小写敏感选项 flag
if(!flag)s全变小写
while(输入字符串str){
	temp=str
	if(!flag)str全遍小写
	if(str.find(s)!=string::npos)cout<<temp<<endl;
}
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
string s,str;
int flag;
int n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>s>>flag>>n;
	if(flag==0){
		for(char&c:s)c=tolower(c);
	}
	while(n--){
		cin>>str;
		string temp=str;
		if(flag==0){
			for(char&c:str)c=tolower(c);
		}
		if(str.find(s)!=string::npos)cout<<temp<<endl;
	}
	return 0;
} 
```

# 201409-4 最优配餐(难)

```
n, m, k, d，分别表示方格图的大小、栋栋的分店数量、客户的数量，以及不能经过的点的数量。
坐标的区间为[1,n]
注意：成本需要考虑送餐数和成本
int graph[n][n];//0表示普通,-1表示分店,-2表示不能通过的点,大于0的表示送餐数
queue<<x,y,layer>> q
bool inq[]
输入,更新graph,若输入为分店，则更新q和inq；若输入为顾客点，则累加该点的送餐数
BFS模块，当遍历到顾客点时，累加送餐数*距离
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
typedef long long lld;
struct Node{
	int x,y,layer;
	Node(int a,int b,int c){
		x=a;
		y=b;
		layer=c;
	}
};
int n,m,k,d;
int graph[MAXN][MAXN];
queue<Node> q;
bool inq[MAXN][MAXN];
int dirx[4]={-1,0,1,0};
int diry[4]={0,1,0,-1};
void init(){
	memset(graph,0,sizeof(graph));
	memset(inq,0,sizeof(inq));
}
void input(){
	cin>>n>>m>>k>>d;
	int x,y,z;
	for(int i=0;i<m;i++){
		cin>>x>>y;
		graph[x][y]=-1;
		q.push(Node(x,y,0));
		inq[x][y]=true;
	}
	for(int i=0;i<k;i++){
		cin>>x>>y>>z;
		graph[x][y]+=z;
	}
	for(int i=0;i<d;i++){
		cin>>x>>y;
		graph[x][y]=-2;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	input();
	//BFS
	lld ans=0;
	while(!q.empty()){
		Node node=q.front();
		q.pop();
		if(graph[node.x][node.y]>0){
			ans+=(graph[node.x][node.y]*node.layer);
		}
		for(int i=0;i<4;i++){
			int x=node.x+dirx[i];
			int y=node.y+diry[i];
			if(x>=1&&x<=n&&y>=1&&y<=n&&!inq[x][y]&&graph[x][y]>=0){
				q.push(Node(x,y,node.layer+1));
				inq[x][y]=true;
			}
		}
	}
	cout<<ans<<endl;
	return 0;
}
```

# *201403-1相反数

```
基本思路1：
int a[1000]=1001;
输入数字b,
if(a[abs[b]]==1001)a[abs[b]]=b;
else if(a[abs[b]]==-b)a[abs[b]]==0;
最后，统计有多少个为0
//错误：若输入1 -1 1 -1就错了
```

```
基本思路2：
int a[2001]=0;//输入为1则a[1001]++,输入为-1,则a[1]++;
int num=0;
while(输入为b){
	if(b>0){
		if(a[b]!=0){ 
			a[b]--;
			num++;
		}else{
			a[b+1000]++;
		}
	}else{
		if(a[1000-b]!=0){
			a[1000-b]--;
			num++;
		}else{
			a[-b]++;
		}
	}
}
cout<<num;
```

# *201403-2 窗口

```
基本思路1：
注意：被点击的窗口要移到顶层
int layer[];//layer[0]表示最顶层的窗口的编号，layer[1]是次顶层的
int bound[][4];//表示每个窗口的bound
输入每个窗口的bound；
while(输入点击位置到x,y){
	从layer[0]到最后找第一个容纳x,y的窗口编号，设为layer[a];
	if(a越界即不存在这个窗口){cout<<"ignored"}
	else{
		cout<<layer[a];
		把layer[a]移到layer数组的最前面
	}
}
```

# 201403-3 命令行选项

```
struct Node{
	char op;
	string arg;
	//重载小于运算符
	//构造函数
}
int isop[256]=0; //若是无参数选项，则为1，有参数选项，则为2
set<Node> se;
输入，看有哪些选项；
while(输入一行命令，每个单词保存在strs中){
	int i=1,siz=strs[i].size();
	while(i<siz){
		if(strs[i][0]!='-'||strs[i].size()!=2||isop[strs[i][1]]==0)break;//如果不是选项
		char op=strs[i][1];
		if(isop[op]==1){
			se.insert(Node(op,""));
		}else{
			if(i+1<siz){
				se.insert(Node(op,strs[i+1]));
				i++;
			}
		}
		i++;
	}
}
//错误：不用该用set存储结果，而应该用map
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int isop[256]; //若是无参数选项，则为1，有参数选项，则为2
int n;
map<char,string> mp;
void init(){
	memset(isop,0,sizeof(isop));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	string ops;
	cin>>ops;
	for(int i=0;i<ops.size();){
		if(i+1<ops.size()&&ops[i+1]==':'){
			isop[ops[i]]=2;
			i+=2;
		}else{
			isop[ops[i]]=1;
			i++;
		}
	}
	scanf("%d\n",&n);
	int cnt=1;
	while(n--){
		vector<string> strs;
		string temp;
		/*while(cin>>temp&&cin.get()!='\n'){
			strs.push_back(temp);
		}*/
        while(cin>>temp){
			strs.push_back(temp);
            if(cin.get()=='\n')break;
		}
		
		int i=1,siz=strs.size();
		while(i<siz){
			if(strs[i][0]!='-'||strs[i].size()!=2||isop[strs[i][1]]==0)break;//如果不是选项
			char op=strs[i][1];
			if(isop[op]==1){
				mp[op]="";
			}else{
				if(i+1<siz){
					mp[op]=strs[i+1];
					i++;
				}
			}
			i++;
		}
		cout<<"Case "<<cnt<<":";
		for(auto item:mp){
			cout<<" -"<<item.first;
			if(item.second!=""){
				cout<<" "<<item.second;
			}
		}
		cout<<endl;
		mp.clear(); //error:
		cnt++;
	}
} 
```

错误分析：

1. 32行的逻辑错了

# 201403-4 无线网络

这题有个陷阱。

https://blog.csdn.net/richenyunqi/article/details/87906639

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXNM 105+105

struct Router{
    int x,y;
    int num,numk,type; //路径上路由的个数，k值, type=0表示普通
};
Router routers[MAXNM];//0:普通 1:增设
bool inq[MAXNM];
int inqk[MAXNM];
int n,m,k,r;

void init(){
    for(int i=0;i<MAXNM;i++){
        inq[i]=false;
    }
}
bool isLink(Router r1,Router r2){ //r1与r2不为同一个且距离小于r时返回true
    if(r1.x==r2.x && r1.y==r2.y)
        return false;
    double dis=sqrt(pow(r1.x-r2.x,2)+pow(r1.y-r2.y,2));
    if(dis>(double)r) //error: abs(r1.x=r2.x)
        return false;
    return true;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    init();
    cin>>n>>m>>k>>r;
    for(int i=1;i<=n+m;i++){
        scanf("%d %d",&routers[i].x,&routers[i].y);
        if(i>n)routers[i].type=1;
        else routers[i].type=0;
    }
    //BFS
    queue<Router> q;
    Router route=routers[1]; route.num=1; route.numk=0;
    q.push(route);
    inq[1]=true;
    inqk[1]=0;
    while(!q.empty()){
        route=q.front(); q.pop();
        if(route.x==routers[2].x && route.y==routers[2].y){
            break;
        }
        for(int i=1;i<=n+m;i++){//error: for(int i=1;i<=n+m&&isLink(route,routers[i]);i++)
            if(!isLink(route,routers[i]))
                continue;
            int curk=route.numk + (routers[i].type==1?1:0); //error: 语法错误：用二元操作符时要用括号，因为优先级问题
            if((inq[i]==false||curk<inqk[i]) && curk<=k){ //error: if((inq[i]==false||curk<inqk[i]))
                Router route2 = routers[i];
                route2.num=route.num+1;
                route2.numk=curk;
                route2.type=routers[i].type;
                q.push(route2); //error: 忘了push了
                inq[i]=true;
                inqk[i]=curk;
            }
        }
    }
    cout<<route.num-2<<endl;
}
```

错误分析：

1. 语法错误：51行，用双目运算符时要用括号，否则会出错，这个错误很难找。
2. 细节错误：48行，本来想实现不满足isLink()条件时continue，但写成了那个错误的写法

# *201312-1出现次数最多的数

```
基本思路1：
由于题目中要求计算所有数中出现次数最小的，则可以建立次数到数的集合的映射：map<int,set<int>> mp。
每读一个数，看它是否在mp[i]中，若在，则删除mp[i]中的这个数，若mp[i]为空，则删除mp[i],然后mp[i+1]插
入这个数；若不在，则mp[1]插入这个数
```

```
基本思路2：
思路1时间复杂度太高了，由于每个数的大小范围为[0,10000]，则可以定义一个数组A，表示每个数出现的次数。
```

# *201312-2 ISBN号码

```
基本思路1：
1).输入到string str中。
2).使用stringstream来分割字符串，到vecs中
3).计算校验码，看是否正确。注意：校验码为10是，最后一位是X
```

# 201312-3最大的矩形

```
基本思路1：
h[]表示每个方柱的高度。
考虑第以第i个方柱结尾的矩形的最大面积。
int minh, j=i, maxa=-1; //j是矩形最左边的方柱
while(j>=0){
	minh=h[j];
	if(minh==0)break;
	while(j>=1&&h[j-1]>=minh)j--;
	算此时的面积,更新maxa
	j--;
}
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
int h[MAXN];
int n,maxarea=-1;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++)cin>>h[i];
	for(int i=0;i<n;i++){
		int minh,j=i,maxa=-1;
		while(j>=0){
			minh=h[j];
			if(minh==0)break;
			while(j>=1&&h[j-1]>=minh)j--;
			int area=(i-j+1)*minh;
			maxa=(area>maxa?area:maxa);
			j--;
		}
		maxarea=max(maxarea,maxa);
	}
	cout<<maxarea<<endl;
	return 0;
} 
```

# 201312-4 有趣的数

```
基本思路1：
设每个位置的数据为pos[]，可以证明，pos[0]必为2，那么就考虑pos[1]到pos[n-1]应该有多少种取值情况。
设第一次出现1的位置为a,即pos[a]=1，第一次出现3的位置为b,即pos[b]=3。
若a<b: 则，有约束条件，a>1. 算a,b的取值有多少种情况，累加每种情况下的情况数，
	例如：计算pos[1]到pos[a-1]的情况数*pos[a+1]到pos[b-1]的情况数*pos[b+1]到pos[n-1]的情况数
	注意：pos[1]到pos[a-1]内至少要有1个0
若a>b：则，有约束条件，a>2，与上面类似，注意：pos[1]到pos[a-1]内至少有1个0
```

```
基本思路1_2:优化方案。
由于编程中大量用到计算2的p次方，因此，可以保存2的p次方，0<=p<=n
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
#define MOD (long long)1000000007
typedef long long lld;
int n;
lld powarr[MAXN];
void init(){
	for(int i=0;i<MAXN;i++)powarr[i]=-1;
}
lld pow2(int x,int p){
	lld res=1;
	for(int i=0;i<p;i++){
		res=(res*x)%MOD;
	}
	powarr[p]=res;
	return res;
}
lld getPow(int x,int p){
	return (powarr[p]!=-1?powarr[p]:pow2(x,p));
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n;
	lld ans=0,ans1=0,ans2=0;
	for(int a=2;a<n-1;a++){
		for(int b=a+1;b<n;b++){
			lld temp=1;
			temp=(temp*getPow(2,a-1)-1)%MOD;
			temp=(temp*getPow(2,b-a-1))%MOD;
			temp=(temp*getPow(2,n-b-1))%MOD;
			ans1=(ans1+temp)%MOD;
		}
	}
	for(int a=3;a<n;a++){
		for(int b=1;b<a;b++){
			lld temp=1;
			temp=(temp*getPow(2,b-1))%MOD;
			temp=(temp*getPow(2,a-b-1))%MOD;
			temp=(temp-1)%MOD;
			temp=(temp*getPow(2,n-a-1))%MOD;
			ans2=(ans2+temp)%MOD;
		}
	}
	ans=(ans1+ans2)%MOD;
	cout<<ans<<endl;
}
```

