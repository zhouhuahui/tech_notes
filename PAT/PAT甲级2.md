# A1020 Tree Traversals(树)

```
根据后序和中序遍历构造一棵树。
```

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

# A1021 Deepest Root(并查集+DFS)

```
基本思路：
数据：k(连通块个数);n(点的个数);vis[n];
1).由一个点遍历整个图，访问了点之后置vis=1,然后统计总共访问了多少个点:n1。
2).若n1==n，则去3);否则，去4)
3).从每个点遍历，更新maxDepth和resRoots集合; 输出,return;
4).k++; while(k!=n){从一个未访问过的点遍历;k++}
5)输出

evaluate: 3)过程时间复杂度非常高，可替换未以下算法
```

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
4. 算法逻辑错误。91行，先从一个点找最深节点，结果在A中，再从A中任意一个结点出发找最深结点，结果在B中，而无需对A中所有结点进行DFS，否则会超时。

# A1022 Digital Library(倒排索引)

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

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005

struct Lib{
	string title,author,publisher;
	vector<string> keys;
	int year,id;
};
Lib lib[MAXN];
int n,m;

void input(int index){
	cin>>lib[index].id; 
	cin.get();
	getline(cin,lib[index].title);
	getline(cin,lib[index].author);
	string key="";
	while(cin>>key){
		lib[index].keys.push_back(key);
		if(cin.get()=='\n')
			break;
	}
	getline(cin,lib[index].publisher);
	cin>>lib[index].year; cin.get();
}

vector<int> ans; //编号集合 
void Search(int type,string&query){
	ans.clear(); //清空
	if(type==5){
		int year=stoi(query);
		for(int i=0;i<n;i++){
			if(lib[i].year==year){
				ans.push_back(lib[i].id);
			}
		}
	}else if(type!=3){
		for(int i=0;i<n;i++){
			string&s1=(type==1?lib[i].title:type==2?lib[i].author:lib[i].publisher);
			if(query==s1){
				ans.push_back(lib[i].id);
			}
		}
	}else{
		for(int i=0;i<n;i++){
			if(find(lib[i].keys.begin(),lib[i].keys.end(),query) != lib[i].keys.end()){
				ans.push_back(lib[i].id);
			}
		}
	}
}
int main(){
	ios::sync_with_stdio(false);
	//freopen("D:\\input.txt","r",stdin);
	cin>>n; cin.get();
	for(int i=0;i<n;i++){
		input(i);
	}
	cin>>m;
	while(m--){
		int type;
		string query;
		cin>>type;
		cin.get(); cin.get();
		getline(cin,query);
		Search(type,query); 
		//输出
		cout<<type<<": "<<query<<endl; 
		if(ans.empty()){
			cout<<"Not Found"<<endl;
		}else{
			for(int i=0;i<ans.size();i++){
				sort(ans.begin(),ans.end());
				//printf("%07d\n",ans[i]); //要改正 
				cout<<setw(7)<<setfill('0')<<ans[i]<<endl;
			}
		}
	}
} 
```

**别人的思路**

https://blog.csdn.net/A_Aria/article/details/86558688

这里有个思路比较好。由于题目只要关键词对应的所有书本ID，因此在数据输入时就建立，关键词-书本ID集的对应关系，如``悲惨世界-{0000001, 0000002, 0000003}`` 。这样既简单，又高效

# A1023 Have Fun With Numbers(软件乘法)

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int cnt[10],cnt2[10];
long long num,num2;
void init(){
	memset(cnt,0,10*sizeof(int));
	memset(cnt2,0,10*sizeof(int));
}
void analyse(long long nm,int res[]){
	string s=to_string(nm);
	for(int i=0;i<s.size();i++){
		res[s[i]-'0']++; //error:res[s[i]]++;
	}
}
int main(){
	init();//init
	cin>>num;
	num2=num*2;
	analyse(num,cnt);
	analyse(num2,cnt2);
	for(int i=0;i<10;i++){
		if(cnt[i]!=cnt2[i]){
			cout<<"No"<<endl;
			goto output;
		}
	}
	cout<<"Yes"<<endl;
	output:;
	cout<<num2<<endl;
}
```

11分：

错误分析：

1. 这道题的输入有20位，乘以2之后有21位，超过了long long的20位，所以不能用long long来算。

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

