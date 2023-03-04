2023-01-0520:52
Tags: #Java #SpringFramework #SpringBoot #DataBase #JDBC 

__
# Определение репозиториев JDBC

Используется база данных H2.

Наш репозиторий для хранения объектов Ingredient должен поддерживать следующие операции:
- получение всех ингредиентов в виде коллекции объектов Ingredient;
- получение одного ингредиента по индентификатору;
- сохранение объекта Ingredient;

Следующий интерфейс IngredientRepository определяет эти три операции
```java
package tacos;  
  
import java.util.Optional;  
  
public interface IngredientRepository {  
    public Iterable<Ingredient> findAll();  
  
    public Optional<Ingredient> findById(String id);  
  
    public Ingredient save(Ingredient ingredient);  
}
```

Интерфейс демонстрирует для чего нужен репозиторий ингредиентов, но нам всё равно написать реализацию, которая использует JdbcTemplate для запросов к БД.

```java
package tacos;  
  
import org.springframework.jdbc.core.JdbcTemplate;  
import org.springframework.stereotype.Repository;  
  
import java.util.Optional;

@Repository  
public class JdbcIngredientRepository implements IngredientRepository{  
	
    private JdbcTemplate jdbcTemplate;  
	@Autowired
    public JdbcIngredientRepository(JdbcTemplate jdbcTemplate) {  
        this.jdbcTemplate = jdbcTemplate;  
    }  
  
    //...
}
```

Как видно, класс снабжен аннотацией **@Repository**. Добавляя эту аннотацию мы заявляем, что JdbcIngredientRepository должен автоматически обнаруживаться при сканировании компонентов и создаваться как bean-компонент в контексте приложения Spring. 

@Autowired автоматически внедряет JdbcTemplate с помощью конструктора. Конструктор сохраняет экземпляр в переменную, которая будет использоваться во всех методах.

## findAll()
```java
@Override  
public Iterable<Ingredient> findAll() {  
    return jdbcTemplate.query(  
            "SELECT id, name, type FROM Ingredient",  
            this::mapRowToIngredient);  
}
```
**query()** принимает:
- SQL запрос
- Реализацию RowMapper из фремворка Spring для отображения каждой записи из **набора результатов в объект**.
- Также метод может принимать дополнительный аргументы со значениями для параметров в запросе. (findById)

## findById(String id)
```java
@Override  
public Optional<Ingredient> findById(String id) {  
    List<Ingredient> results = jdbcTemplate.query(  
            "SELECT id, name, type FROM Ingredient WHERE id = ?",  
            this::mapRowToIngredient,  
            id);  
      
    return results.size() == 0 ?   
            Optional.empty() :  
            Optional.of(results.get(0));  
}
```

## Вспомогательный метод mapToRowIndredient
```java
private Ingredient mapRowToIngredient(ResultSet row, int rowNum)  
    throws SQLException {  
  
    return new Ingredient(  
            row.getString("id"),  
            row.getString("name"),  
            Ingredient.Type.valueOf(row.getString("type")));  
}
```
Преобразует сырой набор результирующих значений в необходимый объект. 

## save(Ingredient ingredient)
```java
@Override  
public Ingredient save(Ingredient ingredient) {  
    jdbcTemplate.update(  
      "INSERT INTO  Ingdredient (id, name, type) valus (?, ?, ?)",  
      ingredient.getId(),  
      ingredient.getName(),  
      ingredient.getType()  
    );  
    return ingredient;  
}
```
Метод update позволяет создавать и обновлять данные в БД. 

Теперь можно произвести рефакторинг контроллера DesignTacoController.
__
### Zero-Links
- [[Чтение и запись данных с помощью JDBC]]

__
### Links
- [[Рефакторинг DesignTacoController - репозиторный слой]]

