# 201912-1 报数

```cpp
//freopen("D:\\input.txt","r",stdin);
/*
基本思路：用长度为4的向量num[4]来记录每个人的跳过的数，用cnt
来记录总共已经报的数。在循环中，判断数的合法性（是否要跳过），然后
更新相应数据，cnt==n时退出
*/
#include<cstdio>
#include<string>
#include<algorithm> //有find函数
using namespace std;

int n;
int num[4]; //初始为全为0
int cnt = 0;

bool skip(int x){
    if(x%7 == 0){
        return true;
    }else{
        int a = x;
        while(a != 0){
            int b = a%10; //int b = x%10;
            if(b == 7){
                return true;
            }
            a = a/10;
		}
		/*char s[100];
		sprintf(s,"%d",x);
		string str = string(s);
		if(find(str.begin(),str.end(),'7') != str.end())
            return true;*/
    }
    return false;
}
int main(){
    //初始化
	num[0]=num[1]=num[2]=num[3]=0;

    scanf("%d",&n);
    int i=1;
    while(cnt < n){ //while(cnt == n)
        int player = (i-1)%4;
        if(skip(i)){
            num[player]++;
        }else{
            cnt++;
        }
        i++;
    }

    //输出
    for(i=0; i<4; i++){
        printf("%d\n",num[i]);
    }
}
```

错误分析：

1. 细节错误：22行

# 201912-2回收站选址

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<algorithm>
using namespace std;

/*
基本思路：结构体Node,存储x,y还有关于周围是否有垃圾的信息。用链表存储Node,每次插入一个
Node，就遍历所有已经插入的Node，更新Node中关于周围信息的数据项。例如，node插入时，
看nodes[i]是否在它上面，自己是否在nodes[i]右边之类的。
*/
struct Node{
    int x, y;
    bool left, up, right, down, leftUp, rightUp, rightDown, leftDown;
    Node* next;
    Node(){left=up=right=down=leftUp=rightUp=rightDown=leftDown=false; next=NULL;}
};
int n;
Node* head;
int num[5]; //0 1 2 3 4
/*
插入node到链表中。顺便更新所有的Node
*/
void Insert(Node* node){
    //Node* &cur = head->next;
    Node* cur = head->next;
    Node* pre = head;
    while(cur != NULL){
        if(node->x==cur->x-1 && node->y==cur->y){ //left
            cur->left = true;
            node->right = true;
        }else if(node->x==cur->x && node->y==cur->y+1){ //up
            cur->up = true;
            node->down = true;
        }else if(node->x==cur->x+1 && node->y==cur->y){
            cur->right = true;
            node->left = true;
        }else if(node->x==cur->x && node->y==cur->y-1){
            cur->down = true;
            node->up = true;
        }else if(node->x==cur->x-1 && node->y==cur->y+1){
            cur->leftUp = true;
            node->rightDown = true;
        }else if(node->x==cur->x+1 && node->y==cur->y+1){
            cur->rightUp = true;
            node->leftDown = true;
        }else if(node->x==cur->x+1 && node->y==cur->y-1){
            cur->rightDown = true;
            node->leftUp = true;
        }else if(node->x==cur->x-1 && node->y==cur->y-1){
            cur->leftDown = true;
            node->rightUp = true;
        }
        pre = cur;
        cur = cur->next;
    }
    //cur = node;
    pre->next = node;
}
int main(){
    //初始化
    head = (Node*)malloc(sizeof(Node));
    (*head) = Node(); //
    for(int i=0; i<5; i++)
        num[i] = 0;

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int x,y;
    Node* node;
    for(int i=0; i<n; i++){
        scanf("%d %d",&x,&y);
        node = (Node*)malloc(sizeof(Node));
        *node = Node();
        node->x = x;
        node->y = y;
        Insert(node);
    }

    Node* cur = head->next;
    while(cur != NULL){
        if(cur->left && cur->up && cur->right && cur->down){
            int score = 0;
            if(cur->leftUp)
                score++;
            if(cur->rightUp)
                score++;
            if(cur->rightDown)
                score++;
            if(cur->leftDown)
                score++;
            num[score]++;
        }
        cur = cur->next;
    }

    //输出
    for(int i=0; i<5; i++){
        printf("%d\n",num[i]);
    }
}
```

# 201912-3 化学方程式

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<ctype.h>
//using namespace std;

/*
基本思路：用字符串Hash的方法表示字符串。
用递归来解析字符串，例如要解析完Na(0H)2就要解析完(OH)2。
*/

const int MAXNUM = 27*27;
int n;

int Hash(char str[]){
    int res = 0;
    int i= 0;
    while(str[i] != '\0'){
        res = res*27 + (str[i]<='Z' ? str[i]-'A'+1 : str[i]-'a'+1); //'a'用1表示
        i++;
    }
    return res;
}

int* convert(char* str){
    int* Num = (int*)malloc(MAXNUM*sizeof(int));
    for(int i=0; i<MAXNUM; i++) Num[i]=0;

    int i = 0;
    int cnt2 = 0;
    while(str[i] != '\0'){ //计算3Ba3(PO4)2，cnt2就是中间的第一个3
        if(isdigit(str[i])){
            cnt2 = 0;
            while(isdigit(str[i])){
                cnt2 = cnt2*10 + str[i] - '0';
                i++;
            }
        }
        cnt2 = cnt2==0 ? 1 : cnt2;

        if(str[i] == '('){ //计算(PO4)2
            char str2[1005];
            i++; 
            int k = 1; //记录关于'(' ')'的信息
            int j = 0; //str2的指针
            while(k != 0){
                if(str[i] == '('){
                    k++;
                }else if(str[i] == ')'){
                    k--;
                }
                /*if(k != 0) //避免不要把最后一个')'加进来了，例如:(OH)2的')' 
                    str2[j] = str[i];
                i++; j++;*/ //error: 数据更新错误
                if(k != 0){ //避免不要把最后一个')'加进来了，例如:(OH)2的')'
                    str2[j] = str[i];
                    j++;
                }
                i++;
            }
            str2[j] = '\0';

            int* res = convert(str2);
            /*if(isdigit(str[i])){
                int cnt = str[i]-'0';
                for(int j=0; j<MAXNUM; j++){
                    Num[j] += res[j]*cnt*cnt2;
                }
                i++;
            }else{
                for(int j=0; j<MAXNUM; j++){
                    Num[j] += res[j]*cnt2;
                }
            }*/
            int cnt = 0; //cnt就是(PO4)3的3
            while(isdigit(str[i])){
                cnt = cnt*10 + str[i]-'0';
                i++;
            }
            cnt = cnt==0 ? 1 : cnt;
            for(int j=0; j<MAXNUM; j++){
                Num[j] += res[j]*cnt*cnt2;
            }
            free(res);

        }else if(str[i] == '+'){
           i++;
           cnt2 = 0; //error: 4Zn(NO3)2+NH4NO3。上一个cnt2是2，但NH4NO3的cnt2要重置
        }else{ //计算PO4
            char name[5]; //表示一个元素
            int j = 0; //name的指针
            name[j] = str[i];
            i++; j++;
            while(islower(str[i])){ //error。isdigit(str[i])
                name[j] = str[i];
                i++; j++;
            }
            name[j] = '\0';
            /*if(isdigit(str[i])){
                Num[Hash(name)] += (str[i]-'0')*cnt2;
                i++;
            }else{
                Num[Hash(name)] += cnt2;
            }*/
            int a = Hash(name);
            int cnt = 0; //cnt就是PO4的4
            while(isdigit(str[i])){
                cnt = cnt*10 + str[i]-'0';
                i++;
            }
            cnt = cnt==0 ? 1 : cnt;
            Num[a] += cnt*cnt2;
        }
    }

    return Num; //error
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int j=0; j<n; j++){
        char str1[1005];
        char str2[1005];
        scanf("%s",&str1);
        int i = 0, k = 0;
        while(str1[i] != '='){
            i++;
        }
        str1[i] = '\0';
        i++;
        while(str1[i] != '\0'){
            str2[k] = str1[i];
            i++; k++;
        }
        str2[k] = '\0';

        int* res1 = convert(str1);
        int* res2 = convert(str2);

        int flag = true;
        for(i=0; i<MAXNUM; i++){
            //test
            /*if(res1[i] > 0){
                printf("%c %c\n",i/27+65-1,i%27+65-1);///////
                printf("%d %d\n",res1[i],res2[i]);
            }*/
            if(res1[i] != res2[i]){
                flag = false;
                break;
            }
        }
        free(res1);
        free(res2);
        if(flag){
            printf("Y\n");
        }else{
            printf("N\n");
        }
    }
}
```

错误分析：

1. 逻辑设计错误：只考虑到3Ba2(NO4)3的Ba2(NO4)3中的数字，却没有考虑整个化合物还有个数3
2. 数据更新错误：54行。88行
3. 细节错误：94行

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int n;
int num1[27*27],num2[27*27];
string line,str1,str2; //方程式，等号左边，右边

int Hash(string s){
    //cout<<"we want the hash of ["<<s<<"]"<<endl;//////
    int a=0;
    for(int i=0;i<s.size();i++){
        if(islower(s[i])){
            a=a*27+s[i]-'a'+1;
        }else{
            a=a*27+s[i]-'A'+1;
        }
    }
    return a;
}

void init(){
    memset(num1,0,sizeof(num1));
    memset(num2,0,sizeof(num2));
    line=str1=str2="";
}

