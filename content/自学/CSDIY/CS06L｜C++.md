---
publish: true
created: 2026-03-12T02:16:58.083Z
modified: 2026-09-14T11:32:37.049Z
---

# 学习进度

- 4.23 lecture ppt到11.5，hw写完了0-3

# 编程错的

- 记得要引用，不然只会在副本上操作（我是呆瓜）
- 单引号只能表示char（单个的字符），双引号才能字符串
- 要读文件需要使用fstream，如果使用stringstream会解析成字符串；可以读了之后用文件流的.str()来转化
- C++!=python，不能在函数里面写另外一个函数，不过可以使用lambda函数达到类似的效果（注意变量生效范围！）
- 使用默认构造函数构造实例的时候都不用加括号
- 在头文件声明函数的时候不用加花括号
- auto不能识别到void，此时就会报错
- 要把class的声明写在.h头文件里面（变量和函数声明），然后在具体的实现写函数的具体代码
- 这个测试函数很呆 set了什么set函数后面那串要和变量名完全一样（hw3）
- 在不是template的普通类里面头文件不能include cpp文件，不然会看成循环定义
- 直接用“”会被判作const char数组 需要用std::string包一下才会变成std::string
- string不是自带的 需要include一下

# 英语

spoiler：单独的一个词表示 剧透一下
tradeoff：做出的一些权衡

# 可能剩的

多态 构造和析构

# 点

- 如果是空格分词可以考虑stringstream
- string连接直接使用+=就可以了

# 基本语法教学

静态声明变量类型：容易编译器发现错误
field：字段值，变量的属性
函数重载（overloading）：可以有相同名字，但是参数不同的函数，最后会按参数编译器判定是哪个函数
C不允许这个

```cpp
Student s{“Ethan”, “CA”, 30};
// note that order is based on the original struct order!
```

直接这么初始化就好啊（叫做Uniform Initialization，似乎是新版本的）
有无等于号都可以
Using {} for vector creates an initializer\_list会调用一个别的函数
struct默认属性public，class默认属性private

## template

pair：随便输类型，两个
哦 因为不能返回两个，所以打包成一个pair对象来返回
可以用first和second来访问
tuple：任意多个随便变量，但是不是很常用
get？
template：编译器帮助填入任意类型的变量
auto：变量类型被编译器推导
比较老的版本函数返回时不能使用auto
尤其减轻了要打一大串向量的时候（
![[图源/图源1-7.30/Pasted image 20260312110917.png|300]]
可以直接这么用auto赋值

### vector

std::vector\<int> v(n, k);：就是初始化一个长度为n的整数向量，初始化时每个值都是整数k
v.push\_back(k);：在v的最后增加一个数值k

### STL：standard template library

就是一些生成的template函数
比较

- Vector\<T>
- Stack\<T>, Queue\<T>
- Map\<T>, Set\<T>
- count\_if, find, sort

## reference

通过引用来完成真实的变量改变（函数里面变量传递）
只能引用变量
～l-values
const只能被const ref来引用，且不能改变
本来不是const设成const的ref了也不是const
在auto的时候也需要加const和&，没有那么自动（因为默认是非静态\&copy的形式）

```cpp
int& front(const std::string& file) {
std::vector<int> vec = readFile(file);
return vec[0];
}
int main() {
front("text.txt") = 4; // undefined behavior
return 0;
}
```

不能成功，因为已经被销毁了

## stream

input/output的抽象
转换data和data的字符串表达式

### output stream

type：std::ostream
变成字符串然后送到输出

### input stream

type：std::istream
一直读字母直到遇到空格
读入整行：用getline：读到换行符为止
类型不符合就会触发fail bit，需要用input.clear();来恢复输入流状态，可以继续进行

### string stream

把string看作一个stream来使用：转换

### internals

#### buffering

一个字一个字读太慢了，故一起读进来然后统一操作
提前输出：

