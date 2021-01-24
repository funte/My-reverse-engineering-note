# STL - 识别 std::string 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `string` 大小为 `20h` 字节:  
  ```c++
  union _Bxty
    {	// storage for small buffer or pointer to larger one
    _Bxty()
      {	// user-provided, for fancy pointers
      }

    ~_Bxty() noexcept
      {	// user-provided, for fancy pointers
      }

    value_type _Buf[_BUF_SIZE];
    pointer _Ptr;
    char _Alias[_BUF_SIZE];	// retained for /clr bincompat; unused
    } _Bx;

	size_type _Mysize;	// current length of string
	size_type _Myres;	// current storage reserved for string
  ```
  `string` 实例对象内存布局:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | _Bx                   | 0h        | 10h     | 字符串缓冲区 |
  | _Mysize               | 10h       | 8h      | 当前字符串长度 |
  | _Myres                | 18h       | 8h      | 当前缓冲区容量 |