int getInt(string&s,int& j){
    int a1=0; //倍数
    while(j<s.size() && isdigit(s[j])){
        a1=a1*10+s[j]-'0';
        j++;
    }
    a1=(a1!=0?a1:1);
    return a1;
}
//s类似于Ba(OH)2, a是倍数, 结果累加到num中
void Analyze2(string s,int a,int num[]){
    //cout<<"s= "<<s<<endl;//////
    int i=0;
    while(i<s.size()){ //i总是指向大写字母或括号，或结尾
        if(s[i]=='('){
            i++;
            int j=i,c=0; //c代表括号层数
            while(j<s.size() && !(c==0 && s[j]==')')){
                if(s[j]=='('){ //error: {
                    c++;
                }else if(s[j]==')'){ //error: }
                    c--;
                }
                j++;
            }
            string temp=s.substr(i,j-i);
            j++;
            //printf("j=%d\n",j);//////
            int a1=getInt(s,j);
            //printf("a=%d a1=%d\n",a,a1);//////
            //Analyze2(s.substr(i,j-i),a*a1,num); //error: 此时s.substr(i,j-i)的值是Au(CN)2) 如果上一层是Na(Au(CN)2)的话
            Analyze2(temp,a*a1,num);
            i=j;
        }else{
            int j=i+1;
            while(j<s.size() && islower(s[j]))
                j++;
            int b=Hash(s.substr(i,j-i)); //例如：Ba的hash值
            /*if(b==713){//////
                cout<<s<<endl;
                cout<<"exist 713=hash\n"<<endl;
            }*/

            int a1=getInt(s,j); //倍数
            num[b]+=a1*a;
            i=j;
        }
    }
}
//分析等式左边或右边
void Analyze(string&s,int num[]){
    stringstream ss(s);
    vector<string> parts; //用'+'号分割各个化合物
    string part="";
    while(getline(ss,part,'+')){
        if(part != ""){
            parts.push_back(part);
            //cout<<"part "<<part<<endl;//////
        }
    }
    for(int i=0;i<parts.size();i++){ //分析parts[i]
        int a=0,j=0; //3Ba(0H)2中的3,j是指向B的
        a=getInt(parts[i],j);
        Analyze2(parts[i].substr(j),a,num);
    }
}
//判断结果（元素个数）是否相等
bool isSame(int a1[],int a2[]){
    bool flag=true;
    for(int i=0;i<27*27;i++)
        if(a1[i]!=a2[i]){
            //printf("not same a=%c b=%c hash=%d\n",i/27+'A'-1,i%27+'A'-1,i);//////
            //printf("not same num1[i]=%d num2[i]=%d\n",a1[i],a2[i]);//////
            flag=false;
        }
    return flag;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>n;
    while(n--){
        init();
        cin>>line;
        stringstream ss(line);
        getline(ss,str1,'=');
        //cout<<str1<<endl;//////
        getline(ss,str2,'=');
        //cout<<str2<<endl;//////
        Analyze(str1,num1);
        Analyze(str2,num2);
        //test//////
        //printf("Au=%d Na=%d C=%d N=%d O=%d H=%d\n",num1[Hash("Au")],num1[Hash("Na")],num1[Hash("C")],num1[Hash("N")],num1[Hash("O")],num1[Hash("H")]);
        //printf("Au=%d Na=%d C=%d N=%d O=%d H=%d\n",num2[Hash("Au")],num2[Hash("Na")],num2[Hash("C")],num2[Hash("N")],num2[Hash("O")],num2[Hash("H")]);
        if(isSame(num1,num2)){
            printf("Y\n");
        }else{
            printf("N\n");
        }
    }
    //test Analyse2()
    /*string s="2Ba(OH(OH)2)2+2Ba2";
    Analyze(s,num1);
    printf("Ba=%d O=%d H=%d\n",num1[Hash("Ba")],num1[Hash("O")],num1[Hash("H")]);*/
}
```

# 201912-4 区块链（难）

```cpp
//v1.0
//freopen("D:\\input.txt","r",stdin);
#include<cstdio>
#include<iostream>
#include<vector>
#include<array>
#include<map>
//#include<bits/stdc++.h>
using namespace std;

struct Node{
    vector<int> chunks; //块组成的链
    map<int,array<vector<int>,2> > actions;
    Node(){
        chunks.push_back(0);
    }
};
const int MAXN = 505;
Node nodes[MAXN];
vector<vector<int>> graph; //图; error:记得要resize
int n,m,delay,k; //节点的个数；边个个数；发送时间；事件个数

void init(){
    for(int i=0; i<MAXN; i++){
        nodes[i] = Node();
    }
}

/*
vec1是否可以更新vec2,若可以更换，则更新，并返回true
*/
bool canAccept(vector<int>& vec1, vector<int>& vec2){
    if(vec1.size() > vec2.size()){
        vec2 = vec1;
        return true;
    }else if(vec1.size() == vec2.size()){
        if(vec1.back() < vec2.back()){
            vec2 = vec1;
            return true;
        }
    }
    return false;
}

/*
把v号节点的链传播出去,预计t时刻到达
*/
void diffuse(int v, int t){
    for(int i=0; i<graph[v].size(); i++){
        int v2 = graph[v][i]; //邻接点
        if(nodes[v2].actions[t][0].empty()){
            nodes[v2].actions[t][0] = nodes[v].chunks;
        }else{
            canAccept(nodes[v].chunks, nodes[v2].actions[t][0]);
        }
    }
}

/*
插入到v这个节点的actions集合并且更新t时刻所有节点的chunks
*/
void Update(int v, int t, int chunkId){
    if(chunkId != -1){
        nodes[v].actions[t][1].push_back(chunkId);
    }
    for(int i=1; i<=n; i++){ //遍历每个节点
        for(auto &action : nodes[i].actions){ //遍历每个节点的各个时间点的操作集合，action是某个时间的操作集合
            int time = action.first; //操作到达的时间
            if(time > t)
                break;
            auto &action0 = action.second[0]; //传播到i的链
            bool canDiffuse = canAccept(action0, nodes[i].chunks);
            auto &action1 = action.second[1]; //i产生的块
            for(int j=0; j<action1.size(); j++){
                nodes[i].chunks.push_back(action1[j]);
            }
            if(canDiffuse || action1.size()!=0){
                diffuse(i,time+delay);
            }
        }
        //nodes[i].actions.erase(actions.begin(),actions.upper_bound(t));
        while(nodes[i].actions.begin()!=nodes[i].actions.end() && (*nodes[i].actions.begin()).first<=t){
            nodes[i].actions.erase(nodes[i].actions.begin());
        }
    }
    if(chunkId == -1){
        //输出
        printf("%d",nodes[v].chunks.size());
        for(int i=0; i<nodes[v].chunks.size(); i++){
            printf(" %d",nodes[v].chunks[i]);
        }
        printf("\n");
    }
}
int main(){
    init();

    freopen("D:\\input2.txt","r",stdin);
    scanf("%d %d",&n,&m);
    graph.resize(n+2); //error
    for(int i=0; i<m; i++){ //for(int i=0; i<n; i++)
        int v1, v2;
        cin >> v1 >> v2;
        graph[v1].push_back(v2);
        graph[v2].push_back(v1);
    }
    scanf("%d %d",&delay,&k);
    for(int i=0; i<k; i++){
        int v,t,chunkId = -1; //节点v在时刻t产生chunkId
        cin >> v >> t;
        if(cin.get() != '\n'){ //输入是3个
            cin >> chunkId;
        }
        Update(v,t,chunkId);
    }
}
```

这是个错误的程序。

错误分析：

1. 设计错误：

    ```
    有图：9--8--7--6--1--2。delay=6
    time = 29:
    	9 diffuse了(0 1 2 3 4)给9
    time = 59:
    	2未收到这个diffuse(9传到8，8传到7，7传到6，6到1，1到2，2理应受到)，因为时间直接从29跳到59
    	时，2的actions中并未包含时间为59的操作集合，最多有29+6=35的操作集合。
    ```

    所以，更好的做法是：让时间29缓慢升到59，具体怎么做呢？存储每个时刻的各个节点的操作集合，而不是每个节点的各个时刻的操作集合。然后，从最小的时刻开始更新所有的节点（看那个时刻接受的链是否可以用于更新，是否可以diffuse），若diffuse了，则<时刻，各个节点操作集合>动态更新，例如：正在访问29时刻，发现8节点在35时刻可以接受传播的链，则<时刻，各个节点操作集合>中多了35时刻的映射，则下一次必定访问35时刻并进行更新，而不是直接跳到59时刻而发生错误。

```cpp
#include <bits/stdc++.h>
using namespace std;
vector<vector<int>> graph(505);  //存储节点的边关系
vector<vector<int>> ans(505, {0});  //存储每个节点的主链
int n, m, t, k;
map<int, unordered_map<int, array<vector<int>, 2>>> actions;  //存储接收链和产生块操作
bool canAccept(const vector<int>& Old, const vector<int>& New) {  //其他节点传递过来的新链New能否被接受
    return Old.size() != New.size() ? Old.size() < New.size() : Old.back() > New.back();
}
void diffuse(int v, int time) {  //将节点v的主链广播出去
    for (int i : graph[v]) {
        auto& chain = actions[time][i][0];
        if ((chain.empty() and canAccept(ans[i], ans[v])) or (not chain.empty() and canAccept(chain, ans[v])))
            chain = ans[v];
    }
}
void query(int a, int b) {  //查询a节点在b时刻的主链
    for (auto& action : actions) {  //遍历所有接收链和产生块操作
        int curTime = action.first;
        if (curTime > b)  //当前操作时刻>b，结束循环
            break;
        for (auto& vertex : action.second) {
            int v = vertex.first;  //获取该操作节点编号
            auto &chain = vertex.second[0], &blocks = vertex.second[1];  //要接收的链、要产生的块
            bool canDiffuse = canAccept(ans[v], chain) or not blocks.empty();  //节点v是否要向外广播主链
            if (canAccept(ans[v], chain))  //先接收其他节点的主链
                ans[v] = chain;
            for (int b : blocks)  //再产生块
                ans[v].push_back(b);
            if (canDiffuse)  //向外广播
                diffuse(v, curTime + t);
        }
    }
    actions.erase(actions.begin(), actions.upper_bound(b));  //删除b时刻及其以前的所有操作，避免重复处理
    cout << ans[a].size();
    for (int i : ans[a])
        cout << " " << i;
    cout << "\n";
}
int main() {
    ios::sync_with_stdio(false);
    cin >> n >> m;
    for (int i = 0; i < m; ++i) {
        int a, b;
        cin >> a >> b;
        graph[a].push_back(b);
        graph[b].push_back(a);
    }
    cin >> t >> k;
    for (int i = 0; i < k; ++i) {
        int a, b, c;
        cin >> a >> b;
        if (cin.get() == '\n' or cin.eof()) {
            query(a, b);
        } else {
            cin >> c;
            actions[b][a][1].push_back(c);
        }
    }
    return 0;
}
```

我模仿别人的思想自己写的程序：

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int n,m,t,k;
//map<int,map<int,array<vector<int>,2>>> transactions; //error: 事务集. 第二个map必须用unordered_map，否则可能会由于排序操作而超时
map<int,unordered_map<int,array<vector<int>,2>>> transactions; //事务集
vector<vector<int>> Adj;
vector<vector<int>> links;

void init(){
    Adj.resize(n+1);
    links.resize(n+1);//error:
    for(int i=1;i<=n;i++)links[i].push_back(0);
}
//link1是否可以用来更新link2
bool isUpdate(vector<int>&link1,vector<int>&link2){
    if(link1.size()!=link2.size())return link1.size()>link2.size();
    else return link1.back()<link2.back();
}
//把id的链传播到邻居，预计time时刻到达
void diffuse(int id,int time){
    for(int i=0;i<Adj[id].size();i++){
        int v=Adj[id][i];
        if(isUpdate(links[id],links[v])){
            auto& temp=transactions[time][v][0]; //error：用引用,否则会由于两次访问map而超时
            if(temp.size()==0 || isUpdate(links[id],temp)){
                temp=links[id];
            }
        }
    }
}
void dealTransactions(int b){ //处理b时刻及以前的事务
    for(auto& tTrans:transactions){ //tTrans是t(t<b)时刻的所有结点的事务
        int curTime=tTrans.first;
        if(curTime>b)break;
        for(auto& trans:tTrans.second){ //trans是t时刻的
            int id=trans.first;
            //接受链
            bool acceptFlag;
            if(acceptFlag=isUpdate(trans.second[0],links[id])){
                links[id]=trans.second[0];
            }
            for(int i=0;i<trans.second[1].size();i++){
                links[id].push_back(trans.second[1][i]);
            }
            //传播
            if(acceptFlag || trans.second[1].size()>0){
                diffuse(id,curTime+t);
            }
        }
    }
    //删除b时刻以前的映射
    map<int,unordered_map<int,array<vector<int>,2>>>::iterator it;
    while(true){
        it=transactions.begin();
        if(it==transactions.end())break;//error: 不加这句可能会死循环
        if(it->first<=b)transactions.erase(it);
        else break;
    }
}
int main(){
    ios::sync_with_stdio(false);
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>m;
    init();
    for(int i=0;i<m;i++){
        int u,v;
        cin>>u>>v;
        Adj[u].push_back(v);
        Adj[v].push_back(u);
    }
    //cout<<"done"<<endl;//////
    cin>>t>>k;
    while(k--){
        int a,b,c; //节点，时刻，块
        char temp;
        cin>>a>>b;
        if((temp=cin.get()) != ' '){ //查询
            dealTransactions(b);
            cout<<links[a].size();
            for(int i=0;i<links[a].size();i++){
                cout<<" "<<links[a][i];
            }
            cout<<endl;
        }else{ //产生新块
            cin>>c;
            transactions[b][a][1].push_back(c);
        }
    }
}
```

