# 201712-2 游戏

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
/*#include<bits/stdc++.h>
using namespace std;*/

int n,k;
int exist[1005];

bool isOut(int x){
    if(x%k==0)
        return true;
    else if(x%10==k)
        return true;
    return false;
}
int main(int argc,char* argv[]){
    memset(exist,0,sizeof(exist));
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&k);
    int num=n,i=0,cnt=1;
    while(num!=1){
        if(exist[i+1]==0){
            if(isOut(cnt)){
                exist[i+1]=1;
                num--;
            }
            cnt++;
        }
        i=(i+1)%n;
    }
    while(exist[i+1]!=0){
        i=(i+1)%n;
    }
    printf("%d",i+1);
    return 0;
}
```

# 201712-3 Crontab

https://blog.csdn.net/richenyunqi/article/details/86720394

字符串处理。

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

unordered_map<string,int> monthsAndWeeks = {//英文缩写与数字的映射
    {"jan",1},{"feb",2},{"mar",3},{"apr",4},{"may",5},{"jun",6},
    {"jul",7},{"aug",8},{"sep",9},{"oct",10},{"nov",11},{"dec",12},
    {"sun",0},{"mon",1},{"tue",2},{"wed",3},{"thu",4},{"fri",5},{"sat",6}
};
int n,monthdays[13]={0,31,28,31,30,31,30,31,31,30,31,30,31};//平年时每个月有多少天
long long s,t; //起始时间，结束时间
const int year=(int)(1e8),month=(int)(1e6),day=10000,hour=100; //方便用于分割时间
string crontab[5]; //存储每个配置信息的输入，分，时，天，月，星期
set<int> commandTime[5];//用来存储配置信息的具体时间范围
vector<string> command; //存储配置信息的命令
map<long long,vector<int>> ans; //存储每个时间点对应的要执行的各种命令

int daysOfMonth(int y, int m){
    if((y%400==0 || y%4==0&&y%100!=0) && m==2)
        return 29; //闰年2月有29天
    return monthdays[m];
}
//根据输入的字符串，判断12, Fri, Wed这三种情况
int determineMonthOrWeek(string s){
    if(isdigit(s[0])){ //s是数字字符串
        int a;
        sscanf(s.c_str(),"%d",&a);
        return a;
    }
    for(char& c : s){
        c = tolower(c);
    }
    return monthsAndWeeks[s];
}
int determineWeek(int y, int m, int d, int week=4){
    for(int i=1970; i<y; i++){
        int temp=(i%400==0||i%4==0&&i%100!=0)?366:365;//平年有365天，闰年有366天
        week = (week+temp)%7;
    }
    for(int i=1; i<m; i++){
        week = (week+daysOfMonth(y,i))%7;
    }
    return (week+d-1)%7;
}
void splitCommand(){ //分割命令，crontab到commandTime的转换
    //init
    for(int i=0; i<5; i++){
        commandTime[i].clear();
    }
    //遍历配置信息的每一个时间项
    for(int i=0; i<5; i++){
        if(crontab[i] == "*"){ //配置信息是*
            int left,right;
            if(i == 0){
                left = 0;
                right = 59;
            }else if(i == 1){
                left = 1; right = 23;
            }else if(i == 2){
                left = 1; right = 31;
            }else if(i == 3){
                left = 1; right = 12;
            }else if(i == 4){
                left = 0; right = 6;
            }
            for(int j=left; j<=right; j++){
                commandTime[i].insert(j);
            }
        }else{ //配置信息不是*
            for(int j=0,k1=0;true;j=k1+1){
                k1 = crontab[i].find(',',j);//从j开始找,的位置
                string temp = crontab[i].substr(j,k1==string::npos ? k1 : k1-j);//1-3,2-5中的1-3
                int k2 = temp.find('-');//k2存储temp中'-'的位置
                int p1=determineMonthOrWeek(temp.substr(0,k2)), p2; //p1,p2分别为区间的左右界
                if(k2 == string::npos){
                    p2 = p1;
                }else{
                    p2 = determineMonthOrWeek(temp.substr(k2+1));
                }
                for(int k=p1; k<=p2; k++){
                    commandTime[i].insert(k);
                }
                if(k1 == string::npos){
                    break;
                }
            }
        }
    }
}

void computeCrontab(int c){//计算符合该crontab的时间，以及建立时间点到命令的映射ans
    for(int y=s/year; y<=t/year; y++){ //遍历所有年份
        for(int m : commandTime[3]){ //遍历crontab中符合要求的月份
            for(int d : commandTime[2]){ //遍历crontab中的所有天
                if(d > daysOfMonth(y,m))
                    continue;
                if(find(commandTime[4].begin(),commandTime[4].end(),
                        determineWeek(y,m,d)) != commandTime[4].end()){
                    //说明这一天的星期数是符合crontab要求的
                    for(int h : commandTime[1]){
                        for(int minute : commandTime[0]){
                            //格式化时间
                            long long temp = (long long)y*year + m*month + d*day + h*hour + minute;
                            if(temp>=s && temp<t){
                                ans[temp].push_back(c);
                            }
                        }
                    }
                }
            }

        }
    }

}
int main(){
    //test
    /*crontab[0] = "30";
    crontab[1] = "23";
    crontab[2] = "*";
    crontab[3] = "*";
    crontab[4] = "Sat,Sun";
    splitCommand();
    for(int i=0; i<5; i++){
        for(auto j : commandTime[i]){
            cout << j << " ";
        }
        cout << endl;
    }*/
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>s>>t;
    command.resize(n);
    for(int i=0; i<n; i++){
        for(int j=0; j<5; j++)
            cin>>crontab[j];
        cin>>command[i];
        splitCommand();
        computeCrontab(i);
    }
    for(auto& i : ans){
        for(auto j : i.second){
            printf("%lld %s\n",i.first,command[j].c_str());
        }
    }
    return 0;
}
```

# 201712-4 行车路线

c++实现

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<climits>
/*#include<bits/stdc++.h>
using namespace std;*/
typedef long long lld;

struct Edge{
    int type; //0表示大道
    int u,v,w,next;
    Edge(int _t,int _u,int _v,int _w,int _n){type=_t;u=_u;v=_v;w=_w;next=_n;}
    Edge(){};
};
const int MAXM = 100005;
const int MAXN = 505;
const lld INF = LLONG_MAX-100;
int n,m; //n是1-n, m是1-m
Edge edges[2*MAXM];
int head[MAXN];

void init(){
    memset(head,0,sizeof(head));
}

