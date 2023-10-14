## 2.1基本内置类型

- **算术类型**:整型数(包括字符和布尔型), 浮点数
- **空类型**

#### 2.1.1算数类型

- 无符号和有符号不要混用
- 整形int,要么long long(跳过long 忽略short)
- 浮点数运算用double(float代价同,long代价大)

#### 类型转换

- 非布尔值,0转false,其他转true
- 浮点数给整数,保留整数
- 赋予无符号类型超出范围,取模
- 无有符号数混用,有符号变无符号数  

#### 数制

10,8,16进制部分数重合,所以需要重新标识,开头不为字母下划线防止识别为变量,前缀0不改变大小,即为8进制,x表示hexdecimal的简写,0X即为十六进制

#### 前后缀改变默认类型

| 前缀     | 含义                | 类型          |
| ------ | ----------------- | ----------- |
| u      | unicode16字符       | char16_t    |
| U      | unicode32字符       | char32_t    |
| L      | 宽字符               | wchar_t     |
| u8     | UTF-8(仅用于字符串字面常量) | char        |
| **后缀** | **整型字面值**         | **浮点字面值**   |
| u或U    | unsigned          |             |
| l或L    | long              | long double |
| ll或LL  | long long         |             |
| f或F    |                   | float       |

## 2.2变量

对象,object,存储特定数据类型的空间和数据   
值,value,只读的数据  
初始化和赋值:初始化是创建时赋予初始值,赋值是擦除赋予值

#### 初始化

    int tmp=0;
    int tmp(0);  
    int tmp={0};
    int tmp{0};//c11列表初始化
    
    列表初始化内置类型如果会导致丢失值会报错  
    long double ld=3.1415
    int a{ld},b={ld};错误存在丢失风险

#### 定义与声明

1. 变量定义：为变量分配存储空间，指定初始值(可选)。变量仅有一个定义。
2. 变量声明：向编译器表明变量的类型和名字,可多次,定义也是声明

    extern int ix=1024; 定义变量ix
    extern int ix;声明变量ix

#### 命名规范

- 数字字母下划线,不能数字开头
- 变量小写字母index
- 类名大写字母开头Sales_item
- 多个单词注意划分student_loan

## 2.3复合类型--引用和指针

#### 引用

    int i=1024
    int &refi=i   //refi是指向i的别名
    int &refi2   //错误:引用必须初始化

引用一旦声明就和对象绑定,无法更改,而且必须绑定对象,不能绑定字面值或者表达式结果

    double tmp=3.14;
    int &reftmp=tmp;//错误,必须是double

#### 指针

    int *p1=nullptr//可以转换成任意指针,需要先#include cstdlib
    int *p1=0;//与下句等价
    int *p1=null

- 不建议使用null和0,因为在重载时候会转化成整形,而nullptr无论何时都代表空指针
- 把int变量直接赋给指针是错误操作,即使是0也不行
- 指针只会是四种状态:指向对象,指向对象空间的下一个位置(方便递增遍历),空指针,上述三种以外均为无效指针

#### void*指针

    double obj=3.14,*pd=obj;
    void *pv=&obj;//obj可以是任意对象
    pv=pd//pv可存放任意类型指针

void指针可以存储任意指针类型,但仅仅是内存空间,无法操作,无法访问,无法赋值,使用必须显式类型转换

### 指向指针的引用

    int i=42;
    int *p;//p是一个int指针
    int *&r=p;//r是对p的引用
    r=&i;//geir赋值就是令p指向i
    *r=0;//i改为零

## 2.4const限定符

    contst int bufsize=512;//正确
    bufsize=500;//错误,const类型无法修改
    const int k;//错误,const类型必须初始化

- const默认作用域是文件,如果想要其他文件也使用,用extern
- const对象本身不能改变,但可用于表达式或者初始化其他对象

    extern const int buffsize=500;//f1定义并初始化
    #include"f1.cpp"//f2包含f1
    extern const int buffsize;//f2声明以使用

#### 常量引用

可以把引用绑定到const对象上,这样以来就无法修改绑定对象

    const int i=1024;
    const int &refi=i;
    i=42//错误,是对常量的引用

