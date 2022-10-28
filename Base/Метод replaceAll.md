2022-10-2015:07
Tags: #Java #JavaCoreAdvanced #CollectionAPI 

__
# Метод replaceAll
С помощью данного метода интерфейса List можно заменить все элементы списка другими.
Например, с помощью StreamAPI можно решить задачу так
```java
//[a12, b14, c13]
referenceCodes.stream()
	.map(code -> Character.toUpperCase(code.charAt(0)) + code.substring(1))
	.collect(Collector.toList())
	.forEach(System.out::println);
```
Проблема в том, что в результате этой работы появится новая коллекция строк. Нам же требуется МОДИФИЦИРОВАТЬ уже существующую коллекцию.

Для этой задачи можно использовать объект ListIterator(который поддерживает предназначенный для замены элементов метод Set())
```java
for(ListIterator<String> iterator = referenceCodes.iterator();
   iterator.hasNext(); ) {
	   String code = iterator.next();
	   iterator.set(Character.toUpperCase(code.charAt(0)) + code.substring(1));
   }
```
Этот код весьма "многословен". Кроме того, в [[Метод removeIf]] уже обговаривалось, что использоване итератора с коллекциями чревато возникновением ошибок из-за смешивания итерации и модификации коллекции.

Поэтому можно просто сделать так
```java
referenceCodes.replaceAll(code -> 
						 Character.toUpperCase(code.charAt(0))
						 + code.substring(1));
```
__
### Zero-Links
- [[Работа со списками и множествами]]

__
### Links
- 

