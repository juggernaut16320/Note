条件语句,循环语句和跳转语句

## 5.1简单语句

语句以分号结束

    ival+5 表达式语句,求值后抛弃
    cout<<ival 一条有用的表达式语句
    ; 空语句

花括号内多个语句为复合语句(compound statement),又称块(block).

## 5.3条件语句

    if(condition)
        statement1
    else
        statement2

注意**如果statement有多句话,需要花括号变成块**  
另外st1和st2属于不同作用域，编译默认加花括号

#### 悬垂else

一个if嵌套在另一个if内部,此时**else总是和最近的if匹配**,如下程序

    if(c1)
        if(c2)
            s1
    else
        s2

实际上是

    if(c1)
        if(c2)
            s1
        else
            s2

不符合缩进的语义,**解决办法:花括号形成块**

#### 5.3.2switch语句

    switch(expr){
        case a:
            st1
            break;
        case b:
            st2
            break;
        default:
    }

- 注意break不然会一直执行完块，不过有时候几个条件共享同一操作可以不写
- 两个case的expr结果不能相同不然会错误
- 如果没有case匹配,会执行default后面的语句
- st1和st2是同一作用域
- **switch i选择过程如果跳过了变量的定义后面直接使用会出错,解决办法:case内的变量使用前在该case包含的块中定义**

## 5.4迭代语句(循环)

while和for执行前检查,do while执行后检查

    while(condition)
        statement
    
    for(init;condition;expr)
        statement

for可以定义多个对象,但是**声明类型必须相同(因为;)**

#### 5.4.3范围for语句

遍历容器或其他序列的所有元素

    for(declaration: expression)
        statement

- expr必须是一个序列,比如花括号括起来的初始值列表,数组,vector或string等类型的对象,这些对象的特点是拥有能返回迭代器的begin和end成员  
- declaration定义一个变量,序列中的每个元素都能转换成该变量的类型,确保相容的最简单办法是auto,如果需要对序列中的元素执行写操作,循环变量需要设置成引用类型

    传统for语句
    for(auto beg=v.begin(),end=v.end();beg!=end;++beg){
    
    }

## 5.5跳转语句

四种跳转: break continue goto return

- break终止最近的while,do while,for或switch

- continue中断当前循环,开始下一循环
  
  #### 5.5.3goto
  
  必须在同一函数内，尽量少使用而是使用控制流，不然代码难以阅读修改维护
  
    goto lable;
    lable:
  
  ## 5.6try语句块与异常处理
  
  典型异常包括失去数据库连接以及遇到意外输入等,处理反常行为可能是设计所有系统中最难的部分.  
  异常处理机制包括**异常检测**和**异常处理**,异常处理包括:

- **throw表达式**,异常检测部分使用throw表达式表示遇到问题,称为throw引发(raise)了异常

- **try语句块**(try block),异常处理以关键字try开始,以一个或多个catch子句结束,try中的异常通常会被某个catch处理.他们称为异常处理代码(exception handle)

- 一套**异常类**(exception class),用于在throw表达式和相关的子句传递异常的具体信息

#### 5.6.1throw表达式

    if(a==b){
        cout<<a+b;
        return 0;
    }else{
        cerr<<"not equal ";
        return -1;  
    }
    改写为
    if(a!=b)
        throw runtime_error("not equal")
    cout<<a+b

runtime_error是标准库异常类型的一种,抛出异常将终止当前函数,并将控制权移交给能处理异常的代码

#### 5.6.2try语句块

    语法格式
    try{
        program-statements
    }catch(exception-declaration){
        handler-statements
    }catch(exception-declaration){
        handler-statements
    }

#### try throw结合

    try{
    
        if(){throw type}
    }
    catch(type){
    
    }

#### 函数在寻找处理代码的过程中退出

程序遇到抛出异常的代码前,执行路径可能经过了多个try语句块.例如一个try语句块调用了包含另一个try语句块的函数.  
寻找处理代码的过程与函数调用过程相反,首先搜索抛出该异常的函数,未找到则终止该函数并在调用该函数的函数中继续寻找,以此类推.  
最终仍未找到匹配的catch语句,则程序转为terminate的标准库函数,一般执行该函数会导致程序非正常退出  

#### 异常安全

编写异常安全的代码很困难,略过部分程序意味着对象处于无效或者未完成的状态,或资源没有正常释放.异常发生期间正确的执行了清理工作的程序称为异常安全(exception safe)的代码  
当异常发生若简单的终止程序,此时不需要担心异常安全,但若要继续运行程序,需要确保异常发生后对象有效,资源无泄露,程序处于合理状态等,程序要求非常鲁棒的异常处理,这些超出了本书范畴.

## 5.6.3标准异常

c++标准库定义了一组类,用于报告标准库函数的异常,他们分别包含在四个头文件:

- exception头文件定义了最通用的异常类exception,只报告异常发生,不提供额外信息
- stdexcept头文件定义了几种常见的异常类
- new头文件定义了bad_alloc异常类型
- type_info头文件定义了bad_cast异常类型

- exception,bad_alloc和bad_cast对象只能默认初始化,不允许赋初始值
- 其他异常类型相反,需要string对象或c风格字符串初始化,创建此类对象需要初始值来包含错误相关信息

## Header guards and #pragma once

变量重定义会发生编译错误

    int main()
    {
        int x; // this is a definition for variable x
        int x; // compile error: duplicate definition
        return 0;
    }