string num1,num2;
int cnt[10],cnt2[10];
//num1乘以2放到num2中 
void multi2(){
	int c=0;//进位
	for(int i=num1.size()-1;i>=0;i--){
		int a=(num1[i]-'0')*2+c;
		c=0;
		//num2.append(a%10+'0'); //error: 不能append 字符
		num2.insert(num2.end(),a%10+'0'); 
		c=a/10;
	} 
	if(c==1)num2.insert(num2.end(),c+'0');
	reverse(num2.begin(),num2.end());
}
int main(){
	cin>>num1;
	multi2();
	for(int i=0;i<num1.size();i++){
		cnt[num1[i]-'0']++;
	}
	for(int i=0;i<num2.size();i++){
		cnt2[num2[i]-'0']++;
	}
	for(int i=0;i<10;i++){
		if(cnt[i]!=cnt2[i]){
			cout<<"No"<<endl;
			goto output;
		}
	}
	cout<<"Yes"<<endl;
	output:;
	cout<<num2<<endl;
}
```

# A1024 Palindromic Number

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int k;
string n;

void operate(string& s){
	string s2=s,res; 
	reverse(s2.begin(),s2.end());
	//s和s2相加
	int siz=s.size(),c=0;
	for(int i=siz-1;i>=0;i--){
		int a=(s[i]-'0')+(s2[i]-'0')+c;
		res.insert(res.end(),a%10+'0');
		c=a/10;
	} 
	if(c!=0)res.insert(res.end(),c+'0');
	reverse(res.begin(),res.end());
	s=res;
}
bool isPalindromic(string& s){
	int siz=s.size();
	for(int i=0;i<=siz/2;i++){
		if(s[i]!=s[siz-i-1]){
			return false;
		}
	}
	return true;
}
int main(){
	cin>>n>>k;
	if(isPalindromic(n)){
		cout<<n<<endl;
		cout<<0;
		return 0;
	}
	for(int i=1;i<=k;i++){
		operate(n);
		if(isPalindromic(n)){
			cout<<n<<endl;
			cout<<i<<endl;
			return 0;
		}
	}
	cout<<n<<endl;
	cout<<k<<endl;
	return 0;
} 
```

# A1025 PAT Ranking(排序)

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

# A1026 Table Tennis(难)

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

```
基本思路：
数据：
Player{到达时间;vip;玩耍时间;服务时间=-1}；players[]
Table{服务人数;空闲时间;vip}; tables[]
vipIndex:当前访问的顾客（及以后）的第一个vip玩家的下标
1).数据输入；根据到达时间排序
2).vipIndex=第0个及以后的第一个vip的下标
   1.访问player=players[0],第一个空闲的table,若table的空闲时间大于17:00:00，则回到3)
     若player是vip且与vipIndex相同:找有没有在table的空闲时间和player的到达时间之间的vip_table，
     	若有：则考虑更新player和vip_table的数据项（player的服务时间，vip_table的服务人数，空闲时间）
     	若没有：则考虑更新player和table
     	更新vipIndex
     若player不是vip：
     	若table是vip:则看vipIndex对应的玩家vip_player的到达时间是否在table的空闲时间之前，
     		若是：则让vip_player和table匹配，更新vipIndex
     		否则：让player和table匹配
     	若table不是vip:则匹配table和player
    2.......考虑player=players[n-1].......
3).输出
```

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

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 10005
#define MAXK 105

struct Player{
	int arriveTime,playTime,serveTime;
	bool vip;
	Player(){
		serveTime=-1;
	}
};
struct Table{
	int serveNum,vacancyTime;
	bool vip;
	Table(){
		serveNum=0;
		vacancyTime=8*3600;
		vip=false;
	}
};
Player players[MAXN];
Table tables[MAXK];
int n,k,m;

void init(){
	for(int i=0;i<MAXN;i++)players[i]=Player();
	for(int i=0;i<MAXK;i++)tables[i]=Table();
}

