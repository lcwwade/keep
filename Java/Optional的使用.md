### Java8 Optional的使用



> Optional是一个可以包含NULL值的容器，或者一个包装类，只包装了一个属性。
>
> 使用Optional封装返回值，明确告诉调用者返回结果可能为空，使用Optional接口方法进行操作。
>
> 目的：1.链式调用，2.避免NPE

  

#### 1.引言

##### 1.1一个常见的NPE例子：

```java

/**
 * 获取摄像机
 * @param  id 唯一id
 * @return 摄像机可能为null,代表不存在
 */
public User getCamera(Long id) {
	if (null != id) {
	    return new Camera();
	}
	return null;
}
```

虽然，方法注释写着可能返回null，但如果调用者，在调用该方法后，没有进行`if (camera == null)` 判断直接使用，就会出现万恶的`NullPointerException`。



##### 1.2使用Optional封装返回体

> 如果改变一下返回值，使用Optional，告诉调用者，返回的值可能存在，也可能不存在。
>
> 调用者在使用时，自然会使用Optional接口进行操作判断。

```java
public Optional<Camera> getCamera(Long id) {
	if (null != id) {
		return Optional.of(new Camera());
	}
	return Optional.empty();
}
```

> 注意，不要使用了Optional，却又直接返回null；

```java
public Optional<Camera> getCamera(Long id) {
	if (null != id) {
		return Optional.of(new Camera());
	}
  //错误示例1，违背了Optional的初衷
	return null;
  //错误示范2，使用错误，.of()不允许为null，会抛出NPE
  return Optional.of(null);
}
```



#### 2.Optional API

**2.1 构造一个Optional**

```java
public final class Optional<T>{}

//value不可为null，否则直接NPE
Optional.of(T value)
  
//value可为空
Optional.ofNullable(T value)
  
//构造一个值为null的Optional对象
Optional.empty(): 
```



**2.2 常用方法 **

```java
//1.获取value，但是value为null，会报NoSuchElementException
public T get() ;

//2.返回value是否为null
public boolean isPresent();

//3.如果value不为null，则执行consumer式的函数，为null不跳过
public void ifPresent(Consumer<? super T> consumer) ;

//4.过滤，如果value不为null，则根据条件过滤，为null跳过
public java.util.Optional<T> filter(Predicate<? super T> predicate) ;

//5.转换，在其外面封装Optional，如果value不为null，则map转换，为null不跳过
public<U> java.util.Optional<U> map(Function<? super T, ? extends U> mapper);

//6.转换，如果value不为null，则map转换，为null不跳过
public<U> java.util.Optional<U> flatMap(Function<? super T, java.util.Optional<U>> mapper) ;

//7.value为null时，默认提供other值
public T orElse(T other);

//8.value为null时，默认提供other值
public T orElseGet(Supplier<? extends T> other);

//9.value为null时，默认提供other值
public <X extends Throwable> T orElseThrow(Supplier<? extends X> exceptionSupplier) ;

```



#### 3.Optional典型应用



**1.if判空，ifPresent+orElse 代替if+else**

常规写法：

```java
//1.else返回其他值
if(camera !== null && camera.getName()!= null){
  return camera.getName();
}else{
  return "unknown";
}

//2.else中包含方法调用
if(camera !== null && camera.getName()!= null){
  return camera.getName();
}else{
  Mock.doSomething();
  return getDefaultName();
}
//else返回的默认名字
public static String get(String name){
  return name;
}   
```

Optional写法：

```java
//1.else直接返回值：链式调用仅需一行：Optional.ofNullable包装camera，表示其可能为空,如果存在，则传入Camera.getName中处理。
return Optional.ofNullable(camera).map(Camera::getName).orElse("unknown");//else直接有默认值情况
return Optional.ofNullable(camera).map(Camera::getName).orElse(get("default"));

//3.else中包含方法调用：如果需要调用另一个接口返回结果，需要使用orElseGet()
return Optional.ofNullable(camera).map(Camera::getName)
  .orElseGet(() -> get("default"));//elseGet通过调用函数产生默认值
return Optional.ofNullable(camera).map(Camera::getName)
  .orElseGet(this::getDefaultName);
return Optional.ofNullable(camera).map(Camera::getName).orElseGet(()->{
  Mock.doSomething();
  return getDefaultName();
});
```



> **orElse()和orElseGet()的区别：**
>
> - orElse()接受类型`T`的任何参数
> - 而orElseGet()接受类型为`Supplier`的函数接口，该接口返回类型为T的对象 。
>
> orElse()在不论optional有没有值的时候都会执行，在optional为空值的情况下orElse()和orElseGet()都会执行，当optional不为空时，orElseGet()不会执行。
>
> 一般情况下，**只有当默认值已经事先定义的情况下，才使用`orElse()`，否则使用`orElseGet()`更好**，但代价就是需要传入一个`Supplier`类型的参数。



**2.多级if判空**

常规写法：

```java
//常见多级调用，如果有一个环节为null，就会出现NPE异常。我们必须保证每级都不为空。 
String cameraSn = requestBean.getGroup().getCamera().getCameraSn();

if(requestBean != null && requestBean.getGroup()!=null && requestBean.getGroup().getCamera() != null){
  dosomething();
}
```



Optional写法：

```java
//使用map来逐级get子对象,自动过滤Null情况。
//map遇到不存在的值就会返回一个不含任何值的Optional对象，直到走到else方法，等待使用者的处理
Optional.ofNullable(requestBean)
  .map(RequsetBean::getGroup)//获取group传递到下一级
  .map(Group::getCamera)//获取camera传递到下一级
  .map(Camera::getCameraSn)//获取cameraSn传递到下一级
  .ifPresent(Camera::method);//method使用cameraSn作为入参
```



> **map和flatMap的区别：**
>
> - 区别在于mapping函数的返回值不同：map是把结果自动封装成一个Optional；flatMap中的mapper返回值必须是Optional，调用结束时，flatMap不会对结果用Optional封装，需要你自己去封装。
>
> - map方法的mapping函数返回值可以是任何类型T，而flatMap方法的mapping函数必须是Optional
>
> - ```java
>   Optional<String> newName = name
>     .flatMap((value) -> Optional.of(value.toUpperCase()));
>   ```



**3.抛出异常**

常规写法：

```java
if(camera !== null && camera.getName()!= null){
  return camera.getName();
}
return new InternalException("nothing found");
```

Optional写法：

```java
return Optional.ofNullable(device).map(Device::getName).orElseThrow(()->new InternalException("nothing found"));
```



**4.一般过滤和带外部条件过滤**

> filter方法传入一个断言语句**条件的lambda表达式**，返回一个**原对象的optional**包装，所以支持**链式调用**

常规写法：

```java
//外部条件condition
if(device != null && condition){
  return camera.getCameraId;
}else{
  return "unknown";
}
```

Optional写法：

```java
//1.一般filter
Optional.ofNullable(camera)
  .filter(c->c.getName()!=null)
  .ifPresent(Camera::method);

//2.带外部条件过滤
return Optional.ofNullable(camera)
  .filter(c->condition)
  .map(camera:getCameraId)
  .orElse("unknown");

```



#### 4.不推荐使用场景

1.不要用作类的字段，因为Optional不可序列化

2.不要用作构造函数和方法的参数，会造成不必要的复杂代码

3.不建议在`isPresent`后使用`get`，这和使用`==`判断相同，无法体现Optional的优越性



> posted by Zero