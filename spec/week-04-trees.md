# Week 4 学习手册：树结构

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`data_structures/` 树相关
> 精读文件：4 个（红黑树选读）
> 配套 LeetCode：94, 144, 145, 102, 98, 104, 110, 236

---

## 一、本周目标与产出

### 理解目标（能讲清楚）

- 二叉树三种 DFS 遍历（前/中/后序）的递归与迭代写法
- 层序遍历（BFS）用队列实现
- BST 的性质与插入/删除/查找
- AVL 的平衡因子与四种旋转（LL / RR / LR / RL）
- 二叉堆用数组实现的父子索引关系
- Morris 遍历如何做到 O(1) 空间

### 动手目标（能闭卷手写）

| 结构/算法 | 关键操作 | 复杂度 |
|-----------|----------|--------|
| 二叉树三种 DFS 遍历（递归） | 前/中/后序 | O(n) |
| 二叉树三种 DFS 遍历（迭代） | 用栈模拟 | O(n) |
| 层序遍历（BFS） | 用队列 | O(n) |
| BST | 插入/查找/删除 | 平均 O(log n) |
| 小顶堆 | insert / extractMin / heapify | O(log n) |
| Morris 中序 | O(1) 空间 | O(n) |

### 产出清单

- `my-impl/week-04/*.cpp` — 5-6 份手写实现
- `spec/notes/week-04-trees.md` — 一页小结
- `spec/progress/week-04.md` — 进度表

---

## 二、每日安排（6 学习日 + 周日复盘）

### Day 1（周一）：三种 DFS 遍历

- **上午 1h**：递归实现前/中/后序（非常短，但要彻底理解递归顺序）
- **下午 1.5h**：迭代实现三种遍历（用栈模拟），重点是后序
- **晚上 0.5h**：画遍历顺序图
- **LeetCode**：94（中序遍历）+ 144（前序遍历）

### Day 2（周二）：后序 + 层序

- **上午 1h**：后序迭代（技巧：前序改 "根-右-左" 后反转）
- **下午 1h**：层序遍历（BFS + 队列）
- **晚上 1h**：**LeetCode 145**（后序）+ **102**（层序）

### Day 3（周三）：BST

- **上午 1h**：读 [data_structures/binary_search_tree.cpp](data_structures/binary_search_tree.cpp)
- **下午 1.5h**：闭卷手写 BST 的 insert / find / delete
- **晚上 0.5h**：理解"中序遍历 BST 即升序"的证明
- **LeetCode**：98（验证 BST）

### Day 4（周四）：AVL 简介 + 平衡树理论

- **上午 1.5h**：读 [data_structures/avltree.cpp](data_structures/avltree.cpp)，重点看四种旋转
- **下午 1h**：在纸上画 LL/RR/LR/RL 四种旋转过程（不要求手写完整 AVL）
- **晚上 0.5h**：红黑树简介（了解即可，不写）
- **LeetCode**：110（平衡二叉树判断）

### Day 5（周五）：二叉堆

- **上午 1h**：读 [data_structures/binaryheap.cpp](data_structures/binaryheap.cpp)
- **下午 1.5h**：闭卷手写小顶堆（insert/extractMin/heapify）
- **晚上 0.5h**：对照 Week 1 的 heap_sort 理解上浮 vs 下沉
- **LeetCode**：104（二叉树的最大深度）

### Day 6（周六）：Morris + 综合

- **上午 1h**：读 [data_structures/morrisinorder.cpp](data_structures/morrisinorder.cpp)，关注"线索"的建立和拆除
- **下午 1h**：闭卷手写 Morris 中序
- **晚上 1h**：**LeetCode 236**（最近公共祖先 LCA）— 经典树题

### Day 7（周日）：复盘

- **上午 1h**：闭卷重写 BST + 小顶堆
- **下午 0.5h**：填写 `spec/notes/week-04-trees.md`
- **下午 0.5h**：填写 `spec/progress/week-04.md`

