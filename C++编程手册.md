- Single-argument constructors must be marked explicit to avoid unintentional implicit conversions.

  > 单参数构造函数必须显式标记，以避免无意的隐式转换。

  ```cpp
  class Integer {
  public:
    // Must be marked explicit to avoid unintentional implicit conversions
    explicit Integer(int _value) : value(_value) {}
  private:
    int value;
  };
  ```
