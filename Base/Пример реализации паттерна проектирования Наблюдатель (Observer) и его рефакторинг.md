2022-10-2522:52
Tags: #Java #JavaCoreAdvanced #DesignPatterns 

__
# Пример реализации паттерна проектирования Наблюдатель (Observer) и его рефакторинг

Реализуем систему оповещения приложений вроде Twitter. Принцип её работы прост: несколько новостных агенств подписаны на ленту новостных твитов и хотели бы получать оповещения, если твит содержит заданное ключевое слово.

Во-первых, нужен интерфейс Observer для группировки наблюдателей. Он содержит один метод notify, который субъект (Feed) будет вызывать при поступлении нового твита
```java
interface Observer {
	void notify(String tweet);
}
```
Теперь можно объявить различные наблюдатели (три газеты в нашем случае) с различными действиями для каждого ключевого слова в твите
```java
class NYTimes implements Observer {
	public void notify(String tweer) {
		if(tweet != null && tweet.contains("money")) {
			System.out.println("Breaking news in NY! " + tweet);
		}
	}
}
```
```java
class Guardian implements Observer {
	public void notify(String tweer) {
		if(tweet != null && tweet.contains("queen")) {
			System.out.println("Yet more news from London... " + tweet);
		}
	}
}
```
```java
class LeMonde implements Observer {
	public void notify(String tweer) {
		if(tweet != null && tweet.contains("wine")) {
			System.out.println("Today cheese, wine and news! " + tweet);
		}
	}
}
```
Всё еще не хватает важнейше части - субъекта. Опишем интерфейс для субъекта следующим образом
```java 
interface Subject {
	void registerObserver(Observer o);
	void notifyObserver(String tweet);
}
```
Субъет регистрирует новые наблюдатели с помощью метода registerObserver и оповещает их о появлении нового типа с помощью метода notifyObserver. 

Реализуем новый класс Feed
```java
class Feed implements Subject {
	private final List<Observer> observers = new ArrayList<>();
	public void registerObserver(Observer o) {
		this.observers.add(o);
	}
	public void notifyObserver(String tweet) {
		observers.forEach(o -> o.notify(tweet));
	}
}
```
Реализация проста: внутри объекта Feed содержится список наблюдателей для оповещения при появлении  нового твита. 

Пример взаимодействия с наблюдателями
```java
Feed f = new Feed();

f.registerObserver(new NYTimes());
f.registerObserver(new Guardian());
f.registerObserver(new LeMonde());

f.notifyObserver("The queen said her favorite book is Modern Java in Action!");
```
Данный твит заинтересовал The Guardian

## Использование лямбда-выражений
Как же использовать лямбда-выражения в данном паттерне? Обратить внимание стоит на то, что все классы, реализующие интерфейс Observer, включают реализацию одного-единственного метода - notify. Фактически это адапатеры для элемента поведения, выполняемого при поступлении твита. Лямбда-выражения как раз и предназначены для устранения подобного стереотипного кода. Вместо явного создания экземпляров трех объектов-наблюдателей можно передать непосредственно лямбда-выражения, соответсвующие выполняемому поведению

```java
f.registerObservers( (String tweet) -> {
		if(tweet != null && tweet.contains("money")) {
			System.out.println("Breaking news in NY! " + tweet);
		}
	});

f.registerObservers( (String tweet) -> {
		if(tweet != null && tweet.contains("queen")) {
			System.out.println("Yet more news from London... " + tweet);
		}
	});
```
Всегда ли стоит использовать лямда-выражения? Нет. В данном примере лямда-выражения прекрасно подходят, поскольку выполняемое поведение - очень простое, так что с их помощью можно устранить стереотипный код. Но наблюдатели бывают гораздно сложнее, они могут сохранять состояние, включапть описание нескольких методов и тд. В подобных случаях лучше использовать классы.
__
### Zero-Links
- [[Паттерн проектирования Наблюдатель (Observer)]]

__
### Links
- 

