# 34.join到底能不能用


## Index Nested-Loop Join

如果驱动表和被驱动表都能使用索引，那么会采用这个算法；

## Simple Nested-Loop Join

当有表不能使用索引时，MySQL会把两个表都读入内存，然后执行扫描。

## Block Nested-Loop Join

当内存不够用时，会分段把表读入内存，Simple Nested-Loop Join就会退化为这个算法。

## 能不能使用join？

如果能使用Index Nested-Loop Join算法，则可以用；否则尽量不要用

## 选择哪个表作为驱动表？

小表


