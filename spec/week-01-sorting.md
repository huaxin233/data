# Week 1 学习手册：排序算法

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`sorting/`
> 精读文件：7 个
> 配套 LeetCode：912, 215, 88, 147, 148

---

## 一、本周目标与产出

### 理解目标（能讲清楚）

- 为什么比较排序的下界是 `O(n log n)`
- 什么是**稳定排序**，哪些排序稳定、哪些不稳定
- **原地排序** vs 非原地的区别
- 分治思想在 merge_sort / quick_sort 中如何体现
- 堆（大顶/小顶）如何用数组实现

### 动手目标（能闭卷手写）

Week 结束时，**不查任何参考**能写出下面 6 种排序并通过 `std::is_sorted` 校验：

| 排序 | 核心思想 | 典型复杂度 | 稳定性 |
|------|----------|------------|--------|
| 冒泡 | 相邻交换 | O(n²) | 稳定 |
| 插入 | 抽牌插入 | O(n²)，近有序 O(n) | 稳定 |
| 选择 | 找最小放前 | O(n²) | 不稳定 |
| 归并 | 分治合并 | O(n log n) | 稳定 |
| 快排 | 分治分区 | 平均 O(n log n)，最坏 O(n²) | 不稳定 |
| 堆排 | 建堆+下沉 | O(n log n) | 不稳定 |

第 7 种 `counting_sort`（计数排序）作为**非比较排序代表**理解即可，不强求闭卷手写。

### 配套产出

- `my-impl/week-01/*.cpp` — 自己手写的 6 份排序代码（每份含测试 main）
- `spec/notes/week-01-sorting.md` — 一页小结（周日写）
- `spec/progress/week-01.md` — 进度追踪表

---

## 二、每日安排（6 学习日 + 周日复盘）

### Day 1（周一）：冒泡 + 插入

- **上午 1h**：读 [sorting/bubble_sort.cpp](sorting/bubble_sort.cpp)，理解"`swap_check` 提前终止"的优化点
- **下午 1h**：读 [sorting/insertion_sort.cpp](sorting/insertion_sort.cpp)，关注抽牌比喻和"稳定"性
- **晚上 1h**：闭卷手写 `bubble_sort.cpp` + `insertion_sort.cpp`，写完对比原版差异
- **LeetCode**：无（今天不刷）

### Day 2（周二）：选择 + 今日小节回顾

- **上午 1h**：读 [sorting/selection_sort_iterative.cpp](sorting/selection_sort_iterative.cpp)
- **下午 1h**：闭卷手写选择排序 + 回顾 Day 1 两种排序
- **晚上 1h**：**LeetCode 912（手写快排之前的版本：可用任意 O(n²) 排序 AC 小数据）**
- **LeetCode**：912（用冒泡或插入提交一次体验）

### Day 3（周三）：归并排序（重点）

- **上午 1h**：读 [sorting/merge_sort.cpp](sorting/merge_sort.cpp)，**在纸上画递归树**
- **下午 1.5h**：闭卷手写归并排序（新手建议先写 merge 再写 mergeSort）
- **晚上 0.5h**：看 `merge()` 函数里临时数组 L / R 的用法，理解为什么不能原地合并
- **LeetCode**：88（合并两个有序数组 — 归并的核心子过程）

### Day 4（周四）：快速排序（重点）

- **上午 1h**：读 [sorting/quick_sort.cpp](sorting/quick_sort.cpp)，**关注 `partition` 函数的指针 i / j 游走**
- **下午 1.5h**：闭卷手写快排（先写 partition 再写 quick_sort）
- **晚上 0.5h**：思考——为什么最坏情况 O(n²)？什么时候会触发？
- **LeetCode**：215（第 K 大，快排 partition 的直接应用）

### Day 5（周五）：堆排序

- **上午 1.5h**：读 [sorting/heap_sort.cpp](sorting/heap_sort.cpp)，**在纸上画二叉树与数组的对应关系**（父 i、左 2i+1、右 2i+2）
- **下午 1h**：闭卷手写堆排序（先写 heapify 再写 heapSort）
- **晚上 0.5h**：思考——为什么建堆是 O(n) 而非 O(n log n)？
- **LeetCode**：147（链表插入排序）

### Day 6（周六）：计数排序 + 综合刷题

- **上午 1h**：读 [sorting/counting_sort.cpp](sorting/counting_sort.cpp)，理解"非比较"如何突破 O(n log n) 下界
- **下午 1h**：**综合性复现**——抽一种排序闭卷重写（薄弱的那个）
- **晚上 1h**：**LeetCode 148**（排序链表 — 归并排序的经典变形）