错误分析：

1. 这题有个大坑：57行，用while(true)时一定要保证能够break出去，否则会死循环，虽然给的样例过了，但是很有可能在线上的时候不幸死循环了，一分也得不到。
2. 超时：6行，26行，对于unordered_map的应用和引用的应用可以避免超时。

# 201909-2 小明种苹果（续）

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

struct Tree{
    int appleNum;
    bool drop;
    Tree(){drop = false;}
};
const int MAXN = 1005;
int n;
Tree trees[MAXN];

void init(){
    for(int i=0; i<MAXN; i++){
        trees[i] = Tree();
    }
}
int main(){
    init();

    //freopen("D:\\input.txt","r",stdin);
    cin >> n;
    for(int i=0; i<n; i++){
        int m;
        cin >> m;
        for(int j=0; j<m; j++){
            if(j == 0){
                cin >> trees[i].appleNum;
            }else{
                int a;
                cin >> a;
                int temp = trees[i].appleNum;
                trees[i].appleNum = a>0 ? a : trees[i].appleNum+a;
                if(a > 0 && a < temp){
                    trees[i].drop = true;
                }
            }
        }
    }

    int T=0, D=0, E=0;
    if(n == 3 && trees[0].drop && trees[1].drop && trees[2].drop)
        E = 1;
    for(int i=0; i<n; i++){
        T += trees[i].appleNum;
        D += trees[i].drop;
        if(n>3 && trees[i].drop && trees[(i+1)%n].drop && trees[(i+2)%n].drop)
            E++;
    }

    printf("%d %d %d",T,D,E);
}
```

# 201909-3 字符画(难)

```cpp
/*
基本思路：这题就是要根据图片输出不同颜色的空格（空格的颜色用更改背景色的方式改变）。先输入图片，
遍历每个小块，然后求每个小块的avgR,avgG,avgB,然后根据它是否是默认色，以及与前一个输出的字符的
颜色是否相同，来写if else。
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int m,n,p,q; //宽为m,高为n,每个小块的大小为p,q
int picture[1920][1080][3];

void outputASCII(int a){ //1的话输出\x30,只能输出数字转化的ASCII
    //printf("\\x3%d",a);
    if(a == 0){
        printf("\\x3%d",a);
    }else{
        int b = 1;
        while(a/b != 0)
            b *= 10;
        b = b/10;
        while(b != 0){ //255就输出\x32\x35\x35
            int c = a/b;
            printf("\\x3%d",c);
            a = a%b;
            b = b/10;
        }
    }
}
void changeBack(int r, int g, int b){ //更改背景色为r g b
    printf("\\x1B\\x5B\\x34\\x38\\x3B\\x32"); //[esc][48;2
    for(int i=0; i<3; i++){
        int a;
        if(i == 0){
            a = r;
        }else if(i == 1){
            a = g;
        }else{
            a = b;
        }
        printf("\\x3B"); //;
        outputASCII(a);
    }
    printf("\\x6D"); //m
}

int char2int(char c){ //a变为10
    int a;
    if(c >= '0' && c <= '9'){
        a = c - '0';
    }else if(c >= 'a' && c <= 'z'){
        a = c - 'a' + 10;
    }else{
        a = c - 'A' + 10;
    }
    return a;
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d\n%d %d\n",&m,&n,&p,&q);
    for(int i=0; i<m*n; i++){
        int y = i/m, x = i%m;
        string input;
        cin >> input;
        if(input.size() == 2){
            int a = char2int(input[1]);
            picture[x][y][0] = picture[x][y][1] = picture[x][y][2] = a*16+a;
        }else if(input.size() == 4){
            int a;
            for(int j=1; j<=3; j++){
                a = char2int(input[j]);
                picture[x][y][j-1] = a*16+a;
            }
        }else{
            int a,b;
            for(int j=1; j<input.size(); j=j+2){
                a = char2int(input[j]);
                b = char2int(input[j+1]);
                picture[x][y][j/2] = a*16+b;
            }
        }
    }

    int preR=-1, preG=-1, preB=-1; //记录前一个字符的背景色
    int divideM = m/p;
    int divideN = n/q;
    for(int i=0; i<divideN; i++){
        int beginY = i*q;
        int avgR,avgG,avgB;
        for(int j=0; j<divideM; j++){
            int beginX = j*p;
            //算平均数
            int sumR=0,sumG=0,sumB=0;
            for(int a=beginY; a<beginY+q; a++){
                for(int b=beginX; b<beginX+p; b++){
                    sumR += picture[b][a][0];
                    sumG += picture[b][a][1];
                    sumB += picture[b][a][2];
                }
            }
            avgR = sumR/(p*q);
            avgG = sumG/(p*q);
            avgB = sumB/(p*q);
            
            if(preR == -1){ //第一次输出字符
                if(!(avgR==0 && avgG==0 && avgB==0)){
                    changeBack(avgR,avgG,avgB);
                    //printf("\\x20");
                }
                printf("\\x20");
            }else{
                if(!(avgR==preR && avgG==preG && avgB==preB)){
                    if(!(avgR==0 && avgG==0 && avgB==0)){
                        changeBack(avgR,avgG,avgB);
                    }else{
                        printf("\\x1B\\x5B\\x30\\x6D"); //[esc][0m
                    }
                }
                printf("\\x20");
            }
            preR = avgR; preG = avgG; preB=avgB;
        }
        if(!(avgR==0 && avgG==0 && avgB==0)){ //输出一行后，若当前不是默认状态，则改为默认状态
            printf("\\x1B\\x5B\\x30\\x6D"); //[esc][0m
            preR = preG = preB = -1;
        }
        printf("\\x0A"); //\n
    }

}
```

错误分析：

1. 我很多函数和stl不会用，导致我要写很多底层代码。

看看别人的代码

```cpp
#include <bits/stdc++.h>
using namespace std;
void output(string& s, array<int, 3> rgb = {0, 0, 0}) {  //输出
    for (char c : s)
        if (c == 'R' || c == 'G' || c == 'B') {  //是RGB数值
            string t = to_string(rgb[c == 'R' ? 0 : c == 'G' ? 1 : 2]);  //将数值转换成字符串
            for (char cc : t)  //遍历字符串
                printf("\\x%02X", cc);  //输出16进制数
        } else
            printf("\\x%02X", c);  //输出字符的16进制数
}
int main() {
    string back = "\x1b[48;2;R;G;Bm", reset = "\x1b[0m";  //背景色和重置默认值字符串
    int m, n, p, q;
    cin >> m >> n >> p >> q;
    vector<vector<array<int, 3>>> image(n);  //图像像素
    string rgb;
    for (int i = 0; i < n; ++i) {
        for (int j = 0; j < m; ++j) {
            cin >> rgb;
            if (rgb.size() == 2)  //只有2位字符
                rgb += string(5, rgb.back());  //字符串末尾添加5个末尾字符
            else if (rgb.size() == 4)  //只有4位字符
                rgb = "#" + string(2, rgb[1]) + string(2, rgb[2]) + string(2, rgb[3]);  //添加成为6位
            image[i].push_back({0, 0, 0});
            for (int t = 0; t < 3; ++t)  //计算RGB数值，将16进制数转换成10进制
                image[i].back()[t] = stoi(rgb.substr(2 * t + 1, 2), 0, 16);
        }
    }
    array<int, 3> last = {0, 0, 0}, start = {0, 0, 0};
    for (int i = 0; i < n / q; ++i) {  //遍历所有像素
        for (int j = 0; j < m / p; ++j) {
            array<int, 3> cur = {0, 0, 0};
            for (int r = 0; r < q; ++r) {  //计算块内RGB颜色分量之和
                for (int s = 0; s < p; ++s) {
                    for (int t = 0; t < 3; ++t)
                        cur[t] += image[i * q + r][j * p + s][t];
                }
            }
            for (int t = 0; t < 3; ++t)  //计算RGB颜色分量平均值
                cur[t] /= p * q;
            if (cur != last)  //和上一个块颜色分量不同
                if (cur == start)  //和默认颜色分量一致，输出重置转义序列
                    output(reset);
                else
                    output(back, cur);
            last = cur;  //更新上一个状态为当前状态
            printf("\\x%02X", ' ');  //每一个块后输出一个空格
        }
        if (last != start)  //每一行结束后恢复默认状态
            output(reset);
        last = start;  //每一行结束后更新上一个状态位默认状态
        printf("\\x%02X", '\n');  //每一行字符后输出一个换行
    }
    return 0;
}
```

我又写的程序：

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1080
#define MAXM 1920

int m,n,p,q;
int graph[MAXN][MAXM][3];

void input(){
    cin>>m>>n>>p>>q;
    string s;
    for(int i=0;i<n;i++){
        for(int j=0;j<m;j++){
            cin>>s;
            if(s.size()==7){
                //long long a;
                int a; //error: sscanf不支持long long型
                sscanf(s.substr(1).c_str(),"%x",&a);
                graph[i][j][0]=a/(256*256);
                graph[i][j][1]=(a%(256*256))/256;
                graph[i][j][2]=(a%256);
            }else if(s.size()==4){
                for(int k=1;k<4;k++){
                    int a=(s[k]>='a'?s[k]-'a'+10:s[k]-'0');
                    graph[i][j][k-1]=a*17;
                }
            }else{
                int a=(s[1]>='a'?s[1]-'a'+10:s[1]-'0');
                graph[i][j][0]=graph[i][j][1]=graph[i][j][2]=a*17;
            }
        }
    }
}
void changeBack(int r,int g,int b){
    string bac="\x1b[48;2;R;G;Bm";
    for(int i=0;i<bac.size();i++){
        if(bac[i]=='R' || bac[i]=='G' || bac[i]=='B'){
            char sint[10];
            sprintf(sint,"%d",(bac[i]=='R'?r:bac[i]=='G'?g:b)); //error:"%s"
            int j=0;
            while(sint[j]!='\0'){
                //printf("\\x%02X",'0'+sint[j]); //error: sint[j]中保存的是'8'，而不是8
                printf("\\x%02X",sint[j]);
                j++;
            }
        }else{
            printf("\\x%02X",bac[i]);
        }
    }
}
void reset(){
    string rest="\x1b[0m";
    for(char c:rest){
        printf("\\x%02X",c);
    }
}
void computeRgb(int row,int col,int&r,int&g,int&b){
    int sumR=0,sumG=0,sumB=0;
    for(int i=row*q;i<row*q+q;i++){
        for(int j=col*p;j<col*p+p;j++){
            sumR+=graph[i][j][0];
            sumG+=graph[i][j][1];
            sumB+=graph[i][j][2];
        }
    }
    r=sumR/(p*q);
    g=sumG/(p*q);
    b=sumB/(p*q);
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    input();
    for(int i=0;i<n/q;i++){ //列索引为[i*q,i*q+q)
        int preR=0,preG=0,preB=0;
        for(int j=0;j<m/p;j++){ //行索引为[j*p,j*p+p)
            int r,g,b;
            //计算r,g,b
            computeRgb(i,j,r,g,b);

            if(r==preR&&g==preG&&b==preB){
                //直接输出
                printf("\\x%02X",' ');
            }else{
                if(!(r==0&&g==0&b==0)){
                    //更改背景色再输出
                    changeBack(r,g,b);
                }else{
                    reset();
                }
                printf("\\x%02X",' ');
            }

            preR=r;preG=g;preB=b;
        }
        if(!(preR==0&&preG==0&&preB==0)){
            //重置为默认
            reset();
        }
        printf("\\x%02X",'\n');
    }
}
```

错误分析：

1. 程序很快写完了，但是调试依然花了不少时间。而且让我花很多时间的并不是很难得设计问题，而是我在细节上犯糊涂，比如18行：我不知道scanf不支持16进制long long的输入；43行，我搞错了，不知道sint中保存的正是8的ASCII码，而不是8的二进制码。

# 201909-4 推荐系统

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXM 55
struct Comd{
	int type,id,score;
	Comd(int a,int b,int c){
		type=a;
		id=b;
		score=c;
	}
	Comd(){};
};
int comdNum=0,m,n,k,opnum; //comdNum是总的商品个数 
vector<Comd> comds;
vector<int> thre(MAXM);
bool cmp(const Comd& a,const Comd& b){ //输入端的排序 
	return (a.score!=b.score?a.score>b.score:a.type!=b.type?a.type<b.type:a.id<b.id);
} 
bool cmp2(const Comd& a,const Comd& b){ //输出端的排序 
	return (a.type!=b.type?a.type<b.type:a.id<b.id);
}
void insert(int type,int id,int score){
	comds.push_back(Comd(type,id,score));
	comdNum++;
	sort(comds.begin(),comds.end(),cmp);
}
void erase(int type,int id){
	vector<Comd>::iterator it;
	for(it=comds.begin();it!=comds.end();it++){
		if(it->type==type && it->id==id){
			comds.erase(it);
			comdNum--;
			break;
		}
	}
}
void query(){
	//依次遍历每个商品
	int i=0;
	vector<int> ans[MAXM];
	while(k>0&&i<comdNum){
		int type=comds[i].type;
		if(thre[type]>0){
			ans[type].push_back(comds[i].id);
			k--;
			thre[type]--;
		}
		i++;
	}
	for(i=0;i<m;i++){
		if(ans[i].size()==0){
			printf("-1\n");
		}else{
			for(int j=0;j<ans[i].size();j++){
				if(j==0){
					printf("%d",ans[i][j]);
				}else{
					printf(" %d",ans[i][j]);
				}
			}
			printf("\n");
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d %d",&m,&n);
	//输入,商品信息存入comds中，注意累加comdNum 
	while(n--){
		int id,score;
		scanf("%d%d",&id,&score);
		for(int i=0;i<m;i++){
			comds.push_back(Comd(i,id,score));
			comdNum++;
		}
	}
	sort(comds.begin(),comds.end(),cmp);
	scanf("%d",&opnum);
	while(opnum--){
		int kind,type,id,score;
		scanf("%d",&kind);
		if(kind==1){
			scanf("%d%d%d",&type,&id,&score);
			insert(type,id,score);
		}else if(kind==2){
			scanf("%d%d",&type,&id);
			erase(type,id);
		}else{
			scanf("%d",&k);
			for(int i=0;i<m;i++){
				int temp;
				scanf("%d",&temp);
				thre[i]=temp;
			}
			query();
			//if(opnum)printf("\n");
		}
	}
	return 0;
}
```

错误分析：

1. 设计错误导致超时。我用的是排序方法来向有序数组中插入新元素，这个代价是巨大的。

```
基本思路2；
由于题目中会有频繁的插入和删除操作，因此必须用set来存储元素（score,id,score）。为了更快的速度，可以
建立(type,id)到set的迭代器的映射，这样每次在删除元素的时候，时间复杂度将为O(logn),但同时增加了插入
map的时间和空间，总体上减少了时间复杂度。
在输出的时候，把同一个type的商品归为一类，这样方便输出。
```

```cpp
#include <bits/stdc++.h>
using namespace std;
struct Commodity {  //商品类
    long long id, score;  //id和分数
    Commodity(long long i, long long s) : id(i), score(s) {}
    bool operator<(const Commodity& c) const {  //重载小于运算符
        return this->score != c.score ? this->score > c.score : this->id < c.id;
    }
};
int main() {
    const long long mul = (long long)(1e9);
    int m, n, id, score, op, t, c, k;
    cin >> m >> n;
    vector<int> K(m);  //存储每类商品被选中的最多件数
    set<Commodity> commodities;  //对所有商品进行排序
    unordered_map<long long, set<Commodity>::iterator> um;  //存储商品id和对应的set中的迭代器
    for (int i = 0; i < n; ++i) {
        cin >> id >> score;
        for (int j = 0; j < m; ++j) {
            long long a = j * mul + id;
            um[a] = commodities.insert(Commodity(a, score)).first;
        }
    }
    cin >> op;
    while (op--) {
        cin >> c;
        if (c == 1) {  //添加商品
            cin >> t >> id >> score;
            long long a = t * mul + id;
            um[a] = commodities.insert(Commodity(a, score)).first;
        } else if (c == 2) {  //删除商品
            cin >> t >> id;
            long long a = t * mul + id;
            commodities.erase(um[a]);
            um.erase(a);
        } else {
            vector<vector<int>> ans(m);
            cin >> k;
            for (int i = 0; i < m; ++i)
                cin >> K[i];
            for (auto& i : commodities) {  //遍历整个set
                t = i.id / mul;
                if (ans[t].size() < K[t]) {
                    ans[t].push_back(i.id % mul);
                    if (--k == 0)  //商品已选满k件，结束遍历
                        break;
                }
            }
            for (auto& i : ans)
                if (i.empty()) {  //没有选中的商品，输出-1
                    cout << "-1\n";
                } else {
                    sort(i.begin(), i.end());//从小到大排序
                    for (auto j : i)
                        cout << j << " ";
                    cout << "\n";
                }
        }
    }
    return 0;
}
```

# 201903-3 损坏的RAID5（难）

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<vector<char>> memory; //memory[1]表示编号为1的磁盘,初始为size=n
int n,l,s;

//若只有0--n-1号磁盘的信息，则恢复n号磁盘的信息
void DiskRestore(){
    int lostDiskNum  = -1;
    int siz;
    for(int i=0; i<n; i++){
        if(memory[i].size() == 0){
            lostDiskNum = i;
        }else{
            siz = memory[i].size();
        }
    }
    if(lostDiskNum == -1)return; //表示磁盘并没有损坏
    memory[lostDiskNum].resize(siz);

    for(int i=0; i<siz; i++){
        char res = '\0';
        for(int j=0; j<n; j++){
            if(j != lostDiskNum){
                res = res^memory[j][i];
            }
        }
        memory[lostDiskNum][i] = res;
        //memory[lostDiskNum].push_back(res);
    }
}

char getChar(char c, char d){
    char res = 0;
    if(isdigit(c)){
        res += (c-'0')*16;
    }else{
        res += (c-'A'+10)*16;
    }
    if(isdigit(d)){ //error:if(isdigit(c))
        res += d-'0';
    }else{
        res += d-'A'+10;
    }
    return res;
}
int main(){
    freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d\n",&n,&l,&s);
    memory.resize(n);
    for(int i=0; i<s; i++){
        int j;
        scanf("%d ",&j);
        //cin >> memory[j]; //error
        char c,d;
        while((c=cin.get()) != '\n'){
            d = cin.get();
            memory[j].push_back(getChar(c,d));
            //printf("%02X\n",getChar(c,d));//////
        }
    }
    //test //////
    /*n = 3;
    memory.resize(n);
    memory[0] = "0";
    memory[1] = "1";
    DiskRestore();
    printf("%02X",memory[2][0]);*/

    DiskRestore();

    int queryNum;
    scanf("%d",&queryNum);
    while(queryNum--){
        int query; //比如要查询7
        scanf("%d",&query);
        int stripeNum = query/(n-1); //在哪个条带
        int restoreBlockPos; //恢复块在哪个磁盘
        restoreBlockPos = (n-1-stripeNum)%n;
        int queryPos = (restoreBlockPos+(query-(n-1)*stripeNum)+1)%n; //要查询的块在哪个磁盘

        //输出
        int queryBegin = stripeNum*4;
        int queryEnd = queryBegin + 4;
        //printf("query=%d queryPos=%d restoreBlockPos=%d queryBegin=%d queryEnd=%d\n",query,queryPos,restoreBlockPos,queryBegin,queryEnd);//////
        for(int i=queryBegin; i<queryEnd; i++){
            printf("%02X",memory[queryPos][i]);
        }
        printf("\n");
    }
}
```

这是错误的代码

错误分析：

1. 业务逻辑错误：理解错了一个条带是什么意思，也没有考虑条带有多少个块组成（以前我认为默认一个块）

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<vector<unsigned char>> memory; //memory[1]表示编号为1的磁盘,初始为size=n
int n,l,s;
const int BLOCKSIZE = 4; //4字节

//若只有0--n-1号磁盘的信息，则恢复n号磁盘的信息
void DiskRestore(){
    int lostDiskNum  = -1;
    int siz;
    for(int i=0; i<n; i++){
        if(memory[i].size() == 0){
            lostDiskNum = i;
        }else{
            siz = memory[i].size();
        }
    }
    if(lostDiskNum == -1)return; //表示磁盘并没有损坏
    memory[lostDiskNum].resize(siz);

    for(int i=0; i<siz; i++){
        unsigned char res = '\0';
        for(int j=0; j<n; j++){
            if(j != lostDiskNum){
                res = res^memory[j][i];
            }
        }
        memory[lostDiskNum][i] = res;
        //memory[lostDiskNum].push_back(res);
    }
}

unsigned char getChar(unsigned char c, unsigned char d){
    unsigned char res = 0;
    if(isdigit(c)){
        res += (c-'0')*16;
    }else{
        res += (c-'A'+10)*16;
    }
    if(isdigit(d)){ //error:if(isdigit(c))
        res += d-'0';
    }else{
        res += d-'A'+10;
    }
    return res;
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d\n",&n,&l,&s);
    memory.resize(n);
    for(int i=0; i<s; i++){
        int j;
        scanf("%d ",&j);
        //cin >> memory[j]; //error
        unsigned char c,d;
        while((c=cin.get()) != '\n'){
            d = cin.get();
            memory[j].push_back(getChar(c,d));
            //printf("%02X\n",getChar(c,d));//////
        }
    }
    //test //////
    /*n = 3;
    memory.resize(n);
    memory[0] = "0";
    memory[1] = "1";
    DiskRestore();
    printf("%02X",memory[2][0]);*/

    DiskRestore();

    int queryNum;
    scanf("%d",&queryNum);
    int stripeSize = l; //一个条带的大小（块）
    while(queryNum--){
        int query; //比如要查询7
        scanf("%d",&query);

        int a = query/stripeSize; //1
        int index2 = a/(n-1); //0
        int index3Begin = index2*stripeSize*BLOCKSIZE + (query-a*stripeSize)*BLOCKSIZE; //0
        int index3End = index3Begin + BLOCKSIZE;
        int restoreIndex1 = (n-1-index2)%n;
        int index1 = (restoreIndex1 + a - (n-1)*index2 + 1)%n;
        for(int i=index3Begin; i<index3End; i++){
            //outputCharBy16(memory[index1][i]);
            printf("%02X",memory[index1][i]);
        }
        printf("\n");
    }
}

//char 和 unsigned char
```

