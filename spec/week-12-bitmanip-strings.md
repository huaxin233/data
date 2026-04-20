# Week 12 学习手册：位运算 + 字符串 + 综合冲刺

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`bit_manipulation/` + `strings/` + **M4 综合冲刺 + 3 次模拟面试**
> 精读文件：5 个
> 配套 LeetCode：136, 191, 190, 338, 28, 5, 647

---

## 一、本周目标与产出

### 理解目标

- 位运算 6 种基础操作（`& | ^ ~ << >>`）
- 常用位运算技巧：`n & (n-1)` 清除最低位 1；`n & (-n)` 取 lowbit
- XOR 的三大性质（`a^a=0`, `a^0=a`, 结合律）
- KMP 的 next 数组本质
- Rabin-Karp 的滚动哈希
- 回文问题的三种做法（中心扩展 / DP / Manacher）
- **整个 12 周的知识图谱**

### 动手目标

| 题型 | 代表 | 关键思路 |
|------|------|----------|
| 位运算基础 | 136, 191, 338 | XOR / 位计数 |
| 位反转 | 190 | 分治或查表 |
| 字符串匹配 | 28 | KMP 或朴素 |
| 回文子串 | 5, 647 | 中心扩展 |

### 产出清单

- `my-impl/week-12/*.cpp` — 5-6 份手写实现
- `spec/notes/week-12-bitmanip-strings.md` — 一页小结
- `spec/progress/week-12.md` — 进度表 + **12 周总复盘**
- **3 次模拟面试记录**

---

## 二、每日安排

### Day 1（周一）：位运算基础

- **上午 1h**：6 种基础操作温习
- **下午 1h**：读 [bit_manipulation/power_of_2.cpp](bit_manipulation/power_of_2.cpp) + [bit_manipulation/count_of_set_bits.cpp](bit_manipulation/count_of_set_bits.cpp)
- **晚上 1h**：**LeetCode 136**（只出现一次的数字）+ **191**（位 1 的个数）

### Day 2（周二）：位反转 + 计数进阶

- **上午 1h**：读 [bit_manipulation/gray_code.cpp](bit_manipulation/gray_code.cpp)
- **下午 1h**：**LeetCode 190**（颠倒二进制位）— 尝试分治
- **晚上 1h**：**LeetCode 338**（比特位计数）— DP 思路

### Day 3（周三）：字符串匹配 — 朴素 + KMP

- **上午 1h**：读 [strings/knuth_morris_pratt.cpp](strings/knuth_morris_pratt.cpp)
- **下午 2h**：理解 next 数组构造
- **晚上 1h**：**LeetCode 28**（找字符串第一个匹配）— 朴素 + KMP 各写一次

### Day 4（周四）：Rabin-Karp + 回文

- **上午 1h**：读 [strings/rabin_karp.cpp](strings/rabin_karp.cpp)
- **下午 1h**：**LeetCode 5**（最长回文子串）— 中心扩展
- **晚上 1h**：**LeetCode 647**（回文子串总数）— 同一思路

### Day 5（周五）：**模拟面试 #1**（1 小时）

- **上午**：选 3 道题（1 Easy + 1 Medium + 1 Hard），限时 1 小时
- **下午**：复盘——做不出的地方是什么？查补漏
- **晚上**：补上没 AC 的题

**推荐出题范围**（从 M1-M4 复盘）：

- Easy：206 反转链表 / 20 有效括号
- Medium：215 第 K 大 / 102 层序 / 300 LIS / 207 课程表
- Hard：23 合并 K 链表 / 72 编辑距离 / 51 N 皇后

### Day 6（周六）：**模拟面试 #2**（1 小时）+ **模拟面试 #3**（1 小时）

- **上午**：模拟面试 #2
- **下午**：模拟面试 #3
- **晚上**：总复盘——12 周里哪些主题最薄弱？

### Day 7（周日）：**12 周总复盘**

