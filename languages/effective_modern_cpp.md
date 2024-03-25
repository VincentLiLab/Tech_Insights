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