lld dist[MAXN]; //error:可能超过int
int vis[MAXN];
int pre[MAXN]; //前面的边
void initDijkstra(){
    int i;
    for(i=0;i<MAXN;i++){
        dist[i]=INF;
        vis[i]=0;
        pre[i]=0;
    }
}
lld getNewDist(int k){ //i是端点号，k是边的号
    if(edges[k].type==0){
        return dist[edges[k].u]+edges[k].w;
    }
    else{
        int a=k,b;
        lld c=0;
        while(a!=0 && edges[a].type!=0){
            c+=edges[a].w;
            b=a;
            a=pre[edges[a].u];
        }
        return c*c+dist[edges[b].u];
    }
}
void Dijkstra(){ //从1出发
    dist[1]=0;
    int i;
    for(i=0;i<n;i++){ //error: 以前只运行了n-1次
        int u=-1,j;
        lld minD=INF;
        for(j=1;j<=n;j++){ //error: j<n
            if(vis[j]==0 && dist[j]<minD){
                minD=dist[j]; u=j;
            }
        }
        if(u==-1)return;
        if(u==n)return; //找到答案了，不需要运行完dijkstra
        vis[u]=1;
        for(j=head[u];j!=0;j=edges[j].next){
            lld temp;
            if(vis[edges[j].v]==0 && (temp=getNewDist(j))<dist[edges[j].v]){
                dist[edges[j].v]=temp;
                pre[edges[j].v]=j;
            }
        }
    }
}
int main(int argc,char* argv[]){
    init();
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    int i,t,a,b,w,cnt=1;
    while(m--){
        scanf("%d %d %d %d",&t,&a,&b,&w);
        edges[cnt]=Edge(t,a,b,w,head[a]);
        head[a]=cnt++;
        edges[cnt]=Edge(t,b,a,w,head[b]); //error: 没有考虑输入的是双向道路
        head[b]=cnt++;
    }
    initDijkstra();
    Dijkstra();
    printf("%lld",dist[n]);
    return 0;
}
```

错误分析：

1. 语法错误：可能超过int，话说题目中明明说了不超过10^6的啊。

2. 细节错误：56行，59行，Dijkstra的某些细节错误了。

    85行，忘了输入是双向道路。

# 201709-2 公共钥匙盒

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXN 1005
#define MAXK 1005

typedef struct Action{
	int id,type,time;//type=0表示还，1表示取 
}Action;
int n,k;
Action actions[2*MAXK];//1-2*k
int pos[MAXN];//1-n

void init(){
	int i;
	for(i=0;i<MAXN;i++)pos[i]=i;
}

int Comp(Action a1,Action a2){
	if(a1.time!=a2.time){
		return a1.time<a2.time?1:0;
	}else if(a1.type!=a2.type){
		return a1.type<a2.type?1:0;
	}else{
		return a1.id<a2.id?1:0;
	}
}
void downAdjust(Action as[],int k1,int k2){
	int i=k1;
	int j=2*k1;
	while(j<=k2){
		if(j+1<=k2){
			if(Comp(as[j],as[j+1])==1){
				j=j+1;
			}
		}
		if(Comp(as[i],as[j])==1){
			Action temp=as[i];
			as[i]=as[j];
			as[j]=temp;
		}
		i=j; j=2*i;
	}
}
void createHeap(Action as[],int k1,int k2){
	int i=k2/2;
	while(i>=k1){
		downAdjust(as,i,k2);
		i--;
	}
}
void sort(Action as[],int k1,int k2){//[k1,k2]
	createHeap(as,k1,k2);
	int i=k2;
	while(i>k1){
		Action temp=as[k1];
		as[k1]=as[i];
		as[i]=temp;
		downAdjust(as,k1,i-1);
		i--;
	}
}
int findLeft(int k1){ //找到pos中为k1的下标 
	int i;
	for(i=1;i<=n;i++){
		if(pos[i]==k1){
			return i;//error:return k1
		}
	}
	return -1;
}
int main(int argc,char* argv[]){
	init();
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d",&n,&k);
	int i,w,s,c,cnt=1;//钥匙id,拿走时间，用的时间 
	Action a1,a2;
	while(k--){
		scanf("%d %d %d",&w,&s,&c);
		a1.id=w; a1.time=s; a1.type=1;
		a2.id=w; a2.time=s+c; a2.type=0;
		actions[cnt++]=a1;
		actions[cnt++]=a2;
	}
	//sort
	sort(actions,1,cnt-1);
	for(i=1;i<cnt;i++){
		if(actions[i].type==0){ //还 
			int j=findLeft(0);
			pos[j]=actions[i].id;
		}else{ //取 
			int j=findLeft(actions[i].id);
			pos[j]=0;
		}
	}
	//输出
	 for(i=1;i<=n;i++){
	 	if(i==1){
	 		printf("%d",pos[i]);
		 }else{
		 	printf(" %d",pos[i]);
		 }
	 }
	 return 0;
}
```

# *201709-3 JSON查询(难)

```cpp
/*
基本思想：由于键值成对存在，且键值都有双引号引用，则遇到开始两个引号，则是值，再遇到两个引号是值，
或者再遇到{,是复合值。
1):寻找两个引号，赋给key
2):若遇见{，则递归；若遇见双引号，则是值。返回1)
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

struct Node{
    string value;
    map<string,Node*>* mp;
    Node(){mp = new map<string,Node*>;}
};
map<string,Node*>* mp=new map<string,Node*>; //存储键值对
int n,m;

void dealKeyOrValue(string& str, int& k1, string& res){ //k1指向"\"abcd\""的a，最后的结果res="abcd",k1指向第二个"的后面
    //error
    /*int k2;
    while((k2=str.find("\\",k1)) != string::npos){ //判断是否有特殊字符
        res += str.substr(k1,k2-k1);
        res += str.substr(k2+1,1);
        k1 = k2+2;
    }
    k2 = str.find("\"",k1);
    res += str.substr(k1,k2-k1);//error
    k1 = k2 + 1;*/
    int k2 = k1;
    while(str[k2] != '\"'){
        if(str[k2] == '\\'){
            res += str.substr(k1,k2-k1);
            res += str.substr(k2+1,1);
            k2 += 2;
            k1 = k2;
        }else{
            k2++;
        }
    }
    res += str.substr(k1,k2-k1);
    k1 = k2+1;
}
void dealMap(string& str, int& k1, map<string,Node*>* mp2){ //实现两个对应{}间的信息插入mp2，递归实现
    string key = "";
    Node* vnode = new Node();
    while(true){
        if(k1 == str.size()){ //如果这个字符串遍历完毕，则重新输入一个
            getline(cin,str); n--;
            k1 = 0;
        }
        int k2 ; //k2是下一个"的位置
        if((k2=str.find("\"",k1)) != string::npos){ //str后面有key
            k1 = k2 + 1; //k1指向"\"abcd\""的第一个"的后面的位置
            if(key == ""){
                dealKeyOrValue(str,k1,key);
                (*mp2)[key] = vnode; //找到了键，则建立键值对
            }else{
                dealKeyOrValue(str,k1,vnode->value);
            }
        }else if((k2=str.find("{",k1)) != string::npos){ //如果遇到了{，则意味着key对应的value时复合的，则开始递归
            k1 = k2+1;
            dealMap(str,k1,vnode->mp);
        }else if((k2=str.find("}",k1)) != string::npos){ //如果遇到了},则递归返回
            k1 = k2+1;
            return;
        }else{ //如果这一个字符串遍历完毕，则k1指向末尾
            k1 = str.size();
        }
        if(key!="" && !(vnode->value=="" && vnode->mp->size()==0)){//如果一个键值对已经形成，则赋给mp2,然后继续找下一个键值对
            key = "";
            vnode = new Node();
        }
    }
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin >> n >> m;
    getchar();//error
    string line1;
    getline(cin,line1);
    int k3 = line1.find("{",0)+1,k4=0;
    dealMap(line1,k3,mp);

    for(int i=0; i<m; i++){
        string line;
        getline(cin,line);
        int k1 = 0;
        int k2 = 0;//error:忘了更新k2
        Node* node;
        map<string,Node*>* mp2 = mp; //error:mp2指向mp对象的空间，当mp2改变时,mp对象也变化
        bool flag = true;
        while(k2 != string::npos){
            k2 = line.find('.',k1);
            string temp = k2!=string::npos ? line.substr(k1,k2-k1) : line.substr(k1);
            map<string,Node*>::iterator it = mp2->find(temp);
            if(it != mp2->end()){
                node = it->second;
                k1 = k2+1;
                mp2 = node->mp;
            }else{
                flag = false;
                break;
            }
        }
        if(flag == false){
            printf("NOTEXIST\n");
        }else if(node->mp->empty()){
            printf("STRING %s\n",node->value.c_str());
        }else{
            printf("OBJECT\n");
        }
    }

}
```

