C++ 的模板是一种强大的工具，可以让我们创建可重用的代码，而不需要关心操作的数据类型。模板可以用于定义函数、类以及运算符。

在 C++ 中，数组的大小是固定的，不能动态改变。因此，为了在函数或类中操作数组，需要知道数组的大小。当你使用 `template<typename T, std::size_t N>` 定义一个类或函数时，这表示该模板接受两个参数：一个是类型参数 T，另一个是整数常量参数 N。对数组来说，N 通常用于表示数组的大小。

例如，你可以创建一个模板函数来复制一个数组：

```cpp
template<typename T, std::size_t N>
void copy_array(const T (&src)[N], T (&dst)[N]) {
  for (std::size_t i = 0; i < N; ++i) {
    dst[i] = src[i];
  }
}
```

这个函数模板可以接受两个数组作为参数，无论它们的元素类型是什么。`std::size_t N` 告诉编译器数组的长度，这样编译器就可以确保我们在循环中不会访问超过数组长度的元素。

对于数组作为模板参数，编译器会记住数组的大小。例如，如果你调用:

```cpp
int src_arr[5] = {1, 2, 3, 4, 5};
int dst_arr[5] = {6, 7, 8, 9, 10};

copy_array(src_arr, dst_arr);
```

编译器会知道 N 是 5，因此它会生成一个只能复制 5 个元素的代码。乍一看会很懵，会很奇怪为什么模板就能推断出数组大小 N。这其实是模板泛型的省略写法，它的完整写法如下：

```cpp
copy_array<int, 5>(src_arr, dst_arr);
```

另外，模板的泛型可以从右到左依次省略，因此还可以将数组的大小省略：

```cpp
copy_array<int>(src_arr, dst_arr);
```

现在就瞬间豁然开朗了。

需要注意的是，从 C++11 开始，可以直接使用 auto 关键字来让编译器推断数组的大小：

```cpp
template<typename T>
void copy_array(const T (&src)[], T (&dst)[]) {
  for (std::size_t i = 0; i < src.size(); ++i) {
    dst[i] = src[i];
  }
}
```

在这个版本中，不需要提供数组的大小。然而，为了能够访问到 `.size()` 成员函数，使用时需要确保你的数组是一个标准容器（例如 std::array 或 std::vector）。对于原生 C++ 数组，`.size()` 不是一个有效的操作。