### Day 7（周日）：复盘

- **上午 1h**：对照"动手目标"6 种排序，**全部闭卷重写一遍**
- **下午 0.5h**：填写 `spec/notes/week-01-sorting.md` 一页小结
- **下午 0.5h**：填写 `spec/progress/week-01.md` 进度表
- **休息**：剩余时间休息 / 预习 Week 2

---

## 三、7 种排序深度讲解

### 3.1 冒泡排序（Bubble Sort）

**核心思想**：相邻比较 + 交换。每轮把未排序区间的最大值"冒"到区间末尾。

**实现要点**（对照 [sorting/bubble_sort.cpp:77-84](sorting/bubble_sort.cpp#L77-L84)）：

```cpp
for (int i = 0; i < size && swap_check; i++) {
    swap_check = false;               // 每轮开始先假设已排序
    for (int j = 0; j < size - 1 - i; j++) {
        if (arr[j] > arr[j + 1]) {
            swap(arr[j], arr[j + 1]);
            swap_check = true;        // 有交换才继续
        }
    }
}
```

**关键细节**：

- **内层循环 `size - 1 - i`**：每轮最大值已到位，无需再看
- **`swap_check` 优化**：一轮无交换 → 已排好序 → 提前退出。最好情况 O(n)
- **稳定性**：`>` 才交换，`==` 不交换 → 稳定

**常见坑**：

- 内层写成 `j < size - 1` → 不会错但多余比较
- 忘记 `swap_check = false`（放在外层循环内部第一行）

### 3.2 插入排序（Insertion Sort）

**核心思想**：像抓扑克牌——每次拿一张，在已排好的牌堆里找到插入位置。

**实现要点**（对照 [sorting/insertion_sort.cpp:60-68](sorting/insertion_sort.cpp#L60-L68)）：

```cpp
for (int i = 1; i < n; i++) {
    T temp = arr[i];                  // 待插入的牌
    int j = i - 1;
    while (j >= 0 && temp < arr[j]) { // 向前比较
        arr[j + 1] = arr[j];          // 挪位
        j--;
    }
    arr[j + 1] = temp;                // 插入
}
```

**关键细节**：

- **外层从 `i=1` 开始**：`arr[0]` 默认已排好
- **边界 `j >= 0`**：必须放在 `temp < arr[j]` 之前，否则越界
- **稳定性**：`temp < arr[j]` 用的是严格小于，等于时不挪 → 稳定

**为什么近有序数据 O(n)**：如果已基本有序，内层 while 几乎不执行 → 只剩外层 n 次。

### 3.3 选择排序（Selection Sort）

**核心思想**：每轮找剩余部分的最小值，放到当前位置。

**实现要点**（对照 [sorting/selection_sort_iterative.cpp:53-66](sorting/selection_sort_iterative.cpp#L53-L66)）：

```cpp
for (int i = 0; i < n; i++) {
    int min = i;
    for (int j = i + 1; j < n; j++)
        if (arr[j] < arr[min]) min = j;
    if (min != i) swap(arr[i], arr[min]);
}
```

**关键细节**：

- **`min` 记录的是索引**，不是值
- **`min != i` 的判断**：避免无意义的自交换
- **不稳定**：跨越式的 swap 会破坏相等元素的相对位置。例：`[5a, 3, 5b, 2]` 第一轮 swap(5a, 2) → `[2, 3, 5b, 5a]`，5a/5b 相对位置反了

**选择排序的固定开销**：无论输入是否有序，都是 O(n²)。所以它比冒泡还慢（冒泡近有序可 O(n)）。

### 3.4 归并排序（Merge Sort）⭐ 重点

**核心思想**：分治——把数组分两半分别排好，再合并两个有序数组。

**实现要点**（对照 [sorting/merge_sort.cpp:37-89](sorting/merge_sort.cpp#L37-L89)）：

```cpp
void merge(int* arr, int l, int m, int r) {
    int n1 = m - l + 1, n2 = r - m;
    vector<int> L(n1), R(n2);          // 临时数组拷贝
    for (int i = 0; i < n1; i++) L[i] = arr[l + i];
    for (int j = 0; j < n2; j++) R[j] = arr[m + 1 + j];
    int i = 0, j = 0, k = l;
    while (i < n1 && j < n2)
        arr[k++] = (L[i] <= R[j]) ? L[i++] : R[j++];
    while (i < n1) arr[k++] = L[i++];
    while (j < n2) arr[k++] = R[j++];
}

void mergeSort(int* arr, int l, int r) {
    if (l < r) {
        int m = l + (r - l) / 2;        // 防溢出写法
        mergeSort(arr, l, m);
        mergeSort(arr, m + 1, r);
        merge(arr, l, m, r);
    }
}
```

**关键细节**：

- **`m = l + (r - l) / 2` 而不是 `(l + r) / 2`**：避免大数溢出
- **合并时用 `<=` 而非 `<`**：保证稳定性（相等时优先取左半边）
- **空间 O(n)**：临时数组 L、R 不可避免
- **递归深度 O(log n)**：栈空间开销

**为什么时间复杂度 O(n log n)**：递归树深度 log n，每层合并总共 O(n)。

### 3.5 快速排序（Quick Sort）⭐ 重点

**核心思想**：分治——选一个 pivot，把数组分成"小于 pivot"和"大于 pivot"两半，递归排序两半。

**实现要点**（对照 [sorting/quick_sort.cpp:70-85](sorting/quick_sort.cpp#L70-L85)）：

```cpp
int partition(vector<T>& arr, int low, int high) {
    T pivot = arr[high];               // 选末尾为 pivot
    int i = low - 1;                   // i 是"小于 pivot 区"的右边界
    for (int j = low; j < high; j++) {
        if (arr[j] <= pivot) {
            i++;
            swap(arr[i], arr[j]);
        }
    }
    swap(arr[i + 1], arr[high]);       // pivot 归位
    return i + 1;                      // pivot 的最终索引
}

void quick_sort(vector<T>& arr, int low, int high) {
    if (low < high) {
        int p = partition(arr, low, high);
        quick_sort(arr, low, p - 1);
        quick_sort(arr, p + 1, high);
    }
}
```

**关键细节**：

- **`i` 的语义**：永远指向"小于等于 pivot 区间的最后一个元素"
- **最后一次 swap**：把 pivot 从 `arr[high]` 移到它该在的位置 `arr[i+1]`
- **最坏情况 O(n²)**：已排序数组 + 选末尾为 pivot → 每次分区都是 [0, n-1]。解决方案：随机选 pivot（见 `random_pivot_quick_sort.cpp`）
- **原地排序**：空间复杂度 O(log n)（递归栈）

**踩坑提醒**：

- partition 里 `<=` 还是 `<`？两种都可以，但影响稳定性和重复元素表现
- 调用时 `high` 是**闭区间右端**（即 `size - 1`），别传成 `size`

### 3.6 堆排序（Heap Sort）

**核心思想**：利用堆（完全二叉树）每次取最大值的特性 → 建堆 + 不断取堆顶。

**数组表示堆**：

- 父节点索引 `i`，左子 `2i+1`，右子 `2i+2`
- 父索引：`(i-1) / 2`

**实现要点**（对照 [sorting/heap_sort.cpp:57-90](sorting/heap_sort.cpp#L57-L90)）：

```cpp
void heapify(T* arr, int n, int i) {   // 下沉 arr[i]
    int largest = i;
    int l = 2*i + 1, r = 2*i + 2;
    if (l < n && arr[l] > arr[largest]) largest = l;
    if (r < n && arr[r] > arr[largest]) largest = r;
    if (largest != i) {
        swap(arr[i], arr[largest]);
        heapify(arr, n, largest);      // 继续下沉
    }
}

void heapSort(T* arr, int n) {
    for (int i = n/2 - 1; i >= 0; i--) // 建堆：从最后一个非叶节点开始
        heapify(arr, n, i);
    for (int i = n - 1; i > 0; i--) {  // 不断取堆顶放末尾
        swap(arr[0], arr[i]);
        heapify(arr, i, 0);
    }
}
```

> 注：仓库版本的建堆循环是 `for (int i = n - 1; i >= 0; i--)` —— 从叶子开始其实是冗余的（叶子已满足堆性质）。更标准的写法是从 `n/2 - 1` 开始。

**关键细节**：

- **建堆是 O(n)** 而非 O(n log n)（教材上有数学证明，背结论即可）
- **下沉 vs 上浮**：堆排序中建堆用下沉；插入新元素用上浮
- **大顶堆 → 升序**：堆顶是最大，不断往末尾塞
- **不稳定**：下沉过程会跨越式交换

### 3.7 计数排序（Counting Sort）

**核心思想**：不比较大小，直接用"值"作为索引统计频次。

**实现要点**（对照 [sorting/counting_sort.cpp:24-45](sorting/counting_sort.cpp#L24-L45)）：

```cpp
int* Counting_Sort(int* arr, int n) {
    int min = Min(arr, n), max = Max(arr, n);
    int* count = new int[max - min + 1]();       // 频次数组
    for (int i = 0; i < n; i++) count[arr[i] - min]++;
    for (int i = 1; i < max - min + 1; i++)       // 前缀和
        count[i] += count[i - 1];
    int* sorted = new int[n];
    for (int i = n - 1; i >= 0; i--) {            // 逆序保证稳定
        sorted[count[arr[i] - min] - 1] = arr[i];
        count[arr[i] - min]--;
    }
    return sorted;
}
```

**关键细节**：

- **时间 O(n + k)**，k 是值域大小
- **适用条件**：值域小且是整数（或可映射为整数）
- **非原地**：需要额外 O(n + k) 空间
- **稳定**：逆序遍历 + 前缀和减 1 的技巧

**为什么能突破 O(n log n)**：比较排序的信息论下界 = log(n!) ≈ n log n。计数排序不做比较，不受这个下界约束。

---

## 四、STL 速查（本周用到）

| API | 用法 | 备注 |
|-----|------|------|
| `std::swap(a, b)` | `<utility>` 头文件 | 交换两个变量（容器元素也行） |
| `std::vector<T> v(n)` | `<vector>` | n 个默认值 |
| `std::vector<T> v(n, x)` | — | n 个 x |
| `v.size()` | 返回 `size_t` | 别和 `int` 做减法（下溢） |
| `std::is_sorted(begin, end)` | `<algorithm>` | 验证排序结果 |
| `std::sort(begin, end)` | `<algorithm>` | 库函数参考（本周禁用） |
| `std::begin(v)` / `std::end(v)` | — | 适用于数组和容器 |
| `assert(cond)` | `<cassert>` | 测试 |

---

## 五、LeetCode 题目指南

> **原则**：先独立思考 30 分钟，超时查题解，次日重写一遍。

### 912. 排序数组（Medium）

- **目的**：提交一份自己手写的排序通过所有测试
- **提示**：冒泡/选择/插入会超时 → 必须用 O(n log n) 级别的归并或快排
- **延伸**：如果快排 TLE（最坏情况），换随机 pivot

### 215. 数组中的第 K 个最大元素（Medium）

- **目的**：快排 partition 的经典应用
- **提示**：不需要排完整个数组——partition 一次就知道 pivot 的最终位置
- **优化方向**：快速选择算法（QuickSelect），平均 O(n)

### 88. 合并两个有序数组（Easy）

- **目的**：归并排序中 merge 子过程
- **提示**：**从后往前**合并可以原地，不需要额外空间

### 147. 对链表进行插入排序（Medium）

- **目的**：插入排序的链表版
- **提示**：用 dummy head 简化边界；每次从原链表头取一个节点插入到已排好的链表中

### 148. 排序链表（Medium）

- **目的**：归并排序的链表版 —— **本周压轴**
- **提示**：分治三步（快慢指针找中点 → 递归排序两半 → 合并）
- **要求**：O(n log n) 时间，O(1) 额外空间（进阶要求可暂忽略）

---

## 六、周日复盘清单（Day 7 照此填写）

把以下问题回答清楚，能答全 → 本周达标：

**理论层**：

1. 比较排序的下界是？为什么？
2. 哪些排序是稳定的？为什么？
3. 归并和快排都是分治，它们的"分"和"合"分别在哪一步？
4. 堆排序为什么不稳定？
5. 快排最坏情况怎么触发？怎么规避？

**动手层**：

6. 6 种排序是否都能闭卷手写并通过测试？
7. 5 道 LeetCode 是否都 AC？哪些题用了 30 分钟以上？

**反思层**：

8. 本周最大收获是什么？
9. 最困惑的点是什么？下周怎么解决？
10. 下周节奏要调整吗？

---

## 附：本周禁用清单

为了真正内化算法，本周**禁止**使用：

- ❌ `std::sort`（除了参考对比）
- ❌ `std::priority_queue`（Week 4 再学）
- ❌ 复制粘贴仓库代码——必须手敲
- ❌ 跳过 LeetCode 直接看题解（未思考满 30 分钟前）

**允许**使用：

- ✅ `std::swap`、`std::vector`、`std::is_sorted`
- ✅ 查 cppreference（但不查算法实现）
- ✅ 任何调试工具（gdb、printf、IDE 调试）
