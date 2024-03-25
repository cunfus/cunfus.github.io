# doxygen

## 使用流程

### 1. 生成配置

```shell
$ doxygen -g Doxyfile.in
```

### 2. 修改配置

根据需要修改 Doxyfile.in 里的选项，不过直接基于下面这份修改也不错。

```shell
# Project related configuration options
DOXYFILE_ENCODING      = UTF-8
PROJECT_NAME           = "My Project"
PROJECT_NUMBER         = v0.1.0
PROJECT_BRIEF          = "a research project"
PROJECT_LOGO           =
OUTPUT_DIRECTORY       = doc
ALLOW_UNICODE_NAMES    = NO
OUTPUT_LANGUAGE        = English

OPTIMIZE_OUTPUT_FOR_C  = YES

# Build related configuration options
EXTRACT_ALL            = YES
EXTRACT_STATIC         = YES

# Configuration options related to the input files
INPUT                  = @PROJECT_SOURCE_DIR@
FILE_PATTERNS          = *.c *.h
RECURSIVE              = YES 
EXCLUDE                = @PROJECT_SOURCE_DIR@/etc @PROJECT_SOURCE_DIR@/build 
USE_MDFILE_AS_MAINPAGE = @PROJECT_SOURCE_DIR@/README.md

# Configuration options related to the LaTeX output
GENERATE_LATEX         = NO

# Configuration options related to the HTML output
GENERATE_TREEVIEW      = YES

# Configuration options related to diagram generator tools
HAVE_DOT               = YES
UML_LOOK               = YES
CALL_GRAPH             = YES
CALLER_GRAPH           = YES
DOT_IMAGE_FORMAT       = svg
INTERACTIVE_SVG        = YES
```

### 3. 集成到 CMake

```cmake
# doxygen.cmake

find_package (Doxygen)

if (DOXYGEN_FOUND)
        message("-- -- Create and Install the HTML based API document")

        set(DOXYFILE_IN ${PROJECT_SOURCE_DIR}/Doxyfile.in)
        set(DOXYFILE    ${PROJECT_BINARY_DIR}/Doxyfile)

        configure_file (${DOXYFILE_IN} ${DOXYFILE} @ONLY)
        # make doc
        add_custom_target (doc # ALL 
                    COMMAND ${DOXYGEN_EXECUTABLE} ${DOXYFILE}
                    WORKING_DIRECTORY ${PROJECT_BINARY_DIR}
                    COMMENT "Generate docs using Doxygen" VERBATIM)
endif ()
```

```cmake
# CMakeLists.txt

include(doxygen.cmake)
```

### 4. Doxygen 注释风格

Doxygen 支持多种评论风格，因此可以多次尝试，直到找到自己喜欢的一种。（见参考中的 CmdExample）

```c
/*! \brief Brief description.
 *         Brief description continued.
 *
 *  Detailed description starts here.
 *  
 * \return status code          \n
 *          0 Success           \n 
 *         -1 Failure
 * 
 * \param[in]       w double the width of a rectangle.
 * \param[in]       h double the height of a rectangle.
 * \param[in, out]  area double the area of a rectangle.
 * ... 
 */
```

注释主要是供其他程序员理解（主要是未来的自己），不是供用户阅读的。良好的代码规范比起大量的注释来说更加友善，这一点可以多参考 nginx 等优秀代码的规范。

练习注释风格不如多锻炼锻炼如何写用户手册和产品文档，这对程序员来说更有益处。


## 效果

找个 CMake 项目试一下吧。

![](https://pic.imgdb.cn/item/64ddfb5d661c6c8e54ee0abf.png)



## 总结

标题写的是 CMake 组件，但只要环境中有 Doxygen 工具就能使用。比如用 shell 或是别的方式替换 `@PROJECT_SOURCE_DIR@`，再集成到项目里，方法总是有的。 



## 参考

- *[doxygen - Usage](https://www.doxygen.nl/manual/doxygen_usage.html)*
- *[doxygen - Configuration](https://www.doxygen.nl/manual/config.html)*
- *[doxygen - Documenting the code](https://www.doxygen.nl/manual/docblocks.html)*
- *[doxygen - CmdExample](https://www.doxygen.nl/manual/commands.html#cmdexample)*
- *[cmake - configure_file](https://www.cnblogs.com/the-capricornus/p/4717566.html)*
