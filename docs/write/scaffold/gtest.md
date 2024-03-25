# gtest

关于单元测试，比较关注的点

- 易用，将过程专注于测试
- 方便，可以构造测试的前提条件，重复使用
- 直观，结果可视化，增强反馈

先来看 cmake 自带的测试驱动程序 ctest，ctest 当且仅当返回值为 0 时，测试成功。这样一来，ctest 框架可以容纳各种语言的测试脚本，扩展性强。但由于一个返回条目只对应一个测试用例，因此更适合用作集成测试，用在单元测试上属于折磨人。

敲定了集成测试，在单元测试上再来对比对比 cunit 和 gtest。

## 单元测试框架

cunit gtest 的核心思想是一样的。

```shell
                       +----------------------------------------------+
                       | unit test                 +---------------+  |
                       |   +---------------+  +--->+    test 1     |  |
                       |   |               |  |    +---------------+  |
                   +------>+    Suit 1     +--+            .          |
                   |   |   |               |  |    +---------------+  |
+----------------  |   |   +---------------+  +--->+    test n     |  |
|               |  |   |          .                +---------------+  |
|   Registry    +--+   +----------------------------------------------+
|               |  |              .                +---------------+
+----------------  |       +---------------+  +--->+    test 1     |
                   |       |               |  |    +---------------+
                   +------>+    Suit m     +--+            .
                           |               |  |    +---------------+
                           +---------------+  +--->+    test n     |
                                                   +---------------+
```

gtest 在易用性上做得更好（支持通配过滤，如 `--gtest_filter=*`），接口封装的很干净。支持事件机制，方便性也满足了，最后看下可视化方面。


## 可视化

```shell
# 执行 gtest 程序，并输出为 xml
$ ./a.out --gtest_output=xml:./result.xml

# xml 转 html
# 参考 https://gitlab.uni-koblenz.de/agrt/gtest2html readme介绍
```

可视化上 cunit 自带显示 xml 的工具，gtest 需要第三方工具转换。

有一说一，cunit 输出页面风格还是很好看的。



## 集成到 CMake

gtest 从 1.13 开始需要 c++14 的支持，1.12 需要 c++11。大多实际项目也不会盲目引进 c++14 的特性，因此，指定 1.12 一般够用了。

```cmake
set(CMAKE_CXX_STANDARD 11)
set(CMAKE_CXX_STANDARD_REQUIRED ON)

find_package(GTest QUIET)

if(NOT GTest_FOUND)
    include(FetchContent)
    FetchContent_Declare(
        googletest
        GIT_REPOSITORY https://github.com/google/googletest.git
        GIT_TAG release-1.12.0
    )
	
	# FetchContent_MakeAvailable() is not suitable for here :(
    FetchContent_GetProperties(googletest)
    if(NOT googletest_POPULATED)
    	FetchContent_Populate(googletest)
        add_subdirectory(${googletest_SOURCE_DIR} ${googletest_BINARY_DIR})
    endif()
endif()


add_executable(exec_name exec_name.cc)
target_link_libraries(exec_name gtest gtest_main)
```



## 用法



gtest 的判断分为断言 ASSERT_ 和期望 EXPECT_，两者在执行时的区别相当于 break 和 continue。

判断类型速查：

| 主要判断类型 | 宏                          | 说明                   |
| ------------ | --------------------------- | ---------------------- |
| 布尔判断     | _TRUE(condition)            |                        |
|              | _FALSE(condition)           |                        |
| 整型判断     | _EQ(expect, actual)         | ==                     |
|              | _NE(expect, actual)         | !=                     |
|              | _LT(expect, actual)         | <                      |
|              | _LE(expect, actual)         | <=                     |
|              | _GT(expect, actual)         | >                      |
|              | _GE(expect, actual)         | >=                     |
| 浮点型判断   | _FLOAT_EQ                   |                        |
|              | _DOUBLE_EQ                  |                        |
|              | _NEAR((v1, v2, abs_err))    | abs(v1, v2) <=abs_err  |
| 字符串判断   | _STREQ                      |                        |
|              | _STRNE                      |                        |
|              | _STRCASEEQ                  |                        |
|              | _STRCASENE                  |                        |
| 异常判断     | _THROW(statement, expect_e) | statement 抛出期望异常 |
|              | _ANY_THROW(statement)       | statement 抛出任意异常 |
|              | _NO_THROW(statement)        | statement 无异常       |



## 总结

gtest 目前从我的使用角度来说，只剩下可视化这个问题了。如果 gtest 输出的 xml 支持 cunit 风格，且不需要太复杂的设置进行格式转换，就很好了。 

## 参考

- *[gtest 事件机制](https://www.cnblogs.com/coderzh/archive/2009/04/06/1430396.html)*

- *[gtest 单元测试与代码覆盖率](https://paul.pub/gtest-and-coverage/)*

- *[cunit 单元测试](https://www.cnblogs.com/linux-sir/archive/2012/08/25/2654557.html)*
