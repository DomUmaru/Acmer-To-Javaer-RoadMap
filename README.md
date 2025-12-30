# 🚀 ACMer to Javaer: The Missing Manual
> 献给所有曾经在 OJ 刷题，如今在 CRUD 中迷茫的退役选手们。

[![License](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Java](https://img.shields.io/badge/Java-17%2B-orange)](https://www.oracle.com/java/)
[![Author](https://img.shields.io/badge/Author-DomUmaru-ff69b4)](https://github.com/DomUmaru)

## 📖 前言 (Introduction)

我曾是一名 ICPC 亚洲区域赛铜牌选手，习惯了 `cin >> n`，习惯了全局数组一把梭，习惯了手写线段树。
当我退役转型 Java 后端开发时，我发现：
- 这里的 Map 不是红黑树？
- 为什么 `a + b` 要写成 `a.add(b)`？
- Spring 的 Bean 到底是个什么魔法？

这个仓库**不是**教你 Java 语法（去买本书就行），而是**利用你深厚的算法功底，建立 C++ 到 Java 的思维映射，实现降维打击**。

## 🗺️ 导航 (Roadmap)

### Phase 1: 武器库转换 (STL vs Collections)
ACMer 手里没有 `vector` 和 `map` 就不会走路。这里帮你快速找回“手感”。
- [x] [STL 容器的 Java 完美替代方案](docs/02-container-map.md) `Pending`
- [ ] 优先队列的坑：大根堆还是小根堆？
- [ ] 甚至比 C++ 更快的 IO：StreamTokenizer

### Phase 2: 思维重构 (Mindset Shift)
- [ ] **从 "Run Once" 到 "Always On"**：GC 是怎么把你的服务器搞挂的？
- [ ] **从 "Main Function" 到 "IoC Container"**：别再想着控制一切，把控制权交给 Spring。
- [ ] **从 "O(N)" 到 "QPS"**：高并发场景下的算法优化。

### Phase 3: 实战复盘 (Project)
结合我的微服务高并发（秒杀）项目经验，剖析 ACMer 写业务代码的通病。
- [ ] 为什么你的数据库连接池炸了？
- [ ] 缓存一致性问题

## ⚔️ 快速对照表 (Cheat Sheet)

| C++ STL | Java Equivalent | 核心注意点 (Key Differences) |
| :--- | :--- | :--- |
| `std::vector<int>` | `ArrayList<Integer>` | Java 泛型不支持基本类型，会有自动拆装箱开销 |
| `std::priority_queue` | `PriorityQueue` | **巨坑警告**：C++ 默认大根堆，Java 默认**小根堆** |
| `std::map` | `TreeMap` | 底层都是红黑树，但 Java 里 90% 的场景请用 `HashMap` |
| `std::pair` | `javafx.util.Pair` / 自定义类 | 别找了，Java 原生 Pair 很难用，建议自己写个 `Node` 类 |
| `std::sort` | `Collections.sort` | 对象排序必须实现 `Comparable` 接口或传入 `Comparator` |

## 👨‍💻 关于作者

**DomUmaru**
- Retired ICPC Regional Bronze Medalist 🥉
- Java Backend Developer
- 正在从算法思维向架构思维转型的路上

---
*如果你觉得这个仓库对你有帮助，请点亮右上角的 ⭐️ Star，这是我更新的最大动力！*
