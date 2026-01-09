# Feign 用户信息透传

## 问题

微服务之间通过 Feign 调用时，下游服务如何知道当前登录用户是谁？

---

## 解决方案

在 **Feign 请求发送之前**，通过 `RequestInterceptor` 手动把用户信息放入请求头。

---

## 最小实现代码

```java
@Bean
public RequestInterceptor userInfoInterceptor() {
    return template -> {
        Long userId = UserContext.getUser();
        if (userId != null) {
            template.header("user-info", userId.toString());
        }
    };
}
```
## Bean 放置位置（关键）

该 RequestInterceptor 必须定义在 Feign Client 的配置类中，
并通过 @FeignClient(configuration = xxx.class) 指定生效。

不建议放在全局 @Configuration 中，
否则会对所有 Feign 调用生效。

## 执行时机（重点）
```text
Feign 方法被调用
→ RequestInterceptor.apply()
→ HTTP 请求发送
```
## 数据流向
```text
用户请求
> 前提：UserContext 中的 userId 通常由登录拦截器或网关在请求进入时设置
→ UserContext.set(userId)
→ Feign 调用
→ Header: user-info
→ 下游服务解析
```