错误代码：70分

错误分析：

1. 细节错误：80行，忘了去掉行末的换行。

2. 数据更新错误：90行

3. 逻辑错误：20行。如何处理``"abc\"abc\\abc"``中的转义字符，我以前的想法是，先找``\``字符，依次把``abc`` ``"`` ``abc`` ``\`` ``abc`` 加进去，但是没想到，这种情况``"abc": "abc\"abc\\abc"``这种情况，按照我以前的想法，则把

    ``abc": "abc\"abc\\abc``加进去了，错误了，因为你要考虑的那个字符串里可能没有``\``，而你找到的第一个``\``可能超出你期望考虑的字符串``abc``。

4. 语法错误: 92行。这解释了为什么我的循环第二次后出现了问题，因为我在第一个循环的时候改变了mp的值，由于对引用的错误理解。

5. 超时：由于对每个键值对都要存储，导致大量的字符串赋值操作，超时，这种方案在有大量查询时较好，但在只有少量查询时，显然创造map的过程的耗时会更显著。

6. 设计错误：我应该在读入数据时把不必要的空格去掉

```cpp
/*
基本思路2：吸收上一次的教训，在输入字符串input后不要创建map，而是对于特定的查询操作，直接在input中
进行遍历查询，这样不会超时，而且实现上较为简单。*/
```

```
基本四路2的不好设计也会有Bug。考虑输入序列{"address":{"first":"xinyang","xinyang":"gushi"}}
如果我要查询"address.xinyang"，显然会在输入序列的第一个xinyang处停止，然后程序判定不存在address.xinyang
，因为xinyang后面没有':',但是程序在第二个xinyang处停止才对；考虑查询"address.\"first"，若在程序
中并没有判断是否在""内的话，就会错误判断存在这个映射，因为虽然输入序列中有"\"first",但是不在""内，
所以正确答案是，不存在"address.\"first"对应的值。

基本思路2的正确设计应该是：查找键时只在对应域内的对应区间内查找。比如上面的例子，要查找"address.xinyang",
找到address的地址后，若后面没有'{'，则肯定不存在这个映射，若存在'{',则在address地址后的第一个单独
'{'后，第一个'}'前，在':'前面的""内查找子串。
```

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXNUM 100 //每行最多字符数
#define MAXN 100

int n,m;
char str[MAXNUM*MAXN*100]="\0";
char input[MAXNUM]="\0";

void dealInput(char s[]){
	char s1[MAXNUM];
	int i=0,j=0;
	while(s[i]!='\n'&&s[i]!='\0'){
		if(s[i]!=' '){
			s1[j++]=s[i];
		}
		i++;
	}
	s1[j]='\0';
	strcpy(s,s1);
} 

//在pos后{}内查找值为s的键的位置 
void findStr(int pos,char s[],int*pos1){
	int i=pos,a=0; //a代表括号层数,保证在a==1的情况下进行比较，a=0时失败返回 
	while(str[i]!='\"' && str[i]!='\0'){//在遇到下一个'\"'之前看看有没有'{' 
		if(str[i]=='{'){
			break;
		}
		i++;//error
	}
	if(str[i]!='{'){ //没有找到 {
		*pos1=-1; 
		return;
	}
	//有{了，说明有希望找到想要的键
	i+=2; //str的指针 
	a=1;
	while(a>0){
		char s1[MAXNUM]; //""内的串 
		int j=0; //s1的指针 
		while(str[i]!='\"'){//str是要查找s的串 
			if(str[i]=='\\'){
				s1[j++]=str[++i];
			}else{
				s1[j++]=str[i];
			}
			i++;
		}
		i++;
		s1[j]='\0';
		//printf("%s\n",s1);///////
		if(strcmp(s1,s)==0){
			*pos1=i-strlen(s)-1;
			return;
		}
		//找到下一个对应的""的位置
		/*int b=0; //"的个数 
		while(a!=0){
			if(str[i]=='{'){
				a++;
			}else if(str[i]=='}'){
				a--;
			}else if(str[i]=='\\'){
				i++;
			}else if(str[i]=='\"'){
				if(a==1){ //error:只在a==1时才递增b, 
					b++;
				}
			}
			if(b==3 && a==1){ //找到了 ,想要的"" 
				break;
			}
			i++;
		}
		i++;*/
		//以上代码，错误，
		//访问一个键后，后面要么是'\"',要么是'{'，分两种情况，来越到下一个键的部分
		while(str[i]!='\"'&&str[i]!='{'){
			i++;
		}
		if(str[i]=='\"'){
			i++;
			while(str[i]!='\"'){
				if(str[i]=='\\'){
					i+=2;
				}else{
					i++;
				}
			}
		}else{
			i++;
			while(str[i]!='}'){
				if(str[i]=='\\'){
					i+=2;
				}else{
					i++;
				}
			}
		}
		if(str[i+1]!=','){
			break;
		}else{
			i=i+3;
		}
	}
	//没有找到
	*pos1=-1; 
}
void search(char query[],char type[],char value[]){
	if(query[0]=='\0'){
		strcpy(type,"NOTEXIST");
		return;
	}
	//printf("%s\n",query);//////
	int dotPos[MAXNUM]; //记录address.city为0,6,8,11 
	memset(dotPos,-1,sizeof(dotPos));
	int i=0,d1=0;//d1是dotPos中数据的个数，为偶数 
	dotPos[d1++]=0;
	while(query[i]!='\n'&&query[i]!='\0'){
		if(query[i]=='.'){
			dotPos[d1++]=i-1;
			dotPos[d1++]=i+1;
		}
		i++;//error:
	}
	dotPos[d1++]=i-1;
	//printf("dotPos0=%d dotPos1=%d\n",dotPos[0],dotPos[1]);///////
	int pos=0,pos1,pos2,j,k;//pos1是目标子串在query中的位置 
	char q[MAXNUM]="\0";
	for(i=0;i<d1;i=i+2){ //error: i/2表示第0，1个数据，如address,city 
		k=0;
		for(j=dotPos[i];j<=dotPos[i+1];j++){//把address复制到q中 
			q[k++]=query[j];
		}
		q[k]='\0';
		//printf("q=%s\n",q);///////
		findStr(pos,q,&pos1);
		//printf("pos1=%d\n",pos1);///////
		if(pos1==-1){//未能找到合法的address 
			strcpy(type,"NOTEXIST");
			return;
		}
		//pos=pos1+1;//error
		pos=pos1+strlen(q)+1;
	}
	//若可以找到键 
	pos2=pos1+strlen(q)+2; //键后面的位置 
	//printf("value begin=%c\n",str[pos2]);//////
	if(str[pos2]=='{'){ //是object error: if(query[pos2]=='{')
		strcpy(type,"OBJECT");
	}else if(str[pos2]=='\"'){ //是string 
		strcpy(type,"STRING");
		j=pos2+1;
		k=0;
		while(str[j]!='\"'){ //复制值的内容
			if(str[j]!='\\'){
				value[k++]=str[j];
			}else{
				value[k++]=str[++j];
			}
			j++;
		}
	}
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d\n",&n,&m);
	while(n--){
		fgets(input,MAXNUM,stdin);
		dealInput(input);
		strcat(str,input);
	}
	//printf("%s\n",str);//////
	while(m--){
		input[0]='\0';//只能初始化，不能赋值字符串 
		fgets(input,MAXNUM,stdin);
		char type[MAXNUM]="\0",value[MAXNUM]="\0";
		search(input,type,value);
		printf("%s",type);
		if(strcmp(type,"STRING")==0){
			printf(" %s",value);
		}
		printf("\n");
	}
	return 0;
} 
```

错误代码，80分，运行错误

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXNUM 10000

string str;
int n,m;

void dealLine(string&line){
    char line1[MAXNUM];
    int i=0,j=0;
    while(line[i]!='\0'){
        if(line[i]!=' '){
            line1[j++]=line[i];
        }
        i++;
    }
    line1[j]='\0';
    line=string(line1);
}

void getStr(int pos,string&res){//得到键的部分
    char s[MAXNUM];
    int j=0;
    while(str[pos]!='\"'){
        if(str[pos]!='\\'){
            s[j++]=str[pos];
        }else{
            s[j++]=str[++pos];
        }
        pos++;
    }
    s[j]='\0';
    res=string(s);
}
void skipToValueEnd(int& pos){//跳到值末尾的'\"'或者'}'处
    //1.跳到键的末尾
    int i=pos;
    while(str[i]!='\"'){
        if(str[i]!='\\'){
            i++;
        }else{
            i+=2;
        }
    }
    i+=2;
    char c=str[i];//c为'\"'或者'{'
    if(c=='{')c='}'; //error: 如果c='{'则永远不会有str[i]==c的时候了，因为str[i]只能等于'}'
    i++;
    int a=0;//表示有多少个单独的'{'
    while(!(a==0&&str[i]==c)){//error:while(a==0 && str[i]!=c)
        if(str[i]!='\\'){
            if(str[i]=='{'){
                a++;
            }else if(str[i]=='}'){
                a--;
            }
            i++;
        }else{
            i+=2;
        }
    }
    pos=i;//error
}
void findStr(int pos,string&s,int&i1){
    if(str[pos]!='{'){
        i1=-1;
        return;
    }
    pos+=2; //pos调节到键的第一个字符处
    while(true){
        string s1;
        getStr(pos,s1);
        if(s1==s){
            i1=pos;
            return;
        }else{
            skipToValueEnd(pos);//跳到值末尾的'\"'或者'}'处
            if(str[pos+1]!=','){
                i1=-1;
                return;
            }else{
                pos+=3;//pos调节到键的第一个字符处
            }
        }
    }
}
void splitString(string&s,vector<string>&parts){
    int i=0,k=0,cnt=0;
    while(k!=string::npos){
        k=s.find('.',i);
        parts.push_back(s.substr(i,k-i));//error: s.substr(i,k)
        i=k+1;
    }
}
void Search(string&query,string&type,string&value){
    if(query==""){
        type="NOTEXIST";
        return;
    }
    //1.将query按照'.'拆分为几个部分，保存在parts中
    vector<string> parts;
    splitString(query,parts);
    //2:
    int i1,i2;
    for(int i=0;i<parts.size();i++){
        if(i==0){
            findStr(0,parts[0],i1);
        }else{
            findStr(i2+3,parts[i],i1);
            //cout<<"parts["<<i<<"]"<<" done. i1="<<i1<<" str[i1]="<<str[i1]<<endl;//////
        }
        if(i1==-1){
            type="NOTEXIST";
            return;
        }else{
            //i2=i1+parts[i].size()-1;//error:
            //上述代码是错的，因为："esc\\aped"通过getStr()函数
            //得到的是"esc\aped"
            i2=i1;
            while(str[i2]!='\"'){
                if(str[i2]=='\\'){
                    i2+=2;
                }else{
                    i2++;
                }
            }
            i2--;
        }
    }
    //3:根据值是string还是object做相应处理
    int i=i2+3;
    if(str[i]=='{'){
        type="OBJECT";
    }else{
        type="STRING";
        i=i+1;
        getStr(i,value);
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>m;
    getchar();
    for(int i=0;i<n;i++){
        string line;
        getline(cin,line);
        dealLine(line);
        str+=line;
    }
    string query,type,value;
    while(m--){
        getline(cin,query);
        Search(query,type,value);
        cout<<type;
        if(type=="STRING"){
            cout<<" "<<value;
        }
        cout<<endl;
    }
}
```

