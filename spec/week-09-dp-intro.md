# Week 9 学习手册：动态规划入门

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`dynamic_programming/` 入门部分
> 精读文件：5 个
> 配套 LeetCode：509, 70, 198, 213, 53, 121, 322

---

## 一、本周目标与产出

### 理解目标

- DP 三要素：**状态定义 / 转移方程 / 边界条件**
- 自顶向下（记忆化搜索）vs 自底向上（递推）
- 无后效性（Markov 性质）
- 最优子结构
- 空间优化（滚动数组、一维化）
- DP 识别信号：求最值 / 方案数 / 可行性

### 动手目标

| 题型 | 代表 | 难点 |
|------|------|------|
| 斐波那契 | 509, 70 | 起手式 |
| 线性 DP | 198, 213 | 选与不选 |
| 最大子数组 | 53 | Kadane |
| 股票 | 121 | 一次交易 |
| 完全背包 | 322 | 硬币可重复 |
| 0-1 背包 | — | 倒序遍历容量 |

### 产出清单

- `my-impl/week-09/*.cpp` — 5-6 份手写实现
- `spec/notes/week-09-dp-intro.md` — 一页小结
- `spec/progress/week-09.md` — 进度表

---

## 二、每日安排

### Day 1（周一）：DP 心智模型 + 斐波那契

- **上午 1h**：学习 DP 三要素
- **下午 1.5h**：读 [dynamic_programming/fibonacci_bottom_up.cpp](dynamic_programming/fibonacci_bottom_up.cpp)
- **晚上 1h**：**LeetCode 509**（斐波那契）+ **70**（爬楼梯）

### Day 2（周二）：打家劫舍

- **上午 1h**：读 [dynamic_programming/house_robber.cpp](dynamic_programming/house_robber.cpp)
- **下午 1h**：**LeetCode 198**（打家劫舍）— 理解"选与不选"
- **晚上 1h**：**LeetCode 213**（打家劫舍 II）— 环形数组的技巧

### Day 3（周三）：最大子数组 + Kadane

- **上午 1h**：读 [dynamic_programming/kadane.cpp](dynamic_programming/kadane.cpp)
- **下午 1h**：**LeetCode 53**（最大子数组和，DP 视角）— 对比 Week 6 分治
- **晚上 1h**：**LeetCode 121**（股票 I）— 只能一次交易

### Day 4（周四）：0-1 背包

- **上午 1.5h**：读 [dynamic_programming/0_1_knapsack.cpp](dynamic_programming/0_1_knapsack.cpp)
- **下午 1.5h**：闭卷手写二维 DP 版
- **晚上**：推导一维优化（为什么**倒序遍历容量**）

### Day 5（周五）：完全背包

- **上午 1h**：读 [dynamic_programming/coin_change.cpp](dynamic_programming/coin_change.cpp)
- **下午 1.5h**：**LeetCode 322**（零钱兑换）— 完全背包，**正序遍历容量**
- **晚上 0.5h**：总结 0-1 vs 完全背包的区别

### Day 6（周六）：综合 + 空间优化

- **上午**：重做所有题，尝试空间优化（滚动数组）
- **下午**：补漏——任何一道没 AC 的题重做
- **晚上**：看一道面试高频变形题（198 → 337 打家劫舍 III 树形 DP，作为延伸）

### Day 7（周日）：复盘

- **上午**：闭卷重写背包和打家劫舍
- **下午**：填写小结 + 进度表

---

## 三、DP 深度讲解

### 3.1 DP 三要素

1. **状态定义**：`dp[...]` 表示什么？最小覆盖所有子问题
2. **转移方程**：`dp[...] = f(dp[...前面的状态])`
3. **边界条件**：初始状态、dp[0] 的意义

**识别信号**：

- 求**最值**（min / max / 最长 / 最短）
- 求**方案数**
- 判断**可行性**（true / false）

### 3.2 自顶向下 vs 自底向上

#### 记忆化搜索（Top-Down）

```cpp
unordered_map<int, int> memo;
int solve(int n) {
    if (n <= 1) return n;
    if (memo.count(n)) return memo[n];
    return memo[n] = solve(n-1) + solve(n-2);
}
```

**优势**：写法贴近递归定义，易上手。
**劣势**：递归开销，可能栈溢出。