bool cmp(const Player&p1,const Player&p2){
	return p1.arriveTime<p2.arriveTime;
}
//从i开始寻找下一个vipIndex 
int findNextVipIndex(int i){
	while(i<n){
		if(players[i].vip==true){
			return i;
		}
		i++;
	}
	return -1;
}
//找到最早空闲的table的下标 
int findEarlyVacancy(){
	int minpop=INT_MAX,mintable=-1;
	for(int i=1;i<=k;i++){
		if(tables[i].vacancyTime<minpop){
			minpop=tables[i].vacancyTime;
			mintable=i;
		}
	}
	return mintable;
}
//更新players[a]和tables[b] 
void update(int a,int b){
	players[a].serveTime=max(players[a].arriveTime,tables[b].vacancyTime);
	tables[b].serveNum++;
	//tables[b].vacancyTime+=min(3600*2,players[a].playTime); //error
	tables[b].vacancyTime = players[a].serveTime+min(3600*2,players[a].playTime);
}
bool cmp2(const Player&p1,const Player&p2){
	if(p1.serveTime != -1){
		return p1.serveTime<p2.serveTime;
	}else{
		//return p2.serveTime!=-1; //error:
		return p2.serveTime==-1;
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	//输入数据和排序 
	cin>>n;
	for(int i=0;i<n;i++){
		int hh,mm,ss,playTime,vip;
		scanf("%d:%d:%d %d %d",&hh,&mm,&ss,&playTime,&vip);
		players[i].arriveTime=hh*3600+mm*60+ss;
		players[i].playTime=playTime*60;
		players[i].vip=(vip==1?true:false);
	}
	cin>>k>>m;
	for(int i=0;i<m;i++){
		int a;
		cin>>a;
		tables[a].vip=true;
	}
	sort(players,players+n,cmp);
	//运行
	int vipIndex=findNextVipIndex(0);
	for(int i=0;i<n;i++){
		int minTableIndex=findEarlyVacancy();
		if(tables[minTableIndex].vacancyTime>=21*3600){
			break;
		}
		if(players[i].vip && i==vipIndex){
			int vipTableIndex=-1;  
			for(int j=1;j<=k;j++){
				if(tables[j].vacancyTime>=tables[minTableIndex].vacancyTime
					&& tables[j].vacancyTime<=players[i].arriveTime && tables[j].vip==true){
					vipTableIndex=j;	
				}
			}
			if(vipTableIndex==-1){
				update(i,minTableIndex);
			}else{
				update(i,vipTableIndex);
			}
			vipIndex=findNextVipIndex(i+1); 
		}else if(players[i].vip==false){
			if(tables[minTableIndex].vip==true){
				if(vipIndex!=-1 && players[vipIndex].arriveTime<=tables[minTableIndex].vacancyTime){
					update(vipIndex,minTableIndex);
					//vipIndex=findNextVipIndex(minTableIndex+1); //error:
					vipIndex=findNextVipIndex(vipIndex+1);
					i--;
				}else{
					update(i,minTableIndex);
				}
			}else{
				update(i,minTableIndex);
			}
		}
	}
	//输出
	sort(players,players+n,cmp2);
	for(int i=0;i<n;i++){
		if(players[i].serveTime==-1){
			break;
		}
		int time1 = players[i].arriveTime;
        int time2 = players[i].serveTime;
        int h1 = time1/3600, m1 = (time1%3600)/60, s1 = (time1%3600)%60;
        int h2 = time2/3600, m2 = (time2%3600)/60, s2 = (time2%3600)%60;
        printf("%02d:%02d:%02d %02d:%02d:%02d %.0f\n",h1,m1,s1,h2,m2,s2,round((time2-time1)/60.0));
	}
	for(int i=1;i<=k;i++){
		if(i==1)cout<<tables[i].serveNum;
		else cout<<" "<<tables[i].serveNum;
	}
}
```

我花了1个小时找出bug，而且还是靠运气。

细节错误，和比较函数出错了

# A1027 Colors in Mars

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int a,b,c;
void convert(int x){
	string s;
	if(x/13>9){
		s.insert(s.end(),'A'+x/13-10);
	}else{
		s.insert(s.end(),'0'+x/13);
	}
	if(x%13>9){
		s.insert(s.end(),'A'+x%13-10);
	}else{
		s.insert(s.end(),'0'+x%13);
	}
	cout<<s;
}
int main(){
	cin>>a>>b>>c;
	cout<<"#";
	convert(a);
	convert(b);
	convert(c);
} 
```

# A1028 List Sorting(排序)

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
//#include<bits/stdc++.h>
//using namespace std;
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXN 100005
typedef struct Record{
	int id;
	char name[10];
	int grade;
}Record;
typedef int (*Comp)(Record*,Record*);
int n,c;
Record recs[MAXN];

int cmp1(Record* r1,Record* r2){
	return r1->id<r2->id?1:0;
}
int cmp2(Record* r1,Record* r2){
	int a;
	if((a=strcmp(r1->name,r2->name))!=0)return a<0?1:0;
	else return r1->id<r2->id?1:0;
}
int cmp3(Record* r1,Record* r2){
	if(r1->grade!=r2->grade)return r1->grade<r2->grade?1:0;
	else return r1->id<r2->id?1:0;
}
void downAdjust(int a,int b,Comp cmp){
	int i=a,j=2*a;
	while(j<=b){
		if(j+1<=b){
			if(cmp(&recs[j],&recs[j+1])==1)j=j+1; 
		}
		if(cmp(&recs[i],&recs[j])==1){
			//交换recs[i]与recs[j]
			Record temp=recs[i];
			recs[i]=recs[j];
			recs[j]=temp;
			i=j;
			j=i*2; 
		}else{
			break;
		}
	}
}
void heapSort(int a,int b,Comp cmp){ 
	if(b<=a)return;
	//建堆
	int i;
	for(i=(a+b)/2;i>=a;i--){
		downAdjust(i,b,cmp);
	} 
	//排序 
	while(b>a){
		//交换recs[a]与recs[b]
		Record temp;
		temp=recs[a];
		recs[a]=recs[b];
		recs[b]=temp;
		//downAdjust(a,b-1)
		downAdjust(a,b-1,cmp);
		b--;
	}
}
int main(){
	freopen("D:\\input.txt","r",stdin);
	scanf("%d %d\n",&n,&c);
	int i;
	for(i=1;i<=n;i++){
		scanf("%d %s %d",&recs[i].id,&recs[i].name,&recs[i].grade);
	}
	heapSort(1,n,(c==1?cmp1:c==2?cmp2:cmp3));
	//输出
	for(i=1;i<=n;i++){
		printf("%06d %s %d\n",recs[i].id,recs[i].name,recs[i].grade);
	} 
	return 0;
}
```

# A1029 Median(排序)

直接用排序就解决了

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

# A1030 Travel Plan(图论)

Dijkstra+DFS

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

# A1031 Hello World for U(数学问题)

```
设高度为x个字符，宽度为y个字符。
则有:2*x+y-2=n
    x<=y and x最大
解的x=(n+2)/3
```

```cpp
#include<cstdio>
#include<cmath>
using namespace std;

void erase_n(char* s){
    int i = 0;
    while(s[i] != '\n' && s[i] != '\0')
        i++;
    s[i] = '\0';
}
int computeSize(char* s){
    int i = 0;
    while(s[i] != '\0')
        i++;
    return i;
}
int main(){
    char str[85];
    fgets(str,85,stdin);
    erase_n(str);

    int n = computeSize(str);
    int x,y;
    x = floor((n+2)/3.0);
    y = n + 2 - 2*x;

    for(int i=0; i<x-1; i++){
        printf("%c",str[i]);
        for(int j=0; j<y-2; j++){
            printf(" ");
        }
        printf("%c\n",str[n-i-1]);
    }
    for(int i=x-1; i<x-1+y; i++){
        printf("%c",str[i]);
    }
}
```

# A1032 Sharing(静态链表)

静态链表存储+链表遍历

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

# A1033 To Fill or Not to Fill（贪心，难）

```
题意：在一条公路上有很多个加油站，每个加油站的单价不同，在起始点（杭州）时车的油量为0，问在哪些加油站加油，
，加多少油，可以使的从杭州到目的地的费用最低；若不能到达目的地，最多可以走多远。
基本思路：尽量在可以在单价低的加油站加够足够的油。(贪心算法)
1).在自己的油量可以到达的最大距离（当前距离-->当前距离+满油可以走的距离）范围内，有没有比当前加油站单价低的，
2).若有：则加到可以到达此加油站A的油即可（看自己的当前油量决定，若当前油量不够就加，够就不用加），走到那个加油站A  
   若没有：最远能不能到达终点，
      若可以：则直接加到够到达终点的油，走到终点
      若不可以：则加满油，
          若不可以到达最近的加油站：走到最远的距离，回到4)
          若可以：则走到最远可以到达的加油站