- **上午 2h**：**对照 M1/M2/M3/M4 四个里程碑逐项自评**
- **下午**：填写 `spec/progress/week-12.md` 总复盘板块
- **晚上**：做一份"给面试官的 5 分钟自我介绍 + 算法经验"演练

---

## 三、位运算 + 字符串深度讲解

### 3.1 位运算 6 种基础操作

| 操作 | 符号 | 含义 | 典型用法 |
|------|------|------|----------|
| 与 | `&` | 双方都 1 才 1 | 掩码、lowbit、判奇偶 |
| 或 | `\|` | 任一为 1 | 合并标志位 |
| 异或 | `^` | 不同才 1 | 去重、加密 |
| 非 | `~` | 反转每一位 | 常配合与使用 |
| 左移 | `<<` | 低位补 0 | 乘 2 的幂 |
| 右移 | `>>` | 高位补符号 | 除 2 的幂 |

**常见误区**：

- C++ 中 `1 << 31` 对 `int` 是 UB（超 int 表示范围）→ 用 `1LL << 31`
- 右移负数对无符号/有符号不同（面试用 `unsigned` 更安全）

### 3.2 常用位运算技巧

#### n & (n - 1)：清除最低位的 1

例：`12 = 1100`，`11 = 1011`，`12 & 11 = 1000 = 8`。

**应用**：

- 判断是否 2 的幂：`n > 0 && (n & (n-1)) == 0`
- 计算 1 的个数（Brian Kernighan）：`while (n) { cnt++; n &= n-1; }`

#### n & (-n)：取最低位的 1

例：`12 = 1100`，`-12`（补码）`= ...11110100`，`12 & -12 = 0100 = 4`。

**应用**：树状数组（BIT）的 lowbit。

#### XOR 性质

- `a ^ a = 0`
- `a ^ 0 = a`
- 满足交换律、结合律

**应用**：

- 136 只出现一次的数字：所有数 XOR 起来，重复的抵消
- 无临时变量交换：`a ^= b; b ^= a; a ^= b;`（但教育意义大于实用）

### 3.3 判断 2 的幂（power_of_2）

对照 [bit_manipulation/power_of_2.cpp](bit_manipulation/power_of_2.cpp)。

```cpp
bool isPowerOfTwo(int n) {
    return n > 0 && (n & (n - 1)) == 0;
}
```

**为什么**：2 的幂的二进制只有一位是 1（如 `100`, `1000`），减 1 后变全 1（如 `011`, `0111`），按位与为 0。

### 3.4 位 1 的个数（191）

**朴素法**：

```cpp
int hammingWeight(uint32_t n) {
    int cnt = 0;
    while (n) { cnt += n & 1; n >>= 1; }
    return cnt;
}
```

**Brian Kernighan 法**：

```cpp
int hammingWeight(uint32_t n) {
    int cnt = 0;
    while (n) { cnt++; n &= n - 1; }  // 有几个 1 循环几次
    return cnt;
}
```

**对比**：朴素法 32 次循环，BK 法只循环到 1 的个数。

### 3.5 颠倒二进制位（190）

**分治法**：

```cpp
uint32_t reverseBits(uint32_t n) {
    n = (n >> 16) | (n << 16);
    n = ((n & 0xff00ff00) >> 8)  | ((n & 0x00ff00ff) << 8);
    n = ((n & 0xf0f0f0f0) >> 4)  | ((n & 0x0f0f0f0f) << 4);
    n = ((n & 0xcccccccc) >> 2)  | ((n & 0x33333333) << 2);
    n = ((n & 0xaaaaaaaa) >> 1)  | ((n & 0x55555555) << 1);
    return n;
}
```

**理解**：分别交换相邻 16 位、8 位、4 位、2 位、1 位。

### 3.6 比特位计数（338）— DP

**问题**：返回 `[0, n]` 每个数的位 1 计数。