---

## 三、树结构深度讲解

### 3.1 二叉树基础

**节点定义**：

```cpp
struct TreeNode {
    int val;
    TreeNode *left, *right;
    TreeNode(int x) : val(x), left(nullptr), right(nullptr) {}
};
```

**三种 DFS 遍历顺序**：

- **前序**（Preorder）：根 → 左 → 右
- **中序**（Inorder）：左 → 根 → 右
- **后序**（Postorder）：左 → 右 → 根

#### 递归版（超级简洁）

```cpp
void inorder(TreeNode* root) {
    if (!root) return;
    inorder(root->left);
    cout << root->val << " ";      // 换到前/后只需调换这行位置
    inorder(root->right);
}
```

#### 迭代版：前序

```cpp
void preorder(TreeNode* root) {
    if (!root) return;
    stack<TreeNode*> stk;
    stk.push(root);
    while (!stk.empty()) {
        TreeNode* node = stk.top(); stk.pop();
        cout << node->val << " ";
        if (node->right) stk.push(node->right);  // 右先压
        if (node->left)  stk.push(node->left);   // 左后压（先出）
    }
}
```

#### 迭代版：中序

```cpp
void inorder(TreeNode* root) {
    stack<TreeNode*> stk;
    TreeNode* cur = root;
    while (cur || !stk.empty()) {
        while (cur) { stk.push(cur); cur = cur->left; }  // 左链全压栈
        cur = stk.top(); stk.pop();
        cout << cur->val << " ";
        cur = cur->right;
    }
}
```

#### 迭代版：后序（技巧法）

**思路**：前序是"根左右"，改写成"根右左"后反转序列 = "左右根"（后序）。

```cpp
void postorder(TreeNode* root) {
    if (!root) return;
    stack<TreeNode*> stk;
    vector<int> result;
    stk.push(root);
    while (!stk.empty()) {
        TreeNode* node = stk.top(); stk.pop();
        result.push_back(node->val);
        if (node->left)  stk.push(node->left);
        if (node->right) stk.push(node->right);
    }
    reverse(result.begin(), result.end());
}
```

### 3.2 层序遍历（BFS）

**用队列**：

```cpp
void levelOrder(TreeNode* root) {
    if (!root) return;
    queue<TreeNode*> q;
    q.push(root);
    while (!q.empty()) {
        int sz = q.size();               // 当前层的节点数
        for (int i = 0; i < sz; i++) {
            TreeNode* node = q.front(); q.pop();
            cout << node->val << " ";
            if (node->left)  q.push(node->left);
            if (node->right) q.push(node->right);
        }
        cout << endl;                    // 换层
    }
}
```

**关键**：`int sz = q.size()` 固化当前层大小，内层循环后队列里就是下一层。

### 3.3 二叉搜索树（BST）

**性质**：对任何节点，左子树所有值 < 节点值 < 右子树所有值。

**推论**：**中序遍历即升序**。

#### 插入

```cpp
TreeNode* insert(TreeNode* root, int val) {
    if (!root) return new TreeNode(val);
    if (val < root->val) root->left  = insert(root->left, val);
    else                  root->right = insert(root->right, val);
    return root;
}
```

#### 查找

```cpp
TreeNode* find(TreeNode* root, int val) {
    if (!root || root->val == val) return root;
    return val < root->val ? find(root->left, val) : find(root->right, val);
}
```

#### 删除 ⭐ 难点

三种情况：

1. **叶子节点**：直接删除
2. **只有一个子节点**：子节点取代自己
3. **有两个子节点**：用右子树的最小值（或左子树的最大值）替换，然后递归删除那个值

