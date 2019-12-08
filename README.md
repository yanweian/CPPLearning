# CPPLearning
Record of learning CPP using << Accelerated C++ >>.

[cpp 参考网站（中文）](https://zh.cppreference.com/w/首页)

## 使用字符串
- `string s(int num,char c)` 构造一个由num个重复字符c所组成的字符串s
- `std::cin>>v` 表达式会忽略任何在标准输入流中的空白字符
---
## 循环和计数
### `std::string::size_type col`
- 我们使用`std::string::size_type`类型来保存一个字符串的字符个数，因为我们要确保 `col` 足以容纳字符串的字符个数——不管这个数字有多大<br>
- **无论何时，只要我们想要定义一个变量来保存特定数据结构的大小，就应该使用库为特殊用途而定义的类型**

### 逻辑运算符
- 关系运算符的优先级比算数运算符低
- 短路求值 ：`||` 和 `&&`

### `using std::cout`
- `using` 出现在花括号中，作用域即局限在花括号中

### 计数
- 之所以从0开始计数，是因为：`[m,n)`的区间包含`n-m`个数，然而`[m,n]`的区间中则有`n-m+1`个数。前者显得更直观
- 空区间表示:非对称表示为`[n,n)`;对称表示为`[n,n-1]`;如果一个区间的末尾比开头小，编程过程中会遇到无尽的麻烦
- 结束条件可以设置为`r!=rows`，结束时，可以得到`r==rows`

### 普通算数转换规则
- 较小的类型 会被转换为 较大的类型
- 有符号类型 会被转换为 无符号类型
- 算数值 会被转换为 布尔值

### 类型
- `unsigned` 修饰符，表示非负数
- `short` 保存至少16位的整数类型
- `long` 保存至少32位的整数类型
- `long long` 保存至少64位的整数类型
- `size_t` 无符号整数类型，`sizeof()`的返回类型，可以保存任何对象的长度

---
## 使用批量数据
### 计算学生成绩
- 浮点类型一般使用`double`，没必要使用`float`：现代计算机，内存足够，`double` 更精确且不会比`float`慢
- `cin>>mid>>final;` 等价`cin>>mid;cin>>final;` 左结合
- ```c++
  cout<<"Hello, "
        "World!";
  ```
  等价于
  ```c++
  cout<<"Hello, World!";
  ```
  如果两个以上字符串直接量仅仅是被空白符分隔开的话，那么这些字符串直接量会被自动连接到一起
- ```c++
  int cout=0;
  double sum=0;
  ```
  数值 `0` 的类型为`int`型，用它来对`sum`进行初始化时，需要将其转换为`double`类型，如果使用`0.0`则避免了转换操作。
  
  ***但其实没有必要这样做：任何一个功能完善的系统环境都会在编译期间进行这个转换操作，因此并不会带来运行时的额外开销***
- 如果我们不初始化内部类型的局部变量，那么这些变量就是未定义的。许多系统不会对这种情况进行检测，并且允许程序访问这些变量，导致极易产生冲突和错误：存储在内存中的随机信息几乎从来不会是一个正确的值——它经常会是一个对类型无效的值
- `setprecision(int)`，给控制器为流的后继输出设置了一个特定的有效位数
  
  我们需要将精度重置为修改前的精度：
  ```c++
  streamsize prec=cout.precision();
  cout<<setprecision(3)<<3.222<<setprecision(prec)<<endl;
  ```
  `streamsize`  是用于表示 I/O 操作中转移字符数或 I/O 缓冲区大小的有符号整数类型。它被用作 std::size_t 的有符号对应。
- ```c++
  int c;
  while(cin>>c){...}
  ```
  类`istream`提供了一个转换：用来把`cin`转换成一个可以在条件中使用的值，这个状态用来记住最近一次读数据的尝试是否成功

  在以下几种情况中，读取失败：
  * 到达输入文件尾
  * 输入与变量类型不一致
  * 输入时检测到硬件问题

## 用中值代替平均值

### `vector<double> homework`
- 不需要预知数值的个数
- 高效排序
  - `sort(n,m)` 可以对`[n,m)`区间的元素进行**非递减**排序
  - `vector.begin()` 指向第一个元素的位置
  - `vector.end()` 指向紧跟着最后一个元素的后一个位置
  ```c++
  #include<vector>
  #include<algorithm>
  using namespace std;
  vector<double> homework;
  {
    // 添加元素
    homework.push_back(...);
  }
  sort(homework.begin(),homework.end());
  ```
### 一些深入的观察
- 检测出异常，退出程序通常是一个正确的选择：**如果我们不知道做什么，那么我们还是退出比较好**
- `size_t` 是无符号整数类型，根本不可能用来存储负值：普通整数和无符号整数在表达式中结合到一起时，普通整数会被转换成无符号整数。因此，`homework.size()-100` 的结果是无符号整数，且`>0`——即使是`homework.size()<100`
- 使用这些库函数编写的程序的性能是足够的：c++标准对库的执行性能要求很高；C++是被设计来为注重性能的应用服务的

---

## 组织程序和数据
```
介绍函数和数据结构来让我们组织大型的程序

介绍一个方法来把程序分为我们可以独立编译且在编译后可以组合在一起的文件
```

### 组织计算

#### 函数：返回类型-函数名（参数列表）
  - 参数列表中的**参数**是作为函数的**局部变量**而被使用的，即在调用函数时会被创建，函数返回时会被销毁

  - 调用函数时，我们将提供参数用来对相应的参数进行**初始化**，即`复制`
  
  - 使用**按值调用**的意义是，对值进行处理并获得返回值的过程，不应该改变原来的值
  - `const vector<double>&` 对参数类型为double的向量的引用，**`引用`是指一个特定对象的另一个名称**
    ```c++
    // 例如
    vector<double> homework;
    // hw是homework的替代名
    vector<double>& hw=homework; 
    // chw是homework的只读替代名
    const vector<double>& chw=homework;
    // 一个引用的引用等价于原来对象的引用
    const vector<double>& hw1=hw;
    // 错误，非常量引用无法指向常量引用或常量对象
    vector<double>& hw2=chw;
    ```
    - 上例中我们对`hw`的操作等价于对`homework`的操作
    - `const` 确保 我们不能对 `chw` 做任何可能改变其值的动作
    - `&` 可以避免复制参数的额外开销
  - 我们没有必要对`int`和`double`等简单内部类型而去使用`const引用`
  - 通过参数列表的不同，我们可以对拥有相同函数名的函数进行重载

#### 异常
- `throw domain_error("some description")` 抛出一个异常
- 在抛出异常时，我们希望它能够给用户更多的提示，让用户更好的了解错误的所在
- ```c++
  try{

  }catch(domain_error){
    cout<<"...";
    return 1;
  }
  ```
  ```
  note：我们需要保证一条语句抛出的异常不超过一个
  ```

#### 从函数中返回多个值的方法：
    给函数多个参数，这些参数是对对象的引用
```c++
// 读一个输入流，把家庭作业读进一个double类型的向量中
istream& read_hw(istream& in,vector<double> hw){
  if(in){
    // 清除hw中的内容
    hw.clear();
    // 从in中读取内容
    double x;
    while (in>>x){
      hw.push_back(x);
    }
    //清除流以使输入动作对下一个学生有效
    in.clear();
  }
  return in;
}
// 由于返回的是流，可以写出下面的语句
if(read_hw(cin,homework)){/*...*/}
```
  - 上述例子中，`in.clear()`表示清除流中的所有错误标记，是输入可以继续

### 组织数据
#### `sort` 函数
`sort` 函数有一个可选的第三个参数，这个参数是一个`谓词`，`谓词`表示一个函数。这个函数需要返回一个`bool`值，如下:
```c++
// 参数列表的类型为结构体的引用，该结构体表示学生信息
bool compare(const Student_info& x,const Student_info& y){
  return x.name<y.name;
}
// students为一个Student_info类型的向量
sort(students.begin(),students.end(),compare);
```

#### `setw`
`setw` 用来对下一个输出项填充成有特定数目的字符集（通过给输出项添加空格完成）

它与`setprecision`不同，它对输出宽度的修改是暂时的，作用完下一个输出后，马上重置宽度

### 连接各部分代码
#### 头文件
- ```c++
  #ifndef xxx
  #define xxx
  //...头文件的内容
  #endif
  ```
  上面的操作控制程序的编译，使得每个头文件只会被包含一次
- 系统环境提供的头文件，称为`标准头`；自定义头文件称为`头文件`
- 头文件中包含对函数的声明，声明中的参数列表可以只是类型
- 头文件不应该使用`using`声明，而是使用限定名(例如`std::vector`)：我们的代码用户可能不需要使用这个using声明
- 头文件需要把**函数声明所用到的所用名称**都包含进去
#### 源文件
- 有必要在源文件中包含相应的头文件：检查声明和定义是否一致
- 源文件中可以使用`using`