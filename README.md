# 2024090908012-但家利-CS-03 #
## C语言内存模型 ##
### 程序代码区 ###
<p>具体含义：存储程序二进制指令的内存区域</p>  
<p>应用场景：存储机器代码，重定位  </p>
<p>调用方式：直接调用。 间接调用（通过指针实现。</p>
<h3> 常量区: </h3>
<p>具体含义：存储程序中的常量，如字符常量，数字常量，const修饰的全局变量和字符串常量</p>
<p>应用场景：存储常量</p>
<p>调用方式：直接访问它们的定义名称来调用的</p>
<h3> 全局数据区：</h3>
<p>具体含义：存储已经初始化的全局变量和静态变量</p>
<p>应用场景：存储全局变量，存储静态变量</p>
<p>调用方式：直接访问；在函数内部声明；如果需要在不同的文件中访问同一个全局变量，需要在其他文件中使用 extern 关键字来声明它。</p>
<h3>堆区：</h3>
<p>具体含义：用来存放进程运行中被动态分配的内存段，用于存储程序运行时创建的对象、数据结构和动态分配的内存块</p>
<p>应用场景：优先队列，排序算法，图算法</p>
<p>调用方式：</p> 
<p>`malloc(size_t size)`：分配指定大小的内存块，返回指向它的指针。</p>
<p>`calloc(size_t num, size_t size)`：分配内存块，并初始化为零。</p>
<p>`realloc(void *ptr, size_t new_size)`：更改先前分配的内存块的大小。</p>
<p>`free(void *ptr)`：释放先前分配的内存块。</p>
<h3>动态链接库：</h3>
<p>具体含义：是一个可以被其它应用程序共享的程序模块，其中封装了一些可以被共享的例程和资源</p>
<p>应用场景：多个不同主程序需使用相同程序的函数时</p>
<p>调用方式：静态调用，动态调用</p>
<h3>栈区：</h3>
<p>具体含义：存放局部变量 函数参数 返回值</p>
<p>应用场景：回溯算法，撤销操作，深度优先算法</p>
<p>调用方式：创建一个空栈，通常使用特定的数据结构来实现，如数组或链表。</p>

## 导学问题
1. 指程序在运行过程中，尝试向栈（Stack）中写入的数据超过了栈的内存限制。
2. 栈由操作系统自动分配和释放而堆需要程序员手动管理； 栈内存空间相对较小而堆的内存空间相对较大； 栈主要用于存储函数调用时的参数、局部变量和返回地址等，而堆用于存储动态分配的数据，如动态数组、链表、树等数据结构； 栈不需要使用分配函数而堆需要使用分配函数
3. <p>只读：程序代码区，常量区</p>
   <p>堆区，栈区，全局数据区</p>
   <p>动态链接库中的代码和只读数据是只读的，但其中的数据段可以包含全局变量，是可读写的</p>
4.<p> `malloc()` :  void* malloc(size_t size); 其中 void 表返回值类型， size 表参数名， size_t 是参数类型</p>
   `free()`: void free(void* ptr) 其中 void 表返回值类型， void* ptr 表要指向释放内存块的通用指针 ptr 是参数名称； 堆区
5. 方便对不同类型的数据等的管理，提高内存使用效率；使不同类型数据等分区，有利于提高程序的可移植性；便于防止内存碎片化，优化内存分配