这是错误的代码，20分

错误分析：

1. 业务逻辑错误：没有考虑可能有2个及以上的硬盘丢失的情况。
2. 细节错误：存储一个字节用unsigned char 而不是char
3. 超时：只需在查询时才恢复相应的硬盘块的数据，而不是一开始恢复所有的硬盘数据，这样会超时。

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<vector<unsigned char>> memory; //memory[1]表示编号为1的磁盘,初始为size=n
int n,l,s;
const int BLOCKSIZE = 4; //4字节
int diskLength; //disk有多少个字节

//若只有0--n-1号磁盘的信息，则恢复n号磁盘的信息
void DiskRestore(){
    if(n-s > 1)
        return; //多个磁盘丢失，不能恢复
    int lostDiskNum  = -1;
    int siz;
    for(int i=0; i<n; i++){
        if(memory[i].size() == 0){
            lostDiskNum = i;
        }else{
            siz = memory[i].size();
        }
    }
    if(lostDiskNum == -1)return; //表示磁盘并没有损坏
    memory[lostDiskNum].resize(siz);

    for(int i=0; i<siz; i++){
        unsigned char res = '\0';
        for(int j=0; j<n; j++){
            if(j != lostDiskNum){
                res = res^memory[j][i];
            }
        }
        memory[lostDiskNum][i] = res;
        //memory[lostDiskNum].push_back(res);
    }
}

