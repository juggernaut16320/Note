## 6.1函数基础

函数function:

- 返回类型
- 函数名
- 形参列表
- 函数体

函数调用的工作:1.实参初始化形参 2.主调函数中断,被调函数执行  
return语句的工作:1.返回return语句的值 2.控制权由被调函数转到主调函数    

**形参数量类型必须匹配**

#### 函数的形参列表

    void f1(){} 隐式定义空形参列表
    void f2(void){} 显式的定义空形参列表
    即使形参类型一样,也必须每个写完整
    int f3(int v1,v2){} 错误
    int f4(int v1,int v2){} 正确

#### 函数返回类型

一种特殊类型是void,表示不返回任何值.大多数类型都可以作为返回类型,但是数组类型和函数类型不行,但可以是指向数组或函数的指针

#### 6.1.1局部对象

CPP中,名字有作用域,对象有生命周期.形参和函数体内定义的变量称为局部变量,局部变量仅函数作用域可见

**自动对象**  
到达定义的块末尾自动销毁,只存在于块执行期间的称为自动对象.

**局部静态对象**  
在函数结束后不希望销毁,而是在程序结束后,static类型,内置类型的局部静态对象初始化为0

#### 6.1.2函数声明

函数只能定义一次,但是可以声明多次,如果一个函数永远不会用到,可以只声明不定义,函数声明可以忽略形参,但是写上可以增加代码可读性

#### 6.1.3分离式编译

即程序分隔到几个文件里

## 6.2参数传递

每次调用函数都会重新创建形参,并用传入实参对形参初始化  
形参类型决定了形参实参的交互方式,形参是引用类型时候绑定实参(引用传递called by reference),否则将实参拷贝后的值传给形参(passed by value)

#### 6.2.1传值参数

指针形参,拷贝的是指针的值

    void reset(int *ip){
        *ip=0 改变了ip所指对象的值
        ip=0 只改变了ip的局部拷贝,实参并未改变
    }

c常用指针访问函数外的对象,**cpp建议用引用类型的形参代替指针**

#### 6.2.2传引用函数

    void reset(int &i){
        i=0 改变了i
    }

我们可以直接传递对象而无需传递对象的地址

 **使用引用避免拷贝**  
 拷贝大容量的类类型对象或者容器对象效率低,甚至有的类型不支持拷贝,这时候只能通过引用访问,比如string对象可能非常长  

    bool isShorter(const string &s1,const string &s2){
        return s1.size()<s2.size();
    }

#### 6.2.3const形参和实参

形参的初始化与变量的初始化类似,可以用非常量初始化一个底层const对象,但是反过来不行,同时一个普通引用必须用同类型的对象初始化

    int i=42;
    const int *cp=&i;正确,但cp不能改变i
    const int &r=i;正确,但r不能改变i
    const int &r2=42; 正确
    int *p=cp; 错误,p和cp类型不匹配
    int &r3=r;错误,类型不匹配
    int &r4=42;错误,不能用字面值初始化一个非常量引用

将同样的规则应用到参数传递上可以得到以下

    int i=0;
    const int ci=i; ci是一个常数整形
    string::size_type ctr=0;
    reset(&i) 调用类型是int*
    reset(&ci) 错误,不能用指向const int对象的指针初始化int
    reset(ci) 错误,不能把普通引用绑定到const对象ci上
    reset(42) 错误,不能把普通引用绑定到字面值上
    reset(ctr) 错误,类型不匹配,ctr是无符号类型

**尽量使用常量引用**  
函数不会改变的形参定义成普通引用是常见错误,使用引用而非常量引用会限制函数所能接受的实参类型,而且会发生其他错误

#### 6.2.4数组形参

数组的两个性质:1.不允许拷贝数组2.使用数组会将其转化为指针  
因为不能拷贝数组,所以无法以值传递使用数组参数,因为传递的是指针,所以为函数传递数组实际上是指向首位的指针

    void print(int (&arr)[10])//括号不能省略，不然不是数组的引用而是引用的数组

## 6.3返回类型和return语句

终止当前函数并将控制权转移给调用该函数的函数

#### 6.3.1无返回值函数

没有返回值的return语句只能用在void类型函数中,return也不是必须,因为函数末尾会隐式return,如果函数中return就类似于break

#### 6.3.2有返回值函数

有返回值的函数必须有return语句,此外注意循环中的return后出循环还要加上return,很多编译器不检查这类错误  
return语句返回值的类型必须与函数的返回类型相同,或者能隐式的转换成函数的返回类型

**值是如何返回的**  
返回值的方式和初始化一个变量或形参的方式完全一样:返回的值用于初始化调用点的一个临时量,该临时量就是函数调用的结果 

    string make_plural(size_t ctr,const string &word,const string &ending)
    {
        return(ctr>1)?word+ending:word
    }

该函数返回类型是string,意味着返回值拷贝到调用点,返回的是word的一个副本或者一个未命名的临时string对象,该对象内容是word和ending的和

    const string &shorterString(const  string &s1,const string &s2)  
    {
        rerturn s1.size()<s2.size()?s1:s2;
    }

