# 算法基础

## 3.图论与搜索



### 搜索

#### DFS 深度优先搜素

执着的人 --- 走到头 //  无路可走才回溯

- 数据结构：stack
- 空间：O（h）
- 不具有最短性



每个DFS对应一个搜索树 最重要的是  **顺序**

- 回溯
  - 恢复现场
- 剪枝



##### 全排列问题：

​	

```c++
#include<iostream>
using namespace std;

const int N = 10;//最大数不超过N
int n;
int path[N];
bool st[N];

void dfs(int u){
    if(u == n){
        for(int i = 0 ; i< n;i++) printf("%d",path[i]);
        putc("\n");
        return;
    }
    
    for(int i = 1 ; i <= n ;i++){
    	if(!st[i]){
        	path[u] = i;            
            st[i] = true;
            dfs(u+1);
            //path[u] = i;无关紧要，回覆盖掉 
            st[i] = false;//恢复现场
        }
        
  
    }
} 


int main(){
    cin>>n;
    
    dfs(0);
    
    return 0;
}

```



##### n皇后问题



​		剪枝：提前判断当前方案不合法



- 按行枚举

​		类似于全排列问题

```c++
#include<iostream>
using namespace std;

const int N = 10;//最大数不超过N
int n;//棋盘是n*n
char g[N][N];
bool col[N],dg[2*N],udg[2*N];

void dfs(int u){
    if(u == n){
        for(int i = 0 ; i< n ;i++) puts(g[i]);
        puts("");
        return;
    }
    
    for(int i = 0 ; i < n ;i++){
        //剪枝
    	if(!col[i] && !dg[u+i] & !udg[n-u+i]){//用截距来表示每一个对角线，i-u+n是为了防止出现负数，u+i和n-u+i都有可能大于N，故设数组时设为2N
        	g[u][i] = 'Q';            
            col[i] = dg[i+u]= udg[i-u+n] = true;
            dfs(u+1);
            g[u][i] = '.';            
            col[i] = dg[i+u]= udg[i-u+n] = false;
        }   
    }
} 


int main(){
    cin>>n;
    for(int i = 0 ;i<n;i++){
        for(int j = 0; j< n; j++){
            g[i][j] = '.';
        }
    }
    
    dfs(0);
    
    return 0;
}
```

- 按格子枚举

二叉树，每一次都选择放皇后和不放皇后两种，深度遍历到底然后再回溯

```c++
#include<iostream>
using namespace std;

const int N = 10;//最大数不超过N
int n;//棋盘是n*n
char g[N][N];
bool row[N],col[N],dg[2*N],udg[2*N];

void dfs(int x,int y,int s){
	if(y == n) x++,y=0;//一行搜索完了
    
    if(x == n){ //所有的格子都搜索完了
        if(s == n){//放了n个皇后的情况符合题意
            for(int i=0;i<n;i++) puts(g[i]);
            puts("");
        }
        
        return; //所有格子都搜索完了之后，不管放了几个皇后，都要返回
    }
    
    //不放皇后
    dfs(x,y+1,s);
    
    //放皇后
    if(!col[y] && !row[x] && !dg[x+y] && !udg[x-y+n]){
        g[x][y] = 'Q';
        col[y] =row[x] =dg[x+y] =udg[x-y+n] = true;
        dfs(x,y+1,s+1);
        g[x][y] = '.';
        col[y] =row[x] =dg[x+y] =udg[x-y+n] = false;
    }
    
} 


int main(){
    cin>>n;
    for(int i = 0 ;i<n;i++){
        for(int j = 0; j< n; j++){
            g[i][j] = '.';
        }
    }
    
    dfs(0);
    
    return 0;
}
```



#### BFS 宽度优先搜索

稳重的人 --- 眼观六路 耳听八方

- 数据结构：queue
- 空间：O（2**h）
- **最短**路
  - 一圈一圈往外走



##### 走迷宫

