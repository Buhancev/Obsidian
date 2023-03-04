2023-01-0519:32
Tags: #Java #SpringFramework #SpringBoot #DataBase #JDBC 

__
# Чтение и запись данных с помощью JDBC

Поддержка JDBC в Spring уходит корнями в класс JdbcTemplate. Этот класс позволяет посылать SQL-запросы к реляционной БД без лишних церемоний и шаблонов, характерных для работы с JDBC.

**Рассмотрим выполнение просто запроса в Java без JdbcTemplate**

```java

@Override
public Optional<Ingredient> findById(String id) {
	Connection connection = null;
	PreparedStatement statement = null;
	ResultSet resultSet = null;
	try {
		connection = dataSource.getConnection();
		statement = connection.preparedStatement(
			"SELECT id, name, type FROM Ingredient WHERE id=?");
		statement.setString(1, id);
		resultSet = statement.executeQuery();
		Ingredient ingredient = null;
		if(resultSet.next()) {
			ingredient = new Ingredient(
				resultSet.getString("id"),
				resultSet.getString("name"),
				Ingredient.Type.valueOf(resultSet.getString("type")));
		}
		return Optional.of(ingredient);
	} catch(SQLException e) {
	//??? что-то делаем при исключении
	}
	finally {
		if(resultSet != null) {
			try {
				resultSet.close();
			} catch (SQLException e) {}
		}
		if(statement != null) {
			try {
				statement.close();
			} catch (SQLException e) {}
		}
		if(connection != null) {
			try {
				connection.close();
			} catch (SQLException e) {}
		}
	}
	return Optional.empty();
}

```

Как видно, сам запрос занимает крохотную часть кода. В основном мы открываем или закрываем соединения, создаем или выполняем запрос или ловим исключения. 

**SQLException** - контролируемое исключение, которое требует обработки в блоке catch. Но типичные проблемы, такие как сбой при открытии соединения с БД или во время выполнения запроса, невозможно решить в блоке catch, поэтому исключение часто генерируется вновь, чтобы передать его обработку функциям, находящимся выше в потоке выполнения. 

Теперь взглянем на тот код, но уже использующий JdbcTempate из фреймворка Spring
```java
Spring.private JdbcTemplate jdbcTemplate;

public Optional<Ingredient> findById(String id) {
	List<Ingredient> results = jdbcTemplate.query(
		"SELECT id, name, type FROM Ingredient WHERE id=?",
		this::mapRowToIngredient,
		id);
	return results.size() == 0 ?
		Optional.empty() :
		Optional.of(results.get(0));
}

private Ingredient mapToRowIngredient(ResultSet row, int rowNum) 
	throws SQLException {
	return new Ingredient(
		row.getString("id"),
		row.getString("name"),
		Ingredient.Type.valueOf(row.getString("type")));
}
```
Видно, что не выполняется никаких ни соединительных, ни заключительных операций. Наконце, полностью отсутсвует обратко исключений, которые на этом уровне невозможно правильно обработать. Остался только код, ореинтированный исключительно на выплнение запроса - **вызов метода JdbcTemplate.query()** и отображение результатов в объект Ingredient - **методом mapRowToIngredient()**.

__
### Zero-Links
- [[Работа с базами данных в Spring]]

__
### Links
- [[Определение репозиториев JDBC]]

