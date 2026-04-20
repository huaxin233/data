# Week 10 学习手册：动态规划进阶

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`dynamic_programming/` 进阶
> 精读文件：5 个
> 配套 LeetCode：300, 1143, 72, 516, 64, 416

---

## 一、本周目标与产出

### 理解目标

- 二维 DP 的状态设计（字符串比对类）
- LIS 的 O(n²) 和 O(n log n) 两种做法
- 编辑距离（Levenshtein）三种编辑操作
- 区间 DP 的枚举顺序（长度 → 起点）
- 网格 DP 模板
- 子序列 vs 子串的区别

### 动手目标

| 题型 | 代表 | 难度 |
|------|------|------|
| LCS | 1143 | Medium |
| LIS（O(n²)） | 300 基础解 | Medium |
| LIS（O(n log n)） | 300 优化解 | ⭐ |
| 编辑距离 | 72 | Hard（实际 Medium 难度） |
| 区间 DP | 516 | Medium |
| 网格 DP | 64 | Medium |
| 背包变形 | 416 | Medium |

### 产出清单

- `my-impl/week-10/*.cpp` — 5-6 份手写实现
- `spec/notes/week-10-dp-advanced.md` — 一页小结
- `spec/progress/week-10.md` — 进度表

---

## 二、每日安排

### Day 1（周一）：LIS 基础

- **上午 1h**：读 [dynamic_programming/longest_increasing_subsequence.cpp](dynamic_programming/longest_increasing_subsequence.cpp)
- **下午 1.5h**：**LeetCode 300**（LIS，先用 O(n²) 做）
- **晚上 1h**：理解 `dp[i]` = 以 i 结尾的最长 LIS

### Day 2（周二）：LIS O(n log n) 优化 ⭐

- **上午 1h**：读 [dynamic_programming/longest_increasing_subsequence_nlogn.cpp](dynamic_programming/longest_increasing_subsequence_nlogn.cpp)
- **下午 2h**：理解 `tails` 数组 + 二分
- **晚上 1h**：闭卷手写 O(n log n) 版本

### Day 3（周三）：LCS

- **上午 1h**：读 [dynamic_programming/longest_common_subsequence.cpp](dynamic_programming/longest_common_subsequence.cpp)
- **下午 1h**：**LeetCode 1143**（LCS）
- **晚上 1h**：理解二维 DP 的画表法

### Day 4（周四）：编辑距离 ⭐

- **上午 1h**：读 [dynamic_programming/edit_distance.cpp](dynamic_programming/edit_distance.cpp)
- **下午 2h**：**LeetCode 72**（编辑距离）
- **晚上**：画出状态转移表（6×6 小例子）

### Day 5（周五）：区间 DP

- **上午 1h**：读 [dynamic_programming/longest_palindromic_subsequence.cpp](dynamic_programming/longest_palindromic_subsequence.cpp)
- **下午 1.5h**：**LeetCode 516**（最长回文子序列）
- **晚上 0.5h**：理解"长度 → 左端点"的枚举顺序

### Day 6（周六）：网格 DP + 背包变形

- **上午 1h**：**LeetCode 64**（最小路径和）— 网格 DP
- **下午 1.5h**：**LeetCode 416**（分割等和子集）— 背包变形
- **晚上 1h**：总结本周所有 DP 模板

### Day 7（周日）：复盘

- **上午**：闭卷重写 LCS + 编辑距离 + LIS O(n log n)
- **下午**：填写小结 + 进度表

---

## 三、DP 进阶深度讲解

### 3.1 二维 DP 框架（字符串比对类）

**通用状态**：`dp[i][j]` = 关于 A 的前 i 个和 B 的前 j 个的某种答案。

**常见转移**：

- 字符相等：`dp[i][j] = dp[i-1][j-1] + 1`
- 字符不等：`dp[i][j] = max/min(dp[i-1][j], dp[i][j-1], ...)`

**画表法**：初学者推荐用 6×6 小例子手工填表理解。

### 3.2 最长公共子序列 LCS（1143）