![image-20240403221100651](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240403221100651.png)

![image-20240403221123637](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240403221123637.png)

深搜没有常用的框架，但是宽搜有一个常用的框架

![image-20240403221729254](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240403221729254.png)

代码：

//记录路径：开一个数组，记录他的前一个点的坐标

//遍历输出：在x，y不同时为零的时候，一直往前倒

```c++
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;
typedef pair<int,int> PII;

const int N = 110;
int n,m;
int g[N][N];//地图
int d[N][N];//存放距离
PII q[N*N];//存放坐标
PII pre[N][N];//存放前一个点的坐标，输出路径做准备
//不能写成pre[N*N]不然后面的相关代码会报错？？？why

int bfs(){
    int hh = 0, tt = 0;//q（队列）的两个指针
    q[0] = {0,0};

    memset(d,-1,sizeof(d));
    d[0][0] = 0;//起始位置的距离设置为0

    int dx[4] = {-1,0,1,0} , dy[4] = {0,1,0,-1};//代表四个方向的向量，在后面的for循环中可以简化向四周扩展的代码

    while(hh <= tt){
        //取出队列的头部
        auto t = q[hh++];
        //向四个方向扩展
        for(int i =0;i<4;i++){
            int x = t.first + dx[i],y = t.second + dy[i];
            //将满足条件的点的坐标存入队列尾部
            if(x >= 0 && y>= 0 && y<m && x<n && g[x][y] == 0 && d[x][y] == -1){
                //距离+1
                d[x][y] = d[t.first][t.second] +1;
                pre[x][y] = t;
                q[++tt] = {x,y};
            }
        }
    }
    //横纵坐标，输出路径
    int x = n -1 ,y =m -1 ;
    while(x || y){
        cout<<x << "," << y<<endl;
        PII temp = pre[x][y];
        x = temp.first,y = temp.second;
    }

    return d[n-1][m-1];//返回最后一个点的距离

}

int main(){
    cin >> n >> m;
    for(int i = 0; i<n;i++){
        for(int j=0;j<m;j++){
            cin>>g[i][j];
        }
    }
    cout<< bfs()<<endl;

    return 0;
}


```



### 树和图的存储与遍历 



树是一种特殊的图（无环连通图）



有向图和无向图

- 邻接矩阵
- 邻接表 √



#### 树的重心

删掉树的重心之后，连通块的最大联通量为？

对于每个点：

- 把点删掉之后，连通块的最大值

- 最大值的最小值就是答案



​		`郭子铭到此一游wink~`

```c++
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 100010;//点数
const int M = N*2;//边数

int n;
int h[N],e[M],ne[M],idx;//数组实现邻接表
//h为头指针数组，节点代号为下标，值为对应链表的第一个节点的代号
//e为节点代号数组，下标用来模拟指针，值为节点的代号
//ne相当于next指针，下标表示节点代号，值为下一个节点在e数组中的下标
bool st[N];//该点是否被遍历过
int ans = N;//答案

//添加边就是在a链表的头部加上b
void add(int a,int b){
    e[idx] = b,ne[idx] = h[a],h[a] = idx++;
}

//dfs返回以u为根的连通块的点数
int dfs(int u){
    st[u] = true;
    
    int sum = 1;//返回值
    int res = 0;//把该节点删掉后所有连通块点数的最大值
    
    //对u点的所有邻接点进行遍历
    //i为下标/序号
    for(int i = h[u]; i != -1 ; i = ne[i]){
        int j = e[i];//j为点的代号，不同于序号
        if(!st[j]){
            int _sum = dfs(j); 
        	sum += _sum;//将节点数加到u上
            //更新连通块的点数最大值
            res = max(res,_sum);
        }
    }
    //更新连通块的最大值
    res = max(res,n-sum);
    
    //更新连通块的最大值的最小值
    ans = min(ans,res);
    
    return sum;
}

int main(){
    cin>>n;
    memset(h,-1,sizeof(h));
    
    //为啥n-1？
    for(int i = 0;i<n-1;i++){
        int a ,b;
        cin>>a>>b;
        add(a,b),add(b,a);
    }
    
    dfs(1);
    
    cout<<ans<<endl;
    
    return 0;
}
```

