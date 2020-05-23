# A1060 Are They Equal(字符串处理)

```cpp
/*
基本思路：先判断数字等于1.0还是大于1.0还是小于1.0。若大于1.0，则先去掉前导0（考虑到02.02这种
情况），再记录小数点前面有多少数字，则为指数，同时把数字一个一个输出直到达到significant digit的
数量；若小于1.0，则找到小数点后面的位置，记录小数点后面第一个非0数前面有多少个0，则为-exp，然后把后面
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

```
基本思路2：
1).输入数字到str中,如str="02.002"
2).按照'.'分割，到strs中。
3)若strs.size()==1:则先去掉前导0，剩下的数字的个数即为指数，剩下的数字一次输出到significant，
      不够3位就补0
  若strs.size()==2:则先去掉strs[0]的前导0，若不为空：则剩下的个数为指数，否则：strs[1]的前导0的个数
      即为指数的负数。若strs[0]非空，则从strs[0]和strs[1]中输出到significant，否则，从strs[1]
      的非0数字开始输出到significant
```

# A1061 Dating（字符串处理）

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

char input[4][65];
int weekDay,hour,minute;
string weekdays[8]={"","MON","TUE","WED","THU","FRI","SAT","SUN"};

int main(){
	//freopen("D:\\input.txt","r",stdin);
	for(int i=0;i<4;i++){
		fgets(input[i],65,stdin);
	}
	int siz=min(strlen(input[0]),strlen(input[1])),i;
	for(i=0;i<siz;i++){
		//if(isupper(input[0][i]) && input[1][i]==input[0][i]){
		if(input[0][i]>='A'&&input[0][i]<='G'&&input[0][i]==input[1][i]){
			weekDay=input[0][i]-'A'+1;
			break;
		}
	}
	i++;
	for(;i<siz;i++){
		if(input[1][i]==input[0][i]){ //error:if(isupper(input[0][i]) && input[1][i]==input[0][i])
			if(isdigit(input[0][i])){
				hour=input[0][i]-'0';
			}else if(input[0][i]>='A' && input[0][i]<='N'){
				hour=input[0][i]-'A'+10;
			}else{
				continue;
			}
			break;
		}
	}
	siz=min(strlen(input[2]),strlen(input[3]));
	for(i=0;i<siz;i++){
		if(input[2][i]==input[3][i] && isalpha(input[2][i])){
			break;
		}
	}
	minute=i;
	printf("%s %02d:%02d",weekdays[weekDay].c_str(),hour,minute);
	return 0;
}
```

这题有些细节：比如17行和25行注释中的写法是错误的。

# A1062 Talent and Virtue（排序）

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

# A1063 Set Similarity(双指针法)

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

# A1064 Complete Binary Search Tree(难)

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

# A1067 Sort With Swap(0,1)(难)

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

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int pos[MAXN];
int n;
int cnt=0;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	for(int i=0;i<n;i++){
		int a;
		scanf("%d",&a);
		pos[a]=i;
	}
	for(int i=0;i<n;i++){
		while(i!=pos[i]){
			while(pos[0]!=0){
				swap(pos[0],pos[pos[0]]);
				cnt++;
			}
			if(i!=pos[i]){
				swap(pos[0],pos[i]);
				cnt++;
			}
		}
	}
	cout<<cnt;
	return 0;
}
```

我曾经想过先执行20到23行的代码，再判断数组是否完全排序，若未完全排序，则交换0和任意一个未排序的元素，继续进行交换，否则，程序结束。但这种想法会慢，因为有很多次要遍历整个数组，判断是否排序。由于这个题有个特点是，一旦到达正确位置的元素（除了0）不会再交换，所以可以在外面加一个外层for循环，指示可能的未排序元素。

# A1068 Find More Coins(0/1背包，难)

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

```
用DFS+剪纸的方法
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

int n,m;
int value[MAXN];

