# Week 3 学习手册：基础数据结构（链表/栈/队列）

> 配套学习方案：`spec/2026-04-19-learning-plan-design.md`
> 本周主题：`data_structures/` 基础部分
> 精读文件：5 个
> 配套 LeetCode：206, 21, 141, 142, 20, 232, 155

---

## 一、本周目标与产出

### 理解目标（能讲清楚）

- 链表 vs 数组的优劣（插入/删除/查找）
- **dummy head（哨兵节点）** 如何简化链表操作
- **快慢指针**（Floyd 判圈）的工作原理
- 链表反转的**三指针法**
- 栈（LIFO）与队列（FIFO）的本质区别
- 为什么"最小栈"需要辅助栈
- `new`/`delete` 配对与内存泄漏

### 动手目标（能闭卷手写）

| 结构 | 接口 | 复杂度 |
|------|------|--------|
| 单链表 | insert / delete / search / reverse | O(n) 查找 |
| 双向链表 | insert / delete（给节点指针） | O(1) 删除 |
| 数组栈 | push / pop / top / empty | O(1) |
| 链表队列 | enqueue / dequeue / front | O(1) |
| 链表反转（迭代） | — | O(n), O(1) 空间 |
| 链表反转（递归） | — | O(n), O(n) 栈空间 |

### 产出清单

- `my-impl/week-03/*.cpp` — 6 份手写实现
- `spec/notes/week-03-data-structures-basic.md` — 一页小结
- `spec/progress/week-03.md` — 进度表

---

## 二、每日安排（6 学习日 + 周日复盘）

### Day 1（周一）：单链表基础

- **上午 1h**：读 [data_structures/linked_list.cpp](data_structures/linked_list.cpp)
- **下午 1.5h**：闭卷手写单链表（insert / display / delete）
- **晚上 0.5h**：画图理解"带 dummy head 的链表"
- **LeetCode**：无（把基础打好）

### Day 2（周二）：双向链表 + 链表反转

- **上午 1h**：读 [data_structures/doubly_linked_list.cpp](data_structures/doubly_linked_list.cpp)
- **下午 1h**：读 [data_structures/reverse_a_linked_list.cpp](data_structures/reverse_a_linked_list.cpp)，**在纸上画三指针变化**
- **晚上 1h**：闭卷手写链表反转（迭代 + 递归两版）
- **LeetCode**：206（反转链表）

### Day 3（周三）：快慢指针

- **上午 1h**：学习 Floyd 判圈算法原理
- **下午 1h**：实践——找链表中点、找倒数第 k 个节点
- **晚上 1h**：**LeetCode 141**（环形链表）+ **142**（找环入口）

### Day 4（周四）：栈

- **上午 1h**：读 [data_structures/stack_using_array.cpp](data_structures/stack_using_array.cpp)
- **下午 1.5h**：闭卷手写栈（数组实现 + 链表实现各一份）
- **晚上 0.5h**：最小栈的设计思路（辅助栈）
- **LeetCode**：20（有效的括号）+ 155（最小栈）

### Day 5（周五）：队列

- **上午 1h**：读 [data_structures/queue_using_linked_list.cpp](data_structures/queue_using_linked_list.cpp)
- **下午 1h**：闭卷手写队列（链表实现）
- **晚上 1h**：思考"栈 ↔ 队列"互相模拟的原理
- **LeetCode**：232（用栈实现队列）

### Day 6（周六）：综合应用

- **上午 1.5h**：**LeetCode 21**（合并两个有序链表）— 经典递归 + 迭代都写
- **下午 1.5h**：回顾本周所有结构，做对比表
- **晚上 1h**：补漏——任意一个没 AC 的 LeetCode 题重做

### Day 7（周日）：复盘

- **上午 1h**：闭卷重写链表反转 + 栈/队列
- **下午 0.5h**：填写 `spec/notes/week-03-data-structures-basic.md`
- **下午 0.5h**：填写 `spec/progress/week-03.md`

---

## 三、基础数据结构深度讲解

### 3.1 单链表（Singly Linked List）

**核心思想**：每个节点包含数据和指向下一个节点的指针。

**节点定义**：

```cpp
struct Node {
    int val;
    Node* next;
    Node(int x) : val(x), next(nullptr) {}
};
```

**插入到尾部**（对照 [data_structures/linked_list.cpp](data_structures/linked_list.cpp)）：

```cpp
void insert(Node*& head, int val) {
    Node* newNode = new Node(val);
    if (!head) { head = newNode; return; }
    Node* cur = head;
    while (cur->next) cur = cur->next;
    cur->next = newNode;
}
```

