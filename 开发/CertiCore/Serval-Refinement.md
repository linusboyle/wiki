# Serval-Refinement

状态机精化需要证明一下三个部件：

- functional specification：规范所描述的抽象状态
- abstract function：实现的底层状态到抽象状态的映射
- representation invariant：功能单元前后的不变量

满足：

对任意的满足不变量的底层状态s，以及其对应的抽象状态a=abs(s)，如果执行底层功能单元f后得到的底层状态t同样满足不变量，且按照规范执行后得到的状态是b=F(a)，两者是对应的即b=abs(t)

证明了状态机精化之后，就可以关注功能本身的性质。