#### 递推（Bottom-Up）

```cpp
int solve(int n) {
    vector<int> dp(n + 1);
    dp[0] = 0; dp[1] = 1;
    for (int i = 2; i <= n; i++) dp[i] = dp[i-1] + dp[i-2];
    return dp[n];
}
```

**优势**：无递归开销，利于空间优化。
**劣势**：需先想清楚求解顺序。

**经验**：新手从记忆化开始，理解后改递推。

### 3.3 无后效性

**定义**：当前状态一旦确定，后续决策不受此前**过程**影响，只依赖**当前状态**。

**反例**：若"今天的决策"影响"明天的状态定义本身"，则需要扩展状态维度。

### 3.4 斐波那契（509）/ 爬楼梯（70）

**状态**：`dp[i]` = 第 i 项值 / 爬到第 i 阶的方法数。

**转移**：`dp[i] = dp[i-1] + dp[i-2]`。

**边界**：`dp[0] = 0, dp[1] = 1`（509）或 `dp[1] = 1, dp[2] = 2`（70）。

**空间优化**：只需两个变量。

```cpp
int climbStairs(int n) {
    int prev = 1, curr = 1;
    for (int i = 2; i <= n; i++) {
        int next = prev + curr;
        prev = curr;
        curr = next;
    }
    return curr;
}
```

### 3.5 打家劫舍（198）

**状态**：`dp[i]` = 前 i 间房屋能偷到的最大金额。

**转移**：`dp[i] = max(dp[i-1], dp[i-2] + nums[i])`
- 不偷 i：= dp[i-1]
- 偷 i：= dp[i-2] + nums[i]（不能偷相邻）

对照 [dynamic_programming/house_robber.cpp](dynamic_programming/house_robber.cpp)。

**空间优化**：同斐波那契。

### 3.6 打家劫舍 II（213）— 环形

**环形技巧**：头尾相邻 → 拆成两条线性：

- 情形 A：偷第 0 间 → 不偷 n-1 间 → 子问题 `[0, n-2]`
- 情形 B：不偷第 0 间 → 可以偷 n-1 间 → 子问题 `[1, n-1]`

取两者最大值。

### 3.7 最大子数组和（53）— Kadane

对照 [dynamic_programming/kadane.cpp](dynamic_programming/kadane.cpp)。

**状态**：`dp[i]` = 以 i 结尾的最大子数组和。

**转移**：`dp[i] = max(nums[i], dp[i-1] + nums[i])`
- 要么从 i 重新开始，要么延续之前

**答案**：`max(dp[0..n-1])`

**空间 O(1) 版**：

```cpp
int maxSubArray(vector<int>& nums) {
    int curr = nums[0], best = nums[0];
    for (int i = 1; i < nums.size(); i++) {
        curr = max(nums[i], curr + nums[i]);
        best = max(best, curr);
    }
    return best;
}
```

### 3.8 股票买卖 I（121）— 只能一次交易

**状态**：`minPrice` = 到目前为止最低价。

**转移**：每天用 `max(profit, prices[i] - minPrice)` 更新利润。

```cpp
int maxProfit(vector<int>& prices) {
    int minPrice = INT_MAX, profit = 0;
    for (int p : prices) {
        minPrice = min(minPrice, p);
        profit = max(profit, p - minPrice);
    }
    return profit;
}
```

**DP 视角**：`dp[i]` = 前 i 天最大利润；但用两变量更省。

### 3.9 0-1 背包 ⭐ 模板

对照 [dynamic_programming/0_1_knapsack.cpp](dynamic_programming/0_1_knapsack.cpp)。

**问题**：n 个物品各有重量 w[i] 和价值 v[i]，容量 W，每物品**最多选一次**，求最大价值。

**二维 DP**：

- **状态**：`dp[i][j]` = 前 i 个物品中，容量不超过 j 时的最大价值
- **转移**：
  - 不选 i：`dp[i-1][j]`
  - 选 i（需 `j >= w[i]`）：`dp[i-1][j - w[i]] + v[i]`
  - 取最大
- **边界**：`dp[0][j] = 0`, `dp[i][0] = 0`