函数重定义也是这样

    #include <iostream>
    int foo() // this is a definition for function foo
    {
        return 5;
    }
    
    int foo() // compile error: duplicate definition
    {
        return 5;
    }

虽然这些程序很容易修复(删除重复的定义)，但对于头文件，很容易出现头文件中的定义被包含多次的情况。当一个头文件#包含另一个头文件(这很常见)时，就会发生这种情况。  

    //square.h:
    int getSquareSides()
    {
        return 4;
    }
    
    //wave.h:
    #include "square.h"
    
    //main.cpp
    #include "square.h"
    #include "wave.h"
    
    int main()
    {
        return 0;
    }

此时该文件解析所有的include后,会出现类似上述的情况.  

**HEADER GUARDS**  

好消息是，我们可以通过一种称为header保护(也称为include保护)的机制来避免上述问题。头保护符是条件编译指令，采用以下形式:

    #ifndef SOME_UNIQUE_NAME_HERE
    #define SOME_UNIQUE_NAME_HERE
    
    // your declarations (and certain types of definitions) here
    
    #endif

当这个头包含#时，预处理器检查SOME_UNIQUE_NAME_HERE是否已经被先前定义过。如果这是我们第一次包含头，SOME_UNIQUE_NAME_HERE将没有被定义。因此，它#定义SOME_UNIQUE_NAME_HERE并包含文件的内容。如果头文件再次被包含到同一个文件中，SOME_UNIQUE_NAME_HERE将在头文件的内容第一次被包含时就已经被定义，并且头文件的内容将被忽略(由于#ifndef)。

所有的头文件都应该有头保护。SOME_UNIQUE_NAME_HERE可以是您想要的任何名称，但按照惯例，它被设置为头文件的完整文件名，**输入全部大写，使用下划线代替空格或标点符号**。例如，square.h将有标题保护:

**EXTRA**  
在大型程序中，可能有两个独立的头文件(来自不同的目录)最终具有相同的文件名(例如directoryA\config.h和directoryB\config.h)。如果只将文件名用于包含保护(例如CONFIG_H)，则这两个文件可能最终使用相同的保护名称。如果发生这种情况，任何包含(直接或间接)两个config.h文件的文件都不会收到第二个要包含的包含文件的内容。这可能会导致编译错误。

由于可能出现保护名称冲突，许多开发人员建议在头保护中使用更复杂/唯一的名称。一些好的建议是命名约定为<PROJECT><PATH><FILE>H、<FILE><LARGE RANDOM NUMBER>H或<FILE><CREATION DATE>_H

**头保护不阻止一个标头在不同的代码文件中被包含一次**  
请注意，头保护的目标是防止代码文件接收一个受保护头的多个副本。根据设计，头保护不阻止给定的头文件被包含(一次)到单独的代码文件中。这也可能导致意想不到的问题。考虑:

    //square.h:
    #ifndef SQUARE_H
    #define SQUARE_H
    
    int getSquareSides()
    {
        return 4;
    }
    
    int getSquarePerimeter(int sideLength); // forward declaration 
    #endif
    
    
    //square.cpp:
    #include "square.h"  // square.h is included once here
    int getSquarePerimeter(int sideLength)
    {
        return sideLength * getSquareSides();
    }
    
    
    //main.cpp
    #include "square.h" // square.h is also included once here
    #include <iostream>
    int main()
    {
        std::cout << "a square has " << getSquareSides() << " sides\n";
        std::cout << "a square of length 5 has perimeter length " 
        << getSquarePerimeter(5) << '\n';
    
        return 0;
    }

注意，main.cpp和square.cpp都包含了square.h。这意味着square.h的内容将分别被包含在square.cpp和main.cpp中。

让我们更详细地研究一下为什么会发生这种情况。当从square.cpp中包含square.h时，SQUARE_H将被定义到square.cpp的末尾。这个定义防止square.h第二次被包含到square.cpp中(这是头保护的意义)。然而，一旦square.cpp完成，SQUARE_H就不再被认为是已定义的。这意味着当预处理器在main.cpp上运行时，SQUARE_H最初并没有在main.cpp中定义。

最终的结果是square.cpp和main.cpp都获得了getSquareSides定义的副本。这个程序可以编译，但是链接器会抱怨你的程序有多个标识符getSquareSides的定义!

- **相同的HEADER GUARD出现在不同文件,这些不同的文件彼此无包含关系,导致编译成功但是链接失败**
- 通常告诉你不要在头文件中包含函数定义,非函数的定义则使用HEADER GUARD

#### pragma once

现代编译器使用#pragma预处理器指令支持一种更简单的替代头文件保护形式.作用与头文件保护相同:避免头文件被多次包含。使用传统的头保护，开发人员负责保护头(通过使用预处理器指令#ifndef， #define和#endif)。使用#pragma once，我们请求编译器保护头文件。

对于大多数项目，#pragma工作得很好，许多开发人员现在更喜欢它，因为它更简单，更不容易出错。许多IDE还会在通过IDE生成的新头文件的顶部自动包含#pragma。

然而，由于pragmas并不是c++语言的正式组成部分(并且可能不被一致支持，或者在更深奥的平台上根本不被支持)，其他人(例如Google)仍然建议坚持使用传统的头守卫。

有一种情况，#pragma once通常会失败。如果复制了一个头文件，使其存在于文件系统的多个位置，如果不知怎么地包含了头文件的两个副本，则头文件保护将成功地对相同的头文件进行重复数据删除，但#pragma once不会(因为编译器不会意识到它们实际上是相同的内容)。
