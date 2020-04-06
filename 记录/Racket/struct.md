# struct

## 声明

```lisp
(struct struct-id (field-id ...))
```

struct-id 既是数据类型的名称也是构造函数。声明中只有子域的名称和数量，没有规定类型。

每一个这样的声明都生成一组标识符：

- *struct-id*? 是判定谓词
- *struct-id*-*field-id* 是访问子域的函数
- **struct**:*struct-id* 将数据类型本身作为值

## 更新

`struct-copy`从旧的实例生成一个新的实例，并更新一部分子域的值。原实例不变。

```lisp
(struct-copy struct-id struct-expr [field-id expr] ...)
```

## 子类

一种struct声明的变种可以声明子类型：

```lisp
(struct struct-id super-id (field-id ...))
```

性质：
- 子类继承超类的域。
- 超类的判定谓词和访问函数都可以用在子类的实例上。
- 子类的构造函数接受所有超类的域，以及定义的额外的域。
- 对于超类中的域，子类不会生成对应的访问函数。（用超类的函数访问）

## 封装与可见数据类型

默认，数据类型的域值不会被打印出来，即被封装。

要将类型声明为可见的， 在声明后面加上`#:transparent`。

可见的数据类型可以应用反射特性。

## 其他

一些略过的部分：

- 类型等值比较和哈希。需要在声明中定义相应的方法。
- Racket中类型可以有相同的名字，每一次struct声明被计算，都会生成新的类型。
- Racket中存在内建prefab类型，通过`#s(name field...)`指称。可以声明struct为prefab类型，此时，将允许在无法访问数据类型定义的情况下创建此类型。