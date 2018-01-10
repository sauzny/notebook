# java8函数式接口

## 函数式接口实例

Predicate <T> 接口是一个函数式接口，它接受一个输入参数 T，返回一个布尔值结果。
该接口包含多种默认方法来将Predicate组合成其他复杂的逻辑（比如：与，或，非）。

该接口用于测试对象是 true 或 false。

我们可以通过以下实例（Java8Tester.java）来了解函数式接口 Predicate <T> 的使用：

```java
import java.util.Arrays;
import java.util.List;
import java.util.function.Predicate;
 
public class Java8Tester {
   public static void main(String args[]){
      List<Integer> list = Arrays.asList(1, 2, 3, 4, 5, 6, 7, 8, 9);
        
      // Predicate<Integer> predicate = n -> true
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // n 如果存在则 test 方法返回 true
        
      System.out.println("输出所有数据:");
        
      // 传递参数 n
      eval(list, n->true);
        
      // Predicate<Integer> predicate1 = n -> n%2 == 0
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // 如果 n%2 为 0 test 方法返回 true
        
      System.out.println("输出所有偶数:");
      eval(list, n-> n%2 == 0 );
        
      // Predicate<Integer> predicate2 = n -> n > 3
      // n 是一个参数传递到 Predicate 接口的 test 方法
      // 如果 n 大于 3 test 方法返回 true
        
      System.out.println("输出大于 3 的所有数字:");
      eval(list, n-> n > 3 );
   }
    
   public static void eval(List<Integer> list, Predicate<Integer> predicate) {
      for(Integer n: list) {
        
         if(predicate.test(n)) {
            System.out.println(n + " ");
         }
      }
   }
}
```

结果：

```
$ javac Java8Tester.java 
$ java Java8Tester
输出所有数据:
1 
2 
3 
4 
5 
6 
7 
8 
9 
输出所有偶数:
2 
4 
6 
8 
输出大于 3 的所有数字:
4 
5 
6 
7 
8 
9 
```

## 所有接口

共43个接口，按照输入参数的不同，将这些接口分为三类：

- 类型一，输入参数一个
- 类型二，输入参数两个，这两个参数类型相同
- 类型三，输入参数两个，这两个参数类型不同

### 一，输入参数一个

![-](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/783a2fcdd8178cee7b0585198b9cc5ad3dfc66439d34733d9e37704d85452be88512941ccf75fa03b182cc141e3eff94?pictype=scale&from=30113&version=2.0.0.2&uin=361376366&fname=java-fi-01.png&size=1024) | 无参数 | T | double | int | long |
---|---|---|---|---|---
**T** | Supplier<T> | UnaryOperator<T> |  |  | 
**R** |  | Function<T,R> | DoubleFunction<R> | IntFunction<R> | LongFunction<R>
**void** |  | Consumer<T> | DoubleConsumer | IntConsumer | LongConsumer
**boolean** | BooleanSupplier | Predicate<T> | DoublePredicate | IntPredicate | LongPredicate
**double** | DoubleSupplier | ToDoubleFunction<T> | DoubleUnaryOperator | IntToDoubleFunction | LongToDoubleFunction
**int** | IntSupplier | ToIntFunction<T> | DoubleToIntFunction | IntUnaryOperator | LongToIntFunction
**long** | LongSupplier | ToLongFunction<T> | DoubleToLongFunction | IntToLongFunction | LongUnaryOperator

### 二，输入参数两个，这两个参数类型相同

![-](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/783a2fcdd8178cee7b0585198b9cc5ad3dfc66439d34733d9e37704d85452be88512941ccf75fa03b182cc141e3eff94?pictype=scale&from=30113&version=2.0.0.2&uin=361376366&fname=java-fi-01.png&size=1024) | T | double | int | long |
---|---|---|---|---
**T** | BinaryOperator<T> |  |  | 
**double** |  | DoubleBinaryOperator |  | 
**int** |  |  | IntBinaryOperator | 
**long** |  |  |  | LongBinaryOperator

### 三，输入参数两个，这两个参数类型不同

![-](http://picabstract.preview.ftn.qq.com:8080/ftn_pic_abs_v2/783a2fcdd8178cee7b0585198b9cc5ad3dfc66439d34733d9e37704d85452be88512941ccf75fa03b182cc141e3eff94?pictype=scale&from=30113&version=2.0.0.2&uin=361376366&fname=java-fi-01.png&size=1024) | T型+U型 | Object+double | Object+int | Object+long |
---|---|---|---|---
**void** | BiConsumer<T,U> | ObjDoubleConsumer<T> | ObjIntConsumer<T> |  ObjLongConsumer<T>
**R** | BiFunction<T,U,R> |  |  | 
**boolean** | BiPredicate<T,U> |  |  | 
**double** | ToDoubleBiFunction<T,U> |  |  | 
**int** | ToIntBiFunction<T,U> |  |  | 
**long** | ToLongBiFunction<T,U> |  |  |