unsigned char DiskRestore2(int index1, int index2){ //index1是磁盘号
    unsigned char res = '\0';
    for(int j=0; j<n; j++){
        if(j != index1){
            res = res^memory[j][index2];
        }
    }
    return res;
}
unsigned char getChar(unsigned char c, unsigned char d){ //根据A和0得到A0字符
    unsigned char res = 0;
    if(isdigit(c)){
        res += (c-'0')*16;
    }else{
        res += (c-'A'+10)*16;
    }
    if(isdigit(d)){ //error:if(isdigit(c))
        res += d-'0';
    }else{
        res += d-'A'+10;
    }
    return res;
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d\n",&n,&l,&s);
    memory.resize(n);
    for(int i=0; i<s; i++){
        int j;
        scanf("%d ",&j);
        //cin >> memory[j]; //error
        unsigned char c,d;
        int cnt = 0;
        while((c=cin.get()) != '\n'){
            d = cin.get();
            memory[j].push_back(getChar(c,d));
            cnt++; //cnt += 2;
        }
        diskLength = cnt;
    }
    //test //////
    /*n = 3;
    memory.resize(n);
    memory[0] = "0";
    memory[1] = "1";
    DiskRestore();
    printf("%02X",memory[2][0]);*/

    //DiskRestore(); //error

    int queryNum;
    scanf("%d",&queryNum);
    int stripeSize = l; //一个条带的大小（块）
    while(queryNum--){
        int query; //比如要查询7
        scanf("%d",&query);

        int a = query/stripeSize;
        int index2 = a/(n-1);
        int index3Begin = index2*stripeSize*BLOCKSIZE + (query-a*stripeSize)*BLOCKSIZE;
        if(index3Begin >= diskLength){
            printf("-\n");
            continue;
        }
        int index3End = index3Begin + BLOCKSIZE;
        int restoreIndex1 = (n-1-index2)%n;
        int index1 = (restoreIndex1 + a - (n-1)*index2 + 1)%n;

        if(memory[index1].size() > 0){
            for(int i=index3Begin; i<index3End; i++){
                printf("%02X",memory[index1][i]);
            }
            printf("\n");
        }else if(n-s <= 1){
            for(int i=index3Begin; i<index3End;i++){
                unsigned char res = DiskRestore2(index1,i);
                printf("%02X",res);
            }
            printf("\n");
        }
        else{
            printf("-\n");
        }
    }
}

//char 和 unsigned char
```

改动了，相较于上一版本，样例方面没有提升，20分

错误分析：

1. 我觉得超时的原因是输入不好。
2. 细节错误：75行。计算每个磁盘的字节数出错

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<vector<unsigned char>> memory; //memory[1]表示编号为1的磁盘,初始为size=n
int n,l,s;
const int BLOCKSIZE = 4; //4字节
int diskLength; //disk有多少个字节

//若只有0--n-1号磁盘的信息，则恢复n号磁盘的信息
void DiskRestore(){
    if(n-s > 1)
        return; //多个磁盘丢失，不能恢复
    int lostDiskNum  = -1;
    int siz;
    for(int i=0; i<n; i++){
        if(memory[i].size() == 0){
            lostDiskNum = i;
        }else{
            siz = memory[i].size();
        }
    }
    if(lostDiskNum == -1)return; //表示磁盘并没有损坏
    memory[lostDiskNum].resize(siz);

    for(int i=0; i<siz; i++){
        unsigned char res = '\0';
        for(int j=0; j<n; j++){
            if(j != lostDiskNum){
                res = res^memory[j][i];
            }
        }
        memory[lostDiskNum][i] = res;
        //memory[lostDiskNum].push_back(res);
    }
}

unsigned char DiskRestore2(int index1, int index2){ //index1是磁盘号
    unsigned char res = '\0';
    for(int j=0; j<n; j++){
        if(j != index1){
            res = res^memory[j][index2];
        }
    }
    return res;
}
unsigned char getChar(unsigned char c, unsigned char d){ //根据A和0得到A0字符
    unsigned char res = 0;
    if(isdigit(c)){
        res += (c-'0')*16;
    }else{
        res += (c-'A'+10)*16;
    }
    if(isdigit(d)){ //error:if(isdigit(c))
        res += d-'0';
    }else{
        res += d-'A'+10;
    }
    return res;
}

int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d\n",&n,&l,&s);
    memory.resize(n);
    for(int i=0; i<s; i++){
        int j;
        string str;
        cin >> j >> str;
        diskLength = str.size()/2;
        for(int a=0; a<diskLength; a++){
            unsigned char res;
            res = getChar(str[a*2],str[a*2+1]);
            memory[j].push_back(res);
        }
    }

    //DiskRestore(); //error

    int queryNum;
    scanf("%d",&queryNum);
    int stripeSize = l; //一个条带的大小（块）
    while(queryNum--){
        int query; //比如要查询7
        scanf("%d",&query);

        int a = query/stripeSize;
        int index2 = a/(n-1);
        int index3Begin = index2*stripeSize*BLOCKSIZE + (query-a*stripeSize)*BLOCKSIZE;
        if(index3Begin >= diskLength){
            printf("-\n");
            continue;
        }
        int index3End = index3Begin + BLOCKSIZE;
        int restoreIndex1 = (n-1-index2)%n;
        int index1 = (restoreIndex1 + a - (n-1)*index2 + 1)%n;

        if(memory[index1].size() > 0){
            for(int i=index3Begin; i<index3End; i++){
                printf("%02X",memory[index1][i]);
            }
            printf("\n");
        }else if(n-s <= 1){
            for(int i=index3Begin; i<index3End;i++){
                unsigned char res = DiskRestore2(index1,i);
                printf("%02X",res);
            }
            printf("\n");
        }
        else{
            printf("-\n");
        }
    }
}

//char 和 unsigned char
```

30分，相较于上一版本，改变了输入方法，但是还是超时

错误分析：

1. 超时：超时原因可能是：输入的string转换为字节数组时很耗时，最好只在查询时，才转换特定的字节，因为查询的数量远小于硬盘的大小。

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<string> memory; //memory[1]表示编号为1的磁盘,初始为size=n,半字节单位
string *memory;
int n,l,s;
const int BLOCKSIZE = 4; //4字节
int diskLength; //disk有多少个字节
int querys[1005];

int main(){
    ios::sync_with_stdio(false); //error:
    //freopen("D:\\input.txt","r",stdin);
    cin >> n >> l >> s;
    memory = new string[n];
    for(int i=0; i<s; i++){
        int j;
        cin >> j;
        cin >> memory[j];
        diskLength = memory[j].length()/2;
    }
    int queryNum;
    cin >> queryNum;
    for(int i=0; i<queryNum; i++){
        cin >> querys[i];
    }

    int stripeSize = l; //一个条带的大小（块）
    for(int i=0; i<queryNum; i++){
        int query = querys[i]; //比如要查询7

        int a = query/stripeSize; //条带的索引
        int index2 = a/(n-1); //小块单位
        int index3Begin = index2*stripeSize*BLOCKSIZE + (query-a*stripeSize)*BLOCKSIZE; //字节单位
        int index3End = index3Begin + BLOCKSIZE;
        if(index3End > diskLength){
            //printf("-\n");
            cout << "-" << endl;
            continue;
        }
        int restoreIndex1 = n-1-index2%n;
        //int index1 = (restoreIndex1 + a - (n-1)*index2 + 1)%n;
        int index1 = (restoreIndex1 + 1 + a%(n-1))%n; //磁盘号

        if(memory[index1].size() > 0){
            string str;
            str = memory[index1].substr(index3Begin*2,BLOCKSIZE*2);
            cout << str << endl;
        }else if(n-s <= 1){
            string str = "00000000";
            for(int j=0; j<n; j++){
                if(j == index1)
                    continue;
                int cnt = 0;
                for(int i=index3Begin*2; i<index3End*2; i++){
                    unsigned char c1,c2;
                    c1 = str[cnt] <= '9' ? str[cnt]-'0' : str[cnt]-'A'+10;
                    c2 = memory[j][i] <= '9' ? memory[j][i]-'0' : memory[j][i]-'A'+10;
                    str[cnt] = c1^c2;
                    str[cnt] = str[cnt] <= 9 ? '0'+str[cnt] : str[cnt]-10+'A';
                    cnt++;
                }
            }
            cout << str << endl;
        }else{
            //printf("-\n");
            cout << "-" << endl;
        }
    }

    delete[] memory;
}

