---
layout:     post                    # 使用的布局（不需要改）
title:      GCC-and-Make               # 标题 
subtitle:   编译，链接和构建C/C++应用程序 #副标题
date:       2017-11-10              # 时间
author:     whillys                     # 作者
header-img: img/post-bg-2015.jpg    #这篇文章标题背景图片
catalog: true                       # 是否归档
tags:                               #标签
    - GCC
    - Make
---

## 1、GCC (GNU Compiler Collection)

### 1.1 GCC的简史和介绍

最初的GNU C编译器由GNU计划的创始人Richard Stallman开发，Richard Stallman于1984年创立了GNU计划，旨在创建一个完整的类unix操作系统作为自由软件，以促进计算机用户和程序员之间的自由和协作。

GCC，最初用于GNU C编译器，经过不断的发展以支持多种言语，如C++，Objective-C，Java，Fortran和Ada。现在被称为“GNU编译器集合”，GCC的官方网站是：httt://gcc.gnu.org/

GCC是GNU工具链的关键组成部分，用于开发应用程序和操作系统，GNU工具链包括：

1. GNU Compiler Collection(GCC)：编译器集合，支持多种语言，例如，C++，C，Java和Objective-c
2. GNU Make：用于编译和构建应用程序的自动化工具
3. GNU Binutils：一套二进制实用工具，包括链接器和汇编器
4. GNU Debug(GDB)
5. GNU Autotools：一个构建系统，包括Autoconf，Autoheader，Automake和Libtool
6. GNU Bison：解析器生成器（类似于lex和yacc）

