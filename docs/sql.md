# 数据库操作

鲲鹏云联采用更加 JS 的方式来操作数据库，将 SQL 语句与 Array，Object 对象平滑的关联起来。

## 全局对象

数据库操作的全局对象是 db,包含以下 方法：

1. 查询方法：db.select
2. 插入方法: db.insert
3. 更新方法: db.update
4. 删除方法: db.delete

## 查询操作

```javascript
var options = {
    columns: [db.raw('*')],
    where: {
        id: objId
    },
    order_by: ['col_1', 'col_2 desc'],
    group_by: ['col_1', 'col_2'],
    limit: 20,
    offset: 0,
    format: 'array',
    ...
}

var data = db.select('table_name', options)

```

* **format** 参数有四种可选值：
1. *array* 默认值，返回对象的数组，形如：[{col: 'a', col2: 'b', ...}, ...]
2. *table* 返回数据表格，形如：[['col1', 'col2', 'col3'], ['col1', 'col2', 'col3'], ...]
3. *object* 返回单个对象，形如：{col: 'a', col2: 'b', ...}
4. *scalar* 返回标量值，即第一行第一列的值，形如：'col1'

* 选择所有的订单信息

```javascript
    var orders = db.select('orders')
```

* 选择单个订单信息

```javascript
    /*
     * 本例里面有两个参数 format 和 where
     * format 可选值为 ['array', 'object', 'table', 'scalar'] 默认为 array
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

## 插入操作

* 插入一条订单信息

```javascript
    /*
     * 插入对象的属性名对应数据表中的列名，调用 insert 方法即可完成插入
     */ 
    var order = {
        date: '2018-01-24',
        title: '购买 iPhone X',
        amount: 6800.0,
        pay_method: 'alipay'
    }

    var orderId = db.insert('orders', order)
```

## 更新操作

* 更新订单信息

```javascript
    /*
     * 插入方法的基础上可选 where 参数，指定更新的条件
     */ 
    var order = {
        date: '2018-01-24',
        title: '购买 iPhone X',
        amount: 6800.0,
        pay_method: 'alipay'
    }

    db.update('orders', order, {
        where: {
            id: oldOrderId
        }
    })
```

## 删除操作

* 删除所有数据

```javascript
    db.delete('orders')
```

* 删除符合条件的数据

```javascript
    db.delete('orders', {
        where: {
            id: delOrderId
        }
    })
```

## 补充

* where 对象生成对应的 WHERE 子句时，默认使用 AND 连接条件，需要使用 OR 条件时应采用下面的方法：

```javascript
    var orders = db.select('orders', {
        where: {
            '||': {                             // || is OR
                'date__gte': '2017-12-12',      // gte： >=
                'name__like': '%iPhone%'
            }
        }
    })
```
