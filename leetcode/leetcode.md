# 6.ZigZag Conversion

```cpp
/*
我的思路是：构建一个矩阵，没有字符的位置填充为空格。考虑4种情况(假如numRows==4):0; 1,2; 3; 4,5;
就是说行号可以整除(numRows-1)并且行号余(numRows-1)是偶数，行号不可以整除(numRows-1)并且行号余
(numRows-1)是偶数，行号可以整除(numRows-1)并且行号余(numRows-1)是奇数，行号不可以整除(numRows-1)
并且行号余(numRows-1)是奇数
*/; 
class Solution {
public:
    string convert(string s, int numRows) {
        if(numRows==1)return s;
        vector<char>* graph = new vector<char>[numRows];
        int row=0,col=0;
        int i=0;
        while(i<s.size()){
            if((i/(numRows-1))%2 == 0 && i%(numRows-1)==0){
                for(int j=0;j<numRows;j++){
                    if(j==row)graph[j].push_back(s[i]);
                    else graph[j].push_back(' ');
                }
                row++;
            }
            else if((i/(numRows-1))%2 == 0){
                graph[row][col] = s[i];
                row++;
            }else if(i%(numRows-1)==0 && (i/(numRows-1))%2==1){
                graph[row][col] = s[i];
                row--;
                col++;
            }else{
                for(int j=0;j<numRows;j++){
                    if(j==row)graph[j].push_back(s[i]);
                    else graph[j].push_back(' ');
                }
                row--;
                col++;
            }
            i++;
        }
        string res;
        for(int i=0;i<numRows;i++){
            for(int j=0;j<graph[i].size();j++){
                if(graph[i][j]==' ')continue;
                else res.insert(res.end(),graph[i][j]);
            }
        }
        return res;
    }
};
```

```cpp
/*
思路2：设有两种state, 分别为up和down，初始为down. 考虑在两种不同的state下，如何更新行号，在何时
变更状态。与思路1的不同是，在一个vector中不存在a  b   c的情况，只有abc.
*/
```

# 62. Unique Paths(dp)

```cpp
//overflow代码
class Solution {
public:
    int uniquePaths(int m, int n) {
        long long res = 1;
        for(int i=m+n-2; i>=m; i--){
            res *= i;
        }
        for(int i=n-1; i>=1; i--){
            res /= i;
        }
        return res;
    }
};
```

```cpp
//TLE 代码
class Solution {
public:
    int go(int m, int n, int i, int j){
        if(i==m-1 || j==n-1){
            return 1;
        }else{
            return go(m,n,i+1,j) + go(m,n,i,j+1);
        }
    }
    int uniquePaths(int m, int n) {
        return go(m,n,0,0);
    }
};
```

```cpp
class Solution {
public:
    int go(int m, int n, int i, int j, int* arr){
        if(i==m-1 || j==n-1){
            *(arr+i*n+j) = 1;
        }else{
            if(*(arr+(i+1)*n+j)==0){
                *(arr+(i+1)*n+j) = go(m,n,i+1,j,arr);
            }
            if(*(arr+i*n+j+1)==0){
                *(arr+i*n+j+1) = go(m,n,i,j+1,arr);
            }
            *(arr+i*n+j) = *(arr+(i+1)*n+j) + *(arr+i*n+j+1);
        }
        return *(arr+i*n+j);
    }
    int uniquePaths(int m, int n) {
        int* arr = (int*)malloc(m*n*sizeof(int));
        for(int i=0;i<m;i++){
            for(int j=0;j<n;j++){
                *(arr+i*n+j) = 0;
            }
        }
        return go(m,n,0,0,arr);
    }
};
```



# 90. Subsets2(dfs)

```cpp
class Solution {
public:
    void dfs(int num, vector<pair<int,int>>&vp, int i, vector<int>&item, vector<vector<int>>&res){
        if(num==0){
            res.push_back(item);
            return;
        }
        if(i==vp.size()){
            return;
        }
        for(int k=0;k<=min(vp[i].second,num);k++){ //error: for(int k=0;k<min(vp[i].second,num);k++)
            for(int j=0;j<k;j++)item.push_back(vp[i].first);
            dfs(num-k,vp,i+1,item,res);
            for(int j=0;j<k;j++)item.pop_back();
        }
    }
    vector<vector<int>> subsetsWithDup(vector<int>& nums) {
        vector<pair<int,int>> vp; //保存<value,cnt>对
        sort(nums.begin(),nums.end());
        for(int i=0;i<nums.size();){
            int cnt=0,j;
            for(j=i;j<nums.size()&&nums[j]==nums[i];j++)
                cnt++;
            vp.push_back({nums[i],cnt});
            i=j;
        }
        vector<int> item;
        vector<vector<int>> res;
        for(int cap=0;cap<=nums.size();cap++){ //cap是一个集合的容量
            								   //error: for(int cap=0;cap<nums.size();cap++)
            dfs(cap,vp,0,item,res);
        }
        return res;
    }
};
```