## 内存模型的应用
1. `constValue` `constString`:存储在常量区。首先，两者有 const 前缀，其次是全局变量且均被初始化，所以可以判断，两者是不允许被更改的，应当存储在常量区。倘若两者并没有存储在常量区，那么程序中对于两者值的变动的部分可能不会引起警告，又因为使用 const 进行修饰的本意就是保证两者的值不变，那么就可能导致程序运行时出现逻辑错误，且无法得到快速的更正。
2. `globalVar`:全局数据区。是全局变量且被初始化，在程序中可以对其值进行更改。
3. `staticVar`:全局数据区。变量位于主函数内，但是用 static 修饰，属于静态变量，应而存储在全局数据区。
4. `localVar` `localVarMain`:栈区。localVar 位于函数 function 中，localVarMain 位于 main 函数中，两者都属于局部变量。存储在栈中，可以使得它们的生命周期与它们所在函数的执行过程相对应，从而可以达到随函数调用而自动创建和销毁的特性。可以得到更快的分配和回收速度，满足程序运行的需要。栈的线程是私有的，从而可以保证各个函数运行的独立性，互不干扰。倘若两者不存储在栈中，则上述适配于局部变量的优势无法实现，而且可能会导致变量生命周期延长（全局数据区），变量赋值后无法更改（常量区），因未分配内存而导致的内存泄漏（堆区）。
5. 对于 globalVar 和 staticVar 运行如下程序
```c
#include <stdio.h>
#include <stdlib.h>

const int constValue = 100;
const char* constString = "Hello, World!";
int globalVar = 10;

void function(int arg) {
    int localVar = 20;
    int *ptr = malloc(sizeof(int));
    *ptr = 30;
    free(ptr);
}

int main() {
    static int staticVar = 40;
    int localVarMain = 50;
    function(60);
    printf("address of globalVar:%p\n",(void*)&globalVar);
    printf("address of localVarMain:%p\n",(void*)&localVarMain);
    printf("address of staticVar:%p\n",(void*)&staticVar);
    return 0;
}
```
运行结果显示为
```c
address of globalVar:0000000000403018
address of localVarMain:000000000061FE1C
address of staticVar:000000000040301C
```
可见`globalVar`和`staticVar`地址的值比较接近，符合全局变量的特征
## 浅谈Cache
1. 它是一种将程序指令存储器和数据存储器合并在一起的存储器结构；以CPU为中心的组织形式，所有数据流动，程序运行，结果输出都由CPU执行和居中协调；计算机由五大部件组成：运算器、存储器、控制器、输入设备、输出设备；指令和数据都以二进制表示。    
运算器和控制器通常集成到一个芯片中，即中央处理器（CPU）；现代计算机更加注重I/O设备的性能，以减少CPU和I/O设备之间的速度差异；现代计算机体系结构可能采用并行处理技术，如多核处理器和多线程，以提高计算性能。
冯诺伊曼体系结构以运算器为中心，而现代计算机体系结构以存储器为中心。
冯诺伊曼体系结构中，指令和数据在同一存储器中按顺序串行执行，现代计算机可能采用并行处理技术。
2. 主存储器是存储单元的集合，主要部分是RAM（随机存储器）。当CPU需要读取数据时，它会发送一个地址信号到存储器，存储器根据这个地址将对应的数据发送回CPU。当CPU需要写入数据时，它会发送一个地址信号以及要写入的数据到存储器，存储器将数据写入到指定的地址。
3. 程序在访问数据时会倾向于访问之前访问过的数据。包括空间局部性（如果一个元素被访问，那么它周围的元素很有可能接下来被访问到）和时间局部性(如果一个数据被访问，那么它很有可能在短时间内再次被访问到)
4. CPU的运行速度远快于主存储器。缓存作为两者之间的缓冲，其访问速度接近CPU，这样可以减少CPU等待数据的时间。访问主存储器（RAM）比访问缓存需要更长的时间。缓存减少了CPU需要等待主存储器响应的次数。缓存利用这些局部性原理，预测性地将数据加载到缓存中，使得CPU更有可能在缓存中找到需要的数据。

## 代码优化
原代码
```C
#include <stdio.h>

int main()
{
    int code;
    for(;;)
    {
        printf("Show me your code,please.\n");
        scanf("%d",&code);
        if(code>=100000 && code<=999999)
        {
            printf("I am super hacker");
            return 0;
        }
        else
        {
            printf("Fack code!");
        }
    }
}
```
优化后
```c
#include <stdio.h>
#define code_start 100000
#define code_end 999999

int main()
{
    int code;
    while(1)
    {
        printf("Show me your code,please.\n");
        if(scanf("%d",&code)==1)
        {
            if(code>=code_start && code<=code_end)
            {
                printf("I am super hacker");
                break;
            }
            else
            {
                printf("Fack code!\n");
            }
        }
        else
        {
            printf("fack code\n");
            while(getchar() != '\n');
        }
    }
    return 0;
}






