# Week 6 学习手册：分治算法

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`divide_and_conquer/` + 回顾 `sorting/`
> 精读文件：主要回顾 + 1 个新文件
> 配套 LeetCode：53, 169, 23, 4

---

## 一、本周目标与产出

> 本周是**思想周**，节奏稍轻。目标是**把分治思想内化**，能识别新问题何时适用分治。

### 理解目标

- 分治三段式：**分 / 治 / 合**
- 分治必须满足的条件：子问题独立 + 能合并 + 规模缩小
- 递归树分析法
- 主定理（Master Theorem）三种情形
- 分治 vs DP 的本质区别（子问题是否重叠）

### 动手目标

| 算法 | 复杂度 | 关键思路 |
|------|--------|----------|
| 归并排序（回顾） | O(n log n) | 分治 + 合并有序数组 |
| 快速排序（回顾） | 平均 O(n log n) | 分治 + 分区 |
| 求逆序对 | O(n log n) | 归并排序的副产物 |
| Karatsuba 大数乘法（了解） | O(n^1.585) | 分治 3 次子乘法 |

### 产出清单

- `my-impl/week-06/*.cpp` — 3-4 份手写实现（重点是逆序对）
- `spec/notes/week-06-divide-and-conquer.md` — 一页小结
- `spec/progress/week-06.md` — 进度表

---

## 二、每日安排

### Day 1（周一）：分治理论

- **上午 1h**：理解分治三段式，在笔记中画出递归树
- **下午 1h**：回顾 Week 1 的 merge_sort，标出"分 / 治 / 合"各部分
- **晚上 1h**：回顾 Week 1 的 quick_sort，理解分区是"分"，递归是"治"，无需显式"合"
- **LeetCode**：53（最大子数组，先用 Kadane 做，Week 9 DP 会再遇到；本周用分治法）

### Day 2（周二）：递归树与主定理

- **上午 1h**：学习主定理三种情形（不求证明，记结论）
- **下午 1h**：用递归树分析 merge_sort、quick_sort 复杂度
- **晚上 1h**：**LeetCode 169**（多数元素）— 用 Boyer-Moore 或分治
- **LeetCode**：169

### Day 3（周三）：逆序对

- **上午 1.5h**：读 [sorting/count_inversions.cpp](sorting/count_inversions.cpp)
- **下午 1.5h**：闭卷手写归并求逆序对
- **晚上 0.5h**：思考——逆序对还能用什么数据结构做？（BIT / 线段树）

### Day 4（周四）：Karatsuba + 综合

- **上午 1h**：读 [divide_and_conquer/karatsuba_algorithm_for_fast_multiplication.cpp](divide_and_conquer/karatsuba_algorithm_for_fast_multiplication.cpp)
- **下午 1h**：理解 Karatsuba 3 次子乘法的分解公式
- **晚上 1h**：**LeetCode 23**（合并 K 个升序链表）— 分治 vs 堆

### Day 5（周五）：LeetCode 4 攻坚

- **全天**：**LeetCode 4**（两正序数组中位数）— 本周最难题
- **提示**：用二分 + 分治混合，想不出来允许查题解（这题确实需要启发）

### Day 6（周六）：综合复习 + 延伸

- **上午**：重做 Week 1 的 merge_sort / quick_sort，这次用分治视角看
- **下午**：总结"哪些问题可以分治"的启发式清单
- **晚上**：复做本周卡壳过的 LeetCode

### Day 7（周日）：复盘

- **上午**：把递归树、主定理、分治模板整理成笔记
- **下午**：填写 `spec/notes/` 和 `spec/progress/`

---

## 三、分治深度讲解

### 3.1 分治三段式

**分（Divide）**：把问题拆成若干规模更小的**独立**子问题。
**治（Conquer）**：递归解决子问题。
**合（Combine）**：合并子问题的解为原问题的解。

**模板**：

```cpp
ResultType solve(Input input) {
    if (base_case(input)) return base_result;    // 基线

    // 分：拆成 k 个子问题
    auto [sub1, sub2, ...] = divide(input);

    // 治：递归
    auto r1 = solve(sub1);
    auto r2 = solve(sub2);
    ...

    // 合：组合子结果
    return combine(r1, r2, ...);
}
```

### 3.2 分治的适用条件

