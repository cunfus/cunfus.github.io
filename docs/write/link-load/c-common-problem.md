# c-common-problem


c/c++ 可使用检测工具`asan` `valgrind`定位程序问题。一般的检查，asan 比 valgrind 更快，结果更精确，经过 `time` 测试，asan 消耗时间是 valgrind 的 1/30。

## 版本环境

- valgrind（3.21.0）
- gcc(13.1.1)

## 用法
- Sanitizer 编译时附带 `-fsanitize=address -g` 直接执行程序
- Valgrind  编译时附带 `-g` 使用 Valgrind 执行程序 `valgrind --tool=memcheck --leak-check=full ./a.out`

## 触发类型


### 内存问题

- memory leak

    ```c
    char *str = (char *)malloc(100 * sizeof(char));
    strcpy(str, "Hello Leak!");
    printf("%s\n", str);
    /** leak here */
    return 0;
    ```

- double free

    ```c
    char *str = (char *)malloc(100 * sizeof(char));
    free(str);
    /** double free */
    free(str);
    return 0;
    ```


- use after free

    ```c
    char *str = (char *)malloc(100 * sizeof(char));
    free(str);
    /** use after free */
    strcpy(str, "Hello Leak!");
    printf("%s\n", str);
    return 0;
    ```

- heap buffer overflow

    ```c
    char *str = (char *)malloc(sizeof(char) * 12);
    /* heap-buffer-overflow */
    strcpy(str, "Hello World!");
    printf("string is :%s\n", str);
    free(str);
    return 0;
    ```

- stack buffer overflow

    ```c
    char str[100] = {1};
    /** stack-buffer-overflow */
    str[101] = 1;
    return 0;
    ```

- global buffer overflow

    ```c
    int array[100] = {-1};
      
    int main()
    {
        /* global-buffer-overflow */
        array[101] = 1;
        return 0;
    }
    ```

- alloc/dealloc mismatch

    ```c
    int main()
    {
        char *str = new char[100];
        /** alloc-dealloc-mismatch*/  
        delete str;
      
        return 0;
    }
    ```

sanitizer 给出的信息比较直观，如 memory leak 的执行信息（类型、行数、影响）：

```shell
Hello Leak!

=================================================================
==212706==ERROR: LeakSanitizer: detected memory leaks

Direct leak of 100 byte(s) in 1 object(s) allocated from:
    #0 0x7f6aa04e1359 in __interceptor_malloc 
					/usr/src/debug/gcc/gcc/libsanitizer/asan/asan_malloc_linux.cpp:69
    #1 0x55c93274c1aa in main 
    			***************************/leak.c:7
    #2 0x7f6aa023984f  (/usr/lib/libc.so.6+0x2384f) (BuildId: ****)

SUMMARY: AddressSanitizer: 100 byte(s) leaked in 1 allocation(s).

```



### 线程问题（附带测试）

多线程场景下常见的错误

- data race

    ```c
    int g_var;
    
    void* thread1(void *arg)
    {
        ...
        g_var++;
        ...
    }
    
    void* thread2(void *arg)
    {
        ...
        g_var++;
        ...
    }
    ```

- dead lock

    ```c
    
    pthread_mutex_t mutex_a, mutex_b;
    pthread_barrier_t barrier;
    
    void* lock_a(void *arg)
    {
        pthread_mutex_lock(&mutex_a);
        {
            pthread_barrier_wait(&barrier);
    
            pthread_mutex_lock(&mutex_b);  /* 等待 lock_b 释放*/
            pthread_mutex_unlock(&mutex_b);        
        }
        pthread_mutex_unlock(&mutex_a);
    }
    
    void* lock_b(void *arg)
    {
        pthread_mutex_lock(&mutex_b);
        {
            pthread_barrier_wait(&barrier);
    
            pthread_mutex_lock(&mutex_a);  /* 等待 lock_a 释放 */
            pthread_mutex_unlock(&mutex_a);        
        }
        pthread_mutex_unlock(&mutex_b);
    }
    
    ```

data race 方面，tsan 相比 valgrind 结果基本一致，但速度完胜。

dead lock 方面，valgrind 能检测出线程退出未解锁导致的死锁，对相互死锁没有办法，tsan 对死锁完全没有办法。

## 想法

比排查手段更重要的是排查思路。线上的环境不比生产环境工具齐全，还有着种种约束（访问时段限制，服务启停限制等）增加成本。保持思路清晰，通过服务日志、系统记录信息寻找关键点，更考验对系统的整体了解程度。

比排查思路更接近问题源头的就是设计思路了……

## 参考

- *[Sanitizers](https://github.com/google/sanitizers)*

