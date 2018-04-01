## context
基于gorilla修改，不可用于生产环境。

context is a general purpose registry for global request variables.

## install

	go get github.com/dachengzao/WSocket

## 解读

context 把变量值关联 *http.Request 存储的工具. 看源码

```go
data = make(map[*http.Request]map[interface{}]interface{})
```

就是一个 map.如果您熟悉jQuery,那这个工具和 jQuery.fn.data 有异曲同工之妙.

代码说话, 直接看 test 吧

当然一定要记住 Clear(r). 因为每一个 *http.Request 都是新的. 害怕忘记, 不要紧context提供了 ClearHandler, 可以对http.Handler进行包装,代码很简单

```go
func ClearHandler(h http.Handler) http.Handler {
    return http.HandlerFunc(func(w http.ResponseWriter, r *http.Request) {
        defer Clear(r)
        h.ServeHTTP(w, r)
    })
}
```

http.Handler 结束后自动Clear.
反之如果 Request 结束了. 服务器端内存中还保留着 data[*http.Request], 这是不合理的.
这个实现其实非常简单, 而且是并发安全的. 很明显某些场合下, 这会让共享参数变的简单. 有意思的地方是你会如何用她, 会产生什么样的效果.