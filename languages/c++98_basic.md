- [_C++_ 对 _C_ 的增强](#c-对-c-的增强)
  - [变量](#变量)
    - [可以在任意位置定义变量](#可以在任意位置定义变量)
    - [禁止重复定义](#禁止重复定义)
  - [_register_](#register)
    - [可以对 _register_ 变量取地址](#可以对-register-变量取地址)
    - [可以将频繁使用的变量优化为 _register_ 变量](#可以将频繁使用的变量优化为-register-变量)
  - [_struct_](#struct)
    - [可以使用 _struct_ 来表示类](#可以使用-struct-来表示类)
  - [三目运算符](#三目运算符)
    - [可以对三目运算符进行赋值](#可以对三目运算符进行赋值)
  - [_const_](#const)
    - [_const_ 变量必须要初始化](#const-变量必须要初始化)
    - [_const_ 变量真正不可被修改](#const-变量真正不可被修改)
    - [_const_ 变量是 _compile-time_ 的常量](#const-变量是-compile-time-的常量)
- [_C++_ 的特性](#c-的特性)
  - [引用](#引用)
    - [引用在定义的同时必须要初始化](#引用在定义的同时必须要初始化)
    - [引用和 _被引用_ 具有相同的值和地址](#引用和-被引用-具有相同的值和地址)
    - [在调用函数时产生临时变量的条件](#在调用函数时产生临时变量的条件)
    - [_const \&_ 可以避免临时变量的问题](#const--可以避免临时变量的问题)
    - [_return-by-reference_ 的函数可以做为左值](#return-by-reference-的函数可以做为左值)
    - [_by-reference_ 比 _by-value_ 高效](#by-reference-比-by-value-高效)
  - [函数](#函数)
    - [默认参数](#默认参数)
    - [占位参数](#占位参数)
    - [重载函数](#重载函数)
    - [友元函数](#友元函数)
    - [_operator_](#operator)
- [类](#类)
  - [数据成员](#数据成员)
    - [非静态数据成员](#非静态数据成员)
    - [静态数据成员](#静态数据成员)
    - [静态数据成员不可以使用非静态数据成员](#静态数据成员不可以使用非静态数据成员)
  - [构造函数](#构造函数)
    - [构造函数的作用](#构造函数的作用)
    - [_default constructor_](#default-constructor)
    - [_parameterized constructor_](#parameterized-constructor)
    - [_copy constructor_](#copy-constructor)
    - [_conversion constructor_](#conversion-constructor)
    - [_copy elision_](#copy-elision)
    - [_copy elision_ 所带来的重要结论](#copy-elision-所带来的重要结论)
    - [_copy initialization_ 和 _direct initialization_](#copy-initialization-和-direct-initialization)
    - [构造函数被生成的条件](#构造函数被生成的条件)
  - [析构函数](#析构函数)
    - [析构函数的作用](#析构函数的作用)
    - [析构函数被生成的条件](#析构函数被生成的条件)
  - [转换函数](#转换函数)
  - [继承](#继承)
    - [_public_](#public)
    - [_protected_](#protected)
    - [_private_](#private)
    - [构造函数的执行顺序](#构造函数的执行顺序)
    - [析构函数的执行顺序](#析构函数的执行顺序)
    - [覆盖](#覆盖)
    - [_base class_ 的引用或指针可以引用或指向 _derived class_ 的对象](#base-class-的引用或指针可以引用或指向-derived-class-的对象)
    - [_base class object_(_derived class object_)](#base-class-objectderived-class-object)
    - [_derived class object_(_derived class object_)](#derived-class-objectderived-class-object)
    - [_base class object_ = _derived class object_](#base-class-object--derived-class-object)
    - [_derived class object_ = _derived class object_](#derived-class-object--derived-class-object)
    - [使用 _public virtual_ 来解决二义性问题](#使用-public-virtual-来解决二义性问题)
  - [_virtual function_](#virtual-function)
    - [_virtual function_ 的定义](#virtual-function-的定义)
    - [_virtual function_ 的继承性](#virtual-function-的继承性)
    - [构造函数不可以是 _virtual function_](#构造函数不可以是-virtual-function)
    - [析构函数可以是 _virtual function_](#析构函数可以是-virtual-function)
    - [友元函数不可以是 _virtual function_](#友元函数不可以是-virtual-function)
    - [_virtual function table_ 和 _pointer to virtual function table_](#virtual-function-table-和-pointer-to-virtual-function-table)
    - [_pure virtual function_](#pure-virtual-function)
  - [多态](#多态)
    - [多态的作用](#多态的作用)
    - [静态链编和动态链编](#静态链编和动态链编)
    - [重写函数](#重写函数)
    - [多态的实现条件](#多态的实现条件)
    - [多态的实现原理](#多态的实现原理)
- [类模板](#类模板)
  - [类模板的格式](#类模板的格式)
    - [类模板的声明和实现都必须在头文件中](#类模板的声明和实现都必须在头文件中)
  - [类模板的定义](#类模板的定义)
    - [类模板也是类，具有类的一些特性](#类模板也是类具有类的一些特性)
  - [类模板的模板形参和模板实参](#类模板的模板形参和模板实参)
    - [_type template parameter_ 类型模板形参和 _type template argument_ 类型模板实参](#type-template-parameter-类型模板形参和-type-template-argument-类型模板实参)
    - [_non-type template parameter_ 非类型模板形参和 _non-type template argument_ 非类型模板实参](#non-type-template-parameter-非类型模板形参和-non-type-template-argument-非类型模板实参)
- [异常](#异常)
  - [异常的格式](#异常的格式)
    - [_exception specification_](#exception-specification)
    - [_try-catch_](#try-catch)
  - [异常的分类](#异常的分类)
    - [意外异常](#意外异常)
    - [未捕获异常](#未捕获异常)
  - [异常的运行机制](#异常的运行机制)
    - [当异常被抛出时，会停止运行函数并开始向 _上_ 开始寻找 _try-catch_](#当异常被抛出时会停止运行函数并开始向-上-开始寻找-try-catch)
    - [在异常中继承关系仍然有效](#在异常中继承关系仍然有效)
    - [_throw;_](#throw)
    - [异常不适用于模板](#异常不适用于模板)


# _C++_ 对 _C_ 的增强

## 变量

### 可以在任意位置定义变量

在 _C++_ 中，可以在任意位置定义变量；在 _C_ 中，不可以。

### 禁止重复定义

```C++
  int v;
  int v = 0;
```

在 _C++_ 中，会显示重复定义错误；在 _C_ 中，不会显示重复定义，编译器会将 _v_ 链接到同一个内存地址上。

## _register_ 

### 可以对 _register_ 变量取地址

在 _C++_ 中，可以对 _register_ 变量取地址；在 _C_ 中，不可以。

### 可以将频繁使用的变量优化为 _register_ 变量

在 _C++_ 中，可以将频繁使用的变量优化为 _register_ 变量；在 _C_ 中，不可以。

```C++
  for(int i = 0; i < 10000; i++) {}
```

_C++_ 的编译器会将 _int i_ 优化为 _int register i_。

## _struct_

### 可以使用 _struct_ 来表示类 

在 _C++_ 中，_struct_ 表示类；在 _C_ 中，_struct_ 表示数据类型的集合，在其中不能定义函数。

在 _C++_ 中，_struct_ 和 _class_ 都表示类，只是默认的访问属性不同，_struct_ 默认的访问属性是 _public_，而 _class_ 默认的访问属性是 _private_，在 _C++_ 一般用 _class_ 来表示类，而使用 _struct_ 来表示在 _C_ 中的传统功能：数据类型的整合。

```C++
  extern "c"
  {
  struct Widget {

  };
  };

  Widget w;                   // c++ 
  struct Widget w;            // c
```

## 三目运算符

### 可以对三目运算符进行赋值

在 _C++_ 中：

```C++
  int v1 = 0;
  int v2 = 0;

  (v1 > v2 ? v1 : v2) = 1;              // ok! 
```

在 _C_ 中：

```C
  int v1 = 0;
  int v2 = 0;

  (v1 > v2 ? v1 : v2) = 1;              // error!  

  *(v1 > v2 ? &v1 : &v2) = 1;           // ok! 
```

_(v1 > v2) ? v1 : v2_ 是右值，不能赋值，而 _(v1 > v2 ? &v1 : &v2)_ 也是右值，但是经过 _&_ 后可以做为左值。

## _const_ 

### _const_ 变量必须要初始化 

在 _C++_ 中，_const_ 变量必须要初始化；在 _C_ 中，_const_ 变量可以不初始化。

### _const_ 变量真正不可被修改

在 _C++_ 中，不可以通过地址去修改 _const_ 变量，因为 _const_ 变量是被分配在了符号表中的，不可被修改；在 _C_ 中，可以通过地址去修改 _const_ 变量。

### _const_ 变量是 _compile-time_ 的常量

在 _C++_ 中，_const_ 变量是 _compile-time_ 的常量，可以被用来定义数组；在 _C_ 中，不可以。

# _C++_ 的特性

## 引用

### 引用在定义的同时必须要初始化

```C++
  int v = 0;
  int &rv = v;
```

### 引用和 _被引用_ 具有相同的值和地址

_v_ 和 _rv_ 具有相同的值和地址

### 在调用函数时产生临时变量的条件

* 实参类型不正确，但是可以转换为正确的形参的类型。
* 实参类型正确，但是不是左值。

### _const &_ 可以避免临时变量的问题

```C++
  void Function(short &, short &) {
    ...
  }
```
```C++
  int a = 0, b = 1;

  Function(a, b);                       // error!
```
如果一个函数的形参的类型为 _non-const-reference_，并且在调用这个函数时会产生临时变量的话，那么是不可以通过编译的。

因为属于实参类型不正确，但是可以转换为正确的形参的类型的场景，所以会产生两个临时变量。因为真正传递给 _Function_ 的是这两个临时变量。所以形参 _non-const &_ 绑定的是临时变量。因为形参都是 _non-const_ 的，所以形参是可能在 _Function_ 中被修改的，也就是临时变量是可能在 _Function_ 中被修改的。因为在 _Function_ 中被修改的实际上是临时变量，并不是真正的 _a_ 和 _b_，所以是无意义的，是不可以通过编译的，并且在函数调用结束后，这两个临时变量也是被销毁的。


```C++
  void Function(const short &, const short &) {
    ...
  }
```
```C++
  int a = 0, b = 1;

  Function(a, b);                       // ok！
```

如果一个函数的形参的类型为 _const-reference_，并且在调用这个函数时会产生临时变量的话，那么是可以通过编译的。

因为属于实参类型不正确，但是可以转换为正确的形参的类型的场景，所以会产生两个临时变量。因为真正传递给 _Function_ 的是这两个临时变量。所以形参 _const &_ 绑定的是临时变量。因为形参都是 _const_ 的，所以形参是不可能在 _Function_ 中被修改的，也就是临时变量是不可能在 _Function_ 中被修改的。所以是可以通过编译的。

```C++
  Function(0, 1);                 // error!
```

与上面的情况相似，只是属于实参类型正确，但是不是左值的场景。

### _return-by-reference_ 的函数可以做为左值

```C++
  int& Function(const int &) {
    ...
  }

  Function(0) = 0;            // ok!
```

```C++
  int Function(const int &) {
    ...
  }

  Function(0) = 0;            // error!
```

_return-by-reference_ 的函数可以做为左值，而 _return-by-value_ 的函数不可以。 

### _by-reference_ 比 _by-value_ 高效

_pass-by-value_ 会产生实参的副本，相当于形参和实参是两个独立的变量，形参和实参互不影响。_pass-by-reference_ 不会产生实参的副本，形参和实参具有相同的值和地址。因为 _pass-by-reference_ 比 _pass-by-value_ 不会产生副本，所以 _pass-by-reference_ 比 _pass-by-value_ 高效。

_return-by-reference_ 和 _return-by-value_ 也是相同的原因。

## 函数

### 默认参数

```C++
  
  void Function(int x = 0);             // .h 

  void Function(int x) {                // .cpp

    std::cout << x << std::endl;
  }

  Function(0);                          // 0

  Function(1);                          // 1
``` 

默认参数的后面必须都是默认参数。

```C++
  void Function(int x, int y = 0, int z = 0);     // ok!   

  void Function(int x, int y = 0, int z);          // error! 
```

### 占位参数

```C++
  
  void Function(int x，int)                  
  {
    ...
  }

  Function(0);                          // error!

  Function(0, 0);                       // ok!

``` 

占位参数无法使用，但是必须传入。

### 重载函数

重载函数为函数名相同，但是函数的特征标（形参类型 _||_ 形参个数 _||_ 形参的顺序）不同的函数。本质还是不同的函数。

```C++
  void Function(int);

  void Function(short);

  void Function(int， int);

  void Function(short, int);

```

注意 _const_、_non-const_、_reference_ _non-reference_ 都是类型。

```C++
  void Function(int);

  void Function(const int);

  void Function(int &);

  void Function(const int &);

```

注意函数的返回类型不在特征标范围内，会报重复定义的错误。

```C++
  void Function(int);

  double Function(int);     // error! 

```

### 友元函数

友元函数本质是全局函数，只是增加了对类成员的访问权限而已，将友元函数放在哪个类声明中，就可以在这个友元函数中访问该类的全部成员，包括私有成员。_operator<<_ 只能通过友元函数来完成，因为如果使用类成员数据来完成的话，都需要在标准 _std::ostream_ 中完成，这是不可以的。注意返回值是 _std::ostream&_，这样才能支持链式访问。

```C++
  class Widget {
  public:
    friend std::ostream& operator<<(std::ostream &c, const Widget &w) {
      ...
    }
    ...
};
```

### _operator_

class Widget {
public:
  Widget& operator++() {
    this->_value++;
    return *this;
  }

  Widget operator++(int) {
    Widget w(*this);
    this->_value++;
    return w;
  }

   Widget operator+(const Widget &w) {
    ...
  }

private:
  _value;  
};

* _Widget& operator++()_：前置 _++_，返回 _Widget&_。
* _Widget operator++(int)_：后置 _++_，占位参数，返回 _Widget_。
* _Widget operator+(const Widget &w)_：返回 _Widget_ 以支持链式访问，比如：_w1 + w2 + w3_。
* _._、_::_、_?_ 和 _sizeof_ 不能有所对应的 _operator_。
* _=_、_()_、_[]_ 和 _->_ 只能使用类成员数据完成。
* 形参类型必须有类本身存在，隐含的 _this_ 也算实参之一。  

# 类

## 数据成员

### 非静态数据成员
  
同一个类可以定义多个对象，每个对象都有自己的存储空间用来存储自己的非静态数据成员，即为非静态数据成员属于每个对象，而不是类。可以通过 _this_ 指针来访问非静态数据成员，不可以通过 _class::_ 来访问非静态数据成员。

### 静态数据成员

同一类定义的所有对象共享静态数据成员，即为静态数据成员是属于类的而不是属于各个对象。必须要先初始化静态数据成员后，才可以进行使用。因为类中只是声明并没有分配内存，初始化是在要求编译器来分配内存。可以通过 _class::_ 来访问静态数据成员，不可以通过 _this_ 指针来访问非静态数据成员。静态数据成员不可以使用非静态数据成员，只可以使用静态数据成员。

### 静态数据成员不可以使用非静态数据成员

静态数据成员不可以使用非静态数据成员，只可以使用静态数据成员。

## 构造函数

### 构造函数的作用

构造函数被用来初始化对象中的数据成员，特别是私有属性的数据成员，因为不能在对象外初始化对象的私有数据，所以需要在构造函数中使用初始化列表进行，并且对象不能像调用函数成员一样调用构造函数。

### _default constructor_

```C++
  class Widget {
  public:
    Widget();
    ...
  }
```

### _parameterized constructor_

```C++
  class Widget {
  public:
    Widget(int, int);
    ...
  }
```

### _copy constructor_

```C++
  class Widget {
  public:
    Widget(const Widget &);
    ...
  }
```

### _conversion constructor_

```C++
  class Widget {
  public:
    Widget(int);
    ...
  }
```
_conversion constructor_ 是没有使用 _explicit_ 声明且可以使用单个形参进行调用的构造函数。
> A constructor that is not declared with the specifier explicit and which can be called with a single parameter is called a converting constructor.

_conversion constructor_ 指定了从它的实参的类型到它的类的类型的隐式转换。注意 _non-explicit_ 用户定义的转换函数也指定了隐式转换。

> It is said that a converting constructor specifies an implicit conversion from the types of its arguments (if any) to the type of its class. Note that non-explicit user-defined conversion Function also specifies an implicit conversion

### _copy elision_

```C++
    class Widget {
    public:
      Widget(int);
      Widget(const Widget &);
      ...
  }
```

```C++
  Widget Function(void) {
    return Widget(0);
  }
```

_copy elision_ 可以避免调用多余的 _copy constructor_。可以通过指定 _-fno-elide-constructors_ 来关闭 _copy elision_。

当 _disable_ _copy elision_ 时，_Widget w(Widget(0));_ 和 _Function()_ 都是先执行一次 _conversion constructor_ ，再执行一次 _copy constructor_。

当 _enable_ _copy elision_ 时，_Widget w(Widget(0));_ 和 _Function()_ 都是只执行一次 _conversion constructor_。避免了多余的 _copy constructor_，_Widget w(Widget(0));_ 是直接将 _conversion constructor_ 所产生的临时变量做为了结果，而 _Function()_ 是利用了 [_RVO_](effective_modern_cpp.md#rvo-return-value-optimization)。

### _copy elision_ 所带来的重要结论

因为编译器可能会进行各种各样的优化，比如：_copy elision_，所以，比如：_Widget w(Widget(0));_ 和 _Function()_ 这两个表达式都只会执行一次 _conversion constructor_，不会再执行一次 _copy constructor_ 了，如果自定义的类的 _conversion constructor_ 和 _copy constructor_ 执行的是不同的结果的话，那么就有问题了。也不能去假设有没有进行更多的其他的优化，唯一可以做的是使定义的类中的所有的构造函数的结果都是相同的，这样就不用猜测担心各种优化了。

### _copy initialization_ 和 _direct initialization_

```C++
    class Widget {
    public:
      Widget(int);
      explicit Widget(const Widget &);
      ...
  }

  Widget w1 = 0;              // ok! copy initialization 
  Widget w2 = w1;             //error! direct initialization
```
_copy initialization_ 不允许使用 _explicit_ 构造函数。_direct initialization_ 允许使用 _explicit_ 构造函数。

### 构造函数被生成的条件

见 [_default constructor_ 被生成的条件](effective_modern_cpp.md#copy-operation-被生成的条件) 和 [_copy constructor_ 被生成的条件](effective_modern_cpp.md#default-constructor-被生成的条件)。

## 析构函数

### 析构函数的作用

当对象的生命周期结束时，对象的析构函数被自动调用。

```C++
  class Widget {
  public:
    ~Widget();
    ...
  }
```

### 析构函数被生成的条件

见 [_析构函数被生成的条件_](effective_modern_cpp.md#析构函数被生成的条件)。

## 转换函数

转换函数指定了它的类的类型到指定类型的转换。
```C++
  class Widget {
  public:
    operator int() {
      ...
    }
    ...
  };

  int x = Widget(0);

```

## 继承

### _public_ 

* _base class_ 的 _public_ 成员生为 _derived class_ 的 _public_ 成员。
* _base class_ 的 _protected_ 成员生为 _derived class_ 的 _protected_ 成员。
* _base class_ 的 _private_ 成员生为 _derived class_ 的 _private_ 成员。

### _protected_

* _base class_ 的 _public_ 和 _protected_ 成员生为 _derived class_ 的 _protected_ 成员；  
* _base class_ 的 _private_ 成员生为 _derived class_ 的 _private_ 成员。

### _private_

* _base class_ 的 _public_、_protected_ 和 _private_ 成员生为 _derived class_ 的 _private_ 成员。

### 构造函数的执行顺序

* _base class_ 的构造函数。
* _derived class_ 所包含的其他类的构造函数，按照声明顺序而不是初始化列表的调用顺序执行。
* _derived class_ 的构造函数。

### 析构函数的执行顺序

* _derived class_ 的析构函数。
* _derived class_ 所包含的其他类的析构函数，按照声明顺序而不是初始化列表的调用顺序的相反顺序执行。
* _base class_ 的析构函数。

### 覆盖

_derived class_ 中的同名成员会将 _base class_ 中的同名成员覆盖，同名成员可以是变量、枚举、函数等，注意函数的名字相同即可，返回类型和函数特征标不同也可以覆盖。发生覆盖时访问 _base class_ 中的成员需要使用 _bass class ::_ 进行指定。

### _base class_ 的引用或指针可以引用或指向 _derived class_ 的对象

_base class_ 的引用或指针可以引用或指向 _derived class_ 的对象，但是不可以访问 _derived class_ 的成员，这是实现多态的基础。

### _base class object_(_derived class object_)

_base class object_(_derived class object_) 是以 _derived class_ 中的 _base class_ 部分为参数来调用 _base class_ 的 _copy constructor_ 的。

### _derived class object_(_derived class object_)

_derived class object_(_derived class object_) 需要在 _derived class_ 的构造函数的初始化列表中初始化 _base class_ 部分。

```C++
  class Base {
  public:
    Base(const Base &) {
      ...
    }
    ...
  };

  class Derived : public Base {
  public:
    Derived(const Derived &d) : Base(d) {
      ...
    } 
    ...
  };

```

### _base class object_ = _derived class object_

以 _derived class_ 中的 _base class_ 部分为参数，来调用 _base class_ 的 _copy operator_。

### _derived class object_ = _derived class object_

* 当 _derived class_ 的 _copy operator_ 是被生成的时，会自动以 _derived class_ 中的 _base class_ 部分为参数来调用 _base class_ 的 _copy operator_。
* 当 _derived class_ 的 _copy operator_ 不是被生成的，而是被用户所定义的时，注意不会自动以 _derived class_ 中的 _base class_ 部分为参数来调用 _base class_ 的 _copy operator_，需要自己进行显式操作。

```C++
  class Base {
  public:
    Base& operator=(const Base &) {
      ...
    }
    ...
};

class Derived : public Base {
public:
  Derived& operator=(const Derived &d) {
    Base::operator=(d);
    ...
  }
};
```

### 使用 _public virtual_ 来解决二义性问题

```C++
  class Worker
  {
  public:
    Worker(int v) {
      ...
    }
    int _value;
  };

  class Waiter : public virtual Worker                 
  {                                       
  public:
    Waiter(int v) : Worker(v) {
      ...
    }
  };                                   

  class Singer : public virtual Worker
  {
  public:
    Singer(int v) : Worker(v) {
      ...
    }
  };	

  class SingerWaiter : public Waiter, public Singer
  {
    SingerWaiter(int v) : 
    Worker(v), 
    Waiter(v), 
    Singer(v) {
      ...
    }
  };

  SingerWaiter sw(0);
  sw._value = 1;              // ok!
```

因为如果使用的是 _public_ 的话，那么会出现二义性问题，所以需要使用 _public virtual_ 来解决二义性问题，注意必须要在初始化列表中显式地调用 _virtual base class_ _Worker_ 的构造函数。

## _virtual function_

### _virtual function_ 的定义

_virtual function_ 是在类中使用 _virtual_ 修饰的函数。

### _virtual function_ 的继承性

* 如果 _base class_ 中的某函数成员是 _virtual function_ 的话，那么在 _derived class_、_derived derived class_... 中继承而来的此函数成员也将是 _virtual function_。
* 如果在 _derived class_、_derived derived class_... 中对此函数成员进行了重写的话，那么在 _derived class_、_derived derived class_...中无论是否用 _virtual_ 修饰了此重写函数，在 _derived class_、_derived derived class_...中的此重写函数都将是 _virtual function_，但最好用 _virtual_ 标识。

### 构造函数不可以是 _virtual function_

因为 _derived class_ 不继承 _base class_ 的构造函数，所以构造函数不可以是 _virtual function_。

### 析构函数可以是 _virtual function_

因为如果类的析构函数为 _virtual function_ 的话，那么 _delete_ 该类的一个指针不会根据这个指针的定义的类型来进行析构，而是根据指针所指向对象的类型来进行析构，即为去调用指针所指向的对象的析构函数。如果析构函数是 _virtual function_ 的话，那么也遵循 _virtual function_ 的继承性。

### 友元函数不可以是 _virtual function_ 

友元函数不可以是 _virtual function_。
 
### _virtual function table_ 和 _pointer to virtual function table_

如果类中包含有 _virtual function_，那么编译器会在编译期间生成一个隐藏的 _virtual function table_ 以及一个 _pointer to virtual function table_，在这个 _virtual function table_ 存储了这个类的所有 _virtual function_。

### _pure virtual function_

```C++
  class Widget {
    virtual int Function(void) = 0;
  };
```

* 包含 _pure virtual function_ 的类为抽象类。
* _pure virtual function_ 必须要重写。
* 抽象类不可以用来实例化对象，但可以用来定义指针。
* 抽象类的 _pure virtual function_ 一般只有声明，没有实现，但也可以有函数实现。
* 抽象类一般不声明数据成员，但也可以声明。
* 抽象类一般用来做为 _base class_ 和接口类。

## 多态

### 多态的作用

根据指针或引用所指向或引用的对象的类型来选择所对应的类的 _virtual function_，而不是根据指针或引用的定义的类型来选择所对应的类的 _virtual function_。

### 静态链编和动态链编

执行函数是发生在执行阶段，而确定执行哪个函数如果发生在编译阶段则称为静态链编，如果发生在执行阶段则称为动态链编。

* 静态链编效率高，成本低，但无法实现多态。
* 动态链编效率低，成本高，因为需要 _virtual function table_ 和 _pointer to virtual function table_，但可以实现多态。

### 重写函数

[_重写函数_](effective_modern_cpp.md#重写的条件)。

### 多态的实现条件

* 必须有继承关系。
* 必须有 _base class_ 的指针或引用指向或引用 _derived class_ 的对象。
* 必须有在 _derived class_ 中重写 _base class_ 的 _virtual function_。

### 多态的实现原理

当符合多态的实现条件且指针或引用调用了 _virtual function_ 时，会自动在指针或引用所指向或引用的对象的 _pointer to virtual function table_ 所指向的 _virtual function table_ 中，获取所对应的 _virtual function_ 并执行，从而实现根据指针或引用所指向或引用的对象的类型来选择所对应的类的 _virtual function_，而不是根据指针或引用的定义的类型来选择所对应的类的 _virtual function_。

# 类模板

## 类模板的格式 

```C++
  template<class T, int size>
  class Widget {
  public:
    Widget();
    Widget(const Widget &);
    Widget& operator=(const Widget &);
    ...
  };

  template <class T, int size>
  Widget<T, size>::Widget() {
    ...
  }

  template <class T, int size>
  Widget<T, size>::Widget(const Widget &) {
    ...
  }

  template <class T, short size>
  Widget<T, size>& Widget<T, size>::operator=(const Widget &) {
    ...
  }
```

### 类模板的声明和实现都必须在头文件中

类模板的声明和实现都必须在头文件中。在类模板的声明中和数据成员函数中可以直接使用 _Widget_，除此之外，需要使用 _Widget&lt;T, size&gt;_，比如：函数的返回类型。

## 类模板的定义

### 类模板也是类，具有类的一些特性

模板类也是类，具有类的一些特性，不要把模板类特殊化。

## 类模板的模板形参和模板实参

### _type template parameter_ 类型模板形参和 _type template argument_ 类型模板实参

* _type template parameter_ 类型模板形参是一个符号，比如：_T_。
* _type template argument_ 类型模板实参是一个类型，比如：_int_、_std::vector_ 和 _Widget_ 等。

### _non-type template parameter_ 非类型模板形参和 _non-type template argument_ 非类型模板实参

* _non-type template parameter_ 非类型模板形参是一个类型，可以是引用类型、_integral_ 类型、枚举类型和指针类型。
* _non-type template argument_ 非类型模板实参是一个 _compile-time_ 的常量。

# 异常 

## 异常的格式

### _exception specification_ 

```C++
  class Widget {
  public:
    void Function(void) throw(Widget, int, double) {
      
      if(...) {
        Widget w;
        throw(w);
      }
      else if(...) {
        throw(0);
      }
      else {
        throw(0.0);
      }
    }
    ...
  };
```

* _exception specification_ 是告诉编译器：函数可能会抛出这些异常，如果所抛出的异常不在其中话，那么是会产生意外异常的。
* _exception specification_ 是告诉调用方：函数可能会抛出这些异常，调用方应该去捕获这些异常，如果没有进行捕获的话，那么是会产生未捕获异常的。


### _try-catch_ 

```C++
  Widget w;

  try {
    w.Function();
  } 
  catch (int v) {
    ...
  }
```

## 异常的分类 

### 意外异常

意外异常是函数所抛出的不属于它的 _exception specification_ 以外的异常。当产生意外异常时，默认是终止程序，可以通过 _set_unexpected_ 修改默认行为。

### 未捕获异常

未捕获异常是函数所抛出属于它的 _exception specification_ 之内的但没有进行捕获的异常。当产生未捕获异常时，默认是终止程序，可以通过 _set_terminate_ 修改默认行为。


## 异常的运行机制

### 当异常被抛出时，会停止运行函数并开始向 _上_ 开始寻找 _try-catch_

当异常被抛出 _throw_ 时，会停止运行函数并开始向 _上_ 开始寻找 _try-catch_，找到第一个 _try-catch_ 后，然后会释放这个 _throw_ 和 _try-catch_ 之间的所有对象。 

### 在异常中继承关系仍然有效

在异常中继承关系仍然有效，比如：_base class_ 的引用仍然可以引用 _derived class_ 的对象。所以，如果 _exception specification_ 中指定的是 _base class_，但抛出的却是 _derived class_ 的话，那么这不算是以外异常的。

### _throw;_ 

_throw;_ 表示向 _上_ 抛出所捕获到的异常。

### 异常不适用于模板