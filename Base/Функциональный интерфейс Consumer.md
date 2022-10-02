2022-09-0815:49
Tags: #Java #JavaCoreAdvanced 

__
# Функциональный интерфейс Consumer
- Обычно применяется для доступа к объекту типа Т и  не возвращает ничего( возвращает void ).
- Находится в import java.util.function.Consumer;
- Имеет абстрактный метод accept, который принимает обобщенный объект Т, и возвращает void( ничего )
- Ниже приведен пример реализации этого итерфейса и работы с ним

```java
//import java.util.function.Consumer;  
//имеет абстрактный метод accept, который принимает обобщенный объект Т, и возвращает void(ничего)  
  
import java.util.ArrayList;  
import java.util.Arrays;  
import java.util.List;  
  
public class ConsumerTest {  
    @FunctionalInterface  
    public interface Consumer<T> {  
        void accept(T t);  
    }  
  
    public <T> void forEach(List<T> list, Consumer<T> c) {  
        for(T t : list) {  
            c.accept(t);  
        }  
    }  
  
    public static void main(String[] Args) {  
  
        ConsumerTest ct = new ConsumerTest();  
  
        ct.forEach(  
                Arrays.asList(1, 2, 3, 4, 5),  
                (Integer i) -> System.out.println(i)  
        );  
    }  
}
```
__
### Zero-Links
- [[00 Лямбда-выражения и Функциональные интерфейсы]]



