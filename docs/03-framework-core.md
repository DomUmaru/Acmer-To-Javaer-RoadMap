# ?? 工程思维：Spring 到底在干嘛？

如果说学习 Java 语法只是换了一种写法来刷题，那么学习 Spring 就相当于从“单机游戏”进化到了“多人在线网游”的服务器开发。

很多 ACMer 转后端时，最困惑的就是：**“我的 `main` 函数去哪了？程序是从哪里开始运行的？”**

## 1. 控制反转 (IoC)：别打电话给我，我会打给你

在 ACM 比赛中，你是上帝。你写 `main` 函数，你决定什么时候 `new` 一个对象，什么时候销毁它，什么时候调用它的函数。

```cpp
// C++ 思维 (上帝模式)
int main() {
    GameService game = new GameService(); // 我控制创建
    game.start(); // 我控制流程
    return 0;
}
```

在 Spring 开发中，**Spring 容器才是上帝**。这就是 **控制反转 (Inversion of Control, IoC)**。

- 你不再直接 new 对象。
- 你只需要告诉 Spring：“我需要一个 GameService”。
- Spring 会在启动时帮你把对象创建好，放到一个巨大的 Map 里，等你用的时候直接塞给你（这就是 **依赖注入 DI**）。

```java
// Spring 思维 (打工模式)
@Service
public class UserAction {
    
    // 喂，Spring，把那个做好的 GameService 给我拿过来
    @Autowired 
    private GameService gameService;

    public void play() {
        // 我不管它是怎么 new 出来的，直接用
        gameService.start();
    }
}
```

**ACMer 类比：**
以前你是**厨师**（自己买菜、切菜、炒菜）。
现在你是**食客**（只管点菜 @Autowired，Spring 厨房负责把菜做好端上来）。

## 2. 单例模式与线程安全：全局变量的诅咒

在刷题时，我们喜欢开全局大数组 int a[100005]，方便又快。
**在 Spring 中，默认所有的 Bean（Service, Controller）都是单例的 (Singleton)。**

这意味着：**全服务器只有一个 UserService 对象，成千上万个并发请求都在共用这同一个对象！**

### ? 绝对禁止的写法 (ACMer 习惯性错误)

```java
@Service
public class RankingService {
    // ? 危险！这是类成员变量，被所有线程共享！
    private int tempScore = 0; 

    public void calculate(int score) {
        tempScore = score; // 线程 A 改了这里
        // ... 线程 B 突然进来，把 tempScore 改成了别的值
        saveToDb(tempScore); // 线程 A 存入了 B 的值！数据乱了！
    }
}
```

### ?正确写法

把变量写在方法内部（栈内存），这样每个线程都有自己独立的一份。

```java
@Service
public class RankingService {
    public void calculate(int score) {
        int tempScore = score; // 局部变量，安全
        saveToDb(tempScore);
    }
}
```

**结论：** 在 Spring 的 Bean (Service/Controller) 里，**严禁使用有状态的成员变量**（除非你知道自己在做什么，比如用 AtomicInteger 或锁）。

## 3. 生命周期：从 "Run Once" 到 "Always On"

- **ACM 程序：** 启动 -> 读数据 -> 计算 -> 输出 -> **进程结束**。生命周期几百毫秒。内存随便开，反正跑完就回收。
- **Web 服务：** 启动 -> **死循环等待请求** (Tomcat 线程池) -> 处理请求 -> 等待下一个。生命周期可能长达数月。

**这意味着：**

1. **内存泄漏是致命的：** 你开了一个 static List<User> cache 往里塞数据却从来不删，三天后服务器 OOM (Out Of Memory) 宕机。
2. **连接池的重要性：** 不能每次处理请求都 new Connection() 连数据库，必须用池子（Pool）复用连接，否则数据库瞬间被打挂。

## 4. AOP：一种高级的“打补丁”

你肯定写过这种代码：在调试 DFS 时，在函数开头打印 cout << "Enter DFS" << endl;，在结尾打印 cout << "Exit" << endl;。

如果要把全项目 100 个函数都加上这个日志怎么办？

- **C++:** 累死，复制粘贴 100 次。
- **Spring AOP (面向切面编程):** 定义一个“切面”，告诉 Spring：“在所有函数执行前，帮我运行这段打印日志的代码”。

**应用场景：** 统一日志记录、计算函数运行时间、统一权限检查、数据库事务管理（@Transactional）。

------



> **总结：**
> 转 Spring 开发，本质上是交出控制权。你不再关注“怎么启动”，而是关注“业务逻辑怎么写”。
> 既然选择了 Java 后端，就要学会信任 Spring 这个强大的“管家”。