# 223. Rectangle Area

```cpp
class Solution {
public:
    int computeArea(int A, int B, int C, int D, int E, int F, int G, int H) {
        long long comx, comy;  
        comx = (min(C,G)<=max(A,E)?0:min(C,G)-max(A,E));
        comy = (min(D,H)<=max(B,F)?0:min(D,H)-max(B,F));
        long long areaABCD = (C-A)*(D-B);
        long long areaEFGH = (G-E)*(H-F);
        long long areaLap = comx*comy;
        long long area = areaABCD + areaEFGH - areaLap;
        return area;
    }
};

//error1: 使用long long
//error2: 注意第5行的计算，不能先算再比较，应该先比较再算
```

# 464. Can I Win(dp+dfs)

```cpp
class Solution {
public:
    int dfs(int player, int total, int desiredTotal, vector<int>& vis) {
        //若0获胜，则返回1, 若1获胜，则返回-1，否则，返回0
        //剪枝操作(如果可以直接找到一个数使得自己直接赢了，则直接选择这个数)
        for(int i=desiredTotal-total; i<vis.size(); i++){
            if(vis[i]==0)
                return (player==0?1:0);
        }
        //遍历可选择的数，去深搜
        int score0=-2, score1=2;
        for(int i=1;i<vis.size();i++){
            if(vis[i]==1) continue;
            total += i;
            vis[i] = 1;
            if(player == 0){
                score0 = max(score0, dfs((player==0?1:0), total, desiredTotal, vis));
            }else{
                score1 = min(score1, dfs((player==0?1:0), total, desiredTotal, vis));
            }
            total -= i;
            vis[i] = 0;
            //剪枝操作（如果找到一个数，使得自己在不久的将来以最优方式一定回赢，则选择这个数）
            if(player==0 && score0==1)
                return 1;
            if(player==1 && score1==-1)
                return -1;
        }
        if(player == 0){
            if(score0 == -2)
                return 0;
            else
                return score0;
        }else{
            if(score1 == 2)
                return 0;
            else 
                return score1;
        }
    }
    
    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        vector<int> vis(maxChoosableInteger+1, 0);
        return dfs(0, 0, desiredTotal, vis)==1;
    }
};
```

这个代码已经由很多剪枝操作了，但还是TLE，因为没有用dp

 taking a number the remaing choosable numbers will form a state and that is to be stored (result of that).. 

```cpp
class Solution {
public:
    //state: 状态（由maxChoosableInteger个0,1组成的二进制数）
    int dfs(int player, int total, int desiredTotal, vector<int>& vis, int state, vector<int>& dp) {
        //若0获胜，则返回1, 若1获胜，则返回-1，否则，返回0
        if(dp[state] != 0){
            return ((player==0&&dp[state]==1)?1:(player==0&&dp[state]==-1)?-1:(player==1&&dp[state]==1)?-1:1);
        }
        //剪枝操作(如果可以直接找到一个数使得自己直接赢了，则直接选择这个数)
        for(int i=desiredTotal-total; i<vis.size(); i++){
            if(vis[i]==0){
                dp[state] = 1; //处于这个状态的玩家必赢
                return (player==0?1:-1);
            }
        }
        //遍历可选择的数，去深搜
        int score0=-2, score1=2;
        for(int i=1;i<vis.size();i++){
            if(vis[i]==1) continue;
            total += i;
            vis[i] = 1;
            if(player == 0){
                score0 = max(score0, dfs((player==0?1:0), total, desiredTotal, vis, state|(1<<(i-1)), dp));
            }else{
                score1 = min(score1, dfs((player==0?1:0), total, desiredTotal, vis, state|(1<<(i-1)), dp));
            }
            total -= i;
            vis[i] = 0;
            //剪枝操作（如果找到一个数，使得自己在不久的将来以最优方式一定回赢，则选择这个数）
            if(player==0 && score0==1){
                dp[state]=1; //处于这个状态的玩家必赢
                return 1;
            }
            if(player==1 && score1==-1){
                dp[state]=1; //处于这个状态的玩家必赢
                return -1;
            }
        }
        if(player == 0){
            if(score0 == -2){
                return 0;
            }
            else{
                dp[state]=-1; //处于这个状态的玩家必输
                return score0; //这个时候score0必为-1
            }
        }else{
            if(score1 == 2)
                return 0;
            else{
                dp[state]=-1; //处于这个状态的玩家必输
                return score1; //这个时候score1必为1
            }
        }
    }

    bool canIWin(int maxChoosableInteger, int desiredTotal) {
        vector<int> vis(maxChoosableInteger+1, 0);
        vector<int> dp(1<<maxChoosableInteger, 0);
        return dfs(0, 0, desiredTotal, vis, 0, dp)==1;
    }
};
```

