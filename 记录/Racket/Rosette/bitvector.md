# bitvector

bitvector是rosette提供的固定字长机器整数类型。类型支持任意长的字长，对任意的size，(bitvector size)都代表了一个判定谓词。这个谓词只能作用于实际量。

这个类型的字面量用bv创建。 (bv 3 4)是4位机器精度下的3的表示。bv?是这组类型整体的判定谓词，它可以用于符号量（当然产生符号值）。

对应的还有一组判等，判大小的谓词。比如bvult：unsigned less than. 以及很多对应的操作。详见参考手册