int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n>>m;
	int i;
	for(i=0;i<n;i++){
		scanf("%d",&value[i]);
	}
	sort(value,value+n);
	i=0;
	vector<int> path;
	while(i<n){
		path.push_back(i);
		m-=value[i];
		if(m==0){
			break;
		}else if(m<0){
			do{
				i=path.back();
				path.pop_back();
				m+=value[i];
				i++;
			}while(i==n && !path.empty());
		}else{
			i++;
			while(i==n && !path.empty()){
				i=path.back();
				path.pop_back();
				m+=value[i];
				i++;
			}
		}
	}
	if(m==0){
		printf("%d",value[path[0]]);
		for(int i=1;i<path.size();i++){
			printf(" %d",value[path[i]]);
		}
	}else{
		printf("No Solution");
	}
}
```

29分。有一个超时了

注意：迭代法的DFS很难写

# A1069 The Black Hole of Numbers

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int num;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>num;
	string s1,s2;
	do{ //error:考虑到第一次num=0或num=6174的情况 
		s1=to_string(num);
		//error:要考虑num=12的情况，即没有达到四位
		int siz=s1.size(); //error:由于循环要改变s1的size，所以要把s1.size()保存一下 
		for(int i=0;i<4-siz;i++){
			s1.insert(s1.begin(),'0');
		}
		sort(s1.begin(),s1.end());
		s2=s1;
		sort(s1.begin(),s1.end(),greater<int>());
		num=stoi(s1)-stoi(s2);
		printf("%s - %s = %04d\n",s1.c_str(),s2.c_str(),num);
	}while(num!=6174 && num!=0);
}
```

有好多细节错误。

# A1070 Mooncake

```
简单的贪心问题
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005

struct Cake{
	double tons;
	double totalPrice;
	double price;
}; 
Cake cakes[MAXN];
int n;
double d;
bool cmp(const Cake&c1,const Cake&c2){
	return c1.price>c2.price;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n>>d;
	for(int i=0;i<n;i++){
		scanf("%lf",&cakes[i].tons);
	}
	for(int i=0;i<n;i++){
		scanf("%lf",&cakes[i].totalPrice);
		cakes[i].price=cakes[i].totalPrice/cakes[i].tons;
	}
	sort(cakes,cakes+n,cmp);
	double ans=0.0;
	for(int i=0;i<n&&d>0.0;i++){ //error:d>0.0
		if(d>=cakes[i].tons){
			ans+=cakes[i].totalPrice;
			d-=cakes[i].tons;
		}else{
			ans+=cakes[i].price*d;
			d=0.0;
		}
	}
	printf("%.2f",ans);
}
```

注意：必须用double类型存储月饼的数量。原因可能是，输入的数量可能超过int的限制，因为题目中只说d<500,而并没有说月饼的数量的大小限制。

# A1071 Speech Pattern

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXNUM  1048577

char line[MAXNUM];
string input;
map<string,int> mp;

