# 幂等性在 HTTP/1.1 规范中的定义
> A request method is considered "idempotent" if the intended effect on
   the server of multiple identical requests with that method is the
   same as the effect for a single such request.  Of the request methods
   defined by this specification, PUT, DELETE, and safe request methods
   are idempotent.

中文翻译大致为一次和多次请求某一个资源应该具有同样的副作用，也就是一次访问与多次访问，对这个资源带来的变化是相同的。请求方法 PUT、DELETE 和安全请求方法是具有幂等性的。

什么是安全方法？本质上是只读的。以 GET 和 POST 为例，GET (不含统计)是只读的，而 POST则不是。 

# 为什么需要幂等性？
举个例子，在 ATM 取钱时，假如未保证幂等性，你取了1000，卡里的余额由于网络抖动、服务器等原因却没有扣除1000，这个问题很严重。涉及金钱交易的数据必须保证一致性。
# 哪些常见请求方法具有幂等性？
GET、PUT、DELETE、HEAD、OPTIONS
## PUT 和 PATCH 区别
PUT 请求跟新资源时需要带上完整的资源参数，相当于替换资源。而 PATCH 仅需要发送要更新的参数。PUT 是幂等的，而 PATCH 不是。






# Reference
- [MDN-幂等](https://developer.mozilla.org/zh-CN/docs/Glossary/%E5%B9%82%E7%AD%89)
- [如何理解 HTTP 方法的幂等性](https://stackoverflow.com/questions/45016234/what-is-idempotency-in-http-methods)
- [RFC 7231](https://tools.ietf.org/html/rfc7231#section-4.2.2)
- [Difference Between PUT and PATCH Requests](https://devqa.io/difference-put-patch-requests/)