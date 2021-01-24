# STL - 识别 std::function 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* C++17 带来的内部函数对象(`_Func_impl_no_alloc`)类型变化  
  [C++17 removed allocator support in std::function](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2016/p0302r1.html)  
  如果采用 `C17` 之后的版本的标准库, 应该不难发现 `_Func_class` 中指向内部函数对象的指针都是 `_Func_impl_no_alloc` 类型的.  
  如果需要构造的内部函数对象 `_Func_impl_no_alloc` 足够小(小于 `(_Small_object_num_ptrs - 1) * sizeof(void*)`),  就会直接使用 `function` 对象的内存空间??  
  TODO: 这段代码没看明白
  ```c++
  virtual _Mybase* _Copy(void* _Where) const override {
      if _CONSTEXPR_IF (_Is_large<_Func_impl_no_alloc>) {
          (void) _Where; // TRANSITION, DevCom-1004719
          return _Global_new<_Func_impl_no_alloc>(_Callee);
      } else {
          return ::new (_Where) _Func_impl_no_alloc(_Callee);
      }
  }
  ```
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `function` 对象大小为 `40h` 字节(`_Small_object_num_ptrs` 为 `8`):  
  ```c++
  union _Storage { // storage for small objects (basic_string is small)
      max_align_t _Dummy1; // for maximum alignment
      char _Dummy2[_Space_size]; // to permit aliasing
      _Ptrt* _Ptrs[_Small_object_num_ptrs]; // _Ptrs[_Small_object_num_ptrs - 1] is reserved
  };

  _Storage _Mystorage;
  ```
  但真正的函数地址存储在 `_Ptrs[_Small_object_num_ptrs-1]`(最后一个) 中:  
  ```c++
  void _Set(_Ptrt* _Ptr) noexcept { // store pointer to object
      _Mystorage._Ptrs[_Small_object_num_ptrs - 1] = _Ptr;
  }
  ``` 
  对象实例内存布局:  
  | Name                            | Offset    | Size    | Specification |
  | ------------------------------- | --------- | ------- | ------------- |
  | _Func_impl_no_allocs::vftable   | 0h        | 8h      | 参考: `std::_Func_base`, 其中 `_Copy` 和 `_Move` 拥有相同定义, 这导致编译器优化后它们通常指向相同的函数 |
  | 函数地址                        | 8h        | 8h      | 真正的函数地址 |
  | ...                             | 10h       | 28h       | 一些参数 |
  | _Ptrs[_Small_object_num_ptrs - 1] | 38h       | 8h      | 内部函数对象地址, 通常为 `function` 的起始地址 |
