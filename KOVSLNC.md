# STL - 识别 std::unordered_map 对象

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
* 内存布局  
  在 `64bit release` 模式下, 编译后的 `unordered_map` 实例大小为 `40h` 字节:  
  ```c++
  _Traits _Traitsobj;	// traits to customize behavior
  _Mylist _List;	// list of elements, must initialize before _Vec
  _Myvec _Vec;	// vector of list iterators, begin() then end()-1
  size_type _Mask;	// the key mask
  size_type _Maxidx;	// current maximum key value
  ```
  `unordered_map` 实例对象内存布局:  
  | Name                  | Offset    | Size    | Specification |
  | --------------------- | --------- | ------- | ------------- |
  | _Traitsobj            | 0h        | 4h      | 初值 `1.f` |
  | _List                 | 8h        | 10h     | 初值长度 `0` |
  | _Vec                  | 18h       | 18h     | 初值 `_List._Unchecked_end()` |
  | _Mask                 | 30h       | 8h      | 初值 `_Buckets-1`, 默认 `7`  |
  | _Maxidx               | 38h       | 8h      | 初值 `_Buckets`, 默认 `8` |
* 甄别存储数据类型  
  在容器 `unordered_map` 中, 数据以键值对的形式 `using value_type = pair<const _Kty, _Ty>;` 存储在成员 `_List` 的元素节点中([STL - 识别 std::list 对象](./INCBWH3.md)), 所以想要从茫茫多的汇编代码中寻出存储的数据类型首先需要找到函数 `std::unordered_map::operator[]` 对应的反汇编代码, 观察这个函数:  
  ```c++
  // std::unordered_map::operator[] 的返回数据.
  return (try_emplace(_STD move(_Keyval)).first->second)
  ```
  发现这个函数会通过 `using _Pairib = pair<iterator, bool>` 类型的数据直接返回将要被写入的地址, 因此根据经验, 就可以推测出这些代码附近可能就有关于存储数据类型的信息.  
