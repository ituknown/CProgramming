# 前言

使用C++和QML进行交互时，通常都是在 `main.cpp`中使用 `qmlRegisterType` 来注册类型。`qmlRegisterType` 具有两个重载方法：

```c++
template<typename T>
int qmlRegisterType(const char *uri, int versionMajor, int versionMinor, const char *qmlName);

template<typename T, int metaObjectRevision>
int qmlRegisterType(const char *uri, int versionMajor, int versionMinor, const char *qmlName);
```

第一个重载方法是将类型 T 注册为新类型的标准形式。 而第二个重载方法多了个参数 `int metaObjectRevision`，这个参数用于指定元对象修订版本号。当你想要添加、修改类的属性、方法或信号，同时又不想改变类型的主要版本号时，可以使用这个重载方法。这样，QML中的旧代码仍然可以工作，而新代码可以使用更新的元数据（不过重来没用过）。

例如，将 C++ 类 Worker 注册在名为 `cn.example.Worker` 的类型命名空间下（我习惯的做法都是将一个类注册在一个命名空间下，并以类名结尾），1.0 版对应的 QML 类型为 Worker：

```c++
```cpp
// Worker.h
#include <QObject>

class Worker : public QObject
{
    Q_OBJECT

public:
    // ...
};
```

在 main 中注册 QML 类型：

```c++
// main.cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>

#include "Worker.h"

int main(int argc, char *argv[]) {
    QGuiApplication app(argc, argv);
    QQmlApplicationEngine engine;

    qmlRegisterType<Worker>("cn.example.Worker", 1, 0, "Worker"); // 注册

    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));
    return app.exec();
}
```

注册后，可以通过导入指定的类型命名空间和版本号在 QML 中使用该类型（QT6之后可以忽略版本号，缺省时默认会查找最高版本）：

```qml
// Qt6 之前
// import cn.example.Worker 1.0

// Qt6 之后可以忽略版本号
import cn.example.Worker

Worker {
	// ...
}
```

# 最佳实践

如果需要注册的类型特别多，还一窝蜂的全部写在 `main` 函数中的话，就会感觉不够优雅。为了代码的可维护性和可读性，通常都会考虑下面两种最佳实践：

1、**模块化注册**：创建一个专门的函数或类来处理所有与QML相关的注册，然后在 `main` 函数中调用这个函数或创建这个类的实例。这样可以保持 `main` 函数的简洁和清晰。

2、**使用单例模式**：对于只需要一个实例的类，可以使用 `qmlRegisterSingletonType` 来注册单例，这样在QML中可以直接访问这个单例而不需要创建多个实例。

## 模块化注册

模块化注册通常的做法是创建一个（或多个）静态方法类（如 `TypeRegistrar`），在这个类中定义一个静态方法 `registerTypes()` 来执行 `qmlRegisterType` 调用。之后在 main 函数中调用该静态方法。示例如下：

```c++
// TypeRegistrar.h
#ifndef TYPEREGISTRAR_H
#define TYPEREGISTRAR_H

#include <QtQml>
#include "Worker.h"

class TypeRegistrar {
public:
    static void registerTypes() {
        qmlRegisterType<Worker>("cn.example.Worker", 1, 0, "Worker");
        // ... 注册其他类型
        // ... 也可以继续调用其他静态方法类
    }
};

#endif // TYPEREGISTRAR_H


// main.cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>

#include "TypeRegistrar.h"

int main(int argc, char *argv[]) {
    QGuiApplication app(argc, argv);
    QQmlApplicationEngine engine;

    TypeRegistrar::registerTypes(); // 使用静态方法类进行注册

    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));
    return app.exec();
}
```

## 单例模式

对于单例类，可以使用`qmlRegisterSingletonType`来注册。这里是一个单例类的示例：

```cpp
// MySingleton.h
#ifndef WORK_H
#define WORK_H

#include <QObject>

class Worker : public QObject {
    Q_OBJECT
    Q_PROPERTY(int value READ value WRITE setValue NOTIFY valueChanged)

public:
    static Worker* instance() {
        static Worker _instance;
        return &_instance;
    }

    int value() const { return m_value; }
    void setValue(int val) {
        if (m_value != val) {
            m_value = val;
            emit valueChanged();
        }
    }

signals:
    void valueChanged();

private:
    int m_value;
};

#endif // WORK_H
```

在`main.cpp`中注册单例：

```cpp
// main.cpp
#include <QGuiApplication>
#include <QQmlApplicationEngine>

#include "Work.h"

static QObject *singletonProvider(QQmlEngine *engine, QJSEngine *scriptEngine) {
    Q_UNUSED(engine)
    Q_UNUSED(scriptEngine)

    return Worker::instance();
}

int main(int argc, char *argv[]) {
    QGuiApplication app(argc, argv);
    QQmlApplicationEngine engine;

    qmlRegisterSingletonType<Worker>("cn.example.Worker", 1, 0, "Worker", singletonProvider);

    engine.load(QUrl(QStringLiteral("qrc:/main.qml")));
    return app.exec();
}
```

其中宏 `Q_UNUSED` 的作用是告诉编译器：虽然这些参数在函数中存在，但在当前函数体中并没有使用它们。因此，不要产生未使用变量的警告。

之后在QML中，就可以直接访问单例对象的属性和方法：

```qml
import cn.example.Worker

Item {
    Component.onCompleted: {
        console.log(Worker.value) // 访问单例的属性
        Worker.value = 10 // 设置单例的属性
    }
}
```

| **Note**                          |
| :-------------------------------- |
| 即使采用单利模式依然推荐使用模块化注册单利，这里只是为了说明方便。 |