//char 和 unsigned char
```

错误分析：

1. 13行。在读取和输出大文件时，要用``ios::sync_with_stdio(false);`` 并用cin, cout来输入输出

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

int n,s,l,m;
int diskLen;
vector<string> disks;

void init(){
    disks.resize(n);
}

//查询在哪个硬盘，哪个条带，哪个块，然后输出
void Search(int x){
    int index1=x/s;
    int dim1=index1/(n-1); //x在哪个条带
    int pDisk=(n-1-dim1)%n;//P条带在哪个磁盘
    int xDisk=(pDisk+index1%(n-1)+1)%n;//x块在哪个磁盘
    int index2=x%s; //x在条带中的索引,单位是块
    int i=(dim1*s+index2)*8; //x在哪个磁盘中的哪个半字节
    //cout<<"pDisk="<<pDisk<<" xDisk="<<xDisk<<" dim1="<<dim1<<" i="<<i<<endl;//////
    if(i+8>diskLen){
        cout<<"-"<<endl;
        return;
    }
    if(disks[xDisk].size()!=0){
        for(int j=0;j<8;j++){
            cout<<disks[xDisk][i+j];
        }
        cout<<endl;
    }else if(n-l<=1){
        for(int j=0;j<8;j++){ //恢复数据
            char c='\0'; //要恢复的半字节
            for(int k=0;k<n;k++){
                if(k!=xDisk){
                    char c1=disks[k][i+j];
                    if(c1>='0'&&c1<='9'){ //error: c1>='0'&&c1<<'9'
                        c1=c1-'0';
                    }else{
                        c1=c1-'A'+10;
                    }
                    c = c^c1; //值在0-15间
                }
            }
            if(c>=10)c=c-10+'A'; //error: 忘了把值转为ASCII码了，否则输出不出去
            else c=c+'0';
            cout<<c;
        }
        cout<<endl;
    }else{ //阵列无法恢复
        cout<<"-"<<endl;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    ios::sync_with_stdio(false);
    cin>>n>>s>>l;
    init();
    for(int i=0;i<l;i++){
        int diskId;
        cin>>diskId;
        cin>>disks[diskId];
        diskLen=disks[diskId].size();
    }
    cin>>m;
    while(m--){
        int x;
        cin>>x;
        Search(x);
    }
}
```

错误分析：45行，字符的格式转换我真是转不过这个弯呢

# 201903-1 小中大

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
//#include<bits/stdc++.h>
//using namespace std;

int num[100000+5];
int n;

int main(int argc, char* argv[]){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int max2=-(1e8+5),min2=1e8+5;
    double mid;
    int a,i;
    for(i=0;i<n;i++){
        scanf("%d",&a);
        if(a>max2)
            max2=a;
        if(a<min2)
            min2=a;
        num[i]=a;
    }

    int left,right;
    if(n%2==1){
        mid = (double)num[n/2];
    }else{
        left=num[n/2-1];
        right=num[n/2];
        double temp = (left+right)/2.0;
        if(temp>0.0){
            mid = (int)(temp*10+0.5)/10.0; //四舍五入
        }else{
            mid = (int)(temp*10-0.5)/10.0;
        }
    }

    if(n%2==1 || (left+right)%2==0){
        printf("%d %d %d",max2,(int)mid,min2);
    }else{
        printf("%d %.1f %d",max2,mid,min2);
    }

    return 0;
}
```

# 201903-4 消息传递接口

```cpp
/*
基本思路：用vector<string>存储每个进程的所有指令，然后用指针法，指针指向当前这个在需要完成的指令。
1. 不是所有的所有的指针都到达末尾的话，
2. 从0号指令开始，找后面有没有匹配的，若有，则更新指针，回到1，若没有，则从1号找......
*/
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

vector<string> processes; //存储每个进程的指令,processes[0]="R1S2"表示1号进程的指令为R1,S2
vector<int> pointers;
int t,n;

void erase_n(char str[]){
    int i=0;
    while(str[i] != '\0' && str[i] != '\n') //error:i != '\0'
        i++;
    str[i] = '\0';
}

void init(){
    processes.clear();
    processes.resize(n);
    pointers.clear();
    pointers.resize(n);
    for(int i=0; i<n; i++){
        pointers[i] = 0;
    }
}

bool allEnd(){ //看是否所有指令都执行完毕
    for(int i=0; i<n; i++){
        if(pointers[i] != processes[i].size()){
            return false;
        }
    }
    return true;
}

int haveMatch(int i){ //看i号进程后面有没有与i号进程当前执行的指令匹配的
    char a1,a2;
    int b1 = 0, b2 = 0;
    int j = pointers[i] + 1;
    while(j < processes[i].size() && processes[i][j] != ' '){
        b1 = b1*10 + processes[i][j]-'0';
        j++;
    }
    if(b1 >= n || b1 <=i){
        return -1;
    }
    a2 = processes[b1][pointers[b1]];
    if(a2 == a1){
        return -1;
    }
    j = pointers[b1] + 1;
    while(j < processes[b1].size() && processes[b1][j] != ' '){
        b2 = b2*10 + processes[b1][j]-'0';
        j++;
    }
    if(b2 != i){
        return -1;
    }
    return b1;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin >> t >> n;
    cin.get(); //error:去掉换行符
    char input[100]; //接受字符串
    while(t--){
        init();
        for(int i=0; i<n; i++){
            fgets(input,100,stdin);
            erase_n(input);
            processes[i] = string(input);
        }
        while(!allEnd()){
            bool flag = false; //是否有可匹配的进程
            for(int i=0; i<n && flag==false; i++){
                if(pointers[i] == processes[i].size())
                    continue;
                int j;
                if((j=haveMatch(i)) != -1){
                    /*pointers[i] = min(processes[i].size(),pointers[i]+3);
                    pointers[j] = min(processes[j].size(),pointers[j]+3);
                    flag = true;*/
                    pointers[i]++;
                    while(pointers[i]<processes[i].size() && processes[i][pointers[i]] != 'R' && processes[i][pointers[i]] != 'S')
                        pointers[i]++;
                    pointers[j]++;
                    while(pointers[j]<processes[j].size() && processes[j][pointers[j]] != 'R' && processes[j][pointers[j]] != 'S')
                        pointers[j]++;
                    flag = true;
                }
            }
            if(!flag)
                break; //如果找不到可以匹配的进程
        }
        if(allEnd()){
            cout << "0" << endl;
        }else{
            cout << "1" << endl;
        }
    }

    //test
    /*n = 3;
    processes.resize(3);
    processes[0] = "R1 S1";
    processes[1] = "S0 R0 S2";
    processes[2] = "R1";
    pointers.resize(3);
    pointers[0] = 0;
    pointers[1] = 0;
    pointers[2] = 0;
    int a = haveMatch(0);
    cout << a << endl;*/
}
```

超时代码：80分

错误分析：

1. 细节错误：16行，68行
2. 超时

别人的代码

```cpp
#include<bits/stdc++.h>
using namespace std;
int main(){
    int T,n;
    scanf("%d%d%*c",&T,&n);
    while(T--){
        list<pair<queue<int>,int>>process;//first成员是一个队列，存储每个进程的所有指令；second成员存储上一条执行的指令，初始化为INT_MAX
        unordered_set<int>commands;//存储每个进程正在执行的指令
        string s;
        for(int i=0;i<n;++i){
            process.push_back({queue<int>(),INT_MAX});
            getline(cin,s);
            for(int j=0,k;j<s.size();j=k+1){//按空格键分割字符串，并用整数表示指令
                k=s.find(' ',j);
                if(k==string::npos)
                    k=s.size();
                if(s[j]=='S')
                    process.back().first.push(i*10000+stoi(s.substr(j+1,k-j-1)));
                else
                    process.back().first.push(-(stoi(s.substr(j+1,k-j-1))*10000+i));
            }
        }
        for(bool f=true;!process.empty();f=true){//f标志是否所有进程指令都被堵塞
            for(auto i=process.begin();i!=process.end();++i){//遍历所有进程
                if(commands.find(i->second)!=commands.end())//上一次执行的指令被堵塞了
                    continue;//进程也被堵塞，其他指令无法执行
                if((i->first).empty()){//所有指令均已执行完毕
                    i=process.erase(i);//将该进程删除
                    --i;
                    continue;
                }
                while(!(i->first).empty()){//进程还有指令要执行
                    int t=(i->first).front();//从队首弹出一条指令
                    (i->first).pop();
                    auto j=commands.find(-t);//查找其它进出中是否有正在执行的对应的指令
                    if(j==commands.end()){//没有对应的指令
                        commands.insert(t);//将该指令插入到commands中
                        i->second=t;//更新上一次执行的指令为当前指令
                        break;
                    }else{//有对应的指令
                        commands.erase(j);//将对应的指令从commands中删除
                        f=false;//并不是所有进程都被堵塞
                    }
                }
            }
            if(f)//遍历完所有进程后发现所有进程均被堵塞，说明发生死锁
                break;
        }
        puts(process.empty()?"0":"1");//跳出循环时进程list为空，表示所有进程指令均执行完毕，则没有死锁
    }
    return 0;
}
```

分析：

1. 使用大量的stl容器，遍历所有进城后查找速度为nlogn，而我的查找速度为n^2。

# 201812-1 小明上学

```cpp
//freopen("D:\\input.txt","r",stdin);
#include <stdio.h>
#include <stdlib.h>

int r,y,g,n;
int ans = 0;

int main(int argc, char *argv[]){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d %d",&r,&y,&g,&n);
    int type,time;
    while(n--){
        scanf("%d %d",&type,&time);
        if(type==0){
            ans+=time;
        }else if(type==1){
            ans+=time;
        }else if(type==2){
            ans+=time+r;
        }
    }
    printf("%d",ans);
    return 0;
}
```

# 201812-2 小明放学

```cpp
//freopen("D:\\input.txt","r",stdin);
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
typedef long long lld;

int r,y,g,n;
lld ans= 0;

void convert(lld timePass,int* type,int* time){
    if(*type==0)
        return;
    lld a;
    if(*type==2){
        a = y-*time;
    }else if(*type==1){
        a = y+r-*time;
    }else{
        a = y+r+g-*time;
    }
    a = (a+timePass)%(r+y+g);
    if(a<y){
        *type=2; *time=y-a;
    }else if(a<y+r){
        *type=1;
        *time=y+r-a;
    }else{
        *type=3;
        *time=y+r+g-a;
    }
}

int main(int argc, char* argv[]){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d %d",&r,&y,&g,&n);
    int type,time;
    while(n--){
        scanf("%d %d",&type,&time);
        convert(ans,&type,&time);
        if(type==0){
            ans+=time;
        }else if(type==1){
            ans += time;
        }else if(type==2){
            ans += time+r;
        }
    }
    printf("%lld",ans);
    return 0;
}
```

# *201812-3 CIDR合并（难）

```cpp
//freopen("D:\\input.txt","r",stdin);
//#include<stdio.h>
//#include<stdlib.h>
#include<bits/stdc++.h>
typedef long long lld;
using namespace std;

struct Pre{
    lld pre;//前缀的十进制形式
    int preLen; //如24
    Pre(lld _pre, int _preLen){pre=_pre; preLen=_preLen;}
    bool operator < (const Pre& p) const{ //error
        return (p.pre!=pre) ? this->pre<p.pre : this->preLen<p.preLen; //error
    }
};
set<Pre> se;
lld pow2[33]; //2的i次方

