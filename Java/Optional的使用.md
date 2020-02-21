### Java Optional的使用



> Optional是一个可以包含NULL值的容器，或者一个包装类，只包装了一个属性。
>
> 使用Optional封装返回值，明确告诉调用者返回结果可能为空，使用Optional接口方法进行操作。



#### 1.一个常见的NPE例子：

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



#### 2.使用Optional封装返回体

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

> 注意，不要使用了Optional，却又返回null；

```java
public Optional<Camera> getCamera(Long id) {
	if (null != id) {
		return Optional.of(new Camera());
	}
  //错误示例1，违背了Optional的初衷，同时，
	return null;
  //错误示范2，编码错误，会抛出NullPointerException
  return Optional.of(null);
}
```



#### 3.Optional最佳实践

1.if判空

```java
//常见判空
if(cameraSn != null){
  Camera.method(cameraSn);
}else{
  Camera.method2(cameraSn);
}

//链式调用仅需一行：Optional.ofNullable包装cameraSn，表示其可能为空,如果存在，则传入Camera.method中处理。
Optional.ofNullable(cameraSn).ifPresent(Camera::method).orElse(Camera::method2)；
```



2.多级if判空

```java
//常见多级调用，如果有一个环节为null，就会出现NPE异常。我们必须保证每级都不为空。 
String cameraSn = requestBean.getGroup().getCamera().getCameraSn();

//使用map来get子对象,自动过滤Null情况
Optional.ofNullable(requestBean)
  .map(RequsetBean::getGroup)
  .map(Group::getCamera)
  .map(Camera::getCameraSn)
  .ifPresent(Camera::method);
```



