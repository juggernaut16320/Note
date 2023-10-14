**类的三大特性**:

- 封装(保护,功能分离) 
- 继承
- 多态

## 7.1定义抽象数据类型

#### 7.1.2定义改进的sales_data类

    struct sales_data(){
        新成员:关于sales_data的操作
        std:string isbn() const{return bookNO}
        sales_data& combine(const salesdata&)
        double avg_price() const
    
        数据成员相对无变化
        std::string bookNo
        unsigned units_sold=0
        double revenue=0.0
    }
    sales_data的非成员函数
    sales_data add(const salesdata&,const salesdata&)
    std::ostream &print(std::ostream&,const sales_data&)
    std::istream &read(std:istream&,sales_data)

- this是一个常量指针,不能改变,一般只在类内部使用   

**引入const成员函数**  
**不对成员做任何修改,参数列表后面加上const**,作用是修改隐式this指针的类型.  
this默认是指向类类型非常量版本的常量指针,不能在一个常量对象上调用普通的成员函数

**类外定义函数**  
成员函数必须在类里面声明,可以加上类作用域在类外定义

    double sales_data::avg_price() const{}

**定义一个返回this对象的函数**

    sales_data& sales_data::combine(const sales_data &rhs){
        units_sold+=rhs.units_sold; rhs的成员加到this对象的成员上
        revenue+=rhs.revenue;
        return *this;
    }

#### 7.1.3定义类相关的非成员函数

需要定义一些辅助函数比如read,print,这些属于类的接口的组成部分,但不属于类本身.
如果函数在概念上属于类但不定义在类中,则它应与类声明在同一个头文件.这种方式下,使用接口的任意部分都只需要引入一个文件  

**定义read和print函数**

    istream &read(istream &is,sales_data &item){
        double price=0;
        is>>item.bookNo>>item.units_sold>>price;
        item.revenue=price*item.units_sold;
        return is;
    }
    
    ostream &print(ostream &os,const sales_data &item){
        os<<item.isbn()<<" "<<item.units_sold<<" "
            <<item.revenue<< " "<<item.avg_price();
        return os;
    }

两个函数各自接受一个IO类型的引用作为其函数,IO类型属于不能拷贝的类型,只能通过引用传递,且因为读取或者写入会改变流的内容,因此是普通引用而非常量引用.  

#### 7.1.4构造函数

- 类的的初始化过程,名字同类名,参数(可能为空)和函数体(可能为空)
- 不能声明为const

**默认构造函数**  
如果没有给类编写构造函数,编译器自动生成默认构造函数

- 如果规定了初始值,用初始值
- 否则,默认初始化该成员

**某些类不能依赖于合成的默认构造函数** 

1. 定义在块中的内置类型和复合类型(比如数组和指针)默认初始化后将是未定义的,该准则同样适用于默认初始化的内置类型成员
2. 编译器不能为某些类合成构造函数,例如类中包含其他类型类成员并且这个成员没有默认构造函数,编译器无法初始化该成员  

**定义sales_data的构造函数**  
使用以下四个参数,定义四个不同的构造函数

- 一个istream&,从中读取一条交易信息
- 一个const string&,表示ISBN编号,一个unsigned,表示售出的图书数量,以及一个double,表示图书的售出价格   
- 一个const string&,表示ISBN编号,编译器将赋予其他成员默认值
- 一个空参数列表(即默认构造函数),正如刚刚介绍的,既然我们已经定义了其他构造函数,那么也必须定义一个默认构造函数

    struct sales_data{
        sales_data()=default;
        sales_data(const std::string &s) :bookNo(s){}
        sales_data(const std::string &s,unsigned n,double p):
            bookNo(s),units_sold(n),revenue(p*n){}
        sales_data(std::istream &)
        之前已有的其他成员
        std::string isbn() const{return bookNo}
        sales_data& combine(const sales_data&)
        double avg_price() const
        std::string bookNo
        unsigned units_sold=0;
        double revenue=0.0
    }

- 需要默认,参数列表后加=default,在类的内部则默认构造函数内联,在外部相反
- 冒号和花括号之间称为**构造函数初始值列表**  

**类的外部定义构造函数**  

    类的外部定义函数,必须声明该构造函数是哪个类的成员
    sales_data::sales_data(std::istream &is){
        read(is,*this)
    }

#### 7.1.5拷贝,赋值和析构 (完整见ch13)

类还需要控制拷贝,赋值和销毁对象时发生的行为,如果不去定义的话编译器会合成这些操作

- 对象在几种情况下会被拷贝:初始化变量以及以值的方式传递或者返回一个对象
- 赋值运算会发生对象的赋值
- 对象不再存在执行销毁  

**某些类不能依赖合成的版本**  
编译器虽然能合成拷贝,赋值,销毁的操作.但当类需要分配类对象以外的资源,合成的版本常常会失效,管理动态内存的类通常不能依赖上述操作的合成版本.  
所以,很多需要动态内存的类使用vector或者string对象管理必要的存储空间,能避免分配和释放内存带来的复杂性

## 7.2访问控制与封装

- 定义在public后可以被整个程序访问,public定义类的接口
- private可以被类的成员函数访问,不能被使用该类的代码访问

    class sales_data{
        public:
        private:
    }

**class和struct**  
都可以定义类,唯一区别是class碰到第一个访问说明符之前默认private,struct默认public

#### 7.2.1友元（非声明仅权限开放，使用前要声明）

