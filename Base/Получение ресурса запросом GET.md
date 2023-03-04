2023-01-3020:21
Tags: #Java #SpringFramework #SpringBoot #RESTful 

__
# Получение ресурса запросом GET

Например, нужно получить ингредиет из Taco Cloud API. Сделать это можно с помощью метода *getForObject()* класса RestTemplate. Следующий код получает объект Ingredient по его ID:
```java
public Ingredient getIngredientById(String ingredientId) {
	return rest.getForObject(
		"http://localhost:8080/ingredients/{id}",
		Ingredient.class,
		ingredientId
	);
}
```
Здесь мы используем версию *getForObject*, которая принимает строку с URL и список аргументов для подстановки в параметры URL. 
- Аргумент *ingredientId*, переденный в *getForObject*, подставляется на место **{id}** в URL. В примере один аргумент, но вообще подстановка производится в том порядке, в котором они заданы в вызову метода.
- Второй параметр - типа объекта, в котором должен быть возвращен ответ. В данном случае ответ (скорее всего, в формате JSON) должен быть десериализован в объект Ingredient.

Для передачи параметров URL можно использовать ассоциативный массив, как показано ниже:
```java
publoc Ingredient getIngredientById(String ingredientId)  {
	Map<String, String> urlVariables = new HashMap<>();
	urlVariables.put("id", ingredientId);
	return rest.getForObject(
		"http://localhost:8080/ingredients/{id}",
		Ingredient.class,
		urlVariables
	);
}
```
В этом примере аргумент *ingredientId* сапоставляется с ключом *id*. Перед выполнением запроса на место **{id}** будет подставлено значение из ассоциативного массива, соответствующее ключу *id*.

Использовать параметр URI немного сложнее, так как для этого нужно создавать объект URI перед вызовом *getForObject*. В остальном выполнение мало отличается:
```java
publoc Ingredient getIngredientById(String ingredientId)  {
	Map<String, String> urlVariables = new HashMap<>();
	urlVariables.put("id", ingredientId);
	URI url = UriComponentBuilder
		.fromHttpUrl("http://localhost:8080/ingredients/{id}")
		.build(urlVariables);
	return rest.getForObject(url, Ingredient.class);
}
```
Здесь URI создается на основе строкового значения, заполнители в котором замещаются значениями из ассоциативного массива Map.

Метод **getForObject** довольно мощный инструмент получения ресурсов. Но **если клиенту недостаточно тела ответа,** то можно прибегнуть к **getForEntity**.

Он действует почти также, как *getForObject*, но вместо объекта данных предметной области, содержащео информацию из тела ответа, **возвращает объект ResponseEntity** - обертку, включающую объект данных предметной области. Объект ResponseEntity содержит доп. сведения об ответе, например заголовки ответа.

Например, кроме фактических данных об ингредиентах нам нужно проверить заголовок Date ответа:
```java
public Ingredient getIngredientById(String ingredientId) {
	ResponseEntity<Ingredient> responseEntity =
		rest.getForEntity("http://localhost:8080/ingredients/{id}",
			Ingredient.class,
			ingredientId
	);
	log.info("Fetched time: {}", responseEntity.getHeaders().getDate());
	return responseEntity.getBody();
}
```
Метод *responseEntity* имеет такие же перегруженные версии, что и *getForObject*.

__
### Zero-Links
- [[Использование служб REST]]

__
### Links
- 

