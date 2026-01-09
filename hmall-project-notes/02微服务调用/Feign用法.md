# Feign Client 基本用法（声明式 HTTP 调用）

## 作用

Feign 用于在微服务之间进行远程 HTTP 调用。  
通过 **接口 + 注解** 的方式，把一次 HTTP 请求声明成一次方法调用。

---

## 核心注解

```java
@FeignClient(value = "item-service")

作用：
```
声明这是一个 Feign 客户端
value 指定目标服务名（注册中心中的服务名）
Feign 会通过服务发现，自动找到对应服务实例

## 使用前提

启动类或配置类中启用 Feign
```java
@EnableFeignClients
```
Feign Client 接口所在包必须被扫描到
