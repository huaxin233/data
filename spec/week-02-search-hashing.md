# Week 2 学习手册：搜索 + 哈希

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`search/` + `hashing/`
> 精读文件：5 个
> 配套 LeetCode：704, 33, 34, 74, 1, 49, 128

---

## 一、本周目标与产出

### 理解目标（能讲清楚）

- 二分搜索为什么是 O(log n)
- 左闭右闭（`[l, r]`）与左闭右开（`[l, r)`）两种区间写法的边界差别
- `lower_bound` / `upper_bound` 的语义
- 三分搜索什么时候能用（单峰函数）
- 哈希表冲突的两种解决思路：拉链 vs 开放寻址
- 负载因子（load factor）与 rehash 的关系

### 动手目标（能闭卷手写）

| 算法/结构 | 复杂度 | 关键输出 |
|-----------|--------|----------|
| 线性搜索 | O(n) | 热身 |
| 二分搜索（左闭右闭） | O(log n) | 两种边界都能写 |
| 二分搜索（左闭右开） | O(log n) | 两种边界都能写 |
| `lower_bound` 手写 | O(log n) | 二分变体 |
| 哈希表（拉链法） | 平均 O(1) | 能插入/查找 |
| 哈希表（线性探测） | 平均 O(1) | 能处理冲突 |

### 产出清单

- `my-impl/week-02/*.cpp` — 6 份手写实现
- `spec/notes/week-02-search-hashing.md` — 一页小结
- `spec/progress/week-02.md` — 进度追踪表

---

## 二、每日安排（6 学习日 + 周日复盘）

### Day 1（周一）：线性搜索 + 二分基础

- **上午 1h**：读 [search/linear_search.cpp](search/linear_search.cpp)
- **下午 1h**：读 [search/binary_search.cpp](search/binary_search.cpp)，重点看 `l <= h` 和 `m = l + (h-l)/2`
- **晚上 1h**：闭卷手写两种搜索
- **LeetCode**：704（最基础二分）

### Day 2（周二）：二分两种区间写法

- **上午 1.5h**：对比写法——左闭右闭（`l <= r`）vs 左闭右开（`l < r`）
- **下午 1h**：手写两种版本，跑通相同测试用例
- **晚上 0.5h**：思考——为什么两种写法的"未找到"返回值一致？
- **LeetCode**：34（第一个和最后一个位置）— 二分变体经典

### Day 3（周三）：二分进阶变体

- **上午 1h**：阅读 cppreference 的 `lower_bound` / `upper_bound`
- **下午 1.5h**：手写 `lower_bound`（返回第一个大于等于 target 的位置）
- **晚上 0.5h**：搜索旋转排序数组的思路（不写，只想）
- **LeetCode**：33（搜索旋转排序数组）+ 74（二维矩阵搜索）

### Day 4（周四）：三分搜索

- **上午 1h**：读 [search/ternary_search.cpp](search/ternary_search.cpp)，理解单峰函数的概念
- **下午 1h**：手写三分搜索
- **晚上 1h**：综合复习二分
- **LeetCode**：1（两数之和）— 提前体验哈希表威力

### Day 5（周五）：哈希表 — 拉链法

- **上午 1.5h**：读 [hashing/chaining.cpp](hashing/chaining.cpp)，关注 `hash()` / `add()` / `find()`
- **下午 1.5h**：闭卷手写一个简单版本（不用 `shared_ptr`，用裸指针或 `vector<list<int>>`）
- **LeetCode**：49（字母异位词分组）

### Day 6（周六）：哈希表 — 开放寻址 + 综合

- **上午 1.5h**：读 [hashing/linear_probing_hash_table.cpp](hashing/linear_probing_hash_table.cpp)，理解探测序列和墓碑（tombstone）
- **下午 1h**：闭卷手写线性探测版本
- **晚上 1h**：**LeetCode 128**（最长连续序列）— 哈希经典应用