1. **子问题独立**：子问题之间不共享状态（否则子问题**重叠** → 考虑 DP）
2. **能合并**：两个子问题的解可以合并为原问题的解
3. **规模缩小**：子问题规模必须严格小于原问题，否则无限递归

### 3.3 归并排序（回顾）

**分治视角**：

- **分**：`mid = (l + r) / 2`，切成 `[l, mid]` 和 `[mid+1, r]`
- **治**：递归排序两半
- **合**：`merge()` 合并两个已排序的子数组

**时间复杂度推导**：`T(n) = 2T(n/2) + O(n) = O(n log n)`（主定理 Case 2）

**空间**：O(n)（merge 需临时数组）

### 3.4 快速排序（回顾）

**分治视角**：

- **分**：partition 把数组分成"小于 pivot"和"大于 pivot"两部分
- **治**：递归排序两部分
- **合**：**无需显式合并**（pivot 已归位，两边就地有序）

**时间复杂度**：

- 平均 O(n log n)（Case 2）
- 最坏 O(n²)（Case 1，分区极度不平衡）

**空间**：O(log n)（递归栈，原地排序）

**快排的"无需合并"是它优于归并的关键点之一**（少一次 O(n) 操作，常数更小）。

### 3.5 逆序对（重点应用）

**问题**：数组中有多少对 `(i, j)` 满足 `i < j` 且 `a[i] > a[j]`。

**朴素 O(n²)**：双重循环。

**归并 O(n log n)**：

```cpp
long long merge_count(vector<int>& a, int l, int mid, int r) {
    vector<int> L(a.begin()+l, a.begin()+mid+1);
    vector<int> R(a.begin()+mid+1, a.begin()+r+1);
    long long cnt = 0;
    int i = 0, j = 0, k = l;
    while (i < L.size() && j < R.size()) {
        if (L[i] <= R[j]) a[k++] = L[i++];
        else {
            a[k++] = R[j++];
            cnt += L.size() - i;        // ⭐ 关键：L 中剩下的都与 R[j] 构成逆序对
        }
    }
    while (i < L.size()) a[k++] = L[i++];
    while (j < R.size()) a[k++] = R[j++];
    return cnt;
}

long long merge_sort(vector<int>& a, int l, int r) {
    if (l >= r) return 0;
    int mid = (l + r) / 2;
    return merge_sort(a, l, mid)
         + merge_sort(a, mid+1, r)
         + merge_count(a, l, mid, r);
}
```

**核心洞察**：当从 R 取一个元素时，L 中所有剩余元素都比它大 → 一次性累加。

对照 [sorting/count_inversions.cpp](sorting/count_inversions.cpp)。

### 3.6 最大子数组（53 题）— 分治视角

**分治三种情况**：

1. 最大子数组**完全在左半**
2. **完全在右半**
3. **跨越中点**

前两种递归解决，第三种 O(n) 扫描。

```cpp
struct Result { int leftSum, rightSum, maxSum, totalSum; };

Result solve(vector<int>& a, int l, int r) {
    if (l == r) return {a[l], a[l], a[l], a[l]};
    int mid = (l + r) / 2;
    auto L = solve(a, l, mid), R = solve(a, mid+1, r);
    return {
        max(L.leftSum, L.totalSum + R.leftSum),
        max(R.rightSum, R.totalSum + L.rightSum),
        max({L.maxSum, R.maxSum, L.rightSum + R.leftSum}),
        L.totalSum + R.totalSum
    };
}
```

**对比**：Kadane 算法（DP 做法）更简单，O(n)；但分治可并行化。

### 3.7 合并 K 个升序链表（23 题）— 分治

**朴素**：一对一合并，总复杂度 O(NK)（N 是总节点数，K 是链表数）。

**分治**：两两配对合并 → O(N log K)。

```cpp
ListNode* mergeKLists(vector<ListNode*>& lists) {
    if (lists.empty()) return nullptr;
    int n = lists.size();
    while (n > 1) {
        int k = (n + 1) / 2;
        for (int i = 0; i < n / 2; i++)
            lists[i] = mergeTwo(lists[i], lists[i + k]);
        n = k;
    }
    return lists[0];
}
```

**对比**：堆法同样 O(N log K)，但分治常数更小。

### 3.8 Karatsuba 大数乘法（了解）