输入：

`9
1 2
1 7
1 4
2 8
2 5
4 3
3 9
4 6`

输出

`4`



#### 图中点的层次

![image-20240407134451895](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240407134451895.png)

```c++
#include<cstring>
#include <iostream>
#include<algorithm>

using namespace std;

const int N = 100010;

int n,m;
int h[N],e[N],ne[N],idx;
int d[N],q[N];

void add(int a,int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

int bfs(){
    int hh = 0,tt = 0;
    q[0] = 1;
    
    memset(d,-1,sizeof(d));
    d[1] = 0;
    
    while(hh<= tt){
        int t = q[hh++];
        for(int i =h[t];i != -1 ;i = ne[i]){
            int j = e[j];
            if(d[j] == -1){
                d[j] = d[t] + 1; 
                q[++tt] = j;
            }
        }
    }
    
    return d[n];
    
}

int main(){
    cin>>n>>m;
    
    memset(h,-1,sizeof(h));
    
    for(int i =0;i<m;i++){
        int a,b;
        cin>>a>>b;
        add(a,b);
    }
    
    cout << bfs() <<endl;
    
    return 0;
}


```



输入：

`4 5 `

`1 2 `

`2 3 `

 `3 4  `

 `1 3 `

 `1 4 `

输出

`1`



#### 图的拓扑序列

![image-20240407140407166](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240407140407166.png)

![image-20240407140653029](C:\Users\HP\AppData\Roaming\Typora\typora-user-images\image-20240407140653029.png)



- 有向无环图（拓扑图）才有拓扑序列

- 入度
  - 入度为零的点可以作为起点

- 出度



```c++
#include<cstring>
#include<iostream>
#include<algorithm>

using namespace std;

const int N = 100010;

int n,m;
int h[N],e[N],ne[N],idx;
int d[N],q[N];//d为某点的入度

void add(int a ,int b){
    e[idx] = b;
    ne[idx] = h[a];
    h[a] = idx++;
}

bool topsort(){
    //tt的取值？
    int hh = 0,tt = -1;
    //i的取值？ -- 顶点标号从1开始
    for(int i = 1;i<= n;i++){
        if(!d[i]){
            q[++tt] = i;//++tt??tt++
        }
    }
    //如果上面没有插入元素的话（没有入度为零的点），循环不会进行。这是tt取-1的原因
    while(hh<=tt){
        int t = q[hh++];
        
        for(int i = h[t];i != -1;i = ne[i]){
            int j = e[i];
            d[j]--;
            if(d[j] == 0){
                q[++tt] = j;
            }
        }
    }
    return tt == n-1;
}

int main(){
    cin>>n>>m;
    
    memset(h,-1,sizeof(h));
    
    for(int i = 0 ;i<m;i++){
        int a,b;
        cin>>a>>b;
        add(a,b);
        d[b]++;
    }
    
    if(topsort()){
        //q里面的次序就是拓扑序
        for(int i =0;i<n;i++){ 
            printf("%d",q[i]);
        }
        puts("");
    }else{
        puts("-1");
    }
    
    return 0;
}
```





### 最短路径问题



n点数，m边数

- 单源最短路
  - 所有边权都是正数
    - 朴素的dijkstra算法 - 稠密图
    - 堆优化的dijkstra算法 - 稀疏图
  - 存在负权边
    - Bellman-Ford
    - SPFA 
- 多源汇最短路
  - 源点就是起点，汇点就是终点
  - Floyd算法



建图是难点



#### 朴素的dijkstra算法

思路：

1. 初始化距离 dsit[1] = 0 , dist[i] = 正无穷，s集合，已确定最短距离的点
2. 循环n次
   1. 不在s中的距离最近的点 = t
   2. 把t加到s
   3. 用t更新其他点的距离