**DP 状态**：`dp[i] = dp[i >> 1] + (i & 1)`

**解读**：i 的 1 个数 = (i/2 的 1 个数) + (i 最低位是否 1)。

```cpp
vector<int> countBits(int n) {
    vector<int> dp(n + 1, 0);
    for (int i = 1; i <= n; i++)
        dp[i] = dp[i >> 1] + (i & 1);
    return dp;
}
```

### 3.7 格雷码（gray_code）

对照 [bit_manipulation/gray_code.cpp](bit_manipulation/gray_code.cpp)。

**定义**：相邻两数只有一位不同的二进制编码。

**生成公式**：`gray(n) = n ^ (n >> 1)`

**应用**：硬件电路抗干扰、磁盘传感。

### 3.8 字符串匹配 — 朴素法

**思路**：逐位置尝试匹配。

```cpp
int strStr(string haystack, string needle) {
    int n = haystack.size(), m = needle.size();
    for (int i = 0; i <= n - m; i++) {
        int j = 0;
        while (j < m && haystack[i+j] == needle[j]) j++;
        if (j == m) return i;
    }
    return -1;
}
```

**时间**：O(nm) 最坏。

### 3.9 KMP 算法 ⭐

对照 [strings/knuth_morris_pratt.cpp](strings/knuth_morris_pratt.cpp)。

**核心**：构造 `next` 数组，记录 `needle` 每个位置的"最长相等前后缀长度"。匹配失败时，pattern 跳到 `next[j]` 继续，**不回退 text 指针**。

**next 数组构造**：

```cpp
vector<int> buildNext(string& p) {
    int m = p.size();
    vector<int> next(m, 0);
    int k = 0;
    for (int i = 1; i < m; i++) {
        while (k > 0 && p[k] != p[i]) k = next[k-1];
        if (p[k] == p[i]) k++;
        next[i] = k;
    }
    return next;
}
```

**匹配**：

```cpp
int kmp(string& text, string& pattern) {
    if (pattern.empty()) return 0;
    auto next = buildNext(pattern);
    int j = 0;
    for (int i = 0; i < text.size(); i++) {
        while (j > 0 && text[i] != pattern[j]) j = next[j-1];
        if (text[i] == pattern[j]) j++;
        if (j == pattern.size()) return i - j + 1;
    }
    return -1;
}
```

**时间**：O(n + m)。

### 3.10 Rabin-Karp — 滚动哈希

对照 [strings/rabin_karp.cpp](strings/rabin_karp.cpp)。

**思路**：计算 `pattern` 的哈希 H；滑动窗口计算 text 每个长度 m 的子串哈希，O(1) 更新。

**滚动更新**：

```
hash(s[i+1..i+m]) = (hash(s[i..i+m-1]) - s[i]*B^(m-1)) * B + s[i+m]
```

（需对大素数取模）

**时间**：平均 O(n + m)，最坏 O(nm)（哈希冲突）。

**应用**：多模式匹配、去重。

### 3.11 回文子串（5, 647）— 中心扩展法

**思路**：每个字符（奇数长度）或字符间隙（偶数长度）作为中心，向两侧扩展。

```cpp
string longestPalindrome(string s) {
    int n = s.size(), start = 0, maxLen = 1;
    auto expand = [&](int l, int r) {
        while (l >= 0 && r < n && s[l] == s[r]) { l--; r++; }
        return r - l - 1;
    };
    for (int i = 0; i < n; i++) {
        int len = max(expand(i, i), expand(i, i + 1));
        if (len > maxLen) {
            maxLen = len;
            start = i - (len - 1) / 2;
        }
    }
    return s.substr(start, maxLen);
}
```

**时间**：O(n²)。

**对比**：

- DP 法：O(n²) 时间 O(n²) 空间
- 中心扩展：O(n²) 时间 O(1) 空间
- Manacher：O(n)（复杂，面试几乎不考手写）

**647（回文子串总数）**：同中心扩展，每扩展一次计数 +1。

