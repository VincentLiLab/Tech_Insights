- [Item 1 理解模板的类型推导](#item-1-理解模板的类型推导)
  - [_by-reference_](#by-reference)
  - [_by-value_](#by-value)
  - [_by-pointer_](#by-pointer)
- [Item 2 理解 _auto_ 的类型推导](#item-2-理解-auto-的类型推导)
  - [_auto_ 的类型推导和模板的类型推导的关系](#auto-的类型推导和模板的类型推导的关系)
  - [_auto_ 的类型推导和模板的类型推导的异同](#auto-的类型推导和模板的类型推导的异同)
- [Item 3 理解 _decltype_](#item-3-理解-decltype)
  - [用于声明函数返回值类型](#用于声明函数返回值类型)
  - [用于声明变量类型](#用于声明变量类型)
  - [用于非名字的左值表达式](#用于非名字的左值表达式)
- [Item 4 了解如何查看所推导的类型](#item-4-了解如何查看所推导的类型)
- [Item 5 首选 _auto_ 而不是显式类型声明](#item-5-首选-auto-而不是显式类型声明)
  - [_auto_ 可以避免未初始化的变量和冗长的变量声明，可以使重构的过程简化](#auto-可以避免未初始化的变量和冗长的变量声明可以使重构的过程简化)
  - [_auto_ 可以避免类型不匹配所导致的效率问题](#auto-可以避免类型不匹配所导致的效率问题)
  - [_auto_ 可以避免类型不匹配所导致的可移植问题](#auto-可以避免类型不匹配所导致的可移植问题)
- [Item 6 当 _auto_ 推导出的类型是 _undesired_ 时，使用 _the explicitly typed initializer idiom_](#item-6-当-auto-推导出的类型是-undesired-时使用-the-explicitly-typed-initializer-idiom)
  - [避免 _auto someVar = expression of **invisible** proxy class type_](#避免-auto-somevar--expression-of-invisible-proxy-class-type)
  - [必须 _auto someVar = static\_cast\<T\>(expression of **invisible** proxy class type)_](#必须-auto-somevar--static_casttexpression-of-invisible-proxy-class-type)
  - [_the explicitly typed initializer idiom_ 可以强制 _auto_ 去推导你想要的 **_正确_** 的类型](#the-explicitly-typed-initializer-idiom-可以强制-auto-去推导你想要的-正确-的类型)
- [Item 7 创建对象时区分 _()_ 和 _{}_](#item-7-创建对象时区分--和-)
  - [_braced initialization_ 的用法](#braced-initialization-的用法)
  - [_braced initialization_ 的特性](#braced-initialization-的特性)
  - [_braced initialization_ 的易错](#braced-initialization-的易错)
- [Item 8 首选 _nullptr_ 而不是 _0_ 和 _NULL_](#item-8-首选-nullptr-而不是-0-和-null)
  - [_nullptr_ 在任意情况下都可以做为空指针](#nullptr-在任意情况下都可以做为空指针)
- [Item 9 首选 _alias declarations_ 而不是 _typedefs_](#item-9-首选-alias-declarations-而不是-typedefs)
  - [_alias declarations_ 是支持模板化的](#alias-declarations-是支持模板化的)
- [Item 10 首选 _scoped enums_ 而不是 _unscoped enums_](#item-10-首选-scoped-enums-而不是-unscoped-enums)
  - [_scoped enums_ 可以降低 _namespace_ 的污染](#scoped-enums-可以降低-namespace-的污染)
  - [_scoped enums_ 所对应的 _enumerators_ 是不可以被隐式转换的](#scoped-enums-所对应的-enumerators-是不可以被隐式转换的)
  - [_scoped enums_ 可以直接进行前置声明来减少编译依赖](#scoped-enums-可以直接进行前置声明来减少编译依赖)
- [Item 11 首选 _deleted functions_ 而不是 _private undefined functions_](#item-11-首选-deleted-functions-而不是-private-undefined-functions)
  - [_deleted functions_ 是在编译阶段报错的](#deleted-functions-是在编译阶段报错的)
  - [_deleted functions_ 是可以用于任何函数的](#deleted-functions-是可以用于任何函数的)
  - [_deleted functions_ 是可以用于模板特化的](#deleted-functions-是可以用于模板特化的)
- [Item 12 使用 _override_ 来声明重写函数](#item-12-使用-override-来声明重写函数)
  - [重写的条件](#重写的条件)
  - [_reference qualifiers_ 的作用](#reference-qualifiers-的作用)
  - [使用 _override_ 来声明重写函数的优势](#使用-override-来声明重写函数的优势)
- [Item 13 首选 _const\_iterators_ 而不是 _iterators_](#item-13-首选-const_iterators-而不是-iterators)
  - [_const\_iterators_ 符合只要有可能使用 _const_ 就应该去使用 _const_ 的规则](#const_iterators-符合只要有可能使用-const-就应该去使用-const-的规则)
- [Item 14 当函数不会抛出异常时声明函数为 _noexcept_](#item-14-当函数不会抛出异常时声明函数为-noexcept)
  - [_C++98_ 的 _exception specification_  的格式](#c98-的-exception-specification--的格式)
  - [_C++98_ 的 _exception specification_  的缺点](#c98-的-exception-specification--的缺点)
  - [_C++11_ 的 _exception specification_  的格式](#c11-的-exception-specification--的格式)
  - [_C++11_ 的 _exception specification_  的优点](#c11-的-exception-specification--的优点)
  - [_noexcept_ 允许编译器去生成更好的对象代码](#noexcept-允许编译器去生成更好的对象代码)
  - [_noexcept_ 可以提高性能](#noexcept-可以提高性能)
  - [_noexcept_ 是接口，只有当愿意长期来维护一个 _noexcept_ 实现时，才应该声明一个函数为 _noexcept_](#noexcept-是接口只有当愿意长期来维护一个-noexcept-实现时才应该声明一个函数为-noexcept)
  - [_exception-neutral_ 的函数不是 _noexcept_ 的](#exception-neutral-的函数不是-noexcept-的)
  - [扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的](#扭曲函数的实现以去让-noexcept-成为可能是不合理的)
  - [所有的内存释放函数和析构函数默认都是隐式 _noexcept_ 的](#所有的内存释放函数和析构函数默认都是隐式-noexcept-的)
  - [没有声明为 _noexcept_ 的函数也可以是 _noexcept_ 的](#没有声明为-noexcept-的函数也可以是-noexcept-的)
- [Item 15 只要有可能就使用 _constexpr_](#item-15-只要有可能就使用-constexpr)
  - [_constexpr_ 对象](#constexpr-对象)
  - [_constexpr_ 函数](#constexpr-函数)
  - [只要有可能就使用 _constexpr_ 的优势](#只要有可能就使用-constexpr-的优势)
  - [_constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_](#constexpr-是接口只有当愿意长期来维护一个-constexpr-实现时才应该声明一个对象或函数为-constexpr)
- [Item 16 使 _const_ 成员函数成为线程安全的](#item-16-使-const-成员函数成为线程安全的)
  - [_mutable_ 的用法](#mutable-的用法)
  - [_std::atomic_ 与 _std::mutex_](#stdatomic-与-stdmutex)

# Item 1 理解模板的类型推导

```C++
  template<typename T>
  void f(ParamType param);
  
  f(expr);                    // deduce T and ParamType from expr
```

## _by-reference_

如果 _param_ 的类型为引用类型（注意可以是左值引用类型 _&_ 或是右值引用类型 _&&_），也就是 _ParamType_ 中包含  
有 _reference qualifier_ 的话，那么在模板的类型推导期间，首先要忽略 _expr_ 的 _reference-ness_，然后再通过 _expr_ 的  
类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到引用类型的形参上时，希望的是这个对象是保持不变的，即为：希望形参的类型是  
_const&_ 或者 _const&&_ 的。这也是为什么传递一个 _const_ 类型的对象到一个持有形参的类型为 _T&_ 或者 _T&&_ 的模板  
上时是安全的：因为对象的 _constness_ 成为了 _T_ 的一部分。

* 左值引用

```C++
  template<typename T>
  void f(T &param);           // param is a reference

  int x = 27;                 // x is an int
  const int cx = x;           // cx is a const int
  const int &rx = x;          // rx is a reference to x as a const int

  f(x);                       // T is int, param's type is int&
  
  f(cx);                      // T is const int,
                              // param's type is const int&
  
  f(rx);                      // T is const int,
                              // param's type is const int&
```

```C++
  template<typename T>
  void f(const T &param);     // param is now a ref-to-const

  int x = 27;                 // as before
  const int cx = x;           // as before
  const int &rx = x;          // as before

  f(x);                       // T is int, param's type is const int&

  f(cx);                      // T is int, param's type is const int&

  f(rx);                      // T is int, param's type is const int&
```

```C++
  template<typename T>
  void f(T &&param);          // param is now a universal reference

  int x = 27;                 // as before
  const int cx = x;           // as before
  const int &rx = x;          // as before

  f(x);                       // x is lvalue, so T is int&,
                              // param's type is also int&

  f(cx);                      // cx is lvalue, so T is const int&,
                              // param's type is also const int&

  f(rx);                      // rx is lvalue, so T is const int&,
                              // param's type is also const int&
```

* 右值引用

```C++
  template<typename T>
  void f(T &&param);          // param is now a universal reference

  int x = 27;                 // as before
  const int cx = x;           // as before
  const int& rx = x;          // as before

  f(std::move(x));            // std::move(x) is rvalue, so T is int,
                              // param's type is therefore int&&

  f(std::move(cx));           // std::move(cx) is rvalue, so T is const int,
                              // param's type is therefore const int&&

  f(std::move(rx));           // std::move(rx) is rvalue, so T is const int,
                              // param's type is therefore const int&&

  f(27);                      // 27 is rvalue, so T is int,
                              // param's type is therefore int&& 
```

```C++
  template<typename T>
  void f(const T &&param);    // param is now a universal reference

  int x = 27;                 // as before
  const int cx = x;           // as before
  const int& rx = x;          // as before

  f(std::move(x));            // std::move(x) is rvalue, so T is int,
                              // param's type is therefore const int&&

  f(std::move(cx));           // std::move(cx) is rvalue, so T is int,
                              // param's type is therefore const int&&

  f(rx);                      // std::move(rx) is rvalue, so T is int,
                              // param's type is therefore const int&&

  f(27);                      // 27 is rvalue, so T is int,
                              // param's type is therefore const int&& 
```

## _by-value_

如果 _param_ 的类型为 **_值类型_** 的话，那么在模板的类型推导期间，首先要忽略 _expr_ 的 _reference-ness_、_const_ 或者  
_volatile_，然后再通过 _expr_ 的类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到 **_值类型_** 的形参上时，并不希望这个对象是保持不变的，即为：并不希望形参的类  
型是 _const_ 的。因为 _param_ 是一个完全和 _expr_ 无关的对象，它是 _expr_ 的副本。_expr_ 不能被更改的事实并不能说  
明 _param_ 是否可以被更改。这也就是为什么当推导 _param_ 的类型时，_expr_ 的 _constness_ 和 _volatileness_ 会被忽略  
的原因，因为 _expr_ 不能被更改并不意味着它的副本也不能被更改。
 
## _by-pointer_

如果 _param_ 的类型为指针类型的话，那么在模板的类型推导期间，通过 _expr_ 的类型和 _ParamType_ 进行模式匹配  
去确定 _T_。在模板的类型推导期间，指针和引用在本质上是相同的。

当传递一个 _const_ 类型的对象到指针类型的形参上时，希望的是这个对象是保持不变的，即为：希望形参的类型是  
_const T*_ 的。这也是为什么传递一个 _const_ 类型的对象到一个持有形参的类型为 _T*_ 的模板上时是安全的：因为对  
象的 _constness_ 成为了 _T_ 的一部分。

```C++
  template<typename T>
  void f(T *param);           // param is a pointer

  int x = 27;                 // as before
  const int cx = x;           // as before

  f(&x);                      // T is int, param's type is int*
  
  f(&cx);                     // T is const int, param's type is const int*  
```

```C++
  template<typename T>
  void f(const T *param);     // param is now a ref-to-const

  int x = 27;                 // as before
  const int cx = x;           // as before

  f(&x);                      // T is int, param's type is const int*

  f(&cx);                     // T is int, param's type is const int*

```

注意这种场景：_expr_ 是指向 _const_ 对象的 _const_ 指针，并且 _expr_ 会被传递给 _by-pointer_ 或  _by-value_ 的 _param_。当  
_expr_ 被传递给 _f_ 时，无论是在 _by-pointer_ 还是 _by-value_ 的情况下，这个指针_expr_ 都会按位复制给 _param_ 。所以  
此时 _expr_ 本身是 _pass-by-value_ 的，那么与 _by-value_ 的形参所对应的类型推导规则一样，_expr_ 的 _constness_ 会被忽  
略掉，所推导出的 _param_ 的类型就是就是 _const T*_ 了。_expr_ 所指向的对象的 _constness_ 在类型推导期间是被保留  
的，而 _expr_ 的 _constness_ 在拷贝 _expr_ 去创建一个新的指针 _param_ 时是被忽略掉的。

```C++
  template<typename T>
  void f(T *param);                     // param is a pointer

  int x = 27;                           // as before
  const int cx = x;                     // as before
  const int * const cptr = &cx;         // rx is a const pointer to cx as a const int

  f(&x);                                // T is int, param's type is int*
  
  f(&cx);                               // T is const int, param's type is const int*  

  f(cptr);                              // T is const int, param's type is const int*
```

```C++
  template<typename T>
  void f(T param);                     // param is a value

  int x = 27;                           // as before
  const int cx = x;                     // as before
  const int * const cptr = &cx;         // rx is a const pointer to cx as a const int

  f(x);                                 // T is int, param's type is int
  
  f(cx);                                // T is int, param's type is int  

  f(cptr);                              // T is const int*, param's type is const int*
```

在模板的类型推导期间，数组名或函数名的实参是会退化为指针的，除非它们是被用于初始化引用的。

```C++
  template<typename T>
  void f(T *param);                     // param is a pointer

  int a[10] = {0};                      // a is an int[]
  const int ca[10] = {0};               // ca is an const int[]
  
  f(a);                                 // T is int, param's type is int*

  f(ca);                                // T is const int, param's type is const int*

```

```C++
  template<typename T>
  void f(T param);                     // param is a pointer

  int a[10] = {0};                      // a is an int[]
  const int ca[10] = {0};               // ca is an const int[]
  
  f(a);                                 // T is int*, param's type is int*

  f(ca);                                // T is const int*, param's type is const int*

```

```C++
  template<typename T>
  void f(T &param);                     // param is a pointer

  int a[10] = {0};                      // a is an int[10]
  const int ca[10] = {0};               // ca is an const int[10]
  
  f(a);                                 // T is int[10], param's type is int[10]

  f(ca);                                // T is const int[10], param's type is const int[10]

```

# Item 2 理解 _auto_ 的类型推导

## _auto_ 的类型推导和模板的类型推导的关系

_auto_ 扮演的是模板的类型推导中的 _T_ 的角色，而变量的 _type specifier_ 充当的是 _ParamType_ 的作用，变量声明中的  
= 右侧的表达式可以当作是模板的类型推导中的实参的角色。  

```C++
  template<typename T>
  void f(T& param);

  
  int x = 27;
  const int cx = x;

  auto &rx = cx;              // auto == T, type specifier == T&, rx == param, cx == argument                        
                              // 'auto &rx = cx' is mapped with 'f(cx)'. T is const int 
                              // and ParamType is 'const int&', so auto is const int
                              // and rx's type is const int&
```

## _auto_ 的类型推导和模板的类型推导的异同

_auto_ 的类型推导和模板的类型推导一般情况下都是相同的，只是 _auto_ 的类型推导会假设 _braced initializer_ 表示的  
是 _std::initialier_list_，而模板的类型推导则不会。

```C++
  auto x = { 11, 23, 9 };     // x's type is
                              // std::initializer_list<int>

  template<typename T>        // template with parameter
  void f(T param);            // declaration equivalent to
                              // x's declaration

  f({ 11, 23, 9 });           // error! can't deduce type for T
```  

在函数的返回值或者 _lambda_ 的形参中，_auto_ 意味着模板的类型推导，而不是 _auto_ 的类型推导，此时并不会假设 _braced initializer_ 表示的是 _std::initialier_list_。

```C++
  auto createInitList()
  {
    return { 1, 2, 3 };       // error: can't deduce type
  }                           // for { 1, 2, 3 }
```

```C++
  std::vector<int> v;
  …

  auto resetV =
    [&v](const auto& newValue) { v = newValue; };           // C++14
  
  …
  
  resetV({ 1, 2, 3 });                                      // error! can't deduce type
                                                            // for { 1, 2, 3 }
``` 

注意只是不会 **_假设_**，并不是说不能使用 _braced initializer_。

```C++
  std::initializer_list<int> createInitList()
  {
      return { 1, 2, 3 };     // suceess: can deduce type for std::initializer_list<int>
                              // but it has a warning. we ignore the warning now.
  }
```

```C++
  std::vector<int> v;


  auto resetV =
      [&v](const auto& newValue) { v = newValue; };          // C++14
  

  auto x = {1, 2, 3};
  
  resetV(x);                                                // suceess: can deduce type for std::initializer_list<int>
                                                            // but it has a warning. we ignore the warning now.
```

# Item 3 理解 _decltype_

_decltype(auto)_ 可以用于声明函数返回值类型和用于声明变量类型，所遵循的规则是 _auto_ 指明了类型会被推导，而  
_decltype_ 则说明了 _decltype_ 的规则应该在推导期间被使用。

## 用于声明函数返回值类型

```C++
  template<typename Container, typename Index>    // C++14;
  auto authAndAccess(Container &c, Index i)       // not quite
  {                                               // correct
    authenticateUser();
    return c[i];                                  // return type deduced from c[i]  
  }

  std::deque<int> d;
  …
  authAndAccess(d, 5) = 10;                       // authenticate user, return d[5],
                                                  // then assign 10 to it;
                                                  // this won't compile!
``` 

```C++
  template<typename Container, typename Index>    // C++14; works,
  decltype(auto)                                  // but still
  authAndAccess(Container &c, Index i)            // requires
  {                                               // refinement
    authenticateUser();
    return c[i];
  }

  std::deque<int> d;
  …
  authAndAccess(d, 5) = 10;                       // authenticate user, return d[5],
                                                  // then assign 10 to it;
                                                  // this will compile!
```  

## 用于声明变量类型

```C++
  Widget w;

  const Widget& cw = w;
  
  auto myWidget1 = cw;                  // auto type deduction:
                                        // myWidget1's type is Widget

  decltype(auto) myWidget2 = cw;        // decltype type deduction:
                                        // myWidget2's type is
                                        // const Widget&
```

## 用于非名字的左值表达式

对于非名字的类型 T 的左值表达式，decltype 总是报告类型为 T&。
> **_注意这非常易错，简直就是傻逼设计！_**

```C++
  int x = 0;                  // decltype(x) == int, but decltype((x)) == int&
                              // this is a shit!
```  

```C++
  decltype(auto) f1()
  {
    int x = 0;
    …
    return x;                 // decltype(x) is int, so f1 returns int
  }

  decltype(auto) f2()
  {
    int x = 0;
    …
    return (x);               // decltype((x)) is int&, so f2 returns int&
  }
``` 

# Item 4 了解如何查看所推导的类型

因为一些工具的结果可能是无用的和不准确的，所以理解 C++ 的类型推导规则仍然是必要的。

# Item 5 首选 _auto_ 而不是显式类型声明

## _auto_ 可以避免未初始化的变量和冗长的变量声明，可以使重构的过程简化

_auto_ 变量必须要进行初始化，所以可以避免未初始化的变量和冗长的变量声明。

```C++
  int x1;                     // potentially uninitialized
  
  auto x2;                    // error! initializer required
  
  auto x3 = 0;                // fine, x's value is well-defined
```

## _auto_ 可以避免类型不匹配所导致的效率问题

_auto_ 和 _std::function_ 都可以被用来持有 _closures_，但是 _auto_ 声明的持有 _closure_ 的变量的类型和 _closure_ 的类型是  
相同的，所使用的内存大小和 _closure_ 所需要的内存大小也是相同的。而 _std::function_ 声明的持有 _closure_ 的变量的  
类型是 _std::function_ 模板的实例，对于任意的所给定的 _signature_ 来说，它的大小都是固定的。这个大小对于存储  
_closure_ 来说，可能是不够的。在这种场景下，_std::function_ 的构造函数就会去分配堆栈内存来存储 _closure_。导致  
的结果就是 _std::function_ 对象通常会比 _auto_ 所声明的对象使用更多的内存。由于限制内联和产生间接调用的实现  
细节，通过 _std::function_ 对象来调用 _closuer_ 几乎肯定要比通过 _auto_ 所声明的对象来调用 _closure_ 要慢。而且可能  
还会产生内存溢出的异常。 

## _auto_ 可以避免类型不匹配所导致的可移植问题

```C++
  std::vector<int> v;
  …
  unsigned sz = v.size();
```  
_v.size()_ 的官方的返回类型是 _std::vector<int>::size_type_，但是很少有开发者会注意到它。_std::vector<int>::size_type_ 被指定为是  
_unsigned integral_ 类型，所以很多开发者认为使用 _unsigned_ 也足够了，所以就写成了上面那样。这可以有一些有  
趣的后果。例如，在 _32-bit_ _Windows_ 上，_unsigned_ 和 _std::vector<int>::size_type_ 是相同的大小，但是在 _64-bit_ _Windows_  
上，_unsigned_ 是 _32bits_ 而 _std::vector<int>::size_type_ 则是  _64bits_。这意味着在 _32-bit_ _Windows_ 上可以正确工作的代码可  
能在 _64-bit_ _Windows_ 下就表现的是不正确的，然后当你将程序从 _32bits_ 移植到 _64bits_ 时，谁会想花时间在这种问  
题上呢？

```C++
  std::unordered_map<std::string, int> m;
  …
  
  for (const std::pair<std::string, int>& p : m)
  {
    …                         // do something with p
  }
```  

要意识到缺失了什么，需要记住 _std::unordered_map_ 的 _key_ 是 _const_ 的，所以在哈希表也就是 _std::unordered_map_  
中的 _std::pair_ 的类型不是 _std::pair<std::string, int>_ 而是 _std::pair<const std::string, int>_。但是这却不是上面循环中  
的变量 _p_ 的类型。结果就是编译器将会尽力寻找方法来将 _std::pair<const std::string, int>_ 对象也就是哈希表中的对  
象转换为 _std::pair<std::string, int>_ 也就是 _p_ 的类型的对象。这可以通过以下方法来完成：通过拷贝
_m_ 中的每一个  
对象来创建 _p_ 想要绑定的类型的临时对象，然后再将引用 _p_ 绑定到这个临时对象上。在每次循环结束时，所对应  
的临时变量都将会被销毁。如果你写了这个循环的话，你可能会被这个行为所惊讶到，因为几乎可以肯定的是你想  
要只是将引用 _p_ 绑定到 _m_ 中的每个元素上而已。

# Item 6 当 _auto_ 推导出的类型是 _undesired_ 时，使用 _the explicitly typed initializer idiom_

## 避免 _auto someVar = expression of **invisible** proxy class type_

```C++
  std::vector<bool> features(const Widget& w);
``` 

```C++
  auto highPriority = features(w)[5];   // deduce highPriority's
                                        // type
```  

_std::vector::operator[]_ 一般都是返回 _T&_ 的，但是因为 _C++_ 禁止引用 _bits_，不能够返回 _bool&_，所以 _std::vector<bool>_ 的  
_operator[]_ 返回的是一个表现的像是 _bool&_ 的对象：_std::vector<bool>::reference_。为了可以这样，_std::vector<bool>::reference_ 必 
须在基本上所有可以使用 _bool&_ 的上下文中都可以使用。在 _std::vector<bool>::reference_ 中的可以完成这个工作的一个特  
性是可以隐式转换为 _bool_，是 _bool_ 而不是 _bool&_。所以此时 _highPriority_ 的类型是 _std::vector<bool>::reference_。

_std::vector<bool>::reference_ 的一种实现是去包含一个指针，这个指针指向一个机器字，而这个机器字中保存着那个 _bit_ 和  
那个 _bit_ 所对应的偏移量。因为此时涉及到是 _auto_ 的 _by-value_ 的类型推导规则，所以此时 _highPriority_ 也就有了  
这个指针的副本了，在 _auto highPriority = features(w)[5];_ 这个语句结束后，因为临时对象会被销毁掉。所以临时  
对象所对应的指针也会被销毁掉，那么 _highPriority_ 就包含有一个 _dangling_ 指针了。

```C++
  bool highPriority = features(w)[5];   // declare highPriority's
                                        // type explicitly
```  
在 _bool highPriority = features(w)[5];_ 时，会将 _std::vector<bool>::reference_ 对象隐式转换为 _bool_ 来初始化 _highPriority_。此时就没有 _dangling_ 指针的问题了。

_std::vector<bool>::reference_ 就是 **_invisble_** _proxy classes_，不能 **_直接_** 和 _auto_ 一起使用，因为 **_invisble_** _proxy classes_ 类型的  
对象。因为这种类型的对象通常不会被设计为比单语句存在的还久，所以创建这种类型的变量就是在违反基础库的  
设计假设的。

## 必须 _auto someVar = static_cast&lt;T&gt;(expression of **invisible** proxy class type)_

```C++
  auto highPriority = static_cast<bool>(features(w)[5]);
```  

## _the explicitly typed initializer idiom_ 可以强制 _auto_ 去推导你想要的 **_正确_** 的类型

```C++
  double calcEpsilon();       // return tolerance value

  float ep = calcEpsilon();   // impliclitly convert
                              // double → float
```  
这很难表示出“我是故意缩小了这个函数所返回的值的精度。”但是使用 _the explicitly typed initializer idiom_ 的声明  
却可以做到：  
```C++
  auto ep = static_cast<float>(calcEpsilon());
```

# Item 7 创建对象时区分 _()_ 和 _{}_

## _braced initialization_ 的用法

显式声明

```C++
  int x{ 0 };                 // ok

  int x = { 0 };              // ok
```

_auto_ 声明

```C++
  auto z{ 0 };                // ok, z is int                 

  auto z = { 0 };             // ok, but z is std::initializer_lists

  auto z{0, 1};               // error, shit! C++ fuck you
```
> 原文认为：“我通常会忽略 _equals-sign-plus-braces_ 语法，因为 _C++_ 通常把它和 _braces-only_ 做同样地处理。” 实  
> 际测试不是这样，使用的编译器是 _c++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0_。_auto v{0, 1};_ 这样是无法通过编  
> 译的并且 _auto v{0}_ 中的 _v_ 是 _int_ 类型而不是 _std::initializer_lists_ 类型。
> _C++ fuck you!_

## _braced initialization_ 的特性

_braced initialization_ 可以直接创建持有一组特定值的 _container_。

```C++
  std::vector<int> v{ 1, 3, 5 };        // v's initial content is 1, 3, 5
```

_braced initialization_ 可以被用于指明非静态数据成员的默认初始化值。

```C++
  class Widget {
  …
  private:
    int x{ 0 };               // fine, x's default value is 0
    int y = 0;                // also fine
    int z(0);                 // error!
  };
``` 
> _C++11_ 后才可以，注意 _()_ 不可以。

_braced initialization_ 可以禁止内建类型之间的 _implicit narrowing conversions_。

```C++
  double x, y, z;
  
  …
  
  int sum1{ x + y + z };      // error! sum of doubles may
                              // not be expressible as int
```

_braced initialization_ 可以免疫 _C++_ 的 _most vexing parse_。

```C++
  Widget w2();                // most vexing parse! declares a function
                              // named w2 that returns a Widget!

  Widget w3{};                // calls Widget ctor with no args
```

_braced initialization_ 可以让编译器强烈地优先选择持有 _std::initializer_lists_ 的重载函数。此处的 **_强烈地优先选择_** 是  
指：只要可以 **_implicit converting_** 的话，编译器强就会去选择持有 _std::initializer_lists_ 的重载函数，就算此时的这  
个 **_implicit converting_** 是 _implicit narrowing converting_ 的会导致报错，编译器也会去选择持有 _std::initializer_lists_  
的重载函数。

> 只有当没有办法将 _braced initializer_ 中的实参的类型转换 _std::initializer_list_ 中的类型时，编译器才会回退到一般  
> 的重载决议中，但是注意经过经过重载决议后，如果编译器仍然有机会去选择持有 _std::initializer_lists_ 的重载函  
> 数的话，那么编译器强仍然会去选择持有 _std::initializer_lists_ 的重载函数。

```C++
  class Widget {
  public:
    Widget(int i, bool b);                        // as before
    Widget(int i, double d);                      // as before

    Widget(const Test &);
    Widget(std::initializer_list<Widget> il);
    
    Widget(std::initializer_list<bool> il);       
                                                  
    …                                            
  };                                              

  Widget w{10, 5.0};                              // error! requires narrowing conversions

  Test t; 
  Widget w{t};                                    // t type is Test, so no implicit conversion.
                                                  // first, the 'Widget(const Test &)' is invoked, which indicates a Widget is created;
                                                  // second, the 'std::initializer_list<Widget> il' is invoked by passing the Widget created in the first step. 
```  
> 就是这么 **_强烈地优先选择_**。_C++_ 你是有病吗？学你算我倒霉。


_braced initialization_ 对于常规的拷贝构造函数和移动构造函数会有不同。
> 原文认为：“甚至那些常规的拷贝构造和移动构造也可以被 _std::initializer_list_ 构造函数所劫持。”实际测试不是  
> 这样，使用的编译器是 _c++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0_。

* 原文认为的常规的拷贝构造和移动构造情况

```C++
  class Widget {
  public:
    Widget(int i, bool b);                                  // as before
    Widget(int i, double d);                                // as before
    Widget(std::initializer_list<long double> il);          // as before
    
    operator float() const;                                 // convert
    …                                                       // to float
  };

  Widget w5(w4);                                            // uses parens, calls copy ctor
  
  Widget w6{w4};                                            // uses braces, calls
                                                            // std::initializer_list ctor
                                                            // (w4 converts to float, and float
                                                            // converts to long double)

  Widget w7(std::move(w4));                                 // uses parens, calls move ctor

  Widget w8{std::move(w4)};                                 // uses braces, calls
                                                            // std::initializer_list ctor
                                                            // (for same reason as w6)
```

* 实际测试的拷贝构造和移动构造情况
  
```C++
  class Widget {
  public:
    Widget(int i, bool b);                                  // as before
    Widget(int i, double d);                                // as before
    Widget(std::initializer_list<long double> il);          // as before
    
    operator float() const;                                 // convert
    …                                                       // to float
  };

  Widget w5(w4);                                            // uses parens, calls copy ctor
  
  Widget w6{w4};                                            // uses braces, calls copy ctor

  Widget w7(std::move(w4));                                 // uses parens, calls move ctor

  Widget w8{std::move(w4)};                                 // uses braces, calls move ctor
```

## _braced initialization_ 的易错

```C++
  std::vector<int> v;
  …
  doSomeWork<std::vector<int>>(10, 20);
```

如果创建 _localObject_ 时 _doSomeWork_ 使用了 _()_ 的话，那么 _std::vector_ 就是有 _10_ 个元素。如果创建 _localObject_ 时   
_doSomeWork_ 使用了 _{}_ 的话，那么 _std::vector_ 就是有 _2_ 个元素。哪一个是正确的？_doSomeWork_ 的作者是不知道  
的，只有调用者知道。_()_ 和 _{}_ 只使用其中之一最好。

# Item 8 首选 _nullptr_ 而不是 _0_ 和 _NULL_

## _nullptr_ 在任意情况下都可以做为空指针

_nullptr_ 在任意情况下都可以做为空指针，而 _0_ 和 _NULL_ 只有在指针被使用的情况下才可以做为空指针，否则将 _0_ 和 _NULL_ 做为它们本身的 _integral_ 类型。

* _0_ 和 _NULL_ 可以做为空指针的情况   

```C
  void f(void*);
  
  f(0);                       // this is the context where only a pointer can be used,
  f(NULL);                    // C++ will grudgingly interpret 0 and NULL as a null pointer.
```

```C++

  int f1(std::shared_ptr<Widget> spw);            // call these only when
  double f2(std::unique_ptr<Widget> upw);         // the appropriate
  bool f3(Widget* pw);  

  std::mutex f1m, f2m, f3m;             // mutexes for f1, f2, and f3
  
  using MuxGuard =                      // C++11 typedef; see Item 9
  std::lock_guard<std::mutex>;
  …
  
  {
    MuxGuard g(f1m);                    // lock mutex for f1
    auto result = f1(0);                // pass 0 as null ptr to f1
  }                                     // unlock mutex
  
  …
  
  {
    MuxGuard g(f2m);                    // lock mutex for f2
    auto result = f2(NULL);             // pass NULL as null ptr to f2
  }                                     // unlock mutex
  
  …
  
  {
    MuxGuard g(f3m);                    // lock mutex for f3
    auto result = f3(nullptr);          // pass nullptr as null ptr to f3
  }
```  

因为此时是在只有指针被使用的环境下，所以可以将 _0_ 隐式转换为 _std::shared_ptr&lt;Widget&gt;_ 类型的形参。_NULL_ 和  
_std::unique_ptr&lt;Widget&gt;_ 类型的形参也是这样的情况。

* _0_ 和 _NULL_ 不可以做为空指针的情况

```C++
  void f(void*);
  void f(int);

  f(0);                       // this is the context where not only a pointer can be used,
  f(NULL);                    // C++ will grudgingly interpret 0 and NULL as a interal type.
```

```C++

  int f1(std::shared_ptr<Widget> spw);            // call these only when
  double f2(std::unique_ptr<Widget> upw);         // the appropriate
  bool f3(Widget* pw);  

  template<typename FuncType,
            typename MuxType,
            typename PtrType>
  decltype(auto) lockAndCall(FuncType func,       // C++14
                              MuxType& mutex,
                              PtrType ptr)
  {
    MuxGuard g(mutex);
    return func(ptr);
  }

  auto result1 = lockAndCall(f1, f1m, 0);         // error!
  
  …
  
  auto result2 = lockAndCall(f2, f2m, NULL);      // error!  
  
  …

  auto result3 = lockAndCall(f3, f3m, nullptr);   // fine
``` 

因为在 _lockAndCall_ 所对应的模板的类型推导下，会将 _0_ 所对应的 _ptr_ 的类型推导为 _integral_ 类型，而 _integral_ 类  
型是无法隐式转换为 _std::shared_ptr&lt;Widget&gt;_ 类型的，所以是错误的。_NULL_ 和 _std::unique_ptr&lt;Widget&gt;_ 类型的  
形参也是这样的情况。

# Item 9 首选 _alias declarations_ 而不是 _typedefs_

## _alias declarations_ 是支持模板化的

_alias declarations_ 是支持模板化的，而 _typedef_ 是不支持模板化的。

* _alias declarations_

```C
  template<typename T>                            // MyAllocList<T>
  using MyAllocList = std::list<T, MyAlloc<T>>;   // is synonym for
                                                  // std::list<T,
                                                  // MyAlloc<T>>

  MyAllocList<Widget> lw;                         // client code
```

* _typedef_ 

```C++
  template<typename T>                            // MyAllocList<T>::type
  struct MyAllocList {                            // is synonym for
    typedef std::list<T, MyAlloc<T>> type;        // std::list<T,
  };                                              // MyAlloc<T>>
  
  MyAllocList<Widget>::type lw;                   // client code
```  

# Item 10 首选 _scoped enums_ 而不是 _unscoped enums_

## _scoped enums_ 可以降低 _namespace_ 的污染

 _scoped enums_ 是不会将其所对应的 _enumerators_ 的名称泄露到那个包含着它的作用域中的，而 _unscoped enums_   
 是会的。

* _scoped enums_

 ```C++
  enum class Color { black, white, red };         // black, white, red
                                                  // are scoped to Color
  
  auto white = false;                             // fine, no other
                                                  // "white" in scope

  Color c = white;                                // error! no enumerator named
                                                  // "white" is in this scope

  Color c = Color::white;                         // fine

  auto c = Color::white;                          // also fine (and in accord
    
 ```

 _unscoped enums_

 ```C++
  enum Color { black, white, red };     // black, white, red are
                                        // in same scope as Color

  auto white = false;                   // error! white already
                                        // declared in this scope
```

## _scoped enums_ 所对应的 _enumerators_ 是不可以被隐式转换的

_scoped enums_ 所对应的 _enumerators_ 是不可以被隐式转换的，而 _unscoped enums_ 所对应的 _enumerators_ 是可以  
被隐式转换的。

* _scoped enums_

```C++
  enum class Color { black, white, red };         // enum is now scoped

  Color c = Color::red;                           // as before, but
  …                                               // with scope qualifier

  if (c < 14.5) {                                 // error! can't compare
                                                  // Color and double
    auto factors =                                // error! can't pass Color to
      primeFactors(c);                            // function expecting std::size_t
    …
  }
```

* _unscoped enums_

```C++
  enum Color { black, white, red };     // unscoped enum

  std::vector<std::size_t>              // func. returning
    primeFactors(std::size_t x);        // prime factors of x
  
  Color c = red;
  …

  if (c < 14.5) {                       // compare Color to double (!)
    auto factors =                      // compute prime factors
      primeFactors(c);                  // of a Color (!)
    …
  }
```

## _scoped enums_ 可以直接进行前置声明来减少编译依赖

_scoped enums_ 可以直接进行前置声明来减少编译依赖，而 _unscoped enums_ 则需要在指明 _underlying type_ 的情况  
下才可以进行前置声明。
 
* _scoped enums_

```C++
  enum class Color;           // fine
```

* _unscoped enums_

```C++
  enum Color;                 // error!

  enum Color : int;           // fine
``` 

# Item 11 首选 _deleted functions_ 而不是 _private undefined functions_

## _deleted functions_ 是在编译阶段报错的

_deleted functions_ 是在编译阶段报错的，而 _private undefined functions_ 则是在链接阶段报错的。_deleted functions_ 是应该被声明为 _public_ 而不是 _private_ 的。

## _deleted functions_ 是可以用于任何函数的

_deleted functions_ 是可以用于任何函数的，包括成员函数、非成员函数和模板实例，而 _private undefined functions_  
是只可以用于成员函数的。

* 成员函数

```C++
  template <class charT, class traits = char_traits<charT> >
  class basic_ios : public ios_base {
  public:
    …
    basic_ios(const basic_ios& ) = delete;
    basic_ios& operator=(const basic_ios&) = delete;
    …
  };
```

* 非成员函数

```C++
  bool isLucky(int number);             // original function
  
  bool isLucky(char) = delete;          // reject chars
  
  bool isLucky(bool) = delete;          // reject bools
  
  bool isLucky(double) = delete;        // reject doubles and
                                        // floats
``` 

* 模板实例

```C++
  template<typename T>
  void processPointer(T* ptr);

  template<>
  void processPointer<void>(void*) = delete;
  
  template<>
  void processPointer<char>(char*) = delete; 
```  

## _deleted functions_ 是可以用于模板特化的

_deleted functions_ 是可以用于模板特化的，而 _private undefined functions_ 是不可以的。如果在类中有一个函数模  
板，并且你想通过 _private_ 声明来 _disable_ 它的一些实例的话，那么这样做是不可以的。因为不能给成员函数模板  
的特化一个和主模板是不同的访问级别。

* _deleted functions_

```C++
  class Widget {
  public:
  …
  template<typename T>
  void processPointer(T* ptr)
  { … }
  …
  };
  template<>                                                  // still
  void Widget::processPointer<void>(void*) = delete;          // public,
                                                              // but
                                                              // deleted
```

* _private undefined functions_

```C++
  class Widget {
  public:
    …
    template<typename T>
    void processPointer(T* ptr)
    { … }

  private:
    template<>                          // error!       
    void processPointer<void>(void*);
 };
``` 

# Item 12 使用 _override_ 来声明重写函数

## 重写的条件

_base class_ 的函数必须是 _virtual functions_。  
_base function_ 和 _derived function_ 的名字必须一致，除了 _destructors_ 的场景以外。  
_base function_ 和 _derived function_ 的形参类型必须一致。  
_base function_ 和 _derived function_ 的 _constness_ 必须一致。  
_base function_ 和 _derived function_ 的返回类型和异常规范必须要兼容。  
_base function_ 和 _derived function_ 的 _reference qualifiers_ 必须一致。

## _reference qualifiers_ 的作用

成员函数的 reference qualifiers 使得可以不同地处理左值 *this 对象和右值 *this 对象了。
```C+++
  class Widget {
  public:
    using DataType = std::vector<double>;
    …

    DataType& data() &                            // for lvalue Widgets,
    { return values; }                            // return lvalue

    DataType data() &&                            // for rvalue Widgets,
    { return std::move(values); }                 // return rvalue
    …
  private:
    DataType values;
  };
```  

```C++
  auto vals1 = w.data();                // calls lvalue overload for
                                        // Widget::data, copy-
                                        // constructs vals1

  auto vals2 = makeWidget().data();     // calls rvalue overload for
                                        // Widget::data, move-
                                        // constructs vals2
```  

## 使用 _override_ 来声明重写函数的优势

```C++
  class Base {
  public:
    virtual void mf1() const;
    virtual void mf2(int x);
    virtual void mf3() &;
    void mf4() const;
  };
```

在 _C++98_ 中，如果没有满足重写的条件的话，那么编译器可能是不会发出警告的，因为覆盖也是符合要求的。

```C++
  class Derived: public Base {
  public:
    virtual void mf1();
    virtual void mf2(unsigned int x);
    virtual void mf3() &&;
    void mf4() const;
  };
```

在 _C++11_ 中，如果没有满足重写的条件，但使用了 _override_ 来声明重写函数的话，那么编译器是会判定为错误  
的。
```C++
  class Derived: public Base {
  public:
  virtual void mf1() override;
  virtual void mf2(unsigned int x) override;
  virtual void mf3() && override;
  virtual void mf4() const override;
  };
``` 

# Item 13 首选 _const_iterators_ 而不是 _iterators_

## _const_iterators_ 符合只要有可能使用 _const_ 就应该去使用 _const_ 的规则

只要有可能使用 _const_ 就应该去使用 _const_，这个规则在 _C++11_ 之前就是一直有的。但是在 _C++98_ 中，当使用到   
_iterators_ 时，这个原则就不适用了。而在 _C++11_ 中，这个原则就很适用了。_container_ 的  成员函数 _cbegin_ 和 _cend_   
都产生的是 _const_iterators_。

```C++
  std::vector<int> values;                        // as before
  
  …
  
  auto it =                                       // use cbegin
  
  std::find(values.cbegin(),values.cend(), 1983); // and cend
  
  values.insert(it, 1998);
```

# Item 14 当函数不会抛出异常时声明函数为 _noexcept_

## _C++98_ 的 _exception specification_  的格式

```
  int f(int x) throw();                           // no exceptions from f: C++98 style

  int f(int x) throw(int, char, std::string);     // exceptions from f: C++98 style
```

## _C++98_ 的 _exception specification_  的缺点

在 _C++98_ 中，必须要总结出一个函数的所有可能会抛出的异常类型，所以如果这个函数的实现被更改了的话，那  
么它的 _exception specification_ 可能也需要更改。而改变一个函数的  _exception specification_  是可能会破坏客户代码  
的，这是因为调用方可能是依赖于原来的 _exception specification_ 的。

## _C++11_ 的 _exception specification_  的格式

```C++
  int f(int x) noexcept;      // no exceptions from f: C++11 style

  int f(int x);               // exceptions from f: C++11 style
```

## _C++11_ 的 _exception specification_  的优点

在 _C++11_ 开发期间，达成了一个共识：关于函数的异常抛出行为，真正有意义的信息是它是否有异常。黑或白，  
函数要么可能会抛出异常，要么保证不会抛出异常。_maybe-or-never_ 二分法是 _C++11_ 的 _exception specification_  
的基础，本质上取代了 _C++98_ 的 _exception specification_。_C++98-style_ 的 _exception specification_ 仍然是有效的，  
但是是被废弃的。在 _C++11_ 中，无条件的 _noexcept_ 是对应于那些保证不会抛出异常的函数的。

## _noexcept_ 允许编译器去生成更好的对象代码

```C++
  RetType function(params) noexcept;    // most optimizable
  
  RetType function(params) throw();     // less optimizable
  
  RetType function(params);             // less optimizable
```

## _noexcept_ 可以提高性能

```C++
  std::vector<Widget> vw;
  
  …
  
  Widget w;
  
  …                           // work with w
  
  vw.push_back(w);            // add w to vw
  
  …
``` 

当一个新元素被添加到 _std::vector_ 时，_std::vector_ 可能已经没有这个元素的空间了，即为：_std::vector_ 的大小等于它  
的容量了。当发生这种情况时，_std::vector_ 会分配一个新的大的内存块去保存它的元素，并将它的元素从旧内存块  
转移到新内存块上。在 _C++98_ 中，是通过将每一个元素从旧内存拷贝到新内存来完成的，然后再去销毁旧内存上   
的对象。这种方法给 _push_back_ 提供了强异常安全保证：如果在拷贝期间抛出了一个异常的话，那么 _std::vector_ 的  
状态是保持不变的。因为直到全部元素都被成功拷贝到新内存后，旧内存上的全部元素才会被销毁。

在 _C++11_ 中，一个自然的优化是：对于 _std::vector_ 的元素，会使用移动来代替拷贝。不幸地是，这样做是冒着违  
反 _push_back_ 的异常安全保证风险的。如果 _n_ 个元素已经从旧内存移走了，然后在移动第 _n+1_ 
个元素期间抛出了  
一个异常，那么 _push_back_ 操作是不可以完成的。但是原来的 _std::vector_ 已经被更改了：因为 _n_ 个元素已经被移动  
走了。不可能恢复原来的状态，因为试图移动每一个元素回到原来的旧内存都可能会产生异常。

这是一个严重的问题，因为 _legacy_ 代码的行为可能是依赖于 _push_back_ 的强异常安全保证的。因此，_C++11_ 的实  
现不能悄悄地使用 _move operations_ 来替换 _push_back_ 中的 _copy operations_，除非确认 _move operations_ 不会抛出  
异常。不会抛出异常时，_move operations_ 代替 _copy operations_ 是安全的，唯一的附加影响是将会提升性能。

_std::vector::push_back_ 利用了 **_move if you can, but copy if you must_** 的策略的优势，它不是唯一一个在标准库中这  
样做的函数。其他的利用了 _C++98_ 的强异常安全保证的函数也是使用了相同的方式，比如：_std::vector::reserve_ 和  
 _std::deque::insert_ 等。如果 _move operations_ 被认为是不会抛出异常异常的话，那么全部的这些函数会使用 _C++11_   
的 _move operations_ 来代替 _C++98_ 的 _copy operations_。但是这些函数如何可以知道 _move operation_ 是否不会产生  
异常呢？答案是明显的：去检查 _move operations_ 是否被声明为了 _noexcept_。

```C++
  template <class T, size_t N>
  void swap(T (&a)[N],                                      // see
            T (&b)[N]) noexcept(noexcept(swap(*a, *b)));    // below
  
  template <class T1, class T2>
  struct pair {
    …
    void swap(pair& p) noexcept(noexcept(swap(first, p.first)) &&
                                noexcept(swap(second, p.second)));
    …
  };
```  

这些函数是 **_有条件地_** _noexcept_ 的：它们是否是 _noexcept_ 的是依赖于 _noexcept clauses_ 中的表达式是否是 _noexcept_  
的。例如：给定两个 _Widget_ 类型的 _array_，只有当 _array_ 中的单独的元素的交换是 _noexcept_ 时，即为：_Widget_ 的  
_swap_ 是 _noexcept_ 的时，这两个 _array_ 的交换才会是 _noexcept_ 的。因此，_Widget_ 的 _swap_ 的作者决定了 _Widget_ 的  
_array_ 的交换是否是 _noexcept_ 的。这依次决定了其他的像 _Widget_ 的 _array_ 的 _array_ 这样的 _swaps_ 是否是 _noexcept_  
的。类似地，还有两个包含着 _Widget_ 的 _std::pair_ 对象的交换是否是 _noexcept_ 的是依赖于 _Widget_ 的  _swap_ 是否是  
_noexcept_ 的。只有当低级别成分的交换是 _noexcept_ 的时，高级别的数据结构才可以是 _noexcept_ 的，这个事实激励  
你只要可以就应该去提供 _noexcept_ 的 _swap_ 函数。

## _noexcept_ 是接口，只有当愿意长期来维护一个 _noexcept_ 实现时，才应该声明一个函数为 _noexcept_

只有当愿意长期来维护一个 _noexcept_ 实现时，才应该声明一个函数为 _noexcept_。因为如果你开始时声明了一个函  
数为 _noexcept_，但是后续却后悔了的话，那么你的选择是有限的。你可以从函数的声明中删除 _noexcept_，即为：  
改变它的接口，但这是冒着破坏客户的代码的风险的。你还可以改变函数的实现，以让一个异常可以 _escape_，但  
还是保持原始的但现在是错误的 _exception specification_。如果你这样做了，当这个异常尝试离开函数时，你的程序将会被终止。

## _exception-neutral_ 的函数不是 _noexcept_ 的

事实上，大多数函数都是 _exception-neutral_ 的。这些函数它们本身是不会抛出异常的，但是它们所调用的函数是  
可能会抛出异常的。当这种情况发生时，_exception-neutral_ 的函数允许它们所调用的函数所抛出的异常通过它们的  
路到达更上层的调用链路的处理程序。_exception-neutral_ 的函数永远不是 _noexcept_ 的，因为它们还是可能会抛出  
**_just passing through_** 的异常的。因此，大多数函数很恰当地缺少了 _noexcept_ 名称。


## 扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的

扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的。如果一个简单的函数实现可能会产生异常的话，比如：调  
用可能会抛出异常的函数，那么你可以付出努力来将这些抛出的异常对调用方进行隐藏，比如：捕获所有的异常并  
使用状态码或特殊返回值来代替它，这不仅会使得你的函数的实现变得非常复杂，还通常也会使得调用点的代码变  
得复杂。例如：调用方必须要检查状态码或者特殊返回值。这些复杂度的运行时间成本，比如：额外的分支和更大  
的函数会给指令缓存施加更大的压力，可能会超出你希望通过 _noexcept_ 所实现的加速，而且代码更难以理解和维  
护了。这将是糟糕的软件工程。

## 所有的内存释放函数和析构函数默认都是隐式 _noexcept_ 的

对于一些函数来说，成为 _noexcept_ 的是非常重要的，它们默认就应该是那样。在 _C++98_ 中，允许内存释放函数，  
即为：_operator delete and operator delete[]_，和析构函数去抛出异常被认为是一个糟糕的设计，而在 _C++11_ 中，  
这种风格规则已经被升级为是一个语言规则了。所有的内存释放函数和析构函数，包括用户定义的和编译器生成  
的，默认都是隐式 _noexcept_ 的。因此不需要声明它们为 _noexcept_。进行声明也不会伤害任何东西，只是不常见而  
已。只有当类的数据成员，包括所继承的成员和被包含在其他数据成员中的成员，明确地声明了它们的析构函数可  
能会抛出异常时，比如：声明为 _noexcept(false)_，才是仅有的析构函数不是隐式 _noexcept_ 的情况。这样的析构函  
数是不常见的。在标准库中是没有这样的析构函数的，如果标准库所使用的对象的析构函数抛出了一个异常的话，  
比如：这个对象是在 _container_ 中的，是被传递到算法中的，那么程序的行为是未定义的。

## 没有声明为 _noexcept_ 的函数也可以是 _noexcept_ 的

```C++
  void setup();               // functions defined elsewhere
  void cleanup();

  void doWork() noexcept
  {
    setup();                  // set up work to be done
    
    …                         // do the actual work
    
    cleanup();                // perform cleanup actions
  }
```  
此处，_doWork_ 是被声明为 _noexcept_ 的，尽管 _doWork_ 调用了 _non-noexcept_ 函数 _setup_ 和 _cleanup_ 函数。这似乎  
是矛盾的，但却是可以的，那就是当 _setup_ 和 _cleanup_ 在文档中说明了它们永远不会抛出异常，尽管它们没有被声  
明为 _noexcept_。有好的理由将这样不会抛出异常的函数不声明为 _noexcept_，例如：它们可能是 _C_ 库的一部分，甚  
至一些已经从 _C_ 标准库移动到 _std namespace_ 的函数仍然缺少着 _exception specification_，比如：_std::strlen_ 没有被  
声明为 _noexcept_。又或者它们是决定不使用 _C++98_ 的 _exception specification_，但还没有被修改为使用 _C++11_ 的  
_exception specification_ 的 _C++98_ 的库。

# Item 15 只要有可能就使用 _constexpr_

## _constexpr_ 对象

_constexpr_ 对象的值在编译期间就是已知的，所以需要使用在编译期间就是已知的值来进行初始化，而 _const_ 对象  
不需要使用在编译期间就是已知的值来进行初始化。也就是说：所有 _constexpr_ 对象都是 _const_ 的，但所有 _const_  
对象不都是 _constexpr_ 的。

```C++
  int sz;                               // non-constexpr variable
  
  …
  
  constexpr auto arraySize1 = sz;       // error! sz's value not
                                        // known at compilation
  
  std::array<int, sz> data1;            // error! same problem
  
  constexpr auto arraySize2 = 10;       // fine, 10 is a
                                        // compile-time constant
  
  std::array<int, arraySize2> data2;    // fine, arraySize2
                                        // is constexpr
```

```C++
  int sz;                               // as before
  
  …
  
  const auto arraySize = sz;            // fine, arraySize is
                                        // const copy of sz

  std::array<int, arraySize> data;      // error! arraySize's value
                                        // not known at compilation
```  

## _constexpr_ 函数

如果你传递给 _constexpr_ 函数的实参的值是在编译期间就是已知的话，那么这个 _constexpr_ 函数的结果将会在编译  
期间被计算。如果你传递给 _constexpr_ 函数的实参的值不是在编译期间就是已知的话，那么这个 _constexpr_ 函数的  
结果将会在运行期间被计算。构造函数和其他成员函数也可以是 _constexpr_ 的。因为如果所传入的实参是在编译期  
间就是已知的话，那么所构造的对象的数据成员的值也是在编译期间就是已知的。所以可以是 _constexpr_ 的。

```C++
  class Point {
  public:
    constexpr Point(double xVal = 0, double yVal = 0) noexcept
      : x(xVal), y(yVal)
      {}

      constexpr double xValue() const noexcept { return x; }
      constexpr double yValue() const noexcept { return y; }
      
      void setX(double newX) noexcept { x = newX; }
      void setY(double newY) noexcept { y = newY; }
    
    private:
      double x, y;
  };
``` 

```C++
  constexpr Point p1(9.4, 27.7);        // fine, "runs" constexpr
                                        // ctor during compilation
  
  constexpr Point p2(28.8, 5.3);        // also fine
```  

## 只要有可能就使用 _constexpr_ 的优势

_constexpr_ 对象可以在只读内存中被创建。这意味着：你可以在模板的实参中或者在要指定 _enumerator_ 值的表达式  
中使用像 _mid.xValue() * 10_ 这样的的表达式了。这意味着：在编译期间所完成的工作和运行期间所完成的工作之间  
传统上是相当严格的界限开始变得模糊了，一些传统上是在运行期间所完成的计算现在可以迁移到编译期间来进行  
了。迁移的代码越多，你的程序将会运行的更快，当然编译也会更耗时。

## _constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_

_constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_。

# Item 16 使 _const_ 成员函数成为线程安全的

## _mutable_ 的用法

在 _const_ 成员函数中会，如果一个成员会执行 _no-const_ 操作的话，比如：被更改或者会调用到其他 _no-const_ 函数，那么需要声明这个成员为 _mutable_。

```C++
  class Polynomial {
  public:
    using RootsType = std::vector<double>;
    
    RootsType roots() const
    {
      std::lock_guard<std::mutex> g(m);           // lock mutex

      if (!rootsAreValid) {                       // if cache not valid
      
        …                                         // compute/store roots
      
        rootsAreValid = true;
      }

    return rootVals;
  }                                               // unlock mutex
  
  private:
    mutable std::mutex m;
    mutable bool rootsAreValid{ false };
    mutable RootsType rootVals{};
  };
```  
_std::mutex m_ 被声明为了 _mutable_，因为 _m_ 的加锁和解锁是 _non-const_ 成员函数，但却是在 _const_ 成员函数 _roots_   
中，如果 _m_ 没有被声明为 _mutable_ 的话，那么 _m_ 将会被认为 _const_ 对象。

## _std::atomic_ 与 _std::mutex_

对于单个要求同步的变量或内存区域来说，使用 _std::atomic_ 是适当的，但是一旦你有两个或多个要求做为整体来  
进行操作的变量或内存区域的话，那么你应该去使用 _std::mutex_。

```C++
  class Widget {
  public:
    …
    int magicValue() const
    {
      if (cacheValid) return cachedValue;
      else {
        auto val1 = expensiveComputation1();
        auto val2 = expensiveComputation2();
        cachedValue = val1 + val2;                // uh oh, part 1
        cacheValid = true;                        // uh oh, part 2
        return cachedValue;
      }
    }

  private:
    mutable std::atomic<bool> cacheValid{ false };
    mutable std::atomic<int> cachedValue;
  };
```

* 第一个线程调用了 _Widget::magicValue_，看到了 _cacheValid_ 为 _false_，执行两个成本高的计算后将它们的和分  
  配给了 _cachedValue_。
* 在那时，第二个线程调用了 _Widget::magicValue_，也看到了 _cacheValid_ 为 _false_，因此也执行了和第一个线程  
已经完成的是相同的成本大的计算。实际上，“第二个线程”可能是其他多个线程。
  
```C++
  class Widget {
  public:
    …
    
    int magicValue() const
    {
      if (cacheValid) return cachedValue;
      else {
        auto val1 = expensiveComputation1();
        auto val2 = expensiveComputation2();
        cacheValid = true; // uh oh, part 1
        return cachedValue = val1 + val2; // uh oh, part 2
      }
    }

    …

  };
```  

* 一个线程调用了 _Widget::magicValue_，并执行到了 _cacheValid_ 被设置为 _true_ 的那一点。
* 此时，第二个线程调用了 _Widget::magicValue_，然后会检查 _cacheValid_。看到的它为 _true_ 后，这个线程会返回  
_cachedValue_，尽管第一个线程还没有对它的赋值。因此，所返回的值是错误的。