用邻接矩阵存储图

有重边和自环的图

```c++
#include<iostream>
#include<cstring>
#include<algorithm>

using namespace std;

const int N = 1010;

int n,m;
int g[N][N];
int dist[N];
bool st[N];

int dijkstra(){
    memset(dist,0x3f,sizeof(dist));
    dist[1] = 0;
    
    for(int i =0;i<n;i++){
        //寻找未确定的最短距离的点
		int t =-1;
        for(int j = 1;j<=n;j++){
            if(!st[j] && (t == -1 || dist[j]<dist[t])){
                t = j;
            }
        }
        st[t] = true;
        
        //用t更新其他点的距离
        for(int j = 1;j<=n;j++){
            dist[j] = min(dist[j],dist[t] + g[t][j]);
        }
        
    }
    
    //第n个点没有到起点的最短路径，或者算法出错
    if(dist[n] == 0x3f3f3f3f){
        return -1;
    }
    return dist[n];
}

int main(){
    scanf("%d%d",&n,&m);
    
    //无穷大常量取值是0x3f3f3f3f， 它的十进制是1061109567，也就是10^9级别的，而一般场合下的数据都是小于10^9的，所以它可以作为无穷大使用。
    memset(g,0x3f,sizeof(g));
    
    while(m--){
        int a,b,c;
        scanf("%d%d%d",&a,&b,&c);
        //最短路问题只保存最短的边即可
        g[a][b] = min(g[a][b],c);
    }
    
    //起点到终点的距离
    int t = dijkstra();
    
    printf("%d\n",t);
    
    return 0;
}

```

#### 堆优化的dijkstra算法



二-46



### 最小生成树

对应无向图

- prim算法
  - 朴素的prim算法  - 稠密图
  - 堆优化的prim算法 （稀疏图 不常用）
- kruskal算法 - 稀疏图

#### 朴素的prim算法

类似于dijkstra算法

```c++
#include <cstring>
#include <iostream>
#include <algorithm>

using namespace std;

const int N = 510;
const int INF = 0x3f3f3f3f;
int n,m;
int g[N][N];
int dist[N];//某点到集合的距离
bool st[N];

int prim(){
    memset(dist,0x3f,sizeof(dist));
    
    int res = 0;//最小生成树的总代价
    
    for(int i =0;i < n;i++){
        
        int t = -1;
        for(int j=1;j<=n;j++){
            if(!st[j] &&(t == -1 || dist[j] < dist[t])){
                t = j;
            }
        }
        
        if(i && dist[t] == INF) return INF;
        //先累加再更新？ 若存在负自环，或者自环的权值更小，在下面循环j==t的时候，会更新dist[t],就导致res结果不正确 --- 第二组的4 4 -10
        if(i) res+= dist[t];
        st[t] = true;
        for(int j = 1; j<=n;j++){
            dist[j] = min(dist[j],g[t][j]);
        }
    }
    
    return res;
}


int main(){
    scanf("%d%d",&n,&m);

    memset(g,0x3f,sizeof(g));
    
    while(m--){
        int a,b,c;
    	scanf("%d%d%d",&a,&b,&c);
        g[a][b] = g[b][a] = min(g[a][b],c);
    }
    int t = prim();
    if(t == INF){
        printf("impossible\n");
    }else{
    	printf("%d\n",t);
    }
    
    return 0;
}
```

输入

`4 5 `

`1 2 1 `

`1 3 2 `

`1 4 3 `

`2 3 2 `

`3 4 4`

输出

`6`



输入

`5 10
1 2 8
2 2 7
2 1 1
3 4 3
4 4 -10
1 3 -9
5 2 -4
3 1 0
1 4 8
4 4 7`

输出

`-9`



#### kruskal算法

三-39





### 二分图

- 染色法（dfs）
- 匈牙利算法



