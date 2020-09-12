[TOC]



## 需要考虑的问题

- 构造函数需要是```private```访问权限，避免外部通过```new```创建实例
- 考虑对象创建时的线程安全问题
- 考虑是否支持延迟加载
- 考虑```getInstance()```性能是否高效



## 实现方式

### 1. 饿汉式

类加载时```instance```静态实例已经创建并初始化完成，不支持延时加载

```java
public class Singleton {
    private static final Singleton instance = new Singleton();
    private Singleton(){}
    public static Singleton getInstance() {
        return instance;
    }
}
```

如果实例占用资源太多或初始化耗时长，提前初始化实例是一种浪费资源行为。



### 2. 懒汉式

支持延时加载，```getInstance()```并发度低

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){}
    public static synchronized Singleton getInstance() {
        if (instance == null) {
            instance = new Singleton();
        }
    }
}
```

### 3. 双重检测

支持延迟加载，支持高并发

```java
public class Singleton {
    private static Singleton instance;
    private Singleton(){}
    public static Singleton getInstance() {
        if (instance == null) {
            synchronized(Singleton.class) {
                if (instance == null) {
                    instance = new Singleton();
                }
            }
        }
        returen instance;
    }
}
```

### 4. 静态内部类

线程安全，支持延迟加载

```java
public class Singleton {
    private Singleton() {}
    private static class SingletonHolder {
        private static final Singleton instance = new Singleton();
    }
    public static Singleton getInstance() {
        return SingletonHolder.instance;
    }
}
```

### 5. 枚举

枚举类型本身特性，保证了实例创建的线程安全性和实例的唯一性

```java
public enum Singleton {
    INSTANCE;
    public void doSomething() {
        System.out.println("doSomething");
    }
}

public class Main {
    public static void main(String[] args) {
        Singleton.INSTANCE.doSomething();
    }
}
```

## 单例模式存在的问题

### 单例对OOP特性的支持不友好

违背了基于接口而非实现的设计原则

### 单例会隐藏类之间的依赖关系

单例类不需要显示创建、不需要依赖参数传递，在函数中直接调用就可以，如果代码复杂，这种调用关系就会非常隐蔽，查看代码的时候需要仔细查看每个函数的代码实现。

### 单例对代码的扩展性不友好

### 单例类对代码的可测试性不友好

### 单例不支持有参数的构造函数

