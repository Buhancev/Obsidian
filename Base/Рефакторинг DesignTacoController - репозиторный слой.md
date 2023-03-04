2023-01-0521:44
Tags: #Java #SpringFramework #SpringBoot #DataBase #JDBC 

__
# Рефакторинг DesignTacoController - репозиторный слой

```java
private final IngredientRepository ingredientRepository;  
  
@Autowired  
public DesignTacoController(IngredientRepository ingredientRepository) {  
    this.ingredientRepository = ingredientRepository;  
}  
  
@ModelAttribute  
public void addIngredientsToModel(Model model) {  
	Iterable<Ingredient> ingredients = ingredientRepository.findAll();  
	  
	Type[] types = Ingredient.Type.values();  
	for (Type type : types) {  
	  model.addAttribute(type.toString().toLowerCase(),  
	      filterByType((List<Ingredient>) ingredients, type));  
	}  
}
```
__
### Zero-Links
- [[Определение репозиториев JDBC]]
- [[Создание контроллера, который создает заказ]]

__
### Links
- 

