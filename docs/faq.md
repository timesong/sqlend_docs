# 常见问题

## 项目开发
1. **如何写后台运行的程序？**

首先定义一个全局函数 my_func
```javascript
    function my_func(a, b) {
        console.debug(a, b);
    }
```
然后在 api 函数里面通过如下命令调用全局函数 my_func：
```javascript
    job.add($.my_func, {
        args: ['a', 'b'],
        kwargs: {
            timeout: 15
        }
    });
```

*job* 是全局对象，有 add_high, add, add_low 三个方法，分别表示加入从高到低优先级的任务。

*args* 是函数的参数列表，注意只能传递简单量（字符串、数字类型的），
传递复杂量会导致后台运行失败。

*kwargs* 是后台任务的设置参数，目前只有一个 timeout 的参数，表示后台运行的超时时间。

## 项目部署

## 费用及发票