这是我才用新思路的实践，我把重要的设计细节用过程表示出来，用例子和图片具体化过程，这样的话思路更加清晰。

错误分析：

1. 逻辑错误：48行；117行：想要更新i2，但是错误更新了；92行：分割字符串出错

# 201709-4 通信网络

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXN 1005
#define MAXM 10005

typedef struct Edge{
    int u,v,next;
}Edge;
int know[MAXN][MAXN]; //i知道谁
int knowNum[MAXN]; //i知道多少个节点
int n,m;
Edge edges[MAXM];
int head[MAXN];
int ans=0;

void init(){
    memset(head,0,sizeof(head));
    memset(knowNum,0,sizeof(knowNum));
    memset(know,0,sizeof(know));
}

int vis[MAXN];
void initDfs(){
    memset(vis,0,sizeof(vis));
}
void dfs(int k1,int k2){ //从k1开始遍历,遍历到k2
    if(know[k1][k2]==0){
        know[k1][k2]=know[k2][k1]=1;
        if(k1!=k2){
            knowNum[k1]++;
            knowNum[k2]++;
        }else{
            knowNum[k1]++;
        }
    }
    vis[k2]=1;
    int i;
    for(i=head[k2];i!=0;i=edges[i].next){
        if(vis[edges[i].v]==0)
            dfs(k1,edges[i].v);
    }
}
int main(int argc,char* argv[]){
    init();
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    int cnt=1,a,b;
    while(m--){
        scanf("%d %d",&a,&b);
        edges[cnt].u=a; edges[cnt].v=b; edges[cnt].next=head[a];
        head[a]=cnt++;
    }
    int i;
    for(i=1;i<=n;i++){
        initDfs();
        dfs(i,i);
    }
    for(i=1;i<=n;i++)
        ans+=(knowNum[i]==n);
    printf("%d",ans);
    return 0;
}
```

# *201703-2 学生排队

双向队列法

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXN 1005
#define MAXM 1005

typedef struct Node{
	int id;//学号
	int next; //next大于n表示 不存在 
	int pre; //pre为-1表示不存在 ,为0表示头节点 
}Node;
int n,m; 
Node nodes[MAXN]; //队列,nodes[0]记录头节点 

void init(){
	int i;
	for(i=0;i<MAXN;i++){
		nodes[i].id=i;
		nodes[i].next=i+1;
		nodes[i].pre=i-1;
	}
} 

int pos(int x){
	int i=0;
	while(nodes[i].id!=x){
		i=nodes[i].next;
	} 
	return i;
}
void move(int p,int q){//把学号为p的移动q个位置 
	int i=pos(p);//p的位置
	int j=i;
	int k=j;
	if(q>0 && i<n){
		q=q+1;
		while(q-- && j<=n){ 
			k=j;
			j=nodes[j].next;
		}
		//更改链表结构 
		if(j<=n){
			nodes[nodes[i].pre].next=nodes[i].next;
			nodes[nodes[i].next].pre=nodes[i].pre;
			nodes[k].next=i;
			nodes[j].pre=i;
			nodes[i].pre=k;
			nodes[i].next=j;
		}else{
			nodes[nodes[i].pre].next=nodes[i].next;
			nodes[nodes[i].next].pre=nodes[i].pre;
			nodes[k].next=i;
			nodes[i].pre=k;
			nodes[i].next=n+1;
		}
	}else if(q<0 && i>1){
		q=-q;
		q=q+1;
		while(q-- && j>0){
			k=j;
			j=nodes[j].pre;
		}
		nodes[nodes[i].pre].next=nodes[i].next;
		if(nodes[i].next<=n){
			nodes[nodes[i].next].pre=nodes[i].pre;
		}
		nodes[j].next=i;
		nodes[k].pre=i;
		nodes[i].pre=j;
		nodes[i].next=k;
	}
}
int main(int argc,char* argv[]){
	init();
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d",&n,&m);
	int p,q,i;
	while(m--){
		scanf("%d %d",&p,&q);
		if(p>=1 && p<=n){
			move(p,q);
		}
	}
	//输出
	i=nodes[0].next;
	while(i<=n){
		if(i==nodes[0].next){
	 		printf("%d",nodes[i].id);
		}else{
			printf(" %d",nodes[i].id); 
		}
		i=nodes[i].next;
	}
	return 0;
}
```