bool isAN(char c){
	return isalpha(c) || isdigit(c);
} 
int main(){
	//freopen("D:\\input.txt","r",stdin);
	fgets(line,MAXNUM,stdin);
	input=line;
	int i=0,j,siz=strlen(line);
	string maxLenStr;
	int maxLen=-1;
	while(i<siz){
		while(i<siz && !isAN(input[i]))i++; //error:if(i<siz && !isAN(input[i]))
		if(i>=siz)break;
		//提取单词 
		j=i;
		while(j<siz && isAN(input[j]))j++;
		string s=input.substr(i,j-i);
		//变为小写的
		for(char&c : s)c=tolower(c); 
		map<string,int>::iterator it;
		if((it=mp.find(s))!=mp.end()){
			it->second++;
		}else {
			//error:it->second=1;
			mp[s]=1;
			it=mp.find(s);
		}
		//比较 
		if(it->second>maxLen){
			maxLen=it->second;
			maxLenStr=s;
		}else if(it->second==maxLen && s<maxLenStr){
			maxLenStr=s;
		}
		//更新计数
		i=j; 
	}
	printf("%s %d",maxLenStr.c_str(),maxLen);
}
```

错误分析：

1. 语法错误：34行，不能通过``it=mp.find(s); it->second=1``的形式向map中插入键值对。
2. 一些细节错误

# A1072 Gas Station（难）

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005 //error: define MAXN 1000 我把N<=1000看成N<1000 
#define MAXM 15

struct Edge{
	int u,v;
	int d;
	Edge(int _u,int _v,int _d){
		u=_u;
		v=_v;
		d=_d;
	}
};
vector<Edge> Adj[MAXN+MAXM];
int n,m,k;
int Dist;

int dist[MAXN+MAXM],mind,sumd;
double avgd;
bool inq[MAXN+MAXM];
void initSPFA(){
	memset(dist,0x3f,sizeof(dist));
	mind=INT_MAX;
	sumd=0;
	avgd=0.0;
	for(int i=0;i<MAXN+MAXM;i++)inq[i]=false;
}
//若满足所有房子都在服务范围内，则返回true 
bool SPFA(int s){ 
	dist[s]=0;
	queue<int> q;
	q.push(s); 
	inq[s]=true;
	while(!q.empty()){
		int u=q.front(); q.pop();
		inq[u]=false;
		for(int i=0;i<Adj[u].size();i++){
			int v=Adj[u][i].v;
			int d=Adj[u][i].d;
			if(dist[u]+d<dist[v]){ //error: if(dist[s]
				dist[v]=dist[u]+d;
				if(!inq[v]){
					q.push(v); 
					inq[v]=true;
				}
			}
		}
	} 
	for(int i=1;i<=n;i++){
		if(dist[i]>Dist)return false;
		if(dist[i]<mind)
			mind=dist[i];
		sumd+=dist[i]; //error: 更新sumd时，时机错了，不应该在上面的if里面出现 
	}
	avgd=(double)sumd/n;
	return true;
}
int convert(string&s){
	if(!isalpha(s[0]))return stoi(s);
	else return stoi(s.substr(1))+n;
}
string convert2(int s){
	if(s<=n)return to_string(s);
	else return string("G")+to_string(s-n);
}
double round(double x){
	return (x*10+0.5)/10;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	//输入
	scanf("%d %d %d %d",&n,&m,&k,&Dist);
	string u,v;
	int d;
	for(int i=1;i<=k;i++){
		cin>>u>>v>>d;
		Adj[convert(u)].push_back(Edge(convert(u),convert(v),d));
		Adj[convert(v)].push_back(Edge(convert(v),convert(u),d));
	}
	
	int minDist=-1;
	double avgDist=DBL_MAX;
	int gasStation;
	for(int i=n+1;i<=n+m;i++){
		initSPFA();
		if(!SPFA(i))continue;
		//比较 
		if(mind>minDist){
			minDist=mind;
			avgDist=avgd;
			gasStation=i;
		}else if(mind==minDist){
			if(avgd<avgDist){
				avgDist=avgd;
				gasStation=i;
			}else if(avgd==avgDist && i<gasStation){
				gasStation=i;
			}
		}
	}
	if(minDist!=-1)
		printf("%s\n%.1f %.1f",convert2(gasStation).c_str(),(double)minDist,avgDist);
	else 
		printf("No Solution");
}
```

错误分析：

1. 细节错误：5，43，56。特别是第5行的，不管怎样，都要尽可能地取较大的MAXN

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

