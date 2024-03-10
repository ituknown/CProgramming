# 前言

信号槽是 Qt 框架引以为豪的机制之一。

所谓信号槽，实际就是观察者模式。当某个事件发生之后（比如，按钮检测到自己被点击了一下），它就会发出一个信号（signal）。这种触发是没有目的的，类似广播。

如果有对象对这个信号感兴趣，它就会使用连接（connect）函数，将想要处理的信号和自己的一个函数（称为槽 slot）进行绑定来处理这个信号。

也就是说，当信号发出时，被连接的槽函数会自动被回调。这就类似观察者模式：当发生了感兴趣的事件，某一个操作就会被自动触发。

信号和槽是Qt特有的信息传输机制，是Qt设计程序的重要基础，它可以让互不干扰的对象建立一种联系。

槽的本质是类的成员函数，其参数可以是任意类型的，和普通C++成员函数几乎没有区别。它可以是虚函数、也可以被重载；可以是 public、protected、private；也可以被其他C++成员函数调用。唯一区别的是：槽可以与信号连接在一起，每当和槽连接的信号被发射的时候，就会调用这个槽。

# 信号声明

信号与普通函数的区别是：信号必须使用 `signals` 宏修饰，只有声明不能有具体的实现。另外，信号的返回类型都是 void，不要指望能从信号返回什么有用信息。信号由 moc 自动产生，它们不应该在 .cpp 文件中实现。

```c++
// Example.h
class ExampleSignal : public QObject {
  Q_OBJECT

 public:
  explicit ExampleSignal(QObject* parent = nullptr);

 signals:

  // 声明一个信号, 返回值必须是 void
  void clicked();
}
```

如果在源文件中进行实现（如下）。在编译时就会出现链接错误，并提示重复的符号定义。 

```c++
// Example.cpp

// 注意: 不能在源文件中像这样实现
void ExampleSignal::clicked() {

}
```

信号可以是有参的也可以是无参的，并且可以有任意多个重载版本：

```c++
class ExampleSignal : public QObject {
  Q_OBJECT

 public:
  explicit ExampleSignal(QObject* parent = nullptr);

 signals:
 
  // 无参信号
  void clicked();
  
  // 有参重载版本
  void clicked(const QString);
  
  // 可以继续重载
  void clicked(const QString, int);
}
```

# 槽的声明

槽是普通的 C++ 成员函数，可以被正常调用，它们唯一的特殊性就是很多信号可以与其相关联。当与其关联的信号被发射时，这个槽就会被调用。

槽也可以有参数，但信号槽要求信号和槽的参数一致，所谓一致，是参数类型一致。允许的情况是，槽函数的参数可以比信号的少。

即便如此，槽函数存在的那些参数的顺序也必须和信号的前面参数顺序一致。这是因为，你可以在槽函数中选择忽略信号传来的数据（也就是槽函数的参数比信号的少），但是不能说信号根本没有这个数据（就是槽函数的参数比信号的多，这是不允许的）。

既然槽是普通的成员函数，因此与其它的函数一样，它们也有存取权限。槽的存取权限决定了谁能够与其相关联。同普通的 C++ 成员函数一样，槽函数也分为三种类型，即 public slots、private slots 和 protected slots。

```c++
class ExampleSlot : public QObject {
  Q_OBJECT

 public:
  explicit ExampleSlot(QObject* parent = nullptr);

 // 这里以 public slots 为例
 public slots:

  QString onClicked();

  // 重载版本
  void onClicked(const QString);

  // 重载版本
  QString onClicked(const QString);
}
```

# 信号与槽的连接

通过调用 QObject 对象的 connect 函数来将某个对象的信号与另外一个对象的槽函数相关联，这样当发射者发射信号时，接收者的槽函数将被调用。该函数的定义如下：

```c++
static QMetaObject::Connection connect(
    const QObject *sender, 
    const QMetaMethod &signal, 
    const QObject *receiver, 
    const QMetaMethod &method, 
    Qt::ConnectionType type = Qt::AutoConnection
);
```

这个函数的作用就是将发射者 sender 对象中的信号 signal 与接收者 receiver 中的 method 槽函数联系起来。