# 764. Largest Plus Sign(bfs || dp)

```cpp
//TLE代码
class Solution {
public:
    //从值为1的点进行BFS，对每一个点，都以它为中心，看能达到的plus sign的最大order是什么
    int orderOfLargestPlusSign(int N, vector<vector<int>>& mines) {
        vector<vector<int>> matrix(N, vector<int>(N,1)); //值为1的点被访问过后标记为2
        for(int i=0;i<mines.size();i++){
            matrix[mines[i][0]][mines[i][1]] = 0;
        }
        int order = 0;
        for(int i=0;i<N;i++){
            for(int j=0;j<N;j++){
                if(matrix[i][j]!=1) continue;
                queue<pair<int,int>> q;
                q.push({i,j});
                matrix[i][j] = 2;
                while(!q.empty()){
                    pair<int,int> p = q.front();
                    q.pop();
                    int order2;
                    for(order2=2;;order2++){
                        int x_left,x_right,y_up,y_down;
                        x_left = p.second-order2+1;
                        x_right = p.second+order2-1;
                        y_up = p.first-order2+1;
                        y_down = p.first+order2-1;
                        if(x_left>=0&&x_right<N&&y_up>=0&&y_down<N){
                            if(matrix[y_up][p.second]!=0&&matrix[y_down][p.second]!=0&&matrix[p.first][x_left]!=0&&matrix[p.first][x_right]!=0){
                                continue;
                            }else{
                                break;
                            }
                        }else{
                            break;
                        }
                    }
                    order2--;
                    order=max(order,order2);
                    //加入新的点
                    if(p.second-1>=0&&matrix[p.first][p.second-1]==1){
                        q.push({p.first,p.second-1});
                        matrix[p.first][p.second-1]=2;
                    }
                    if(p.second+1<N&&matrix[p.first][p.second+1]==1){
                        q.push({p.first,p.second+1});
                        matrix[p.first][p.second+1]=2;
                    }
                    if(p.first-1>=0&&matrix[p.first-1][p.second]==1){
                        q.push({p.first-1,p.second});
                        matrix[p.first-1][p.second]=2;
                    }
                    if(p.first+1<N&&matrix[p.first+1][p.second]==1){
                        q.push({p.first+1,p.second});
                        matrix[p.first+1][p.second]=2;
                    }
                }
            }
        }
        return order;
    }
};
```

```cpp
//计算每个点的左延申，右延申，上延申，下延申分别是多少，然后取它们的最小值即为order
//算是dp方法吧
class Solution {
public:
    int orderOfLargestPlusSign(int N, vector<vector<int>>& mines) {
        vector<vector<int>> matrix(N, vector<int>(N, 1));
        for(int i=0;i<mines.size();i++){
            matrix[mines[i][0]][mines[i][1]] = 0;
        }
        vector<vector<int>> left(N, vector<int>(N, 0));
        vector<vector<int>> right(N, vector<int>(N, 0));
        vector<vector<int>> up(N, vector<int>(N, 0));
        vector<vector<int>> down(N, vector<int>(N, 0));
        for(int i=0;i<N;i++){
            for(int j=0;j<N;j++){
                if(matrix[i][j]==1){
                    left[i][j]=1;
                    up[i][j]=1;
                    if(i>0) up[i][j]+=up[i-1][j];
                    if(j>0) left[i][j]+=left[i][j-1];
                }
            }
        }
        for(int i=N-1;i>=0;i--){
            for(int j=N-1;j>=0;j--){
                if(matrix[i][j]==1){
                    right[i][j]=1;
                    down[i][j]=1;
                    if(i<N-1) down[i][j]+=down[i+1][j];
                    if(j<N-1) right[i][j]+=right[i][j+1];
                }
            }
        }
        int order=0;
        for(int i=0;i<N;i++){
            for(int j=0;j<N;j++){
                order = max(order, min(min(left[i][j],right[i][j]),min(up[i][j],down[i][j])));
            }
        }
        return order;
    }
};
```



