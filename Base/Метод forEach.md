2022-10-2114:48
Tags: #Java #JavaCoreAdvanced #CollectionAPI 

__
# Метод forEach
Обход в цикле ключей и значений ассоциативного массива всегда был достаточно неуклюжим. Нужно было организовать цикл с помощью итератора Map.Entry(K, V), проходящего по результату, возвращаемому методом entrySet()
```java
for(Map.Entry<String, Integer> entry : ageOfFriends.entrySet()) {
	String friend = entry.getKet();
	Integer age = entry.getValue();
	System.out.println(friend + "is" + age + "yo");
}
```
Начиная с Java 8 интерфейс Map поддерживаем метод forEach, принимающий в качестве параметра операцию типа BiConsumer с ключом и значением в качестве аргументов. -> Код становится более лаконичным.
```java
ageOfFriends.forEach((friend, age) -> System.out.println(friend + "is" + age + "yo"));
```

__
### Zero-Links
- [[Работа с ассоциативными массивами]] 

__
### Links
- 