GCC是可移植的，能够运行在多种操作系统中，GCC(和GNU工具链）目前在所有的unix操作系统中都可以使用，它们能够通过MinGW和Cygwin被移植到windows操作系统中，GCC也是一个交叉编译器，能够生成用于不同平台的可执行代码。

GCC的各种版本如下：

1987年，GCC的首个版本发布。

1992年，GCC的第二个版本发布，支持C++。

2001年，GCC的第三个版本发布。

2005年，GCC的第四个版本发布，截至2012年7月，GCC的最新版本是4.7.4。


### 1.2 GCC的安装

GCC（GNU 工具链）包含在所有的unix中，对于windows系统，你可以安装MinGW或者Cygwin，参考[How to install Cygwin and MinGW](http://www3.ntu.edu.sg/home/ehchua/programming/howto/Cygwin_HowTo.html)

版本

你可以通过--version选项来查看GCC的版本信息

> $gcc --version
> 
> gcc (GCC) 4.4.7 20120313 (Red Hat 4.4.7-4)
> Copyright (C) 2010 Free Software Foundation, Inc.
> This is free software; see the source for copying conditions.  There is NO
> warranty; not even for MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.

更多的信息可以通过-v选项

> $gcc -v
>
> Using built-in specs.
> Target: x86_64-redhat-linux
> Configured with: ../configure --prefix=/usr --mandir=/usr/share/man --infodir=/usr/share/info --with-bugurl=http://bugzilla.redhat.com/bugzilla --enable-bootstrap --enable-shared --enable-threads=posix --enable-checking=release --with-system-zlib --enable-__cxa_atexit --disable-libunwind-exceptions --enable-gnu-unique-object --enable-languages=c,c++,objc,obj-c++,java,fortran,ada --enable-java-awt=gtk --disable-dssi --with-java-home=/usr/lib/jvm/java-1.5.0-gcj-1.5.0.0/jre --enable-libgcj-multifile --enable-java-maintainer-mode --with-ecj-jar=/usr/share/java/eclipse-ecj.jar --disable-libjava-multilib --with-ppl --with-cloog --with-tune=generic --with-arch_32=i686 --build=x86_64-redhat-linux
> Thread model: posix
> gcc version 4.4.7 20120313 (Red Hat 4.4.7-4) (GCC) 

Help
> gcc --help

Man Pages
> man gcc


### 1.3 入门

GNU C和C++编译器分别是gcc，g++。

编译一个简单的c程序 - hello.c

    #include<stdio.h>
    
    int main()
    {
    	printf("Hello, World!\n");
    	return 0;
    }

编译hello.c

    $gcc hello.c
    
默认的输出文件是a.out

运行可执行程序：

    $./a.out 
    Hello,World

你可以通过-o选项指定输出文件名称，

    $gcc hello.c -o hello
    $./hello 
    Hello,World

更多的GCC编译器选项：

    $gcc -Wall -g hello.c -o hello
    $./hello 
    Hello,World

> -o：指定输出文件名称

> -Wall：打开最常用的编译器告警
> 
> -g：产生调试信息，使用gdb的时候需要该选项

编译和链接分开进行

上面将源文件编译链接成可执行程序在一步中完成，你可以将源文件编译，目标文件链接分开进行：

    //通过-c选项编译源文件
    $gcc -Wall -c hello.c
    
	//将目标文件hello.o链接成可执行文件
    $gcc -g hello.o -o hello

	//运行可执行文件 hello
    $./hello
    Hello,World


编译链接多个源文件

假设你有两个源文件：file1.c file2.c，你可以在单行命令中同时编译这两个源文件

    gcc file1.c file2.c -o exef_file

你也可以将源文件单独编译成目标文件，然后链接成可执行文件：

    gcc -c file1.c
    
    gcc -c file2.c
    
    gcc file1.o file2.o -o exe_file


### 1.4 GCC编译过程

![](https://github.com/whillys/whillys.github.io/blob/master/img/gcc_compilation.png)

GCC编译c，c++程序到可执行程序经历了上图中所示的四个步骤：

1、预处理：通过GNUC C预处理器（cpp），包括头文件包含（#include）和宏定义展开（#define）

    cpp hello.c > hello.i

2、编译：

    gcc -S hello.i 

-S选拔表示生成汇编代码而不是目标代码，对应的汇编文件是hello.s

3、汇编：汇编器将汇编代码转换成目标代码

    as hello.s -o hello.o

4、链接：链接器将目标代码和其他的库代码链接生成可执行程序

    ld hello.o ...libraries... -o hello


### 1.5 检查编译程序的实用程序

对于所有的GNU实用程序，你可以通过“command --help"来显示help菜单，或者”man command"来查看帮助页

**file实用程序：查看文件类型**

    $file hello.c
    hello.c: ASCII text
    
    $file hello.o
    hello.o: ELF 64-bit LSB relocatable, x86-64, version 1 (SYSV), not stripped
    
    $file hello
    hello: ELF 64-bit LSB executable, x86-64, version 1 (SYSV), dynamically linked (uses shared libs), for GNU/Linux 2.6.18, not stripped

**nm实用程序：列出目标文件的符号表**

    $nm simple.o 
    0000000000000000 T func1
    0000000000000000 D global_init_var
    0000000000000004 C global_uninit_var
    0000000000000024 T main
     				 U printf
    0000000000000004 d static_var.2056
    0000000000000000 b static_var2.2057
    
    $nm -f sysv simple.o 
    
    
    Symbols from simple.o:
    
    Name  Value   ClassType Size Line  Section
    
    func1  			 	|0000000000000000|   T  |  FUNC|0000000000000024| |.text
    global_init_var  	|0000000000000000|   D  |OBJECT|0000000000000004| |.data
    global_uninit_var   |0000000000000004|   C  |OBJECT|0000000000000004| |*COM*
    main				|0000000000000024|   T  |  FUNC|0000000000000032| |.text
    printf  			|				|   U   |NOTYPE|				| |*UND*
    static_var.2056 	|0000000000000004|   d  |OBJECT|0000000000000004| |.data
    static_var2.2057	|0000000000000000|   b  |OBJECT|0000000000000004| |.bss

nm通常用于检查目标文件中是否定义了特定的函数，第二列中的“T"表示定义了该函数，”U"表示没有定义该函数。

**ldd实用程序：用于列出可执行文件或者动态库依赖的动态库**


## 2. GNU Make

Make实用程序是一个代码构建工具，用于自动的从源代码构建可执行文件的方方面面，Make使用一个叫做Makefile的文件，其中包含了如何构建可执行文件的规则。

### 2.1 一个使用Makefile的简单示例

    //hello.c
    #include<stdio.h>
    
    int main()
    {
    printf("Hello,World\n");
    return 0;
    }

创建一个Makefile文件，里面包含了生成可执行文件的各种规则

    //Makefile
    all:hello
    
    hello:hello.o
    	gcc hello.o -o hello
    
    hello.o:hello.c
    	gcc -c hello.c
    
    clean:
    	rm -f hello.o hello

使用make：

    $make
    gcc -c hello.c
    gcc hello.o -o hello
    
    $./hello 
    Hello,World
    
Makefile文件由各种规则组成，规则由三部分组成，其语法为：

    target:prerequisites...
    	command
目标和先决条件之间用冒号分开，命令前面必须跟一个tab。


### 2.2 更多关于Makefile

**注释和续行符**

注释以#开头，持续到行尾，反斜杠（\）表示续行符。


规则的通用语法为：

    target1[target2...]：[pre-re-1][pre-req-2...]
    	[command1
    	[command2
    	...
    	]


**伪目标**

如果一个target不表示一个文件，那么这个target就是一个伪目标，例如，上面Makefile中的clean就是一个伪目标，伪目标总是被决议，即后面的指令总是会被执行。一些标准的伪目标有：all，clean，install。

**变量**

Makefile中的变量相当于C语言中的宏，变量定义时，变量和变量内容通过=号连起来，例如，SOURCE = file1.c file2.c，变量的应用通过$(variable)的形式来引用。

**自动变量**

自动变量在规则匹配后由make自动设置，包括：

> $@：指代当前目标，就是Make命令当前构建的那个目标。
> 
> $<：指代第一个前置条件。比如，规则为 t: p1 p2，那么$< 就指代p1。
> 
> $^：指代所有前置条件，之间以空格分隔。比如，规则为 t: p1 p2，那么 $^ 就指代 p1 p2 。

前面的Makefile可用重写为：

    all:hello
    
	#$@表示规则中的目标，$^表示所有的先决条件
    hello:hello.o
    	gcc $^ -o $@
    
	#$<表示第一个先决条件
    hello.o:hello.c
    	gcc -c $<
    
    clean:
   		rm -f hello.o hello

**VPATH**

在默认情况下，make在当前目录下搜索源文件和目标文件，可以定义变量VPATH（大写）来指定在当前目录下找不到文件的情况下去其他目录搜索文件。例如：

VPATH = src:../headers

指定了两个目录，“src”,“../headers”，make会按照这个顺序进行搜索，目录由“冒号”分隔。（当前目录永远是最高优先搜索的地方）
还可以通过make的关键字vpath（小写）来指定不同文件的不同搜索目录，它有三种使用方式：

1、vpath pattern directories
 
为符合模式<pattern>的文件指定搜索目录directories。 

2、vpath pattern

清除符合模式pattern的文件的搜索目录。 

3、vpath 

清除所有已被设置好了的文件搜索目录

vapth使用方法中的pattern需要包含“%”字符。“%”的意思是匹配零或若干字符，例如，“%.h”表示所有以“.h”结尾的文件。pattern指定了要搜索的文件集，而directories则指定了pattern的文件集的搜索的目录。例如： 

vpath %.h ../headers 

该语句表示，要求make在“../headers”目录下搜索所有以“.h”结尾的文件。（如果某文件在当前目录没有找到的话）

VPATH/vpath只对Makefile中的依赖查找有效，对源文件中的#include的查找无效。

**一个示例Makefile**

这个Makefile是从Eclipse的c/c++开发指南中提取的：

    # A sample Makefile
    # This Makefile demonstrates and explains 
    # Make Macros, Macro Expansions,
    # Rules, Targets, Dependencies, Commands, Goals
    # Artificial Targets, Pattern Rule, Dependency Rule.
    
    # Comments start with a # and go to the end of the line.
    
    # Here is a simple Make Macro.
    LINK_TARGET = test_me.exe
    
    # Here is a Make Macro that uses the backslash to extend to multiple lines.
    OBJS =  \
     Test1.o \
     Test2.o \
     Main.o
    
    # Here is a Make Macro defined by two Macro Expansions.
    # A Macro Expansion may be treated as a textual replacement of the Make Macro.
    # Macro Expansions are introduced with $ and enclosed in (parentheses).
    REBUILDABLES = $(OBJS) $(LINK_TARGET)
    
    # Here is a simple Rule (used for "cleaning" your build environment).
    # It has a Target named "clean" (left of the colon ":" on the first line),
    # no Dependencies (right of the colon),
    # and two Commands (indented by tabs on the lines that follow).
    # The space before the colon is not required but added here for clarity.
    clean : 
      rm -f $(REBUILDABLES)
      echo Clean done
    
    # There are two standard Targets your Makefile should probably have:
    # "all" and "clean", because they are often command-line Goals.
    # Also, these are both typically Artificial Targets, because they don't typically
    # correspond to real files named "all" or "clean".  
    
    # The rule for "all" is used to incrementally build your system.
    # It does this by expressing a dependency on the results of that system,
    # which in turn have their own rules and dependencies.
    all : $(LINK_TARGET)
      echo All done
    
    # There is no required order to the list of rules as they appear in the Makefile.
    # Make will build its own dependency tree and only execute each rule only once
    # its dependencies' rules have been executed successfully.
    
    # Here is a Rule that uses some built-in Make Macros in its command:
    # $@ expands to the rule's target, in this case "test_me.exe".
    # $^ expands to the rule's dependencies, in this case the three files
    # main.o, test1.o, and  test2.o.
    $(LINK_TARGET) : $(OBJS)
      g++ -g -o $@ $^
    
    # Here is a Pattern Rule, often used for compile-line.
    # It says how to create a file with a .o suffix, given a file with a .cpp suffix.
    # The rule's command uses some built-in Make Macros:
    # $@ for the pattern-matched target
    # $< for the pattern-matched dependency
    %.o : %.cpp
      g++ -g -o $@ -c $<
    
    # These are Dependency Rules, which are rules without any command.
    # Dependency Rules indicate that if any file to the right of the colon changes,
    # the target to the left of the colon should be considered out-of-date.
    # The commands for making an out-of-date target up-to-date may be found elsewhere
    # (in this case, by the Pattern Rule above).
    # Dependency Rules are often used to capture header file dependencies.
    Main.o : Main.h Test1.h Test2.h
    Test1.o : Test1.h Test2.h
    Test2.o : Test2.h
    
    # Alternatively to manually capturing dependencies, several automated
    # dependency generators exist.  Here is one possibility (commented out)...
    # %.dep : %.cpp
    #   g++ -M $(FLAGS) $< > $@
    # include $(OBJS:.o=.dep)

【参考资料】

1. [http://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html](http://www3.ntu.edu.sg/home/ehchua/programming/cpp/gcc_make.html)
2. GCC Manual "Using the GNU Compiler Collection (GCC)" @ [http://gcc.gnu.org/onlinedocs](http://gcc.gnu.org/onlinedocs).
3. GNU 'make' manual @ [http://www.gnu.org/software/make/manual/make.html](http://www.gnu.org/software/make/manual/make.html).
4. Robert Mecklenburg, "Managing Projects with GNU Make", 3rd Edition, 2004. 
