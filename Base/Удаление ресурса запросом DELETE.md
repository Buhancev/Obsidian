2023-01-3020:46
Tags: #Java #SpringFramework #SpringBoot #RESTful 

__
# Удаление ресурса запросом DELETE

Допустим, решили исключить из меня некоторые ингредиенты и теперь их нужно сделать недоступными для использования. Это можно сделать вызовом метода **delete** класса *RestTemplate*:
```java
public void deleteIngredient(String ingredientId) {
	rest.delete("http://localhost:8080/ingredients/{id}",
		ingredientId
	);
}
```
__
### Zero-Links
- [[Использование служб REST]]

__
### Links
- 

