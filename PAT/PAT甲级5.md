# A1080 Graduate Admission（模拟）

```
这题思路很难找对，要不是看了别人的思路，我就做不出来
主要步骤是：对学生排序，并得到他们的rank,然后根据rank由大到小考虑这些学生，根据学生的志愿依次考虑学校
是否有空额，若有则录取该学生，若所有志愿学校都没有空额，则失败。
```

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

# A1081Rational Sum

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
typedef long long lld;

struct Fraction{
	lld up,down;
};
int n;

lld gcd(lld a,lld b){
	if(b==0)return a;
	else return gcd(b,a%b);
}
//约简 
void reduction(Fraction&f){
	if(f.up==0){
		f.down=1;
		return;
	}
	if(f.down<0){
		f.up=-f.up;
		f.down=-f.down;
	}
	lld a=gcd(abs(f.up),f.down);
	f.up/=a;
	f.down/=a;
}
//加 
Fraction add(Fraction&f1,Fraction&f2){
	Fraction res;
	res.up=f1.up*f2.down+f2.up*f1.down;
	res.down=f1.down*f2.down;
	reduction(res);
	return res;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	Fraction a,b;
	string s;
	for(int i=0;i<n;i++){
		cin>>s;
		int k=s.find('/');
		if(i==0){
			a.up=stoi(s.substr(0,k));
			a.down=stoi(s.substr(k+1));
			reduction(a);
		}else{
			b.up=stoi(s.substr(0,k));
			b.down=stoi(s.substr(k+1));
			reduction(b);
			a=add(a,b);
		}
	}
	if(abs(a.up)>=a.down){
		printf("%lld",a.up/a.down);
		lld mod=a.up%a.down;
		if(mod!=0)printf(" %lld/%lld",mod,a.down);
	}else{
		if(a.up==0){ //error: 注意，a.up==0时，不要再输出分母了 
			printf("0");
		}else{
			printf("%lld/%lld",a.up,a.down);
		}
	}
}
```

错误分析：

1. 细节错误：62

# A1082 Read Number in Chinese(字符串处理，难)

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

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

string bit[10] = { string(""),string("Shi"),string("Bai"),string("Qian"),string("Wan"),string(""),string(""),string(""),string("Yi") };
string bit2[10] = { string("ling"),string("yi"),string("er"),string("san"),string("si"),string("wu"),string("liu"),string("qi"),string("ba"),string("jiu") };

int main(){
	//freopen("D:\\input.txt","r",stdin);
	string input; 
	cin>>input;
	if(stoi(input)==0){
		printf("ling");
		return 0;
	} 
	bool first=true;
	bool lastZero=false; //考虑0000 0030, 出现0000时记录lastZero=true 
	for(int i=0;i<input.size();){
		if(input[i]=='-'){
			printf("Fu");
			first=false;
			i++;
			continue;
		}
		int j=((input.size()-i)%4==0?i+3:i+(input.size()-i)%4-1); //[i.j]是一个区间
		//考虑区间内的读法
		for(int k=i;k<=j;k++){
			if(input[k]!='0')goto L1; //error: input[i]
		}
		lastZero=true;
		i=j+1;
		continue;
	L1:;
		while(i<=j){
			if(lastZero){
				printf(" ling");
				lastZero=false;
				while(i<=j && input[i]=='0')i++;
				continue;
			}
			if(input[i]=='0'){
				while(i<=j && input[i]=='0')i++;
				if(i<=j){ //0030的前面两个0的情况要输出0 
					if(first){
						printf("ling");
						first=false; //error；不应该写在else里面 
					}
					else {
						printf(" ling");
					}
				}
			}else{
				if(first){
					printf("%s",bit2[input[i]-'0'].c_str());
					first=false;
				}
				else {
					printf(" %s",bit2[input[i]-'0'].c_str());
				}
				if(j>i)
					printf(" %s",bit[j-i].c_str());
				i++;
			}
		}
		//考虑区间外的读法 
		if(input.size()-1>i)
			printf(" %s",bit[input.size()-i].c_str());
	} 
	return 0;
}
```

