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