# 最简单的信号和槽

为了方便演示，先定义一个 Button，然后定义两个重载的信号：

```C++
// 信号发出者
class MyButton : public QWidget {
  Q_OBJECT

 public:
  explicit MyButton(QObject* parent = nullptr);

 signals:
  void clicked();
}

// 信号接受者
class Reception : public QWidget {
  Q_OBJECT

 public:
  explicit Reception(QObject* parent = nullptr);

 public slots:
  void onClicked();
}
```

那么想将  Reception 的槽函数与  MyButton 信号建立连接，常用的做法是：

```C++
MyButton* button{};
Reception* reception{};

QObject::connect(button, &MyButton::clicked, reception, &Reception::onClicked);
```

不过个人更喜欢用 Lambda 函数的方式，如果槽函数中的内容比较简单的话，没必要再去单独定义一个槽来连接，直接用Lambda 函数会更简单：

```c++
QObject::connect(button, &MyButton::clicked, []() {
	// do something....
});
```

# 重载信号和槽

前面示例中的写法看起来很简洁，但是有一些坑需要注意。信号和槽函数都只有一个还好（即使信号有参），如果 MyButton 中的信号有重载版本：

```c++
class MyButton : public QWidget {
  Q_OBJECT

 public:
  explicit MyButton(QObject* parent = nullptr);

 signals:
  void clicked();
  void clicked(QString); // 有参重载版本
}
```

这继续使用前面的写法，会报错下面的错误：

```
error: no matching member function for call to 'connect' QObject::connect(this, &MyButton::clicked, reception, &Reception::onClicked);
```

这是因为我们自定义的 MyButton 中存在两个重载信号，然后用这种 connect 的方式会无法识别到底想要连接哪个信号。所以，如果信号是重载的话，正确的做法是使用函数指针进行替换：

```C++
// 无参信号指针
void (MyButton::*clicked)() = &MyButton::clicked;

// 有参信号指针
void (MyButton::*clicked_string)(QString) = &MyButton::clicked;

// 将有参的信号和槽建立连接
QObject::connect(button, clicked_string, reception, &Reception::onClicked);
```

问题又来了，如果我的 Reception 中的槽也有重载版本的话，还是会报同样的错误。因为编译器不知道你想要真正连接哪个槽：

```c++
class Reception : public QWidget {
  Q_OBJECT

 public:
  explicit Reception(QObject* parent = nullptr);

 public slots:
  void onClicked();
  void onClicked(QString);
}
```

对于这种情况，你还需要定义槽的函数指针：

```C++
// 有参信号指针
void (MyButton::*clicked_string)(QString) = &MyButton::clicked;

// 有参槽函数指针
void (Reception::*onClicked_string)(QString) = &Reception::onClicked

// 有参信号和有参槽建立连接
QObject::connect(button, clicked_string, reception, onClicked_string);
```

另外对于重载版本，不一定一定要使用函数指针。也可以使用 C++ 的 static_cast 类型转换(编译时进行语法检查)，或者 C++11 的 QOverload::of，C++14 的 qOverload：

```c++
// 使用 static_cast
QObject::connect(button, static_cast<void (MyButton::*)(QString)>(&MyButton::clicked), [=](const QString& text) {
  qDebug() << text;
});

// 使用 QOverload
// QOverload<> 里面是参数列表, of() 里面是成员函数地址  
QObject::connect(button, QOverload<QString>::of(&MyButton::clicked), [](const QString& text) {
  qDebug() << text;
});
```

不过对于重载版本，个人还是比较喜欢使用函数指针~

# 断开信号和槽

当信号与槽没有必要继续保持关联时，我们可以使用 disconnect 函数来断开连接。其定义如下：

```c++
static bool disconnect(const QObject *sender, const char *signal, const QObject *receiver, const char *member);
static bool disconnect(const QObject *sender, const QMetaMethod &signal, const QObject *receiver, const QMetaMethod &member);
inline bool disconnect(const char *signal = nullptr, const QObject *receiver = nullptr, const char *member = nullptr) const;
inline bool disconnect(const QObject *receiver, const char *member = nullptr) const;
static bool disconnect(const QMetaObject::Connection &);
```