错误分析：

1. 细节错误：29，47

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

class Solution{
public:
    void readInChinese(string &input){
        string bit[10] = { string(""),string("Shi"),string("Bai"),string("Qian"),string("Wan"),string(""),string(""),string(""),string("Yi") };
        string bit2[10] = { string("ling"),string("yi"),string("er"),string("san"),string("si"),string("wu"),string("liu"),string("qi"),string("ba"),string("jiu") };
        vector<string> rd;
        int i,j;
        bool zero = false;
        if(input[0]=='-'){
            rd.push_back("Fu");
            i = 1;
        }else{
            i = 0;
        }
        if(input.size()-i<=4){
            j=input.size()-1;
        }else{
            j=(input.size()-i)%4+i-1;
        }
        while(i<input.size()){
            bool allzero = true;
            while(i<=j){
                if(input[i]=='0'){
                    zero = true;
                    while(i<=j && input[i]=='0')
                        i++;
                }else{
                    allzero = false;
                    if(zero){
                        rd.push_back("ling");
                        zero = false;
                    }
                    rd.push_back(bit2[input[i]-'0']);
                    if(j-i>0)
                        rd.push_back(bit[j-i]);
                    i++;
                }
            }
            if(!allzero && input.size()-1-j>0)
                rd.push_back(bit[input.size()-1-j]);
            j = i+3;
        }
        if(rd.size()==0 || (rd.size()==1&&rd[0]=="Fu")){
            cout<<"ling"<<endl;
        }else{
            for(int i=0;i<rd.size();i++){
                if(i==0) cout<<rd[0];
                else cout<<" "<<rd[i];
            }
            cout<<endl;
        }
    }
};

int main(){
    //freopen("D:\\input.txt","r",stdin);
    Solution sol;
    string input;
    cin>>input;
    sol.readInChinese(input);
}
```

# *A1084 Broken Keyboard（集合差集）

```
基本思路：
双指针法，i指向第一个str,j指向第二个str
若相等，则i++.j++;
否则，i指向的字符坏了，保存，i++
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

vector<char> worn;
char s1[100],s2[100];

int main(){
	//freopen("D:\\input.txt","r",stdin);
	fgets(s1,100,stdin);
	s1[strlen(s1)-1]=(s1[strlen(s1)-1]=='\n'?'\0':s1[strlen(s1)-1]);
	fgets(s2,100,stdin);
	s2[strlen(s2)-1]=(s2[strlen(s2)-1]=='\n'?'\0':s2[strlen(s2)-1]);
	int i=0,j=0;
	while(i<strlen(s1) && j<strlen(s2)){
		if(s1[i]!=s2[j]){
			if(find(worn.begin(),worn.end(),toupper(s1[i]))==worn.end())
				worn.push_back(toupper(s1[i]));
			i++;
		}else{
			i++;
			j++;
		}
	}
	for(char c:worn)printf("%c",c);
	return 0;
}
```

17分。最后一个样例没过

```
基本思路2：
开一个大小为256的数组arr，记录每个字符的信息。
遍历第二个字符串，每个字符对应的arr值为1.
遍历第一个字符串，每个字符对应的arr值若不为1，则说明第二个字符串没有这个字符，说明这个字符有问题。