```cpp
TreeNode* remove(TreeNode* root, int val) {
    if (!root) return nullptr;
    if (val < root->val) root->left  = remove(root->left, val);
    else if (val > root->val) root->right = remove(root->right, val);
    else {
        // 情况 1 & 2
        if (!root->left) { TreeNode* r = root->right; delete root; return r; }
        if (!root->right) { TreeNode* l = root->left; delete root; return l; }
        // 情况 3：找右子树的最小值
        TreeNode* minNode = root->right;
        while (minNode->left) minNode = minNode->left;
        root->val = minNode->val;
        root->right = remove(root->right, minNode->val);
    }
    return root;
}
```

**BST 的退化**：有序插入 → 退化成链表 → 操作 O(n)。这就是引入 AVL / 红黑树的动因。

### 3.4 AVL 树（自平衡 BST）

**平衡因子** = 左子树高度 − 右子树高度 ∈ {−1, 0, 1}。

超出 → 失衡 → 旋转。

**四种旋转**：

| 情况 | 失衡点 | 操作 |
|------|--------|------|
| LL | 左子树的左子树高 | 右旋 |
| RR | 右子树的右子树高 | 左旋 |
| LR | 左子树的右子树高 | 先左旋子节点，再右旋 |
| RL | 右子树的左子树高 | 先右旋子节点，再左旋 |

**右旋示意**（LL 情形）：

```
      y              x
     / \           /   \
    x   C   ->    A     y
   / \                 / \
  A   B               B   C
```

**初学者建议**：理解 AVL 思想，能画出旋转；不强求闭卷手写完整 AVL（代码冗长且面试少考）。

**相关面试题**：LeetCode 110（判断平衡）— 只要求判断，不要求实现。

### 3.5 二叉堆（Binary Heap）

**核心性质**（小顶堆）：任一节点的值 ≤ 其子节点的值。

**数组表示**：

- 节点 `i` 的父：`(i-1) / 2`
- 左子：`2*i + 1`
- 右子：`2*i + 2`

#### 插入（上浮 / swim）