对照 [dynamic_programming/longest_common_subsequence.cpp](dynamic_programming/longest_common_subsequence.cpp)。

**状态**：`dp[i][j]` = `text1[0..i-1]` 和 `text2[0..j-1]` 的 LCS 长度。

**转移**：

```cpp
if (text1[i-1] == text2[j-1])
    dp[i][j] = dp[i-1][j-1] + 1;
else
    dp[i][j] = max(dp[i-1][j], dp[i][j-1]);
```

**边界**：`dp[0][j] = dp[i][0] = 0`。

**关键**：**子序列可以不连续**，所以"不匹配"时分别砍 A 或 B 的最后一个字符。

### 3.3 最长递增子序列 LIS（300）

#### O(n²) 基础版

**状态**：`dp[i]` = 以 `nums[i]` 结尾的最长递增子序列长度。

**转移**：

```cpp
for (int i = 0; i < n; i++) {
    dp[i] = 1;
    for (int j = 0; j < i; j++)
        if (nums[j] < nums[i])
            dp[i] = max(dp[i], dp[j] + 1);
}
return *max_element(dp.begin(), dp.end());
```

#### O(n log n) 优化版 ⭐

**关键数据结构**：`tails[k]` = 长度为 `k+1` 的 LIS 中**最小的末尾值**。

**性质**：`tails` 严格递增 → 可二分。

**算法**：对每个 `x`：

- 若 `x > tails.back()`：append
- 否则：用 `x` 替换 `tails` 中**第一个 >= x** 的位置（即 `lower_bound`）

**代码**：

```cpp
int lengthOfLIS(vector<int>& nums) {
    vector<int> tails;
    for (int x : nums) {
        auto it = lower_bound(tails.begin(), tails.end(), x);
        if (it == tails.end()) tails.push_back(x);
        else *it = x;
    }
    return tails.size();
}
```

**注意**：`tails` **不是** LIS 本身！只有长度是对的。

对照 [dynamic_programming/longest_increasing_subsequence_nlogn.cpp](dynamic_programming/longest_increasing_subsequence_nlogn.cpp)。

### 3.4 编辑距离（72）⭐

对照 [dynamic_programming/edit_distance.cpp](dynamic_programming/edit_distance.cpp)。

**状态**：`dp[i][j]` = 把 `word1[0..i-1]` 变成 `word2[0..j-1]` 的最少操作数。

**转移**：

```cpp
if (word1[i-1] == word2[j-1])
    dp[i][j] = dp[i-1][j-1];                            // 无需操作
else
    dp[i][j] = 1 + min({
        dp[i-1][j-1],    // 替换
        dp[i-1][j],      // 删除 word1[i-1]
        dp[i][j-1]       // 插入
    });
```

**边界**：

- `dp[0][j] = j`（插入 j 次）
- `dp[i][0] = i`（删除 i 次）

**画表**：以 `horse` 和 `ros` 为例，手工填 6×4 表格。

### 3.5 最长回文子序列（516）— 区间 DP

**状态**：`dp[i][j]` = `s[i..j]` 的最长回文子序列长度。

**转移**：

```cpp
if (s[i] == s[j])
    dp[i][j] = dp[i+1][j-1] + 2;
else
    dp[i][j] = max(dp[i+1][j], dp[i][j-1]);
```

**边界**：`dp[i][i] = 1`（单字符自身是回文）。

**枚举顺序**：⭐ **按长度从小到大**，再枚举起点。

```cpp
for (int len = 2; len <= n; len++)
    for (int i = 0; i + len - 1 < n; i++) {
        int j = i + len - 1;
        // ... 转移
    }
```

**原因**：`dp[i][j]` 依赖 `dp[i+1][j-1]`（长度更小的子区间）。

**区间 DP 通用模板**：

```
for len in 2..n:
    for i in 0..(n-len):
        j = i + len - 1
        dp[i][j] = f(dp[i+1][j-1], dp[i+1][j], dp[i][j-1], ...)
```

### 3.6 最小路径和（64）— 网格 DP

