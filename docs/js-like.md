# Js-like 语言

Js-like 是 Javascript 的精简版，只包含了其中的基本概念。

## Js-like 与 Javascript 的差异

* Js-like 不允许自定义类，只可使用系统定义的类： Array，Object，Date，RegExp，Math，JSON 等等
* Js-like 的流程是同步执行的，Javascript 采用的是异步执行模式
* Js-like 不允许使用命名函数
*  

## Js-like 最佳实践

### 编写 RESTful API

使用 Js-like 编写 API 时，程序的入口是 main 函数，在这里要用到很多全局对象，现说明如下：
 
* console: 日志对象， 例如: console.debug(...), console.error(...), 等等
* db:      数据库对象， 例如: db.select('table1'), db.insert(...), db.update(...) 等等
* store:   存储对象， 例如: store.upload(...).
* $:       全局函数调用， 例如: $.put_order(...).
* header:  HTTP 头信息对象
* request: HTTP 请求对象，它有 5 个子对象: 

  **request.body**：请求对象的 body

  **request.params**：形如 http://..../orders/<orderId>/ 这样的请求，在 params 中就包含了 orderId 的键值对

  **request.query**：形如 http://..../orders/?orderId=<orderId> 这样的请求，在 query 中就包含了 orderId 的键值对

  **request.forms**： 通过 POST 方式提交的表单中的项目的键值对

  **request.files**： 通过 POST 方式上传的文件对象列表

下面是个处理创建订单请求的 API 代码。

```javascript
/*
 * 客户端发出了 POST 请求，请求的 body 是一个 JSON 字符串
 *
 */

function main() {
	var order = JSON.parse(request.body);
	var orderId = db.insert('orders', order);
	return {ok: true, data: orderId};
}
```
