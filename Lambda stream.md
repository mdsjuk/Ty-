# Lambda表达式 

-  函数式接口:有且仅有一个抽象方法的接口就被称作函数式接口  @FunctionalInterface

```java
interface Lam {
    default void sum() {
        System.out.println("default函数");
    }
    //只能有一个抽象方法
    int sum(int a, int b);
}
class CKA {
    public static void main(String[] args) {
        //匿名内部类实现接口
        cacle(new Lam() {
            @Override
            public int sum(int a, int b) {
                return a+b;
            }
        });
        //Lamdba表达式实现 要求接口为函数接口
        cacle((x,y)->{return x+y});
    }
    public static void cacle(Lam lam){
        System.out.println(lam.sum(1, 2));
    }
}
```

- 函数接口分类

| 类型   | 函数接口                        | 抽象方法           |
| ------ | ------------------------------- | ------------------ |
| 消费型 | Consumer<T>                     | void accept(T t)   |
| 供给型 | Supply<T>                       | T get()            |
| 断言型 | Predicate<T>                    | boolean test(T t); |
| 函数型 | Function<T,R> BiFunction<T,U,R> | R apply(T t)       |

```java
class CKA {
public static void Fun(int a,Function<Integer,Integer> f){
    f.apply(a);
}
    public static void main(String[] args) {
        //函数型接口示例
        Fun(1231,(x)->x+10000);
    }
```

- 方法引用 构造引用
 ```java
  public class Lamdba {
      public static void main(String[] args) {
          addtest addtest=(x,y)->{return x;};
          /**方法引用 将方法作为Lambda的值 以下两种等价**/
          addtest addtest1=(x,y)->static_add(x,y);
          /**类::静态方法
           * 被调用方法要和接口中的方法描述符一样 此处静态方法**/
          addtest addtest2=Lamdba::static_add;
          System.out.println(addtest2.add(1, 3));
             /** 对象::实例方法
           *  out为对象 print是实例方法 **/
         Consumer consumer=System.out::print;
         consumer.accept("");
         /**类::实例方法
         **/
          BiPredicate<String,String> consumer1=(x, y)-> x.equals(y);
          /** 要求 x为String类型调用者 y为参数
          **/
          BiPredicate<String,String> consumer2=String::equals;
          /**调用有参的构造方法 具体要看Function的方法参数
          **/
        Function<Integer,Student> student=Student::new;
        Student s=student.apply(2);
      }
      /**被调用的静态方法
      **/
      public static int static_add(int a,int b){
          return a*b;
      }
  }
  /**Lambda表达式接口
  **/
  interface addtest{
       int add(int a,int b);
  }
 ```

# Stream

- 特点

  ​	stream不存储数据，而是按照特定的规则对数据进行计算，一般会输出结果。	

  ​	stream不会改变数据源，通常情况下会产生一个新的集合或一个值。

  ​	stream具有延迟执行特性,惰性，只有调用终端操作时，中间操作才会执行。

​	![Stream分类](D:\浏览器下载文件\Stream分类.png)

无状态：指元素的处理不受之前元素的影响；

有状态：指该操作只有拿到所有元素之后才能继续下去。

非短路操作：指必须处理所有元素才能得到最终结果；

短路操作：指遇到某些符合条件的元素就可以得到最终结果，如 A || B，只要A为true，则无需判断B的结果。

实例:

1. 从员工集合中筛选出salary大于8000的员工，并放置到新的集合里。
2. 统计员工的最高薪资、平均薪资、薪资之和。
3. 将员工按薪资从高到低排序，同样薪资者年龄小者在前。
4. 将员工按性别分类，将员工按性别和地区分类，将员工按薪资是否高于8000分为两部分

- 集合与数组 创建流

```java
List<String> list = Arrays.asList("a", "b", "c");
// 创建一个顺序流
Stream<String> stream = list.stream();
// 创建一个并行流
Stream<String> parallelStream = list.parallelStream();

String[] s={"2","3"};
Arrays.stream(s).collect(Collectors.toList());
```

- 中间操作
  - 筛选与切片
            filter：过滤流中的某些元素
            limit(n)：获取n个元素
            skip(n)：跳过n元素，配合limit(n)可实现分页
            distinct：通过流中元素的 hashCode() 和 equals() 去除重复元素
  - map：接收一个函数作为参数，该函数会被应用到每个元素上，并将其映射成一个新的元素。
    flatMap：接收一个函数作为参数，将流中的每个值都换成另一个流，然后把所有流连接成一个流。
  - sorted()：自然排序，流中元素需实现Comparable接口
    sorted(Comparator com)：定制排序，自定义Comparator排序器 
