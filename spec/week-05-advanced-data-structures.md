# Week 5 学习手册：高级数据结构（并查集 / 线段树 / 前缀和 / BIT）

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`data_structures/` 高级部分 + `range_queries/`
> 精读文件：5 个
> 配套 LeetCode：208, 307, 303, 547, 200

---

## 一、本周目标与产出

### 理解目标

- 并查集（DSU）的 find / union 原理
- 路径压缩与按秩合并为何近似 O(1)
- 前缀和 O(1) 区间查询 vs 差分数组 O(1) 区间更新
- 线段树 build / update / query 的三重递归结构
- 线段树 Lazy Propagation 思想
- 树状数组（BIT）的 `lowbit` 技巧
- 线段树 vs BIT 各自适用场景

### 动手目标

| 结构 | 核心操作 | 复杂度 |
|------|----------|--------|
| 并查集（基础） | find / union | O(log n) 均摊 |
| 并查集（路径压缩+按秩） | 同上 | α(n) ≈ O(1) |
| 前缀和数组 | 区间和查询 | O(1) |
| 差分数组 | 区间加 | O(1) 更新 + O(n) 查询 |
| 线段树（单点更新，区间查询） | build/update/query | O(log n) |
| 树状数组（BIT） | update/prefixSum | O(log n) |

### 产出清单

- `my-impl/week-05/*.cpp` — 5-6 份手写实现
- `spec/notes/week-05-advanced-data-structures.md` — 一页小结
- `spec/progress/week-05.md` — 进度表

---

## 二、每日安排

### Day 1（周一）：并查集基础

- **上午 1h**：读 [data_structures/disjoint_set.cpp](data_structures/disjoint_set.cpp)，理解 find / union
- **下午 1.5h**：闭卷手写基础并查集（无优化）
- **晚上 0.5h**：画图理解"根节点代表整个集合"
- **LeetCode**：547（省份数量）

### Day 2（周二）：并查集优化

- **上午 1h**：读 [data_structures/dsu_path_compression.cpp](data_structures/dsu_path_compression.cpp)
- **下午 1.5h**：实现"路径压缩 + 按秩合并"双优化版本
- **晚上 0.5h**：理解 α(n) 反阿克曼函数含义
- **LeetCode**：200（岛屿数量，并查集解法）

### Day 3（周三）：前缀和 + 差分数组

- **上午 1h**：读 [range_queries/prefix_sum_array.cpp](range_queries/prefix_sum_array.cpp)
- **下午 1h**：实现一维前缀和 + 二维前缀和
- **晚上 1h**：实现差分数组，理解"差分是前缀和的逆"
- **LeetCode**：303（区域和检索 - 数组不可变）

### Day 4（周四）：线段树 — 单点更新

- **上午 1.5h**：读 [data_structures/segment_tree.cpp](data_structures/segment_tree.cpp)，画出递归调用树
- **下午 1.5h**：闭卷手写单点更新版线段树
- **LeetCode**：307（区域和检索 - 数组可修改）

### Day 5（周五）：线段树 — 区间更新 + Lazy Propagation

- **上午 1.5h**：理解 Lazy Propagation 思想
- **下午 1.5h**：扩展线段树支持区间加
- **晚上**：可选——做一道复杂线段树题

### Day 6（周六）：树状数组 BIT

- **上午 1h**：读 [range_queries/fenwick_tree.cpp](range_queries/fenwick_tree.cpp)，理解 `lowbit = x & (-x)`
- **下午 1.5h**：闭卷手写 BIT（update + prefixSum）
- **晚上 1h**：**LeetCode 208**（实现前缀树 Trie）— 本周额外加餐

### Day 7（周日）：复盘

- **上午 1h**：闭卷重写并查集 + 线段树或 BIT
- **下午 0.5h**：填写小结
- **下午 0.5h**：填写进度表

---

## 三、高级数据结构深度讲解

### 3.1 并查集（DSU / Union-Find）基础

**核心思想**：用树表示集合，根节点是集合代表。

**两个操作**：

- **Find(x)**：找 x 所在集合的代表（根）
- **Union(x, y)**：把 x 和 y 所在的集合合并

