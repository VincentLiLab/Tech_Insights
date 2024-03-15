# Item 1 理解模板的类型推导

```C++
  template<typename T>
  void f(ParamType param);
  
  f(expr);                    // deduce T and ParamType from expr
```


## _by-reference_ or _by-pointer_

如果 _param_ 的类型为引用类型（注意可以是左值引用类型 _&_ 或是右值引用类型 _&&_），也就是 _ParamType_ 中包含  
有 _reference qualifier_ 的话，那么在模板的类型推导期间，实参的 _reference-ness_ 是被忽略的，然后再通过 _expr_ 的  
类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到引用类型的形参上时，希望的是这个对象是保持不变的，即为：希望形参的类型是 _const&_ 或者 _const&&_的。这也是为什么传递一个 _const_ 类型的对象到一个持有形参的类型为 _T&_ 或者 _T&&_ 的模板上时是安全的：因为对象的 _constness_ 成为了 _T_ 的一部分。

在模板的类型推导期间，指针和引用在本质是相同的，并且数组名或函数名的实参是会退化为指针的，除非它们是被用于初始化引用的。

左值引用的例子

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
  emplate<typename T>
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

右值引用的例子

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

## by-value

如果 _param_ 的类型为 **_值类型_** 的话，那么在模板的类型推导期间，实参的 _reference-ness_、_const_ 或者 _volatile_ 是被  
忽略的，然后再通过 _expr_ 的类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到 **_值类型_** 的形参上时，**_param_** 也不是 **_const_** 的。因为 _param_ 是一个完全和 _expr_ 无  
关的对象，它是 _expr_ 的副本。_expr_ 不能被更改的事实并不能说明 _param_ 是否可以被更改。这也就是为什么当推导  
_param_ 的类型时，_expr_ 的 _constness_ 和 _volatileness_ 会被忽略的原因，因为 _expr_ 不能被更改并不意味着它的副本也  
不能被更改。
 
* 在模板的类型推导期间，引用实参是被当作是非引用的，即为：实参的 _reference-ness_ 是被忽略的。
* 当推导 _universal reference_ 类型的形参的类型时，左值实参是需要特殊对待的。
* 当推导 _by-value_ 的形参的类型时，_const_ 和 _volatile_ 的实参是被当作是 _non-const_ 和 _non-volatile_ 的。 
* 在模板的类型推导期间，数组名或函数名的实参是会退化为指针的，除非它们是被用于初始化引用的。

