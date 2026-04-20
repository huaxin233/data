# Week 11 学习手册：图论

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`graph/` 加权图算法
> 精读文件：6 个
> 配套 LeetCode：743, 787, 1584, 207, 210

---

## 一、本周目标与产出

### 理解目标

- 图的两种表示：邻接矩阵 vs 邻接表
- 最短路径三巨头：Dijkstra / Bellman-Ford / Floyd-Warshall
- 为什么 Dijkstra **不能**处理负权
- Bellman-Ford 如何**检测负环**
- 最小生成树 MST 两种实现：Kruskal / Prim
- 拓扑排序（Kahn 入度法 / DFS）
- 强连通分量 SCC（Kosaraju）

### 动手目标

| 算法 | 复杂度 | 关键思路 |
|------|--------|----------|
| Dijkstra（堆优化） | O((V+E) log V) | 贪心 + 优先队列 |
| Bellman-Ford | O(VE) | V-1 轮松弛 |
| Floyd-Warshall | O(V³) | DP，三重循环 |
| Kruskal | O(E log E) | 贪心选边 + 并查集（Week 5） |
| Prim | O(E log V) | 贪心扩展 + 优先队列 |
| 拓扑排序（Kahn） | O(V+E) | BFS + 入度 |
| Kosaraju | O(V+E) | 两次 DFS |

### 产出清单

- `my-impl/week-11/*.cpp` — 6-7 份手写实现
- `spec/notes/week-11-graph.md` — 一页小结
- `spec/progress/week-11.md` — 进度表

---

## 二、每日安排（本周最密集，每日 1-2 个算法）

### Day 1（周一）：图表示 + Dijkstra

- **上午 1h**：图的邻接矩阵 vs 邻接表
- **下午 1.5h**：读 [graph/dijkstra.cpp](graph/dijkstra.cpp)
- **晚上 1.5h**：闭卷手写堆优化 Dijkstra + **LeetCode 743**

### Day 2（周二）：Bellman-Ford

- **上午 1h**：读 [graph/bellman_ford.cpp](graph/bellman_ford.cpp)
- **下午 1h**：闭卷手写 Bellman-Ford（含负环检测）
- **晚上 1h**：**LeetCode 787**（K 站中转 → Bellman-Ford 变形）

### Day 3（周三）：Floyd-Warshall

- **上午 1h**：读 [graph/floyd_warshall.cpp](graph/floyd_warshall.cpp)
- **下午 1h**：闭卷手写 Floyd（三重循环）
- **晚上 1h**：总结三大最短路对比

### Day 4（周四）：MST — Kruskal

- **上午 1h**：读 [graph/kruskal.cpp](graph/kruskal.cpp)
- **下午 1.5h**：闭卷手写 Kruskal（回顾 Week 5 并查集）
- **晚上**：**LeetCode 1584**（连接所有点最小费用）

### Day 5（周五）：MST — Prim

- **上午 1h**：读 [graph/prim.cpp](graph/prim.cpp)
- **下午 1h**：闭卷手写堆优化 Prim
- **晚上 1h**：对比 Kruskal vs Prim

### Day 6（周六）：拓扑排序 + SCC

- **上午 1.5h**：拓扑排序两种实现（Kahn + DFS）
- **下午 1h**：读 [graph/kosaraju.cpp](graph/kosaraju.cpp)
- **晚上 1.5h**：**LeetCode 207**（判环）+ **210**（拓扑序输出）

### Day 7（周日）：复盘

- **上午**：闭卷重写 Dijkstra / Kruskal / 拓扑排序
- **下午**：填写小结 + 进度表 + 对比表

---

## 三、图论深度讲解

### 3.1 图的两种表示

#### 邻接矩阵 `adj[i][j]`

- 空间 O(V²)
- 查询 `(i, j)` 是否相邻 O(1)
- 适合**稠密图**（边数接近 V²）

#### 邻接表 `vector<vector<pair<int, int>>>`

- 空间 O(V + E)
- 查询 `(i, j)` 需遍历 i 的邻居
- 适合**稀疏图**（大多数 LeetCode 场景）

```cpp
// 邻接表：对每个 u，存 (v, w) 对
vector<vector<pair<int,int>>> adj(n);
adj[u].push_back({v, w});    // 加边
adj[v].push_back({u, w});    // 无向图加反向边
```

### 3.2 Dijkstra（单源最短路，正权）

**核心思想**：贪心——每次从"未确定最短距离"的节点中选最近的，用它松弛邻居。