允许其他函数或者类访问它的非共有成员,方式是成的友元(friend).声明语句前面增加friend  
友元的声明仅仅指定访问权限,而非通常意义上的函数声明,如果希望类的用户能调用某个友元函数,就必须在友元声明外对函数再做一次声明

## 7.3类的其他特性

类型成员,类的成员的类内初始值,可变数据成员,内联函数成员,从成员函数返回*this,如何定义以及使用类类型  

- 内联函数,类内自动(隐式)inline,小规模类外手动inline
- 重载:参数数量和类型上不同
- 可变数据成员mutable在const对象内也可以修改,可以跟踪常量成员函数调用次数

    class Screen{
        public:
            void some_member()const;
        private:
            mutable size_t access_ctr;
    };
    void Screen::some_member()const{
        ++access_ctr;
    }

- 基于const的重载

    class Screen{
        public:
            Screen &display(std:ostream &os){
                do_display(os);
                return *this;
            }
            const Screen &display(std:ostream &os){
                do_display(os);
                return *this;
            }
        private:
            void do_display(std:ostream &os)const{
            os<<contents
            }
    }

- 返回类型如果定义在类的内部,则无法被外部使用,需要加上作用域

    Screen::pos Screen::size()const{
        return height*width;
    }

- 函数重载与友元:虽然重载函数名字相同,但是仍是不同函数,设置为友元需要一个个设置
- 友元本身并非常规声明,函数在使用前必须常规声明

## 7.4类的作用域

一个类就是一个作用域,作用域外只能使用对象,引用或指针使用成员访问运算符

    Screen scr(ht,wd,' ')
    Screen *p=&scr
    char c=scr.get();
    c=p->get

#### 作用域和定义在类外的成员

一个类就是一个作用域解释了 为什么类外定义成员函数必须同时提供类名和函数名,类的外部成员函数被隐藏起来

#### 名字查找与类的作用域

- 名字查找:先从所在块中查找,没有则查找外层作用域,最终无则报错
- 类的定义:编译成员的声明,直到全部类可见后编译函数体
- 返回类型出现在类名之前,实际上是位与类的作用域之外
- **类型名**:一般内部名字可以覆盖外部名字,但如果类成员使用了某外部名字,则不能重定义该名字

## 7.5构造函数再探

构造函数两个版本分别是赋值和初始化,有时必须将其初始化的情况

1. 成员是const或者引用,必须将其初始化
2. 成员属于某种类类型但是没有定义构造函数  

**构造函数初始化的顺序与成员在类中出现的顺序一致,而非构造函数列表中初始值的前后**

1. 最好令构造函数初始值的顺序与成员声明的顺序一致
2. 尽量避免用某些成员初始化另一些成员

c11委托构造函数,自己的构造委托给其他构造函数  

    class Sales_data{
        //非委托构造函数使用实参初始化成员
        Sales_data(std::string s,unsigned cnt,double price)  :
            bookNo(s),units_sold(cnt),revenue(cnt*price){}
        //其余构造函数全部委托给一个构造函数
        Sales_data():Sales_data("",0,0){}
        Sales_data(std:string s):Sales_data(s,0,0){}
        Sales_data(std:stream &is):Sales_data
            {read(is,*this)}
    
    }

#### 7.5.4隐式类类型转换

- 单个实参构造函数会隐式向类类型转换,不过该转换只会转换一次,如果运用了两次转换规则则会出错

    //错误99-999转string转sales_data
    item.combine("99-999")
    item.combine(string("99-999"))//正确
    item.combine(sales_data("99-999"))//正确

#### 抑制构造函数隐式转换

    explicit Sales_data(std::istream&)
    //此时上述两种方法不能通过编译

#### explicit函数只能用于直接初始化

    Sales_data item1(null_book)//正确,直接初始化
    Sales_data item2=null_book.
    //错误,不能将explicit函数用于拷贝形式的初始化过程

#### 为转换显式地使用构造函数

编译器不会将explicit的构造函数用于隐式转换,但是可以如下显式强制转换

    //正确,实参是一个显式构造的对象
    item.combine(Sales_data(null_book))
    //正确,static_cast可以使用explicit的构造函数
    item.combine(static_cast<Sales_data>(cin))

#### 7.5.5聚合类

当一个类满足以下条件,我们说他是聚合的

- 所有成员都是public
- 没有定义任何构造函数
- 没有类内初始值
- 没有基类,也没有virtual函数

    struct Data{
        int ival;
        string s;
    }//聚类举例
    Data vall ={0, "Anna"}//初始化

聚类有三个缺点:

- 要求所有成员public
- 初始化的任务交给了用户,过程冗长且容易出错
- 添加或者删除一个成员后,所有的初始化语句都需要更新
  
  #### 字面值常量类
  
  数据成员都是字面值类型的聚合类是字面值常量类.如果一个类不是聚合类,但是符合下列要求,则也是一个字面值常量类
- 数据成员都必须是字面值类型
- 类必须含有一个constexpr构造函数
- 如果一个数据成员含有一个类内初始值,则内置类型成员的初始值必须是一条常量表达式,或者如果成员属于某种类类型,初始值必须使用成员自己的constexpr构造函数
- 类必须使用析构函数的默认定义
  
  ## 7.6类的静态成员（共享数据，减少存储，无需创建实例便可访问）
1. 所有类之间共享并只存在一个副本,外部可通过作用域和类名访问
2. 静态成员函数不绑定任何对象，因此不使用this，不能构造函数初始化，同时不能使用const
3. 一般在类外用类名和作用域初始化,类内定义必须使用常量或者常量表达式
