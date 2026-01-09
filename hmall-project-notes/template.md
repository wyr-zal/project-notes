## ⭐ 索引
hmall-project-notes/
├── README.md                # 总索引（手机最常打开）
│
├── 00-项目全局理解/
│   ├── 项目整体架构.md
│   ├── 请求完整链路.md
│
├── 01-通用基础设施/
│   ├── 统一返回对象R.md
│   ├── 全局异常处理.md
│   ├── UserContext设计.md
│
├── 02-微服务调用/
│   ├── Feign基础用法.md
│   ├── Feign用户信息透传.md
│   ├── Feign降级FallbackFactory.md
│
├── 03-安全与鉴权/
│   ├── 登录流程.md
│   ├── Token解析.md
│
├── 04-高阶设计（可选）
│   ├── 幂等设计.md
│   ├── 事务边界.md
│
└── 99-面试速查/
    ├── 高频问题Q&A.md
    ├── 三分钟讲清这个项目.md


## Feign RequestInterceptor（用户信息透传）

### 1️⃣ 痛点
- 微服务之间调用，用户信息丢失
- 下游服务无法知道当前是谁在操作

---

### 2️⃣ 做法（设计思路）
- 在 Feign 请求发送前拦截
- 从 UserContext（ThreadLocal）中获取 userId
- 将 userId 放入 HTTP Header 中传递

---

### 3️⃣ 触发时机
- 每一次 Feign 发起远程调用之前

---

### 4️⃣ 风险点
- ThreadLocal 在线程池、异步调用中可能丢失

---

### 5️⃣ ⭐ 最小实现模板（我以后照抄这个）

```java
@Configuration
public class FeignConfig {

    @Bean
    public RequestInterceptor requestInterceptor() {
        return requestTemplate -> {
            Long userId = UserContext.getUser();
            if (userId != null) {
                requestTemplate.header("user-info", userId.toString());
            }
        };
    }
}
```
---

## ✅ 面试速查模板（强烈推荐）


## Feign 用户信息透传

### Q1：为什么要做用户信息透传？
A：微服务之间通过 HTTP 调用，ThreadLocal 失效，
   下游服务无法获取当前用户信息。

---

### Q2：你是怎么做的？
A：在 Feign 中定义 RequestInterceptor，
   在请求发送前从 UserContext 中获取 userId，
   放入请求头中传递。

---

### Q3：这个拦截器什么时候执行？
A：每一次 Feign 发起远程调用之前。

---

### Q4：有什么风险？
A：ThreadLocal 在异步线程或线程池中可能丢失，
   需要在下游重新设置上下文。

---

### Q5：你在哪些场景用过？
A：订单服务调用商品服务、购物车服务等。


---
## 🧠 打印小技巧（非常重要）

- **一页 ≤ 2 个代码块**
- **代码不超过 30 行**
- 标题大、留白多
- 不要怕“写得少”

👉 你打印的不是“源码”，是**“肌肉记忆触发器”**

---