#### 初始化和对const的引用

    int i=42;
    const int &r1=i;//常量引用绑定变量
    const int &r3=r1*2//常量引用绑定临时量
    int &r4=r1*2//错误,引用不绑定临时量

#### 引用常量引用非常量对象时

    int i=42;
    int &r1=i;//引用r1绑定i
    const int&r2=i;//常量引用r2绑定i
    r1=0;//正确,通过引用修改
    r2=0;//错误,不能通过常量引用修改

#### 指针和const

指向常量的必须是常量指针,且*p不能赋值

    const double pi=3.14;
    double*ptr=&pi//错误,ptr是一个普通指针
    const double *cptr=3.14;//正确
    *cptr=42//错误,常量不能修改

一种例外:指向非常量的可以是常量指针 

    double dval=3.14;
    cptr=&dval

#### const指针

    int num=0;
    int *const pnum=&num//pnum将一直指向num
    该指针必须初始化

#### 顶层const

- 顶层const(top level const)本身是常量
- 底层(low level const)复合类型基本数据类型是常量

    int i=0;
    int *const p1=&i;//p1不能改,顶层const
    const int ci=42;//ci不能改,顶层const
    const int *p2=&ci//p2可以改,底层const
    const int *const p3=p2//靠右顶层const,靠左底层const
    const int &r=ci;//用于声明的const都是底层const

- 拷贝时顶层const不受影响,底层const必须资格相同(一般常量不能变为非常量)  

    int *p=p3//错误,p3包含底层const,p无
    p2=p3//正确,都是底层const
    p2=&i//正确,int*可以转化为const *int
    int &r=ci//错误,普通int&不能绑定到int常量上
    const int &r2=i//正确

#### constexpr表达式(constexpr定义的对象为顶层const)

    constexpr int mf=20//20是常量表达式
    constexpr int limit=mf+1;//mf+1是常量表达式
    constexpr int sz=size()//只有当size是一个constexpr函数才正确

允许将变量声明为constexpr来判断是否为常量  

字面值类型:**编译时进行计算**,称为字面值类型literal type

- 算术类型,引用,指针都属于(指针必须nullptr或0或**固定地址**[不能定义在函数体]对象)
- 自定义类,io库,string类型不属于

## 2.5处理类型

#### 类型别名

- 传统方法

    typedef double wages//wages是double的同义词
    typedef wages base,*p//base是double同义词,p是double*同义词

- 别名声明

    using SI=sales_item//SI是sales_item近义词

#### auto类型说明符(自动分析类型)

    auto item=item1+item2;//由item1,item2推断item类型

一次申明多个变量

    auto i=0,*p=&i;//正确,整形和整形指针
    auto z=0,pi=3.14;//错误,z,pi类型不一致

auto一般会忽略顶层const,同时底层const会保留下来,如果希望推断出的auto类型是一个顶层const,需要明确指出:

    const auto f=ci;

还可以将引用的类型设为auto,此时适用原来的初始化规则  

#### decltype类型说明符

**希望从表达式的类型推断类型,但不要用表达式的值**

    decltype(f()) sum=x;

decltype返回该变量的类型(包括顶层const和引用)

    const int ci=0,&cj=ci;
    decltype(ci) x=0;//x的类型是const int
    decltype(cj) y=x;//y的类型是const int&,y绑定到变量x
    decltype(cj) z;//错误,引用必须初始化  

decl的表达式如果加上括号,会变成表达式,这样declytype会变成引用类型

    decltype((i)) d;错误,引用类型未初始化
    decltype(i) e;正确,是一个未初始化的int

对比:  

- auto需要初始化,decytype无需
- auto会忽略顶层const
- auto会推导引用原始类型,decytype会保留引用                      

## 2.6自定义数据结构

函数体内定义类会使类收到限制,所以类定义在函数体外,同时不同文件中使用一个类,类需要保持一致,所以类通常被定义在头文件,比如库类型string定义在string.h  
为了防止重复的发生,使用#ifndef当且仅当未定义时为真,执行后续操作到#endif为止

    #ifdef SALES_DATA_H
    #define SALES_DATA.H
    struct  Sales_data{...};
    #endif