void convert1(string& str, lld& res1, int& res2){ //转换标准型
    int k1=0,k2=0;
    while((k2=str.find('.',k1)) != string::npos || (k2=str.find('/',k1)) != string::npos){
        string temp = str.substr(k1,k2-k1);
        int a;
        sscanf(temp.c_str(),"%d",&a);
        res1 = res1*256 + a;
        k1 = k2+1;
    }
    int a;
    sscanf(str.substr(k1).c_str(),"%d",&a);
    res2 = a;
}
void convert2(string& str, lld& res1, int& res2){ //转换省略后缀型
    int k1=0,k2=0;
    int cnt = 0;
    while((k2=str.find('.',k1)) != string::npos || (k2=str.find('/',k1)) != string::npos){
        string temp = str.substr(k1,k2-k1);
        int a;
        sscanf(temp.c_str(),"%d",&a);
        res1 = res1*256 + a;
        k1 = k2+1;
        cnt++;
    }
    for(int i=cnt+1;i<=4;i++){
        res1 = res1*256;
    }
    int a;
    sscanf(str.substr(k1).c_str(),"%d",&a);
    res2 = a;
}
void convert3(string& str, lld& res1, int& res2){ //转换省略长度型
    int k1=0,k2=0;
    int cnt=0;
    while(k2 != string::npos){
        k2 = str.find('.',k1);
        string temp = (k2!=string::npos)?str.substr(k1,k2-k1):str.substr(k1);
        int a;
        sscanf(temp.c_str(),"%d",&a);
        res1 = res1*256 + a;
        k1 = k2+1;
        cnt++;
    }
    res2 = cnt*8;
    for(int i=cnt+1;i<=4;i++){
        res1 = res1*256;
    }
}
void convert(string& str,lld& res1, int& res2){
    smatch sm;
    regex reg1 = regex("[\\d]+.[\\d]+.[\\d]+.[\\d]+/[\\d]+");
    regex reg3 = regex("[^/]+");
    if(regex_match(str,sm,reg1)){ //如果是标准型
        //cout<<"1"<<endl;//////
        convert1(str,res1,res2);
    }else if(regex_match(str,sm,reg3)){ //如果是省略长度型
        //cout<<"3"<<endl;//////
        convert3(str,res1,res2);
    }else{
        //cout<<"2"<<endl;//////
        convert2(str,res1,res2);
    }
}

bool isContain(Pre p1, Pre p2){ //p1是否包含p2
    int minPreLen = min(p1.preLen,p2.preLen);
    lld a1=p1.pre/pow2[32-minPreLen];
    lld a2=p2.pre/pow2[32-minPreLen];
    if(a1 == a2 && p1.preLen<p2.preLen){ //error: a1 = a2
        return true;
    }
    return false;
}
void merge2(){ //根据是否包含来合并
     set<Pre>::iterator it;
     set<Pre>::iterator it2;
     for(it=se.begin();it!=se.end();){
        it2 = ++it; it--;//error: it2 = ++it导致it本身变了
        if(it2 != se.end() && isContain(*it,*it2)){ //
            se.erase(it2);
        }else{
            it++;
        }
     }
}

bool canMerge(Pre p1, Pre p2){ //两个路由前缀是否可以合并
    lld a1,a2,b1,b2;
    if(p1.preLen == p2.preLen){
        a1=p1.pre/pow2[32-p1.preLen];
        a2=p2.pre/pow2[32-p1.preLen];
        b1=a1/2;
        b2=a2/2;
        if(abs(a1-a2) == 1 && b1==b2){ //error:
            return true;
        }
    }
    return false;
}
void merge3(){
    set<Pre>::iterator it;
    set<Pre>::iterator it2; //前面的
    set<Pre>::iterator it3; //后面的
    for(it=se.begin();it!=se.end();){
        if(it == se.begin()){
            it3=++it; it--;
            if(it3 != se.end() && canMerge(*it,*it3)){
                Pre temp(min((*it).pre,(*it3).pre), (*it).preLen-1); //error: it是只读的
                se.erase(it);
                se.erase(it3);
                it = se.insert(temp).first; //插入位置的迭代器
            }else{
                it++;
            }
        }else{
            it2=--it; it++;
            if(it2 != se.end() && canMerge(*it,*it2)){
                Pre temp(min((*it).pre,(*it2).pre), (*it).preLen-1);
                se.erase(it);
                se.erase(it2);
                it = se.insert(temp).first; //插入位置的迭代器
            }else{
                it++;
            }
        }
    }
}

void init(){
    lld res = 1;
    for(int i=0;i<=32;i++){
        pow2[i]=res;
        res *= 2;
    }
}
int main(){
    init();

    //freopen("D:\\input.txt","r",stdin);
    int n;
    cin >> n;
    string pre; //输入的前缀
    lld p=0; int pLen=0; //十进制，前缀长度
    while(n--){
        p=0; pLen=0;
        cin>>pre;
        convert(pre,p,pLen);
        se.insert(Pre(p,pLen));
    }
    merge2();
    merge3();

    //输出
    for(auto& i:se){
        int a,b,c,d;
        a = i.pre/pow2[24];
        b = (i.pre%pow2[24])/pow2[16];
        c = (i.pre%pow2[16])/pow2[8];
        d = i.pre%pow2[8];
        cout<<a<<"."<<b<<"."<<c<<"."<<d<<"/"<<i.preLen<<endl;
    }
}

```

错误分析：

1. 语法错误：这种题的数值可能超过int，所以注意要用long long

    126行，我试图对set元素进行更改: ``*it=newValue`` 但是不成功，因为read-only。我只能先删除，再插入。

    ``it = set.insert(temp).first`` 

2. 细节错误，96行。当使用set遍历时，要访问当前迭代器的前后元素时，要这样做``it2=++it; it--`` 不要忘了后面的``it--``。

3. 逻辑错误：112行，验证两个路由前缀是否可以合并时，判断条件错误。考虑case``00000001与00000010`` ，这两个路由前缀虽然值相差1，但是并不能合并。

4. 数据更新错误, 163行

    ```cpp
    int a;
    while(true){
        a=0;
        //a进行处理
    }
    ```

    数据忘了初始化也会很头疼。

2020/4/6更

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
typedef long long lld;
struct Pre{
	lld pre;
	int prelen;
	bool operator<(const Pre&p) const{
		return (pre!=p.pre?pre<p.pre:prelen<p.prelen);
	}
};
set<Pre> prelist;
int n;
Pre dealPre(string s){
	vector<string> parts;
	int i=0;
	while(i!=string::npos){
		int j=s.find('.',i);
		parts.push_back(j!=string::npos?s.substr(i,j-i):s.substr(i));
		i=(j!=string::npos?j+1:j);
	}
	int k=parts.back().find('/');
	if(parts.size()==4&&k!=string::npos){ //error: if(parts.size()==4)
		lld pre=(lld)stoi(parts[0])*256*256*256+(lld)stoi(parts[1])*256*256+(lld)stoi(parts[2])*256+(lld)stoi(parts[3].substr(0,k));
		int prelen=stoi(parts[3].substr(k+1));
		return {pre,prelen};
	}else{
		if(k!=string::npos){
			lld a=256*256*256;
			lld pre=0;
			int prelen;
			for(int i=0;i<parts.size();i++){
				if(i==parts.size()-1){
					prelen=stoi(parts[i].substr(k+1));
					pre+=a*stoi(parts[i].substr(0,k));
				}else{
					pre+=a*stoi(parts[i]);
					a/=256;
				}
			}
			return {pre,prelen};
		}else{
			lld a=256*256*256;
			lld pre=0;
			int prelen=8*parts.size();
			for(int i=0;i<parts.size();i++){
				pre+=a*stoi(parts[i]);
				a/=256;
			}
			return {pre,prelen};
		}
	}
}
lld pow2(int x){
	int ans=1;
	for(int i=0;i<x;i++){
		ans*=2;
	} 
	return ans;
}
bool iscontains(Pre p1,Pre p2){ 
	if(p1.prelen==p2.prelen && p1.pre==p2.pre){
		return true;
	}
	if(p1.prelen<p2.prelen){
		lld a1=p1.pre/pow2(32-p1.prelen);
		lld a2=p2.pre/pow2(32-p1.prelen);
		return a1==a2;
	}else{
		return false;
	}
}
bool canmerge(Pre p1,Pre p2){
	if(p1.prelen!=p2.prelen){
		return false;
	}
	lld a1=p1.pre/pow2(32-p1.prelen);
	lld a2=p2.pre/pow2(32-p1.prelen);
	return a1/2==a2/2&&a1%2!=a2%2;
}
Pre merge(Pre p1,Pre p2){
	if(p1.pre<p2.pre){
		return {p1.pre,p1.prelen-1};
	}else{
		return {p2.pre,p1.prelen-1};
	}
}
string int2std(Pre pre){
	string ans;
	lld p=pre.pre;
	lld a=256*256*256;
	ans+=to_string(p/a);
	p%=a;
	a/=256;
	while(a!=0){
		ans+="."+to_string(p/a);
		p%=a;
		a/=256;
	}
	return ans+"/"+to_string(pre.prelen);
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	string input;
	while(n--){
		cin>>input;
		prelist.insert(dealPre(input));
	}
	//从小到大合并
	auto it=prelist.begin();
	while(it!=prelist.end()){
		auto temp=it++;
		if(it!=prelist.end()&&iscontains(*temp,*it)){
			prelist.erase(it);
			it=temp;
		}
	} 
	//同级合并
	it=prelist.begin();
	while(it!=prelist.end()){
		auto temp=it++;
		if(it==prelist.end())
			continue;
		if(canmerge(*temp,*it)){
			Pre newt=merge(*temp,*it);
			prelist.erase(temp);
			prelist.erase(it);
			it=prelist.insert(newt).first;
			if(it!=prelist.begin()){
				it--;
			}
		} 
	}
	//输出
	for(const auto& i:prelist){
		cout<<int2std(i)<<endl;
	} 
	return 0;
} 
```

# 201812-5 清洁管道

https://www.cnblogs.com/nietzsche-oier/p/8185805.html

https://blog.csdn.net/weixin_42062229/article/details/99964634

# 201809-2 买菜

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<stdio.h>
#include<stdlib.h>
#include<string.h>
//#include<bits/stdc++.h>
//using namespace std;

int a[1000000+5];
int ans=0, n;

int main(int argc, char* argv[]){
    memset(a,0,sizeof(a));

    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    int left, right, i, j;
    for(i=0;i<n;i++){
        scanf("%d %d",&left,&right);
        for(j=left;j<right;j++){
            a[j]=1;
        }
    }
    for(i=0;i<n;i++){
        scanf("%d %d",&left,&right);
        for(j=left;j<right;j++){
            if(a[j]==1)
                ans++;
        }
    }
    printf("%d",ans);
    return 0;
}
```

# 201809-3 元素选择器

```cpp
//freopen("D:\\input.txt","r",stdin);
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

struct Node{
    string str;
    int father;
    Node(){father = -1;}
};
int cnt = 0; //记录标号
vector<Node> tree; //记录每一个节点的数据
vector<vector<int>> layers; //记录层级关系
int n,m;

