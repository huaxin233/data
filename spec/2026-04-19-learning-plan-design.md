# C++ 算法仓库 12 周深入学习方案

> 制定日期：2026-04-19
> 仓库：TheAlgorithms/C-Plus-Plus
> 方案：方案 A — 主题纵深式

---

## 学习者画像

- **当前水平**：C++ 入门 + 算法初学（熟悉基本语法，了解数组/链表，未系统学过算法）
- **学习目标**：面试刷题准备（校招/社招 LeetCode 导向）
- **时间投入**：每日 2-3 小时，每周 6 学习日 + 1 复盘日
- **总周期**：12 周（约 3 个月）
- **学习路径**：按仓库目录主题逐个纵深攻克

---

## 方案总览

本方案包含五个部分，共同构成完整闭环：

1. **学习方法论** — 每个主题的 5 步学习流程
2. **12 周课程表** — 具体到每周的主题安排
3. **精读文件清单** — 每周聚焦 3-5 个核心 `.cpp` 文件
4. **LeetCode 配套题单** — 每周同步实战
5. **进度追踪与里程碑** — 4 个关键节点 + 红旗警报

---

## 第一节：单主题学习方法论

每进入一个新主题，严格按以下 5 步执行：

### Step 1：概念先行（30 分钟）

先读主题 README 或可靠的外部资料（算法导论、OI Wiki、LeetBook），搞清：

- 这个算法/数据结构**是什么**
- 它**解决什么问题**
- 它的**核心思想**是什么

> 不提前做，直接读代码会卡在"为什么要这样写"上。

### Step 2：精读仓库代码（60 分钟）

从仓库该主题目录下选定 2-3 个代表性实现，逐行阅读：