如果函数返回引用,那么该引用仅是它所引对象的别名.这里形参和返回类型都是const string的引用,不管调用函数还是返回结果都不会真正拷贝string对象

**不要返回局部对象的引用或指针**  
函数完成后占用内存释放掉,因此局部变量的引用不再指向有效的内存区域

    严重错误,该函数试图返回局部对象的引用
    const string &manip(){
        string ret;
        if(!ret.empty())
            return ret; 错误,返回局部对象的引用
        else
            return "empty" 错误,empty是一个局部临时量
    }

**返回类类型的函数和调用运算符**  
调用运算符的优先级和点运算符和箭头运算符相同而且符合左结合律.因此函数返回指针,引用或类的对象,可以用函数调用的结果访问结果对象的成员

    auto sz=shortString(s1,s2).size()

**引用返回左值**  
函数的返回类型决定函数调用是否是左值.调用一个返回引用的函数得到左值,其他返回类型得到右值

    char &get_val(string &str,string::size_type ix){
        return str[ix];
    }
    int main(){
        string s("a value");
        get_val(s,0)="A" 给调用的结果赋值
        cout<<s<<endl
    }

但是如果是常量引用,不能给调用的结果赋值

**列表初始化返回值**  

    vector<string> process(){
        if(expected.empty())
            return {}; 返回空的vector对象
        else if(expected==actual)
            return{"functionX","Okay"}; 返回列表初始化的vector对象
        else
            return{"functionX","expected","actual"}
    }

**主函数main的返回值**  
主函数和void返回类型的函数一样,可以省略return,编译器会隐式插入返回为0的表示执行成功

**递归**  
如果一个函数调用了自身,不管调用是直接还是间接的,都称函数为递归函数

#### 6.3.3返回数组指针**

因为数组不能拷贝,所以函数不能返回数组（数组名实际上是数组的第一个地址，拷贝会造成会使两个数组名指向同一内存空间，造成内存冲突以及内存泄露）,但可以返回数组的指针或引用  

    typedef int arrt[10] arrt是一个别名,表示类型是含有十个整数的数组
    using arrt=int[10] 上句话的等价声明
    arrt* func(int i) 返回一个指向有十个整数的数组的指针

**声明一个返回数组指针的函数**  

- func(int i)表示调用函数需要一个int类型的实参
- *func(int i)表示可以对函数调用的结果解引用
- (*func(int i))[10]表示解引用func的调用得到一个大小10的数组
- int (*func(int i))[10]表示数组中的元素是int类型

**使用尾置返回类型(trailing return type)**  
c11中有一种简化上述func声明的方法,任何函数都可以使用尾置返回,不过对复杂返回类型函数来说更合适,在本应出现返回类型的地方放一个auto

    auto func(int i)->int(*)[10]

**使用decltype**  
如果知道返回的是哪个数组,可以用declytype关键字声明返回类型

    int odd[]={1,3,5,7,9}
    int even[]={0,2,4,6,8}
    decltype(odd) *arrPtr(int t){
        return(i%2)?&odd:&even;
    }

注意decltype类型不负责把数组类型转化为指针,所以需要加上*

## 6.4函数重载

重载(overload)函数:同一作用域的几个函数名字相同但形参列表不同,编译器会根据情况推断是哪个函数,以达到简化命名  
**定义重载函数**  
一种典型的数据库应用,根据电话,名字,账户号码查询 

    Record lookup(const Account&)
    Record lookup(const Phone&)
    Record lookup(const Name&)

- 不允许两个函数除了返回类型其他均相同
- main不能重载
- 形参的名字仅仅帮助记忆,声明中其他相同但形参名字不同视为同一函数声明  

**重载与const形参**

    顶层const不影响传入函数的对象
    Record lookup(Phone )
    Record lookup(const Phone ) 重复声名

如果形参是某种类型的指针或者引用,通过区分常量或非常量可以重载,此时const是底层

    Record lookup(Account &)
    Record lookup(const Account &)新函数

**const_cast与重载**  

    const string &shorterString(const string &s1,const string &s2)
    {
        return  s1.size()<=s2.size()?s1?s2;
    }

该函数参数和返回类型都是const string,可以对两个非常量的string类型调用该函数,返回的结果仍然是const string的引用

    string shorterString(string &s1,string &s2){
        auto &r=shorterstring(const_cast<const string&>(s1)
        ,const_cast<const string&>(s2))
        return const_cast<string&>(r)
    }

这个版本的函数将实参强制转换成对const的引用

**调用重载的函数**  
调用重载函数会进行函数匹配

- 找到匹配
- 找不到匹配,出错
- 多于一个匹配,二义匹配,出错

#### 6.4.1重载与作用域

内层声明会覆盖外层声明,所以在同一作用域才会重载

## 6.5特殊用途语言特性

