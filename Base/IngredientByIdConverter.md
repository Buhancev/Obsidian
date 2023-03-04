2023-01-0418:48
Tags: #Java #SpringFramework #SpringBoot #SpringMVC 

__
# IngredientByIdConverter

Так как флажки игредиентов в представлении имеют тектовые значения, а объект Taco представляет список ингредиентов List(Ingredient), то возникает противоречие. 

Как список ["FLTO", "GRBF", "LETC"] можно происвоить списку объектов Ingredient, которые являются более сложными объектами? 

Для решения этого противоречия пригодится конвертер. **Конвертер** - любой класс, который реализует интерфейс **Converter** с методом **convert()**, получающим значение одного типа и преобразующим его в значение другого типа. 

Нам нужно будет **преобразовать String в Ingredient**.
```java
package com.bbuhha.taco_cloud.model;  
  
import org.springframework.core.convert.converter.Converter;  
import org.springframework.stereotype.Component;  
  
import java.util.HashMap;  
import java.util.Map;  
  
import static com.bbuhha.taco_cloud.model.Ingredient.Type.*;  
  
@Component  
public class IngredientByIdConverter implements Converter<String, Ingredient> {  
    private Map<String, Ingredient> ingredientMap = new HashMap<>();  
  
    public IngredientByIdConverter() {  
        ingredientMap.put("FLTO",  
                new Ingredient("FLTO", "Flour Tortilla", WRAP));  
        ingredientMap.put("COTO",  
                new Ingredient("COTO", "Corn Tortilla", WRAP));  
        ingredientMap.put("GRBF",  
                new Ingredient("GRBF", "Ground Beef", PROTEIN));  
        ingredientMap.put("CARN",  
                new Ingredient("CARN", "Carnitas", PROTEIN));  
        ingredientMap.put("TMTO",  
                new Ingredient("TMTO", "Diced Tomatoes", VEGGIES));  
        ingredientMap.put("LETC",  
                new Ingredient("LETC", "Lettuce", VEGGIES));  
        ingredientMap.put("CHED",  
                new Ingredient("CHED", "Cheddar", CHEESE));  
        ingredientMap.put("JACK",  
                new Ingredient("JACK", "Monterrey Jack", CHEESE));  
        ingredientMap.put("SLSA",  
                new Ingredient("SLSA", "Salsa", SAUCE));  
        ingredientMap.put("SRCR",  
                new Ingredient("SRCR", "Sour Cream", SAUCE));  
    }  
  
    @Override  
    public Ingredient convert(String id) {  
        return ingredientMap.get(id);  
    }  
}
```

Класс имеет аннотацию @Component, то есть он обнаруживается механизмом сканирования и создается как bean-компонент в контексте приложения Spring. 
Spring boot автоматически обнаружит этот и другие bean-component'ы реализующие интерфейс **Converter** и зарегистрирует их для использования Spring MVC, **когда потребуется преобразовать параметры запроса в свойства**
__
### Zero-Links
- [[Создание контроллера, который создает заказ]]

__
### Links
- [[Рефакторинг IngredientByIdConverter]]

