# gcc-optimize

what is optimization for a program?

- run faster
- use less memory, or consume fewer system resources.

This can involve various techniques such as algorithmic improvements, code restructuring, memory management, and compiler optimizations.

Let's take a look at compiler optimizations.



## -O

You can invoke *GCC with -Q --help=optimizers* to find out the exact set of optimizations that are enabled at each level.


- `-O<level>` The default level is 0.
	- `-O0` Reduce compilation time and make debugging produce the expected results.
	
	- `-O1` The compiler tries to reduce code size and execution time, without performing any optimizations that take a great deal of compilation time.
	
	- `-O2` Optimize even more. GCC performs nearly all supported optimizations that do not involve a space-speed tradeoff. As compared to -O, this option increases both compilation time and the performance of the generated code. 
	
	- `-O3` Optimize yet more.

`-O<level>` turns on all optimizations specified by `-O<level - 1>` also turns on other optimization flags.


- `-Ofast` enables all -O3 optimizations and disregard strict standards compliance.

- `-Og` Optimize debugging experience. -Og should be the optimization level of choice for the standard edit-compile-debug cycle, offering a reasonable level of optimization while maintaining fast compilation and a good debugging experience. It is a better choice than -O0 for producing debuggable code because some compiler passes that collect debug information are disabled at -O0. Like -O0, -Og completely disables a number of optimization passes so that individual options controlling them have no effect. Otherwise -Og enables all -O1 optimization flags except for those that may interfere with debugging.

- `-Os` Optimize for size. -Os enables all -O2 optimizations except those that often increase code size. It also enables -finline-functions, causes the compiler to tune for code size rather than execution speed, and performs further optimizations designed to reduce code size.

- `-Oz` Optimize aggressively for size rather than speed. This may increase the number of instructions executed if those instructions require fewer bytes to encode. -Oz behaves similarly to -Os including enabling most -O2 optimizations.

If you use multiple -O options, with or without level numbers, the last such option is the one that is effective.


```basic
      Og         Os/Oz               Ofast                   1. Og    for debug
      ^          ^                   ^                       2. Os/Oz for size
      |          |                   |                       3. Ofast not use
+----------+----------+----------+------> optimize level   /-----------------------
O0         O1         O2         O3                        | O0 for debug version
>---------->---------->---------->------> enabling options | O2 for release version

```
Thanks for [Meld](https://meldmerge.org/), which helps me compare the list of optimization options.

Some commonly used options:
```basic
-fomit-frame-pointer
...
```




## -g


- `-g<level>` The default level is 2.
	- `-g0` produces no debug information at all. Thus, -g0 negates -g.
	
	- `-g1` produces minimal information, enough for making backtraces in parts of the program that you don’t plan to debug. This includes descriptions of functions and external variables, and line number tables, but no information about local variables.
	
	- `-g2` between g1 and g3. And -g2 equals -g.
	
	- `-g3` includes extra information, such as **all the macro definitions** present in the program. Some debuggers support macro expansion when you use -g3.
	

If you use multiple -O options, with or without level numbers, the last such option is the one that is effective.

```basic
g0         g1         g2         g3                        | g2 for debug version
>---------->---------->---------->------> debug level      | g0 for release version
                      -g
```

In addition, command *strip* can remove the debugging information from executable programs. As a result, the program size is reduced.


## attributes


Some common use cases include:

```basic
__attribute__ ((always_inline))
__attribute__ ((noreturn))
__attribute__ ((weak))
__attribute__ ((visibility ("hidden")))
__attribute__ ((packed))
...
```

Perhaps I should write another article to elaborate on this.


## summary

As you can see, the mechanism of GCC is quite extensive. Although many things may not be needed in daily use, it is still beneficial to have some knowledge about them.

The optimization mechanism of GCC should be considered as a secondary means. Given the capabilities of the current machine, greater attention should be given to algorithmic improvements, code restructuring, and memory management.


## refer

- *[gcc optimize options](https://gcc.gnu.org/onlinedocs/gcc/Optimize-Options.html#Optimize-Options)*
- *[hacknews about gcc optimize level](https://news.ycombinator.com/item?id=23140682)*
- *[gcc debugging options](https://gcc.gnu.org/onlinedocs/gcc/Debugging-Options.html#Debugging-Options)*
- *Attributes of [Function](https://gcc.gnu.org/onlinedocs/gcc/Function-Attributes.html)* / [Variables](https://gcc.gnu.org/onlinedocs/gcc/Variable-Attributes.html) / [Type](https://gcc.gnu.org/onlinedocs/gcc/Type-Attributes.html) 
