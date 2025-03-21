- [_Item 1_ 理解模板的类型推导](#item-1-理解模板的类型推导)
  - [_by-reference_](#by-reference)
  - [_by-value_](#by-value)
  - [_by-pointer_](#by-pointer)
- [_Item 2_ 理解 _auto_ 的类型推导](#item-2-理解-auto-的类型推导)
  - [_auto_ 的类型推导和模板的类型推导的关系](#auto-的类型推导和模板的类型推导的关系)
  - [_auto_ 的类型推导和模板的类型推导的异同](#auto-的类型推导和模板的类型推导的异同)
- [_Item 3_ 理解 _decltype_](#item-3-理解-decltype)
  - [用于声明函数返回类型](#用于声明函数返回类型)
  - [用于声明变量类型](#用于声明变量类型)
  - [用于非名字的左值表达式](#用于非名字的左值表达式)
- [_Item 4_ 了解如何查看所推导的类型](#item-4-了解如何查看所推导的类型)
- [_Item 5_ 首选 _auto_ 而不是显式类型声明](#item-5-首选-auto-而不是显式类型声明)
  - [_auto_ 可以避免未初始化的变量和冗长的变量声明，可以使重构的过程简化](#auto-可以避免未初始化的变量和冗长的变量声明可以使重构的过程简化)
  - [_auto_ 可以避免类型不匹配所导致的效率问题](#auto-可以避免类型不匹配所导致的效率问题)
  - [_auto_ 可以避免类型不匹配所导致的可移植问题](#auto-可以避免类型不匹配所导致的可移植问题)
- [_Item 6_ 当 _auto_ 推导出的类型是 _undesired_ 时，使用 _the explicitly typed initializer idiom_](#item-6-当-auto-推导出的类型是-undesired-时使用-the-explicitly-typed-initializer-idiom)
  - [避免 _auto someVar = expression of **invisible** proxy class type_](#避免-auto-somevar--expression-of-invisible-proxy-class-type)
  - [必须 _auto someVar = static\_cast\<T\>(expression of **invisible** proxy class type)_](#必须-auto-somevar--static_casttexpression-of-invisible-proxy-class-type)
  - [_the explicitly typed initializer idiom_ 可以强制 _auto_ 去推导你想要的 **_正确_** 的类型](#the-explicitly-typed-initializer-idiom-可以强制-auto-去推导你想要的-正确-的类型)
- [_Item 7_ 创建对象时区分 _()_ 和 _{}_](#item-7-创建对象时区分--和-)
  - [_braced initialization_ 的用法](#braced-initialization-的用法)
  - [_braced initialization_ 的特性](#braced-initialization-的特性)
  - [_braced initialization_ 的易错](#braced-initialization-的易错)
- [_Item 8_ 首选 _nullptr_ 而不是 _0_ 和 _NULL_](#item-8-首选-nullptr-而不是-0-和-null)
  - [_nullptr_ 在任意情况下都可以做为空指针](#nullptr-在任意情况下都可以做为空指针)
- [_Item 9_ 首选 _alias declaration_ 而不是 _typedef_](#item-9-首选-alias-declaration-而不是-typedef)
  - [_alias declaration_ 是支持模板化的](#alias-declaration-是支持模板化的)
- [_Item 10_ 首选 _scoped enum_ 而不是 _unscoped enum_](#item-10-首选-scoped-enum-而不是-unscoped-enum)
  - [_scoped enum_ 可以降低 _namespace_ 的污染](#scoped-enum-可以降低-namespace-的污染)
  - [_scoped enum_ 所对应的 _enumerator_ 是不可以被隐式转换的](#scoped-enum-所对应的-enumerator-是不可以被隐式转换的)
  - [_scoped enum_ 可以直接进行前置声明来减少编译依赖](#scoped-enum-可以直接进行前置声明来减少编译依赖)
- [_Item 11_ 首选 _deleted function_ 而不是 _private undefined function_](#item-11-首选-deleted-function-而不是-private-undefined-function)
  - [_deleted function_ 是在编译阶段报错的](#deleted-function-是在编译阶段报错的)
  - [_deleted function_ 是可以用于任何函数的](#deleted-function-是可以用于任何函数的)
  - [_deleted function_ 是可以用于模板特化的](#deleted-function-是可以用于模板特化的)
- [_Item 12_ 使用 _override_ 来声明重写函数](#item-12-使用-override-来声明重写函数)
  - [重写的条件](#重写的条件)
  - [_reference qualifier_ 的作用](#reference-qualifier-的作用)
  - [使用 _override_ 来声明重写函数的优势](#使用-override-来声明重写函数的优势)
- [_Item 13_ 首选 _const\_iterator_ 而不是 _iterator_](#item-13-首选-const_iterator-而不是-iterator)
  - [_const\_iterator_ 符合只要有可能使用 _const_ 就应该去使用 _const_ 的规则](#const_iterator-符合只要有可能使用-const-就应该去使用-const-的规则)
- [_Item 14_ 当函数不会抛出异常时声明函数为 _noexcept_](#item-14-当函数不会抛出异常时声明函数为-noexcept)
  - [_C++98_ 的 _exception specification_ 的格式](#c98-的-exception-specification-的格式)
  - [_C++98_ 的 _exception specification_ 的缺点](#c98-的-exception-specification-的缺点)
  - [_C++11_ 的 _exception specification_ 的格式](#c11-的-exception-specification-的格式)
  - [_C++11_ 的 _exception specification_ 的优点](#c11-的-exception-specification-的优点)
  - [_noexcept_ 允许编译器去生成更好的对象代码](#noexcept-允许编译器去生成更好的对象代码)
  - [_noexcept_ 可以提高性能](#noexcept-可以提高性能)
  - [_noexcept_ 是接口，只有当愿意长期来维护一个 _noexcept_ 实现时，才应该声明一个函数为 _noexcept_](#noexcept-是接口只有当愿意长期来维护一个-noexcept-实现时才应该声明一个函数为-noexcept)
  - [_exception-neutral_ 的函数不是 _noexcept_ 的](#exception-neutral-的函数不是-noexcept-的)
  - [扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的](#扭曲函数的实现以去让-noexcept-成为可能是不合理的)
  - [所有的内存释放函数和析构函数默认都是隐式 _noexcept_ 的](#所有的内存释放函数和析构函数默认都是隐式-noexcept-的)
  - [没有声明为 _noexcept_ 的函数也可以是 _noexcept_ 的](#没有声明为-noexcept-的函数也可以是-noexcept-的)
- [_Item 15_ 只要有可能就使用 _constexpr_](#item-15-只要有可能就使用-constexpr)
  - [_constexpr_ 对象](#constexpr-对象)
  - [_constexpr_ 函数](#constexpr-函数)
  - [只要有可能就使用 _constexpr_ 的优势](#只要有可能就使用-constexpr-的优势)
  - [_constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_](#constexpr-是接口只有当愿意长期来维护一个-constexpr-实现时才应该声明一个对象或函数为-constexpr)
- [_Item 16_ 使 _const_ 成员函数成为线程安全的](#item-16-使-const-成员函数成为线程安全的)
  - [_mutable_ 的用法](#mutable-的用法)
  - [_std::atomic_ 与 _std::mutex_](#stdatomic-与-stdmutex)
- [_Item 17_ 理解特殊成员函数的生成](#item-17-理解特殊成员函数的生成)
  - [_default constructor_ 被生成的条件](#default-constructor-被生成的条件)
  - [析构函数被生成的条件](#析构函数被生成的条件)
  - [_move operation_ 被生成的条件](#move-operation-被生成的条件)
  - [_copy operation_ 被生成的条件](#copy-operation-被生成的条件)
  - [成员函数模板永远不会阻止特殊成员函数的生成](#成员函数模板永远不会阻止特殊成员函数的生成)
- [_Item 18_ 对于 _exclusive-ownership_ 的资源管理使用 _std::unique\_ptr_](#item-18-对于-exclusive-ownership-的资源管理使用-stdunique_ptr)
  - [对于 _exclusive-ownership_ 的资源管理使用 _std::unique\_ptr\<T\>_](#对于-exclusive-ownership-的资源管理使用-stdunique_ptrt)
  - [禁止使用 _std::unique\_ptr\<T\[\]\>_](#禁止使用-stdunique_ptrt)
  - [_std::unique\_ptr_ 适合做为工厂函数的返回类型](#stdunique_ptr-适合做为工厂函数的返回类型)
- [_Item 19_ 对于 _shared-ownership_ 的资源管理使用 _std::shared\_ptr_](#item-19-对于-shared-ownership-的资源管理使用-stdshared_ptr)
  - [对于 _shared-ownership_ 的资源管理使用 _std::shared\_ptr\<T\>_](#对于-shared-ownership-的资源管理使用-stdshared_ptrt)
  - [禁止使用 _std::shared\_ptr\<T\[\]\>_](#禁止使用-stdshared_ptrt)
  - [使用 _std::shared\_ptr_ 的成本](#使用-stdshared_ptr-的成本)
  - [禁止使用原始指针类型的变量来创建 _std::shared\_ptr_](#禁止使用原始指针类型的变量来创建-stdshared_ptr)
  - [创建 _std::shared\_ptr_ 的方式](#创建-stdshared_ptr-的方式)
  - [_enable\_shared\_from\_this_ 模板](#enable_shared_from_this-模板)
- [_Item 20_ 对于可能会悬空的 _std::shared\_ptr-like_ 指针使用 _std::weak\_ptr_](#item-20-对于可能会悬空的-stdshared_ptr-like-指针使用-stdweak_ptr)
  - [_std::weak\_ptr_ 不能阻止它所对应的 _std::shared\_ptr_ 去悬空](#stdweak_ptr-不能阻止它所对应的-stdshared_ptr-去悬空)
  - [_std::weak\_ptr_ 的 _expired_ 函数不能阻止它所对应的 _std::shared\_ptr_ 去悬空](#stdweak_ptr-的-expired-函数不能阻止它所对应的-stdshared_ptr-去悬空)
  - [使用 _std::weak\_ptr_ 来检查 _std::shared\_ptr_ 是否悬空的方法](#使用-stdweak_ptr-来检查-stdshared_ptr-是否悬空的方法)
  - [_std::weak\_ptr_ 的使用场景](#stdweak_ptr-的使用场景)
- [_Item 21_ 首选 _std::make\_unique_ 和 _std::make\_shared_ 而不是直接使用 _new_](#item-21-首选-stdmake_unique-和-stdmake_shared-而不是直接使用-new)
  - [_std::make\_unique_ 和 _std::make\_shared_ 可以消除代码重复](#stdmake_unique-和-stdmake_shared-可以消除代码重复)
  - [_std::make\_unique_ 和 _std::make\_shared_ 可以避免资源泄露](#stdmake_unique-和-stdmake_shared-可以避免资源泄露)
  - [_std::make\_shared_ 可以提高效率](#stdmake_shared-可以提高效率)
  - [_std::make\_unique_ 和 _std::make\_shared_ 不可以指定 _custom deleter_](#stdmake_unique-和-stdmake_shared-不可以指定-custom-deleter)
  - [_std::make\_unique_ 和 _std::make\_shared_ 不可以使用 _braced initializer_](#stdmake_unique-和-stdmake_shared-不可以使用-braced-initializer)
  - [_std::make\_shared_ 不可以用于那些有着私有版本的 _operator new_ 和 _operator delete_ 的类](#stdmake_shared-不可以用于那些有着私有版本的-operator-new-和-operator-delete-的类)
  - [禁止完美转发 _new expression_](#禁止完美转发-new-expression)
- [_Item 22_ 当使用 _Pimpl Idiom_ 时，在源文件中定义特殊成员函数](#item-22-当使用-pimpl-idiom-时在源文件中定义特殊成员函数)
  - [_Pimpl Idiom_ 是通过减少类的客户和类的实现之间的编译依赖来缩短编译时间的](#pimpl-idiom-是通过减少类的客户和类的实现之间的编译依赖来缩短编译时间的)
  - [使用 _std::unique\_ptr_ 实现 _Pimpl Idiom_ 时，需要特殊处理](#使用-stdunique_ptr-实现-pimpl-idiom-时需要特殊处理)
- [_Item 23_ 理解 _std::move_ 和 _std::forward_](#item-23-理解-stdmove-和-stdforward)
  - [函数所返回的右值引用肯定是右值](#函数所返回的右值引用肯定是右值)
  - [_std::move_ 是转换而不是移动](#stdmove-是转换而不是移动)
  - [_std::forward_ 是转换而不是转发](#stdforward-是转换而不是转发)
  - [_std::move_ 并不能保证转换后的结果是可以被移动的，禁止将可以被移动的对象声明为 _const_](#stdmove-并不能保证转换后的结果是可以被移动的禁止将可以被移动的对象声明为-const)
  - [_lvalue-reference-to-const_ 可以绑定左值和右值](#lvalue-reference-to-const-可以绑定左值和右值)
- [_Item 24_ 区分 _universal reference_ 和右值引用](#item-24-区分-universal-reference-和右值引用)
  - [_\&\&_ 是 _universal reference_ 的场景](#-是-universal-reference-的场景)
  - [_\&\&_ 是右值引用的场景](#-是右值引用的场景)
  - [_universal references_ 可以是左值引用或右值引用](#universal-references-可以是左值引用或右值引用)
- [_Item 25_ _std::move_ 用于右值引用 _std::forward_ 用于 _univeral reference_](#item-25-stdmove-用于右值引用-stdforward-用于-univeral-reference)
  - [_std::move_ 用于右值引用，而不能用于 _univeral reference_](#stdmove-用于右值引用而不能用于-univeral-reference)
  - [_std::forward_ 用于 _univeral reference_，而不能用于右值引用](#stdforward-用于-univeral-reference而不能用于右值引用)
  - [只能在最后一次使用完右值引用和 _universal reference_ 后，再去使用 _std::move_ 或 _std::forward_](#只能在最后一次使用完右值引用和-universal-reference-后再去使用-stdmove-或-stdforward)
  - [对于 _return-by-value_ 的函数所返回的右值引用和 _universal reference_，可以使用 _std::move_ 或 _std::forward_ 来提高效率](#对于-return-by-value-的函数所返回的右值引用和-universal-reference可以使用-stdmove-或-stdforward-来提高效率)
  - [_RVO_ _return value optimization_](#rvo-return-value-optimization)
  - [禁止在 _return-by-value_ 的函数所返回的局部对象上使用 _std::move_](#禁止在-return-by-value-的函数所返回的局部对象上使用-stdmove)
- [_Item 26_ 避免重载 _univeral reference_](#item-26-避免重载-univeral-reference)
  - [重载 _universal reference_ 几乎总是会导致 _universal reference_ 的重载函数在不期望被调用的情况下却被调用到](#重载-universal-reference-几乎总是会导致-universal-reference-的重载函数在不期望被调用的情况下却被调用到)
- [_Item 27_ 熟悉重载 _univeral reference_ 的替代方法](#item-27-熟悉重载-univeral-reference-的替代方法)
  - [_tag dispatch_](#tag-dispatch)
  - [_std::enable\_if_](#stdenable_if)
- [_Item 28_ 理解引用折叠](#item-28-理解引用折叠)
  - [引用折叠](#引用折叠)
- [_Item 29_ 假设 _move operation_ 是不存在的、成本大的和不可使用的](#item-29-假设-move-operation-是不存在的成本大的和不可使用的)
  - [移动并不一定比拷贝快](#移动并不一定比拷贝快)
  - [标准 _container_ 与有 _STL_ 接口的内建数组的区别](#标准-container-与有-stl-接口的内建数组的区别)
- [_Item 30_ 熟悉完美转发失败的场景](#item-30-熟悉完美转发失败的场景)
  - [_braced initializer_](#braced-initializer)
  - [_0_ 或 _NULL_ 来做为空指针](#0-或-null-来做为空指针)
  - [只有声明的 _integral static const_ 的数据成员](#只有声明的-integral-static-const-的数据成员)
  - [重载的函数名和模板名](#重载的函数名和模板名)
  - [_bitfield_](#bitfield)
- [_Item 31_ 避免默认捕获模式](#item-31-避免默认捕获模式)
  - [捕获的范围](#捕获的范围)
  - [默认捕获会隐式捕获 _this_ 指针，容易产生悬空的 _this_ 指针](#默认捕获会隐式捕获-this-指针容易产生悬空的-this-指针)
  - [_\[\&\]_ 没有显示指明让人印象深刻](#-没有显示指明让人印象深刻)
  - [_\[=\]_ 会误导性地暗示 _lambda_ 是 _self-contained_ 的](#-会误导性地暗示-lambda-是-self-contained-的)
- [_Item 32_ 使用初始化捕获来将对象移动到 _closure_ 中](#item-32-使用初始化捕获来将对象移动到-closure-中)
  - [初始化捕获](#初始化捕获)
  - [使用初始化捕获来将对象移动到 _closure_ 中](#使用初始化捕获来将对象移动到-closure-中)
- [_Item 33_ 在 _auto\&\&_ 形参上使用 _decltype_ 来进行完美转发](#item-33-在-auto-形参上使用-decltype-来进行完美转发)
- [_Item 34_ 首选 _lambda_ 而不是 _std::bind_](#item-34-首选-lambda-而不是-stdbind)
- [_Item 35_ 首选 _task-based_ 编程而不是 _thread-based_ 编程](#item-35-首选-task-based-编程而不是-thread-based-编程)
  - [_task-based_ 和 _thread-based_ 编程](#task-based-和-thread-based-编程)
  - [_task-based_ 编程可以直接获取异步运行的函数的返回值](#task-based-编程可以直接获取异步运行的函数的返回值)
  - [_task-based_ 编程可以避免线程耗尽问题和线程 _oversubscription_ 问题](#task-based-编程可以避免线程耗尽问题和线程-oversubscription-问题)
- [_Item 36_ 如果异步是必须要的话，那么指明 _std::launch::async_](#item-36-如果异步是必须要的话那么指明-stdlaunchasync)
  - [_std::launch::async_](#stdlaunchasync)
  - [_std::launch::deferred_](#stdlaunchdeferred)
  - [_std::launch::async | std::launch::deferred_](#stdlaunchasync--stdlaunchdeferred)
  - [禁止使用 _std::launch::async | std::launch::deferred_](#禁止使用-stdlaunchasync--stdlaunchdeferred)
- [_Item 37_ 使 _std::thread_ 在所有路径上都为 _unjoinable_](#item-37-使-stdthread-在所有路径上都为-unjoinable)
  - [_unjoinable_ _std::thread_ 和 _joinable_ _std::thread_](#unjoinable-stdthread-和-joinable-stdthread)
  - [调用 _unjoinable_ _std::thread_ 的 _join_ 或 _detach_ 会产生 _undefined behavior_](#调用-unjoinable-stdthread-的-join-或-detach-会产生-undefined-behavior)
  - [调用 _joinable_ _std::thread_ 的析构函数会导致程序终止](#调用-joinable-stdthread-的析构函数会导致程序终止)
  - [_RAII_](#raii)
  - [通过 _RAII_ 来使 _std::thread_ 在所有路径上都为 _unjoinable_](#通过-raii-来使-stdthread-在所有路径上都为-unjoinable)
  - [在数据成员的列表最后再去声明 _std::thread_ 对象](#在数据成员的列表最后再去声明-stdthread-对象)
- [_Item 38_ 注意各种各样的线程 _handle_ 的析构函数](#item-38-注意各种各样的线程-handle-的析构函数)
  - [_future_ 的析构函数的行为](#future-的析构函数的行为)
- [_Item 39_ 对于 _one-shot_ 事件通信考虑 _void future_](#item-39-对于-one-shot-事件通信考虑-void-future)
  - [_one-shot_ 事件通信](#one-shot-事件通信)
  - [_condition variable_ 方法](#condition-variable-方法)
  - [_shared boolean flag_](#shared-boolean-flag)
  - [_condition variable_ \& _boolean_ 方法](#condition-variable--boolean-方法)
  - [_void future_ 方法](#void-future-方法)
- [_Item 40_ 并发使用 _std::atomic_ 特殊内存使用 _volatile_](#item-40-并发使用-stdatomic-特殊内存使用-volatile)
  - [_std::atomic_ 的所有成员函数都是原子的](#stdatomic-的所有成员函数都是原子的)
  - [_std::atomic_ 会对代码的重新排序施加限制](#stdatomic-会对代码的重新排序施加限制)
  - [_volatile_ 会禁止对所对应的内存上的操作执行优化](#volatile-会禁止对所对应的内存上的操作执行优化)
- [_Item 41_ 对于移动是成本小的且总是会被拷贝的可拷贝的形参考虑 _pass-by-value_](#item-41-对于移动是成本小的且总是会被拷贝的可拷贝的形参考虑-pass-by-value)
- [_Item 42_ 考虑使用 _emplacement_ 来代替 _insertion_](#item-42-考虑使用-emplacement-来代替-insertion)
  - [_container_ 中的 _emplacement_ 和 _insertion_](#container-中的-emplacement-和-insertion)
  - [_emplacement_ 不需要生成临时变量](#emplacement-不需要生成临时变量)
  - [_copy initialization_ 和 _direct initialization_](#copy-initialization-和-direct-initialization)

# _Item 1_ 理解模板的类型推导

```C++
  template<typename T>
  void f(ParamType param);
  
  f(expr);                    // deduce T and ParamType from expr
```

## _by-reference_

如果 _param_ 的类型为引用类型（注意可以是左值引用类型 _&_ 或是右值引用类型 _&&_），也就是 _ParamType_ 中包含有 _reference qualifier_ 的话，那么在模板的类型推导期间，首先要忽略 _expr_ 的 _reference-ness_，然后再通过 _expr_ 的类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到引用类型的形参上时，希望的是这个对象是保持不变的，即为：希望形参的类型是 _const&_ 或 _const&&_ 的。这也是为什么传递一个 _const_ 类型的对象到一个持有形参的类型为 _T&_ 或者 _T&&_ 的模板上时是安全的：因为对象的 _constness_ 成为了 _T_ 的一部分。

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

  f(std::move(rx));           // std::move(rx) is rvalue, so T is int,
                              // param's type is therefore const int&&

  f(27);                      // 27 is rvalue, so T is int,
                              // param's type is therefore const int&& 
```

## _by-value_

如果 _param_ 的类型为 **_值类型_** 的话，那么在模板的类型推导期间，首先要忽略 _expr_ 的 _reference-ness_、_const_ 或者 _volatile_，然后再通过 _expr_ 的类型和 _ParamType_ 进行模式匹配去确定 _T_。

当传递一个 _const_ 类型的对象到 **_值类型_** 的形参上时，并不希望这个对象是保持不变的，即为：并不希望形参的类型是 _const_ 的。_param_ 是一个完全和 _cx_ 和 _rx_ 无关的对象，它是 _cx_ 或 _rx_ 的副本。_cx_ 和 _rx_ 不能被更改并不能说明 _param_ 不可以被更改。这也是为什么当推导 _param_ 的类型时，_expr_ 的 _constness_ 和 _volatileness_ 会被忽略的原因，因为 _expr_ 不能被更改并不意味着它的副本也不能被更改。
 
## _by-pointer_

如果 _param_ 的类型为指针类型的话，那么在模板的类型推导期间，通过 _expr_ 的类型和 _ParamType_ 进行模式匹配去确定 _T_。在模板的类型推导期间，指针和引用在本质上是相同的。

当传递一个 _const_ 类型的对象到指针类型的形参上时，希望的是这个对象是保持不变的，即为：希望形参的类型是 _const T*_ 的。这也是为什么传递一个 _const_ 类型的对象到一个持有形参的类型为 _T*_ 的模板上时是安全的：因为对象的 _constness_ 成为了 _T_ 的一部分。

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

注意这种场景：_expr_ 是指向 _const_ 对象的 _const_ 指针，并且 _expr_ 会被传递给 _by-pointer_ 或  _by-value_ 的 _param_。当 _expr_ 被传递给 _f_ 时，无论是在 _by-pointer_ 情况下还是在 _by-value_ 的情况下，这个指针 _expr_ 都会按位复制给 _param_ 。所以此时 _expr_ 本身是 _pass-by-value_ 的，那么与 _by-value_ 的形参所对应的类型推导规则一样，_expr_ 的 _constness_ 会被忽略掉，所推导出的 _param_ 的类型就是就是 _const T*_ 了。_expr_ 所指向的对象的 _constness_ 在类型推导期间是被保留的，而 _expr_ 的 _constness_ 在拷贝 _expr_ 去创建一个新的指针 _param_ 时是被忽略掉的。

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

# _Item 2_ 理解 _auto_ 的类型推导

## _auto_ 的类型推导和模板的类型推导的关系

_auto_ 扮演的是模板的类型推导中的 _T_ 的角色，而变量的 _type specifier_ 充当的是 _ParamType_ 的作用，变量声明中的 = 右侧的表达式可以当作是模板的类型推导中的实参的角色。  

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

_auto_ 的类型推导和模板的类型推导一般情况下都是相同的，只是 _auto_ 的类型推导会假设 _braced initializer_ 表示的是 _std::initialier_list_，而模板的类型推导则不会。

```C++
  auto x = { 11, 23, 9 };     // x's type is
                              // std::initializer_list<int>

  template<typename T>        // template with parameter
  void f(T param);            // declaration equivalent to
                              // x's declaration

  f({ 11, 23, 9 });           // error! can't deduce type for T
```  

在函数和 _lambda_ 返回值和形参中，_auto_ 意味着模板的类型推导，而不是 _auto_ 的类型推导，此时并不会假设 _braced initializer_ 表示的是 _std::initialier_list_。

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

# _Item 3_ 理解 _decltype_

_decltype(auto)_ 可以用于声明函数返回类型和用于声明变量类型，所遵循的规则是：其中的 _auto_ 指明了类型会被推导，而 _decltype_ 则说明了 _decltype_ 的规则应该在推导期间被使用。

## 用于声明函数返回类型

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

# _Item 4_ 了解如何查看所推导的类型

因为一些工具的结果可能是无用的和不准确的，所以理解 C++ 的类型推导规则仍然是必要的。

# _Item 5_ 首选 _auto_ 而不是显式类型声明

## _auto_ 可以避免未初始化的变量和冗长的变量声明，可以使重构的过程简化

_auto_ 变量必须要进行初始化，所以可以避免未初始化的变量和冗长的变量声明。

```C++
  int x1;                     // potentially uninitialized
  
  auto x2;                    // error! initializer required
  
  auto x3 = 0;                // fine, x's value is well-defined
```

## _auto_ 可以避免类型不匹配所导致的效率问题

_auto_ 和 _std::function_ 都可以被用来持有 _closure_，但是 _auto_ 声明的用于持有 _closure_ 的变量的类型和 _closure_ 的类型是相同的，所使用的内存大小和 _closure_ 所需要的内存大小也是相同的。而 _std::function_ 声明的持有 _closure_ 的变量的类型是 _std::function_ 模板的实例，对于任意的所给定的 _signature_ 来说，它的大小都是固定的。这个大小对于存储 _closure_ 来说，可能是不够的。在这种场景下，_std::function_ 的构造函数就会去分配堆栈内存来存储 _closure_。导致的结果就是 _std::function_ 对象通常会比 _auto_ 所声明的对象使用更多的内存。由于限制内联和产生间接调用的实现细节，通过 _std::function_ 对象来调用 _closuer_ 几乎肯定要比通过 _auto_ 所声明的对象来调用 _closure_ 要慢。而且可能还会产生内存溢出的异常。 

## _auto_ 可以避免类型不匹配所导致的可移植问题

```C++
  std::vector<int> v;
  …
  unsigned sz = v.size();
```  

_v.size()_ 的官方的返回类型是 _std::vector<int>::size_type_，但是很少有开发者会注意到它。_std::vector<int>::size_type_ 被指定为是 _unsigned integral_ 类型，所以很多开发者认为使用 _unsigned_ 也足够了，所以就写成了上面那样。这可以有一些有趣的后果。例如：在 _32-bit_ _Windows_ 上，_unsigned_ 和 _std::vector<int>::size_type_ 是相同的大小，但是在 _64-bit_ _Windows_ 上，_unsigned_ 是 _32bits_，而 _std::vector<int>::size_type_ 则是  _64bits_。这意味着：在 _32-bit_ _Windows_ 上可以正确工作的代码可能在 _64-bit_ _Windows_ 下就表现的是不正确的，然后当你将程序从 _32bits_ 移植到 _64bits_ 时，谁会想花时间在这种问题上呢？

```C++
  std::unordered_map<std::string, int> m;
  …
  
  for (const std::pair<std::string, int>& p : m)
  {
    …                         // do something with p
  }
```  

要意识到缺失了什么，需要记住 _std::unordered_map_ 的 _key_ 是 _const_ 的，所以在哈希表也就是 _std::unordered_map_ 中的 _std::pair_ 的类型不是 _std::pair<std::string, int>_ 而是 _std::pair<const std::string, int>_。但是这却不是上面循环中的变量 _p_ 的类型。结果就是编译器将会尽力寻找方法来将 _std::pair<const std::string, int>_ 对象也就是哈希表中的对象转换为 _std::pair<std::string, int>_ 也就是 _p_ 的类型的对象。这可以通过以下方法来完成：通过拷贝 _m_ 中的每一个对象来创建 _p_ 所想要绑定的类型的临时对象，然后再将引用 _p_ 绑定到这个临时对象上。在每次循环结束时，所对应的临时变量都将会被销毁。如果你写了这个循环的话，你可能会被这个行为所惊讶到，因为几乎可以肯定的是你想要只是将引用 _p_ 绑定到 _m_ 中的每个元素上而已。

# _Item 6_ 当 _auto_ 推导出的类型是 _undesired_ 时，使用 _the explicitly typed initializer idiom_

## 避免 _auto someVar = expression of **invisible** proxy class type_

```C++
  std::vector<bool> features(const Widget& w);
``` 

```C++
  auto highPriority = features(w)[5];   // deduce highPriority's
                                        // type
```  

_std::vector::operator[]_ 一般情况下返回的都是 _T&_，但是 _C++_ 禁止引用 _bit_。不能够返回 _bool&_，所以 _std::vector&lt;bool&gt;_ 的 _operator[]_ 返回的是一个表现地像是 _bool&_ 的对象。为了可以这样做，_std::vector&lt;bool&gt;::reference_ 必须在基本上所有可以使用 _bool&_ 的上下文中都可以使用。_std::vector&lt;bool&gt;::reference_ 中的可以完成这个工作的一个特性是可以隐式转换为 _bool_，是 _bool_ 而不是 _bool&_。所以此时 _highPriority_ 的类型是 _std::vector&lt;bool&gt;::reference_。

_std::vector&lt;bool&gt;::reference_ 的一种实现是去包含一个指针，这个指针指向一个机器字，而这个机器字中保存着那个 _bit_ 和那个 _bit_ 所对应的偏移量。因为此时涉及到是 _auto_ 的 _by-value_ 的类型推导规则，所以此时 _highPriority_ 也就有了这个指针的副本了，在 _auto highPriority = features(w)[5];_ 这个语句结束后，因为临时对象会被销毁掉。所以临时对象所对应的指针也会被销毁掉，那么 _highPriority_ 就包含有一个悬空指针了。

```C++
  bool highPriority = features(w)[5];   // declare highPriority's
                                        // type explicitly
```  

在执行 _bool highPriority = features(w)[5];_ 时，会将 _std::vector&lt;bool&gt;::reference_ 对象隐式转换为 _bool_，以去初始化 _highPriority_。此时就没有 _dangling_ 指针的问题了。

_std::vector&lt;bool&gt;::reference_ 就是 **_invisble_** _proxy class_，不能 **_直接_** 和 _auto_ 一起使用，因为 **_invisble_** _proxy class_ 这种类型的对象通常不会被设计为比单语句存在的还久，所以创建这种类型的变量就是在违反基础库的设计假设。

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
这很难表示出“我是故意缩小了这个函数所返回的值的精度。”但是使用 _the explicitly typed initializer idiom_ 的声明却可以做到。

```C++
  auto ep = static_cast<float>(calcEpsilon());
```

# _Item 7_ 创建对象时区分 _()_ 和 _{}_

## _braced initialization_ 的用法

显式声明

```C++
  int x{ 0 };                 // fine!

  int x = { 0 };              // fine!
```

_auto_ 声明

```C++
  auto z{ 0 };                // fine! z is int                 

  auto z = { 0 };             // fine! but z is std::initializer_lists

  auto z{0, 1};               // error, shit! C++ fuck you
```
> 原文认为：“我通常会忽略 _equals-sign-plus-braces_ 语法，因为 _C++_ 通常把它和 _braces-only_ 做同样地处理。” 实际测试不是这样，使用的编译器是 _c++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0_。_auto v{0, 1};_ 这样是无法通过编译的并且 _auto v{0}_ 中的 _v_ 是 _int_ 类型而不是 _std::initializer_list_ 类型。_C++ fuck you!_

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

_braced initialization_ 可以禁止内建类型之间的 _implicit narrowing conversion_。

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

_braced initialization_ 可以让编译器强烈地优先选择持有 _std::initializer_list_ 的重载函数。**_强烈地优先选择_** 是指：只要可以 **_implicit converting_** 的话，编译器强就会去选择持有 _std::initializer_list_ 的重载函数，就算此时的这个 **_implicit converting_** 是 _implicit narrowing converting_ 的，会导致报错，编译器也会去选择持有 _std::initializer_list_ 的重载函数。

> 只有当没有办法将 _braced initializer_ 中的实参的类型转换 _std::initializer_list_ 中的类型时，编译器才会回退到一般的重载决议中，但是注意经过重载决议后，如果又有机会去选择持有 _std::initializer_list_ 的重载函数的话，那么编译器强仍然会去选择持有 _std::initializer_list_ 的重载函数。

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


_braced initialization_ 对于常规的 _copy constructor_ 和 _move constructor_ 会有不同。
> 原文认为：“甚至那些常规的拷贝构造和移动构造也可以被 _std::initializer_list_ 构造函数所劫持。”实际测试不是这样，使用的编译器是 _c++ (Ubuntu 11.4.0-1ubuntu1~22.04) 11.4.0_。

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

如果创建 _localObject_ 时 _doSomeWork_ 使用了 _()_ 的话，那么 _std::vector_ 就是有 _10_ 个元素。如果创建 _localObject_ 时  _doSomeWork_ 使用了 _{}_ 的话，那么 _std::vector_ 就是有 _2_ 个元素。哪一个是正确的？_doSomeWork_ 的作者是不知道的，只有调用方知道。_()_ 和 _{}_ 只使用其中之一就好。

# _Item 8_ 首选 _nullptr_ 而不是 _0_ 和 _NULL_

## _nullptr_ 在任意情况下都可以做为空指针

_nullptr_ 在任意情况下都可以做为空指针，而 _0_ 和 _NULL_ 只有在只有指针被使用的情况下才可以做为空指针，否则将 _0_ 和 _NULL_ 做为它们本身的 _integral_ 类型。

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

因为此时是在只有指针被使用的环境下，所以可以将 _0_ 隐式转换为 _std::shared_ptr&lt;Widget&gt;_ 类型的形参。_NULL_ 和 _std::unique_ptr&lt;Widget&gt;_ 类型的形参也是这样的情况。

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

因为在 _lockAndCall_ 所对应的模板的类型推导规则下，会将 _0_ 所对应的 _ptr_ 的类型推导为 _integral_ 类型，而 _integral_ 类型是无法被隐式转换为 _std::shared_ptr&lt;Widget&gt;_ 类型的，所以是错误的。_NULL_ 和 _std::unique_ptr&lt;Widget&gt;_ 类型的形参也是这样的情况。

# _Item 9_ 首选 _alias declaration_ 而不是 _typedef_

## _alias declaration_ 是支持模板化的

_alias declaration_ 是支持模板化的，而 _typedef_ 是不支持模板化的。

* _alias declaration_

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

# _Item 10_ 首选 _scoped enum_ 而不是 _unscoped enum_

## _scoped enum_ 可以降低 _namespace_ 的污染

 _scoped enum_ 是不会将其所对应的 _enumerator_ 的名称泄露到那个包含着它的作用域中的，而 _unscoped enum_ 是会的。

* _scoped enum_

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

 _unscoped enum_

 ```C++
  enum Color { black, white, red };     // black, white, red are
                                        // in same scope as Color

  auto white = false;                   // error! white already
                                        // declared in this scope
```

## _scoped enum_ 所对应的 _enumerator_ 是不可以被隐式转换的

_scoped enum_ 所对应的 _enumerator_ 是不可以被隐式转换的，而 _unscoped enum_ 所对应的 _enumerator_ 是可以被隐式转换的。

* _scoped enum_

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

* _unscoped enum_

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

## _scoped enum_ 可以直接进行前置声明来减少编译依赖

_scoped enum_ 可以直接进行前置声明来减少编译依赖，而 _unscoped enum_ 则需要在指明 _underlying type_ 的情况下才可以进行前置声明。
 
* _scoped enum_

```C++
  enum class Color;           // fine
```

* _unscoped enum_

```C++
  enum Color;                 // error!

  enum Color : int;           // fine
``` 

# _Item 11_ 首选 _deleted function_ 而不是 _private undefined function_

## _deleted function_ 是在编译阶段报错的

_deleted function_ 是在编译阶段报错的，而 _private undefined function_ 则是在链接阶段报错的。_deleted function_ 是应该被声明为 _public_ 而不是 _private_ 的。

## _deleted function_ 是可以用于任何函数的

_deleted function_ 是可以用于任何函数的，包括成员函数、非成员函数和模板实例化，而 _private undefined function_ 是只可以用于成员函数的。

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

* 模板实例化

```C++
  template<typename T>
  void processPointer(T* ptr);

  template<>
  void processPointer<void>(void*) = delete;
  
  template<>
  void processPointer<char>(char*) = delete; 
```  

## _deleted function_ 是可以用于模板特化的

_deleted function_ 是可以用于模板特化的，而 _private undefined function_ 是不可以的。如果在类中有一个函数模板，并且你想通过 _private_ 声明来 _disable_ 它的一些实例的话，那么这样做是不可以的。因为不能给成员函数模板的特化一个和主模板是不同的访问级别。

* _deleted function_

```C++
  class Widget {
  public:
    …
    template<typename T>
    void processPointer(T* ptr)
    { … }
    …
  };
  template<>                                                // still
  void Widget::processPointer<void>(void*) = delete;        // public,
                                                            // but
                                                            // deleted
```

* _private undefined function_

```C++
  class Widget {
  public:
    …
    template<typename T>
    void processPointer(T* ptr)
    { … }

  private:
    template<>                                              // error!       
    void processPointer<void>(void*);
 };
``` 

# _Item 12_ 使用 _override_ 来声明重写函数

## 重写的条件

_base class_ 的函数必须是 _virtual function_。  
_base function_ 和 _derived function_ 的名字必须一致，除了析构函数的场景以外。  
_base function_ 和 _derived function_ 的形参类型必须一致。  
_base function_ 和 _derived function_ 的 _constness_ 必须一致。  
_base function_ 和 _derived function_ 的返回类型和异常规范必须要兼容。  
_base function_ 和 _derived function_ 的 _reference qualifier_ 必须一致。

## _reference qualifier_ 的作用

成员函数的 _reference qualifiers_ 使得可以不同地处理左值 _*this_ 对象和右值 _*this_ 对象了。

```C++
  class Widget {
  public:
    using DataType = std::vector<double>;
    …

    DataType& data() &                  // for lvalue Widgets,
    { return values; }                  // return lvalue

    DataType data() &&                  // for rvalue Widgets,
    { return std::move(values); }       // return rvalue
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

# _Item 13_ 首选 _const_iterator_ 而不是 _iterator_

## _const_iterator_ 符合只要有可能使用 _const_ 就应该去使用 _const_ 的规则

只要有可能使用 _const_ 就应该去使用 _const_，这个规则在 _C++11_ 之前就是一直有的。但是在 _C++98_ 中，当使用到 _iterator_ 时，这个原则就不适用了。而在 _C++11_ 中，这个原则就很适用了。_container_ 的  成员函数 _cbegin_ 和 _cend_ 都产生的是 _const_iterator_。

```C++
  std::vector<int> values;                        // as before
  
  …
  
  auto it =                                       // use cbegin
  
  std::find(values.cbegin(),values.cend(), 1983); // and cend
  
  values.insert(it, 1998);
```

# _Item 14_ 当函数不会抛出异常时声明函数为 _noexcept_

## _C++98_ 的 _exception specification_ 的格式

```C++
  int f(int x) throw();                           // no exceptions from f: C++98 style

  int f(int x) throw(int, char, std::string);     // exceptions from f: C++98 style
```

## _C++98_ 的 _exception specification_ 的缺点

必须要总结出一个函数所有的可能会抛出的异常类型，所以如果这个函数的实现被更改了的话，那么它的 _exception specification_ 可能也需要更改。而改变一个函数的  _exception specification_  是可能会破坏客户的代码的，因为调用方可能是依赖于原来的 _exception specification_ 的。

## _C++11_ 的 _exception specification_ 的格式

```C++
  int f(int x) noexcept;      // no exceptions from f: C++11 style

  int f(int x);               // exceptions from f: C++11 style
```

## _C++11_ 的 _exception specification_ 的优点

在 _C++11_ 开发期间，达成了这样的一个共识：关于函数的异常抛出行为，真正有意义的信息是函数是否有异常。黑或白，函数要么可能会抛出异常，函数要么保证不会抛出异常。_maybe-or-never_ 二分法构成了 _C++11_ 的 _exception specification_ 的基础，本质上取代了 _C++98_ 的 _exception specification_。_C++98-style_ 的 _exception specification_ 仍然是有效的，但是是被废弃的。在 _C++11_ 中，无条件的 _noexcept_ 是对应于那些保证不会抛出异常的函数的。

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

当一个新元素被添加到 _std::vector_ 时，_std::vector_ 可能已经没有这个元素的空间了，即为：_std::vector_ 的大小等于它的容量了。当发生这种情况时，_std::vector_ 会分配一个新的大的内存块去保存它的元素，并将它的元素从旧内存块转移到新内存块上。在 _C++98_ 中，是通过将每一个元素从旧内存拷贝到新内存来完成的，然后再去销毁旧内存上的对象。这种方法给 _push_back_ 提供了强异常安全保证：如果在拷贝期间抛出了一个异常的话，那么 _std::vector_ 的状态是保持不变的。因为直到全部元素都被成功拷贝到新内存后，旧内存上的全部元素才会被销毁。

在 _C++11_ 中，一个自然的优化是：对于 _std::vector_ 的元素，会使用移动来代替拷贝。不幸地是，这样做是冒着违反 _push_back_ 的异常安全保证风险的。如果 _n_ 个元素已经从旧内存移走了，然后在移动第 _n+1_ 
个元素期间抛出了一个异常，那么 _push_back_ 操作是不可以完成的。但是原来的 _std::vector_ 已经被更改了：因为 _n_ 个元素已经被移动走了。不可能恢复原来的状态，因为试图移动每一个元素回到原来的旧内存都可能会产生异常。

这是一个严重的问题，因为 _legacy_ 代码的行为可能是依赖于 _push_back_ 的强异常安全保证的。因此，_C++11_ 的实现不可以悄悄地使用所对应的 _move operation_ 来替换 _push_back_ 中的 _copy operation_，除非确认 _move operation_ 不会抛出异常。不会抛出异常时，_move operation_ 代替 _copy operation_ 是安全的，唯一的附加影响是将会提升性能。

_std::vector::push_back_ 利用了 **_move if you can, but copy if you must_** 的策略的优势，它不是唯一一个在标准库中这样做的函数。其他的利用了 _C++98_ 的强异常安全保证的函数也是使用了相同的方式，比如：_std::vector::reserve_ 和 _std::deque::insert_ 等。如果 _move operation_ 被认为是不会抛出异常异常的话，那么全部的这些函数会使用 _C++11_ 的 _move operation_ 来代替 _C++98_ 的 _copy operation_。但是这些函数如何可以知道 _move operation_ 是否不会产生异常呢？答案是明显的：去检查 _move operation_ 是否被声明为了 _noexcept_。

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

这些函数是 **_有条件地_** _noexcept_ 的：这些函数是否是 _noexcept_ 的是依赖于 _noexcept clause_ 中的表达式是否是 _noexcept_ 的。例如：给定两个 _Widget_ 类型的 _array_，只有当 _array_ 中的单独的元素的交换是 _noexcept_ 时，即为：_Widget_ 的 _swap_ 是 _noexcept_ 的时，这两个 _Widget_ 所对应的 _array_ 的交换才会是 _noexcept_ 的。因此，_Widget_ 的 _swap_ 的作者决定了 _Widget_ 的 _array_ 的交换是否是 _noexcept_ 的。这依次决定了其他的像 _Widget_ 的 _array_ 的 _array_ 这样的 _swap_ 是否是 _noexcept_ 的。类似地，还有两个包含着 _Widget_ 的 _std::pair_ 对象的交换是否是 _noexcept_ 的是依赖于 _Widget_ 的  _swap_ 是否是 _noexcept_ 的。只有当低级别成分的交换是 _noexcept_ 的时，高级别的数据结构才可以是 _noexcept_ 的，这个事实激励你只要可以就应该去提供 _noexcept_ 的 _swap_ 函数。

## _noexcept_ 是接口，只有当愿意长期来维护一个 _noexcept_ 实现时，才应该声明一个函数为 _noexcept_

只有当你愿意长期来维护一个 _noexcept_ 实现时，你才应该声明一个函数为 _noexcept_。如果你在开始时把一个函数声明为了 _noexcept_，但是后续却后悔了的话，那么你的选择是很有限的。你可以从函数的声明中删除 _noexcept_，即为：改变它的接口，但这是冒着破坏客户的代码的风险的。你还可以改变函数的实现，以让一个异常可以 _escape_，但还是保持原始的但现在是错误的 _exception specification_。如果你这样做了，当这个异常尝试离开函数时，你的程序将会被终止。

## _exception-neutral_ 的函数不是 _noexcept_ 的

事实上，大多数函数都是 _exception-neutral_ 的。这些函数本身是不会抛出异常的，但是它们所调用的函数是可能会抛出异常的。当这种情况发生时，_exception-neutral_ 的函数允许它们所调用的函数所抛出的异常通过它们的路到达更上层的调用链路的处理程序。_exception-neutral_ 的函数永远不是 _noexcept_ 的，因为它们还是可能会抛出 **_just passing through_** 的异常的。因此大多数函数很恰当地缺少了 _noexcept_ 名称。

## 扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的

扭曲函数的实现以去让 _noexcept_ 成为可能是不合理的。**_Is putting the cart before the horse. Is not seeing the forest for the trees_.** 选择你最爱的比喻。如果一个简单的函数实现可能会产生异常的话，比如：调用可能会抛出异常的函数，那么你可以付出努力来将这些抛出的异常对调用方进行隐藏，比如：捕获所有的异常并使用状态码或特殊返回值来代替它，这不仅会使得你的函数的实现变得非常复杂，还会通常使得调用点的代码变得复杂。例如：调用方必须要检查状态码或者特殊返回值。这些复杂度的运行时间成本，比如：额外的分支和更大的函数会给指令缓存施加更大的压力，可能会超出你希望通过 _noexcept_ 所实现的加速，而且代码更难以理解和维护了。这将是糟糕的软件工程。

## 所有的内存释放函数和析构函数默认都是隐式 _noexcept_ 的

对于一些函数来说，成为 _noexcept_ 的是非常重要的，它们默认就应该是那样。在 _C++98_ 中，允许内存释放函数，即为：_operator delete_ 和 _operator delete[]_，和析构函数去抛出异常被认为是一个糟糕的设计，而在 _C++11_ 中，这种风格规则已经被升级为是一个语言规则了。所有的内存释放函数和析构函数，包括用户定义的和编译器生成的，默认都是隐式 _noexcept_ 的。因此不需要声明它们为 _noexcept_。进行声明也不会伤害任何东西，只是不常见而已。只有当类的数据成员，包括所继承的成员和被包含在其他数据成员中的成员，明确地声明了它们的析构函数可能会抛出异常时，比如：声明为 _noexcept(false)_，才是仅有的析构函数不是隐式 _noexcept_ 的情况。这样的析构函数是不常见的。在标准库中是没有这样的析构函数的，如果标准库所使用的对象的析构函数抛出了一个异常的话，比如：这个对象是在 _container_ 中的，是被传递到算法中的，那么程序的行为是未定义的。

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

此处，_doWork_ 是被声明为 _noexcept_ 的，尽管 _doWork_ 调用了 _non-noexcept_ 函数 _setup_ 和 _cleanup_ 函数。这似乎是矛盾的，但却是是可以的，那就是当 _setup_ 和 _cleanup_ 函数在文档中说明了它们是永远不会抛出异常的，尽管它们没有被声明为 _noexcept_。有好的理由将这样不会抛出异常的函数不声明为 _noexcept_，例如：它们可能是 _C_ 库的一部分，甚至一些已经从 _C_ 标准库移动到 _std namespace_ 的函数仍然缺少着 _exception specification_，比如：_std::strlen_ 没有被声明为 _noexcept_。又或者它们是决定不使用 _C++98_ 的 _exception specification_，但还没有被修改为使用 _C++11_ 的 _exception specification_ 的 _C++98_ 的库。

# _Item 15_ 只要有可能就使用 _constexpr_

## _constexpr_ 对象

_constexpr_ 对象的值在编译期间就是已知的，所以需要使用在编译期间就是已知的值来进行初始化，而 _const_ 对象不需要使用在编译期间就是已知的值来进行初始化。也就是说：所有 _constexpr_ 对象都是 _const_ 的，但所有 _const_ 对象不都是 _constexpr_ 的。

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

如果你传递给 _constexpr_ 函数的实参的值是在编译期间就是已知的话，那么这个 _constexpr_ 函数的结果将会在编译期间被计算。如果你传递给 _constexpr_ 函数的实参的值不是在编译期间就是已知的话，那么这个 _constexpr_ 函数的结果将会在运行期间被计算。构造函数和其他成员函数也可以是 _constexpr_ 的。因为如果所传入的实参是在编译期间就是已知的话，那么所构造的对象的数据成员的值也是在编译期间就是已知的。所以可以是 _constexpr_ 的。

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

_constexpr_ 对象能在只读内存中被创建。这意味着：你可以在模板的实参中或者在要指定 _enumerator_ 值的表达式中使用像 _mid.xValue() * 10_ 这样的的表达式了。这意味着：在编译期间所完成的工作和运行期间所完成的工作之间传统上是相当严格的界限开始变得模糊了，一些传统上是在运行期间所完成的计算现在可以迁移到编译期间来进行了。迁移的代码越多，你的程序将会运行的更快，当然编译也会更耗时。

## _constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_

_constexpr_ 是接口，只有当愿意长期来维护一个 _constexpr_ 实现时，才应该声明一个对象或函数为 _constexpr_。

# _Item 16_ 使 _const_ 成员函数成为线程安全的

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
_std::mutex m_ 被声明为了 _mutable_，因为 _m_ 的加锁和解锁是 _non-const_ 成员函数，但却是在 _const_ 成员函数 _roots_ 中，如果 _m_ 没有被声明为 _mutable_ 的话，那么 _m_ 将会被认为 _const_ 对象。

## _std::atomic_ 与 _std::mutex_

对于单个要求同步的变量或内存区域来说，使用 _std::atomic_ 是适当的，但是一旦你有两个或多个要求做为整体来进行操作的变量或内存区域的话，那么你应该去使用 _mutex_。

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

* 第一个线程调用了 _Widget::magicValue_，看到了 _cacheValid_ 为 _false_，执行两个成本高的计算后将它们的和分配给了 _cachedValue_。
* 在那时，第二个线程调用了 _Widget::magicValue_，也看到了 _cacheValid_ 为 _false_，因此也执行了和第一个线程已经完成的是相同的成本大的计算。实际上，“第二个线程”可能是其他多个线程。
  
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
* 此时，第二个线程调用了 _Widget::magicValue_，然后会检查 _cacheValid_。看到的它为 _true_ 后，这个线程会返回 _cachedValue_，尽管第一个线程还没有对它的赋值。因此，所返回的值是错误的。

# _Item 17_ 理解特殊成员函数的生成

## _default constructor_ 被生成的条件

只有当类中没有包含用户声明的构造函数时，_default constructor_ 才会被生成。

## 析构函数被生成的条件

只有当类中没有用户声明的析构函数时，析构函数才会被生成。

## _move operation_ 被生成的条件

只有当类中没有用户声明的 _move operation_、_copy operation_ 和析构函数时，_move operation_ 才会被生成。

如果声明了其中一个 _move operation_ 的话，那么会阻止编译器生成另一个 _move operation_。因为只要声明了其中一个 _move operation_，就表明了编译器所生成的 _move operation_ 的 _memberwise move_ 是不合适的，所以应该阻止编译器生成另一个 _move operation_。

如果声明了任意一个 _copy operation_ 的话，那么会阻止编译器生成 _move operation_。因为只要声明了 _copy operation_，就表明了编译器所生成的 _copy operation_ 的 _memberwise copy_ 是不合适的，编译器也会认为如果 _memberwise copy_ 都不合适的话，那么 _memberwise move_ 也是不合适的。所以应该阻止编译器生成 _move operation_。

如果声明了析构函数的话，那么会阻止编译器生成 _move operation_。因为只要声明了析构函数，就表明了需要在析构函数中做特殊处理，那么编译器所生成的 _move operation_ 的 _memberwise move_ 是不合适的了，所以应该阻止编译器生成 _move operation_。

## _copy operation_ 被生成的条件

只有当类中没有用户声明的 _copy constructor_ 时，_copy constructor_ 才会被生成；同样只有当类中没有用户声明的 _copy assignment operator_ 时，_copy assignment operator_ 才会被生成；如果 _move operation_ 有被声明了的话，那么 _copy operation_ 会被删除。

如果声明了其中一个 _copy operation_ 的话，那么 **_应该_** 阻止编译器生成另一个 _copy operation_。因为只要声明了其中一个 _copy operation_，就表明了编译器所生成的 _copy operation_ 的 _memberwise copy_ 是不合适的，所以应该阻止编译器生成另一个 _copy operation_。但注意是 **_应该_**，但是并没有，因为这会破坏 _C++98_ 的代码。

如果声明了任意一个 _move operation_ 的话，那么会阻止编译器生成 _copy operation_。因为只要声明了 _move operation_，就表明了编译器所生成的 _move operation_ 的 _memberwise move_ 是不合适的，编译器也会认为如果 _memberwise move_ 都不合适的话，那么 _memberwise copy_ 也是不合适的。所以阻止编译器生成 _copy operation_。

如果声明了析构函数的话，那么 **_应该_** 阻止编译器生成 _copy operation_。因为只要声明了析构函数，就表明了需要在析构函数中做特殊处理，那么编译器所生成的 _copy operation_ 的 _memberwise copy_ 是不合适的了，所以应该阻止编译器生成 _copy operation_。但注意是 **_应该_**，但是并没有，因为这会破坏 _C++98_ 的代码。

所以，如果你声明了任意一个 _copy constructor_、_copy assignment operator_ 和析构函数的话，那么你应该把这三个都声明出来，否则虽然仍然是可以编译和运行的，但却是被 _C++11_ 废弃的。

## 成员函数模板永远不会阻止特殊成员函数的生成

```C++
  class Widget {
    …
    template<typename T>                // construct Widget
    Widget(const T& rhs);               // from anything
    
    template<typename T>                // assign Widget
    Widget& operator=(const T& rhs);    // from anything
    …
  };
```  
当 _T_ 是 _Widget_ 时，这些模板是可以被实例化去产生出 _copy constructor_ 和 _copy assignment operator_ 的 _signature_ 的，但是这并不会阻止编译器生成特殊成员函数。


# _Item 18_ 对于 _exclusive-ownership_ 的资源管理使用 _std::unique_ptr_

## 对于 _exclusive-ownership_ 的资源管理使用 _std::unique_ptr&lt;T&gt;_

对于 _exclusive-ownership_ 的资源管理使用 _std::unique_ptr&lt;T&gt;_。

## 禁止使用 _std::unique_ptr&lt;T[]&gt;_

_std::unique_ptr&lt;T&gt;_ 没有 _operator[]_，相应地 _std::unique_ptr&lt;T[]&gt;_ 没有 _operator*_ 和 _operator->_。_std::unique_ptr&lt;T[]&gt;_ 较少被使用，这是因为 _std::array_、_std::vector_ 和 _std::string_ 相比于原始数组几乎总是更好的数据结构选择。所以只有在使用 _C-like_ 的 _API_，才需要使用原始数组。

## _std::unique_ptr_ 适合做为工厂函数的返回类型

因为对于工厂函数来说，它们并不知道调用方会使用 _exclusive ownership_ 语义还是 _shared ownership_ 语义，又因为 _std::unique_ptr_ 是可以被简单高效地转换为 _std::shared_ptr_ 的，所以 _std::unique_ptr_ 适合做为工厂函数的返回类型。

# _Item 19_ 对于 _shared-ownership_ 的资源管理使用 _std::shared_ptr_

## 对于 _shared-ownership_ 的资源管理使用 _std::shared_ptr&lt;T&gt;_

对于 _shared-ownership_ 的资源管理使用 _std::shared_ptr&lt;T&gt;_。

## 禁止使用 _std::shared_ptr&lt;T[]&gt;_

_std::shared_ptr_ 不能用于数组。没有 _std::shared_ptr&lt;T[]&gt;_。禁止指定一个 _custom deleter_ 去执行数组的删除。因为首先，_std::shared_ptr_ 没有提供 _operator[]_；其次，_std::shared_ptr_ 支持 _derived-to-base_ 指针的转换，这种转换对于单个对象是合理的，但是当应用于数组时，就是错误的了。因为 _std::array_、_std::vector_ 和 _std::string_ 相比于原始数组几乎总是更好的数据结构选择，所以只有在使用 _C-like_ 的 _API_，才需要使用原始数组。


## 使用 _std::shared_ptr_ 的成本

因为 _std::shared_ptr_ 会涉及到动态分配的 _control block_、任意大小的 _deleter_ 和 _allocator_、_virtual function_ 的机制和原子引用计数操作，所以是有成本的。

## 禁止使用原始指针类型的变量来创建 _std::shared_ptr_

```C++
  auto pw = new Widget;                           // pw is raw ptr
  
  …
  
  std::shared_ptr<Widget> spw1(pw, loggingDel);   // create control
                                                  // block for *pw
  …

  std::shared_ptr<Widget> spw2(pw, loggingDel);   // create 2nd
                                                  // control block
                                                  // for *pw!
```  

此时，_pw_ 会被析构两次，造成 _undefined behavior_。

```C++
  std::vector<std::shared_ptr<Widget>> processedWidgets;

  class Widget {
  public:
    …
    void Widget::process()
    {
      …                                           // process the Widget
      
      processedWidgets.emplace_back(this);        // add it to list of
    }                                             // processed Widgets;
                                                  // this is wrong!
    …
  }; 
```
如果在成员函数外已经存在有了 _shared_ptr_ 指向了这个 _this_ 的话，那么就会发生 _undefined behavior_。  

所以禁止根据原始指针类型的变量来创建 _std::shared_ptr_

## 创建 _std::shared_ptr_ 的方式 

直接使用 _new_

```C++
  std::shared_ptr<Widget> spw1(new Widget,        // direct use of new
                                loggingDel);
```  

使用智能指针

```C++
 std::shared_ptr<Widget> spw2(spw1);              // spw2 uses same
                                                  // control block as spw1 
```

使用 _make_shared_


```C++
 std::shared_ptr<Widget> spw3 = std::make_shared<Widget>(); // use std::make_shared 
```

## _enable_shared_from_this_ 模板

如果想要根据 _this_ 指针来安全地创建 _std::shared_ptr_ 的话，那么需要继承 _std::enable_shared_from_this_ 模板。它定义有一个成员函数，它会创建一个指向当前对象的 _std::shared_ptr_，但是不会重复创建 _control block_。这个成员函数是 _shared_from_this_，所以，无论什么时候你想要一个指向和 _this_ 指针一样的对象的 _std::shared_ptr_ 时，你都可以在成员函数中使用 _shared_from_this_。这样就可以避免当使用原始指针类型的变量来创建 _std::shared_ptr_ 时所遇到的问题了。

_shared_from_this_ 会在内部找出当前对象的 _control block_，然后会创建一个新的指向那个所找出的 _control block_ 的 _std::shared_ptr_。这种设计依赖于当前对象已经有了它所对应的 _control block_ 了。为了可以这样，必须已经存在一个当前对象的 _std::shared_ptr_，比如：已经在成员函数之外调用了 _shared_from_this_。如果这样的 _std::shared_ptr_ 不存在的话，即为：当前对象没有所对应的 _control block_ 的话，那么行为将是未定义的。

为了避免客户在 _std::shared_ptr_ 指向某对象前就先去调用了执行了 _shared_from_this_ 的成员函数，那些继承自 _std::enable_shared_from_this_ 的类通常会声明它们的构造函数为 _private_，并让客户通过返回类型为 _std::shared_ptr_  的工厂函数来让创建对象。 

```C++
  class Widget: public std::enable_shared_from_this<Widget> {
  public:
    // factory function that perfect-forwards args
    // to a private ctor
    template<typename... Ts>
    static std::shared_ptr<Widget> create(Ts&&... params);
    
    …
    void process();         
    {
      // as before, process the Widget
      …
    
      // add std::shared_ptr to current object to processedWidgets
      processedWidgets.emplace_back(shared_from_this());

    }
    …
  
  private:
    …                         // ctors
  };
``` 

# _Item 20_ 对于可能会悬空的 _std::shared_ptr-like_ 指针使用 _std::weak_ptr_

## _std::weak_ptr_ 不能阻止它所对应的 _std::shared_ptr_ 去悬空 

_std::weak_ptr_ 是根据 _std::shared_ptr_ 来创建的，但是并不会改变它所对应的对象的引用计数，也就是并不会改变它所对应的 _std::shared_ptr_ 所对应的对象的引用计数，所以它并不能阻止它所对应的 _std::shared_ptr_ 去悬空。 

## _std::weak_ptr_ 的 _expired_ 函数不能阻止它所对应的 _std::shared_ptr_ 去悬空

_std::weak_ptr_ 的 _expired_ 函数表示的是它是否过期了，也就是表示的是它所对应的 _std::shared_ptr_ 是否悬空了，因为_std::weak_ptr_ 并不能阻止它所对应的 _std::shared_ptr_ 去悬空，所以在使用 _std::weak_ptr_ 的 _expired_ 函数判断它自己没有过期后，它仍然随时都有可能会过期。现在假设 _std::weak_ptr_ 有解引用操作。如果在调用 _std::weak_ptr_ 的 _expired_ 函数和解引用操作之间，另一个线程 _reassign_ 或者销毁了最后一个指向所对应的对象的 _std::shared_ptr_ 话，那么这个解引用操作会导致 _undefined behavior_。所以 _std::weak_ptr_ 的 _expired_ 函数表示的只是当前此刻它是否过期了，并不能阻止它所对应的 _std::shared_ptr_ 去悬空。 

## 使用 _std::weak_ptr_ 来检查 _std::shared_ptr_ 是否悬空的方法

可以使用 _std::weak_ptr_ 来检查 _std::shared_ptr_ 是否悬空，共有两种方法：_lock_ 和 _throw_。

```C++
  auto spw =                            // after spw is constructed,
    std::make_shared<Widget>();         // the pointed-to Widget's
                                        // ref count (RC) is 1. (See
                                        // Item 21 for info on
                                        // std::make_shared.)

  std::weak_ptr<Widget> wpw(spw);       // wpw points to same Widget
                                        // as spw. RC remains 1
```

_lock_ 方法

```C++
  
  std::shared_ptr<Widget> spw1 = wpw.lock();      // if wpw's expired,
                                                  // spw1 is null
  if(spw1 != nullptr)
    ...
```  

_throw_ 方法

```C++
  std::shared_ptr<Widget> spw2(wpw);              // if wpw's expired,
                                                  // throw std::bad_weak_ptr
```

## _std::weak_ptr_ 的使用场景

* 缓存。在返回类型为 _std::shared_ptr_ 的工厂函数中存在缓存，缓存中所保存的指针需要能够探测这些指针何时才是悬空的，以便于在工厂函数的客户在使用完了工厂函数所返回的对象之后，可以将缓存中的所对应的指针进行销毁。此时在缓存中需要的是 _std::weak_ptr_。
* 观察者设计模式。这种设计模式的主要组件是 _subject_，是状态可能会改变的对象，和 _observer_，是状态改变发生时将要被通知的对象。在大部分的实现中，每一个 _subject_ 都包含一个数据成员，在这个数据成员中保存着指向所对应的 _observer_ 的指针。如果 _subject_ 确定其中的某个 _observer_ 已经被销毁了的话，那么 _subject_ 就不会尝试去访问这个 _observer_ 了。所以让每个 _subject_ 都持有一个 _std::weak_ptr_ 的 _container_，这个 _container_ 中的每个 _std::weak_ptr_ 都指向一个 _observer_。这样的话，_subject_ 就可以在使用指针之前先去确认指针是否是悬空的了。 
* 防止 _std::shared_ptr_ 互相嵌套。假设有 _A_、_B_ 和 _C_ 三个对象，其中 _A_ 和 _C_ 持有指向 _B_ 的 _std::shared_ptr_，如果存在一个 _B_ 到 _A_ 的指针的话，那么这个指针必须是 _std::weak_ptr_ 而不能是 _std::shared_ptr_，否则会造成 _std::shared_ptr_ 互相嵌套，发生资源泄露。

# _Item 21_ 首选 _std::make_unique_ 和 _std::make_shared_ 而不是直接使用 _new_

## _std::make_unique_ 和 _std::make_shared_ 可以消除代码重复

```C++
  auto upw1(std::make_unique<Widget>());          // with make func
  std::unique_ptr<Widget> upw2(new Widget);       // without make func
  
  auto spw1(std::make_shared<Widget>());          // with make func
  std::shared_ptr<Widget> spw2(new Widget);       // without make func
```

## _std::make_unique_ 和 _std::make_shared_ 可以避免资源泄露

直接使用 _new_ 容易资源泄露。

```C++
  processWidget(std::shared_ptr<Widget>(new Widget),        // potential
    computePriority());                                     // resource
                                                            // leak!
```  

当编译器按照下面这样的顺序来执行操作：  
* 执行 _new Widget_。
* 执行 _computePriority_。
* 执行 _std::shared_ptr_ 的构造函数。

 如果生成了这样的代码，并在运行时 _computePriority_ 产生了一个异常的话，那么动态分配的 _Widget_ 将会被泄露，因为它永远不会被存储到那个应该管理它的 _std::shared_ptr_ 中。

使用 _std::make_shared_ 可以避免资源泄露。

 ```C++
  processWidget(std::make_shared<Widget>(),       // no potential
                  computePriority());             // resource leak
 ```  

在运行时，_std::make_shared_ 或 _computePriority_ 都可以先被调用。如果 _std::make_shared_ 先被调用了的话，那么指向动态分配的 _Widget_ 的原始指针是可以在 _computePriority_ 被调用之前，就被安全存储到 _std::make_shared_ 所返回的 _std::shared_ptr_ 中的。如果 _computePriority_ 随后产生了一个异常的话，那么 _std::shared_ptr_ 的析构函数会销毁它拥有的 _Widget_。 如果 _computePriority_ 先被调用了，并产生了一个异常的话，那么 _std::make_shared_ 不会被执行，因此是不用担心动态分配的 _Widget_ 的。

## _std::make_shared_ 可以提高效率

直接使用 _new_ 需要两次内存分配。因为每个 _std::shared_ptr_ 都指向有一个 _control block_，而这个 _control block_ 的内存是在 _std::shared_ptr_ 的构造函数中被分配的，所以需要一次对象的内存分配，还需要一次 _control block_ 的内存分配。

使用 _std::make_shared_ 只需要一次内存分配。这是因为 _std::make_shared_ 分配了一块可以同时保存 _control block_ 和 _Widget_ 的内存，因为代码只有一次内存分配调用，所以提高了效率。使用 _std::make_shared_ 可以避免一些在 _control block_ 中所需要的 _bookkeeping information_，这潜在地减少了程序的 _memory footprint_ 总量。但是 _control block_ 还包含着 _weak count_，这个 _weak count_ 记录着有多少个 _std::weak_ptr_ 引用了这个 _control block_。只要有 _std::weak_ptr_ 引用着 _control block_，这个 _control block_ 就必须保持存在。只要这个 _control block_ 存在，包含着这个 _control block_ 的内存也就必须存在。所以 _std::make_shared_ 所分配的内存只有当引用着这个内存的最后一个 _std::shared_ptr_ 和最后一个 _std::weak_ptr_ 都被销毁后，才能被释放。如果对象是非常大的，并且此对象所对应的最后一个 _std::shared_ptr_ 和最后一个 _std::weak_ptr_ 之间的析构时间又是非常重要的话，那么在销毁这个对象和释放这个对象所占用的内存之间会有延迟，而直接使用 _new_ 则没有这个问题。

## _std::make_unique_ 和 _std::make_shared_ 不可以指定 _custom deleter_

_std::make_unique_ 和 _std::make_shared_ 不可以指定 _custom deleter_，只可以通过直接使用 _new_ 来完成。

```C++
auto widgetDeleter = [](Widget* pw) { … };

std::unique_ptr<Widget, decltype(widgetDeleter)>
    upw(new Widget, widgetDeleter);

std::shared_ptr<Widget> spw(new Widget, widgetDeleter);

``` 

## _std::make_unique_ 和 _std::make_shared_ 不可以使用 _braced initializer_

_std::make_unique_ 和 _std::make_shared_ 不可以使用 _braced initializer_，只可以通过直接使用 _new_ 或者使用 _auto = {}_ 来完成。

```C++
  auto upv = std::make_unique<std::vector<int>>{10, 20};  // error

  auto spv = std::make_shared<std::vector<int>>{10, 20};  // error
```  

```C++
  std::unique_ptr<std::vector<int>> upv(new std::vector<int>{10, 20});          // direct use of new

  std::shared_ptr<std::vector<int>> spv(new std::vector<int>{10, 20});          // direct use of new
``` 

```C++
  // create std::initializer_list
  auto initList = { 10, 20 };
  
  // create std::vector using std::initializer_list ctor
  auto spv = std::make_shared<std::vector<int>>(initList);                      // use _auto = {}_
```

## _std::make_shared_ 不可以用于那些有着私有版本的 _operator new_ 和 _operator delete_ 的类

一些类定义有私有版本的 _operator new_ 和 _operator delete_。这些函数的存在说明了：全局版本的 _operator new_ 和 _operator delete_ 对于这些类的对象是不合适的。类的私有版本的 _operator new_ 和 _operator delete_ 是被用于分配和释放特定大小的内存块的，但是 _std::allocate_shared_ 所需要的内存的大小是不等于动态分配的对象的大小的，而是要再加上 _control block_ 的大小的。所以使用 _make_ 函数去创建有着私有版本的 _operator new_ 和 _operator delete_ 的类的对象通常不是一个好主意。

## 禁止完美转发 _new expression_

```C++
  std::list<std::shared_ptr<Widget>> ptrs;
```   
```C++
  void killWidget(Widget* pWidget);
```  

```C++
  ptrs.push_back(std::shared_ptr<Widget>(new Widget, killWidget));
```  

```C++
  ptrs.push_back({ new Widget, killWidget });
```

不管哪种方式，一个临时的 _std::shared_ptr_ 将会在调用 _push_back_ 之前被构造。_push_back_ 的形参是 _std::shared_ptr_ 的引用，所以形参必须指向 _std::shared_ptr_。

考虑下面潜在的事件顺序：  
* 在上面的两个调用中，一个临时的 _std::shared_ptr&lt;Widget&gt;_ 对象是被构造出来以去持有 _new Widget_ 所生成的原始指针的。称这个对象为 _temp_。
* _push_back_ 按 _by-value_ 的形式持有 _temp_。在持有 _temp_ 的副本的 _list node_ 的分配期间，一个 _out-of-memory_ 异常抛出了。
* 当这个异常传播到 _push_back_ 外面时，_temp_ 是被销毁的。
做为唯一的 _std::shared_ptr_，它指向着它所管理的 _Widget_，它会自动释放所管理的 _Widget_，在这个场景中，是通过调用 _killWidget_。

尽管一个异常发生了，但是没有东西泄露：在 _push_back_ 中通过 _new Widget_ 所创建的 _Widget_ 是在被创建以去管理 _temp_ 的 _std::shared_ptr_ 的析构函数中进行释放的。

现在考虑：如果调用的是 _emplace_back_ 完美转发了 _new expression_ 而不是 _push_back_ 的话，那么会发生什么：  
```C++
  ptrs.emplace_back(new Widget, killWidget);
```  
* _new Widget_ 所生成的原始指针是被完美转发给 _emplace_back_ 的，并且在 _emplace_back_ 中将会分配一个 _list node_。这个分配失败，一个 _out-of-memory_ 异常被抛出。
*  当这个异常传播到 _push_back_ 外面时，唯一可以获取堆上的 _Widget_ 的原始指针就丢失了。这个 _Widget_ 和它所拥有的所有资源被泄露了。

所以，禁止完美转发 _new expression_，因为这会导致资源泄露。

# _Item 22_ 当使用 _Pimpl Idiom_ 时，在源文件中定义特殊成员函数

## _Pimpl Idiom_ 是通过减少类的客户和类的实现之间的编译依赖来缩短编译时间的

在头文件中，定义指针类型的对象时，可以只有指针类型的声明而没有指针类型的定义，这样的话就不需要包含指针类型所对应的头文件了，_Pimpl Idiom_ 就是在头文件使用已经被声明，但没有被定义的指针，这样就减少了类的客户和类的实现之间的编译依赖，缩短了编译时间。

## 使用 _std::unique_ptr_ 实现 _Pimpl Idiom_ 时，需要特殊处理

使用 _std::unique_ptr_ 实现 _Pimpl Idiom_ 时，必须要在头文件中声明特殊成员函数，然后在源文件中实现特殊成员函数，并且成员函数必须要在 _Pimpl_ 的定义后。

# _Item 23_ 理解 _std::move_ 和 _std::forward_

## 函数所返回的右值引用肯定是右值

```C++
  template<typename T>                            // C++14; still in
  decltype(auto) move(T&& param)                  // namespace std
  {
    using ReturnType = remove_reference_t<T>&&;
    return static_cast<ReturnType>(param);
  }
```  

## _std::move_ 是转换而不是移动

_std::move_ 的形参的类型是 _univeral reference_，这表示它的形参可以引用左值或右值，也就是说它的形参的类型可以是左值引用类型或右值引用类型。_std::move_ 的返回类型是右值引用类型，这表示它的返回值肯定是右值。_std::move_ 首先会在函数中将形参的类型转换为右值引用类型，注意只是将形参类型的 **_值类型_** 部分做了转换，形参类型的其他部分仍然会保留，比如：_const_ 会一直保留，然后再返回这个形参，因为函数所返回的右值引用肯定是右值，所以就是将实参转换为了右值。应用 _std::move_ 到一个对象上是在告诉编译器这个对象是可以被移动的。 

## _std::forward_ 是转换而不是转发

_std::forward_ 的形参的类型是 _univeral reference_，这表示它的形参可以引用左值或右值，也就是说它的形参的类型可以是左值引用类型或右值引用类型。_std::forward_ 的返回类型是依赖于模板类型实参的，可以是左值引用类型或右值引用类型，这表示它的返回值可以是左值或右值。_std::forward_ 首先会在函数中将形参的类型根据模板类型实参来转换为左值引用类型或右值引用类型，注意只是将形参类型的 **_值类型_** 部分做了转换，形参类型的其他部分仍然会保留，比如：_const_ 会一直保留，然后再返回这个形参，因为函数所返回的可以是左值引用类型或右值引用类型，所以就是将实参进行了转发。 

## _std::move_ 并不能保证转换后的结果是可以被移动的，禁止将可以被移动的对象声明为 _const_

```C++
  class Annotation {
  public:
    explicit Annotation(const std::string text)
    : value(std::move(text))                      // "move" text into value; this code
    { … }                                         // doesn't do what it seems to!
    
    …
  
  private:
    std::string value;
  };
```   
这个代码可以编译。这个代码可以链接。这个代码可以运行。这个代码也将数据成员 _value_ 的内容设置为了 _text_ 的内容。这个代码和你所设想的完美实现间的唯一的区别是：_text_ 不是被移动到 _value_ 中的，而是被拷贝到 _value_ 中的。是的，是通过 _std::move_ 将 _text_ 转换为了一个右值，但是 _text_ 是被声明为 _const std::string_ 的，所以 _text_ 在转换之前就是一个 _const std::string_ 类型的左值了，相应地，在转换后就是一个 _const std::string_ 类型的右值了，在这个过程中， _constness_ 一直保留。

```C++
  class string {                        // std::string is actually a
  public:                               // typedef for std::basic_string<char>
    …
    string(const string& rhs);          // copy ctor
    string(string&& rhs);               // move ctor
    …
  };
```  
在 _Annotation_ 的构造函数的成员初始值列表中，_std::move(text)_ 的结果是一个 _const std::string_ 的右值。这个右值不可以被传递到 _std::string_ 的移动构造函数中，因为 _std::string_ 的移动构造函数持有的是 _non-const std::string_ 的右值引用。然而，这个右值是可以被传递到 _std::string_ 的 _copy constructor_ 中的，因为允许 _lvalue-reference-to-const_ 去绑定一个 _const_ 右值。因此 _Annotation_ 的成员初始化执行的会是 _std::string_ 的 _copy constructor_，尽管 _text_ 已经被转换为了一个右值。这样的行为对于维护 _const-correctness_ 是必不可少的。将值移出对象之外通常会修改这个对象，所以，语言不允许将 _const_ 对象传递给那些像移动构造函数一样的可能会修改它们的函数。

## _lvalue-reference-to-const_ 可以绑定左值和右值

因为 _lvalue-reference-to-const_ 的 _const_ ，所以可以绑定左值或右值。

# _Item 24_ 区分 _universal reference_ 和右值引用

## _&&_ 是 _universal reference_ 的场景 

如果函数模板的形参的类型是严格为 _T&&_ 且 _T_ 会被推导的话，那么 _&&_ 是 _universal reference_。

```C++
  template<typename T>
  void f(T&& param);          // param is a universal reference
``` 

如果一个对象是严格使用 _auto&&_ 所声明的话，那么 _&&_ 是 _universal reference_。

```C++
  auto&& var2 = var1;         // var2 is a universal reference
```  

## _&&_ 是右值引用的场景

如果函数模板的形参的类型不是严格为 _T&&_ 或 _T_ 不会被推导的话，那么 _&&_ 是右值引用。

```C++
  template<typename T>
  void f(std::vector<T>&& param);                           // param is an rvalue reference
```

```C++
  template<typename T>
  void f(const T&& param);                                  // param is an rvalue reference
```  

```C++
  template<class T, class Allocator = allocator<T>>       
  class vector {                                          
  public:
    void push_back(T&& x);                                  // x is an rvalue reference, because
                                                            // there’s no type deduction in this case. 
                                                            // That’s because push_back can’t exist 
                                                            // without a particular vector instantiation 
                                                            // for it to be part of, and the type of that 
                                                            // instantiation
                                                            // fully determines the declaration for push_back.
    …
  };
``` 

如果一个对象不是严格使用 _auto&&_ 所声明的话，那么 _&&_ 是右值引用。

```C++
  const auto&& var2 = var1;                                 // var2 is a rvalue reference
```  

## _universal references_ 可以是左值引用或右值引用

如果 _universal references_ 是被右值所初始化的话，那么 _universal references_ 对应的是右值引用。如果 _universal references_ 是被左值所初始化的话，那么 _universal references_ 对应的是左值引用。

# _Item 25_ _std::move_ 用于右值引用 _std::forward_ 用于 _univeral reference_

## _std::move_ 用于右值引用，而不能用于 _univeral reference_ 

因为 _universal references_ 可以是左值引用或右值引用。右值引用表示所绑定的对象是可以被移动的，可以被修改的，而左值引用并不表示所绑定的对象是可以被移动的，可以被修改的。所以将 _std::move_ 用于 _univeral reference_ 可能会导致所对应的左值对象被意外修改。

## _std::forward_ 用于 _univeral reference_，而不能用于右值引用

虽然在右值引用上使用 _std::forward_ 可以表现出正确的行为，但是代码是冗长的、易错的和不符合语言习惯的，所以禁止在右值引用上使用 _std::forward_。

## 只能在最后一次使用完右值引用和 _universal reference_ 后，再去使用 _std::move_ 或 _std::forward_

```C++
  template<typename T>                  // text is
  void setSignText(T&& text)            // univ. reference
  {
    sign.setText(text);                 // use text, but
                                        // don't modify it
    auto now =                          // get current time
    std::chrono::system_clock::now();

    signHistory.add(now,
      std::forward<T>(text));           // conditionally cast
  }                                     // text to rvalue
```
此处，我们想要确保 _text_ 的值不会被 _sign.setText_ 所改变，因为还要在调用 _signHistory_ 时使用 _text_，因此只在最后使用 _universal reference_ 时才使用 _std::forward_。

## 对于 _return-by-value_ 的函数所返回的右值引用和 _universal reference_，可以使用 _std::move_ 或 _std::forward_ 来提高效率

```C++
  template<typename T> 
  Fraction                              // by-value return
  reduceAndCopy(T&& frac)               // universal reference param
  {
    frac.reduce();
    return std::forward<T>(frac);       // move rvalue into return
  }                                     // value, copy lvalue
```

如果实参是个右值的话，那么实参的值应该被移动到返回值中，这样可以避免执行拷贝所产生的成本，但是如果实参是个左值的话，那么实际的拷贝必须产生。因此：如果省略了调用 _std::forward_ 的话，那么 _frac_ 将会被无条件地拷贝到 _reduceAndCopy_ 的返回值中。

## _RVO_ _return value optimization_

_RVO_ 所需要的条件：
* 函数是 _return-by-value_ 的函数。
* 函数返回的是局部对象。
* 函数所返回的局部对象的类型和函数的返回类型一致。
* 编译器知道在为函数的返回值所分配的内存中构建那个局部对象。

_RVO_ 的优化：当满足 _RVO_ 的条件时，编译器可以直接在为函数的返回值所分配的内存中构建所要返回的局部变量，以去避免再一次拷贝所要返回的局部变量。

```C++
  Widget makeWidget()         
  {
  
    Widget w;
    …
    return w;                 
}
```

这个代码满足 _RVO_ 所需要的条件。

```C++
  Widget makeWidget()        
  {
    Widget w;
    …
    return std::move(w);      
  }                           
```

因为这个代码返回的不是局部对象 _w_，而是 _w_ 的右值引用，所以不满足 _RVO_ 所需要的条件。


```C++
  Widget makeWidget(Widget w)           // by-value parameter of same
  {                                     // type as function's return
    …
    return w;
  }
```

因为 _by-value_ 的形参也是局部对象，但是编译器不知道应该在为函数的返回值所分配的内存中构建哪个局部对象，所以不满足 _RVO_ 所需要的条件。

```C++
  Widget makeWidget() 
  {      
    Widget w1, w2;
    ...
    if(...)
      Widget w1;
    else
      return w2;                
  }
```

因为编译器不知道应该在为函数的返回值所分配的内存中构建哪个局部对象，所以不满足 _RVO_ 所需要的条件。

## 禁止在 _return-by-value_ 的函数所返回的局部对象上使用 _std::move_

对于是要返回局部对象的 _return-by-value_ 的函数，当不满足 _RVO_ 所需要的条件时，此时编译器会隐式地应用 _std::move_ 到所返回的局部对象上。因此，当在 _return-by-value_ 的函数所要返回的局部对象上使用了 _std::move_ 时，如果函数原先是满足 _RVO_ 所需要的条件的话，那么现在破坏了，因为所返回的不是局部对象，而是右值引用了；如果函数原先是不满足 _RVO_ 所需要的条件的话，那么编译器也是会隐式地应用 _std::move_ 到所返回的局部对象上的。所以禁止在 _return-by-value_ 的函数所返回的局部对象上使用 _std::move_，可能破坏 _ROV_ 优化。

# _Item 26_ 避免重载 _univeral reference_

## 重载 _universal reference_ 几乎总是会导致 _universal reference_ 的重载函数在不期望被调用的情况下却被调用到

因为重载 _universal reference_ 几乎总是会导致 _universal reference_ 的重载函数在不期望被调用的情况下却被调用到，所以避免重载 _univeral reference_。

```C++
  class Person {
  public:
    explicit Person(Person& n)          // instantiated from
    : name(std::forward<Person&>(n)) {} // perfect-forwarding
                                        // template

    explicit Person(int idx);           // as before

    Person(const Person& rhs);          // copy ctor
    …                                   // (compiler-generated)
};
```  

传递 _int_ 之外的 _integral_ 类型，比如：_std::size_t_、_short_ 和 _long_ 等，将会调用的是 _universal reference_ 的重载函数而不是 _int_ 的重载函数，这会导致编译失败。

# _Item 27_ 熟悉重载 _univeral reference_ 的替代方法

## _tag dispatch_

_dispatch function_ 持有没被限制的 _univeral reference_ 形参，它不是重载的。_implementation function_ 持有 _univeral reference_ 形参，它们是重载的，但是重载函数的重载决议不只依赖于 _univeral reference_ 形参，还依赖于一个 _tag_ 形参，_tag_ 用来确定哪个重载函数会被调用到。

```C++
  template<typename T>
  void logAndAdd(T&& name)
  {
    logAndAddImpl(
    std::forward<T>(name),
    std::is_integral<typename std::remove_reference<T>::type>()
    );
  }
```

```C++
  template<typename T>                                      // non-integral
  void logAndAddImpl(T&& name, std::false_type)             // argument:
  {                                                         // add it to
    auto now = std::chrono::system_clock::now();            // global data
    log(now, "logAndAdd");                                  // structure
    names.emplace(std::forward<T>(name));
  } 
``` 

```C++
  std::string nameFromIdx(int idx);                         // as in Item 26

  void logAndAddImpl(int idx, std::true_type)               // integral
  {                                                         // argument: look
      logAndAdd(nameFromIdx(idx));                          // up name and
  }                                                         // call logAndAdd
                                                            // with it
``` 

## _std::enable_if_

_std::enable_if_ 给了你一种方法可以迫使编译器表现地就好像某个特定的模板不存在一样。这些模板被认为是无效的。默认情况下，全部的模板都是有效的。但是只有当满足了 _std::enable_if_ 所指定的条件时，使用了 _std::enable_if_ 的模板才是有效的。

```C++
class Person {
  public:
    template<
      typename T,
      typename = std::enable_if_t<
        !std::is_base_of<Person, std::decay_t<T>>::value
        &&
        !std::is_integral<std::remove_reference_t<T>>::value
      >
    > 
    explicit Person(T&& n)              // ctor for std::strings and
    : name(std::forward<T>(n))          // args convertible to
    { … }                               // std::strings

    explicit Person(int idx)            // ctor for integral args
    : name(nameFromIdx(idx))
    { … }

    
    …  
                                        // copy and move ctors, etc.
  private:
    std::string name;
};
```

# _Item 28_ 理解引用折叠

## 引用折叠

因为共有两种引用：左值引用和右值引用，所以有四种可能的 _reference-reference_ 组合：_lvalue to lvalue_、_lvalue to rvalue_、_rvalue to lvalue_ 和 _rvalue to rvalue_。如果引用的引用是在所允许的环境下所产生的话，比如：在模板实例化过程中，那么按照下面的规则来将引用的引用折叠为一个单引用：如果任意一个引用是左值引用的话，那么组合的结果就是左值引用。否则，也就是如果全部都是右值引用的话，那么组合的结果是右值引用。

# _Item 29_ 假设 _move operation_ 是不存在的、成本大的和不可使用的

## 移动并不一定比拷贝快

很多的 _string_ 的实现都利用了 _small string optimization_ _SSO_。当使用了 _SSO_ 时，**_小_** _string_，比如：不超过 _15_ 个字符容量的 _string_，是被存储在 _std::string_ 对象中的，不会使用堆分配的内存空间。对于使用了基于 _SSO_ 的实现的小 _string_ 来说，移动并不会比拷贝还快，因为此时会一个个地去移动字符，而不是去复制 _string_ 所对应的指针。

## 标准 _container_ 与有 _STL_ 接口的内建数组的区别

标准 _container_ 都是将它们的内容存储在堆上的。在概念上，这些 _container_ 类型的对象持有的只是一个指针，是将这个指针做为的数据成员的，这个指针指向是存储着所对应的 _container_ 的内容的堆内存。这个实现是非常复杂的，但是对于现在的分析来说并不重要。这个指针的存在使得可以在恒定的时间内来移动整个 _container_ 的内容：将指向 _container_ 的内容的指针从源 _container_ 拷贝到目标 _container_ 中，并将源 _container_ 的指针设置为空。

有 _STL_ 接口的内建数组 _std::array_ 没有这样的一个指针，因为 _std::array_ 的内容是被直接存储在 _std::array_ 对象中的。移动或拷贝 _std::array_ 需要移动或拷贝其中的每一个元素。

# _Item 30_ 熟悉完美转发失败的场景

## _braced initializer_

因为在模板函数的 _univeral reference_ 场景中，编译器不会将 _braced initializer_ 推到为 _std::initializer_list_，所以也就不能转发 _braced initializer_。解决方法是：先使用 _auto_ 来声明一个局部变量，再将这个局部变量转递给转发函数。

```C++
  template<typename T>
  void fwd(T&& param)                   // accept any argument
  {
    f(std::forward<T>(param));          // forward it to f
  }
```  

```C++
  fwd({ 1, 2, 3 });                     // error! doesn't compile
``` 

```C++
  auto il = { 1, 2, 3 };                // il's type deduced to be
                                        // std::initializer_list<int>
  
  fwd(il);                              // fine, perfect-forwards il to f
```


## _0_ 或 _NULL_ 来做为空指针

因为当将 _0_ 或 _NULL_ 做为空指针来传递给模板时，所推导出的是 _integral_ 类型，而不是指针类型，所以 _0_ 和 _NULL_ 都不可以做为空指针来被完美转发。解决方法是：传递 _nullptr_ 而不是 _0_ 或 _NULL_。

## 只有声明的 _integral static const_ 的数据成员

因为在通常情况下，不需要在类中定义 _integral static const_ 的数据成员，声明就足够了，也就是不需要为它们分配内存；又因为在编译器所生成的代码中，引用通常是被视为指针的，因此存在 _univeral reference_ 就必须存在所对应的指针所指向的内存空间；所以 _univeral reference_ 不能指向 _integral static const_ 的数据成员，完美转发会失败。解决方法是：对 _integral static const_ 的数据成员进行定义。

## 重载的函数名和模板名

因为重载的函数名和模板名不是类型，只是名字而已，所以不会有类型推导，也就不会发生完美转发了。解决方法是：指定具体的重载函数和模板实例化。

```C++
  template<typename T>
  T workOnVal(T param)                  // template for processing values
  { … }
  
  fwd(workOnVal);                       // error! which workOnVal
                                        // instantiation?
```  

```C++
  using ProcessFuncType =                         // make typedef;
  int (*)(int);                                   // see Item 9

  ProcessFuncType processValPtr = processVal;     // specify needed
                                                  // signature for
                                                  // processVal
  fwd(processValPtr);                             // fine
  
  fwd(static_cast<ProcessFuncType>(workOnVal));   // also fine
```

## _bitfield_

因为在编译器所生成的代码中，引用通常是被视为指针的，没有方法去创建指向任意位的指针，也就没有方法去创建绑定任意位的引用，所以 _univeral reference_ 不能指向 _bitfield_，完美转发会失败。解决方法是：让 _univeral reference_ 指向 _bitfield_ 的副本。

```C++
  struct IPv4Header {
    std::uint32_t version:4,
                  IHL:4,
                  DSCP:6,
                  ECN:2,
                  totalLength:16;
    …
  };
```   
 
```C++
  fwd(h.totalLength);                   // error!
```  

```C++
  // copy bitfield value; see Item 6 for info on init. form
  auto length = static_cast<std::uint16_t>(h.totalLength);
  
  fwd(length);                          // forward the copy
```

# _Item 31_ 避免默认捕获模式

## 捕获的范围

只能捕获到那些在创建出 _lambda_ 的作用域中是可见的 _non-static_ 局部变量和形参。

## 默认捕获会隐式捕获 _this_ 指针，容易产生悬空的 _this_ 指针

当是默认捕获时，是可以在 _lambda_ 中使用类的 _non-static_ 成员的，因为默认捕获会隐式捕获 _this_ 指针，注意是隐式捕获 _this_ 指针，而不是隐式捕获类的 _non-static_ 成员。

```C++
  class Widget {
  public:
    …                                                       // ctors, etc.
    void addFilter() const;                                 // add an entry to filters

  private:
    int divisor;                                            // used in Widget's filter
  };
```  
 
```C++
  void Widget::addFilter() const
  {
    filters.emplace_back(
      [=](int value) { return value % divisor == 0; }       // fine! implicit use of this pointer
    );                                  
  }
```  

```C++
  void Widget::addFilter() const
  {
    filters.emplace_back(                                   // error!
        [](int value) { return value % divisor == 0; }      // divisor
    );                                                      // not
  }                                                         // available
``` 

```C++
  using FilterContainer =                                   // as before
    std::vector<std::function<bool(int)>>;

  FilterContainer filters;                                  // as before

  void doSomeWork()
  {
    auto pw =                                               // create Widget; see
      std::make_unique<Widget>();                           // Item 21 for
                                                            // std::make_unique
    
    pw->addFilter();                                        // add filter that uses
                                                            // Widget::divisor
    …
  }                                                         // destroy Widget; filters
                                                            // now holds dangling pointer!
```  

## _[&]_ 没有显示指明让人印象深刻

比起 _[&]_ 所表达的 **_确保没有任何悬空_** 的警告，显式写出名字会更让人印象深刻。

```C++
  using FilterContainer =                                   // see Item 9 for
      std::vector<std::function<bool(int)>>;                // "using", Item 2
                                                            // for std::function

  FilterContainer filters;                                  // filtering funcs
```   

```C++
  void addDivisorFilter()
  {
    auto calc1 = computeSomeValue1();
    auto calc2 = computeSomeValue2();

    auto divisor = computeDivisor(calc1, calc2);

    filters.emplace_back(                                   // danger!
      [&](int value) { return value % divisor == 0; }       // ref to
    );                                                      // divisor
  }                                                         // will
                                                            // dangle!
```   

## _[=]_ 会误导性地暗示 _lambda_ 是 _self-contained_ 的

_[=]_ 会误导性地暗示 _lambda_ 是 _self-contained_ 的，_lambda_ 所对应的 _closure_ 之外的数据的改动是不会影响到这个 _closure_ 本身的。

# _Item 32_ 使用初始化捕获来将对象移动到 _closure_ 中

## 初始化捕获

使用初始化捕获可以指定 _lambda_ 所生成的 _closure class_ 中的数据成员的名字和初始化数据成员的表达式。

## 使用初始化捕获来将对象移动到 _closure_ 中

下面展示了如何可以使用初始化捕获来将 _std::unique_ptr_ 移动到 _closure_ 中：  
```C++
  class Widget {                                  // some useful type
  public:
    …

    bool isValidated() const;
    bool isProcessed() const;
    bool isArchived() const;

  private:
    …
  };

  auto pw = std::make_unique<Widget>();           // create Widget; see
                                                  // Item 21 for info on
                                                  // std::make_unique

  …                                               // configure *pw

  auto func = [pw = std::move(pw)]                // init data mbr
              { return pw->isValidated()          // in closure w/
                      && pw->isArchived(); };     // std::move(pw)
```

# _Item 33_ 在 _auto&&_ 形参上使用 _decltype_ 来进行完美转发

```C++
  auto f =
    [](auto&& param)
    {
      return
        func(normalize(std::forward<decltype(param)>(param)));
    };
```

# _Item 34_ 首选 _lambda_ 而不是 _std::bind_

_lambda_ 比起 _std::bind_ 是更易读的、更易表达的并且效率可能也是更高的。

# _Item 35_ 首选 _task-based_ 编程而不是 _thread-based_ 编程

## _task-based_ 和 _thread-based_ 编程

* _task-based_ 编程

```C++
  int doAsyncWork();

  auto fut = std::async(doAsyncWork);   // "fut" for "future"
``` 

* _thread-based_ 编程

```C++
  int doAsyncWork();
  
  std::thread t(doAsyncWork);
``` 

##  _task-based_ 编程可以直接获取异步运行的函数的返回值

 _task-based_ 编程提供了直接的方法来获取异步运行的函数的返回值，而 _thread::based_ 编程则没有。 

## _task-based_ 编程可以避免线程耗尽问题和线程 _oversubscription_ 问题

软件线程是一种有限资源。当你尝试创建的软件线程多于系统可以提供的软件线程时，_std::system_error_ 异常会被抛出，就会发生线程耗尽问题。当 _ready-to-run_ 的软件线程，比如：非阻塞的软件线程，多于硬件线程时，就会发生线程 _oversubscription_ 问题。因为当发生这两个问题时，使用了 _default launch policy_ 的 _std::async_ 可以允许调度器将所指定的函数安排到那个需要这个所指定的函数的结果的线程上，而不会再去创建新的软件线程，所以 _task-based_ 编程可以通过使用 _default launch policy_ 的 _std::async_ 来避免这两个问题。但是注意此时会带来线程负载的问题。

_state-of-the-art_ 线程调度器利用 _system-wide_ 线程池从而避免了 _oversubscription_，它通过 _work-stealing_ 算法提升了硬件核心之间的负载均衡。_C++_ 标准不需要使用线程池或 _work-stealing_，老实说，_C++11_ 的并发规范的一些技术特色使得利用这个技术变得很困难，比我们想象的要困难。然而，一些开发商在它们的标准库实现中利用了这个技术，我们有理由期待在这方面会继续取得进展。如果你在你的并发开发中采用了 _task-based_ 方法的话，那么当这个技术变得更加普遍的时候，你就会自动获得了这个技术的益处了。另一方面，如果你是直接使用了 _std::thread_ 的话，你就要自己承担处理线程耗尽、_oversubscription_ 和负载均衡的责任了，更不用说你的这些问题的解决方法如何与运行在同一台机器上的其他进程中的程序中所实现的方案所协调。


# _Item 36_ 如果异步是必须要的话，那么指明 _std::launch::async_

## _std::launch::async_

_std::launch::async_ 表示所对应的函数必须异步运行，必须运行在一个不同的线程上。这就可能会遇到程耗尽问题和线程 _oversubscription_ 问题。

## _std::launch::deferred_

_std::launch::deferred_ 表示：只有当 _std::async_ 所返回的 _future_ 的 _get_ 或 _wait_ 被调用时，所对应的函数才会同步运行，必须运行在相同的线程上。这不会遇到程耗尽问题和线程 _oversubscription_ 问题。

## _std::launch::async | std::launch::deferred_

_std::async_ 的 _default launch policy_，就是没有显式指定时所会使用的那个 _launch policy_，不是 _std::launch::async_ 或 _std::launch::deferred_，而是 _std::launch::async | std::launch::deferred_。

```C++
  auto fut1 = std::async(f);                      // run f using
                                                  // default launch
                                                  // policy
  
  auto fut2 = std::async(std::launch::async |     // run f either
                          std::launch::deferred,  // async or
                          f);                     // deferred
``` 

当发生线程耗尽问题或线程 _oversubscription_ 问题时，使用了 _default launch policy_ 或 _std::launch::async | std::launch::deferred_ 的 _std::async_ 可以允许调度器将所指定的函数安排到那个需要这个所指定的函数的结果的线程上，而不会再去创建新的软件线程，此时调用 _get_ 或 _wait_ 就不是在并发调用所指定的函数，而如果没有调用 _get_ 或 _wait_ 的话，那么所指定的函数将永远不会被运行。

当没有发生线程耗尽问题或线程 _oversubscription_ 问题时，使用了 _default launch policy_ 或使用了 _std::launch::async | std::launch::deferred_ 的 _std::async_ 可以允许调度器将所指定的函数安排到新创建的软件线程上。

## 禁止使用 _std::launch::async | std::launch::deferred_

* 使用 _std::launch::async | std::launch::deferred_ 时，不确定所指定函数访问的是哪个线程的变量，因为此时可能是在 **_旧线程中_** ，也有可能是在 **_新线程中_**，如果所指定的函数会访问线程中的变量，无法确定访问的是哪个线程中的变量。
* 使用 _std::launch::async | std::launch::deferred_ 时，所指定的函数永远不会被执行到，因为此时如果确实发生了而你不知道发生线程耗尽问题或线程 _oversubscription_ 问题时，所指定的函数将永远不会被执行到，简直是致命 _bug_。
* 使用 _std::launch::async | std::launch::deferred_ 时，所指定的函数永远不会被执行到，因为此时如果确实发生了而你不知道发生线程耗尽问题或线程 _oversubscription_ 问题时， 所对应的 _wait_ 调用所对应的循环将是死循环，简直是致命 _bug_。


  ```C++
  using namespace std::literals;        // for C++14 duration
                                        // suffixes; see Item 34

  void f()                              // f sleeps for 1 second,
  {                                     // then returns
    std::this_thread::sleep_for(1s);
  }
  

  auto fut = std::async(f);             // run f asynchronously
                                        // (conceptually)

  while (fut.wait_for(100ms) !=         // loop until f has
  std::future_status::ready)            // finished running...
  {                                     // which may never happen!
    …
  }

  ``` 

# _Item 37_ 使 _std::thread_ 在所有路径上都为 _unjoinable_

## _unjoinable_ _std::thread_ 和 _joinable_ _std::thread_

_unjoinable_ _std::thread_：
* _default-constructed_ _std::thread_。这些 _std::thread_ 没有可以执行的函数，因此没有所对应的底层执行的线程。
* 已经被移动走的 _std::thread_ 对象。移动走的结果是某个 _std::thread_ 过去所对应的底层执行的线程现在对应上一个不同的 _std::thread_ 了。
* 已经被 _join_ 的 _std::thread_。在 _join_ 后，这个 _std::thread_ 不再对应于那个已经结束运行的底层执行的线程了。
* 已经被 _detach_ 的 _std::thread_。_detach_ 会断开某个 _std::thread_ 对象和这个 _std::thread_ 对象所对应的底层执行的线程之间的联系。 

_joinable_ _std::thread_ ：不是 _unjoinable_ 的  _std::thread_，_joinable_ _std::thread_ 对应的是正在运行或可以运行的底层异步执行的线程。例如：被阻塞的或正在等待被调度的底层线程所对应的 _std::thread_ 就是 _joinable_ _std::thread_。已经运行至结束的底层线程所对应的 _std::thread_ 对象也是 _joinable_ _std::thread_。

## 调用 _unjoinable_ _std::thread_ 的 _join_ 或 _detach_ 会产生 _undefined behavior_ 

调用 _unjoinable_ _std::thread_ 的 _join_ 或 _detach_ 会产生 _undefined behavior_，只能调用 _joinable_ _std::thread_ 的 _join_ 或 _detach_。

## 调用 _joinable_ _std::thread_ 的析构函数会导致程序终止

调用 _joinable_ _std::thread_ 的析构函数会导致程序终止，而调用 _unjoinable_ _std::thread_ 的析构函数被调用不会导致程序终止。

```C++
  constexpr auto tenMillion = 10000000;           // see Item 15
                                                  // for constexpr
  
  bool doWork(std::function<bool(int)> filter,    // returns whether
  int maxVal = tenMillion)                        // computation was
  {                                               // performed; see
                                                  // Item 2 for
                                                  // std::function

    std::vector<int> goodVals;                    // values that
                                                  // satisfy filter

    std::thread t([&filter, maxVal, &goodVals]    // populate
                  {                               // goodVals
                    for (auto i = 0; i <= maxVal; ++i)
                    { if (filter(i)) goodVals.push_back(i); }
                  });

    auto nh = t.native_handle();                  // use t's native
    …                                             // handle to set
                                                  // t's priority
                                                  
    if (conditionsAreSatisfied()) {
      t.join();                                   // let t finish
      performComputation(goodVals);
      return true;                                // computation was
    }                                             // performed

      return false;                               // computation was
  }                                               // not performed
```  

为什么要设计成调用 _joinable_ _std::thread_ 的析构函数会导致程序终止呢？因为隐式 _join_ 和隐式 _detach_ 都是不合适的：  
* 隐式 _join_。在这个场景下，_std::thread_ 的析构函数会等待它的底层异步运行的线程完成。这听起来是合理的，但是这可能会导致非常难以追踪的性能异常。例如：如果 _conditionsAreSatisfied()_ 已经返回了 _false_ 而  _doWork_ 还在等待所有的值在应用到它的 _filter_ 的话，那么就不合理了。
* 隐式 _detach_。在这个场景下， _detach_ 会断开某个 _std::thread_ 对象和这个 _std::thread_ 对象所对应的底层执行的线程之间的联系。底层线程会继续运行。这个方法听起来和 _join_ 方法一样合理，但是它可以导致的调试问题是更糟糕的。例如：在 _doWork_ 中，_goodVals_ 是按 _by-reference_ 的方式所捕捉的局部变量。这个 _goodVals_ 是在 _lambda_ 中通过调用 _push_back_ 被更改的。假定在这个 _lambda_ 异步运行时 _conditionsAreSatisfied()_ 返回了 _false_。那么在这个场景下，_doWork_ 将会结束，它的局部变量，包括 _goodVals_，都将会被销毁。_doWork_ 的 _stack frame_ 会被弹出，_doWork_ 的线程将从 _doWork_ 的调用点继续执行。这个调用点后的语句在某个时间点会调用其他的函数调用，至少会有一个函数可能最终会使用到那些曾经是被 _doWork_ 的 _stack frame_ 所占用的一部分或全部内存。让我们把这个函数称为 _f_。在 _f_ 运行时，_doWork_ 所创建的 _lambda_ 也将继续异步运行。这个 _lambda_ 可能会在过去是存放 _goodVals_ 而现在是在 _f_ 的 _stack frame_ 中了的栈内存上调用 _push_back_。这个调用会更改过去是存放 _goodVals_ 的内存，这意味着：从 _f_ 的角度看，它的 _stack frame_ 中的内存的内容可能会被莫名其妙的改变。想象下调式这个的乐趣。

_Standardization Committee_ 认为销毁 _joinable_ _std::thread_ 是非常可怕的，所以就禁止了隐式 _join_ 和隐式 _detach_ 方法，而是指定销毁 _joinable_ _std::thread_ 会导致程序终止。

## _RAII_

_RAII_ **_Resource Acquisition Is Initialization_** 指的是在构造函数中获取资源，并将必须要执行的操作放到析构函数中。比如：_std::fstream_ 对象，它们的析构函数会关闭它们所对应的文件。

## 通过 _RAII_ 来使 _std::thread_ 在所有路径上都为 _unjoinable_

因为 _joinable_ _std::thread_ 的析构函数被调用会导致程序终止，所以应该使 _std::thread_ 在所有路径上都为 _unjoinable_，这个操作是必须要执行的，所以应该将这个必须要执行的操作放到析构函数中，所以应该通过 _RAII_ 来使 _std::thread_ 在所有路径上都为 _unjoinable_。


```C++
  class ThreadRAII {
  public:
    enum class DtorAction { join, detach };       // see Item 10 for
                                                  // enum class info
    
    ThreadRAII(std::thread&& t, DtorAction a)     // in dtor, take
    : action(a), t(std::move(t)) {}               // action a on t
    
    ~ThreadRAII()
    {                                             // see below for
      if (t.joinable()) {                         // joinability test
        if (action == DtorAction::join) {
          t.join();
        } else {
          t.detach();
        }

      }
    }

    std::thread& get() { return t; } // see below
    
    private:
      DtorAction action;
      std::thread t;
    };
``` 

```C++
  bool doWork(std::function<bool(int)> filter,    // as before
              int maxVal = tenMillion)
  {
    std::vector<int> goodVals;                    // as before
    
    ThreadRAII t( // use RAII object
      std::thread([&filter, maxVal, &goodVals]
                  {
                  for (auto i = 0; i <= maxVal; ++i)
                  { if (filter(i)) goodVals.push_back(i); }
                  }),
                  ThreadRAII::DtorAction::join    // RAII action
    );

    auto nh = t.get().native_handle();
    …

    if (conditionsAreSatisfied()) {
      t.get().join();
      performComputation(goodVals);
      return true;
    }

    return false;
  }
```  

## 在数据成员的列表最后再去声明 _std::thread_ 对象

在数据成员的列表最后再去声明 _std::thread_ 对象。这保证了：在 _std::thread_ 对象被构造出时，所有处在它们之前的数据成员都已经被初始化过了，因此这些数据也可以被那些 _std::thread_ 数据成员所对应的异步运行的线程所访问了。

# _Item 38_ 注意各种各样的线程 _handle_ 的析构函数

##  _future_ 的析构函数的行为

* 指向着通过 _async_ 所创建的 _non-deferred task_ 所对应的 _shared state_ 的最后一个 _future_ 的析构函数会被阻塞，直到所对应的 _task_ 结束。本质上，这个 _future_ 的析构函数会在那个运行异步执行的 _task_ 的线程上执行隐式 _join_。
* 剩下的其他 _future_ 的析构函数都只会销毁所对应的 _future_ 对象。对于异步运行的线程，这相当于在底层线程上执行了隐式 _detach_。对于最后一个 _future_ 所对应的 _deferred task_，这意味着这个 _deferred task_ 永远不会运行。

```C++
  int calcValue();                      // func to run
  
  std::packaged_task<int()>             // wrap calcValue so it
  pt(calcValue);                        // can run asynchronously
  
  auto fut = pt.get_future();           // get future for pt
```  
因为 _fut_ 不是通过 _async_ 所创建的，所以它不会指向调用 _std::async_ 所创建的 _shared state_，所以它的析构函数不会被阻塞。

# _Item 39_ 对于 _one-shot_ 事件通信考虑 _void future_ 

## _one-shot_ 事件通信

_one-shot_ 事件通信是一个 _task_ 只需要告诉另一个异步运行的 _task_ 一次发生了一个特定的事件。

## _condition variable_ 方法

```C++
  std::condition_variable cv;           // condvar for event
  
  std::mutex m;                         // mutex for use with cv
```  
_reacting task_ 

```C++
  …                                     // prepare to react

  {                                     // open critical section

    std::unique_lock<std::mutex> lk(m); // lock mutex

    cv.wait(lk);                        // wait for notify;
                                        // this isn't correct!
    
    …                                   // react to event
                                        // (m is locked)
  
  }                                     // close crit. section;
                                        // unlock m via lk's dtor
  
  …                                     // continue reacting
                                        // (m now unlocked)
```
_deacting task_

```C++
  …                                     // detect event
  
  cv.notify_one();                      // tell reacting task
```  

_condition variable_ 方法的优点是这个方法简单。

_condition variable_ 方法的缺点：  
* 如果 _detecting task_ 在 _reacting task_ 等待之前就已经通知了 _condvar_ 的话，那么 _reacting task_ 将会挂起。按照通知 _condvar_ 去唤醒另一个 _task_ 的顺序，其他的 _task_ 必须先等待这个 _condvar_。如果 _detecting task_ 碰巧是在 _reacting task_ 执行 _wait_ 之前先执行了通知的话，那么 _reacting task_ 将丢失这个通知，它将会永远等待。
* _wait_ 语句没有考虑到 _spurious wakeup_。在很多语言的线程 _API_ 中，不只是在 _C++_ 线程 _API_ 中，等待某个 _condition variable_ 的代码可能会在这个 _condition variable_ 没有被通知的情况下就被唤醒了。这样的唤醒被称为 _spurious wakeup_。合适的代码可以通过确认那个被等待的条件确实真正地产生了，并将这个确认操作做为唤醒后的首个动作来处理这种 _spurious wakeup_ 问题。_C++_ _condvar_ _API_ 非常容易解决这种 _spurious wakeup_ 问题，因为它允许将被用来测试那个被等待的条件的 _lambda_ 或其他函数对象传递给 _wait_。也就是说，在 _reacting task_ 中的 _wait_ 调用看起来像是下面这样。想要利用这个能力需要 _reacting task_ 能够确定它正在等待的条件是否发生了。但是在我们已经考虑到的情景中，_reacting task_ 正在等待的条件是发生了一个 _detecting task_ 负责识别的事件。_reacting task_ 可能没有方法来确定它正在等待的事件是否已经发生了。这也是为什么要等待 _condition variable_。  
```C++
  cv.wait(lk,
    []{ return whether the event has occurred; });
``` 

## _shared boolean flag_

_reacting task_ 

```C++
  …                                     // prepare to react
  
  while (!flag);                        // wait for event  
  
  …                                     // react to event
```

_deacting task_

```C++
  std::atomic<bool> flag(false);        // shared flag; see
                                        // Item 40 for std::atomic

  …                                     // detect event

  flag = true;                          // tell reacting task
```  

_shared boolean flag_ 方法的优点是：不需要 _mutex_，如果 _detecting task_ 在 _reacting task_ 开始轮询前就先设置了 _flag_ 的话，那么是没有问题的，没有类似的 _spurious wakeup_ 问题。

_shared boolean flag_ 方法的缺点是会在 _reacting task_ 中花费成本进行轮询。在 _reacting task_ 等待 _flag_ 被设置期间，肯定是会被阻塞的，但它却仍然在运行。因此这个 _reacting task_ 占用了其他的 _task_ 可以去使用的硬件线程，_reacting task_ 在每次开始或者结束它所对应的 _time slice_ 时，都会带来上下文切换的成本，_reacting task_ 还可以只让一个 _CPU_ 保持运行，让其他的 _CPU_ 关闭去节省能耗。真正被阻塞的 _task_ 不会做这些事情。这正是 _condvar-based_ 方法的优势，因为在 _wait_ 调用中的 _task_ 是真正被阻塞的。

## _condition variable_ & _boolean_ 方法 

_reacting task_ 

```C++
  …                                               // prepare to react
  
  {                                               // as before
    std::unique_lock<std::mutex> lk(m);           // as before
    
    cv.wait(lk, [] { return flag; });             // use lambda to avoid
                                                  // spurious wakeups
    
    …                                             // react to event
                                                  // (m is locked)
  }
  
  …                                               // continue reacting
                                                  // (m now unlocked)
```

_deacting task_

```C++
  std::condition_variable cv;                     // as before
  std::mutex m;
  bool flag(false);                               // not std::atomic
  
  …                                               // detect event
  
  {
    std::lock_guard<std::mutex> g(m);             // lock m via g's ctor
    
    flag = true;                                  // tell reacting task
                                                  // (part 1)
  
  }                                               // unlock m via g's dtor
  
  cv.notify_one();                                // tell reacting task
                                                  // (part 2)

``` 

_condition variable_ & _boolean_ 方法的优点是：不管 _reacting task_ 等待是否是在 _detecting task_ 通知之前，这个方法都可以工作，这个方法就算出现了 _spurious wakeup_ 也可以工作，不需要轮询。

_condition variable_ & _boolean_ 方法的缺点是 _detecting task_ 仍然在按照非常奇怪的方式与 _reacting task_ 通信。通知 _condition variable_ 是告诉 _reacting task_ 它正在等待的事件已经发生了，但是 _reacting task_ 还必须要去检查这个 _flag_ 才能确定。设置了这个 _flag_ 是告诉了 _reacting task_ 那个事件是真正地发生了，但是 _detecting task_ 仍然要通知 _condition variable_，为的是 _reacting task_ 将被唤醒以去检查那个 _flag_。这个方法可以工作，但似乎不是非常干净。

## _void future_ 方法

```C++
  std::promise<void> p;                 // promise for
                                        // communications channel
```  

_reacting task_  

```C++
  …                                     // prepare to react
  
  p.get_future().wait();                // wait on future
                                        // corresponding to p
  
  …                                     // react to event
```

_detecting task_

```C++
  …                                     // detect event
  
  p.set_value();                        // tell reacting task
```  

_void future_ 方法的优点是：不需要 _mutex_，不管 _detecting task_ 是否会在 _reacting task_ 等待前先设置了它的 _std::promise_，都可以工作，并且不受 _spurious wakeup_ 的影响。

_void future_ 方法的缺点是 _std::promise_ 可能只能被设置一次。_std::promise_ 和 _future_ 之间的通信通道是 _one-shot_ 机制：它不可以被重复使用。

# _Item 40_ 并发使用 _std::atomic_ 特殊内存使用 _volatile_

## _std::atomic_ 的所有成员函数都是原子的

_std::atomic_ 的所有成员函数都是原子的，而且这些原子操作都是使用特殊机器指令实现的，比利用 _mutex_ 更加高效，而 _volatile_ 并不保证对它的操作是原子的。

## _std::atomic_ 会对代码的重新排序施加限制

_std::atomic_ 会对代码的重新排序施加限制，这个限制是源代码中位于写 _std::atomic_ 变量之前的代码不得放在这个写 _std::atomic_ 变量之后，而 _volatile_ 并不会对代码的重新排序施加限制。

不使用 _std::atomic_ 时：

```C++
  bool valAvailable(false);
  
  auto imptValue = computeImportantValue();       // compute value
  
  valAvailable = true;                            // tell other task
                                                  // it's available
```

当人们看到这个代码时，我们知道，_imptValue_ 的赋值是发生在 _valAvailable_ 的赋值之前的，这是重要的，但是编译器看到的所有只是一对独立变量的赋值而已。通常情况下，编译器被允许去重新排序这些不相关的赋值，可能重新排序为下面这样，这是错误的。

```C++
  bool valAvailable(false);
  
  valAvailable = true;                            // tell other task
                                                  // it's available

  auto imptValue = computeImportantValue();       // compute value
```

使用 _std::atomic_ 时：

```C++
  std::atomic<bool> valAvailable(false);
  
  auto imptValue = computeImportantValue();       // compute value
  
  valAvailable = true;                            // tell other task
                                                  // it's available
```

因为 _std::atomic_ 会对代码的重新排序施加限制，所以代码仍然为上面这样，这是正确的。

## _volatile_ 会禁止对所对应的内存上的操作执行优化

_volatile_ 会禁止对所对应的内存上的操作执行优化，而 _std::atomic_ 并不会禁止对所对应的内存上的操作执行优化。

因为对于 **_常规_** 内存上的操作，是允许编译器执行优化的，

```C++
  auto y = x;                           // read x
  y = x;                                // read x again
  x = 10;                               // write x
  x = 20;                               // write x again
```  

所以编译器可以优化为下面这样。

```C++
  auto y = x;                           // read x
  
  x = 20;                               // write x
```

因为对于 **_特殊_** 内存上的操作，比如：被用于 _memory-mapped I/O_ 的内存和进程间共享的内存上的操作，是禁止编译器执行优化的，

```C++
  volatile int x;
```  

```C++
  auto y = x;                           // read x
  y = x;                                // read x again
  x = 10;                               // write x
  x = 20;                               // write x again
```   

所以需要使用 _volatile_ 来禁止对所对应的内存上的操作执行优化。

```C++
  auto y = x;                           // read x
  y = x;                                // read x again
  x = 10;                               // write x
  x = 20;                               // write x again
```  

如果 _x_ 对应的是 _radio transmitter_ 的控制端口的话，那么这可能是代码正在像这个 _ratio_ 发送命令，值 _10_ 所对应的命令和 _20_ 所对应的命令是不同的。优化了第一个赋值语句将会改变发往这个 _ratio_ 的命令的顺序。

# _Item 41_ 对于移动是成本小的且总是会被拷贝的可拷贝的形参考虑 _pass-by-value_

使用重载或 _universal reference_ 而不是 _pass-by-value_，除非能证实对于你需要的形参类型，_pass-by-value_ 能产生可接受的高效的代码。

```C++
  class Widget {                                  // Approach 1:
  public:                                         // overload for
    void addName(const std::string& newName)      // lvalues and
    { names.push_back(newName); }                 // rvalues
    
    void addName(std::string&& newName)
    { names.push_back(std::move(newName)); }
    …
  
  private:
    std::vector<std::string> names;
  };
  
  class Widget {                                  // Approach 2:
  public:                                         // use universal
    template<typename T>                          // reference
    void addName(T&& newName)
    { names.push_back(std::forward<T>(newName)); }
    …
  };

  class Widget {                                  // Approach 3:
  public:                                         // pass by value
  void addName(std::string newName)
  { names.push_back(std::move(newName)); }
  …
  };
```  

# _Item 42_ 考虑使用 _emplacement_ 来代替 _insertion_

## _container_ 中的 _emplacement_ 和 _insertion_

_emplacement_：_emplace_、_emplace_front_、_emplace_back_ 和 _emplace_after_ 等。

_insertion_：_insert_、_push_front_、_push_back_ 和 _insert_after_ 等。

## _emplacement_ 不需要生成临时变量

```C++
  std::vector<std::string> vs;          // container of std::string
  vs.push_back("xyzzy");                // add string literal
``` 

```C++
  vs.push_back(std::string("xyzzy"));   // create temp. std::string
                                        // and pass it to push_back
``` 

因为 _insertion_ 不是使用完美转发的，所以当所传递的实参的类型不是所对应的 _container_ 所持有的类型时，是需要生成临时变量的。

```C++
  std::vector<std::string> vs;          // container of std::string
  vs.emplace_back("xyzzy");                // add string literal
``` 

因为 _emplacement_ 是使用完美转发的，可以直接将所传递的实参完美转发给所对应的构造函数，所以当所传递的实参的类型不是所对应的 _container_ 所持有的类型时，是不需要生成临时变量的。

所以 _emplacement_ 不需要生成临时变量，而 _insertion_ 需要生成临时变量，前者效率高。

## _copy initialization_ 和 _direct initialization_

```C++
  std::regex r1 = nullptr;              // copy initialization error! won't compile
  
  std::regex r2(nullptr);               // direct initialization compiles
```   
_copy initialization_ 不允许使用 _explicit_ 构造函数。_direct initialization_ 允许使用 _explicit_ 构造函数。