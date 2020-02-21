### lambda表达式



>- Lambada表达式也被称为箭头函数、匿名函数、闭包
>- Lambada表达式体现的事轻量级函数式编程思想
>- “->”符号是Lambada表达式核心操作符，符号左侧是操作参数，符号右侧是操作表达式
>- JDK1.8以上



#### 1.引言：需求场景 - 线程类的创建

```java
//1.传统模式，匿名内部类
new Thread(new Runnable() {
  @Override
  public void run() {
    System.out.println("a");
  }
}).start();


//2.jdk8新特性，lambda表达式优化线程模式
new Thread(()->{
  System.out.println("a");
}).start();
```



#### 2.Lambda基础知识

##### 2.1 函数式接口

1. 函数式接口，就是Java类型系统中的接口；
2. 只包含一个抽象方法的特殊接口
3. 语义化检测注解：@Functionallnterface，是一种避免在功能接口中意外添加抽象方法的工具

```java
/**
 * 1.只包含一个未实现的方法（不包括Object继承来的方法，如toString()等）
 * 2.@FunctionalInterface注解
 */
@FunctionalInterface
public interface IUserCredential {
    //0.定义了唯一一个未实现的方法；
    String verifyUser(String username);

    //Java8接口开始支持 default ，static方法，这样扩展方法时，可以不用每个实现类都去实现，
    //接口中定义，所有实现类都可以调用；
  
    //1.默认方法(，可选)：实现类可重写
    default String getCredential(String username) {
				return username;
    }
  
    //2.静态方法（可选），实现类不可重写，适合于提供实用方法，例如空检查、集合排序等
    //  不实例化，直接用类名调用的方法
    static boolean getNickName(){
				return true;
    }
}

public static void main(String[] args) {
  //1.实现类可以直接调用接口中定义的“默认方法”
	IUserCredential ic = new UserCredentialImpl();
  ic.getCredential("admin");
  
  //2.静态类,类名调用
  IUserCredential.getNickName();
}
```



##### 2.2 函数式接口和Lambda表达式的关系

> - 函数式接口，只包含一个操作方法
> - Lambda表达式，只能操作一个方法
> - Lambda表达式，核心就是一个函数式接口的实现



```java
//1.常规的单独用一个类实现接口，然后new 出这个实现类 
public class UserCredentialIml implements IUserCredential{
  @Override
  public String verifyUser(String username){
    return username;
  }
}
IUserCredential ic = new UserCredentialImpl();
ic.verifyUser("admin");


//2.匿名内部类的方法 实现一个接口
IUserCredential ic2 = new IUserCredential(){
  @Override
  public String verifyUser(String username){
    return username;
  }
};
ic2.verifyUser("admin");


//3. Lambda表示式的实现接口
IUserCredential ic3 = （String username）->{
  return username;
};
ic3.verifyUser("admin");
```



##### 2.3 jdk中常见的函数式接口

- java.lang.Runnable
- java.lang.Comparable
- java.lang.Comparator
- java.io.FileFilter
- More... JDK8提供了java.util.function包，提供了常用的函数式功能接口



Java.util.function.Predicate<T>

- 接收参数对象T，返回一个boolean类型结果
- 