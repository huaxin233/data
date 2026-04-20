# Week 8 学习手册：贪心算法

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`greedy_algorithms/`
> 精读文件：4 个
> 配套 LeetCode：55, 45, 122, 763, 435, 621

---

## 一、本周目标与产出

### 理解目标

- 贪心的定义：每步取局部最优，期望全局最优
- 贪心正确性**必须证明**（交换论证 / 反证法 / 数学归纳）
- 贪心 vs DP 的本质区别
- 常见贪心套路：按某个维度排序后扫描
- 区间调度问题的经典处理

### 动手目标

| 题型 | 代表 LeetCode | 核心思路 |
|------|---------------|----------|
| 跳跃游戏 | 55, 45 | 维护最远可达位置 |
| 股票买卖 | 122 | 所有上升段累加 |
| 区间调度 | 435, 763 | 按右端点排序 |
| 任务调度 | 621 | 最大频率决定周期 |
| 哈夫曼编码 | — | 贪心选最小两个合并 |
| Kruskal MST | — | 贪心选最小边 |

### 产出清单

- `my-impl/week-08/*.cpp` — 4-5 份手写实现
- `spec/notes/week-08-greedy.md` — 一页小结
- `spec/progress/week-08.md` — 进度表

---

## 二、每日安排

### Day 1（周一）：跳跃游戏

- **上午 1h**：学习贪心定义和证明方法
- **下午 1.5h**：**LeetCode 55**（跳跃游戏，判可达）
- **晚上 1h**：**LeetCode 45**（跳跃游戏 II，最少步数）— 两种贪心各不同

### Day 2（周二）：股票 + 区间问题入门

- **上午 1h**：**LeetCode 122**（买卖股票最佳时机 II）— 允许多次交易
- **下午 1h**：区间问题的"按右端点排序"思想
- **晚上 1h**：**LeetCode 435**（无重叠区间）

### Day 3（周三）：区间调度 + 划分

- **上午 1.5h**：**LeetCode 763**（划分字母区间）
- **下午 1h**：理解区间合并与区间划分的区别
- **晚上 1h**：总结区间问题套路

### Day 4（周四）：任务调度

- **上午 1.5h**：**LeetCode 621**（任务调度器）
- **下午 1h**：理解为什么"最大频率"决定最短周期
- **晚上**：复习 Day 1-3 的贪心

### Day 5（周五）：哈夫曼编码

- **上午 1.5h**：读 [greedy_algorithms/huffman.cpp](greedy_algorithms/huffman.cpp)
- **下午 1.5h**：理解前缀码 + 优先队列实现
- **晚上**：尝试自写一份简化版哈夫曼

### Day 6（周六）：Kruskal MST + 综合

- **上午 1h**：读 [greedy_algorithms/kruskals_minimum_spanning_tree.cpp](greedy_algorithms/kruskals_minimum_spanning_tree.cpp)
- **下午 1h**：读 [greedy_algorithms/jump_game.cpp](greedy_algorithms/jump_game.cpp)
- **晚上 1h**：读 [greedy_algorithms/knapsack.cpp](greedy_algorithms/knapsack.cpp)（贪心分数背包）

### Day 7（周日）：复盘

- **上午**：闭卷重写 55 / 45 / 435
- **下午**：填写小结 + 进度表

---

## 三、贪心深度讲解

### 3.1 贪心的定义与适用条件

**定义**：每一步选择当前最优（局部最优），不回退不反悔。

**适用条件**（二选一的证明思路）：

1. **最优子结构**：问题的最优解包含子问题的最优解
2. **贪心选择性**：每步的局部最优可以推出全局最优

**致命误解**：贪心不等于"看起来对就做"。**没有证明的贪心是错的**。

### 3.2 贪心 vs DP

| 维度 | 贪心 | DP |
|------|------|-----|
| 决策 | 当下最优，不回头 | 考虑所有子问题 |
| 时间 | 通常 O(n log n) 或 O(n) | O(n²) 或更高 |
| 空间 | O(1) 或 O(n) | O(n) 或 O(n²) |
| 适用 | 有贪心性质 | 有最优子结构 + 重叠 |
| 风险 | 不证不能用 | 状态设计错会超时 |

