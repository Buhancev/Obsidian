2023-01-3020:48
Tags: #Java #SpringFramework #SpringBoot #RESTful 

__
# Отправка ресурса запросом POST

Например, решили добавлю новый ингредиент. Сделать его доступным для составления рецептов. Это можно сделать с помощью HTTP-запроса POST к конечной точке *.../ingredients* и с данными об ингредиенте в теле запроса. 

Класс RestTemplate предлагает три метода отправки POST-запроса, каждый из которых имеет одинаковые перегруженные версии, отличающиеся способом передачи URL.

Добавить новый ресурс Ingredient с помощью POST можно вызовом **postForObject()**:
```java
public Ingredient createIngredient(Ingredient ingredient) {
	return rest.postForObject("http://localhost:8080/ingredients",
		ingredient,
		Ingredient.class
	);
}
```
Эта версия принимает строку с URL, объект для отправки на сервер и тип данных предметной области для возврата в теле ответа. **В четвертом** необязательном параметре можно передать ассоциативный массив Map со значениями параметров для подстановки в URL, но в данном примере мы не используем эту возможность.

Если после отправки POST-запроса нужно получить местоположение только что созданного ресурса, то запрос можно выполнить вызовом **postForLocation()**:
```java
public java.net.URI createIngredient(Ingredient ingredient) {
	return rest.postForLocation("http://localhost:8080/ingredients",
		ingredient
	);
}
```
*postForLocation* работает почти так же, как и *postForObject*, но **вместо объекта ресурса возвращает URI со ссылкой на только что созданный ресурс**. Возвращаемый экземпляр URI создается на основе заголовка Location ответа. 

Если нам понадобиться и сам объект, и его адрес, то **postForEntity()**:
```java
public Ingredient createIngredient(Ingredient ingredient) {
	ResponseEntity<Ingredient> responseEntity =
		rest.postForEntity("http://localhost:8080/ingredients",
			ingredient,
			Ingredient.class
	);

	log.info("New resource created at {}",
		responseEntity.getHeaders().getLocation()
	);

	return responseEntity.getBody();
}
```

Несмотря на различия в предназначении, методы RestTemplate очень похожи особенностями использования. Это упрощает осваивание RestTemplate и его практическое использование.
__
### Zero-Links
- [[Использование служб REST]]

__
### Links
- 