错误代码：70分

# 201703-3 Markdown

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXNUM 300

void parseLine(char line[],int type){ //转换的同时去掉行尾的\n 
	char line1[MAXNUM];
	int i,j=0,cnt=0;
	for(i=0;line[i]!='\n'&&line[i]!='\0';){
		if(line[i]=='#'){
			while(line[i]=='#'){
				cnt++; i++;
			}
			line1[j++]='<';
			line1[j++]='h';
			line1[j++]=cnt+48;
			line1[j++]='>';
			while(line[i]==' '){
				i++;
			}
		}else if(line[i]=='*'){
			i++;
			line1[j++]='<';
			line1[j++]='l';
			line1[j++]='i';
			line1[j++]='>';
			while(line[i]==' '){
				i++;
			}
		}else if(line[i]=='_'){
			i++;
			line1[j++]='<';
			line1[j++]='e';
			line1[j++]='m';
			line1[j++]='>';
			for(;line[i]!='_';){
				if(line[i]=='['){ //如果有超链接嵌套 
					i++; 
					int j1=i,j2=i; //j1指向link,j2指向url 
					while(line[j2]!='('){
						j2++;
					}
					j2++;
					line1[j++]='<';
					line1[j++]='a';
					line1[j++]=' ';
					line1[j++]='h';line1[j++]='r';line1[j++]='e';line1[j++]='f';line1[j++]='=';line1[j++]='\"';
					while(line[j2]!=')'){
						line1[j++]=line[j2++];
					}
					i=j2+1; //更新i 
					line1[j++]='\"';
					line1[j++]='>';
					while(line[j1]!=']'){
						line1[j++]=line[j1++];
					}
					line1[j++]='<';line1[j++]='/';line1[j++]='a';line1[j++]='>';
				}else{
					line1[j++]=line[i++];
				}
			}
			i++;//error: _italic_, 不加这句，会一直停留在最后一个_ 
			line1[j++]='<';
			line1[j++]='/';
			line1[j++]='e';
			line1[j++]='m';
			line1[j++]='>';
		}else if(line[i]=='['){
			i++; 
			int j1=i,j2=i; //j1指向link,j2指向url 
			while(line[j2]!='('){
				j2++;
			}
			j2++;
			line1[j++]='<';
			line1[j++]='a';
			line1[j++]=' ';
			line1[j++]='h';line1[j++]='r';line1[j++]='e';line1[j++]='f';line1[j++]='=';line1[j++]='\"';
			while(line[j2]!=')'){
				if(line[j2]!='_'){
					line1[j++]=line[j2++];
					continue;
				}
				j2++;
				line1[j++]='<';
				line1[j++]='e';
				line1[j++]='m';
				line1[j++]='>';
				while(line[j2]!='_'){
					line1[j++]=line[j2++];
				}
				j2++;
				line1[j++]='<';
				line1[j++]='/';
				line1[j++]='e';
				line1[j++]='m';
				line1[j++]='>';
			}
			i=j2+1; //更新i 
			line1[j++]='\"';
			line1[j++]='>';
			while(line[j1]!=']'){
				if(line[j1]!='_'){
					line1[j++]=line[j1++];
					continue;
				}
				j1++;
				line1[j++]='<';
				line1[j++]='e';
				line1[j++]='m';
				line1[j++]='>';
				while(line[j1]!='_'){
					line1[j++]=line[j1++];
				}
				j1++;
				line1[j++]='<';
				line1[j++]='/';
				line1[j++]='e';
				line1[j++]='m';
				line1[j++]='>';
			}
			line1[j++]='<';line1[j++]='/';line1[j++]='a';line1[j++]='>';
		}else{
			line1[j++]=line[i++];//error
		}
	}
	if(type==0){
		line1[j++]='<';line1[j++]='/';line1[j++]='h';line1[j++]=cnt+48;line1[j++]='>';
	}else if(type==1){
		line1[j++]='<';line1[j++]='/';line1[j++]='l';line1[j++]='i';line1[j++]='>';
	}
	line1[j++]='\0';
	strcpy(line,line1);
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	char line[MAXNUM];
	int cnt=0;
	int type=-1; //0是标题，1是列表，2是段 
	while(fgets(line,MAXNUM,stdin),line[0]!='\0'){
		if(line[0]=='\n'){
			if(type==1){ //处理列表
				printf("</ul>\n");
			}else if(type==2){ //处理段 
				printf("</p>\n");
			}
			cnt=0; 
			type=-1;
			continue;
		}
		if(cnt==0){
			if(line[0]=='#'){
				type=0;
			}else if(line[0]=='*'){
				type=1;
			}else{
				type=2;
			}
		}
		parseLine(line,type);
		//输出 
		if(cnt==0 && type==1){ //处理列表 
			printf("<ul>\n");
		}
		if(type==0 || type==1){
			printf("%s\n",line);
		}else{ //处理段 
			if(cnt==0){
				printf("<p>%s",line);
			}else {
				printf("\n%s",line);
			}
		}
		cnt++;
		line[0]='\0'; //error:line赋值为空串 
	}
	if(type==1){ //处理列表
		printf("</ul>\n");
	}else if(type==2){ //处理段 
		printf("</p>\n");
	}
	return 0;
}
```

这段代码太冗余复杂了

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXNUM 300

//把 A [_link_](http)转换为...... 
//i是)后面的下标，res是转换后的结果,cnt保证递归层数不会超过2 
void convert(int cnt,char str[],int* i,char res[]){
	int j=0;
	if(str[*i]=='_'){
		(*i)++;
		strcat(res,"<em>"); j=strlen(res);
		while(str[*i]!='_'){
			//if(str[*i]=='_' || str[*i]=='['){
			if(str[*i]=='[' && cnt==0){
				char res1[MAXNUM]="\0";
				convert(cnt,str,i,res1);
				strcat(res,res1); j=strlen(res);
			}else{
				res[j++]=str[(*i)++]; res[j]='\0';
			}
		}
		(*i)++;
		strcat(res,"</em>");
	}else if(str[*i]=='['){
		(*i)++;
		strcat(res,"<a href=\""); j=strlen(res);
		int j1=*i,j2=*i; //j1是link的首下标, j2是url的首下标 
		while(str[j2]!='('){
			j2++;
		}
		j2++;
		while(str[j2]!=')'){
			//if(str[j2]!='[' && str[j2]!='_'){
			if(str[j2]!='_' || cnt!=0){
				res[j++]=str[j2++]; res[j]='\0';
				continue;
			}
			char res1[MAXNUM]="\0";
			convert(cnt+1,str,&j2,res1);
			strcat(res,res1); j=strlen(res);
			//(*i)=j2; //error
		}
		j2++;
		(*i)=j2;
		strcat(res,"\">"); j=strlen(res);
		while(str[j1]!=']'){
			if(str[j1]!='_' || cnt!=0){
				res[j++]=str[j1++]; res[j]='\0';
				continue;
			}
			char res1[MAXNUM]="\0";
			convert(cnt+1,str,&j1,res1);
			strcat(res,res1); j=strlen(res);
		}
		strcat(res,"</a>");
	}
}
void parseLine(char line[],int type){ //转换的同时去掉行尾的\n 
	char line1[MAXNUM]="\0";
	int i,j=0,cnt=0;
	for(i=0;line[i]!='\n'&&line[i]!='\0';){
		if(line[i]=='#'){
			while(line[i]=='#'){
				cnt++; i++;
			}
			strcat(line1,"<h"); j=strlen(line1);
			line1[j++]='0'+cnt; 
			line1[j++]='>'; line1[j]='\0';
			while(line[i]==' '){
				i++;
			}
		}else if(line[i]=='*'){
			i++;
			strcat(line1,"<li>"); j=strlen(line1);
			while(line[i]==' '){
				i++;
			}
		}else if(line[i]=='_' || line[i]=='['){
			char res[MAXNUM]="\0";//error
			convert(0,line,&i,res);
			strcat(line1,res); j=strlen(line1);
		}
		else{
			line1[j++]=line[i++];//error
			line1[j]='\0';
		}
	}
	if(type==0){
		line1[j++]='<';line1[j++]='/';line1[j++]='h';line1[j++]=cnt+48;line1[j++]='>';
	}else if(type==1){
		line1[j++]='<';line1[j++]='/';line1[j++]='l';line1[j++]='i';line1[j++]='>';
	}
	line1[j++]='\0';
	strcpy(line,line1);
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	char line[MAXNUM];
	int cnt=0;
	int type=-1; //0是标题，1是列表，2是段 
	while(fgets(line,MAXNUM,stdin),line[0]!='\0'){
		if(line[0]=='\n'){
			if(type==1){ //处理列表
				printf("</ul>\n");
			}else if(type==2){ //处理段 
				printf("</p>\n");
			}
			cnt=0; 
			type=-1;
			continue;
		}
		if(cnt==0){
			if(line[0]=='#'){
				type=0;
			}else if(line[0]=='*'){
				type=1;
			}else{
				type=2;
			}
		}
		parseLine(line,type);
		//输出 
		if(cnt==0 && type==1){ //处理列表 
			printf("<ul>\n");
		}
		if(type==0 || type==1){
			printf("%s\n",line);
		}else{ //处理段 
			if(cnt==0){
				printf("<p>%s",line);
			}else {
				printf("\n%s",line);
			}
		}
		cnt++;
		line[0]='\0'; //error:line赋值为空串 
	}
	if(type==1){ //处理列表
		printf("</ul>\n");
	}else if(type==2){ //处理段 
		printf("</p>\n");
	}
	return 0;
}
```