```cpp
stream << std::flush; // flush what we have so far
stream << std::endl; // flush with newline
// this is equivalent to: stream << “\n” << std::flush;
```

这个是提前输出到面板，不是消息丢掉了
std::cerr, which is not buffered：错误报错需要实时输出，避免丢失重要信息

#### state bits

Good bit: whether ready for read/write
Fail bit: previous operation failed, future operations frozen
EOF bit: previous operation reached end of file
Bad bit: external integrity error：底层io出错，比如硬盘/文件损坏

#### chaining

<<和>>都是函数，具体是operator<<和operator>>，读入std::ostream& out, const std::string& s然后进行对应操作
![[图源/图源1-7.30/Pasted image 20260319113518.png]]

## container

operator\[] does not perform bounds checking：不会检查下标，所以如果超标访问只会默默的出bug（）
std::vector does not have a push\_front function：意思是如果在vector的头插入元素的话慢的像闪电一样
std::deque provides fast insertion anywhere：deque有push\_front和pop\_front，意思是双端队列
size：指本来的大小
capacity：可能有的大小
list：任何地方插入都很快，但是不能index：每个节点要额外储存前后两个指针
stability：容器进行增删改的时候是否原来保存的指针/迭代器/引用是否会指向正确的元素

```cpp
std::vector<int> vec;
vec.reserve(1000000);
for (size_t i = 0; i < 1000000; ++i) {
vec.push_back(i);
}
```

预留了1000000个位置，然后就不用经历重复的重开和复制
hrink\_to\_fit：请求释放多余的预留空间
Call empty(), rather than check if size() == 0.：因为性能会好一点
Don't use vector\<bool> ("noble failed experiment")：因为之前试图把8个bool类型值插入到同一个字节变量里，导致

1. 不是容器：没法单独访问一个bit的地址
2. 并发危险，可能多个线程同时更改一个字节
3. 破坏了期望，会得到临时代理结果

### wrapper

改变外界交互的方式
wrapper不自毁：

1. 🈚️权限
2. 比较危险，如果之后访问会出错
3. 一般会自动清理（生命周期结束后自动调用析构函数

### adaptor：套壳助手

- **`std::stack`**（栈）：先进后出（LIFO）。默认套在 `deque` 上。
- **`std::queue`**（队列）：先进先出（FIFO）。默认套在 `deque` 上。
- **`std::priority_queue`**（优先队列）：按优先级出队。默认套在 `vector` 上。
  不能调用at（访问比较底层的接口
  ![[图源/图源1-7.30/Pasted image 20260402100857.png]]

### Associative Containers

set：储存唯一&有序的数据（自动按内置的比较函数排序）
map：m.insert({k, v});
set or map需要（键值）类型是可比较的
结构化绑定的类型名要用auto
multimap/set：没什么用
unordered set比set快：直接使用哈希表
应用const &：引用可以避免复杂的复制（开销较高）；const可以使得数据不变（更放心）

## iterator：对container操作的

it = c.begin();
++it;：比it++快，因为it++必然创造一个临时的it副本然后再修改，这创造了多余开销
(it == c.end())
map.end()返回最后一个元素之后的那个（是未定义的元素）
用iterator增加来变换不能变换的container本身
input iterator：只能从那里读数据的iterator
output iterator：只能从那里写数据的iterator

## template function

应用生成函数！渲染类template
![[图源/图源1-7.30/Pasted image 20260402200117.png]]
但是渲染的范围仅限于这一函数内

- 前后两个可能不一样&字符串可能会被渲染成字符串的指针比较
- 所以使用两个变量类型名并且让compiler来决定到底哪个是对的
  所以最终变成

```cpp
template <typename T, typename U>
auto my_min(const T& a, const U& b) {
return a < b ? a : b;
}
my_min(4, 3.2);
// this returns 3.2
```

template function只是一种实例化的生成函数：指应用函数的时候通过template把一种具体类型的函数赋到一个具体的值上

