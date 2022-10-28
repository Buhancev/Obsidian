2022-10-2522:25
Tags: #Java #JavaCoreAdvanced #DesignPatterns 

__
# Пример реализации паттерна проектирования Стратегия (Strategy) и его рефакторинг
Пусть нужно проверить корректно ли отформатирован входной текст по нескольким различным критериям(например, состоит из символов нижнего региства или цифр).

Начнем с описания интерфейса для проверки текста (представленного в виде объекта String)
```java
public interface ValidateStrategy {
	boolean execute(String s);
}
```
Далее описаваем одну или несколько реализаций этого интерфейса
```java
public class IsAllLoserCase implements ValidateStrategy {
	pulic boolean execute(String s) {
		return s.matches("[a-z]+");
	}
}
```
```java
public class IsNumeric implements ValidateStrategy {
	pulic boolean execute(String s) {
		return s.matches("\\d+");
	}
}
```

После этого можно использовать указанные стратегии проверки в своей программе
```java
public class Validator {
	private final ValidateStrategy strategy;
	public Validator(ValidateStrategy v) {
		this.strategy = v;
	}
	public boolean validate(String S) {
		return strategy.execute(s);
	}
}
```

Пример
```java
Validator numericValidator = new Validator(new IsNumeric());
boolean b1 = numericValidator.validate("aaaa"); //вернет false

Validator lowerCaseValidator= new Validator(new IsAllLoserCase());
boolean b1 = numericValidator.validate("aaaa"); //вернет true
```

## Использование лямбда-выражений
Очевидно, что ValidationStrategy - функциональный интерфейс. Кроме того, его функциональный дескриптор соответсвует Predicate(String). В результате вместо объявления новых классов для реализации различных стратегий можно передавать непосредственно более лаконичные лямбда-выражения.

```java
Validator numericValidator = 
	new Validator((String s) -> s.matches("[a-z]+"));
boolean b1 = numericValidator.validate("aaaa"); 

Validator lowerCaseValidator= 
	new Validator((String s) -> s.matches("\\d+"));
boolean b1 = numericValidator.validate("aaaa");
```
Как видно, лямбда-выражение позволяет устранить присущий этому паттерну проектирования стереотипный код. Если задуматься, становится понятно, что лямбда-выражение инкапсулирует элемент кода (стратегию) - то, для чего и создан паттерн, так что мы рекомендуем использовать вместо Strategy лямбда-выражение.

__
### Zero-Links
- [[Паттерн проектирования Стратегия (Strategy)]]

__
### Links
- 

