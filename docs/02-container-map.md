# ?? 武器库映射：STL 容器 vs Java Collections

对于 ACMer 来说，STL 就是我们的左膀右臂。刚转 Java 时，最痛苦的莫过于想用 `vector` 却只能敲出 `Array...`，想用 `priority_queue` 却发现默认是小根堆。

本文档旨在建立从 C++ STL 到 Java集合框架 (JCF) 的**肌肉记忆映射**，并指出那些可能导致 TLE (Time Limit Exceeded) 或 MLE (Memory Limit Exceeded) 的坑。

## 1. 动态数组：std::vector -> ArrayList

在 Java 中，数组 `int[]` 是定长的。如果需要动态扩容，必须使用 `ArrayList`。

### 核心操作对比

| 操作     | C++ (`std::vector<int> v`) | Java (`List<Integer> v = new ArrayList<>()`) |
| :------- | :------------------------- | :------------------------------------------- |
| **添加** | `v.push_back(val)`         | `v.add(val)`                                 |
| **访问** | `v[i]`                     | `v.get(i)`                                   |
| **修改** | `v[i] = val`               | `v.set(i, val)`                              |
| **大小** | `v.size()`                 | `v.size()`                                   |
| **排序** | `sort(v.begin(), v.end())` | `Collections.sort(v)`                        |
| **清空** | `v.clear()`                | `v.clear()`                                  |

### ?? ACMer 避坑指南
1.  **基本类型包装费（Autoboxing Overhead）：**
    Java 的 `ArrayList` **不能**存 `int`，只能存 `Integer`。
    - C++: `vector<int>` 内存是连续的，非常紧凑。
    - Java: `ArrayList<Integer>` 存的是对象引用。这不仅费内存（MLE 警告），而且涉及自动拆装箱，性能比原生数组慢。
    - **建议：** 如果在高并发或高性能算法题中，**已知数据范围，尽量直接用 `int[]` 数组**，哪怕稍微麻烦点写扩容逻辑。

2.  **扩容机制：**
    - `ArrayList` 默认扩容是 1.5 倍（oldCapacity + oldCapacity >> 1）。
    - 如果已知数据量，请在构造时指定大小：`new ArrayList<>(100000)`，避免频繁扩容带来的拷贝开销。

---

## 2. 优先队列：std::priority_queue -> PriorityQueue

这是无数转 Java 选手踩到的第一个**巨坑**。

### 核心差异：堆的方向

*   **C++ STL**: `priority_queue` 默认是 **大根堆** (Max-Heap)。
*   **Java**: `PriorityQueue` 默认是 **小根堆** (Min-Heap)。

### 代码对比

#### 场景 A：默认情况
**C++ (大根堆):**
```cpp
priority_queue<int> pq;
pq.push(10);
pq.push(5);
cout << pq.top(); // 输出 10
```

**Java (小根堆):**

```java
PriorityQueue<Integer> pq = new PriorityQueue<>();
pq.add(10);
pq.add(5);
System.out.println(pq.peek()); // 输出 5 (注意不是 top 而是 peek)
```

#### 场景 B：如何实现大根堆？

**C++:**

```c++
priority_queue<int, vector<int>, greater<int>> pq; // 变成小根堆
```

**Java (推荐使用 Lambda):**

```java
// 方法 1: 使用 Collections.reverseOrder()
PriorityQueue<Integer> pq = new PriorityQueue<>(Collections.reverseOrder());

// 方法 2: Lambda 表达式 (推荐，通用性强)
// (a, b) -> b - a 表示降序
PriorityQueue<Integer> pq = new PriorityQueue<>((a, b) -> b - a);
```

## 3. 映射表：std::map / unordered_map -> TreeMap / HashMap

### 映射关系

- std::map (红黑树, 有序) -> java.util.TreeMap
- std::unordered_map (哈希表, 无序) -> java.util.HashMap (99% 的情况下你只需要这个)

### 核心操作对比

| 操作             | C++ (unordered_map<string, int> mp) | Java (Map<String, Integer> mp = new HashMap<>())      |
| ---------------- | ----------------------------------- | ----------------------------------------------------- |
| **插入/更新**    | mp["key"] = val                     | mp.put("key", val)                                    |
| **查找值**       | mp["key"] (如果不存在会新建!)       | mp.get("key") (如果不存在返回 null)                   |
| **查找是否存在** | if(mp.count("key"))                 | if(mp.containsKey("key"))                             |
| **带默认值查找** | 需手动判断                          | mp.getOrDefault("key", 0) (神器!)                     |
| **遍历**         | for(auto &it : mp)                  | for(Map.Entry<String, Integer> entry : mp.entrySet()) |

### ?? ACMer 避坑指南

1. **关于 mp["key"] 的陷阱：**
   C++ 中，如果你访问一个不存在的 key，它会自动创建一个默认值（0）。
   Java 中 mp.get("key") 会返回 null。如果你直接拆箱 int val = mp.get("key")，会抛出 **NullPointerException (NPE)**。
   **正确姿势：** 永远使用 getOrDefault 或者先 check containsKey。
2. **自定义对象作为 Key：**
   C++ 中重载 < 或提供 Hash 函数即可。
   Java 中必须在类里重写 equals() 和 hashCode() 方法（用 IDE 自动生成或 Lombok @Data），否则 Map 会认为两个属性相同的对象是不同的 Key。

------



## 4. 栈与队列：std::stack / std::queue -> Deque

Java 中虽然有 Stack 类，但它是继承自 Vector 的古董类，全是 synchronized 锁，慢且不推荐。
**Java 官方推荐使用 Deque (双端队列) 接口的实现类 ArrayDeque 来代替栈和队列。**

### 替代方案

| C++ STL        | Java 推荐写法 (Deque<Integer> dq = new ArrayDeque<>())   |
| -------------- | -------------------------------------------------------- |
| **std::stack** | dq.push(val) (入栈), dq.pop() (出栈), dq.peek() (看顶)   |
| **std::queue** | dq.offer(val) (入队), dq.poll() (出队), dq.peek() (看头) |

**记忆口诀：**

- 当栈用：push/pop
- 当队列用：offer/poll

------



## 5. Pair 与排序

C++ 的 std::pair 简直是懒人神器。Java 以前没有好用的 Pair，现在的替代方案：

1. **JavaFX Pair:** import javafx.util.Pair; (不推荐，依赖 JDK 版本，有时候找不到)
2. **自定义静态内部类 (推荐):**
   作为 ACMer，手写一个 Node 类只需要 10 秒。

```java
// 放在类内部
static class Node implements Comparable<Node> {
    int x, y;
    public Node(int x, int y) { this.x = x; this.y = y; }
    
    // 实现 Comparable 接口用于排序
    @Override
    public int compareTo(Node o) {
        return this.x - o.x; // 升序
    }
}
```

使用 Lambda 进行复杂排序（类似 C++ 的 cmp 函数）：

```java
List<Node> list = new ArrayList<>();
// 按 x 升序，如果 x 相等按 y 降序
Collections.sort(list, (a, b) -> {
    if (a.x != b.x) return a.x - b.x;
    return b.y - a.y;
});
```

