## 第4章 引入流

### 流的定义
* 流是Java API的新成员，它允许你以声明性方式处理数据集合（通过查询语句来表达，而不是临时编写一个实现）。就现在来说，你可以把它们看成遍历数据集的高级迭代器。
此外，流还可以透明地并行处理，你无须写任何多线程代码了

* 简短的定义：从支持数据操作的源生成的元素序列
* 集合讲的是数据，流讲的是计算
* 很多流操作本身会返回一个流，多个操作链接起来，可以构成一个更大的流

### 集合与流
* 集合是内存中的数据结构，集合中的每个元素都要先算出来才能添加到集合中。流是概念上固定的数据结构，元素是按需计算的。
* 流像是一个延迟创建的集合
* 集合是外部迭代，流是内部迭代
* 流只能遍历一次

### 流的操作
两类操作
* 中间操作，可以连起来的操作，生成流的操作
* 终端操作，关闭流的操作，结果是任何不是流的值

### 使用流
使用流包含三件事：
1. 一个数据源（比如集合）来执行一个查询
2. 一个中间操作链，形成一个流的流水线
3. 一个终端操作，执行流水线，生成结果

## 第5章 使用流
### 筛选
* filter 筛选，接收一个判断的函数，返回符合结果的元素
* distinct 去重，去掉重复的元素

### 流的切片
采用filter需要遍历整个流中的数据，takeWihle可以对流进行分片，并且在遭遇第一个不符合的元素时停止

dropWhile操作是对takeWhile操作的补充。它会从头开始，丢弃所有谓词结果为false的元素
```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        specialMenu.stream()
                .takeWhile(dish -> dish.getCalories() < 320)
                .map(dish -> dish.getName())
                .forEach(System.out::println);
    }
```
#### 截断流

流支持limit(n)方法，该方法会返回另一个不超过给定长度的流。所需的长度作为参数传递给limit。如果流是有序的，则最多会返回前n个元素
```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        specialMenu.stream()
                .filter(dish -> dish.getCalories() < 400)
                .limit(3)
                .map(dish -> dish.getName())
                .forEach(System.out::println);
    }
    
    result---------------
    seasonal fruit  
    prawns
    rice
```

#### 跳过元素
流还支持skip(n)方法，返回一个扔掉了前n个元素的流。如果流中元素不足n个，则返回一个空流
```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        specialMenu.stream()
                .filter(dish -> dish.getCalories() < 400)
                .skip(2)
                .map(dish -> dish.getName())
                .forEach(System.out::println);
    }
    
    result------------
    rice
    
 ```
### 映射
一个非常常见的数据处理套路就是从某些对象中选择信息。比如在SQL里，你可以从表中选择一列。Stream API也通过map和flatMap方法提供了类似的工具
#### 对流中的每一个元素应用函数
流支持map方法，它会接受一个函数作为参数。这个函数会被应用到每个元素上，并将其映射成一个新的元素，使用映射一词，是因为它和转换类似，但其中的细微差别在于它是“创建一个新版本”而不是去“修改
```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        specialMenu.stream()
                .map(Dish::getName)
                .forEach(System.out::println);
    }
    
    result-------------
    seasonal fruit
    prawns
    rice
    chicken
    french fries
    
```
#### 流的扁平化
flatMap方法让你把流中的每一个值都换成另一个流，然后把所有流连接起来成为一个流
使用flatMap方法的效果是，各个数组并不是分别映射成一个流，而是映射成流的内容。所有使用flatMap(Arrays::stream)时生成的单个流都被合并起来，即扁平化为一个流
```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        String[] arrayOfWords = {"Goodbye", "World"};
        Stream<String> streamDemoOfwords = Arrays.stream(arrayOfWords);

        streamDemoOfwords
                .map(str -> str.split(""))
                .flatMap(Arrays::stream)
                .distinct()
                .forEach(System.out::println);
    }
    
    result--------
    G
    o
    d
    b
    y
    e
    W
    r
    l
```

### 查找和匹配

另一个常见的数据处理套路是看看数据集中的某些元素是否匹配一个给定的属性。Stream API通过allMatch、anyMatch、noneMatch、findFirst和findAny方法提供了这样的工具

anyMatch 至少匹配一个  结果boolean值，终端操作 allMatch匹配所有，也是终端操作;
anyMatch、allMatch和noneMatch这三个操作都用到了所谓的短路;
短路操作可以将无限流变成有限流

```java
    public static void main(String[] args) {
        List<Dish> specialMenu = Arrays.asList(
                new Dish("seasonal fruit", true, 120, Dish.Type.OTHER),
                new Dish("prawns", false, 300, Dish.Type.FISH),
                new Dish("rice", true, 350, Dish.Type.OTHER),
                new Dish("chicken", false, 400, Dish.Type.MEAT),
                new Dish("french fries", true, 530, Dish.Type.OTHER));

        if (specialMenu.stream().anyMatch(Dish::isVegetarian)) {
            System.out.println("is vegetarian");
        }
    }
    result-------------
    true
```
#### Option类
Optional<T>类（java.util.Optional）是一个容器类，代表一个值存在或不存在
    
#### 查找第一个元素
    