**状态**：`dp[i][j]` = 从 `(0,0)` 到 `(i,j)` 的最小路径和。

**转移**：

```cpp
dp[i][j] = grid[i][j] + min(dp[i-1][j], dp[i][j-1]);
```

**边界**：第一行和第一列只能一方向来。

**空间优化**：原地修改 `grid`（面试常要求）。

### 3.7 分割等和子集（416）— 0-1 背包变形

**问题**：数组能否分成两个等和子集。

**转化**：找一个子集，和为 `sum / 2`（sum 是奇数直接 false）。

**0-1 背包模型**：

- 容量：`sum / 2`
- 每个数是一个物品
- 判断能否**恰好装满**

**状态**：`dp[j]` = 能否凑出和为 j。

**转移**：`dp[j] = dp[j] || dp[j - nums[i]]`（倒序遍历 j）

```cpp
bool canPartition(vector<int>& nums) {
    int sum = accumulate(nums.begin(), nums.end(), 0);
    if (sum % 2) return false;
    int target = sum / 2;
    vector<bool> dp(target + 1, false);
    dp[0] = true;
    for (int x : nums)
        for (int j = target; j >= x; j--)
            dp[j] = dp[j] || dp[j - x];
    return dp[target];
}
```

### 3.8 子序列 vs 子串

| | 子序列（subsequence） | 子串（substring） |
|---|---------------------|------------------|
| 是否连续 | 不要求 | 要求 |
| 例（abc） | ac, bc, abc | ab, bc, abc |
| 典型 DP 状态 | dp[i][j]（两维度） | dp[i] 或双指针 |
| 本周题目 | LCS, LIS, 516 | 不涉及 |

**注意**：LeetCode 5（最长回文子串）是 Week 12 题，和 516（最长回文**子序列**）不同！

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::lower_bound(begin, end, x)` | 第一个 >= x 的迭代器 |
| `std::upper_bound(begin, end, x)` | 第一个 > x 的迭代器 |
| `std::accumulate(begin, end, init)` | `<numeric>`，求和 |
| `std::min({a, b, c})` | 初始化列表比较 |
| `std::vector<vector<int>>(m, vector<int>(n, v))` | 二维初始化 |

---

## 五、LeetCode 题目指南

### 300. 最长递增子序列（Medium）

- **目的**：LIS 两种解法都写
- **提示**：先 O(n²)，再 O(n log n)

### 1143. 最长公共子序列（Medium）

- **目的**：LCS 模板
- **提示**：二维 DP，字符相等 / 不等两种转移

### 72. 编辑距离（Hard）

- **目的**：经典二维 DP
- **提示**：三种操作 → 三个前驱状态取 min

### 516. 最长回文子序列（Medium）

- **目的**：区间 DP
- **提示**：按长度枚举；注意 vs 子串（5 题）

### 64. 最小路径和（Medium）

- **目的**：网格 DP
- **提示**：边界处理 + 原地优化

### 416. 分割等和子集（Medium）

- **目的**：背包变形
- **提示**：转化成"容量 sum/2 的 0-1 背包"

---

## 六、周日复盘清单

**理论层**：

1. 二维 DP 和一维 DP 的状态设计差别？
2. LIS O(n log n) 的 tails 数组为什么严格递增？
3. 编辑距离三种操作分别对应哪个前驱状态？
4. 区间 DP 为什么按长度枚举？
5. 子序列 vs 子串在 DP 设计上的差异？

**动手层**：

6. 能否闭卷写 LCS 和编辑距离？
7. 能否闭卷写 LIS 的 O(n log n) 版？
8. 能否闭卷写 516？
9. 6 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的是？下周怎么解决？
12. DP 模板库是否已形成？

---

## 附：本周禁用清单

**禁用**：

- ❌ 不画表就写 DP 转移
- ❌ 72 题不理解"为什么不等时是 min + 1"

**允许**：

- ✅ 先写 O(n²) LIS 再优化
- ✅ 72 / 516 在纸上填小例子的表
