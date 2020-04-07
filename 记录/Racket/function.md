# function

## apply

因为一般的函数虽然有重载，但是一次只能接受固定长度的参数。如果想要使用列表中的参数调用函数，可以用apply。

更复杂的情况包括关键字参数等。略

## lambda

除了一般的lambda定义，还有如下形式：

```
(lambda rest-id
  body ...+)
```

此时，函数接受任意长度的参数，并且将rest-id绑定为这些参数的**列表**。 当然，也可以不传参数（null）。

也可以把一般形式和这种形式结合：

```
(lambda (arg-id ...+ . rest-id)
  body ...+)
```

注意，中间有点

此时，前面的参数是必须的。

### 可选参数

在lambda的绑定变量处，使用`[arg-id default-expr]`可以指定可选参数以及其默认值。默认值中可以用所有之前出现的绑定变量。

可选参数必须是绑定变量列表的后缀。

### 按名传参

lambda绑定变量列表中，可以在想要允许按名传参的参数前加上keyword。

## case-lambda

case-lambda对传入参数的基数敏感。它有一系列绑定和函数体。

```racket
(define greet
  (case-lambda
    [(name) (string-append "Hello, " name)]
    [(given surname) (string-append "Hello, " given " " surname)]))
```

## define

define支持rest参数

```
(define (head args) body ...+)
 
head	 	=	 	id
 	 	|	 	(head args)
args	 	=	 	arg ...
 	 	|	 	arg ... . rest-id
```

define允许定义柯里化的函数：

```racket
(define ((make-add-suffix s2) s)
  (string-append s s2))
 ```