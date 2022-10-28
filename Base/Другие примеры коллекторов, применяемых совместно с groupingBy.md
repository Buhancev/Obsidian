2022-10-0414:05
Tags: #Java #JavaCoreAdvanced #StreamAPI 

__
# Другие примеры коллекторов, применяемых совместно с groupingBy
В общем случае коллектор, передаваемый в качестве второго аргумента фабричного метода groupingBy, затем используется для последующей свертки всех элементов потока данных, отнесенных при классификации в одну группу. 

Например, можно переиспользовать коллектор, предназначенный для суммирования калорийности блюд в меню, и получить аналогичные суммы, но уже для каждой из групп блюд
```java
Map<Dish.Type, Integer> totalCaloriesByType = 
	menu.stream().collect(groupingBy(Dish::getType,
		summingInt(Dish::getCalories)));
```

# mapping 
кроме того, часто в сочетании с groupingBy применяется коллектор, генерируемый методом mapping. 

Этот метод принимает два аргумента
- функцию преобразования элементов потока
- еще один коллектор, служащий для накопления полученных в результате преобразования объектов
Он выступает в качестве адаптера между коллектором, принимающий объекты одного типа, и коллектором, работаюзим с объектами другого типа. 

Например, мы хотим узнать, какие уровни калорийности присутсвуют в меню для каждого из типов блюд.
```java
Map<Dish.Type, Set<CaloricLevel>> caloricLevelsByType = 
	menu.stream().collect(
		groupingBy(Dish::getType,
			mapping(dish -> {
				if(dish.getCalories() <= 400) return CaloricLevel.DIET;
				else if(dish.getCalories() <= 700) return CaloricLevel.NORMAL;
				else return CaloricLevel.FAT;},
				toSet()
		)));
```
В данном случае, передаваемая в метод mapping преобразующая функция возвращает для каждого блюда его уровень калорийности. Затем полученный поток объъектов CaloricLevel передается коллектору toSet() (аналогичен коллектору toList(), но накапливает элементы потока в объекте Set, вместо List, чтобы исключить дубликаты). Затем с помощью коллектора mapping производит сбол элементов всех сгенерированных функцией группировки подпотоков, что позволяет получить конечный результат в виде следующего ассоциативного массива
```java
{OTHER=[DIET, NORMAL], MEAT=[DIET, NORMAL, FAT], FISH=[DIET, NORMAL]}
```
Cтоит отметить, что никакой уверенности в возвращаемом типе Set у нас не было. Для расширения контроля за этим воспользоватсья методом toCollection.

Например, можно потребовать, чтобы был возвращен объект типа HashSet, путем предачи ссылки на соответствующий конструктор
```java
Map<Dish.Type, Set<CaloricLevel>> caloricLevelsByType = 
	menu.stream().collect(
		groupingBy(Dish::getType,
			mapping(dish -> {
				if(dish.getCalories() <= 400) return CaloricLevel.DIET;
				else if(dish.getCalories() <= 700) return CaloricLevel.NORMAL;
				else return CaloricLevel.FAT;},
				toCollection(HashSet::new)
		)));
```
__
### Zero-Links
- [[Сбор данных потока данных в подгруппы]]

__
### Links
- 