3).若到达了终点：回到4)
   若还没有：回到1)
4)输出
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 505 

struct Sotation{
	double p;
	int dist;
};
int cmax,d,davg,n; 
Station stations[MAXN];

bool cmp(const Station&s1,const Station&s2){
	return s1.dist<s2.dist;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	//输入
	scanf("%d %d %d %d\n",&cmax,&d,&davg,&n);
	for(int i=0;i<n;i++){
		scanf("%lf %d",&stations[i].p,&stations[i].dist);
	}
	//排序
	sort(stations,stations+n,cmp); 
	
	int curSta=0;//当前的加油站编号 
	const int MAXCOV=cmax*davg;
	double curGas=0.0,curDist=0.0,money=0.0;//当前油量,当前距离,目前花的钱 
	while(true){
		//printf("curSta=%d curDist=%.2f curGas=%.2f money=%.2f\n",curSta,curDist,curGas,money);//////
		if(stations[0].dist != 0)break;
		//能到达的加油站中的单价比当前低的 
		int nextSta=-1,i;
		for(i=curSta+1;i<n;i++){
			if(stations[i].dist<=stations[curSta].dist+MAXCOV){
				if(stations[i].p<stations[curSta].p){
					nextSta=i;
					break;
				}
			}else{
				break;
			}
		}
		//如果有价格较低的 
		if(nextSta!=-1){
			//double a=min((double)(stations[nextSta].dist-stations[curSta].dist)/davg-curGas,0.0);//需要加的油
			double a=max((double)(stations[nextSta].dist-stations[curSta].dist)/davg-curGas,0.0);//需要加的油
			money+=a*stations[curSta].p; 
			curGas+=a;
			//走到下一个地方 
			curGas-=(double)(stations[nextSta].dist-stations[curSta].dist)/davg;//error
			curDist=stations[nextSta].dist;
			curSta=nextSta;
		}else{ //如果没有 
			//如果最远可以到达终点
			if(curDist+MAXCOV>=d){
				double a=max((double)(d-curDist)/davg-curGas,0.0);
				money+=a*stations[curSta].p;
				curGas+=a;
				//走到一个地方 
				curGas-=(double)(d-curDist)/davg;
				curDist=(double)d;
				curSta=-1;
			}else{ //若不可以 
				//若不能到达下一站 
				if(curSta==n-1 || curDist+MAXCOV<stations[curSta+1].dist){
					//走到最远的距离 
					curDist+=MAXCOV;
					break;
				}else{//若可以 
					//走到最远的加油站
					nextSta=curSta;
					while(stations[nextSta].dist<=stations[curSta].dist+MAXCOV){
						nextSta++;
					}
					nextSta--;
					double a=max((double)cmax-curGas,0.0);
					money+=a*stations[curSta].p;
					curGas+=a;
					//走到一个地方 
					curGas-=(double)(stations[nextSta].dist-stations[curSta].dist)/davg;
					curDist=stations[nextSta].dist;
					curSta=nextSta;
				}
			}
		} 
		//若到达了终点 
		if(curDist==(double)d){
			break;
		}
	}
	//输出 
	if(curDist<(double)d){ //如果不能到达终点 
		printf("The maximum travel distance = %.2f\n",curDist);
	}else{
		printf("%.2f\n",money);
	}
}
```

错误分析：

1. 数据更新错误：53行，更新当前油量curGas错误，既然有加油操作，就应该有走到另一个地点之后的减油操作；
2. 逻辑错误：78行对应基本思路的第8行，但是我实现错了，应该加满油，而不是正好加到到下一个地点的油量

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

# A1034 head of gang（图论,难）

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

1. 55行：gang是string 到 int的map，所以在更新gang时，要把编号换成姓名
2. 39行：更新总边权totalValue出错，将点权累加进去了
3. 54行：if条件少了，少考虑了帮会人数大于2的条件
4. 69和70行：更新邻接图时，应该将通话时间累加上去，而不是单纯地赋值，因为在输入中，可能出现相同的名字对，表示两个罪犯不同的通话记录。

# A1035 Password

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
vector<pair<string,string>> mv;
map<char,char> tableMp={{'1','@'},{'0','%'},{'l','L'},{'O','o'}};

bool canModify(string&s){
	int siz=s.size();
	bool mflag=false;
	for(int i=0;i<siz;i++){
		if(tableMp.find(s[i]) != tableMp.end()){
			mflag=true;
			s[i]=tableMp[s[i]];
		}
	}
	return mflag;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	int cnt=0;//记录多少个密码被修改 
	string account,password;
	for(int i=0;i<n;i++){
		cin>>account>>password;
		if(canModify(password)){
			cnt++;
			mv.push_back({account,password});
		}
	}
	if(cnt==0){
		if(n==1){
			cout<<"There is 1 account and no account is modified"<<endl;
		}else{
			cout<<"There are "<<n<<" accounts and no account is modified"<<endl; //error: Ther is
		}
	}else{
		cout<<cnt<<endl;
		for(auto& i:mv){
			cout<<i.first<<" "<<i.second<<endl;
		}
	}
}
```

