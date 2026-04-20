# Week 7 学习手册：回溯 + DFS/BFS

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`backtracking/` + `graph/` 的 DFS/BFS
> 精读文件：5 个
> 配套 LeetCode：46, 47, 78, 39, 51, 37, 200, 695, 127（本周题量最大）

---

## 一、本周目标与产出

### 理解目标

- 回溯三要素：**路径、选择列表、结束条件**
- 回溯与 DFS 的关系（回溯是"带撤销"的 DFS）
- 剪枝技巧：排序后去重、提前终止、约束判断
- DFS vs BFS 的选型（连通性用 DFS，最短步数用 BFS）
- 网格题的四方向模板
- visited 的两种方式（数组标记 vs 原地修改）

### 动手目标

| 主题 | 典型题 | 复杂度 |
|------|--------|--------|
| 全排列 | 46, 47 | O(n · n!) |
| 子集 / 组合 | 78, 39 | O(2^n · n) |
| N 皇后 | 51 | O(n!) |
| 数独 | 37 | 回溯 + 约束 |
| 网格 DFS | 200, 695 | O(m·n) |
| BFS 最短路径 | 127 | O(m·n·L) |

### 产出清单

- `my-impl/week-07/*.cpp` — 6-7 份手写实现
- `spec/notes/week-07-backtracking-dfs-bfs.md` — 一页小结
- `spec/progress/week-07.md` — 进度表

---

## 二、每日安排（本周题量大，节奏紧凑）

### Day 1（周一）：回溯模板 + 全排列

- **上午 1h**：学习回溯通用模板
- **下午 1h**：**LeetCode 46**（全排列）— 回溯入门
- **晚上 1h**：**LeetCode 47**（全排列 II）— 去重技巧

### Day 2（周二）：子集 + 组合

- **上午 1h**：**LeetCode 78**（子集）
- **下午 1h**：**LeetCode 39**（组合总和）— 可重复选
- **晚上 1h**：总结 46/47/78/39 的**回溯模板变体**

### Day 3（周三）：N 皇后

- **上午 1.5h**：读 [backtracking/n_queens.cpp](backtracking/n_queens.cpp)
- **下午 1.5h**：闭卷手写 N 皇后
- **晚上**：**LeetCode 51**（N 皇后）

### Day 4（周四）：数独

- **上午 1h**：读 [backtracking/sudoku_solver.cpp](backtracking/sudoku_solver.cpp)
- **下午 1h**：理解约束：行、列、3×3 宫格
- **晚上 1h**：**LeetCode 37**（解数独）— 允许查题解

### Day 5（周五）：DFS/BFS 基础

- **上午 1h**：读 [graph/depth_first_search.cpp](graph/depth_first_search.cpp)
- **下午 1h**：读 [graph/breadth_first_search.cpp](graph/breadth_first_search.cpp)
- **晚上 1h**：**LeetCode 200**（岛屿数量 DFS 版）— 对比 Week 5 并查集版

### Day 6（周六）：网格 + BFS 最短路径

- **上午 1.5h**：**LeetCode 695**（岛屿的最大面积）
- **下午 1.5h**：**LeetCode 127**（单词接龙）— BFS 找最短变换

### Day 7（周日）：复盘

- **上午**：闭卷重写全排列 + N 皇后 + 岛屿数量
- **下午**：填写小结 + 进度表

---

## 三、回溯与 DFS/BFS 深度讲解

### 3.1 回溯通用模板

**三要素**：

- **路径（path）**：已做出的选择
- **选择列表**：当前可做的选择
- **结束条件**：到达叶节点（解完成或无解）

**模板**：

```cpp
void backtrack(Path& path, Choices& choices) {
    if (end_condition(path)) {
        result.push_back(path);
        return;
    }
    for (auto& c : choices) {
        if (!valid(c)) continue;        // 剪枝
        path.add(c);                     // 做选择
        backtrack(path, new_choices);
        path.remove(c);                  // 撤销选择（关键）
    }
}
```

