# 你不知道的Javascript

## 1.作用域

#### LHS查询：
找目标；
查找的目的是对变量进行赋值就是LHS

赋值操作会导致LHS查询


不谈严格模式，找不到目标变量，就会报隐式创建一个全局变量；
严格模式会报ReferenceError

#### RHS查询：
找源；
查找的目的是获取变量的值

找不到变量的定义，就会报ReferenceError

#### ReferenceError和TypeError

* ReferenceError同作用域判别失败相关
* TypeError是作用域判别成功了，但是对结果的操作是非法或者不合理的；