**堆优化版**（对照 [graph/dijkstra.cpp](graph/dijkstra.cpp)）：

```cpp
vector<int> dijkstra(int start, int n, vector<vector<pair<int,int>>>& adj) {
    vector<int> dist(n, INT_MAX);
    dist[start] = 0;
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;  // 小顶堆
    pq.push({0, start});

    while (!pq.empty()) {
        auto [d, u] = pq.top(); pq.pop();
        if (d > dist[u]) continue;          // 陈旧条目跳过
        for (auto [v, w] : adj[u]) {
            if (dist[u] + w < dist[v]) {
                dist[v] = dist[u] + w;
                pq.push({dist[v], v});
            }
        }
    }
    return dist;
}
```

**时间**：O((V+E) log V)（堆优化）。

**为什么不能处理负权**：一旦确定某节点的最短距离就不再更新；但负边可能提供更短路径。

**反例**：三角形 A-(1)-B-(-10)-C, A-(0)-C。Dijkstra 先把 C 标记为 0，但实际 A→B→C 为 -9。

### 3.3 Bellman-Ford（允许负权，检测负环）

**核心思想**：对所有边做 `V - 1` 轮松弛，任何最短路径最多 V-1 条边。

```cpp
vector<int> bellmanFord(int start, int n, vector<tuple<int,int,int>>& edges) {
    vector<int> dist(n, INT_MAX);
    dist[start] = 0;
    for (int i = 0; i < n - 1; i++) {
        for (auto [u, v, w] : edges) {
            if (dist[u] != INT_MAX && dist[u] + w < dist[v])
                dist[v] = dist[u] + w;
        }
    }
    // 第 n 轮若仍能松弛 → 存在负环
    for (auto [u, v, w] : edges) {
        if (dist[u] != INT_MAX && dist[u] + w < dist[v])
            return {};  // 负环
    }
    return dist;
}
```

**时间**：O(VE)。

**适用**：有负权边 / 需要检测负环。

### 3.4 Floyd-Warshall（多源最短路）

**核心**：DP，`dp[k][i][j]` = 只用前 k 个节点作中转时 i→j 最短距离。

**转移**：`dp[k][i][j] = min(dp[k-1][i][j], dp[k-1][i][k] + dp[k-1][k][j])`

**空间优化**：可压缩到二维（k 维度就地覆盖）。

```cpp
vector<vector<int>> floyd(int n, vector<vector<int>>& adj) {
    auto dist = adj;
    for (int k = 0; k < n; k++)
        for (int i = 0; i < n; i++)
            for (int j = 0; j < n; j++)
                if (dist[i][k] != INT_MAX && dist[k][j] != INT_MAX)
                    dist[i][j] = min(dist[i][j], dist[i][k] + dist[k][j]);
    return dist;
}
```

**时间**：O(V³)。

**适用**：多源多目标查询；稠密图；V 小（V ≤ 500）。

### 3.5 三大最短路对比

| 算法 | 复杂度 | 负权 | 负环检测 | 多源 | 适用 |
|------|--------|------|----------|------|------|
| Dijkstra (堆) | O((V+E) log V) | ❌ | ❌ | ❌ | 单源，正权 |
| Bellman-Ford | O(VE) | ✅ | ✅ | ❌ | 单源，负权 |
| Floyd | O(V³) | ✅ | ⚠️ | ✅ | 多源，V 小 |

### 3.6 MST — Kruskal

**核心**：贪心——所有边按权重排序，依次加入不形成环的边。

**并查集判环**（Week 5）：

```cpp
int kruskal(int n, vector<tuple<int,int,int>>& edges) {
    sort(edges.begin(), edges.end(), [](auto& a, auto& b){
        return get<2>(a) < get<2>(b);
    });
    DSU dsu(n);
    int total = 0, count = 0;
    for (auto [u, v, w] : edges) {
        if (dsu.find(u) != dsu.find(v)) {
            dsu.unite(u, v);
            total += w;
            if (++count == n - 1) break;
        }
    }
    return total;
}
```

**时间**：O(E log E)（排序主导）。

**适用**：稀疏图。

### 3.7 MST — Prim

**核心**：贪心——从任一起点扩展，每次选连到已选集合权重最小的边。

**堆优化版**：

```cpp
int prim(int n, vector<vector<pair<int,int>>>& adj) {
    vector<bool> inMST(n, false);
    priority_queue<pair<int,int>, vector<pair<int,int>>, greater<>> pq;
    pq.push({0, 0});                       // (weight, node)
    int total = 0, count = 0;
    while (!pq.empty() && count < n) {
        auto [w, u] = pq.top(); pq.pop();
        if (inMST[u]) continue;
        inMST[u] = true;
        total += w;
        count++;
        for (auto [v, wv] : adj[u])
            if (!inMST[v]) pq.push({wv, v});
    }
    return total;
}
```