---

## 四、STL 速查

| API | 用法 |
|-----|------|
| `std::string::find(sub)` | 库版 KMP |
| `std::bitset<N>` | 固定大小位集 |
| `std::hash<string>()(s)` | 字符串哈希 |
| `s.substr(pos, len)` | 取子串 |
| `__builtin_popcount(n)` | GCC 内置位 1 计数 |
| `__builtin_clz(n)` | 前导 0 数量 |

---

## 五、LeetCode 题目指南

### 136. 只出现一次的数字（Easy）

- **目的**：XOR 性质
- **提示**：一行 `reduce(^)`

### 191. 位 1 的个数（Easy）

- **目的**：Brian Kernighan
- **提示**：`while (n) { cnt++; n &= n-1; }`

### 190. 颠倒二进制位（Easy）

- **目的**：分治交换
- **提示**：16→8→4→2→1 分层翻转

### 338. 比特位计数（Easy）

- **目的**：位运算 DP
- **提示**：`dp[i] = dp[i>>1] + (i&1)`

### 28. 找出字符串中第一个匹配项（Easy）

- **目的**：KMP
- **提示**：朴素法先写，再写 KMP

### 5. 最长回文子串（Medium）

- **目的**：中心扩展
- **提示**：奇偶两种中心

### 647. 回文子串（Medium）

- **目的**：计数而非找最长
- **提示**：中心扩展每扩展一次 +1

---

## 六、周日 12 周总复盘清单

### M1（Week 3 末）基础关自评

- [ ] 能闭卷手写 6 种核心排序？
- [ ] 能写二分两种区间写法？
- [ ] 能独立实现链表反转 / 去重？

### M2（Week 5 末）数据结构关自评

- [ ] 能闭卷写 BST（insert/delete）？
- [ ] 能闭卷写小顶堆？
- [ ] 能闭卷写并查集（含路径压缩）？
- [ ] 能闭卷写线段树 build/update/query？

### M3（Week 9 末）算法范式关自评

- [ ] 掌握 DFS / BFS / 回溯 / 贪心 / DP 五大范式？
- [ ] LeetCode Medium 正确率 ≥ 70%？
- [ ] 能识别题目该用哪种范式？

### M4（Week 12 末）综合关自评

- [ ] 图论四大算法（Dijkstra / Bellman-Ford / Kruskal / Prim）能手写？
- [ ] 位运算常见技巧熟练？
- [ ] 完成 3 次 1 小时模拟面试？

### 12 周整体反思

1. 哪些主题最扎实？哪些最薄弱？
2. 每日 2-3 小时节奏是否合适？
3. 面试准备度自评（1-10 分）？
4. 下一步计划？（继续刷题 / 学系统设计 / 项目实战 / 开始投简历）

---

## 附：本周禁用清单 + 模拟面试指南

### 本周禁用

- ❌ 5 题直接用 DP（先写中心扩展）
- ❌ 28 题只写朴素不写 KMP

### 模拟面试指南

**环境准备**：

- 白板或 Google Docs（不用 IDE）
- 计时器（60 分钟硬限制）
- 准备说出思路（不只是写代码）

**流程**（面向国内面试）：

1. **读题 + 澄清（3-5 分钟）**：提问边界、输入规模、复杂度要求
2. **思路（5-10 分钟）**：先朴素解法，再优化；说清时空复杂度
3. **编码（25-35 分钟）**：边写边念；变量命名清晰
4. **测试（5-10 分钟）**：跑一遍小例子；考虑边界（空、单元素、极大）
5. **复盘（5 分钟）**：自己指出潜在优化点

**常见失误**：

- 不说思路直接写代码 → 面试官看不懂
- 写完不测试 → 有小 bug 显得不严谨
- 卡壳不说话 → 让面试官知道你在想什么

**3 次模拟结束后**：总结 3 场里的共同弱点，针对性补漏。