**最朴素版**（对照 [data_structures/disjoint_set.cpp:37-82](data_structures/disjoint_set.cpp#L37-L82)）：

```cpp
vector<int> root, rank_;

void init(int n) {
    root.assign(n + 1, 0);
    rank_.assign(n + 1, 1);
    for (int i = 1; i <= n; i++) root[i] = i;
}

int Find(int x) {
    if (root[x] == x) return x;
    return Find(root[x]);           // 朴素版，每次 O(树高)
}

void Union(int x, int y) {
    int a = Find(x), b = Find(y);
    if (a != b) root[a] = b;        // 简单挂靠
}
```

**问题**：不优化的话，最坏情况树高 O(n)，find 退化为 O(n)。

### 3.2 并查集优化

#### 路径压缩（Path Compression）

**思想**：Find 时把沿途节点都直接挂到根。

```cpp
int Find(int x) {
    if (root[x] == x) return x;
    return root[x] = Find(root[x]);   // 赋值实现压缩
}
```

对照 [data_structures/dsu_path_compression.cpp](data_structures/dsu_path_compression.cpp)。

#### 按秩合并（Union by Rank）

**思想**：永远把矮树挂到高树下，避免树长高。

```cpp
void Union(int x, int y) {
    int a = Find(x), b = Find(y);
    if (a == b) return;
    if (rank_[a] < rank_[b]) root[a] = b;
    else if (rank_[a] > rank_[b]) root[b] = a;
    else { root[b] = a; rank_[a]++; }   // 等高时任选，秩+1
}
```

**组合效果**：路径压缩 + 按秩合并 → 均摊复杂度 α(n) ≈ O(1)（反阿克曼函数，实际应用中 ≤ 4）。

**应用**：连通性问题、Kruskal MST、动态等价关系。

### 3.3 前缀和（Prefix Sum）

**一维前缀和**：

```cpp
vector<int> prefix(n + 1, 0);
for (int i = 0; i < n; i++)
    prefix[i + 1] = prefix[i] + a[i];

// 查询 [l, r] 区间和（闭区间）
int sum = prefix[r + 1] - prefix[l];     // O(1)
```

**关键**：`prefix[0] = 0` 作为哨兵，让查询公式统一。

**二维前缀和**：

```cpp
for (int i = 1; i <= m; i++)
    for (int j = 1; j <= n; j++)
        prefix[i][j] = prefix[i-1][j] + prefix[i][j-1]
                     - prefix[i-1][j-1] + a[i-1][j-1];

// 子矩阵 (r1,c1) 到 (r2,c2) 的和
int sum = prefix[r2+1][c2+1] - prefix[r1][c2+1]
        - prefix[r2+1][c1] + prefix[r1][c1];
```

**局限**：数组**不可修改**，修改后要 O(n) 重建。

### 3.4 差分数组（Difference Array）

**核心**：`diff[i] = a[i] - a[i-1]`，是前缀和的"逆"。

**区间加** `[l, r] += x` 在 O(1) 内完成：

```cpp
diff[l] += x;
diff[r + 1] -= x;
```

**还原原数组**：对 diff 做前缀和。

**应用**：大量区间修改、一次性查询最终结果。

### 3.5 线段树（Segment Tree）⭐ 重点

**核心**：每个节点表示一个区间的聚合值（和 / 最值 / …）。

**四大操作**：

- `build` O(n)：递归建树
- `update(pos, val)` O(log n)：单点更新
- `query(l, r)` O(log n)：区间查询
- Lazy 版支持 `updateRange(l, r, val)` O(log n)：区间更新

#### 单点更新版（对照 [data_structures/segment_tree.cpp:62-70](data_structures/segment_tree.cpp#L62-L70)）

```cpp
int n;
vector<long long> t;     // 通常开 4*n 大小

void build(vector<int>& a, int v, int tl, int tr) {
    if (tl == tr) { t[v] = a[tl]; return; }
    int tm = (tl + tr) / 2;
    build(a, v*2, tl, tm);
    build(a, v*2+1, tm+1, tr);
    t[v] = t[v*2] + t[v*2+1];
}

void update(int v, int tl, int tr, int pos, int val) {
    if (tl == tr) { t[v] = val; return; }
    int tm = (tl + tr) / 2;
    if (pos <= tm) update(v*2, tl, tm, pos, val);
    else           update(v*2+1, tm+1, tr, pos, val);
    t[v] = t[v*2] + t[v*2+1];
}

long long query(int v, int tl, int tr, int l, int r) {
    if (l > r) return 0;
    if (l == tl && r == tr) return t[v];
    int tm = (tl + tr) / 2;
    return query(v*2, tl, tm, l, min(r, tm))
         + query(v*2+1, tm+1, tr, max(l, tm+1), r);
}
```

**空间**：节点数最多 4n（保守估计）。

#### Lazy Propagation 思想（区间更新）

**核心**：区间加时，不立即更新子树，而是**打标记**（lazy tag）。下次查询/更新到子树时才下推。

**场景**：区间加、区间赋值等。

实战代码略长，建议先掌握单点更新版，理解后再补 Lazy。

### 3.6 树状数组（BIT / Fenwick Tree）

**核心思想**：用二进制表示法巧妙分块。

**lowbit**：`x & (-x)` 取 x 最低位的 1（及其后的 0）。

```cpp
// 例：x = 12 (1100) → -x (0100, 补码) → x&(-x) = 0100 = 4
```

**实现**（对照 [range_queries/fenwick_tree.cpp:74-80](range_queries/fenwick_tree.cpp#L74-L80)）：

```cpp
class BIT {
    vector<int> bit;
    int n;
public:
    BIT(int sz) : n(sz), bit(sz + 1, 0) {}

    void update(int i, int val) {        // i 是 1-indexed
        for (; i <= n; i += i & (-i))
            bit[i] += val;
    }

    int prefixSum(int i) {
        int s = 0;
        for (; i > 0; i -= i & (-i))
            s += bit[i];
        return s;
    }

    int rangeSum(int l, int r) {          // 查询 [l, r] 闭区间（1-indexed）
        return prefixSum(r) - prefixSum(l - 1);
    }
};
```

**关键细节**：

- BIT **必须 1-indexed**（0 的 lowbit 是 0，循环死锁）
- 代码极简，常数比线段树小
- 天然支持前缀和，差分版可扩展到区间修改

### 3.7 线段树 vs BIT 对比

| 维度 | 线段树 | BIT |
|------|--------|-----|
| 代码量 | 多（50+ 行） | 少（20 行） |
| 空间 | 4n | n+1 |
| 常数 | 大 | 小 |
| 支持操作 | 极灵活（和/max/min/区间赋值） | 主要前缀可逆操作（和） |
| 区间修改 | Lazy 支持 | 差分变种支持 |

**经验**：能用 BIT 的地方首选 BIT；复杂操作（区间赋值、区间 max）只能线段树。

### 3.8 Trie（前缀树，额外加餐）

**应用**：字符串前缀查询（208 题）。

```cpp
struct Trie {
    Trie* children[26] = {};
    bool isEnd = false;

    void insert(string& word) {
        Trie* node = this;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) node->children[idx] = new Trie();
            node = node->children[idx];
        }
        node->isEnd = true;
    }

    bool search(string& word) {
        Trie* node = this;
        for (char c : word) {
            int idx = c - 'a';
            if (!node->children[idx]) return false;
            node = node->children[idx];
        }
        return node->isEnd;
    }
};
```

---

## 四、STL 速查

本周主要手写结构，STL 没有直接对应的并查集/线段树/BIT。补充：

| API | 用法 |
|-----|------|
| `std::vector<int>(n, 0)` | 初始化长度 n、全 0 |
| `std::fill(v.begin(), v.end(), x)` | 填充 |
| `std::bitset<N>` | 固定大小位集，空间省 |
| `partial_sum` | 前缀和一键生成 |

---

## 五、LeetCode 题目指南

### 208. 实现 Trie 前缀树（Medium）

- **目的**：Trie 基础
- **提示**：`insert`、`search`、`startsWith` 三个方法

### 307. 区域和检索 - 数组可修改（Medium）

- **目的**：线段树 / BIT 模板题
- **提示**：两种都实现一遍

### 303. 区域和检索 - 数组不可变（Easy）

- **目的**：前缀和模板题
- **提示**：构造时预计算 prefix

### 547. 省份数量（Medium）

- **目的**：并查集经典应用
- **提示**：`isConnected[i][j]=1` 则 `union(i, j)`；最后数根节点数

### 200. 岛屿数量（Medium）

- **目的**：并查集（或 DFS/BFS）
- **提示**：对每个 '1' 与相邻 '1' 合并；最后数连通分量
- **对比**：Week 7 会用 DFS 解同一题

---

## 六、周日复盘清单

**理论层**：

1. 为什么路径压缩 + 按秩合并能做到近似 O(1)？
2. 前缀和和差分数组是什么关系？
3. 线段树节点为什么要开 4n？
4. BIT 的 `lowbit` 在干什么？
5. 线段树和 BIT 什么时候必须用前者？

**动手层**：

6. 能否闭卷写完整的并查集（含两种优化）？
7. 能否闭卷写单点更新的线段树？
8. 能否闭卷写 BIT 的 update + prefixSum？
9. 5 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的是？下周怎么解决？
12. 下周节奏要调整吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ LeetCode 题目中使用 `std::set` 存并查集（手写才有意义）

**允许**：

- ✅ 画图辅助（强烈推荐画线段树递归树）
- ✅ 查 cppreference