```cpp
int knapsack(vector<int>& w, vector<int>& v, int W) {
    int n = w.size();
    vector<vector<int>> dp(n+1, vector<int>(W+1, 0));
    for (int i = 1; i <= n; i++)
        for (int j = 0; j <= W; j++) {
            dp[i][j] = dp[i-1][j];
            if (j >= w[i-1])
                dp[i][j] = max(dp[i][j], dp[i-1][j - w[i-1]] + v[i-1]);
        }
    return dp[n][W];
}
```

**一维优化**（倒序遍历容量）：

```cpp
vector<int> dp(W + 1, 0);
for (int i = 0; i < n; i++)
    for (int j = W; j >= w[i]; j--)        // ⭐ 倒序！
        dp[j] = max(dp[j], dp[j - w[i]] + v[i]);
```

**为什么倒序**：正序会用到"本轮已更新的 dp[j - w[i]]"，等价于物品被选多次，变成完全背包。

### 3.10 完全背包（322 零钱兑换）

对照 [dynamic_programming/coin_change.cpp](dynamic_programming/coin_change.cpp)。

**区别**：物品可选**无限次**。

**状态**：`dp[i]` = 凑出金额 i 的最少硬币数。

**转移**：`dp[i] = min(dp[i - coin] + 1)` for each coin。

**正序遍历容量**（才能重复选）：

```cpp
int coinChange(vector<int>& coins, int amount) {
    vector<int> dp(amount + 1, amount + 1);
    dp[0] = 0;
    for (int i = 1; i <= amount; i++)
        for (int coin : coins)
            if (i >= coin)
                dp[i] = min(dp[i], dp[i - coin] + 1);
    return dp[amount] > amount ? -1 : dp[amount];
}
```

**初始化 `amount + 1`**：相当于 INF，但避免 `dp[i-coin] + 1` 溢出。

### 3.11 0-1 vs 完全背包速记

| | 0-1 背包 | 完全背包 |
|---|----------|----------|
| 物品选次数 | 0 或 1 | 无限 |
| 一维 DP 遍历容量 | **倒序** | **正序** |
| 时间复杂度 | O(nW) | O(nW) |

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::vector<int>(n, val)` | 一维初始化 |
| `std::vector<vector<int>>(m, vector<int>(n, val))` | 二维初始化 |
| `std::min / std::max` | 取极值 |
| `std::min_element / std::max_element` | 返回迭代器 |
| `INT_MAX / INT_MIN` | `<climits>` |
| `std::numeric_limits<int>::max()` | `<limits>` |

---

## 五、LeetCode 题目指南

### 509. 斐波那契数（Easy）

- **目的**：DP 起手
- **提示**：空间 O(1)

### 70. 爬楼梯（Easy）

- **目的**：同斐波那契
- **提示**：边界不同

### 198. 打家劫舍（Medium）

- **目的**：线性 DP，选与不选
- **提示**：`dp[i] = max(dp[i-1], dp[i-2] + nums[i])`

### 213. 打家劫舍 II（Medium）

- **目的**：环形拆分
- **提示**：分两段 `[0, n-2]` 和 `[1, n-1]`

### 53. 最大子数组和（Medium）

- **目的**：Kadane
- **提示**：`dp[i] = max(nums[i], dp[i-1] + nums[i])`

### 121. 买卖股票最佳时机（Easy）

- **目的**：一次交易
- **提示**：记录最低价 + 更新最大利润

### 322. 零钱兑换（Medium）

- **目的**：完全背包
- **提示**：正序遍历容量；初始化 INF 技巧

---

## 六、周日复盘清单

**理论层**：

1. DP 三要素是什么？
2. 记忆化 vs 递推怎么选？
3. 什么是无后效性？
4. 0-1 背包一维化为什么要倒序？
5. 完全背包为什么要正序？

**动手层**：

6. 能否闭卷写 198 / 53？
7. 能否闭卷写 0-1 背包（二维 + 一维）？
8. 能否闭卷写 322？
9. 7 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的是？下周怎么解决？
12. DP 心智模型建立起来了吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ DP 题直接抄转移方程不理解推导
- ❌ 空间优化前没先写出"能跑通"的基础版

**允许**：

- ✅ 先记忆化再改递推（标准流程）
- ✅ 画状态转移图（强烈推荐）
