```c++
#include <functional>
#include <iostream>
#include <string>

template <typename Arg>
void print_args(Arg&& _arg) {
  std::cout << _arg << ' ';
}

template <typename T, typename... Args>
void print_args(T&& t, Args&&... args) {
  print_args(std::forward<T>(t));
  std::cout << "sizeof(args)..." << sizeof...(args) << '\n';

  // print_args(1, 2, 3, 4) 为例:
  // 当 t 为3时, args 此时只有一个 4, 因此最后调用的是 print_args(Arg&&)
  print_args(std::forward<Args>(args)...);
}

int main() {

  print_args(1, 2, 3, 4);

  std::cout << '\n';
  std::cout << "------------";
  std::cout << '\n';

  int a{1};
  double b{2.0};
  std::string c{"h"};
  char d{'A'};
  print_args(a, b, c, d);

  return 0;
}
```