-  匹配、聚合操作
          allMatch：接收一个 Predicate 函数，当流中每个元素都符合该断言时才返回true，否则返回false
          noneMatch：接收一个 Predicate 函数，当流中每个元素都不符合该断言时才返回true，否则返回false
          anyMatch：接收一个 Predicate 函数，只要流中有一个元素满足该断言则返回true，否则返回false
          findFirst：返回流中第一个元素
          findAny：返回流中的任意元素
          count：返回流中元素的总个数
          max：返回流中元素最大值
          min：返回流中元素最小值

-  reduce
  - Optional<T> reduce(BinaryOperator<T> accumulator)：第一次执行时，accumulator函数的第一个参数为流中的第一个元素，第二个参数为流中元素的第二个元素；第二次执行时，第一个参数为第一次函数执行的结果，第二个参数为流中的第三个元素；依次类推。
  -  T reduce(T identity, BinaryOperator<T> accumulator)：流程跟上面一样，只是第一次执行时，accumulator函数的第一个参数为identity，而第二个参数为流中的第一个元素。
  -  <U> U reduce(U identity,BiFunction<U, ? super T, U> accumulator,BinaryOperator<U> combiner)：在串行流(stream)中，该方法跟第二个方法一样，即第三个参数combiner不会起作用。在并行流(parallelStream)中,我们知道流被fork join出多个线程进行执行，此时每个线程的执行流程就跟第二个方法reduce(identity,accumulator)一样，而第三个参数combiner函数，则是将每个线程的执行结果当成一个新的流，然后使用第一个方法reduce(accumulator)流程进行规约。

```java
//经过测试，当元素个数小于24时，并行时线程数等于元素个数，当大于等于24时，并行时线程数为16
List<Integer> list = Arrays.asList(1, 2, 3, 4, 5);

Integer v = list.stream().reduce((x1, x2) -> x1 + x2).get();
System.out.println(v);   

Integer v1 = list.stream().reduce(10, (x1, x2) -> x1 + x2);
System.out.println(v1);  
// 第二个x*y 在stream中不起作用
Integer v2 = list.stream().reduce(0,
        (x1, x2) -> {
            System.out.println("stream accumulator: x1:" + x1 + "  x2:" + x2);
            return x1 - x2;
        },
        (x1, x2) -> {
            System.out.println("stream combiner: x1:" + x1 + "  x2:" + x2);
            return x1 * x2;
        });
System.out.println(v2); 

/*
 * 计算过程是(1,2,3,4,5) -> 1*2*3*4*5=120？
 * 实际是(1,2,3,4,5) -> ((1,2),(3,(4,5))) -> (2,(3,20)) -> (2,60) -> 120
 */
result = list.stream().parallel().reduce((a, b) -> a * b).get();
System.out.println(result);

/*
 * 计算过程是(1,2,3,4,5) -> (2,4,6,8,10) -> 2*4*6*8*10=3840？
 * 实际是(1,2,3,4,5) -> (2,4,6,8,10) -> ((2,4),(6,(8,10))) -> (8,(6,80)) -> (8,480) -> 3840
 */
result = list.stream().parallel().reduce(2, (a, b) -> a * b);
System.out.println(result);
/*
 * 计算过程是(1,2,3,4,5) -> (2,4,6,8,10) -> 2+4+6+8+10=30？
 * 实际是(1,2,3,4,5) -> (2,4,6,8,10) -> ((2,4), (6,(8,10))) -> (6,(6,18)) -> (6,24) -> 30
 */
result = list.stream().parallel().reduce(2, (a, b) -> a * b, (x, y) -> x + y);
System.out.println(result);

//并行流 要和串行流结果一样就不能添加默认值
 Integer v3 = list.parallelStream().reduce(0,
                (x1, x2) -> {
                    System.out.println("parallelStream accumulator: x1:" + x1 + "  x2:" + x2);
                    return x1 - x2;
                },
                (x1, x2) -> {
                    System.out.println("parallelStream combiner: x1:" + x1 + "  x2:" + x2);
                    return x1 + x2;
                });
        System.out.println(v3);
```