错误分析：

1. 细节错误：36行

# A1036 Boys vs. Girls

找最大值，最小值

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	int fMax=-1,mMin=101;
	string fName,mName,fId,mId;
	string name,gender,id;
	int grade;
	while(n--){
		cin>>name>>gender>>id>>grade;
		if(gender=="M"){
			if(grade<mMin){
				mMin=grade;
				mName=name;
				mId=id;
			}
		}else{
			if(grade>fMax){
				fMax=grade;
				fName=name;
				fId=id;
			}
		}
	}
	if(fMax!=-1){
		cout<<fName<<" "<<fId<<endl;
	}else{
		cout<<"Absent"<<endl;
	}
	if(mMin!=101){
		cout<<mName<<" "<<mId<<endl;
	}else{
		cout<<"Absent"<<endl;
	}
	if(fMax==-1 || mMin==101){
		cout<<"NA"<<endl;
	}else{
		cout<<fMax-mMin<<endl;
	}
	return 0;
}
```

# A1037 Magic Coupon(贪心)

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int nc,np;
int coupons[MAXN];
int costs[MAXN];

bool cmp(int a,int b){
	/*if(a>0)return a>b;
	else return (b<0?a<b:a>b);*/
	if(a>0)return a>b;
	else if(a==0) return false;
	else return (b<0?a<b:b>0?false:true);
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&nc);
	for(int i=0;i<nc;i++){
		scanf("%d",&coupons[i]);
	}
	scanf("%d",&np);
	for(int i=0;i<np;i++){
		scanf("%d",&costs[i]);
	}
	sort(coupons,coupons+nc,cmp);
	sort(costs,costs+np,cmp);
	int i=0,j=0;
	long long money=0;
	while(i<nc && j<np){
		if(coupons[i]>0 && costs[j]>0){
			money+=coupons[i++]*costs[j++];
		}else if(coupons[i]<0 && costs[j]<0){
			money+=coupons[i++]*costs[j++];
		}else if(coupons[i]>=0){
			i++;
		}else if(costs[j]>=0){
			j++;
		}
	}
	printf("%lld",money);
}
```

