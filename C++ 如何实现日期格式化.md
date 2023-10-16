日期格式化需要使用到占位符，常见的占位符有 %Y、%m、%d、%H、%M 和 %S，分别表示年份、月份、日期、小时、分钟和秒。另外还有一个 %X 占位符，它是 `%H %M %S` 的缩写。更多占位符具体可以参考：[https://cplusplus.com/reference/ctime/strftime/](https://cplusplus.com/reference/ctime/strftime/) 以及 [https://en.cppreference.com/w/cpp/io/manip/put_time](https://en.cppreference.com/w/cpp/io/manip/put_time)。

# C++ 11 实现日期格式化

```cpp
#include <iomanip> // std::chrono::system_clock
#include <ctime>   // std::time_t, std::localtime
#include <chrono>  // std::put_time
#include <sstream> // std::stringstream
#include <iostream>

int main() {

  auto now = std::chrono::system_clock::now();
  std::time_t tm = std::chrono::system_clock::to_time_t(now);

  std::stringstream ss{};

  // 输出 2023-09-04 15:57:44
  ss << std::put_time(std::localtime(&tm), "%Y-%m-%d %X");
  std::cout << ss.str() << '\n';

  // clear
  ss.str("");
  ss.clear();

  // 输出 2023-09-04
  ss << std::put_time(std::localtime(&tm), "%Y-%m-%d");
  std::cout << ss.str() << '\n';

  // clear
  ss.str("");
  ss.clear();

  // 输出 15:57:44
  ss << std::put_time(std::localtime(&tm), "%X");
  std::cout << ss.str() << '\n';

  return 0;
}
```

# C++ 20 实现日期格式化

如果是 C++ 20 的话可以直接使用 `std::format` 库实现日期格式化：

```cpp
#include <iostream>
#include <chrono>
#include <format>

int main() {
  // 获取当前时间
  auto now = std::chrono::system_clock::now();

  // 格式化日期
  std::string formattedDate = std::format("{}", now);
  std::cout << "Current Date and Time: " << formattedDate << std::endl;

  return 0;
}
```

上面的代码示例的运行结果将会是当前的系统时间，以字符串的形式表示。具体的格式取决于你使用的C++标准库的实现，以及操作系统和环境。

例如，如果你运行上述代码在Linux或macOS系统上，可能会得到类似于以下的结果：

```yaml
Current Date and Time: 2023-06-21T15:24:34.123456Z
```

如果你运行在Windows系统上，可能会得到类似于以下的结果：

```yaml
Current Date and Time: 21/06/2023 15:24:34.123456
```

这些结果都是按照系统默认的日期和时间格式进行输出的。

如果你想要自定义日期和时间的格式，可以使用 `std::chrono::format` 函数，并传入自定义的格式字符串。

下面是一个示例代码，展示如何使用 `std::chrono::format` 来指定日期和时间的格式：

```cpp
#include <iostream>
#include <chrono>

int main() {
  // 获取当前时间
  auto now = std::chrono::system_clock::now();

  // 指定日期和时间的格式
  std::string format = "%Y-%m-%d %H:%M:%S";

  // 格式化日期
  std::string formattedDate = std::chrono::format(format, now);
  std::cout << "Formatted Date and Time: " << formattedDate << std::endl;

  return 0;
}
```

|**注意**|
|:------|
|gcc-11 目前对 C++20 的支持还是很不完善，还不支持 format 库。|