**关系**：**贪心是 DP 的特例**——当每一步的最优选择不依赖后续时，DP 退化为贪心。

### 3.3 贪心正确性证明方法

#### 交换论证（Exchange Argument）

假设最优解 OPT 不采用贪心选择 G，把 OPT 中某个选择换成 G，证明结果不变差 → G 也是最优。

#### 反证法

假设贪心错，存在更优解，推出矛盾。

#### 数学归纳

对问题规模归纳：规模 1 贪心对；假设规模 n-1 对，证明规模 n 也对。

**建议**：每做一道贪心题，**试着证明**一下，即使是口头。这习惯养成后，能快速识别贪心陷阱。

### 3.4 跳跃游戏（55 题）

**问题**：非负整数数组，起点 0，每步最多跳 `nums[i]` 步。能否到达末尾？

**贪心**：维护"当前最远可达距离" `reach`。

```cpp
bool canJump(vector<int>& nums) {
    int reach = 0;
    for (int i = 0; i < nums.size(); i++) {
        if (i > reach) return false;               // 走不到 i
        reach = max(reach, i + nums[i]);
        if (reach >= nums.size() - 1) return true; // 提前返回
    }
    return true;
}
```

**证明**：若位置 i 可达，则 [0, i + nums[i]] 都可达。`reach` 就是到目前为止能到的最远点。

### 3.5 跳跃游戏 II（45 题）

**问题**：同上，保证可达，求最少步数。

**贪心**：维护"本次跳跃的最远边界"和"下一跳能到的最远"。

```cpp
int jump(vector<int>& nums) {
    int steps = 0, end = 0, farthest = 0;
    for (int i = 0; i < nums.size() - 1; i++) {
        farthest = max(farthest, i + nums[i]);
        if (i == end) {                            // 到达当前跳跃边界
            steps++;
            end = farthest;
        }
    }
    return steps;
}
```

**解读**：在 `[0, end]` 扫描时记录下一跳最远点 `farthest`；走到 `end` 时必须跳（`steps++`），下次边界就是 `farthest`。

### 3.6 股票买卖 II（122 题）

**问题**：允许多次交易，求最大利润。

**贪心**：**每一段上升都吃下**。

```cpp
int maxProfit(vector<int>& prices) {
    int profit = 0;
    for (int i = 1; i < prices.size(); i++)
        if (prices[i] > prices[i-1])
            profit += prices[i] - prices[i-1];
    return profit;
}
```

**为什么对**：任何连续上升段 `a < b < c` 的总利润 `c - a = (b - a) + (c - b)`，等价于分两次交易。

### 3.7 无重叠区间（435 题）

**问题**：给定区间集合，最少删除多少使剩下的无重叠。

**等价**：最多选多少个互不重叠的区间。

**贪心**：**按右端点排序**，每次选右端点最小的兼容区间。

```cpp
int eraseOverlapIntervals(vector<vector<int>>& intervals) {
    sort(intervals.begin(), intervals.end(), [](auto& a, auto& b){
        return a[1] < b[1];
    });
    int count = 1, end = intervals[0][1];
    for (int i = 1; i < intervals.size(); i++) {
        if (intervals[i][0] >= end) {
            count++;
            end = intervals[i][1];
        }
    }
    return intervals.size() - count;
}
```

**证明**（交换论证）：若最优解没选右端点最小的 `g`，把最优解的第一个区间替换为 `g`，不会与后续冲突（因为 `g.right` 更小）→ 替换后不变差。

### 3.8 划分字母区间（763 题）

**问题**：把字符串划分成尽可能多的片段，每个字母只出现在一个片段中。

**贪心**：先记录每个字母**最后出现的位置**，然后扫描时维护"当前片段必须扩展到的最远位置"。

```cpp
vector<int> partitionLabels(string s) {
    int last[26] = {};
    for (int i = 0; i < s.size(); i++) last[s[i] - 'a'] = i;

    vector<int> result;
    int start = 0, end = 0;
    for (int i = 0; i < s.size(); i++) {
        end = max(end, last[s[i] - 'a']);
        if (i == end) {                            // 当前片段结束
            result.push_back(end - start + 1);
            start = i + 1;
        }
    }
    return result;
}
```