错误分析：

1. 逻辑错误：12行，对排序逻辑写错了：我想要的排序规则是，正数在前，右大到小排序，负数在正数后面，由小到大排序，0排在最后。可是12行的代码没有考虑0的情况。

# A1038 Recover the smallest number(难)

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

# A1039 Course List for Student(倒排索引)

字符串hash+查询

```cpp
/*
基本思路1：用map<string,vector<int>>存储每个学生的所有课程
基本思路2：由于string查询会慢，再加上题目中的学生姓名是只有4个字符的，所以用简单的hash来转换字符串
为数字，然后建立vector<int> couList[MAXN],couList[hash(nameStr)]来得到学生的课程列表
*/
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 175760

vector<int> couList[MAXN];
int n,k;
int hash2(char name[]){
	int res=0;
	for(int i=0;i<4;i++){
		if(i==3){
			res=res*10+name[i]-'0';
		}else{
			res=res*26+name[i]-'A';
		}
	}
	return res;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d",&n,&k);
	int a,b;
	char name[5];
	while(k--){
		scanf("%d %d",&a,&b);
		while(b--){
			scanf("%s",&name);
			couList[hash2(name)].push_back(a);
		}
	}
	//输出
	while(n--){
		scanf("%s",&name);
		int index=hash2(name);
		sort(couList[index].begin(),couList[index].end());
		printf("%s %d",name,couList[index].size());
		for(int i:couList[index]){
			printf(" %d",i);
		}
		printf("\n");
	} 
}
```