void convertStr(string& str){ //不是id的部分都变成小写
    int a=0;
    for(int i=0;i<str.size();i++){
        if(str[i]=='#'){
            a=1;
        }else if(str[i]==' '){
            a==0;
        }else{
            if(a==0)
                str[i]=tolower(str[i]);
        }
    }

}
int dealStr(string& str,string& str2){
    int k1=0;
    while(str[k1]=='.')
        k1++;
    str2 = str.substr(k1);
    /*for(int i=0;i<str2.size();i++)
        str2[i] = tolower(str2[i]); //error:*/
    convertStr(str2); //error
    return k1/2;
}
int getFather(int layer){
    if(layer==0)
        return -1;
    return layers[layer-1].back();
}
void init(){
    tree.resize(n);
    layers.resize(n);
}
void splitStr(string& str, vector<string>& vec){ //根据空格分割
    int k1=0,k2=0;
    while(k2 != string::npos){
        k2 = str.find(' ',k1);
        vec.push_back((k2!=string::npos)?str.substr(k1,k2-k1):str.substr(k1));
        /*for(int i=0;i<vec.back().size();i++)
            vec.back()[i] = tolower(vec.back()[i]); //error:*/
        convertStr(vec.back()); //error
        k1 = k2+1;
    }
}
bool isRight(int i, int j, vector<string>& vs){ //第i层的第j个节点是否满足
    int cur = layers[i][j]; //当前正在访问的节点的标号
    int k=vs.size()-1; //error: vs的下标
    while(k>=0 && cur!=-1){
        if(tree[cur].str.find(vs[k])!=string::npos){
            k--;
            cur = tree[cur].father;
        }else if(k==vs.size()-1){
            break;
        }else{
            cur = tree[cur].father; //error
        }
    }
    if(k<0)
        return true;
    else
        return false;
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>n>>m;
    getchar();

    init();

    string tempStr;
    int maxLayer = -1;
    while(n--){
        getline(cin,tempStr); //例如：..head
        int a = dealStr(tempStr,tree[cnt].str); //处理输入，得到层级和存储字符串
        if(a>maxLayer)maxLayer=a;
        tree[cnt].father = getFather(a);
        layers[a].push_back(cnt);
        cnt++;
    }
    while(m--){
        vector<int> lines; //符合要求的行
        vector<string> splitedStrs; //分割字符串
        getline(cin,tempStr);
        splitStr(tempStr,splitedStrs);

        int num = splitedStrs.size(); //字符串个数
        for(int i=num-1;i<=maxLayer;i++){
            for(int j=0;j<layers[i].size();j++){
                if(isRight(i,j,splitedStrs)){
                    lines.push_back(layers[i][j]+1);
                }
            }
        }
        //输出
        sort(lines.begin(),lines.end());
        cout<<lines.size();
        for(int i=0;i<lines.size();i++)
            cout<<" "<<lines[i];
        cout<<endl;
    }
}
```

错误代码：70分

1. 细节错误：48行，忘了赋初值

2. 逻辑错误：题目要求对标签的查询大小写不敏感，对id的查询大小写敏感，我以前没有实现这点

    72行，isRight()函数的逻辑错误，只有在第一个要比较的字符串不成功才break

# 201809-4 再卖菜

```cpp
//freopen("D:\\input.txt","r",stdin);
/*基本思路：回溯法*/
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

const int MAXN = 305;
int prices2[MAXN];
int prices1[MAXN];
int n;

void getProbs(int d, vector<int>&probs){
    if(d==0){
        for(int i=1;i<2*(prices2[1]+1)-1;i++){
            probs.push_back(i);
        }
    }else if(d==1){
        int temp=2*prices2[1]-prices1[1];
        int b=2*(prices2[1]+1);
        if(prices1[1]>=b-1)
            return; //error
        int i=(temp<=0)?1:temp;
        while(i+prices1[1]<b){
            probs.push_back(i);
            i++;
        }
    }else{
        int temp=3*prices2[d]-(prices1[d-1]+prices1[d]);
        int a=prices1[d-1]+prices1[d];
        int b=3*(prices2[d]+1);
        if(a>=b-1)
            return;
        int i=(temp<=0)?1:temp;
        while(i+a<b){
            probs.push_back(i);
            i++;
        }
    }
}
bool flag=false;
void dfs(int d){
    if(d==n && (prices1[d-1]+prices1[d])/2==prices2[d]){ //errir:d==8
        flag=true;
        return;
    }else if(d==n){
        return;
    }
    vector<int> probs; //prices1[d+1]可能取的值
    getProbs
    (d,probs);
    for(int i=0;i<probs.size();i++){
        prices1[d+1]=probs[i];
        //cout<<"prices1["<<d+1<<"]="<<prices1[d+1]<<endl;//////
        dfs(d+1);
        if(flag)
            break;
    }
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d",&n);
    for(int i=1;i<=n;i++){
        scanf("%d",&prices2[i]);
    }
    dfs(0);
    if(flag){
        for(int i=1;i<=n;i++){
            if(i==1){
                printf("%d",prices1[i]);
            }else{
                printf(" %d",prices1[i]);
            }
        }
    }
}
```

错误代码：70分

1. 细节错误：43行，这个错误太不应该了。
2. 运行超时。

# 201803-2 碰撞的小球

```cpp
//freopen("D:\\input.txt","r",stdin);
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

struct Ball{
    int dir,pos,next;
    Ball(){dir=1;next=0;}
};
const int MAXN = 105;
const int MAXL = 1005;
int n,L,t;
Ball balls[MAXN]; //1-n
int road[MAXL]; //0-L,表示该位置的小球标号

void init(){
    for(int i=0;i<MAXN;i++)balls[i]=Ball();
    memset(road,0,sizeof(road));
}
int main(){
    init();
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d %d",&n,&L,&t);
    int pos;
    for(int i=1;i<=n;i++){
        scanf("%d",&pos);
        road[pos]=i;
        balls[i].pos=pos;
    }
    while(t--){
        for(int i=1;i<=n;i++){
            int nextpos = balls[i].pos+balls[i].dir;
            if(road[nextpos]!=0){
                balls[i].next=road[nextpos];
                balls[i].dir=-balls[i].dir;
                balls[road[nextpos]].dir=-balls[road[nextpos]].dir;
            }else if(nextpos!=0 && nextpos!=L){
                balls[i].next=0;
            }else{
                balls[i].dir=-balls[i].dir;
            }
            road[nextpos]=i;
            road[balls[i].pos]=0;
            balls[i].pos=nextpos;
        }
    }
    for(int i=1;i<=n;i++){
        if(i==1){
            printf("%d",balls[i].pos);
        }else{
            printf(" %d",balls[i].pos);
        }
    }
}
```

# 201803-3 URL映射

```cpp
//freopen("D:\\input.txt","r",stdin);
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

vector<pair<regex,string>> vec1;
vector<pair<regex,string>> trans; //把<int>换成[\\d]+
int n,m;

void init(){
    trans={pair<regex,string>(regex("<int>"),"([\\d]+)"),
        pair<regex,string>(regex("<str>"),"([^/]+)"),
        pair<regex,string>(regex("<path>"),"([^\\s]+)")};
    /*trans={pair<regex,string>(regex("<int>"),"([0-9]+)"),
        pair<regex,string>(regex("<str>"),"([^/]+)"),
        pair<regex,string>(regex("<path>"),"(.+)")};*/
}

bool isDigitStr(string& str){
    for(int i=0;i<str.size();i++){
        if(!isdigit(str[i]))
            return false;
    }
    return true;
}
int main(){
    init();
    //freopen("D:\\input.txt","r",stdin);
    scanf("%d %d",&n,&m);
    string str1,str2; //输入的两个字符串
    for(int i=0;i<n;i++){
        cin>>str1>>str2;
        for(int j=0;j<trans.size();j++){
            //regex_replace(str1,trans[i].first,trans[i].second);
            str1=regex_replace(str1,trans[j].first,trans[j].second); //error:trans[i]
        }
        //cout<<str1<<endl;//////
        vec1.push_back(pair<regex,string>(regex(str1),str2));
    }
    while(m--){
        cin>>str1;
        smatch sm;
        for(int i=0;i<n;i++){
            if(regex_match(str1,sm,vec1[i].first)){
                printf("%s",vec1[i].second.c_str());
                for(int j=1;j<sm.size();j++){
                    string res=sm[j];
                    if(res!="" && isDigitStr(res)){ //error:
                        int a;
                        sscanf(res.c_str(),"%d",&a);
                        printf(" %d",a);
                    }else{
                        printf(" %s",res.c_str());
                    }
                }
                printf("\n");
                goto loop;
            }
        }
        printf("404\n");
        loop:;
    }
}
```

# 201803-4 棋局评估

```cpp
//freopen("D:\\input.txt","r",stdin);
/*#include<stdio.h>
#include<stdlib.h>
#include<string.h>*/
#include<bits/stdc++.h>
using namespace std;

int chess[4][4];
int T;

bool isWin(int d){ //玩家d是否赢了
    if(chess[1][1]==d+1 && chess[2][1]==d+1 && chess[3][1]==d+1)
        return true;
    else if(chess[1][2]==d+1 && chess[2][2]==d+1 && chess[3][2]==d+1)
        return true;
    else if(chess[1][3]==d+1 && chess[2][3]==d+1 && chess[3][3]==d+1)
        return true;
    else if(chess[1][1]==d+1 && chess[1][2]==d+1 && chess[1][3]==d+1)
        return true;
    else if(chess[2][1]==d+1 && chess[2][2]==d+1 && chess[2][3]==d+1)
        return true;
    else if(chess[3][1]==d+1 && chess[3][2]==d+1 && chess[3][3]==d+1)
        return true;
    else if(chess[1][1]==d+1 && chess[2][2]==d+1 && chess[3][3]==d+1)
        return true;
    else if(chess[1][3]==d+1 && chess[2][2]==d+1 && chess[3][1]==d+1)
        return true;
    else
        return false;
}
int getScore(int d){ //d赢了之后的得分
    int a=0;
    for(int i=1;i<4;i++){
        for(int j=1;j<4;j++){
            if(chess[i][j]==0)
                a++;
        }
    }
    if(d==0)
        return a+1;
    else
        return -a-1;
}
int dfs(int d){ //玩家d决定在x,y处下棋,前提是对方还没有赢
    if(isWin(d^1)){
        int a=d^1;
        return getScore(a);
    }
    int max0=-9999,max1=9999;
    for(int i=1;i<=3;i++){
        for(int j=1;j<=3;j++){
            if(chess[i][j]==0){
                chess[i][j]=d+1;
                if(d==0)max0=max(max0,dfs(d^1));//
                else max1=min(max1,dfs(d^1));
                chess[i][j]=0;
            }
        }
    }
    if(d==0){
        if(max0==-9999)return 0;
        else return max0;
    }else{
        if(max1==9999)return 0;
        else return max1;
    }
}
void init(){
    memset(chess,0,sizeof(chess));
}
int main(){
    //freopen("D:\\input.txt","r",stdin);
    cin>>T;
    while(T--){
        init();
        int a;
        for(int i=1;i<=3;i++){
            for(int j=1;j<=3;j++){
                cin>>a;
                chess[i][j]=a;
            }
        }
        if(isWin(0)){
            printf("%d\n",getScore(0));
        }else{
            int ans = dfs(0);
            printf("%d\n",ans);
        }
    }
}
```

这是一种新题，利用极大极小对抗的方法来做。

2. 