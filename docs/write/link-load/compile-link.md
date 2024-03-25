# compile-link

## process

```basic
    .c            .i            .s            .o           a.out
+----+-------------+-------------+-------------+-----------+----+
     + propressing + compilation + propressing +  linking  +
     .             .             .             .     ^     .
     |..... gcc -E |             |             |     |     .
     |................... gcc -S |             |     |     .     +---------+
     |................................. gcc -c | +---+---------+ | library |
     .                                         .           .     +---------+
     +-----------------------------------------+-----------+
     |           compiler                      |   linker  |

     1. scanner                                (symbol is address)
     2. grammer parser
     3. semantic analyser                      adress and storage allocation
     4. source code optimizer
          (generate intermediate code)         symbol resolution/binding
     5. code generator
          & target code  optimizer             relocation

```

=== "prepressing"

    预编译处理规则：
    ```basic
    - 宏定义展开            #define
    - 条件预编译指令处理     #if #ifdef #ifndef #elif #else #endif 
    - 递归包含展开          #include
    - 去除注释信息          // /* */
    - 添加行号和文件名标识   for debug
    - 保留编译器指令        #progma
    ```
    可以通过查看 .i 文件判断宏定义或头文件是否包含正确


=== "compilation"

    编译过程将 .i 文件进行一系列词法分析、语法分析、语义分析、中间代码生成、目标代码生成和优化，产生相应的汇编代码文件。



=== "assembly"

    汇编器将汇编代码变成机器可以执行的指令，每一个汇编语句几乎对应一条机器指令。汇编就是翻译。


=== "linking"

    把各个模块之间相互引用的部分处理好，使得各个模块之间能够正确地衔接。

## verify

```c
#include <stdio.h>

int main()
{
    printf("Hello World!\n");
    return 0;
}
```

```shell
$ gcc hello.c
$ ./a.out


$ gcc -E hello.c -o hello.i
$ gcc -S hello.c -o hello.s
$ gcc -c hello.c -o hello.o

```

## refer

- *程序员的自我修养 - 链接、装载和库 chapter 2*