**dummy head 模式**（初学者强烈推荐）：

```cpp
Node dummy;           // 栈上分配，不占堆
dummy.next = head;    // 把真正的 head 挂在 dummy 后
// ...各种操作都以 dummy 为参考点，不用处理 head 空的边界
return dummy.next;
```

**链表 vs 数组**：

| 操作 | 数组 | 链表 |
|------|------|------|
| 索引访问 | O(1) | O(n) |
| 头部插入 | O(n)（搬移） | O(1) |
| 尾部插入 | O(1) | O(n)（要遍历到尾） |
| 已知节点删除 | O(n) | O(1)（单链表需前驱，双链表 O(1)） |

### 3.2 双向链表（Doubly Linked List）

**核心**：节点多一个 `prev` 指针。

```cpp
struct DNode {
    int val;
    DNode *prev, *next;
};
```

**优势**：

- **O(1) 删除已知节点**（不用找前驱）
- 可反向遍历
- `std::list` 就是双向链表

**代价**：每节点多 8 字节（64 位系统），插入/删除要维护多一个指针。

### 3.3 链表反转 ⭐ 面试高频

#### 迭代法（三指针）

**核心**：`prev`, `curr`, `next` 三个指针协同。

```cpp
Node* reverse(Node* head) {
    Node *prev = nullptr, *curr = head;
    while (curr) {
        Node* next = curr->next;   // 先记住下一个
        curr->next = prev;          // 反转当前指针
        prev = curr;                // 前进
        curr = next;
    }
    return prev;                    // prev 就是新的 head
}
```

**关键细节**：

- 先存 `next`，否则 `curr->next = prev` 后无法再走下去
- 最后返回 `prev` 而非 `curr`（`curr` 已是 `nullptr`）
- 空链表 / 单节点都能正确处理（直接返回原 head）

#### 递归法

```cpp
Node* reverse(Node* head) {
    if (!head || !head->next) return head;      // 边界：空或单节点
    Node* newHead = reverse(head->next);         // 递归反转后面
    head->next->next = head;                     // 反接
    head->next = nullptr;                        // 断开
    return newHead;
}
```

**理解**：递归到最底，`newHead` 永远是原链表的尾节点。每层反接"当前节点和下一节点"。

### 3.4 快慢指针（Floyd's Tortoise and Hare）

**场景 1：找中点**

```cpp
Node *slow = head, *fast = head;
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
}
// slow 就是中点（偶数个节点时是中间偏右）
```

**场景 2：判断是否有环**

```cpp
while (fast && fast->next) {
    slow = slow->next;
    fast = fast->next->next;
    if (slow == fast) return true;  // 相遇 → 有环
}
return false;
```

**场景 3：找环入口（142 题）**

相遇后，`slow` 回到 head，两指针同步每次走 1 步，再次相遇处即入口。

**数学证明**（简记）：设 head 到环入口距离 a，环入口到相遇点距离 b，相遇点回到入口距离 c。`slow = a + b`，`fast = a + b + n(b+c)`。`fast = 2*slow` → `a = c + (n-1)(b+c)`，即从 head 走 a 步与从相遇点走 c 步（可能多绕圈）等价。

### 3.5 数组实现栈