struct Node{
    int v, d;
};
class Solution{
public:
    int n,m,k,ds;
    vector<vector<Node>> graph;
    void spfa(int s, vector<int>& dis){
        dis = vector<int>(n+m+1, INT_MAX);
        dis[s] = 0;
        vector<bool> vis(n+m+1, false);
        queue<int> que;
        que.push(s);
        vis[s] = true;
        while(!que.empty()){
            s = que.front();
            que.pop();
            vis[s] = false;
            for(auto i:graph[s]){
                if(dis[s]+i.d<dis[i.v]){
                    dis[i.v] = dis[s]+i.d;
                    if(!vis[i.v]){
                        que.push(i.v);
                        vis[i.v] = true;
                    }
                }
            }
        }
    }
public:
    void createGraph(){
        cin>>n>>m>>k>>ds;
        graph = vector<vector<Node>>(n+m+1);
        while(k--){
            string loc1, loc2;
            int u, v, d;
            cin>>loc1>>loc2>>d;
            u = (loc1[0]=='G'?stoi(loc1.substr(1))+n:stoi(loc1));
            v = (loc2[0]=='G'?stoi(loc2.substr(1))+n:stoi(loc2));
            graph[u].push_back({v,d});
            graph[v].push_back({u,d});
        }
    }
    pair<int,pair<int,double>> findBestGasStation(){
        pair<int,pair<int,double>> p={-1,{0,0.0}};
        for(int i=n+1;i<=n+m;i++){
            vector<int> dis;
            spfa(i, dis);
            int mindis=INT_MAX;
            int sumdis=0;
            double avgdis;
            for(int j=1;j<=n;j++){
                if(dis[j]>ds){
                    mindis = INT_MAX;
                    break;
                }
                sumdis+=dis[j];
                mindis = min(mindis, dis[j]);
            }
            if(mindis != INT_MAX){
                avgdis = (double)sumdis/n;
                if(p.first == -1 || mindis>p.second.first || (mindis==p.second.first&&avgdis<p.second.second) || (mindis==p.second.first&&avgdis==p.second.second&&i<p.first)){
                    p.first = i;
                    p.second.first = mindis;
                    p.second.second = avgdis;
                }
            }
        }
        return p;
    }
};
int main(){
    //freopen("D:\\input.txt","r",stdin);
    Solution sol;
    sol.createGraph();
    pair<int,pair<int,double>> p = sol.findBestGasStation();
    if(p.first == -1){
        cout<<"No Solution"<<endl;
    }else{
        cout<<"G"<<p.first-sol.n<<endl;
        printf("%.1f %.1f",(double)p.second.first, p.second.second);
    }
}

```

# A1073 Scientific Notation(难)

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

```cpp
//freopen("D:\\input.txt","r",stdin);
#include<bits/stdc++.h>
using namespace std;

class Solution{
public:
    string scient;
    void input(){
        cin>>scient;
    }
    string convert(){
        string part1;
        int part2;
        part1 = scient.substr(0,scient.find('E'));
        if(part1[0]=='+') part1.erase(part1.begin());
        part2 = stoi(scient.substr(scient.find('E')+1));
        if(part2 == 0){
            return part1;
        }else if(part2 < 0){
            string res;
            if(part1[0] == '-'){
                res+='-';
            }
            for(int i=0;i<-part2;i++){
                if(i==0) res+="0.";
                else{
                    res+="0";
                }
            }
            //res+="1";  error
            res+=(part1[0]=='-'?part1[1]:part1[0]);
            res+=part1.substr(part1.find('.')+1);
            return res;
        }else{
            string res;
            if(part1[0] == '-'){
                res+='-';
            }
            //res+="1";
            res+=(part1[0]=='-'?part1[1]:part1[0]);
            string s1 = part1.substr(part1.find('.')+1);
            res+=s1.substr(0,min((int)(s1.size()),part2));
            if(part2 < s1.size()){
                res+=".";
                res+=s1.substr(part2);
            }else if(part2 > s1.size()){
                int cnt = part2-s1.size();
                while(cnt--){
                    res+="0";
                }
            }
            return res;
        }
    }
};

int main(){
    //freopen("D:\\input.txt","r",stdin);
    Solution sol;
    sol.input();
    cout<<sol.convert();
}

