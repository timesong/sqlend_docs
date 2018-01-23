# 数据库操作

鲲鹏云联采用更加 JS 的方式来操作数据库，将 SQL 语句与 Array，Object 对象平滑的关联起来。下面通过一些例子来演示：

## 查询操作

* 选择所有的订单信息

```javascript
    var orders = db.select('orders')
```

* 选择单个订单信息

```javascript
    /*
     * 本例里面有两个参数 format 和 where
     * format 可选值为 ['object', 'table', 'scalar'] 默认为 table，返回表中所有的行信息
     * format 的值为 object, 以 Js 对象的形式返回一行信息
     * format 的值为 scalar, 返回第一行第一列的值；
     * where 是查询条件
     */
    var order = db.select('orders', {
        format: 'object'            
        where: {
            id: orderId
        }
    })
```

* 选择订单信息并包含订单明细（ Master/Slave 形式的数据）

```javascript
    /*
     * 本例接着上面的例子, 继续演示使用 columns 及 joins 参数
     * columns: 需要获取的列名
     * joins: 表连接信息，有 left_join, right_join, inner_join 三种，当 joins 是列表时，可以表达多个连接操作
     * db.raw 表示这是原生的 SQL 语句
     */ 

    order.items = db.select(db.raw('order_items as item'), {
        columns: ['item.product_id', 'prod.title', 'prod.price'],
        joins: db.left_join(db.raw('product as prod'), {
            on: {
                'item.product_id': 'prod.id'
            }
        }),
        where: {
            order_id: order.id
        }
    })
```