这题其实就是找两个集合的差集
```

# A1085 Perfect Sequence(动态规划)

```
动态规划
```

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

# A1086 Tree Traversals Again（树，难）

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

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

//通过中根遍历的压栈出栈操作可以得到先序遍历和中序遍历
class Solution{
public:
    int n;
    vector<int> pre, in, post;
public:
    void getPreAndIn(){
        cin>>n;
        vector<int> pre2;
        for(int i=0;i<2*n;i++){
            string op;
            cin>>op;
            if(op == "Push"){
                int x;
                cin>>x;
                pre.push_back(x);
                pre2.push_back(x);
            }else{
                in.push_back(pre2.back());
                pre2.pop_back();
            }
        }
    }
    void getPostFromPreAndIn(int prest, int preed, int inst, int ined){
        if(prest > preed)
            return;
        int i=inst;
        while(pre[prest]!=in[i])
            i++;
        getPostFromPreAndIn(prest+1, prest+i-inst, inst, i-1);
        getPostFromPreAndIn(prest+i-inst+1, preed, i+1, ined);
        post.push_back(in[i]);
    }
};

int main(){
    //freopen("D:\\input.txt","r",stdin);
    Solution sol;
    sol.getPreAndIn();
    sol.getPostFromPreAndIn(0,sol.n-1,0,sol.n-1);
    for(int i=0;i<sol.post.size();i++){
        if(i==0)cout<<sol.post[i];
        else cout<<" "<<sol.post[i];
    }
}
```

# A1087 All Roads to Rome(图论)

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

# *A1089 Insert or Merge(排序)

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

# A1090 Highest price in Supply Chain(BFS)

```
树+BFS
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005
int n,root;
double p,r;
vector<int> graph[MAXN];
double S[MAXN];

double maxP=0.0;
int maxNum=0; 
void BFS(int s){
	queue<int> q;
	q.push(s);
	while(!q.empty()){
		int u=q.front();
		q.pop();
		if(S[u]>maxP){
			maxP=S[u];
			maxNum=1;
		}else if(S[u]==maxP){
			++maxNum;
		}
		for(int i=0;i<graph[u].size();i++){
			int v=graph[u][i];
			S[v]=S[u]*(100+r)/100;
			q.push(v);
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %lf %lf",&n,&p,&r);
	for(int i=0;i<n;i++){
		int a;
		scanf("%d",&a);
		if(a!=-1)graph[a].push_back(i);
		else root=i;
	}
	S[root]=p;
	BFS(root);
	printf("%.2lf %d",maxP,maxNum);
	return 0;
}
```

# A1091 Acute Stroke(BFS)

```
三维数组的BFS
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXM 1290
#define MAXN 130
#define MAXL 65
int l,m,n,t,totVol=0;
int slices[MAXL][MAXM][MAXN];
bool inq[MAXL][MAXM][MAXN];
array<int,3> dir[6]={{-1,0,0},{1,0,0},{0,0,-1},{0,0,1},{0,-1,0},{0,1,0}};

bool isLegal(array<int,3> arr){
	if(arr[0]<0||arr[0]>=l||arr[1]<0||arr[1]>=m||arr[2]<0||arr[2]>=n)return false;
	return !inq[arr[0]][arr[1]][arr[2]]&&slices[arr[0]][arr[1]][arr[2]]==1;
}
int maxNum=0;
void BFS(array<int,3> arr){
	maxNum=0;
	queue<array<int,3>> q;
	q.push(arr);
	inq[arr[0]][arr[1]][arr[2]]=true;
	while(!q.empty()){
		arr=q.front();
		q.pop();
		maxNum++;
		for(int i=0;i<6;i++){
			int a=arr[0]+dir[i][0],b=arr[1]+dir[i][1],c=arr[2]+dir[i][2];
			if(isLegal({a,b,c})){
				q.push({a,b,c});
				inq[a][b][c]=true;
			}
		}
	}
}
void init(){
	for(int i=0;i<MAXL;i++)
		for(int j=0;j<MAXM;j++)
			for(int k=0;k<MAXN;k++)
				inq[i][j][k]=false;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d %d %d",&m,&n,&l,&t);
	for(int i=0;i<l;i++){
		for(int j=0;j<m;j++){
			for(int k=0;k<n;k++){
				scanf("%d",&slices[i][j][k]);
			}
		}
	}
	for(int i=0;i<l;i++){
		for(int j=0;j<m;j++){
			for(int k=0;k<n;k++){
				if(!inq[i][j][k] && slices[i][j][k]==1){
					BFS({i,j,k});
					if(maxNum>=t)totVol+=maxNum;
				}
			}
		}
	}
	printf("%d",totVol);
	return 0;
}
```

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

# A1093 count PAT's(动态规划)

