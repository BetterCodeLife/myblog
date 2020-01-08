---
title: C语言共用体
date: 2020-01-09 00:03:39
tags:
---
####  定义
共用体是一种特殊的数据类型，允许您在相同的内存位置存储不同的数据类型。您可以定义一个带有多成员的共用体，但是任何时候只能有一个成员带有值。共用体提供了一种使用相同的内存位置的有效方式。

为了定义共用体，您必须使用 union 语句，方式与定义结构类似。union 语句定义了一个新的数据类型，带有多个成员。union 语句的格式如下：
```objc
union [union tag]
{
   member definition;
   member definition;
   ...
   member definition;
} [one or more union variables];

```
union tag 是可选的，每个 member definition 是标准的变量定义，比如 int i; 或者 float f; 或者其他有效的变量定义。在共用体定义的末尾，最后一个分号之前，您可以指定一个或多个共用体变量，这是可选的。

#### 赋值和取值
下面顶一个一个共用体并且为其成员赋值：
```objc
union test1 {
    unsigned a;
    unsigned b;
    unsigned c;
};
//1 a/b/c赋值顺序
union test1 g;
g.a = 1;
g.b = 2;
g.c = 3;
NSLog(@"a:%d b:%d c:%d",g.a,g.b,g.c);
//打印g.a,g.b,g.c结果为3

//交换顺序 c/a/b
g.c = 3;
g.a = 1;
g.b = 2;

NSLog(@"a:%d b:%d c:%d",g.a,g.b,g.c);
//打印g.a,g.b,g.c结果为2
```
由上面的现象得到的结论是:访问成员的值取决于最后的成员被赋值。

#### 内存占用大小

共用体占用的内存应足够存储共用体中最大的成员

```objc
#include <stdio.h>
#include <string.h>
 
union Data
{
   int i;
   float f;
   char  str[20];
};
 
int main( )
{
   union Data data;        
 
   printf( "Memory size occupied by data : %d\n", sizeof(data));
 
   return 0;
}
//Memory size occupied by data : 20
```
#### 常用

##### union+struct

```objc

union test{
    unsigned temp;
    struct {
        unsigned a : 2;
        unsigned b : 2;
        unsigned c : 4;
    };
};

//调用
  union test d;
        d.c = 8;
        d.a = 2;
        d.b = 2;
    
        //00000000 00000000 00000000 00000010
        //00000000 00000000 00000000 00001000
        //00000000 00000000 00000000 10001010
        NSLog(@"%d",d.temp);
//结果为00000000 00000000 00000000 10001010 转换成10进制为138
```
如上述的例子，结构体位域将内存进行划分，结构体成员指定内存区块为具体的内存，这样的话可以将多种类型的数据定义在一个字节中或者几个字节中，最典型的例子就是苹果的isa类型isa_t。（更加详细的关于isa_t的例子我将在isa_t文章中说明）
```objc
union isa_t {
    isa_t() { }
    isa_t(uintptr_t value) : bits(value) { }

    Class cls;
    uintptr_t bits;
#if defined(ISA_BITFIELD)
    struct {
         uintptr_t nonpointer        : 1;                                       \
      uintptr_t has_assoc         : 1;                                       \
      uintptr_t has_cxx_dtor      : 1;                                       \
      uintptr_t shiftcls          : 33; /*MACH_VM_MAX_ADDRESS 0x1000000000*/ \
      uintptr_t magic             : 6;                                       \
      uintptr_t weakly_referenced : 1;                                       \
      uintptr_t deallocating      : 1;                                       \
      uintptr_t has_sidetable_rc  : 1;                                       \
      uintptr_t extra_rc          : 19;  // defined in isa.h
    };
#endif
};

```