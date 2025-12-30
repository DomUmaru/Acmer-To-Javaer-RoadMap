# 🪤 语法陷阱：从 C++ 到 Java 的阵痛

对于习惯了 C++ 自由灵魂的 ACMer 来说，Java 的条条框框可能会让你觉得像是戴着镣铐跳舞。
这里总结了转型初期最容易踩的语法坑。

## 1. 痛苦的 "Reference" (引用)

在 C++ 里，你很清楚 `Node* p` 是指针，`Node p` 是对象，`Node& p` 是引用。
在 Java 里，**除了基本数据类型 (int, double, boolean...)，其他一切都是引用（指针）！**

### ❌ 常见的错误直觉
```cpp
// C++
Node a = b; // 发生了对象拷贝，a 和 b 是两个独立的对象
a.val = 10; // b 不受影响
```

```java
// Java
Node a = b; // 仅仅是拷贝了地址！a 和 b 指向堆内存里的同一个对象
a.val = 10; // b.val 也变成了 10 ！
```

**ACMer 注意：**
如果你想在 Java 里复制一个对象（比如回溯算法里保存状态），你不能直接赋值，必须手动 new 一个新对象并把属性拷过去，或者实现 Cloneable 接口（不推荐，坑更多）。



## 2. 字符串比较的 == 陷阱

这是面试必问，也是新手必坑。

- **C++:** s1 == s2 比较的是字符串的内容。
- **Java:** s1 == s2 比较的是**内存地址**！

```java
String s1 = new String("hello");
String s2 = new String("hello");

if (s1 == s2) { 
    // 永远进不来！因为它们是两个不同的对象地址
}

if (s1.equals(s2)) {
    // 这才是正确的比较方式
}
```

## 3. 消失的运算符重载

C++ 里的高精度模板写起来很爽：BigInt c = a + b;
Java 里虽然有 BigInteger，但你必须忍受这样的写法：

```java
BigInteger a = new BigInteger("100");
BigInteger b = new BigInteger("200");

// ❌ 编译错误
// BigInteger c = a + b; 

// ✅ 正确写法
BigInteger c = a.add(b);
```

这对于写几何题或者数论题的 ACMer 来说简直是折磨，但这就是 Java 的“严谨”。

## 4. 传值还是传引用？

永远记住一句话：**Java 只有按值传递 (Pass by Value)。**

当你传递一个对象给函数时，你传递的是**这个对象引用的副本**（也就是地址的拷贝）。

```java
void swap(Node a, Node b) {
    Node temp = a;
    a = b;
    b = temp;
    // 函数结束，a 和 b 的局部变量销毁，外面的对象纹丝不动
}
```

**结论：** 在 Java 里想写 swap 函数？死心吧，只能通过修改对象内部成员变量来实现，或者把对象包在数组里。

## 5. 数组初始化的区别

**C++:**

```c++
int a[10]; // 里面的值是随机垃圾值！必须 memset
```

**Java:**

```java
int[] a = new int[10]; // 默认全为 0 (boolean 默认为 false, 对象默认为 null)
```

Java 帮你做了 memset，这是好事，但也意味着 Java 的数组分配比 C++ 慢。