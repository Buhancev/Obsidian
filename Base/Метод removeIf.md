2022-10-2014:47
Tags: #Java #JavaCoreAdvanced #CollectionAPI 
 
__
# Метод removeIf

Рассмотрим код для удаления транзакции, идентификатор которых начинается с цифты
```java
for(Transaction transaction : transactions) {
	if(Character.isDigit(transaction.getReferenceCode().charAt(0))) {
		transactions.remove(transaction);
	}
}
```
Данный код может привести к генерации исключения ConcurrentModificationException. Потому что "под капотом" цикла for-each используется объект итератор, так что код выполняется так
```java
for(Iteratoc<Transaction> iterator = transactions.iterator();
   iterator.hasNext(); ) {
	   Transaction transaction = iterator.next();
	   if(Character.isDigit(transaction.getReferenceCode().charAt(0))) {
			transactions.remove(transaction); 
			//проблема в том, что мы организуем цикл и модифируем коллекцию через два
			//отдельных объекта
		}
   }
```
Работа с коллекцией осуществляется через два отдельных объекта
- Объект-итератор, с помощью которого производится опрос источника (next() и hasNext())
- Сам объект-коллекция, служащий для удаления элемента посредством вызова метода remove(). В результате состояние итератора перестает быть согласованным с состоянием коллекции и наоборот. Для решения этой проблемы необходимо использовать объект-итератор непосредственно и вызывать его метод remove()
```java
for(Iterator<Transaction> iterator = transactions.iterator();
   iterator.hasNext(); ) {
	   Transaction transaction = iterator.next();
	   if(Character.isDigit(transaction.getReferenceCode().charAt(0))) {
		   iterator.remove();
		}
   }
```
Этот код становится весь длинным. К счастью, есть более короткий и безопасный метод
```java
//он принимает в качестве параметра предикат
transactions.removeIf(transaction -> 
					 Character.isDigit(getReferenceCode().charAt(0)));
```

__
### Zero-Links
- [[Работа со списками и множествами]]

__
### Links
- 