```
动态规划
```

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

# A1094 The Largest Generation(图+BFS)

```
树（邻接图表示）+BFS
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 105
vector<int> graph[MAXN];
int num[MAXN],n,m;

void init(){
	memset(num,0,sizeof(num));
}
void BFS(int s,int layer){
	queue<array<int,2>> q;
	q.push({s,layer});
	while(!q.empty()){
		s=q.front()[0];
		layer=q.front()[1];
		q.pop();
		++num[layer];
		for(int i=0;i<graph[s].size();i++){
			int v=graph[s][i];
			q.push({v,layer+1});
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d",&n,&m);
	for(int i=0;i<m;i++){
		int u,v,k;
		scanf("%d %d",&u,&k);
		while(k--){
			scanf("%d",&v);
			graph[u].push_back(v);
		}
	}
	BFS(1,1);
	int maxNum=0,layer,i=1;
	while(num[i]!=0){
		if(num[i]>maxNum){
			maxNum=num[i];
			layer=i;
		}
		i++;
	}
	printf("%d %d",maxNum,layer);
	return 0;
}
```

# A1095 Cars on Campus（栈，难）

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

```cpp
//首先把pair抽取出来，然后query时在这个pair序列中进行
#include<bits/stdc++.h>
using namespace std;
struct Record{
    string plateNum;//车名
    int time=0;//时间
    bool in=false;//停入还是开出
    Record(const string&s,int t,bool i):time(t),in(i){//构造函数
        plateNum=s;
    }
};
bool cmp(const Record&r1,const Record&r2){//比较函数
    return r1.time<r2.time;
}
int main(){
    int N,K;
    scanf("%d%d",&N,&K);
    unordered_map<string,vector<Record>>allCar;//存储车名与其对应的所有记录信息
    int h,m,s;
    while(N--){//读取数据
        string in,plate;
        cin>>plate;
        scanf("%d:%d:%d",&h,&m,&s);
        cin>>in;
        allCar[string(plate)].push_back(Record(plate,h*3600+m*60+s,in=="in"));
    }
    vector<Record>validCar;//存储合法的记录信息
    set<string>maxPlate;//存储停放时间最长的车名，set可直接按字典序排序
    int maxTime=0;//存储停放的最长时间
    for(auto i=allCar.begin();i!=allCar.end();++i){//遍历allCar
        sort((i->second).begin(),(i->second).end(),cmp);//排序
        int t=0;//记录当前遍历到的车的停放时间
        for(int j=0;j<(i->second).size()-1;++j)//遍历记录信息
            if((i->second)[j].in&&!(i->second)[j+1].in){//如果当前记录与下一记录是匹配的
                validCar.push_back((i->second)[j]);//加入合法记录
                validCar.push_back((i->second)[j+1]);//加入合法记录
                t+=(i->second)[j+1].time-(i->second)[j].time;//累加停放时间
            }
        if(t>maxTime){//比较当前车辆停放时间与停放的最长时间，更新相关信息
            maxPlate.clear();
            maxPlate.insert(i->first);
            maxTime=t;
        }else if(t==maxTime)
            maxPlate.insert(i->first);
    }
    sort(validCar.begin(),validCar.end(),cmp);//对合法记录排序
    int parkCar=0,index=0;//当前停车场内的车辆数，索引位置
    while(K--){
        scanf("%d:%d:%d",&h,&m,&s);
        int t=(h*60+m)*60+s;//查询时间
        for(;index<validCar.size()&&validCar[index].time<=t;++index)//遍历合法记录
            parkCar+=validCar[index].in?1:-1;//统计停车场内车辆数
        printf("%d\n",parkCar);//输出
    }
    for(auto i=maxPlate.cbegin();i!=maxPlate.cend();++i)
        cout<<*i<<" ";
    printf("%02d:%02d:%02d",maxTime/3600,maxTime%3600/60,maxTime%60);
    return 0;
}
```

# A1096 Consecutive Factors

