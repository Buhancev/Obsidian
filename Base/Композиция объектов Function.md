2022-09-2110:14
Tags: #Java #JavaCoreAdvanced 

__
# Композиция объектов Function
В интерфейсе Function для этой цели предусмотренно два метода - 
*andThen* и *compose*, и оба они возвращают экземпляр Function.

## andThen
Возвращает функцию, которая сначала применяет заданную функцию к входным данным, а затем применяет другую заданную функцию к полученным данным. Например, если даны функция f, увеличивающая число на 1: (x -> x + 1), и другая функция g, умножающая число на 2: 
(g -> g * 2), то можно их объединить для создания функции h, которая сначала увеличивает число на 1, а потом умножает полученный результат на 2.
```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.andThen(g); 
// g(f(x)) или (g * f)(x)
int result = h.apple(1); //результат равен 4 = (1 + 1) * 2
```

## Compose
Compose делает тоже самое, но в другом порядке.
```java
Function<Integer, Integer> f = x -> x + 1;
Function<Integer, Integer> g = x -> x * 2;
Function<Integer, Integer> h = f.compose(g); 
// f(g(x)) или (f * g)(x)
int result = h.apple(1); //результат равен 3 = 1 * 2 + 1
```

![[разница andThen и compose.png]]
__
### Zero-Links
- [[Композиция]] 