**问题**：两个 n 位数相乘，朴素 O(n²)。

**Karatsuba 思想**：
- 两个数各拆成高低两半：`x = a·B + b`, `y = c·B + d`（B 是 10^(n/2)）
- `xy = ac·B² + (ad+bc)·B + bd`
- 朴素需 4 次子乘法（ac, ad, bc, bd）
- Karatsuba 只需 3 次：`ac`, `bd`, `(a+b)(c+d) - ac - bd`
- 递推：`T(n) = 3T(n/2) + O(n)` → **O(n^log₂3) ≈ O(n^1.585)**

**面试考察频率**：极低，了解思想即可。

### 3.9 主定理（Master Theorem，结论版）

**形式**：`T(n) = aT(n/b) + f(n)`

| 情形 | 条件 | 结论 |
|------|------|------|
| 1 | f(n) = O(n^(log_b(a) - ε)) | T(n) = Θ(n^log_b(a)) |
| 2 | f(n) = Θ(n^log_b(a)) | T(n) = Θ(n^log_b(a) · log n) |
| 3 | f(n) = Ω(n^(log_b(a) + ε)) 且正则性 | T(n) = Θ(f(n)) |

**快速判断**：比较 `f(n)` 和 `n^log_b(a)` 谁大。

- 例 1：归并排序 `T(n) = 2T(n/2) + n`。`a=2, b=2, log_b(a)=1, f(n)=n`，Case 2，O(n log n)
- 例 2：二分搜索 `T(n) = T(n/2) + 1`。`a=1, b=2, log_b(a)=0, f(n)=1`，Case 2，O(log n)
- 例 3：Karatsuba `T(n) = 3T(n/2) + n`。`log_b(a) ≈ 1.585`，Case 1，O(n^1.585)

### 3.10 分治 vs DP

| 维度 | 分治 | DP |
|------|------|-----|
| 子问题关系 | 独立 | 重叠 |
| 是否记忆化 | 否 | 是（否则指数爆炸） |
| 适用 | 子问题规模大幅缩减 | 子问题少但被多次用到 |
| 代表 | 归并/快排 | 斐波那契/LCS |

**小测验**：斐波那契 `fib(n) = fib(n-1) + fib(n-2)` 是分治还是 DP？

答：**形式上像分治，但子问题严重重叠**（`fib(n-2)` 被计算多次），所以**实际是 DP**，需要记忆化。

---

## 四、STL 速查

本周主要回顾，无新增。

---

## 五、LeetCode 题目指南

### 53. 最大子数组和（Medium）

- **目的**：Kadane DP + 分治双视角
- **提示**：分治法用第 3.6 节的 4 字段结构；DP 法用 `dp[i] = max(a[i], dp[i-1]+a[i])`

### 169. 多数元素（Easy）

- **目的**：分治 vs Boyer-Moore
- **提示**：分治——左右多数元素是否一致；Boyer-Moore——投票法 O(1) 空间
- **优先用 Boyer-Moore**（面试常问）

### 23. 合并 K 个升序链表（Hard）

- **目的**：分治合并
- **提示**：两两合并 + 递归；也可用小顶堆

### 4. 寻找两个正序数组的中位数（Hard）⭐ 本周压轴

- **目的**：二分 + 分治
- **提示**：转化为"找第 K 小"；每次从两个数组各取一半比较，砍掉较小的一半
- **难度警告**：边界极多，允许查题解，但要彻底理解

---

## 六、周日复盘清单

**理论层**：

1. 分治必须满足哪三个条件？
2. 分治 vs DP 的核心区别是什么？
3. 主定理三种情形分别对应什么递推？
4. 为什么快排不需要显式合并？
5. 逆序对为什么能在归并中 O(1) 累加？

**动手层**：

6. 能否闭卷写归并求逆序对？
7. 53 题的分治解法能否写出？
8. 4 道 LeetCode 是否都 AC？（4 题可以查题解）

**反思层**：

9. 本周最大收获是？
10. 最困惑的是？下周怎么解决？
11. 下周 DP 入门做好准备了吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ 53 题只用 Kadane（必须用分治做一遍）
- ❌ 23 题只用暴力一对一合并

**允许**：

- ✅ 4 题查题解（真的需要启发）
- ✅ 画递归树（强烈推荐）