## concept lifting

predicate：谓词，用于过滤
用\[]捕获外部变量
scope problem：就是指作用域

```cpp
auto lambda = [capture-values](arguments) {
return expression;
}
[x](arguments)
// captures x from surrounding scope by value
[x&](arguments)
// captures x from surrounding scope by reference
[x, y](arguments)
// captures x, y by value
[&](arguments)
// captures everything by reference
[&, x](arguments)
// captures everything except x by reference
[=](arguments)
// captures everything by copy
```

find/search：返回iterator
struct默认面向public，class默认面向private，其他没了
一般来说不会使用public的variable
用namespace进行逻辑上的分组
也可以把类，函数功能和具体实现分在3个不同的文件里面来完成调用的分离。可以通过链接来编译
![[图源/图源1-7.30/Pasted image 20260417153329.png]]
这里的value\_type和TYPE都是占位符，其中value\_type是默认标准的
![[图源/图源1-7.30/Pasted image 20260417161919.png]]
但这个事实上不可行，因为没有具体的函数类型，此时声明编译的时候不会生成具体的函数，这样在编译的时候就会报错
一般解决方法

- 直接在头文件里面写函数的具体内容（一般不会）
- 在.h末尾#include "vector.cpp"
  这样的话在编译的时候也不用把vector.cpp和vector.o加进去

```cpp
using charvctr = std::vector<char>;
```

alias就这样
![[图源/图源1-7.30/Pasted image 20260417170707.png]]
逐个纠正后的最终结果
`const` 对象只能调用标记为 `const` 的成员函数
T& operator\[ ]\(int i)定义重载函数。
const reference只是一个别名
![[图源/图源1-7.30/Pasted image 20260423112948.png]]
这不对！因为vector.cpp的函数\&h文件的size变量前面都没有加const
const元素只能被其他含有const的东西调用/ref
必须要加const，以免被“不小心”地改动；reference也要加const
这里就是说性能优化比较方便 随便改，因为不会不小心改动了数据
const\_iterator不是const的，只是不支持对象更改
但是const iterator是const的，不能++或者改变or so

## 运算符重载

通过member和non-member function来实现
在编译器检测到运算符的时候会自动寻找有无对应的member function和全局function
在类的public里面声明函数friend属性，则允许访问私有变量。在正式写的cpp里面不需要再次授权
![[图源/图源1-7.30/Pasted image 20260507102940.png]]

- 哦这里传入了std::ostream & out，所以out不需要再次定义。
- 这里最后还是需要return out：这样子就可以重复调用<<，连续输出投到cout
- 别乱用到不符合符号的运算符里面，比较strange

## special member function

编译器自动生成的

- default construction：啥参数都没
- copy construction：复制到啥都没开始建
- copy assignment：复制到已有的上面
- destruction：超出范围时（when it out of scope）的自动销毁函数
  {}会检查是否在做危险动作，比如转换类型（可能导致精度下降
  new出来的对象储存在堆上，需要手动释放
  ![[图源/图源1-7.30/Pasted image 20260507110926.png]]
  直接使用initializer list
- 性能更快（因为不需要二次赋值）
- 在一些需要一次赋值好的情形只能用这个（const，引用的对象：重新赋值的时候只会更改对象的内容，而不会更换对象）
  哦 禁止向std的运算符重载
  ![[图源/图源1-7.30/Pasted image 20260507113454.png]]
  这里不行，因为会deconstruct两次｜导致未曾设想的错误
  \_elems(other.\_elems) { }这是不能成功复制的，因为只复制了指针，还是指向同一个位置
  \=号重载时注意self-reassignment
  new的时候要注意free掉已经建好的变量
  返回this：也是为了连续赋值（比如a=b=c=10）
  Copy constructor：从0开始clone
  Copy assignment：旧瓶装新酒，要先把本来的delete掉
  如果重新定义了constructor，那也要重新定义assignment和destructor
