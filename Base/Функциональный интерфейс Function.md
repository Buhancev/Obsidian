2022-09-0815:55
Tags: #Java #JavaCoreAdvanced 

__
# Функциональный интерфейс Function
- Обычно применяется для описания лямбда-выражения, отображающего информацию входного объекта на выходной.
- Находится в import java.util.function.Function;
- Имеет абстрактный метод apply, который принимает обобщенный объект Т, и возвращает объект обобщенного типа R.
- Ниже приведен пример реализации этого итерфейса и работы с ним

```java
import java.util.ArrayList;  
import java.util.Arrays;  
import java.util.List;  
//import java.util.function.Function;  
//имеет абстрактный метод apply, который принимает обобщенный объект Т, и возвращает объект обобщенного типа R  
  
public class FunctionTest {  
    @FunctionalInterface  
    public interface Function<T, R> {  
        R apply(T t);  
    }  
  
    public static<T, R> List<R> map(List<T> list, Function<T, R> f){  
        List<R> result = new ArrayList<>();  
        for(T t: list) {  
            result.add(f.apply(t));  
        }  
        return result;  
    }  
  
    public static void main(String[] Args) {  
        //[7, 2, 6]  
  
  
        List<Integer> l = map(  
                Arrays.asList("lambdas", "in", "action"),  
                (String s) -> s.length()  
        );  
  
        System.out.println(l);  
    }  
}
```
__
### Zero-Links
- [[00 Лямбда-выражения и Функциональные интерфейсы]]