#### 6.5.1默认实参

    string screen(sz ht=24,sz wid=80,char backgrnd=" ")
    调用
    window=screen(66)
    window=screen(66, ," ")
    
    添加:注意一个形参赋予默认后后续无法修改
    string screen=(sz,sz,char=" ")
    string screen=(sz,sz,char="#") 错误,重复声明
    string screen=(sz=24,sz=80,char) 正确,添加默认实参
    
    局部变量不能作为默认实参,表达式符合类型转换均可

#### 6.5.2内联函数和constexpr函数

函数调用比直接实现函数功能要慢一点  

 **内联函数可以避免函数调用的开销**  
 函数的前面加上inline可以声明成内联函数,适用于优化规模小,流程直接,频繁调用的函数  

**constexpr函数**  
能用于常量表达式的函数,必须有且仅有一句return,函数的返回值和所有形参类型都得是字面值类型,如果结果不是常量表达式,编译器会报错

    constexpr size_t scale(size_t cnt){return new_sz()*cnt}
    
    int arr[scale(2)] 正确 scale(2)是常量表达式
    int i=2 i不是常量表达式
    int a2[scale(i)] 错误:scale(i)不是常量表达式

**把内联函数和constexpr函数放在头文件**  
和普通函数一样,可以多次定义,但是定义内容必须一致,所以通常定义在头文件中.

#### 6.5.3调试帮助

程序可以放一些用于调试的代码,发布时候屏蔽掉,这种方法用了两项预处理功能:assert和NDEBUG  

**assert预处理宏**  

    assert(expr)

- 求值,真啥也不做,假输出信息并终止程序
- assert宏定义在cassert头文件中
- 和预处理变量一样,宏的定义必须唯一,含有该头文件不能再定义assert对象
- assert宏常用于检查不能发生的条件,仅仅是调试程序的辅助手段,不能代替运行时候的逻辑检查  

预处理器定义的几个调试程序常用的名字:

- __func__,存放函数名字
- __FILE__,存放文件名字符串字面值
- __LINE__,存放当前行号的整形字面值
- __TIME__,编译时间
- __DATE__,编译日期

**NDEBUG预处理变量**  
assert行为依赖一个名为NDEBUG的预处理变量,如果定义了NDEBUG,则assert什么也不做,默认状态下没有定义NDEBUG,此时assert将执行运行时检查.可以使用一个#define 语句定义NDEBUG,从而关闭调试状态  
除了用于assert,也可以使用NDEBUG编写自己的条件测试代码:

    void print(const int ia[],size_t size){
        #ifdef NDEBUG  
            cerr<<__func__<<":array size is"<<size<<endl;
        #endif
    }

## 6.6函数匹配

大多数情况下容易确定重载函数的选择,但如果几个重载函数形参数量相等,以及某些形参的类型可以由其他形参转换而来的话

    void f()
    void f(int)
    void f(int,int)
    void f(double,double=3.14)
    f(5.6)  调用void f(double,double)

函数匹配第一步是确定**候选函数**集合,具备两个特征:1.同名 2.声明在调用点可见 .这个例子中有四个名为f的候选函数  
第二步确定**可行函数**,具备两个特征:1.形参实参数量一致 2.形参实参类型一致,或者能够转换  
第三步寻找最佳匹配,形参实参类型越接近匹配越好,如果多个符合会出现二义性报错  
**调用重载函数应该尽量避免强制类型转换**

6.6.1实参类型转换
为了确定最佳匹配,实参到形参类型转换划分为以下几个等级:

1. 精确匹配:
   - 实参形参类型相同
   - 实参从数组类型或函数类型转换成对应的指针类型
   - 向实参添加顶层const或者从实参删除顶层const
2. 通过const转换实现的匹配
3. 通过类型提升实现的匹配
4. 通过算术类型转换(所有算术转换级别一样)
5. 通过类类型转换实现的匹配

## 6.7函数指针

函数指针指向的函数而非对象
    bool (*pf)() 一个函数指针
    bool *pf() 错误,一个返回值bool指针的函数

#### 使用函数指针

函数名当作值来使用,自动转化成指针

    pf=lengthcompare pf指向函数
    pf=&lengthcompare 等价的赋值语句

可以直接调用

    bool b1=pf("hello","goodbye") 调用lengthcompare函数
    bool b2=(*pf)("hello","goodbye") 另一个等价的调用

#### 函数指针形参

形参是函数类型,实际上可以直接当成指针使用,定义形参函数指针类型很繁琐,建议使用decltype

    void usebigger(const string &s1,const string &s2,
    bool pf(const string&,const string&))
    第三个形参是函数,会自动转换成指向函数的指针
    以下是等价的声明
    void usebigger(const string &s1,const string &s2,
    bool (*pf)(const string&,const string&))

#### 返回指向函数的指针

声明一个返回函数指针的函数,最简单的办法:
    using F=int(int*, int) F是函数类型,不是指针
    using PF=int(*)(int*, int) PF是指针类型