- 关注 STL 用法（`std::vector`、`std::priority_queue` 等）
- 关注注释和边界处理
- 不懂的 STL 函数立刻查 [cppreference](https://en.cppreference.com/)

### Step 3：闭卷手写（60 分钟）

**关键环节**：关闭所有参考资料，从零写一遍核心算法，要求：

- 能独立跑通编译
- 能通过 2-3 个测试用例
- 写不出来就回 Step 2 再读，**直到能闭卷为止**

建议在自己的目录 `my-impl/` 下保存手写代码，区别于仓库原版。

### Step 4：LeetCode 配套题（每日 1-2 题）

按第四节题单刷题，原则：

- 先独立思考 30 分钟
- 超时查题解，但必须**第二天重写一遍**
- AC 后看官方题解和高赞讨论

### Step 5：周末小结（30 分钟）

每周日整理一页笔记，格式固定：

```markdown
## 本周主题：<主题名>

### 核心思想（一句话）
### 典型复杂度（时间 / 空间）
### 代表题型（3-5 种）
### 踩坑记录
### 仍需深化的点
```

**核心原则**：不求读完，求读懂。宁可一周只搞定排序，也别匆匆扫 10 个主题。

---

## 第二节：12 周课程表

### 第一阶段：入门基础（Week 1-3）

| 周次 | 主题 | 仓库目录 | 关键产出 |
|------|------|----------|----------|
| Week 1 | 排序算法 | `sorting/` | 手写 6 种核心排序 |
| Week 2 | 搜索 + 哈希 | `search/` `hashing/` | 手写二分变体 + 哈希表 |
| Week 3 | 基础数据结构 | `data_structures/` | 手写链表/栈/队列 |

### 第二阶段：数据结构进阶（Week 4-5）

| 周次 | 主题 | 仓库目录 | 关键产出 |
|------|------|----------|----------|
| Week 4 | 树结构 | `data_structures/` | BST / AVL / 堆 / Morris |
| Week 5 | 高级数据结构 | `data_structures/` `range_queries/` | 并查集 / 线段树 / 前缀和 |

### 第三阶段：核心算法思想（Week 6-9）

| 周次 | 主题 | 仓库目录 | 关键产出 |
|------|------|----------|----------|
| Week 6 | 递归 + 分治 | `divide_and_conquer/` | 分治思想内化 |
| Week 7 | 回溯 + DFS/BFS | `backtracking/` `graph/` | 8 皇后 / 数独 / 岛屿 |
| Week 8 | 贪心 | `greedy_algorithms/` | 4-5 个经典贪心 |
| Week 9 | 动态规划（入门） | `dynamic_programming/` | 线性 DP 范式 |

### 第四阶段：图论与进阶（Week 10-11）

| 周次 | 主题 | 仓库目录 | 关键产出 |
|------|------|----------|----------|
| Week 10 | 动态规划（进阶） | `dynamic_programming/` | 背包/LIS/编辑距离/区间 DP |
| Week 11 | 图论 | `graph/` | Dijkstra/Bellman-Ford/MST |

### 第五阶段：综合冲刺（Week 12）

| 周次 | 主题 | 仓库目录 | 关键产出 |
|------|------|----------|----------|
| Week 12 | 位运算 + 字符串 + 复盘 | `bit_manipulation/` `strings/` | 综合模拟面试 3 次 |

---

## 第三节：每周精读文件清单

> 每个文件精读目标：**闭卷能复现核心逻辑**，不是读一遍就过。

### Week 1 — sorting（排序）

- `sorting/bubble_sort.cpp` — 最朴素，体会交换思想
- `sorting/insertion_sort.cpp` — 面试高频
- `sorting/selection_sort_iterative.cpp` — 选择排序
- `sorting/merge_sort.cpp` — 分治经典
- `sorting/quick_sort.cpp` — 分治 + 分区
- `sorting/heap_sort.cpp` — 联动堆数据结构
- `sorting/counting_sort.cpp` — 非比较排序代表

### Week 2 — search + hashing（搜索 + 哈希）

- `search/linear_search.cpp`
- `search/binary_search.cpp` — 重点，吃透左闭右闭/左闭右开两种写法
- `search/ternary_search.cpp`
- `hashing/chaining.cpp` — 拉链法
- `hashing/linear_probing_hash_table.cpp` — 开放寻址

### Week 3 — 基础数据结构

- `data_structures/linked_list.cpp`
- `data_structures/doubly_linked_list.cpp`
- `data_structures/stack_using_array.cpp`
- `data_structures/queue_using_linked_list.cpp`
- `data_structures/reverse_a_linked_list.cpp` — 面试必考

### Week 4 — 树

- `data_structures/binary_search_tree.cpp`
- `data_structures/avltree.cpp`
- `data_structures/binaryheap.cpp`
- `data_structures/morrisinorder.cpp` — O(1) 空间遍历
- `data_structures/rb_tree.cpp`（选读，难度大，面试很少考手写）

### Week 5 — 高级数据结构

- `data_structures/disjoint_set.cpp`
- `data_structures/dsu_path_compression.cpp` — 并查集优化
- `data_structures/segment_tree.cpp`
- `range_queries/prefix_sum_array.cpp`
- `range_queries/fenwick_tree.cpp` — 树状数组

### Week 6 — 分治

- `divide_and_conquer/karatsuba_algorithm_for_fast_multiplication.cpp` — 了解即可
- 重做 Week 1 的 merge_sort / quick_sort，**分析分治三段式（分 / 治 / 合）**

### Week 7 — 回溯 + DFS/BFS

- `backtracking/n_queens.cpp`
- `backtracking/subset_sum.cpp`
- `backtracking/sudoku_solver.cpp`
- `graph/breadth_first_search.cpp`
- `graph/depth_first_search.cpp`

### Week 8 — 贪心

- `greedy_algorithms/huffman.cpp`
- `greedy_algorithms/knapsack.cpp`
- `greedy_algorithms/kruskals_minimum_spanning_tree.cpp`
- `greedy_algorithms/jump_game.cpp`

### Week 9 — DP 入门

- `dynamic_programming/fibonacci_bottom_up.cpp` — DP 起手式
- `dynamic_programming/kadane.cpp` — 最大子数组
- `dynamic_programming/0_1_knapsack.cpp`
- `dynamic_programming/coin_change.cpp`
- `dynamic_programming/house_robber.cpp`

### Week 10 — DP 进阶

- `dynamic_programming/longest_common_subsequence.cpp`
- `dynamic_programming/longest_increasing_subsequence_nlogn.cpp`
- `dynamic_programming/edit_distance.cpp`
- `dynamic_programming/longest_palindromic_subsequence.cpp`

### Week 11 — 图论

- `graph/dijkstra.cpp` — 单源最短路
- `graph/bellman_ford.cpp` — 允许负权
- `graph/floyd_warshall.cpp` — 多源最短路
- `graph/kruskal.cpp` — MST 并查集
- `graph/prim.cpp` — MST 优先队列
- `graph/kosaraju.cpp` — 强连通分量

### Week 12 — 位运算 + 字符串

- `bit_manipulation/count_of_set_bits.cpp`
- `bit_manipulation/power_of_2.cpp`
- `bit_manipulation/gray_code.cpp`
- `strings/knuth_morris_pratt.cpp` — KMP
- `strings/rabin_karp.cpp` — 字符串哈希

---

## 第四节：LeetCode 配套题单

> 原则：每日 1-2 题，每周 5-10 题；卡壳超过 30 分钟查题解，但次日必须重写。

| 周次 | 主题 | LeetCode 题号 |
|------|------|---------------|
| Week 1 | 排序 | 912, 215, 88, 147, 148 |
| Week 2 | 搜索 + 哈希 | 704, 33, 34, 74, 1, 49, 128 |
| Week 3 | 基础数据结构 | 206, 21, 141, 142, 20, 232, 155 |
| Week 4 | 树 | 94, 144, 145, 102, 98, 104, 110, 236 |
| Week 5 | 高级数据结构 | 208, 307, 303, 547, 200 |
| Week 6 | 分治 | 53, 169, 23, 4 |
| Week 7 | 回溯 + DFS/BFS | 46, 47, 78, 39, 51, 37, 200, 695, 127 |
| Week 8 | 贪心 | 55, 45, 122, 763, 435, 621 |
| Week 9 | DP 入门 | 509, 70, 198, 213, 53, 121, 322 |
| Week 10 | DP 进阶 | 300, 1143, 72, 516, 64, 416 |
| Week 11 | 图论 | 743, 787, 1584, 207, 210 |
| Week 12 | 位运算 + 字符串 | 136, 191, 190, 338, 28, 5, 647 |

**错题本规则**：

- 每卡壳一题必须记录（题号 + 卡点 + 最终思路）
- 每周日复盘全部卡壳题
- 每月底重做历史错题

---

## 第五节：进度追踪与里程碑

### 周度追踪

每周在 `spec/progress/` 下建立 `week-XX.md`，格式：

```markdown
# Week X 进度

## 精读文件
- [x] sorting/bubble_sort.cpp
- [ ] sorting/heap_sort.cpp

## 手写实现
- [x] 冒泡排序 → my-impl/bubble.cpp
- [ ] 堆排序

## LeetCode
- [x] 912 快排 — AC
- [x] 215 第 K 大 — 卡壳 45min，查题解后次日重写 AC

## 一句话小结
本周最大收获：理解了比较排序的下界 O(n log n)
最大困惑：堆排序的下沉/上浮边界条件
```

### 阶段里程碑（4 个关键节点）

#### M1（Week 3 末）—— 基础关

- ✅ 能闭卷手写 6 种核心排序（冒泡/插入/选择/归并/快排/堆排）
- ✅ 能写出二分搜索的左闭右闭 / 左闭右开两种版本
- ✅ 能独立实现链表反转 / 去重

#### M2（Week 5 末）—— 数据结构关

- ✅ 能独立实现 BST 的插入/删除/查找
- ✅ 能独立实现小顶堆并用于 Top-K
- ✅ 能独立实现并查集（带路径压缩）
- ✅ 能独立实现线段树的 build/update/query

#### M3（Week 9 末）—— 算法范式关

- ✅ 掌握 DFS/BFS/回溯/贪心/DP 五大范式
- ✅ 能独立解 LeetCode Medium 难度题目（正确率 ≥ 70%）
- ✅ 能识别题目应该用哪种范式

#### M4（Week 12 末）—— 综合关

- ✅ 图论四大算法（Dijkstra/Bellman-Ford/Kruskal/Prim）能手写
- ✅ 位运算常见技巧熟练
- ✅ 完成 3 次 1 小时模拟面试

### 红旗警报（出现立即调整）

| 信号 | 应对 |
|------|------|
| 某周 LeetCode 正确率 < 50% | 退回该主题重学，不要硬推进 |
| 连续 3 天没动进度 | 降级到轻量节奏（每日 1 小时），避免彻底中断 |
| 精读代码 50%+ 看不懂 | 补该主题的基础理论课（B 站、书） |
| 手写代码跑不过测试 | 不要查参考，单步调试找问题 |

### 每周日复盘三问（30 分钟）

1. 本周学到的**最重要的一个概念**是？
2. 最让我**困惑的是**？下周怎么解决？
3. 下周要**调整节奏**吗？（加速 / 减速 / 保持）

---

## 附录 A：仓库目录快速索引

| 目录 | 涉及周次 | 说明 |
|------|----------|------|
| `sorting/` | W1 | 各种排序算法 |
| `search/` | W2 | 搜索算法 |
| `hashing/` | W2 | 哈希表实现 |
| `data_structures/` | W3, W4, W5 | 基础/树/高级数据结构 |
| `range_queries/` | W5 | 区间查询（前缀和/线段树/BIT） |
| `divide_and_conquer/` | W6 | 分治算法 |
| `backtracking/` | W7 | 回溯算法 |
| `graph/` | W7, W11 | 图论 |
| `greedy_algorithms/` | W8 | 贪心 |
| `dynamic_programming/` | W9, W10 | 动态规划 |
| `bit_manipulation/` | W12 | 位运算 |
| `strings/` | W12 | 字符串算法 |

## 附录 B：本方案不涉及的目录（可选延伸）

- `ciphers/` — 密码学，面试不考
- `machine_learning/` — 机器学习算法实现，非面试主题
- `numerical_methods/` — 数值方法，非主流面试
- `physics/`, `graphics/`, `games/` — 应用专题，按兴趣选读
- `cpu_scheduling_algorithms/` — 操作系统方向专用
- `math/`, `geometry/`, `probability/` — 如有余力可做补充阅读

---

## 如何使用本方案

1. **Week 1 启动**：从 Step 1 开始，不要跳步
2. **建立目录**：
   - `my-impl/` — 自己手写的实现
   - `spec/progress/` — 每周进度记录
   - `spec/notes/` — 每个主题的一页小结
3. **每周日复盘**：严格执行三问
4. **遇到红旗**：立刻调整，不要硬撑
5. **里程碑检查**：每到阶段末对照验收项目
