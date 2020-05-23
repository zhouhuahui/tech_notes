# 2313 软件工程实习

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1005
#define MAXK 30
struct Stu{
	int grade,finalGrade;
	char group;
}; 
Stu stus[MAXN];
int groupGrades[MAXK][MAXK];
int groupFinalGrades[MAXK];
int n,k;
bool cmp(const Stu&s1,const Stu&s2){
	return s1.finalGrade!=s2.finalGrade ? s1.finalGrade>s2.finalGrade : s1.group<s2.group;
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	//输入
	cin>>n>>k;
	int grade;
	char grp;
	for(int i=0;i<n;i++){
		cin>>grade>>grp;
		stus[i]={grade,0,grp};
	} 
	for(int i=0;i<k;i++){
		for(int j=0;j<k;j++){
			cin>>groupGrades[i][j];
		}
	}
	//计算每组最终项目分
	for(int i=0;i<k;i++){
		int summ=0;
		float avg;
		for(int j=0;j<k;j++){
			summ+=groupGrades[j][i];
		}
		avg=(float)summ/k; 
		summ=0;
		int cnt=0;
		for(int j=0;j<k;j++){
			if(abs(avg-groupGrades[j][i])<=15.0){
				summ+=groupGrades[j][i];
				cnt++;
			}
		}
		groupFinalGrades[i]=round((float)summ/cnt);
	} 
	//计算每个同学的最终得分
	for(int i=0;i<n;i++){
		stus[i].finalGrade=round((float)stus[i].grade*0.6+(float)groupFinalGrades[stus[i].group-'A']*0.4);
	} 
	//排序
	sort(stus,stus+n,cmp);
	//输出
	for(int i=0;i<n;i++){
		cout<<stus[i].finalGrade<<" "<<stus[i].group<<endl;
	} 
	return 0;
}
```

# 2314 程序员发橙子

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
#define MAXN 1000005
int n;
int a[MAXN];
int b[MAXN];
long long ans=0;
void init(){
	memset(b,0,sizeof(b));
}
bool legal(int i,int j){
	return (a[i]==a[j]&&b[i]==b[j] || a[i]<a[j]&&b[i]<b[j] || a[i]>a[j]&&b[i]>b[j]);
}
int main(){
	//freopen("D:\\input.txt","r",stdin);
	init();
	cin>>n;
	if(n==0){
		cout<<0;
		return 0;
	}
	for(int i=0;i<n;i++){
		cin>>a[i];
	}
	b[0]=1;
	ans+=b[0];
	for(int i=1;i<n;i++){
		if(a[i]==a[i-1]){
			b[i]=b[i-1];
			ans+=b[i];
		}else if(a[i]>a[i-1]){
			b[i]=b[i-1]+1;
			ans+=b[i];
		}else{
			if(b[i-1]==1){
				b[i-1]++;
				b[i]=1;
				ans+=2;
				for(int j=i-1;j>0;j--){
					if(legal(j-1,j)){
						break;
					}else{
						int temp=b[j-1];
						if(a[j-1]>a[j]){
							b[j-1]=b[j]+1;
							ans+=(b[j-1]-temp);
						}else if(a[j-1]==a[j]){
							b[j-1]=b[j];
							ans+=(b[j-1]-temp);
						}
					}
				}
			}else{
				b[i]=1;
				ans+=b[i];
			}
		}
	}
	cout<<ans;
	return 0;
}
```

# 2315 众数出现的次数

输入为a,b，a和b异或为c; 若a和c不一样，则累加a和c的次数，若a和c一样，则累加a的次数

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
int n;
map<int,int> mp;
int main(){
	//freopen("D:\\input.txt","r",stdin);
	cin>>n;
	int a,b,c;
	for(int i=0;i<n;i++){
		cin>>a>>b;
		c=(a^b);
		//cout<<a<<" "<<b<<" "<<c<<endl;///////
		if(a!=c){
			mp[a]++;
			mp[c]++;
		}else{
			mp[a]++;
		}
	}
	int maxCnt=-1,num;
	for(auto&i:mp){
		if(i.second>maxCnt){
			maxCnt=i.second;
			num=i.first;
		}
	}
	cout<<num;
	return 0;
}
```



# 2316 特殊的翻转

```cpp
//freopen("D:\\input.txt","r",stdin);
//ios::sync_with_stdio(false);
#include<bits/stdc++.h>
using namespace std;
string input;
vector<bool> vec;
vector<int> cnts;
int minTime=1e8;
void swap2(int i){
	cnts[i]++;
	if(i-1>=0)cnts[i-1]++;
	if(i+1<cnts.size())cnts[i+1]++;
}
void deswap2(int i){
	cnts[i]--;
	if(i-1>=0)cnts[i-1]--;
	if(i+1<cnts.size())cnts[i+1]--;
}
bool isright(int i){
	if(i<0)return true;
	return !((vec[i]&&cnts[i]%2==0) || (!vec[i]&&cnts[i]%2==1));
}
void dfs(int i,int time){
	if(i>=vec.size()){
		//cout<<isright(i-2)<<" "<<isright(i-1)<<endl;//////
		if(isright(i-2)&&isright(i-1)){
			if(time < minTime){
				minTime=time;
			}
		}
		return;
	}
	if(!isright(i-2)){
		return;
	}
	dfs(i+1,time);
	swap2(i);
	dfs(i+1,time+1);
	deswap2(i);
}
void convertToBin(char c, vector<bool>& a){
	int temp=(isdigit(c)?c-'0':c-'A'+10);
	while(temp!=0){
		a.push_back(temp%2);
		temp/=2;
	}
	for(int i=a.size();i<4;i++)a.push_back(false);
	reverse(a.begin(),a.end());
}
int main(){
	freopen("D:\\input.txt","r",stdin);
	cin>>input;
	for(int i=0;i<input.size();i++){
		vector<bool> a;
		convertToBin(input[i],a);
		if(vec.size()==0){
			auto it=a.begin();
			while(it!=a.end()){
				if(*it){
					break;
				}else{
					a.erase(it);
					it=a.begin(); 
				}
			}
		}
		for(int j=0;j<a.size();j++){
			vec.push_back(a[j]);
		}
	}
	
	cnts.resize(vec.size());
	for(int i=0;i<cnts.size();i++)cnts[i]=0; //init cnts
	dfs(0,0);
	if(minTime!=1e8){
		cout<<minTime;
	}else{
		cout<<"No";
	}
} 
```

超时，因为我用的DFS