```

# *A1074 Reversing Linked List

```
基本思路：由于是静态链表，所以可以把元素有序存放在数组中，在进行操作，这样方便多了。
```

# *A1075 PAT Judge

```
就是基本的排序，排序规则是<总分，full mark个数，id>
```

# A1076 Forwards on Weibo(难)

```
这题有两个难点：
1. 要想清楚为什么用BFS比较好，而不是用DFS（用DFS也可以，不过稍微负载，而且时间复杂度高）
2. 对题意的理解，如果不是看解析，我对题目的理解是有偏差的，我认为only L levels of indirect followers 
are counted.的意思是第一层是direct follower，而第2,3...L+1层是indirect followers。但是题目并
没有这么复杂，只是考虑第1,2...L层就好了，所以我不知道它的indirect到底什么意思。
```

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

# A1077 Kuchiguse

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int n;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	scanf("%d\n",&n);
	char input[300];
	char suffix[300];
	for(int i=0;i<n;i++){
		fgets(input,300,stdin);
		input[strlen(input)-1]=(input[strlen(input)-1]=='\n'?'\0':input[strlen(input)-1]);
		if(i==0){
			strcpy(suffix,input);
		}else{
			int i=strlen(input)-1,j=strlen(suffix)-1;
			while(i>=0 && j>=0){
				if(input[i]==suffix[j]){
					i--;
					j--;
				}else{
					break;
				}
			}
			if(j>=0)j++;
			else j=0;
			char temp[300];
			int k=0;
			for(;j<=strlen(suffix);j++)temp[k++]=suffix[j];
			strcpy(suffix,temp);
		}
	}
	if(strcmp(suffix,"")==0)printf("nai");
	else printf("%s",suffix);
}
```

# A1078 Hashing(难)

```
如果不知道quadratic probing的话就麻烦了，根本不知道怎么做。
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;

int m,n; //用户定义table大小，插入的个数
int* table;

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
	scanf("%d %d",&m,&n);
	while(!isPrime(m)){
		m++;
	}
	table = (int*)malloc(sizeof(int)*m);
	for(int i=0;i<m;i++)table[i]=-1; //表示为空 
	bool first=true;
	while(n--){
		int num;
		scanf("%d",&num);
		int h=num%m;
		if(table[h]==-1){
			table[h]=num;
			if(first)printf("%d",h),first=false;
			else printf(" %d",h);
		}
		else{
			int h2;
			for(int i=1;(h2=(h+i*i)%m)!=h;i++){ //error:i*i != i^2
				if(table[h2]==-1){
					//printf("\nh=%d i=%d h2=%d\n",h,i,h2);//////
					table[h2]=num;
					printf(" %d",h2);
					goto loop;
				}
			}
			printf(" -");
		}
		loop:;
	}
	free(table);
} 
```

错误分析：

1. 语法错误：37行，不要用``i^2``来计算i的平方

# A1079 Total Sales of Supply Chain

```
bfs+树
```

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 100005

struct Chain{
	double p;
	double amount;
};
Chain chains[MAXN];
vector<int> Adj[MAXN];
bool isRoot[MAXN];
int n;
double unit_p,rate;

void init(){
	for(int i=0;i<MAXN;i++)isRoot[i]=true;
}

double sales=0.0;
void bfs(int s){
	chains[s].p=unit_p;
	queue<int> q;
	q.push(s);
	while(!q.empty()){
		int u=q.front();
		q.pop();
		if(Adj[u].empty()){
			sales+=chains[u].p*chains[u].amount;
		}
		for(int i=0;i<Adj[u].size();i++){
			int v=Adj[u][i];
			chains[v].p=chains[u].p*(100.0+rate)/100;
			q.push(v);
		}
	}
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	scanf("%d %lf %lf",&n,&unit_p,&rate);
	for(int i=0;i<n;i++){
		int k,a;
		scanf("%d",&k);
		if(k==0)scanf("%lf",&chains[i].amount);
		else{
			while(k--){
				scanf("%d",&a);
				Adj[i].push_back(a);
				isRoot[a]=false;
			}
		}
	}
	for(int i=0;i<n;i++){
		if(!isRoot[i])continue;
		bfs(i);
		break;
	}
	printf("%.1lf",sales);
	return 0;
}
```