对照 [data_structures/binaryheap.cpp:55-71](data_structures/binaryheap.cpp#L55-L71)：

```cpp
void insert(int k) {
    harr[heap_size++] = k;                 // 放到末尾
    int i = heap_size - 1;
    while (i != 0 && harr[parent(i)] > harr[i]) {
        swap(harr[i], harr[parent(i)]);    // 与父比较，小则上浮
        i = parent(i);
    }
}
```

#### 提取最小（下沉 / sink）

```cpp
int extractMin() {
    if (heap_size <= 0) return INT_MAX;
    if (heap_size == 1) return harr[--heap_size];
    int root = harr[0];
    harr[0] = harr[--heap_size];            // 末尾填到根
    heapify(0);                             // 下沉
    return root;
}

void heapify(int i) {
    int l = 2*i+1, r = 2*i+2, smallest = i;
    if (l < heap_size && harr[l] < harr[smallest]) smallest = l;
    if (r < heap_size && harr[r] < harr[smallest]) smallest = r;
    if (smallest != i) {
        swap(harr[i], harr[smallest]);
        heapify(smallest);
    }
}
```

**堆 vs BST**：

- 堆：只保证**根是最值**，其他层无序 → 只能 O(log n) 拿最值
- BST：全局有序 → 任意查询 O(log n)

### 3.6 Morris 遍历（O(1) 空间）

**核心思想**：利用叶子节点的空 `right` 指针，临时指向中序后继，遍历完再恢复。

对照 [data_structures/morrisinorder.cpp:52-83](data_structures/morrisinorder.cpp#L52-L83)：

```cpp
void morrisInorder(TreeNode* root) {
    TreeNode* curr = root;
    while (curr) {
        if (!curr->left) {
            cout << curr->val << " ";       // 左空 → 输出 → 右走
            curr = curr->right;
        } else {
            // 找 curr 左子树的最右节点（中序前驱）
            TreeNode* pre = curr->left;
            while (pre->right && pre->right != curr) pre = pre->right;

            if (!pre->right) {
                pre->right = curr;          // 建立线索
                curr = curr->left;
            } else {
                pre->right = nullptr;       // 拆除线索
                cout << curr->val << " ";
                curr = curr->right;
            }
        }
    }
}
```

**关键**：

- 线索建立时不输出，拆除时才输出（对应中序）
- 每条边最多被访问 3 次 → 时间 O(n)
- 空间 O(1)（不用栈也不用递归）

**适用**：需要 O(1) 空间的场景，实战中用得较少但面试常问。

### 3.7 红黑树（简介，不要求实现）

- 近似平衡 BST（不是严格平衡的 AVL）
- 五条性质：根黑、叶黑、红节点不连续、任意节点到叶子的黑节点数相等、节点非红即黑
- `std::map` / `std::set` 的底层实现
- 面试**极少**考手写

---

## 四、STL 速查

| API | 用法 | 备注 |
|-----|------|------|
| `std::set<T>` | `<set>` | 有序集合（红黑树） |
| `std::map<K, V>` | `<map>` | 有序字典 |
| `std::multiset<T>` | `<set>` | 允许重复 |
| `std::priority_queue<T>` | `<queue>` | 默认大顶堆 |
| `std::priority_queue<T, vector<T>, greater<T>>` | — | 小顶堆 |
| `pq.push(x)` / `pq.top()` / `pq.pop()` | — | 堆接口 |
| `set::insert/erase/count/find` | — | 集合接口 |
| `set::lower_bound(x)` | — | 有序查找 |

---

## 五、LeetCode 题目指南

### 94. 二叉树的中序遍历（Easy）

- **目的**：三种遍历模板起手
- **提示**：递归和迭代都写一遍

### 144. 二叉树的前序遍历（Easy）

- **目的**：同上
- **提示**：迭代用栈，注意左右压栈顺序

### 145. 二叉树的后序遍历（Easy）

- **目的**：后序是三种遍历里最绕的
- **提示**：技巧法（改根右左 + 反转）或双栈法

### 102. 二叉树的层序遍历（Medium）

- **目的**：BFS + 分层
- **提示**：外层 while 处理队列，内层 for 固化当前层 size

### 98. 验证 BST（Medium）

- **目的**：BST 性质的应用
- **提示**：中序遍历判升序；或递归传 `(minVal, maxVal)` 区间
- **坑**：不能只比较 `left.val < root.val`，必须整棵子树都满足

### 104. 二叉树的最大深度（Easy）

- **目的**：递归入门
- **提示**：`max(depth(left), depth(right)) + 1`

### 110. 平衡二叉树（Easy）

- **目的**：AVL 平衡性判断
- **提示**：自底向上计算高度，-1 表示已失衡（剪枝）

### 236. 二叉树的最近公共祖先（Medium）

- **目的**：LCA 经典题
- **提示**：递归——左右子树分别找 p、q，都找到 → 当前节点就是 LCA

---

## 六、周日复盘清单

**理论层**：

1. 前/中/后序的递归版只差一行，为什么？
2. 中序遍历 BST 为什么得到升序？
3. AVL 在什么时候需要双旋转？
4. 堆和 BST 都能 O(log n) 插入，为什么用途不同？
5. Morris 遍历为什么是 O(1) 空间？

**动手层**：

6. 能否闭卷写三种 DFS 遍历的迭代版？
7. 能否闭卷写 BST 的 insert/delete？
8. 能否闭卷写小顶堆 insert/extractMin？
9. 8 道 LeetCode 是否都 AC？

**反思层**：

10. 本周最大收获是？
11. 最困惑的是？下周怎么解决？
12. 下周节奏要调整吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ `std::set` / `std::map`（手写 BST 时）
- ❌ `std::priority_queue`（手写堆时）

**允许**：

- ✅ LeetCode 提交用 STL（面试场景）
- ✅ 画图辅助理解（强烈推荐）
- ✅ 查 cppreference