# 873. Length of Longest Fibonacci Subsequence(dp)

```cpp
//TLE代码：记录以A[i]结尾的所有可能的在A[i]前面的元素使得串长度大于等于2.当考虑A[i+1]时，就要对于
//j<i+1, 找A[j]的前元素又没有等于A[i+1]-A[j]的，若有，则记录A[j]和串长度，否则，记录,A[j]和2
class Solution {
public:
    int lenLongestFibSubseq(vector<int>& A) {
        vector<unordered_map<int,int>> links(A.size()); //记录A[i]前面的元素，以及在这个情况下，以A[i]结尾  的串的最大长度。
        for(int i=1;i<A.size();i++){
            for(int j=i-1;j>=0;j--){
                if(links[j].empty()){
                    links[i].insert({A[j],2});
                }else{
                    if(links[j].find(A[i]-A[j])!=links[j].end()){
                        links[i].insert({A[j],(links[j][A[i]-A[j]]+1)});
                    }else{
                        links[i].insert({A[j],2});
                    }
                }
            }
        }
        int maxLen=1;
        for(int i=1;i<A.size();i++){
            for(auto k:links[i]){
                maxLen=max(maxLen,k.second);
            }
        }
        return (maxLen<3?0:maxLen);
    }
};
```

```cpp
//记录以A[i],A[j]结尾的串的长度，j>i，用unordered_map记录每个元素的下标。考虑i从0到n-2,j从i+1到
//n-1，看A[j]-A[i]的元素是否存在，以及该元素在i,j之间还是在i前面。

class Solution {
public:
    //dp[i][j] denotes max length of sequence ending at index j whose previous element is  at index i
    int lenLongestFibSubseq(vector<int>& a) {
        vector<vector<long int> > dp(a.size()+1,vector<long int>(a.size()+1,0));
        long int ans=0;  
        unordered_map<long int,int> m;
        for(int i=0;i<a.size();i++)
            m[a[i]]=i;
        for(int i=0;i<a.size()-1;i++)
        {
            for(int j=i+1;j<a.size();j++)
            {
                long int diff=a[j]-a[i]; //third element of a subsequence of length 3 to be fibonacci i.e diff+a[i]=a[j]
                if(m.count(diff)!=0 and diff!=a[i])
                {
                    if(m[diff]>i) // if diff is the second element
                    {
                         dp[j][m[diff]]=dp[m[diff]][i]==0 ? 3: dp[m[diff]][i]+1;
                         ans=max(ans,dp[j][m[diff]]);
                    }  
                    else  //if diff is the first element
                    {
                        dp[j][i]=dp[i][m[diff]]==0 ? 3: dp[i][m[diff]]+1;
                        ans=max(ans,dp[j][i]);
                    }       
                }
            }
        }
        return ans;
    }
};
```

# 918. Maximum Sum Circular Subarray(dp)

```cpp
//TLE代码。当考虑dp[i],i>=A.size()时，要重新从dp[i-A.size()+1]算起，其中dp[i-A.size()+1]
//=A[i-A.size()+1]
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        vector<int> dp(A.size()*2-1,0);
        vector<int> dp2(A.size()*2-1,1);
        for(int i=0;i<A.size()*2-1;i++){
            dp[i]=A[i%A.size()];
        }
        int maxsum=dp[0];
        for(int i=1;i<A.size()*2-1;i++){
            if(i<A.size()){
                if(dp[i-1]>0){
                    dp[i]=dp[i-1]+A[i];
                    dp2[i]=dp2[i-1]+1;
                }
            }else{
                dp[i-A.size()+1]=A[i-A.size()+1];
                dp2[i-A.size()+1]=1;
                for(int j=i-A.size()+2;j<=i;j++){
                    if(dp[j-1]>0){
                        dp[j]=dp[j-1]+A[j%A.size()];
                        dp2[j]=dp2[j-1]+1;
                    }else{
                        dp[j]=A[j%A.size()];
                        dp2[j]=1;
                    }
                }
            }
            maxsum=max(maxsum,dp[i]);
        }
        return maxsum;
    }
};
```

