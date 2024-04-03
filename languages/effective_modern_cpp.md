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
  - [_0_ 和 _NULL_ 都不是指针类型，而是 _integral_ 类型](#0-和-null-都不是指针类型而是-integral-类型)
  - [首选 _nullptr_ 而不是 _0_ 和 _NULL_](#首选-nullptr-而不是-0-和-null)
- [Item 9 首选 _alias declarations_ 而不是 _typedefs_](#item-9-首选-alias-declarations-而不是-typedefs)
  - [_alias declarations_ 支持模板化，而 _typedef_ 不支持模板化](#alias-declarations-支持模板化而-typedef-不支持模板化)
- [Item 10 首选 _scoped enums_ 而不是 _unscoped enums_](#item-10-首选-scoped-enums-而不是-unscoped-enums)
  - [_scoped enums_ 可以降低 _namespace_ 的污染](#scoped-enums-可以降低-namespace-的污染)
  - [_scoped enums_ 所对应的 _enumerators_ 是不可以被隐式转换的](#scoped-enums-所对应的-enumerators-是不可以被隐式转换的)
  - [_scoped enums_ 可以直接进行前置声明来减少编译依赖](#scoped-enums-可以直接进行前置声明来减少编译依赖)

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

* _braced initialization_ 是最广泛的可使用的初始化语法，它可以禁止 _narrowing conversions_并且对 _C++_ 的  
_most vexing parse_ 所免疫。

* 在构造函数重载决议期间，如果可能，_braced initializer_ 会和 _std::initializer_list_ 形参匹配，即使其他的构造函  
数提供了看起来是更好的匹配。

* 在选择使用 _()_ 和 _{}_ 时可能产生显著差异的一个例子是使用两个实参来创建 _std::vector&lt;numeric type&gt;_时。


* 当在模板中创建对象时，在 _()_ 和 _{}_ 之间进行选择是具有挑战性的。

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

## _0_ 和 _NULL_ 都不是指针类型，而是 _integral_ 类型

_C++_ 是在只有指针被使用的环境下，才是会将 _0_ 和 _NULL_ 做为空指针的，否则是不会将 _NULL_ 做为空指针 的，而  
是将 _0_ 和 _NULL_ 做为它们本身的 _integral_ 类型。

```C
 
  void f(void*);
  
  f(0);                       // this is the context where only a pointer can be used,
  f(NULL);                    // C++ will grudgingly interpret 0 and NULL as a null pointer.
```

```C++
  void f(void*);
  void f(int);

  f(0);                       // this is the context where not only a pointer can be used,
  f(NULL);                    // C++ will grudgingly interpret 0 and NULL as a interal type.
```

## 首选 _nullptr_ 而不是 _0_ 和 _NULL_

```C++
  int f1(std::shared_ptr<Widget> spw);            // call these only when
  double f2(std::unique_ptr<Widget> upw);         // the appropriate
  bool f3(Widget* pw);                            // mutex is locked
```  

```C++
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

```C++
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

## _alias declarations_ 支持模板化，而 _typedef_ 不支持模板化

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

 _scoped enums_ 不会将其所对应的 _enumerators_ 的名称泄露到那个包含着它的作用域中，而 _unscoped enums_ 会发生泄露。

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

_scoped enums_ 所对应的 _enumerators_ 是不可以被隐式转换的，而 _unscoped enums_ 所对应的 _enumerators_ 是可以被隐式转换的。

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

_scoped enums_ 可以直接进行前置声明来减少编译依赖，而 _unscoped enums_ 只有当指明了 _underlying type_ 才可以进行前置声明。
 
* _scoped enums_

```C++
  enum class Color;           // fine
```

* _unscoped enums_

```C++
  enum Color;                 // error!

  enum Color : int;           // fine
``` 

