# Lambda表达式 

- 函数式接口:有且仅有一个抽象方法的接口就被称作函数式接口  @FunctionalInterface

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
        cacle((x,y)->x+y);
    }
    public static void cacle(Lam lam){
        System.out.println(lam.sum(1, 2));
    }
}
```

- 函数接口分类

| 类型   | 函数接口      | 抽象方法           |
| ------ | ------------- | ------------------ |
| 消费型 | Consumer<T>   | void accept(T t)   |
| 供给型 | Supply<T>     | T get()            |
| 断言型 | Predicate<T>  | boolean test(T t); |
| 函数型 | Function<T,R> | R apply(T t)       |

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