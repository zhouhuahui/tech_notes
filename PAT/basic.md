# 二叉树

## 二叉树的基本操作

**定义二叉树**

```cpp
struct Node{
	int data;
    Node* lchild;
    Node* rchild;
    int layer;
    Node(int v){
        data = v;
        lchild = rchild = nullptr;
    }
}
```

**二叉树结点的插入**

```cpp
//基于二叉树的性质的插入方法
void insert(Node* &root, int x){
    if(root == nullptr){
        root = new Node(x);
        return;
    }
    if(由于二叉树的性质，应该插入在左子树){
        insert(root->lchild,x);
    }else{
        insert(root->rchild,x);
    }
}
```

**二叉树的建立**

```cpp
Node* Create(int data[],int n){
    Node* root = nullptr;
    for(int i=0;i<n;++i){
        insert(root,data[i]);
    }
    return root;
}
```

**先序遍历**

```cpp
//递归写法
void Preorder(Node* root){
	if(root == nullptr)
        return;
    printf("%d\n",root->data); //访问结点
    preorder(root->lchild);
    preorder(root->rchild);
}
```

**层序遍历**

```cpp
void Layerorder(Node* root){
    queue<Node*> q;
    root->layer = 0;
    q.push(root); //初始化队列
    while(!q.empty()){
        Node* now = q.front();
        q.pop();
        printf("%d",now->data); //访问元素
        if(now->lchild){
            now->lchild->layer = now->layer+1;
            q.push(now->lchild);
        }
        if(now->rchild){
            now->rchild->layer = now->layer+1;
            q.push(now->rchild);
        } 
    }
}
```

```cpp

```

## 二叉查找树

**寻找root的后继结点**

```cpp
Node* Findnext(Node* root){
    while(root->rchild != nullptr)
        root = root->rchild;
    return root;
}
```

**寻找root的前驱节点**

```cpp
Node* Findpre(Node* root){
    while(root->lchild != nullptr)
        root = root->lchild;
    return root;
}
```

**删除以root为根的树中权值为x的结点**

```cpp
//通过一个一个地移动结点来删除结点
void DeleteNode(Node* &root,int x){
    if(root == nullptr)
        return; //不存在权值为x地结点
    if(root->data == x){
        //找到了欲删除的结点
        if(root->lchild == nullptr && root->rchild == nullptr){
            delete root;
            root = nullptr;
        }else if(root->lchildd != nullptr){
            Node* pre = Findpre(root->lchild); //找root前驱
            root->data = pre->data; //用前驱覆盖root
            DeleteNode(root->lchild,pre->data);
        }else{
            Node* next = Findnext(root->rchild); //找root后继
            root->data = next->data;
            DeleteNode(root->rchild,next->data);
        }
    }else if(root->data > x){
        DeleteNode(root->lchild,x);
    }else{
        DeleteNode(root->rchild,x);
    }
}
```

## AVL树

**AVL树的结点**

```cpp
struct Node{
    int v, height; //权值，高度
    Node* lchild, *rchild;
    Node(int vv){
        v = vv;
        height = 1;
        lchild = rchild = nullptr;
    }
}
```

**得到树的高度**

```cpp
int getHeight(Node* root){
    if(root == nullptr) return 0;
    return root->height;
}
```

**得到树的平衡因子**

```cpp
int getBalanceFactor(Node* root){
	//左子树的高度减去右子树的高度
	return getHeight(root->lchild)-getHeight(root->rchild);
}
```

**更新树的高度**

```cpp
void updateHeight(Node* root){
    //更新root的高度
    root->height = max(getHeight(root->lchild),getHeight(root->rchild))+1;
}
```

这个函数在后面的左旋，右旋和插入操作中要用到。

**AVL树的左旋右旋操作**

```cpp
void L(Node* &root){
    Node* temp = root->rchild; //temp是root的右子节点
    root->rchild = temp->lchild; //temp的左子节点变为root的右子节点
    temp->lchild = root; //root变为temp的左子结点
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}
void R(Node* &root){
    Node* temp = root->lchild; //temp是root的右子节点
    root->lchild = temp->rchild; //temp的左子节点变为root的右子节点
    temp->rchild = root; //root变为temp的左子结点
    updateHeight(root);
    updateHeight(temp);
    root = temp;
}
```

**AVL树的插入操作**

只要把最靠近插入节点的失衡结点调整到正常，就好了。

```cpp
void insert(Node* &root, int v){
    if(root == nullptr){
        root = new Node(v);
        return;
    }
    if(v < root->v){ //v比根节点的权值小
        insert(root->lchild,v);
        updateHeight(root);
        if(getBalanceFactor(root) == 2){ //插入结点后有子树失衡
            if(getBalanceFactor(root->lchild)==1){ //LL型
                R(root);
            }else if(getBalanceFactor(root->lchild)==-1){ //LR型
                L(root->lchild);
                R(root);
            }
        }
    }else{ //v比根节点的权值大
        insert(root->rchild,v);
        updateHeight(root);
        if(getBalanceFactor(root) == -2){ //插入结点后有子树失衡
            if(getBalanceFactor(root->lchild)==-1){ //RR型
                L(root);
            }else if(getBalanceFactor(root->lchild)==1){ //RL型
                R(root->lchild);
                L(root);
            }
        }
    }
}
```

