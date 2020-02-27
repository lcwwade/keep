### List操作

#### 1.List创建

**1.1可变List**

```java
//1.JDK创建,基本都需要制定初始容量
List<String> list = new ArrayList<>();
//可选赋初始值
list.add("string");

//2.克隆方式,list发生变化不会影响clone的值。
List<String> clone = new ArrayList<>(list);

//3.Guava: Lists.newArrayList-> 带初始值的可变list
List<String> list = Lists.newArrayList("A","B","C");
List<String> list1 = Lists.newArrayList(list)
  
//4.Stream 方式
List<String> list = Stream.of("A","B","C").collect(Collectors.toList());
```



**1.2不可伸缩List**

List长度不可变，内容元素可变，即集合大小不可变。

```java
//1.JDK - Arrays.asList(),返回的集合，可以修改替换元素，但不能增删元素
String[] strArray = {"a","b","c"};
List<String> list = Arrays.asList("a","b","c");
List<String> list1 = Arrays.asList(strArray);//通过传入数组创建
list.set(0,"d");//支持替换元素

Camera c1 = new Camera("1");
Camera c2 = new Camera("2");
Camera c3 = new Camera("3");
List<Camera> list2 = Arrays.asList(c1,c2,c3);
list2.get(1).setCameraId("1");//支持修改元素

```



**1.3不可伸缩，不可变List**

元素不可替换，内容可更改，即集合大小不可变，元素不需要替换。

```java
//1.JDK 可接受null - Collections.unmodifiableList()
final List<String> list = Arrays.asList("a","b","c");
final List<String> list1 = Collections.unmodifiableList(list);

//2.Guava 不可接受null值 - ImmutableList.copyOf()，常做常量集合类
String[] strArray = {"a","b","c"};
List<String> list = Arrays.asList("a","b","c");

List<String> list1 = ImmutableList.copyOf(strArray);
List<String> list1 = ImmutableList.copyOf(list);
```



**1.4使用EmptyList代替null**

返回是集合的方法，建议不返回null，返回一个长度为零的集合

```java
//1.Commons Lang3 Collections.emptyList()
//返回的是一个常量，不可变，不会分配预留空间，省资源效率高
return Collections.emptyList()
  
//不建议Lists.newArrayList(),new ArrayList(),可变，会创建对象，分配空间。
//但返回值的调用方会修改该值时，另说。
```