```cpp
//从别人那那看的方法
//A[n]. 计算A[n]的最大连续子串和maxTotal，最小连续子串和minTotal，总和sum。
//所以sum-minTotal是circular subarray的最大和, 由于sum-minTotal包含了长度为0的串，所以当
//sum==minTotal时，只用返回maxTotal,否则，返回max(sum-minTotal,maxTotal)
class Solution {
public:
    int maxSubarraySumCircular(vector<int>& A) {
        if(A.size() == 0) return 0;
        int sum = A[0];
        int maxSoFar = A[0];
        int maxTotal = A[0];
        int minSoFar = A[0];
        int minTotal = A[0];
        for(int i = 1; i< A.size();i++){
            maxSoFar = max(A[i], maxSoFar + A[i]);
            maxTotal = max(maxTotal, maxSoFar);
            
            minSoFar = min(A[i], minSoFar + A[i]);
            minTotal = min(minTotal, minSoFar);
            sum += A[i];

        }
        if(sum==minTotal) return maxTotal;
        return max(sum - minTotal, maxTotal);
    }
};
```

# 1238.  Circular Permutation in Binary Representation

//When we transform vector[i-1] to vector[i],we just switch the bit of vector[i-1] at  position of the last bit 1 of variable 'i'

```cpp
class Solution {
public:
    int lowbit(int x){ //返回最低一位1所在的位数,如1010是1，1100是2
        int a = x&(-x);
        int cnt = -1;
        while(a!=0){
            a=a>>1;
            cnt++;
        }
        return cnt;
    }
    //When we transform vector[i-1] to vector[i],we just switch the bit of vector[i-1] at       //position of the last bit 1 of variable 'i'
    vector<int> circularPermutation(int n, int start) {
        vector<bool> switches(n, false);
        vector<int> res(1<<n);
        int temp = start;
        while(temp != 0){
            switches[lowbit(temp)] = true;
            temp-=(temp&(-temp));
        }
        res[0] = start;
        for(int i=1;i<(1<<n);i++){
            int a = lowbit(i);
            if(switches[a]){
                switches[a]=false;
                start-=(1<<a);
                res[i]=start;
            }else{
                switches[a]=true;
                start+=(1<<a);
                res[i]=start;
            }
        }
        return res;
    }
};
```

# 1247. Minimum Swaps to Make Strings Equal(难)

```cpp
class Solution {
public:
    //交换只在最小的可交换区间内进行（可交换区间是区间内的x数量为偶数，y数量为偶数，s1和s2都参与计数）
    int minimumSwap(string s1, string s2) {
        int cnt = 0, i=0, j = i;
        while(i<s1.size()){
            if(s1[i]==s2[i]){
                if(i!=j)
                    i++;
                else
                    i++,j++;
                continue;
            }
            int k1=-1, k2=-1;
            if(i==j){ //找到区间[i,j],使得s1和s2的x的总数为偶数，y的总数为偶数
                int x_num=1, y_num=1;
                while(!(x_num%2==0) || !(y_num%2==0)){
                    j++;
                    if(j>=s1.size())
                        return -1;
                    x_num += (s1[j]=='x') + (s2[j]=='x');
                    y_num += (s1[j]=='y') + (s2[j]=='y');
                    if(s1[i]==s2[j] && k1==-1)
                        k1 = j;
                    if(s2[i]==s2[j] && k2==-1)
                        k2 = j;
                }
            }else{
                for(int k=i+1;k<=j;k++){
                    if(s1[i]==s2[k] && k1==-1)
                        k1 = k;
                    if(s2[i]==s2[k] && k2==-1)
                        k2 = k;
                }
            }
            //交换
            if(k2==-1){
                swap(s1[i],s2[i]);
                cnt++;
                swap(s1[i],s2[k1]);
                cnt++;
            }else{
                swap(s1[i],s2[k2]);
                cnt++;
            }
        }
        return cnt;
    }
};
```

错误代码。

```cpp
class Solution {
public:
    //不考虑最小可交换区间了，再整个串内寻找k1,k2，使得s1[i]=s2[k1]且s1[k1]!=s2[k1]（保证不动
    //已经上下位置相等的那一列），s2[i]=s2[k2],且s1[k2]!=s2[k2]
    int minimumSwap(string s1, string s2) {
        int cnt = 0, i=0;
        while(i<s1.size()){
            if(s1[i]==s2[i]){
                i++;
                continue;
            }
            int k1=-1, k2=-1;
            for(int k=i+1;k<s1.size();k++){
                if(s1[i]==s2[k] && s1[k]!=s2[k] && k1==-1)
                    k1 = k;
                if(s2[i]==s2[k] && s1[k]!=s2[k] && k2==-1)
                    k2 = k;
            }
            if(k2==-1&&k1==-1)
                return -1;
            if(k2==-1){
                swap(s1[i],s2[i]);
                cnt++;
                swap(s1[i],s2[k1]);
                cnt++;
            }else{
                swap(s1[i],s2[k2]);
                cnt++;
            }
        }
        return cnt;
    }
};
```