## 堆

为了方便计算子节点在数组中的位置，第一个元素的下标为1

```cpp
const int maxn = 100;
//heap为堆，n为元素个数
int heap[maxn],n=10;
```

**向下调整**

```cpp
void downAdjust(int low,int high){
    int i= low, j = i*2; //i为欲调整结点，j为其左儿子结点
    while(j <= high){ //假如存在孩子结点
        if(j+1 <= high && heap[j+1]>heap[j]){ //如果右孩子存在
            j = j+1; //j存放右孩子下标
        }
        if(heap[j] > heap[i]){ //如果孩子中最大的权值比欲调整结点大
            swap(heap[j],heap[i]);
            i = j;
            j = i*2;
		}
        else{
            break; //孩子的权值均比欲调整结点小，算法结束
        }
    }
}
```

downAdjust算法用于创建堆

```cpp
void createHeap(){
    for(int i=n/2;i>=1;i--){
        downAdjust(i,n);
    }
}
```

**删除堆顶元素**

```cpp
void deleteTop(){
    heap[1] = heap[n--]; //用最后一个元素覆盖堆顶元素，并让元素个数减1
    downAdjust(1,n);
}
```

**向堆中添加一个元素**

```cpp
//low一般为1，high为欲调整结点的数组下标
void upAdjust(int low,int high){
    int i = high, j = i/2; //i为欲调整结点，j为i的父亲
    while(j >= low){ //父亲在[low,high]范围内
        if(heap[j] < heap[i]){ //父亲权值小于欲调整结点i的权值
            swap(heap[j],heap[i]);
            i = j;
            j = i/2;
        }else{
            break; //父亲权值比欲调整结点的权值大，结束
        }
    }
}
```

**堆排序**

```cpp
void heapSort(){
    createHeap(); //建堆
    for(int i=n;i>1;i--){
        swap(heap[i],heap[1]);
        downAdjust(1,i-1);
    }
}
```

## 哈夫曼树

如何得到最终的树的带权路径长度

```cpp
#include<cstdio>
#include <queue>
using namespace std;

//小顶堆的优先队列
priority_queue<long long, vector<long long>, greater<long long>> q;

int main(){
    for(int i=0;i<)
}
```

# 图

```cpp
struct Node{
    int v,w;
    int layer;
    Node(vv,ww){v=vv;w=ww};
}

const int MAXV = 1000;
const int INF = 10000000;
```

## 邻接表表示法的BFS与DFS

```cpp
vector<Node> adj[MAXV];
bool vis[MAXV] = {false};

void DFS(int u, int depth){ //u为当前访问结点的编号；depth为深度，初始为0
    vis[u] = true;
    for(int i=0;i<adj[u].size();i++){
        int v = adj[u].at(i);
        if(vis[v] == false){
            DFS(v,depth+1);
        }
    }
}

bool inq[MAXV] = {false};
void BFS(int u){
    queue<Node> q;
    Node* start;
    start.v = u; //起始结点编号
    start.layer = 0; //起始结点所在层数
    q.push(start); //将起始结点压入队列
    inq[start.v] = true; //设为已加入队列
    while(!q.empty()){
        Node topNode = q.front();
        q.pop();
        int u = topNode.v;
        for(int i=0;i<adj[u].size();i++){
            Node next = adj[u].at(i);
            next.layer = topNode.layer + 1;
            if(inq[next.v] == false){
                q.push(next); //将next入队
                inq[next.v] == true;
            }
        }
    }
    
}

```

## 最短路径

**dijkstra算法**

```cpp
const int MAXV;
const int INF;

int d[MAXV]; //记录到结点的最近距离
int vis[MAXV] = {false};
int G[MAXV][MAXV] = {INF};
```

用pre数组保存路径，然后用DFS来遍历所有的路径，这种Dijkstra+DFS的做法可以编程复杂的情况。

```cpp
if(d[u]+G[u][v] < d[v]){
    d[v] = d[u] + G[u][v];
    pre[v].clear();
    pre[v].push_back(u);
}
if(d[u]+G[u][v] == d[v]){
    pre[v].push_back(u);
}
```

此时pre数组可以产生一个递归树，所有叶子结点到根节点的路径确定了从起点到终点的所有最短路径

```cpp
int optValue; //第二标尺最优值
vector<int> pre[MAXV]; //前驱数组
vector<int> path,tempPath; //最优路径，临时路径
void DFS(int v){ //v为当前访问结点
    if(v == st){ //如果到达了叶子结点st（即路径的起点）
        tempPath.push_back(v); //将st加入临时路径的最后面
        int value; //存放临时路径tempPath的第二标尺值
        计算路径上tempPath上的value值;
        if(value 优于 optValue){
            optValue = value; //更新第二标尺值与最优路径
            path = tempPath;
        }
        tempPath.pop_back(); //返回上一层时，将这一节点从pre中删除
        return;
    }
    //递归式
    tempPath.push_back(v); //将当前访问结点加入临时路径的最后面
    for(int i=0;i<pre[v].size();i++){
        DFS(pre[v][i]);
    }
    tempPath.pop_back(); //返回上一层时，将这一节点从pre中删除
}
```



