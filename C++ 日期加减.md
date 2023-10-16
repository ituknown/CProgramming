代码示例：

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

  ss << std::put_time(std::localtime(&tm), "%Y-%m-%d %H 点");
  std::cout << ss.str() << '\n';

  ss.str("");
  ss.clear();

  // 减1小时
  auto yesterday = now - std::chrono::hours(1);
  std::time_t yesterday_tm = std::chrono::system_clock::to_time_t(yesterday);

  ss << std::put_time(std::localtime(&yesterday_tm), "%Y-%m-%d %H 点");
  std::cout << ss.str() << '\n';

  ss.str("");
  ss.clear();

  return 0;
}
```

C++ 标准库提供了下面几个常用的时间单位：

| **时间单位**       | **标准库代码示例**             |
| :----------------- | :----------------------------- |
| 1 纳秒             | std::chrono::nanoseconds(1)    |
| 3 微秒             | std::chrono::microseconds(3)   |
| 250 毫秒           | std::chrono::milliseconds(250) |
| 60 秒（即 1 分钟） | std::chrono::seconds(60)       |
| 1 分钟             | std::chrono::minutes(1)        |
| 24 小时（即 1 天） | std::chrono::hours(24)         |

不过到了 C++ 17 之后又新增了如下几个时间单位：

| **时间单位** | **标准库代码示例**     |
| :----------- | :--------------------- |
| 1 天         | std::chrono::days(1)   |
| 3 周         | std::chrono::weeks(3)  |
| 6 个月       | std::chrono::months(6) |
| 1 年         | std::chrono::years(1)  |

不过，即使使用的不是 C++ 17，我们也可以自定义时间单位。比如天：

```cpp
#include <ratio>
#include <chrono>

int main() {

  typedef std::chrono::duration<long long, std::ratio<86400>> Days;

  auto now = std::chrono::system_clock::now();

  // 减去一天
  auto yesterday = now - Days(1);

}
```
