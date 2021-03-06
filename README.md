# -
笔记
第7章 链接
7.1编译器驱动程序
 大多数编译系统提供编译器驱动程序（complier driver），它代表用户在需要时调用语言预处理器、编译器、汇编器和链接器
   Linux下的GNU编译系统构造示例程序main.c、sum.c
   首先运行c预处理器(cpp)将源程序main.c翻译成一个ASCII码的中间文件main.i
   运行c编译器(ccl),将main.i翻译成一个ASCII汇编语言文件main.s
   然后驱动程序运行汇编器(as),将main.s翻译成一个可重定位目标文件main.o,sum.c同样变为sum.o
   最后运行链接器(ld)将main.o和sum.o以及一些必要的系统目标文件组合，创建可执行目标文件
   shell调用加载器(loader)函数，将可执行文件的代码和数据复制到内存，然后将控制转移到这个程序的开头

7.2静态链接
   像LinuxLD程序：静态链接器
   目标文件纯粹是字节块的集合。在这些块中，有些包含程序代码，有些包含程序数据，而其他的则包含引导链接器和加载器的数据结构
   
7.3目标文件
   三种形式：
   可重定位目标文件、可执行目标文件、
   共享目标文件。一种特殊类型的可重定位目标文件，可以在加载或者运行时被动态地加载进内存并链接
   技术上来说，一个目标文件就是一个以文件形式存放在磁盘中的目标模块，目标模块就是一个字节序列

7.4
   Linux系统的可重定位目标文件采用ELF格式存放
   ELF中符号表symtab,存放在程序中定义和引用的函数和全局变量的信息，不包含局部变量
   
7.5符号和符号表
   每个可重定位目标模块都有一个符号表，在链接器上下文中有三种符号(符号即目标模块中定义的静态或非静态、全局或局部变量)
   全局符号、外部符号、局部符号
   带有static属性声明的全局变量或者函数是模块私有的
   任何不带static属性声明的全局变量和函数都是公有的，可以被其他模块访问

7.6.1符号解析
   对于c++中的重载函数，编译器将每个唯一的函数和参数列表组合编码成一个对链接器来说唯一的名字
   Linux链接器处理多重定义的符号名。 函数和已初始化的全局变量时强符号、未初始化的全局变量是弱符号
   不允许有多个同名强符号
   一强多弱，选强
   多弱，随意选

7.6.2与静态库链接
   编译系统提供一种机制，将所有相关的目标模块打包成为一个单独的文件，称为静态库
   当链接器构造一个输出的可执行文件时，它只复制静态库里被应用程序引用的目标模块
   在Linux系统中，静态库以一种称为存档的特殊文件格式存放在磁盘中，存档文件时一组连接起来的可重定位目标文件的集合。由后缀.a标识
   
7.6.3链接器如何使用静态库来解析引用
   符号解析阶段，编译器按照命令行上出现的顺序来扫描可重定位目标文件和存档文件，并维护三个集合
   可重定位目标文件集合E，引用但尚未定义的符号集合U，以及一个在前面的输入文件中已定义的符号集合D
   如果当链接器完成对命令行上输入文件的扫描后，U是非空的，那么链接器就输出一个错误并终止。否则，合并E中的目标文件，构建输出的可执行文件
   
7.7重定位
   一旦链接器完成符号解析后，就把代码中每个符号引用和一个符号定义(即一个输入目标模块中的一个符号表条目)关联起来
   此时，链接器就知道它的输入模块中代码节和数据节的确切大小，开始重定位步骤，在这个步骤中，合并输入模块，为每个符号分配运行时地址
   
7.8可执行目标文件

7.10动态链接共享库
   共享库是一个目标模块，在运行和加载时，可以加载到任意的内存地址，并和一个在内存中的程序链接起来。这个过程称为动态链接，由叫动态链接器的程序执行
   共享库在Linux中用.so后缀表示，Windos中称为DLL（动态链接库）
   对于共享库，完成链接时没有任何共享库的代码和数据节被复制到可执行文件，当加载器加载和运行可执行文件时，通过动态链接器重定位共享库中的内容到内存段
   最后动态链接器将控制传递个应用程序
   
7.11从应用程序中加载和链接共享库
   Linux系统为动态链接器提供了一些简单接口 dlopen dlsym dlclose dlerror

7.12位置无关代码
   为了允许多个正在运行的进程共享内存中相同的库代码，节约内存资源，使用PIC编译库代码，使得可以在内存的任何位置加载和执行这些代码
   
小结：
   目标文件(二进制文件)
   链接器的两个主要任务
   符号解析(将目标文件中每个全局符号都绑定到一个唯一的定义)，重定位(确定每个符号的最终内存地址，并修改引用)
   加载器将可执行文件的内容映射到内存，并运行这个程序，对于(含有对定义在共享库中的数据的未解析的引用)部分链接的可执行程序，通过调用动态链接器来完成链接任务
