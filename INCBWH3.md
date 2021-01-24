# STL - 识别 std::list 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* 初始化  
  一个经过初始化长度为 `0` 但没有存储任何元素的 `list` 实例, 其头元素(`_List_node` 类型) 的两个指针都指向自己, 其后紧跟的 `_Mysize` 表示元素数量.  
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `list` 对象大小为 `10h` 字节:  
  ```c++
  // 类 std::_List_val 定义了 std::list 所有数据成员.
  _Nodeptr _Myhead;	// pointer to head node
  size_type _Mysize;	// number of elements
  ```
  `list` 实例内存布局:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | _Myhead               | 0h        | 8h      | 首元素指针 |
  | _Mysize               | 8h        | 8h      | 元素数量 |
  其中元素数据保存在 `_List_node` 中, 编译后大小为 `10h+?` 字节:  
  ```c++
  // 元素 `_List_node` 的数据成员.
  _Nodeptr _Next;	// successor node, or first element if head
  _Nodeptr _Prev;	// predecessor node, or last element if head
  _Value_type _Myval;	// the stored value, unused if head
  ```
  元素 `_List_node` 实例内存布局:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | _Next                 | 0h        | 8h      | 下一个或第一个元素 |
  | _Prev                 | 8h        | 8h      | 前一个或最后一个元素 |
  | _Myval                | 10h       | ?       | 元素存储的数据, 大小依赖于存储数据的类型 |