错误分析：

1. 细节错误：81行，不要忘了把结果数组先赋值空字符串，再调用函数，我为了找这个错误花了很多时间

# 201703-4 地铁修建

```cpp
/*Prim算法求最小生成树
*/
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#define MAXN 100005
#define MAXM 200005

typedef struct Edge{
	int u,v,c,next;
}Edge;
int n,m;
Edge edges[2*MAXM];//1开始 
int head[MAXN]; //1开始 

void init(){
	memset(head,0,sizeof(head));
}

int dist[MAXN];
int vis[MAXN];
void initDijkstra(){
	memset(dist,0x3f,sizeof(dist));
	memset(vis,0,sizeof(vis));
}
int ans=-1;
void Dijkstra(){
	dist[1]=0;
	int i,j;
	for(i=0;i<n;i++){
		int u=-1,min=1e8;
		for(j=1;j<=n;j++){
			if(vis[j]==0 && dist[j]<min){
				min=dist[j];//error:min=j
				u=j;
			}
		}
		vis[u]=1;
		if(dist[u]>ans){
			ans=dist[u];
		}
		if(u==-1 || u==n)return;
		for(j=head[u];j!=0;j=edges[j].next){
			if(vis[edges[j].v]==0 && edges[j].c<dist[edges[j].v]){
				dist[edges[j].v]=edges[j].c;
			}
		}
	}
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d",&n,&m);
	int cnt=1,u,v,c;
	while(m--){
		scanf("%d %d %d",&u,&v,&c);
		edges[cnt].u=u;edges[cnt].v=v;edges[cnt].c=c;edges[cnt].next=head[u];
		head[u]=cnt++;
		edges[cnt].u=v;edges[cnt].v=u;edges[cnt].c=c;edges[cnt].next=head[v];
		head[v]=cnt++;
	}
	initDijkstra();
	Dijkstra();
	printf("%d",ans);
	return 0;
}
```

超时了，80分。原因是，Prim算法的时间复杂度为O(nm)，但是用Kruskal+并查集算法，时间复度为O(mlogm)，减少了不少

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<algorithm>
#define MAXN 100005
#define MAXM 200005
using namespace std;

typedef struct Edge{
	int u,v,c;
}Edge;
int n,m;
Edge edges[MAXM];//1开始
int father[MAXN];

void init(){
	int i;
	for(i=0;i<MAXN;i++)father[i]=i;
}
int findFather(int x){
	if(x==father[x]){
		return x;
	}
	int j=findFather(father[x]);
	father[x]=j;
	return j;
}
bool Comp(Edge e1,Edge e2){
	return e1.c<e2.c;
}

int ans=-1;
void Kruskal(){
	//排序edges
	sort(edges+1,edges+m+1,Comp);

	int i=1;
	while(findFather(1)!=findFather(n)){
		int u=edges[i].u, v=edges[i].v;
		int fu=findFather(u),fv=findFather(v);
		if(fu!=fv){
			father[fu]=fv;
			if(edges[i].c>ans){
                ans=edges[i].c;
			}
		}
		i++;
	}
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %d",&n,&m);
	int u,v,c,cnt=1;
	for(int i=0;i<m;i++){ //error: 尽量不要改m，因为你不知道什么时候会再用到
		scanf("%d %d %d",&u,&v,&c);
		edges[cnt].u=u;edges[cnt].v=v;edges[cnt].c=c;
		cnt++;
	}
	Kruskal();
	printf("%d",ans);
	return 0;
}
```

果然速度提高了很多。

# 201612-2 工资计算（难）

```cpp
/*T=f(S)。根据T来求S，而且f(S)是递增函数
*/
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

int T;
int b[7],baifen[7],a[7],t[7]; //组织数学公式所需的各种数据 

void init(){
	b[0]=0;b[1]=1500;b[2]=4500;b[3]=9000;b[4]=35000;b[5]=55000;b[6]=80000;
	baifen[0]=3;baifen[1]=10;baifen[2]=20;baifen[3]=25;baifen[4]=30;baifen[5]=35;baifen[6]=45;
	int i;
	for(i=0;i<=6;i++){
		if(i==0){
			a[i]=0;
			t[i]=3500;
		}else{
			a[i]=a[i-1]+(b[i]-b[i-1])/100*baifen[i-1];
			t[i]=3500+b[i]-(a[i-1]+(b[i]-b[i-1])*baifen[i-1]/100);
		}
	}
}