**核心**：回溯 = DFS + **撤销操作**。撤销保证不同分支互相独立。

### 3.2 全排列（46 题）

**思路**：每个位置遍历所有未用过的数字。

```cpp
void backtrack(vector<int>& nums, vector<int>& path,
               vector<bool>& used, vector<vector<int>>& result) {
    if (path.size() == nums.size()) {
        result.push_back(path);
        return;
    }
    for (int i = 0; i < nums.size(); i++) {
        if (used[i]) continue;
        used[i] = true;
        path.push_back(nums[i]);
        backtrack(nums, path, used, result);
        path.pop_back();
        used[i] = false;
    }
}
```

**对比**：也可用"交换法"原地操作，但 `used` 数组更清晰。

### 3.3 全排列 II（47 题，含重复）

**去重关键**：排序后，**同层**跳过相同值。

```cpp
sort(nums.begin(), nums.end());
// 在 for 循环里加：
if (i > 0 && nums[i] == nums[i-1] && !used[i-1]) continue;
```

**解读**：`!used[i-1]` 表示上一个相同值在**同层**被撤销过（不是在父层被使用），所以当前跳过避免重复。

### 3.4 子集（78 题）与组合总和（39 题）

**子集思路**：每个元素选或不选。

```cpp
void backtrack(vector<int>& nums, int start, vector<int>& path,
               vector<vector<int>>& result) {
    result.push_back(path);              // 每个节点都是一个子集
    for (int i = start; i < nums.size(); i++) {
        path.push_back(nums[i]);
        backtrack(nums, i + 1, path, result);
        path.pop_back();
    }
}
```

**组合总和**：`start` 传 `i`（可重复选）而非 `i + 1`。

### 3.5 N 皇后（51 题）⭐ 经典

**状态**：逐行放棋子，每行选一列。

