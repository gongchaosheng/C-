## const引用(常量引用)
**常量引用：引用的对象是常量，即对`const`的引用。**
首先声明一点：int和const int可以互相绑定，即
```
int i = 0;
int j = 0;
const int k = 2;

i = k; //正确，const int 可以给int赋值
k = j; //正确 int可以给const int赋值
```
考虑int(const int)和int&、const int&：  
+ 当引用是const类型，绑定的对象既可以是int又可以是const int
```
int i = 0;
const int j = 1;
const int &r1 = i; //正确
const int &r2 = j; //正确
```
+ 当引用不是const类型，绑定的对象只能是int
```
int i = 0;
const int j = 1;
int &r1 = i; //正确
int &r2 = j; //错误
```

## const指针
对于指针来说，有顶层`const`和底层`const`。
+ **顶层`const`** 即指针`p`本身是常量，无法修改指针`p`的值。指针可以看作在底层数据之上，因此是顶层`const`
+ **底层`const`** 即指针`p`指向的对象是常量，此值无法修改。数据在底层，因此是底层`const`
+ **说白了距离p近的是顶层const**
下面来定义一个指针：
```
int i = 0;
const int j = 1;

const int *p1 = &i; //正确，底层const，指针p1指向的对象是常量，p1的类型是int*
int * const p2 = &j; //错误，&j是const int *类型，是底层指针，不能绑定在顶层指针p2上
int * const p2 = &i; //正确，顶层const,指针p2本身是常量，p2的类型是int* const
```
如何分辨这两种指针呢？从指针由内而外看，`p1`先和`*`结合，因此`p1`首先是个指针，继续向外，p1指向的对象是`const int`类型，所以`p1`是个底层`const`。  
`p2`先和`const`结合，说明`p2`首先是个`const`对象，之后和`*`结合，说明`p2`是一个常量指针。  
注意，在拷贝的时候底层`const`对象不能给没有底层`const`的对象赋值。即`p2=p1`是错误的，因为`p2`是一个顶层`const`，而`p1`是底层`const`

## 类中的this指针
```
struct Sales_data{
    std :: string isbn(){return bookNo};

    std::string bookNo;
    unsigned solded = 0;
    double revenue = 0.0;
    double price = 0.0;
};
```
对于成员函数`string isbn(){ return bookNo;}`有隐藏的参数：常量`this`指针，类型为`Sales_data * const`，因为this总是指向定义的那个Sales_data对象，因此它是一个常量
指针，我们不允许修改this指针的值(对象的地址)。此时this是一个顶层const指针，这就意味着我们不能把它绑定到一个底层const对象(常量对象)上，但是有时候我们有这个需求，就需要把
this指针声明成const Sales_data * const类型，这样就可以绑定到常量对象上了。  
C ++中在成员函数的参数列表后添加一个const就可以隐式的将this指针声明为底层+顶层const指针了。
```
struct Sales_data{
    std :: string isbn() const {return bookNo};

    std::string bookNo;
    unsigned solded = 0;
    double revenue = 0.0;
    double price = 0.0;
};
```