### 3.9 任务调度器（621 题）

**问题**：相同任务间隔至少 n，最少多少时间完成所有任务。

**贪心关键**：**出现次数最多的任务决定最短周期**。

**公式**：`ans = max(tasks.size(), (maxCount - 1) * (n + 1) + 同频数)`

```cpp
int leastInterval(vector<char>& tasks, int n) {
    int cnt[26] = {};
    for (char c : tasks) cnt[c - 'A']++;
    int maxCount = *max_element(cnt, cnt + 26);
    int sameFreq = count(cnt, cnt + 26, maxCount);
    return max((int)tasks.size(), (maxCount - 1) * (n + 1) + sameFreq);
}
```

### 3.10 哈夫曼编码

**问题**：给字符频率，构造最优前缀编码（频率越高编码越短）。

**贪心**（对照 [greedy_algorithms/huffman.cpp](greedy_algorithms/huffman.cpp)）：

1. 所有字符作为叶子放入优先队列（小顶堆）
2. 每次取频率最小的两个，合并成一个新节点（频率相加）
3. 新节点放回队列
4. 重复直到只剩一个节点（根）

**实现要点**：

```cpp
priority_queue<Node*, vector<Node*>, greater<Node*>> pq;
while (pq.size() > 1) {
    Node* a = pq.top(); pq.pop();
    Node* b = pq.top(); pq.pop();
    Node* merged = new Node(a->freq + b->freq);
    merged->left = a;
    merged->right = b;
    pq.push(merged);
}
```

### 3.11 Kruskal 最小生成树（贪心选边）

**思路**：

1. 所有边按权重排序
2. 依次考虑每条边，若不形成环（用并查集判），则加入 MST
3. 边数达到 `V - 1` 即完成

对照 [greedy_algorithms/kruskals_minimum_spanning_tree.cpp](greedy_algorithms/kruskals_minimum_spanning_tree.cpp)。Week 11 图论会再次遇到。

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::sort(begin, end, cmp)` | 自定义比较 |
| `std::priority_queue<T>` | 大顶堆（默认） |
| `std::priority_queue<T, vector<T>, greater<T>>` | 小顶堆 |
| lambda 比较器 | `[](auto& a, auto& b){ return a[1] < b[1]; }` |
| `std::max_element` / `std::min_element` | 找最值迭代器 |
| `std::count(begin, end, val)` | 计数 |

---

## 五、LeetCode 题目指南

### 55. 跳跃游戏（Medium）

- **目的**：贪心入门
- **提示**：维护 `reach = max(reach, i + nums[i])`

### 45. 跳跃游戏 II（Medium）

- **目的**：两层贪心（当前边界 + 下跳最远）
- **提示**：`i == end` 时 `steps++`

### 122. 买卖股票最佳时机 II（Easy）

- **目的**：所有正差价累加
- **提示**：一行贪心

### 435. 无重叠区间（Medium）

- **目的**：按右端点排序
- **提示**：结果 = 总数 - 最多不重叠数

### 763. 划分字母区间（Medium）

- **目的**：最后出现位置 + 扫描
- **提示**：预处理每个字母最后位置

### 621. 任务调度器（Medium）

- **目的**：最大频率决定最短周期
- **提示**：公式法或最大堆模拟

---

## 六、周日复盘清单

**理论层**：

1. 贪心和 DP 的本质区别？
2. 贪心正确性的三种证明方法各适合什么场景？
3. 为什么 55 和 45 都是贪心但写法不同？
4. 区间调度为什么按右端点排序而不是左端点？
5. 哈夫曼编码为什么用小顶堆？

**动手层**：

6. 能否闭卷写 55 / 45 / 435？
7. 6 道 LeetCode 是否都 AC？
8. 每道题能否说出证明思路？

**反思层**：

9. 本周最大收获是？
10. 最困惑的是？下周怎么解决？
11. 下周 DP 入门做好准备了吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ 不证明就下贪心结论
- ❌ 435 按左端点排序（错误做法）

**允许**：

- ✅ 621 用最大堆模拟（对照公式法验证）
- ✅ 查 cppreference