**`isSafe` 检查**（对照 [backtracking/n_queens.cpp:57-87](backtracking/n_queens.cpp#L57-L87)）：

- 同列无皇后
- 左上对角线无皇后
- 左下对角线无皇后
  （已放的都在左边，不用查右侧）

**优化**：用 3 个 `bool` 数组（cols / diag1 / diag2）代替遍历检查，O(1) 判断。

### 3.6 数独（37 题）

**回溯 + 约束**：

- 遍历每个空格，尝试 1-9
- `isValid`：当前数字在本行、本列、3×3 宫格是否已出现
- 填入 → 递归 → 失败则撤销

**剪枝优化**（进阶）：每次选候选数最少的空格先填。

### 3.7 DFS（Depth-First Search）

**递归版**：

```cpp
void dfs(int u, vector<vector<int>>& g, vector<bool>& visited) {
    visited[u] = true;
    // 处理 u
    for (int v : g[u]) {
        if (!visited[v]) dfs(v, g, visited);
    }
}
```

**迭代版（栈模拟）**：

```cpp
void dfs(int start, vector<vector<int>>& g) {
    vector<bool> visited(g.size(), false);
    stack<int> stk;
    stk.push(start);
    while (!stk.empty()) {
        int u = stk.top(); stk.pop();
        if (visited[u]) continue;
        visited[u] = true;
        // 处理 u
        for (int v : g[u])
            if (!visited[v]) stk.push(v);
    }
}
```

**风险**：深图递归可能栈溢出 → 改迭代。

### 3.8 BFS（Breadth-First Search）

**模板**（对照 [graph/breadth_first_search.cpp](graph/breadth_first_search.cpp)）：

```cpp
void bfs(int start, vector<vector<int>>& g) {
    vector<bool> visited(g.size(), false);
    queue<int> q;
    q.push(start);
    visited[start] = true;
    while (!q.empty()) {
        int u = q.front(); q.pop();
        // 处理 u
        for (int v : g[u]) {
            if (!visited[v]) {
                visited[v] = true;
                q.push(v);
            }
        }
    }
}
```

**核心特性**：**无权图的最短路径**（边数最少）。

**分层 BFS**：外层 while，内层 for 处理当前层所有节点（同 Week 4 层序遍历）。

### 3.9 DFS vs BFS 选型

| 场景 | 首选 |
|------|------|
| 连通性（岛屿数量） | 两者皆可，DFS 更简 |
| 最短路径（无权图） | BFS |
| 所有路径枚举 | DFS + 回溯 |
| 拓扑排序 | BFS（Kahn）或 DFS |
| 环检测 | DFS + 三色标记 |

### 3.10 网格 DFS 模板

```cpp
int dx[] = {-1, 1, 0, 0}, dy[] = {0, 0, -1, 1};  // 上下左右

void dfs(vector<vector<char>>& grid, int i, int j) {
    int m = grid.size(), n = grid[0].size();
    if (i < 0 || i >= m || j < 0 || j >= n || grid[i][j] != '1') return;
    grid[i][j] = '0';                      // 原地标记已访问
    for (int d = 0; d < 4; d++)
        dfs(grid, i + dx[d], j + dy[d]);
}
```

**技巧**：原地修改 `grid[i][j]` 避免额外 visited 数组。

### 3.11 双向 BFS（127 题优化）

从起点和终点**同时**做 BFS，两边相遇即找到最短。

**时间优化**：搜索空间从 `b^d` 降到 `2 · b^(d/2)`。

**实现**：两个集合 `beginSet` 和 `endSet`，每次选**较小**的集合扩展。

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::vector<int>` 当栈 | `push_back` / `pop_back` / `back` |
| `std::queue<T>` | BFS 标配 |
| `std::stack<T>` | DFS 迭代版 |
| `std::unordered_set<T>` | visited 替代 |
| `std::swap(a, b)` | 原地交换（交换法全排列） |

---

## 五、LeetCode 题目指南（9 道，本周主力）

### 46. 全排列（Medium）

- **目的**：回溯模板
- **提示**：`used[]` 数组或交换法

### 47. 全排列 II（Medium）

- **目的**：同层去重
- **提示**：排序 + `!used[i-1]` 剪枝

### 78. 子集（Medium）

- **目的**：每个节点即一个解
- **提示**：`result.push_back(path)` 放在递归入口

### 39. 组合总和（Medium）

- **目的**：元素可重复选
- **提示**：`start` 传 `i` 而非 `i+1`；剪枝：排序后超 target 即 break

### 51. N 皇后（Hard）

- **目的**：经典回溯
- **提示**：3 个 bool 数组记录列和两条对角线

### 37. 解数独（Hard）

- **目的**：回溯 + 约束
- **提示**：`rows[9][10]`, `cols[9][10]`, `boxes[9][10]` 三个二维数组

### 200. 岛屿数量（Medium）

- **目的**：网格 DFS
- **提示**：见第 3.10 节模板

### 695. 岛屿的最大面积（Medium）

- **目的**：DFS 返回值累计
- **提示**：`return 1 + dfs4 个方向`

### 127. 单词接龙（Hard）

- **目的**：BFS 最短路径
- **提示**：对每个单词枚举每个位置改 a-z；进阶用双向 BFS

---

## 六、周日复盘清单

**理论层**：

1. 回溯三要素是什么？
2. 同层去重的条件 `!used[i-1]` 为什么这么写？
3. DFS 递归会栈溢出怎么办？
4. 什么时候必须用 BFS 而不是 DFS？
5. 双向 BFS 为什么能指数级降低复杂度？

**动手层**：

6. 能否闭卷写全排列（带去重）？
7. 能否闭卷写 N 皇后？
8. 9 道 LeetCode 是否都 AC？

**反思层**：

9. 本周最大收获是？
10. 最困惑的是？下周怎么解决？
11. 下周贪心做好准备了吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ 回溯题不撤销直接提交（不是真回溯）
- ❌ 网格题拷贝 visited 数组（面试常要求原地）

**允许**：

- ✅ 37 题查题解
- ✅ 127 题先 AC 再优化双向 BFS
