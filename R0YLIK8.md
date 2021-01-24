# STL - 识别 std::function::operator = 函数

## 目录
- [上级 &#8673;](./笔记-逆向.md)  

## 正文
使用 `IDA` 反编译这段代码:  
```c++
#include <iostream>
#include <functional>

class CheatingDetector {
public:
    CheatingDetector() {
        printf("Call constructor\n");
    }
    void foo(INT64 val) {
        printf("Call foo with %d\n", val);
    }
    void callFoo(std::function<void(INT64)> &foo) {
        foo(1);
    }
};

int main() {
    using namespace std::placeholders;
    CheatingDetector *cd = new CheatingDetector();

    std::function<void(INT64)> f(
        std::bind(&CheatingDetector::foo, cd, _1)
    );

    std::function<void(INT64)> f2;

    f2 = f;
    f2(1);

    return 0;
}

```
在开始对 `f2` 赋值之前需要在栈上构造函数对象 `f(var_90)`:  
```c++
lea     rax, _Func_impl_no_alloc::vftable
mov     [rbp+20h+var_90], rax
lea     rax, sub_140001070
mov     [rbp+20h+var_88], rax
...
mov     [rbp+20h+var_80], cl
mov     [rbp+20h+var_78], rbx
lea     rax, [rbp+20h+var_90]
mov     [rbp+20h+var_58], rax

```
对 `f2` 的赋值会调用其重载的操作符函数 `operator =`, `release` 模式下这个函数经过编译器优化后可分 3 步:  
* 调用虚函数 `_Copy` 构造一个临时对象 `var_F0`  
  ```c++
  lea     rdx, [rsp+120h+var_F0]
  lea     rcx, [rbp+20h+var_90]
  call    _Copy ; 即 _Func_impl_no_alloc::vftable[0]
  mov     [rsp+120h+var_B8], rax
  ``` 
* 使用 `swap` 完成对 `f2` 赋值  
  ```c++
  lea     rdx, [rbp+20h+var_50]
  lea     rcx, [rsp+120h+var_F0]
  call    sub_140001200
  ```
  `sub_140001200` 可能是函数 `std::function::swap`, 而且函数内部多次调用了虚函数 `_Move`.  
* 调用虚函数 `_Delete_this` 释放临时对象 `var_F0`  
  ```c++
  lea     rdx, [rsp+120h+var_F0]
  ...
  call    qword ptr [rax+20h]
  ```
