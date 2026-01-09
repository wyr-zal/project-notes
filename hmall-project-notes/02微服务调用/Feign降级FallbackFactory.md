# Feign FallbackFactory 触发机制

## 问题

Feign 调用下游服务失败时，调用方会发生什么？

---

## 解决方案

使用 FallbackFactory 为 Feign Client 提供降级实现，
在调用失败时执行兜底逻辑，而不是直接抛异常。

---

## 最小实现代码

```java
@Component
public class ItemClientFallbackFactory
        implements FallbackFactory<ItemClient> {

    @Override
    public ItemClient create(Throwable cause) {
        return new ItemClient() {
            @Override
            public void deductStock(...) {
                throw new RuntimeException(cause);
            }
        };
    }
}
```
## Bean 放置位置（关键）

FallbackFactory 必须被注册为 Spring Bean，
```java
@Configuration
public class FeignFallbackConfig {

    @Bean
    public ItemClientFallbackFactory itemClientFallbackFactory() {
        return new ItemClientFallbackFactory();
    }
}
```
并在 @FeignClient 中通过 fallbackFactory 指定。
```java
@FeignClient(
    name = "item-service",
    fallbackFactory = ItemClientFallbackFactory.class
)
public interface ItemClient {

    @PostMapping("/item/stock/deduct")
    void deductStock(@RequestBody List<OrderDetailDTO> items);
}
```
## 🧠 把两步连起来（一次性记住）
Spring 启动
  ↓
注册 ItemClientFallbackFactory Bean
  ↓
创建 FeignClient(ItemClient)
  ↓
绑定 fallbackFactory
  ↓
Feign 调用失败
  ↓
fallbackFactory.create(cause)