### Day 7（周日）：复盘

- **上午 1h**：闭卷重写二分两种写法 + 拉链法哈希
- **下午 0.5h**：填写 `spec/notes/week-02-search-hashing.md`
- **下午 0.5h**：填写 `spec/progress/week-02.md`

---

## 三、搜索与哈希深度讲解

### 3.1 线性搜索（Linear Search）

**核心思想**：逐个比较，找到即返回。

**实现要点**（对照 [search/linear_search.cpp:21-31](search/linear_search.cpp#L21-L31)）：

```cpp
int LinearSearch(int* array, int size, int key) {
    for (int i = 0; i < size; ++i) {
        if (array[i] == key) return i;
    }
    return -1;
}
```

**关键细节**：

- 时间 O(n)，空间 O(1)
- 无需数据有序
- 面试几乎不单考，但是**二分的 fallback** 和**哈希的对比基准**

### 3.2 二分搜索（Binary Search）⭐ 重点

**核心思想**：每次比较中间元素，砍掉一半区间。

**前提**：数组**必须有序**。

#### 写法一：左闭右闭 `[l, r]`（仓库实现）

对照 [search/binary_search.cpp:61-82](search/binary_search.cpp#L61-L82)：

```cpp
int binarySearch(vector<int>& arr, int val) {
    int low = 0, high = arr.size() - 1;   // 闭区间右端
    while (low <= high) {                  // <= 而非 <
        int m = low + (high - low) / 2;
        if (arr[m] == val) return m;
        else if (val < arr[m]) high = m - 1;  // m 已查过，-1
        else low = m + 1;
    }
    return -1;
}
```

**三个"为什么"**：

- 为什么 `low <= high`？因为区间是闭的，`low == high` 时还有一个元素没检查
- 为什么 `m = low + (high-low)/2` 而不是 `(low+high)/2`？避免大数溢出
- 为什么 `high = m - 1` / `low = m + 1`？`m` 已经查过，要跳过

#### 写法二：左闭右开 `[l, r)`

```cpp
int binarySearch(vector<int>& arr, int val) {
    int low = 0, high = arr.size();       // 开区间右端
    while (low < high) {                   // < 而非 <=
        int m = low + (high - low) / 2;
        if (arr[m] == val) return m;
        else if (val < arr[m]) high = m;   // m 不在新区间
        else low = m + 1;
    }
    return -1;
}
```

**两种写法的选择**：

- **初学者**：建议先固定一种写到熟练，推荐左闭右闭（更直观）
- **进阶**：左闭右开对应 `std::lower_bound` 的思维，统一性更好
- **忌讳**：两种写法混用，边界容易错

### 3.3 二分变体：lower_bound / upper_bound

**`lower_bound(arr, x)`**：返回**第一个大于等于 x** 的位置。

```cpp
int lowerBound(vector<int>& arr, int x) {
    int low = 0, high = arr.size();       // 用左闭右开更自然
    while (low < high) {
        int m = low + (high - low) / 2;
        if (arr[m] < x) low = m + 1;       // 严格小于才缩左
        else high = m;                      // 等于也缩右
    }
    return low;  // 可能等于 arr.size()（所有元素都 < x）
}
```

**`upper_bound(arr, x)`**：返回**第一个大于 x** 的位置。只需把 `<` 改成 `<=`：

```cpp
if (arr[m] <= x) low = m + 1;
```

**应用**：

- `upper_bound(x) - lower_bound(x)` = x 的出现次数
- LeetCode 34 = `lower_bound` + `upper_bound` 组合

### 3.4 三分搜索（Ternary Search）

**核心思想**：对**单峰函数**（先增后减或先减后增）求极值。

**前提**：必须是**单峰**，否则三分无效。

**伪代码**：

```cpp
while (r - l > eps) {                     // 实数版本
    double m1 = l + (r - l) / 3;
    double m2 = r - (r - l) / 3;
    if (f(m1) < f(m2)) l = m1;             // 峰在右侧
    else r = m2;                            // 峰在左侧（或相等）
}
```

**整数版二分搜索三分问题**：

```cpp
while (l < r) {
    int m1 = l + (r - l) / 3;
    int m2 = r - (r - l) / 3;
    if (f(m1) < f(m2)) l = m1 + 1;
    else r = m2 - 1;
}
```

**注意**：面试中三分用得少，但要能识别什么样的函数适用。

### 3.5 哈希表基础

**核心思想**：通过 `hash(key) % capacity` 把键映射到数组下标，实现平均 O(1) 查找。

**要素**：

1. **哈希函数**：把 key 映射到 `[0, capacity)` 的整数
2. **冲突解决**：两个 key 映射到同一位置怎么办
3. **负载因子**（load factor）= `元素数 / 容量`，通常超过 0.75 触发 rehash

**哈希函数的目标**：

- 均匀分布（减少冲突）
- 计算快（O(1)）
- 确定性（同 key → 同 hash）

**常见哈希函数**：

- 整数：`key % prime`（prime 取素数减少冲突）
- 字符串：多项式哈希 `Σ s[i] * base^i mod prime`

### 3.6 拉链法（Chaining）

**核心思想**：每个桶存一个**链表**，冲突的元素挂在链表上。

**实现要点**（对照 [hashing/chaining.cpp:35-58](hashing/chaining.cpp#L35-L58)）：

```cpp
class HashChain {
    vector<list<int>> table;  // 桶数组，每桶是一个 list
    int _mod;
public:
    HashChain(int mod) : _mod(mod), table(mod) {}

    int hash(int x) { return abs(x % _mod); }

    void add(int x) { table[hash(x)].push_back(x); }

    bool find(int x) {
        auto& chain = table[hash(x)];
        for (int v : chain) if (v == x) return true;
        return false;
    }
};
```

**仓库版本用了 `shared_ptr` 手动链表**——初学者可用 `std::list` 简化。

**关键细节**：

- **不需要 rehash**：链表可无限增长（但会退化）
- **最坏 O(n)**：所有 key 冲突到同一桶
- **适合**：key 分布未知，频繁插入删除

### 3.7 开放寻址 — 线性探测（Linear Probing）

**核心思想**：冲突时，**往后找空位**（索引 +1、+2、+3...）。

**实现骨架**（对照 [hashing/linear_probing_hash_table.cpp](hashing/linear_probing_hash_table.cpp)）：

```cpp
class LinearProbing {
    vector<int> table;        // 存 key 本身
    vector<bool> occupied;     // 标记是否占用
    int capacity;
public:
    void insert(int key) {
        int idx = hash(key);
        while (occupied[idx]) idx = (idx + 1) % capacity;  // 探测
        table[idx] = key;
        occupied[idx] = true;
    }

    bool find(int key) {
        int idx = hash(key);
        while (occupied[idx]) {
            if (table[idx] == key) return true;
            idx = (idx + 1) % capacity;
        }
        return false;
    }
};
```

**墓碑（tombstone）问题**：

- 删除一个元素时不能简单置空 → 会打断探测链
- 解决：用一个 `deleted` 标记位，查找时跳过，插入时可覆盖

**关键细节**：

- **负载因子不能太高**（建议 < 0.7）否则性能崩塌
- **一级聚集**：连续占用的桶越长，新冲突越容易卡在长段
- **优点**：缓存友好（都是数组连续访问）
- **适合**：数据量可预估、读多写少

### 3.8 拉链 vs 开放寻址对比

| 维度 | 拉链法 | 线性探测 |
|------|--------|----------|
| 实现复杂度 | 简单 | 中等（墓碑） |
| 内存开销 | 额外指针 | 无额外指针 |
| 缓存友好 | 差（链表跳跃） | 好（连续数组） |
| 删除 | 容易 | 难（墓碑） |
| 负载因子上限 | 可 >1 | 必须 <1 |
| 最坏情况 | O(n) | O(n) |

---

## 四、STL 速查

| API | 头文件 | 用法 |
|-----|--------|------|
| `std::lower_bound(begin, end, x)` | `<algorithm>` | 第一个 >= x 的迭代器 |
| `std::upper_bound(begin, end, x)` | `<algorithm>` | 第一个 > x 的迭代器 |
| `std::binary_search(begin, end, x)` | `<algorithm>` | 返回 bool，仅判存在 |
| `std::unordered_map<K, V>` | `<unordered_map>` | 哈希表键值对 |
| `std::unordered_set<K>` | `<unordered_set>` | 哈希集合 |
| `std::map<K, V>` | `<map>` | 有序字典（红黑树） |
| `std::hash<T>` | `<functional>` | 标准哈希函数对象 |
| `umap.count(k)` | — | 键是否存在（0 或 1） |
| `umap.find(k)` | — | 返回迭代器，未找到返回 `.end()` |
| `umap[k]` | — | 不存在时插入默认值（慎用！） |

**常见坑**：`umap[k]` 会改变容器（默认插入），只想查询请用 `count` 或 `find`。

---

## 五、LeetCode 题目指南

> **原则**：独立思考 30 分钟，超时查题解，次日重写一遍。

### 704. 二分查找（Easy）

- **目的**：二分模板题
- **提示**：两种区间写法都实现一遍

### 33. 搜索旋转排序数组（Medium）

- **目的**：判断"哪一半有序"的二分变体
- **提示**：每次二分后，总有一半是有序的；判断 target 是否在有序那一半
- **坑**：边界 `<=` 和 `<` 的选择会影响单元素数组

### 34. 在排序数组中查找元素的第一个和最后一个位置（Medium）

- **目的**：`lower_bound` + `upper_bound` 实战
- **提示**：写两次二分分别找左右边界

### 74. 搜索二维矩阵（Medium）

- **目的**：把二维展开成一维二分
- **提示**：下标映射 `row = idx / cols`, `col = idx % cols`

### 1. 两数之和（Easy）

- **目的**：哈希表入门
- **提示**：一次遍历，每次查 `target - nums[i]` 是否在 map 里
- **坑**：先查再插，否则会用自己配对

### 49. 字母异位词分组（Medium）

- **目的**：字符串哈希作为 key
- **提示**：排序后字符串相同 → 异位词；或用字符计数数组作 key

### 128. 最长连续序列（Medium）

- **目的**：哈希表的经典应用
- **提示**：要求 O(n)，不能排序。只从"连续序列起点"开始扩展（即 `x-1` 不在哈希集合中）

---

## 六、周日复盘清单

**理论层**：

1. 二分两种区间写法的边界差别是什么？
2. `lower_bound` 和 `upper_bound` 分别返回什么？如何用它们算某值的出现次数？
3. 哈希表为什么是平均 O(1)？最坏情况是什么？
4. 拉链 vs 开放寻址各有什么优缺点？
5. 墓碑问题是什么？如何解决？

**动手层**：

6. 能否闭卷写出两种区间的二分？
7. 能否闭卷写出 `lower_bound`？
8. 能否闭卷写出拉链/线性探测两种哈希表？
9. 7 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的点是？
12. 下周节奏要调整吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ `std::binary_search`（只返回 bool，不够用）
- ❌ `std::lower_bound` / `std::upper_bound` 直接调（手写时）
- ❌ `std::unordered_map` / `std::unordered_set`（手写哈希表时）

**允许**：

- ✅ 在 LeetCode 里使用 `unordered_map` / `unordered_set`（面试常用）
- ✅ `std::vector`, `std::list`, `std::pair`
- ✅ 查 cppreference（但不查二分模板）
