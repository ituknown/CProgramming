
| **标准版本** | **CMake版本要求**  |
| :------- | :------------- |
| C++20    | CMake 3.15 及以上 |
| C++17    | CMake 3.12 及以上 |
| C++14    | CMake 3.5 及以上  |
| C++11    | CMake 3.1 及以上  |

在使用时，需要在 CMakeLists.txt 中添加以下代码来启用 C++ 标准（以 C++20 为例）：

```CMake
set(CMAKE_CXX_STANDARD 20)
set(CMAKE_CXX_STANDARD_REQUIRED true)
```

# 扩展

`CMAKE_CXX_STANDARD_REQUIRED` 参数是用来控制 C++ 标准的强制性要求的。它与 `CMAKE_CXX_STANDARD` 一起使用，主要有以下作用:

1、**强制使用指定的 C++ 标准**

如果将 `CMAKE_CXX_STANDARD_REQUIRED` 设置为 `true`，那么编译器就必须支持 `CMAKE_CXX_STANDARD` 指定的 C++ 标准。如果不支持会报错。

如果设置为 `false`，则 CMake 会尝试使用 `CMAKE_CXX_STANDARD` 指定的标准，但如果编译器不支持也不会报错，会降级使用更低版本的 C++ 标准。

2、**确保项目中所有的 target 使用相同的 C++ 标准**

当 `CMAKE_CXX_STANDARD_REQUIRED` 为 `true` 时，CMake 会确保项目中所有的 target 都使用相同版本的 C++ 标准。这有助于确保代码的一致性。

3、**设置编译器标志**

当 `CMAKE_CXX_STANDARD_REQUIRED` 为 `true` 时，CMake 会自动设置编译器所需的标志。比如 `-std=c++20`、`-std=c++17` 等。

总的来说，`CMAKE_CXX_STANDARD_REQUIRED` 参数可以帮助更好地控制项目中使用的 C++ 标准版本，确保代码的一致性和编译器设置的正确性。因此建议在使用指定 C++ 标准时，明确将其设置为 `true`。

--

https://cmake.org/documentation/

https://cmake.org/cmake/help/latest/prop_tgt/CXX_STANDARD.html