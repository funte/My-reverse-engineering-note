# STL - 识别 std::vector 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `vector` 对象大小为 `18h` 字节.   
  ```c++
  // 类 std::_Vector_val 中定义了 std::vector 所有数据成员.
  pointer _Myfirst;	// pointer to beginning of array
  pointer _Mylast;	// pointer to current end of sequence
  pointer _Myend;	// pointer to end of array
  ```
  对象实例内存布局:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | _Myfirst              | 0h        | 8h      | 缓冲区地址 |
  | _Mylast               | 8h        | 8h      | 下一个元素的写入地址 |
  | _Myend                | 10h       | 8h      | 缓冲区结尾地址 |