//根据税后工资，计算税前工资 
int main(int argc,char*argv[]){
	init();
	scanf("%d",&T);
	int i;
	if(T<t[0]){
		printf("%d",T);
	}else{
		if(T>t[6]){
			i=6;
		}else{
			i=0;
			while(T>t[i]){
				i++;
			}
			i--;
		}
		int s;
		s=(T+a[i]-(3500+b[i])*baifen[i]/100)*100/(100-baifen[i]);
		printf("%d",s);
	}
	return 0;
}
```

这是数学问题编程，以前没有遇到过，所以不太会做。不过要认真读题，都错题了，就浪费了太多时间。

# 201612-3 权限查询

```cpp
//设计
map<string,int> mAuth; //保存各种权限
struct Role{
    string role;
    map<string,int> mRole; //保存角色的各个权限，比vector<pair<string,int>>好，因为map查找起来更快
}
struct User{
    string user;
    map<string,int> mUser; //保存用户的各个角色
}
//使用stringstream分析一行字符串，如：Alice 3 game:3 dev:2 cc
//对每个用户应该查找前，应该判断用户名和权限是否合法（用户名是否在user集合中，权限是否在mAuth中找到）
```

```cpp
//freopen("D:\\input.txt","r",stdin);
/*基本思路；用map<string,int>存储<权限，等级>,等级为-1表示不存在等级
*/
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

map<string,int> authMap;//存储所有权限等级map
map<string,map<string,int>> roleMap; //<角色，权限>
map<string,set<string>> userMap;//<用户，角色>

void dealAuthInput(string&str,string&auth,int&level){
    int i;
    if((i=str.find(':'))!=string::npos){
        auth=str.substr(0,i);
        int num;
        sscanf(str.substr(i+1).c_str(),"%d",&num);
        level=num;
    }else{
        auth=str;
        level=-1;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    int p,r,u,q;//表示权限数量，角色数量，用户数量，询问数量
    int i;
    scanf("%d",&p);
    for(i=0;i<p;i++){
        string auth,input;
        int level;
        cin>>input;
        //cout<<input<<endl;//////
        dealAuthInput(input,auth,level);
        map<string,int>::iterator it;
        if((it=authMap.find(auth))!=authMap.end()){
            int a=it->second;
            if(level>a){
                authMap[auth]=level;
            }
        }else{
            authMap[auth]=level;
        }
    }
    //cout<<"done"<<endl;//////
    scanf("%d",&r);
    for(i=0;i<r;i++){ //error: i<p
        string role;
        int num;
        cin>>role>>num;
        //cout<<role<<" "<<num<<endl;//////
        while(num--){
            string auth,input;
            int level;
            cin>>input;
            dealAuthInput(input,auth,level);
            map<string,int>::iterator it;
            if((it=roleMap[role].find(auth))!=roleMap[role].end()){
                int a=it->second;
                if(level>a){
                    roleMap[role][auth]=level;
                }
            }else{
                roleMap[role][auth]=level;
            }
        }
    }
    //cout<<"done"<<endl;//////
    scanf("%d",&u);
    for(i=0;i<u;i++){
        string user,role;
        int num;
        cin>>user>>num;
        while(num--){
            cin>>role;
            //cout<<role<<endl;//////
            userMap[user].insert(role);
        }
    }
    //cout<<"done"<<endl;//////
    //开始查询
    scanf("%d",&q);
    while(q--){
        string user,input,auth;
        int level;
        cin>>user>>input;
        dealAuthInput(input,auth,level);
        map<string,int>::iterator it1;
        if((it1=authMap.find(auth))==authMap.end() || it1->second<level){
            cout<<"false"<<endl;
            continue;
        }
        if(userMap.find(user)==userMap.end()){
            cout<<"false"<<endl;
            continue;
        }
        int maxLevel=-2; //error: maxLevel=-1
        map<string,map<string,int>>::iterator it2;
        map<string,int>::iterator it3;
        for(string role : userMap[user]){
            it2=roleMap.find(role); //it2是角色的权限map
            if((it3=it2->second.find(auth))!=it2->second.end()){
                if(it3->second>=maxLevel){
                    maxLevel=it3->second;
                }
            }
        }
        if(maxLevel >= level){
            if(maxLevel==-1){
                cout<<"true"<<endl;
            }else if(level==-1){
                cout<<maxLevel<<endl;
            }else{
                cout<<"true"<<endl;
            }
        }else{
            cout<<"false"<<endl;
        }
    }
}
```

错误分析：

1. 细节错误：49行i<r写成了i<p。
2. 逻辑错误：99行：如果要找一个用户的某个权限的最大等级，不应该在找到这个权限时就输出等级，而应该在找到所有权限后比较出最大的那个。

# 201612-4 压缩编码

```cpp
/*
基本思路：动态规划, 状态转移方程为C(2*n1,2*n2)=min{C(2*n1,2k)+C(2*k+2,2*n2)+f(2*n1)+...f(2*k)+f(2*k+2)+...f(2*n2)}(k=n1...n2-1)
f(2*n1)+...f(2*k)+f(2*k+2)+...f(2*n2)可以存储为W(2*n1,2*n2).
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 1005;
int n;
int F[MAXN]; //F[1]表示2*1=2号点的频率,F[0]--F[n-1],2*k号点是辅助点且频率为0
int C[2*MAXN][2*MAXN]; //error:动态规划数组,C[i][j]表示C(i,j)的值，j-i=2*k+1(k=0,1,2...)
int W[2*MAXN][2*MAXN]; //error: 一开始没有开这个数组，但是这个数组可以减少计算量.

void init(){
    memset(C,0,sizeof(int)*MAXN*MAXN*4);
    memset(W,0,sizeof(int)*MAXN*MAXN*4);
    for(int i=0; i<n; i++){
        W[2*i][2*i] = F[i]; //初始化
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=0; i<n; i++){
        scanf("%d",&F[i]);
    }
    init();

    //动态规划
    for(int j=3; j<2*n; j=j+2){ //j=5表示，只考虑从2*k(k=0,1...)开始的5个点,例如：0,1,2,3,4
        for(int k=0; k<n; k++){
            int j1 = 2*k;
            int j2 = 2*k + j - 1; //[j1,j2]区间是要考虑的点
            if(j2 > 2*n-2)
                break;
            int minC = 1e8+5;
            for(int p=k; p<=j2/2-1; p++){ //计算C(j1,2*p)+C(2*p+2,j2)+W(j1,j2)
                int temp = 0;
                temp += C[j1][2*p]+C[2*p+2][j2];
                /*for(int m=k; m<=p; m++){
                    temp += F[m]; //error:2*m点的频率值是F[m]
                }
                for(int m=p+1; m<=j2/2; m++){
                    temp += F[m]; //2*m点的频率值是F[m]
                }*/
                W[j1][j2] = W[j1][2*p] + W[2*p+2][j2];
                temp += W[j1][j2];
                if(temp < minC){
                    minC = temp;
                }
            }
            C[j1][j2] = minC;
        }
    }

    printf("%d",C[0][2*n-2]);
}
```

//2020/3/25更---------------------------------------------------------------------------------------------------------------------------------

```
基本思路：
我们把元素a(i)的标号设为a*i-1, 对于标号为2*i(1<=i<n)的元素，为辅助元素，频率设为0。
当n=n时，考虑的元素标号区间为[1,n-1]，我们设[1,n-1]的所有元素用前缀码表示序列的长度为C(1,n-1)，序列
是原序列用前缀码替换后的序列。序列长度可以用二叉树的叶子结点的深度乘频率之和来算出，二叉树是所有非辅助
元素作为叶子结点的树。因此关键点，在于选哪个辅助点作为根节点，然后对于左右子树，选哪个根节点做根节点。
递推关系式为：C(i,j)=min{C(i,2k-1)+C(2k+1,j)+W(i,2k-1)+W(2k+1,j)}(i<=k<j)。
W(i,j)表示编号在[i,j]区间内的非辅助元素的频率之和。W(i,2k-1)+W(2k+1,j)=W(i,j)
我们依次算出，j-i+1为1，3，2*n-1的C(i,j)的值，存放于c[][]数组中，同时计算的W(i,j)存放于w[][]中。
最后的结果即为C(1,2n-1)
```

# 201609-2 火车购票

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>

int seats[21][6]; //20排，每排5个座位
int num[21];//每排有多少个连续座位
int tickets[105][6]; //每个用户订购的座位号 
int n;

void init(){
	memset(seats,0,sizeof(seats));
	memset(tickets,0,sizeof(tickets));
	int i;
	for(i=0;i<21;i++)num[i]=5;
} 

int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	init(); 
	scanf("%d",&n);
	int i,a,j,k,p; 
	for(k=1;k<=n;k++){
		p=1;
		scanf("%d",&a);
		for(i=1;i<=20;i++){
			if(num[i]>=a){//如果i排的空座位数大于等于a 
				j=1;
				while(seats[i][j]!=0)j++;
				int aa=a; 
				while(a--){
					seats[i][j]=k;
					tickets[k][p++]=5*i-5+j;//计算座位号 
					j++;//error
				}
				num[i]-=aa; //error: num[i]-=a; //a已经a--了，不是原来的a了 
				goto loop;
			}
		}
		for(i=1;i<=20&&a>0;i++){
			for(j=1;j<=5&&a>0;j++){
				if(seats[i][j]==0){
					seats[i][j]=k;
					tickets[k][p++]=5*i-5+j;
					a--;
				}
			}
		}
		loop:;
	}
	//输出
	for(k=1;k<=n;k++){
		i=1;
		while(tickets[k][i]!=0){
			if(i==1){
				printf("%d",tickets[k][i]);
			}else{
				printf(" %d",tickets[k][i]);
			}
			i++;
		}
		printf("\n");
	} 
	return 0;
}
```