**时间**：O(E log V)。

**适用**：稠密图（比 Kruskal 少一个 log）。

### 3.8 拓扑排序（Kahn 入度法）

**前提**：有向无环图（DAG）。

**思想**：

1. 计算每个节点入度
2. 入度为 0 的节点入队
3. 出队处理，其邻居入度 -1；若降为 0 入队
4. 队列空 → 若所有节点都出过队 → 拓扑序；否则存在环

```cpp
vector<int> topoSort(int n, vector<vector<int>>& adj) {
    vector<int> indeg(n, 0);
    for (int u = 0; u < n; u++)
        for (int v : adj[u]) indeg[v]++;
    queue<int> q;
    for (int i = 0; i < n; i++) if (indeg[i] == 0) q.push(i);
    vector<int> order;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        order.push_back(u);
        for (int v : adj[u]) if (--indeg[v] == 0) q.push(v);
    }
    return order.size() == n ? order : vector<int>{};  // 有环返回空
}
```

**时间**：O(V + E)。

**应用**：课程安排（207, 210）、编译器依赖解析。

### 3.9 强连通分量 SCC（Kosaraju）

**定义**：有向图中，若 `u → v` 且 `v → u` 都可达，则 u, v 属于同一 SCC。

**Kosaraju 算法**（两次 DFS）：

1. 对原图 DFS，按完成时间倒序得到栈
2. 构造反图
3. 按栈顺序出栈，对反图 DFS，每次 DFS 到的所有节点构成一个 SCC

对照 [graph/kosaraju.cpp](graph/kosaraju.cpp)。

**时间**：O(V + E)。

**应用**：2-SAT、强连通性检测、图的"缩点"。

**面试考察频率低**，理解思想即可。

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::priority_queue<pair<int,int>, vector<>, greater<>>` | 小顶堆 |
| `std::pair` / `std::tuple` | 存边 |
| `auto [a, b] = pair` | C++17 结构化绑定 |
| `std::vector<vector<pair<int,int>>>` | 加权邻接表 |
| `INT_MAX / INT_MIN` | `<climits>` |

**自定义比较器示例**：

```cpp
// 按 pair.first 升序
auto cmp = [](auto& a, auto& b) { return a.first > b.first; };  // 注意：堆要反写
priority_queue<pair<int,int>, vector<pair<int,int>>, decltype(cmp)> pq(cmp);
```

---

## 五、LeetCode 题目指南

### 743. 网络延迟时间（Medium）

- **目的**：Dijkstra 模板题
- **提示**：从节点 k 到所有点的最长最短路；堆优化 Dijkstra

### 787. K 站中转内最便宜航班（Medium）

- **目的**：Bellman-Ford 变形
- **提示**：限制边数 ≤ K+1；每轮松弛一次（不是所有 V-1 轮）

### 1584. 连接所有点的最小费用（Medium）

- **目的**：MST 模板
- **提示**：Kruskal 或 Prim 都可；曼哈顿距离作权重

### 207. 课程表（Medium）

- **目的**：拓扑排序判环
- **提示**：Kahn 算法，若无法输出 n 个节点则有环

### 210. 课程表 II（Medium）

- **目的**：输出拓扑序
- **提示**：同 207，直接返回 `order`（长度 < n 则返回空）

---

## 六、周日复盘清单

**理论层**：

1. Dijkstra 为什么不能处理负权？举反例。
2. Bellman-Ford 的 V-1 轮松弛是怎么来的？
3. Floyd 的 `k` 维度含义？为什么可以压缩到二维？
4. Kruskal vs Prim 各自适合什么场景？
5. 拓扑排序失败说明什么？

**动手层**：

6. 能否闭卷写堆优化 Dijkstra？
7. 能否闭卷写 Kruskal（结合并查集）？
8. 能否闭卷写拓扑排序（Kahn）？
9. 5 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的是？下周怎么解决？
12. 下周是最后一周（位运算+字符串+综合冲刺），做好准备了吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ 三大最短路混用（每题想清楚用哪个）
- ❌ Dijkstra 遇负权（必然错）

**允许**：

- ✅ 面试提交用 STL `priority_queue`
- ✅ 画图辅助（强烈推荐画 Dijkstra 松弛过程）