```
基本思路：
n=60;
考虑i=60: 60序列可以
考虑i=59: 
......
考虑i=30: 30序列可以
......
考虑i=3: 3,2序列可以
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
bool isPrime(int x){
	if(x<=1)return false;
	int sqrtx=sqrt(x);
	for(int i=2;i<=sqrtx;i++){
		if(x%i==0)return false;
	}
	return true;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	if(isPrime(n)){
		printf("1\n");
		printf("%d",n);
		return 0;
	}
	int maxLen=0;
	vector<int> maxVec;
	int i=sqrt(n)+1; //error: int i=n容易超时; int i=sqrt(n)对于n=6的情况不对 
	while(i>1){
		int len=0;
		vector<int> vec;
		int i2=i,n2=n;
		while(i2>1){
			if(n2%i2==0){
				len++;
				vec.push_back(i2);
				n2=n2/i2;
				/*while(n2%i==0){
					len++;
					vec.push_back(i);
					n2=n2/i;
				}*/
				i2--;
			}else{
				break;
			}
		}
		if(len>maxLen){
			maxLen=len;
			maxVec=vec;
		}else if(len==maxLen){
			maxVec=vec;
		}
		i--;
	}
	printf("%d\n",maxVec.size());
	for(int i=maxVec.size()-1;i>=0;i--){
		if(i==maxVec.size()-1){
			printf("%d",maxVec[i]);
		}else{
			printf("*%d",maxVec[i]);
		}
	}
	return 0;
}
```

错误分析：

1. 设计错误：我以前的思路是：若bk,b(k-1),b(k-2),...,b1序列可以（bk>bk-1），则下一次从b1-1开始考虑，但是这个想法是错误的，下一次应该从bk-1考虑。
2. 超时错误：24行。

# A1097 Deduplication on a Linked List(难)

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

# A1099 Build A Binary Search Tree

```
递归创建树，根据BST的性质来填充data，使得这个树为BST
排序后的keys为:k1 k2 k3......k(n-1)
对于根root，假设k(i)为root的data，root左子树的结点个数肯定是k(i)前面的keys的个数，root右子树的
结点个数肯定是k(i)后面的keys的个数。在这样使用递归
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 105
int n;
struct Node{
	int id,data;
	Node* left,*right;
	int leftNum,rightNum;
	Node(){
		left=right=NULL;
	}
};
int left_index[MAXN];
int right_index[MAXN];
int keys[MAXN];

void createTree(Node*&root,int id){
	if(id==-1){
		root=NULL;
		return;
	}
	root=new Node();
	root->id=id;
	createTree(root->left,left_index[id]);
	createTree(root->right,right_index[id]);
}
void updateNum(Node* root){
	if(root->left==NULL)root->leftNum=0;
	else{
		updateNum(root->left);
		root->leftNum=root->left->leftNum+root->left->rightNum+1;
	}
	if(root->right==NULL)root->rightNum=0;
	else{
		updateNum(root->right);
		root->rightNum=root->right->leftNum+root->right->rightNum+1;
	}
}
void fillData(int a,int b,Node*root){
	if(root==NULL || a>b)return;
	int i=root->leftNum;
	root->data=keys[a+i];
	fillData(a,a+i-1,root->left);
	fillData(a+i+1,b,root->right);
}
void levelOrder(Node*root){
	queue<Node*> q;
	q.push(root);
	bool first=true;
	while(!q.empty()){
		root=q.front();
		q.pop();
		if(first){
			printf("%d",root->data);
			first=false;
		}else{
			printf(" %d",root->data);
		}
		if(root->left)q.push(root->left);
		if(root->right)q.push(root->right);
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d",&n);
	//输入
	for(int i=0;i<n;i++){
		scanf("%d %d",&left_index[i],&right_index[i]);
	} 
	for(int i=0;i<n;i++){
		scanf("%d",&keys[i]);
	}
	Node* root;
	//创建树
	createTree(root,0); 
	updateNum(root);
	//排序keys
	sort(keys,keys+n);
	fillData(0,n-1,root);
	//输出
	levelOrder(root);
	return 0;
}
```