错误分析：

1. 数据更新错误：31行和36行，有时候你用的某个数据项，已经被不期望的更改毁坏了

# 201609-3 炉石传说

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXPOS 7

/*基本思路：任意时刻，战场上的随从总是从1开始连续编号。
可知，随从数据就是以数组形式存放在vector中的，随从召唤和随从死亡就是
vector的插入和删除操作。
*/

array<vector<pair<int,int>>,2> grp;//pair是<生命，攻击力>
int n,player=0; //a=0表示先手玩家

void init(){
    //grp[0].resize(MAXPOS+1);
    //grp[1].resize(MAXPOS+1);
    grp[0].insert(grp[0].begin(),{30,0});
    grp[1].insert(grp[1].begin(),{30,0});
}
void dealLine(string&line){
    string temp;
    stringstream ss;
    ss.str(line);
    if(line[0]=='s'){
        int a,b,c;//位置，攻击力，生命
        ss>>temp>>a>>b>>c;
        grp[player].insert(grp[player].begin()+a,pair<int,int>(c,b));
    }else if(line[0]=='a'){
        int a,b;//攻击者，防卫者
        ss>>temp>>a>>b;
        grp[player][a].first-=grp[player^1][b].second;
        grp[player^1][b].first-=grp[player][a].second;
        if(grp[player][a].first<=0 && a!=0){ //error:if(grp[player][a].first<=0)
            grp[player].erase(grp[player].begin()+a);
        }
        if(grp[player^1][b].first<=0 && b!=0){
            grp[player^1].erase(grp[player^1].begin()+b);
        }
    }else{
        player=player^1;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    init();
    string line;
    cin>>n;
    getchar();
    while(n--){
        getline(cin,line);
        dealLine(line);
    }
    //输出
    if(grp[0][0].first<=0){
        cout<<-1<<endl;
    }else if(grp[1][0].first<=0){ //error: else if(grp[1][0].first==0)
        cout<<1<<endl;
    }else{
        cout<<0<<endl;
    }
    for(int j=0;j<2;j++){
        cout<<grp[j][0].first<<endl;
        cout<<grp[j].size()-1; //error: cout<<grp[j].size();
        for(int i=1;i<grp[j].size();i++){ //error: for(int i=1;i<=grp[j].size();i++)
            cout<<" "<<grp[j][i].first;
        }
        cout<<endl;
    }
}
```

错误分析：

1. 细节错误：56，63，64
2. 业务逻辑错误：33行，只有随从死了才被移除，要考虑这点

# 201609-4 交通规划

```cpp
/*Dijkstra算法的活用*/
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
#include<limits.h>
#define MAXN 10005
#define MAXM 100005
#define LLONG_MAX (((long long)INT_MAX+1)*2-1)*INT_MAX
typedef long long lld;

typedef struct Edge{
	lld u,v,c,next;
}Edge;
Edge edges[2*MAXM];
lld head[MAXN];
lld n,m;

void init(){
	memset(head,0,sizeof(head));
}

lld pre[MAXN]; //i的前驱边的编号 
lld vis[MAXN];
lld dist[MAXN];
lld sum=0;
void initDijkstra(){
	memset(pre,0,sizeof(pre)); 
	memset(vis,0,sizeof(vis)); 
	lld i;
	for(i=0;i<MAXN;i++)dist[i]=LLONG_MAX;
}
void Dijkstra(){
	dist[1]=0;
	lld i;
	for(i=1;i<=n;i++){
		lld u=-1,min=LLONG_MAX,i,j; //j是边的编号 
		for(i=1;i<=n;i++){
			if(vis[i]==0 && dist[i]<min){
				u=i; 
				min=dist[i];
			}
		}
		if(u==-1)return;
		if(u!=1){
			sum+=edges[pre[u]].c;
		}
		vis[u]=1;
		for(j=head[u];j!=0;j=edges[j].next){
			int v=edges[j].v;
			if(vis[v]==0 && dist[u]+edges[j].c<dist[v]){
				dist[v]=dist[u]+edges[j].c;
				pre[v]=j;
			}else if(vis[v]==0 && dist[u]+edges[j].c==dist[v]){
				if(pre[v]!=0 && edges[pre[v]].c>edges[j].c){
					pre[v]=j;
				}
			}
		} 
	}
}
int main(int argc,char* argv[]){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%lld %lld",&n,&m);
	lld i,a,b,c,cnt=1;
	for(i=1;i<=m;i++){ //error：i<=n 
		scanf("%lld %lld %lld",&a,&b,&c);
		edges[cnt].u=a;edges[cnt].v=b;edges[cnt].c=c;edges[cnt].next=head[a];
		head[a]=cnt++;
		edges[cnt].u=b;edges[cnt].v=a;edges[cnt].c=c;edges[cnt].next=head[b];
		head[b]=cnt++;
	}
	initDijkstra();
	Dijkstra();
	printf("%lld",sum);
	return 0;
}
```

# *201604-3 路径解析

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<string> path;
int p;

void dealCmd(string&line,vector<string>&path){ //i=0表示是第一此输入，且一定是路径
    stringstream ss(line);
    if(line=="" || line[0]=='/'){ //如果line是空串和以'/'开头
        path.clear();
    }
    string s="";
    while(getline(ss,s,'/')){
        if(s=="" || s==".")
            continue;
        if(s==".."){
            if(!path.empty())path.pop_back();
        }else{
            path.push_back(s);
        }
    }
}
void printPath(vector<string>&path){
    cout<<"/";
    for(int i=0;i<path.size();i++){
        if(i==0){
            cout<<path[i];
        }else{
            cout<<"/"<<path[i];
        }
    }
    cout<<endl;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>p;
    getchar();
    string line;
    getline(cin,line);
    dealCmd(line,path);
    for(int i=0;i<p;i++){
        vector<string> newPath=path;
        getline(cin,line);
        dealCmd(line,newPath);
        printPath(newPath);
    }
}
```

90分。

错误分析：

1. 一开始我错误理解题意了，我以为所有的路径命令是依据于上一个命令，但不是的，是依据于第一个输入的当前目录。

2. 