**核心**（对照 [data_structures/stack_using_array.cpp:16-67](data_structures/stack_using_array.cpp#L16-L67)）：

```cpp
template <typename T>
class Stack {
    T* data;
    int capacity, topIdx;
public:
    Stack(int n) : data(new T[n]), capacity(n), topIdx(-1) {}
    ~Stack() { delete[] data; }
    bool empty() const { return topIdx == -1; }
    bool full() const { return topIdx == capacity - 1; }
    void push(T x) {
        if (full()) throw std::overflow_error("Stack full");
        data[++topIdx] = x;
    }
    T pop() {
        if (empty()) throw std::underflow_error("Stack empty");
        return data[topIdx--];
    }
    T top() const { return data[topIdx]; }
};
```

**关键细节**：

- `topIdx = -1` 表示空
- `++topIdx` 与 `topIdx++` 的差别：`push` 是前者（先移动再写），`pop` 是后者（先读再移动）
- 溢出处理：抛异常或扩容（仓库实现抛异常）

### 3.6 链表实现队列

**核心**：维护两个指针 `front`（队头）、`back`（队尾）。

```cpp
class Queue {
    Node *front, *back;
public:
    Queue() : front(nullptr), back(nullptr) {}
    void enqueue(int x) {
        Node* n = new Node(x);
        if (!back) front = back = n;
        else { back->next = n; back = n; }
    }
    int dequeue() {
        if (!front) throw std::underflow_error("Empty");
        int val = front->val;
        Node* tmp = front;
        front = front->next;
        if (!front) back = nullptr;  // 队列空了
        delete tmp;
        return val;
    }
};
```

**坑**：`dequeue` 到空时记得把 `back` 也置 `nullptr`，否则下次 `enqueue` 出错。

### 3.7 最小栈（LeetCode 155）

**需求**：push/pop/top 都是 O(1)，**getMin 也是 O(1)**。

**思路**：用两个栈，`data` 存数据，`minStk` 存"当前最小值"。

```cpp
class MinStack {
    stack<int> data, minStk;
public:
    void push(int x) {
        data.push(x);
        if (minStk.empty() || x <= minStk.top()) minStk.push(x);
    }
    void pop() {
        if (data.top() == minStk.top()) minStk.pop();
        data.pop();
    }
    int top() { return data.top(); }
    int getMin() { return minStk.top(); }
};
```

**关键细节**：

- **`<=` 而非 `<`**：重复最小值都要压入，否则 pop 会不同步
- 也可用一个栈存 `pair<int, int>`（值 + 当前最小）

### 3.8 用栈实现队列（232 题）

**思路**：两个栈 `in` 和 `out`。

- `push` → 压入 `in`
- `pop/peek` → 如果 `out` 空，把 `in` 全倒到 `out`；再从 `out` pop

**为什么对**：倒栈一次，顺序就反过来了，`out` 的栈顶就是最早入队的元素。

**均摊 O(1)**：每个元素最多进/出 `in` 一次、进/出 `out` 一次，总 4 次操作。

---

## 四、STL 速查

| API | 头文件 | 用法 |
|-----|--------|------|
| `std::list<T>` | `<list>` | 双向链表 |
| `std::forward_list<T>` | `<forward_list>` | 单向链表 |
| `std::stack<T>` | `<stack>` | 适配器，默认基于 deque |
| `std::queue<T>` | `<queue>` | 适配器 |
| `std::deque<T>` | `<deque>` | 双端队列，两端 O(1) |
| `std::stack::push/pop/top/empty` | — | 栈核心接口 |
| `std::queue::push/pop/front/back/empty` | — | 队列核心接口 |
| `std::deque::push_front/push_back/pop_front/pop_back` | — | 双端操作 |

**坑**：

- `std::stack::pop()` 返回 `void` 不返回值（要先 `top()` 再 `pop()`）
- `std::queue::pop()` 同上

---

## 五、LeetCode 题目指南

### 206. 反转链表（Easy）

- **目的**：三指针模板
- **提示**：迭代 + 递归两种都写

### 21. 合并两个有序链表（Easy）

- **目的**：链表版归并的 merge
- **提示**：用 dummy head 简化边界；迭代 + 递归两种

### 141. 环形链表（Easy）

- **目的**：快慢指针判环
- **提示**：`fast` 走两步，遇 `nullptr` 说明无环

### 142. 环形链表 II（Medium）

- **目的**：找环入口
- **提示**：判环后，`slow` 回到 head，同步走直到再次相遇

### 20. 有效的括号（Easy）

- **目的**：栈的经典应用
- **提示**：左括号入栈，右括号匹配栈顶；结束后栈必须为空

### 232. 用栈实现队列（Easy）

- **目的**：两栈模拟队列
- **提示**：`in` 栈接收，`out` 栈弹出；`out` 空时从 `in` 倒过来

### 155. 最小栈（Medium）

- **目的**：辅助栈思想
- **提示**：两个栈，`minStk` 在"新来的值 <= 当前最小"时同步压入

---

## 六、周日复盘清单

**理论层**：

1. 链表相比数组在哪些场景更优？在哪些场景更差？
2. dummy head 的作用是什么？
3. 快慢指针判环为什么一定会相遇（不会跨过）？
4. 找环入口的数学推导？
5. 最小栈为什么用 `<=` 而非 `<` 同步？

**动手层**：

6. 能否闭卷写链表反转（迭代 + 递归）？
7. 能否闭卷写数组栈、链表队列？
8. 7 道 LeetCode 是否都 AC？

**反思层**：

9. 本周最大收获是？
10. 最困惑的是？下周怎么解决？
11. 下周节奏要调整吗？

---

## 附：本周禁用清单

**禁用**：

- ❌ `std::list` / `std::stack` / `std::queue`（手写时）
- ❌ 复制粘贴仓库代码

**允许**：

- ✅ LeetCode 提交时用 STL（面试场景）
- ✅ 查 cppreference
- ✅ 在纸上画图辅助理解（推荐）
