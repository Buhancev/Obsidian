2022-09-0815:46
Tags: #Java #JavaCoreAdvanced 

__
# Функциональный интерфейс Predicate
- Может пригодиться для представления булева выражения, в котором используются объекты типа Т.
- Находится в import java.util.function.Predicate;
- Имеет абстрактный метод test, который принимает обобщенный объект Т, и возвращает boolean
- Ниже приведен пример реализации этого итерфейса и работы с ним

```java
import java.util.ArrayList;  
import java.util.List;  
//import java.util.function.Predicate;  
//имеет абстрактный метод test, который принимает обобщенный объект Т, и возвращает boolean  
  
public class PredicateTest {  
    @FunctionalInterface  
    public interface Predicate<T> {  
        boolean test(T t);  
    }  
  
    public <T> List<T> filter(List<T> list, Predicate<T> p) {  
        List<T> results = new ArrayList<>();  
        for(T t : list) {  
            if(p.test(t)){  
                results.add(t);  
            }  
        }  
        return results;  
    }  
  
    public static void main(String[] Args) {  
        List<String> listOfStrings = new ArrayList<>(); //типа заполнен  
  
        PredicateTest pt = new PredicateTest();  
        Predicate<String> nonEmptyStringPredicate = (String s) -> !s.isEmpty();  
  
        List<String> nonEmpty = pt.filter(listOfStrings, nonEmptyStringPredicate);  
    }  
}
```
__
### Zero-Links
- [[00 Лямбда-выражения и Функциональные интерфейсы]]



