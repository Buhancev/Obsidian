2023-02-1421:20
Tags: #Java #SpringFramework #SpringBoot #SpringIntegraton

__
# Определение потоков Spring Integration DSL

Вместо объявления отдельного bean-компонента для каждого элемента потока объявим один bean-компонент, определяющий весь поток:
```java
@Configuration  
public class FileWriterIntegrationConfig {

	@Bean
	public IntegrationFlow fileWriterFlow() {
		return IntegrationFlow
			.from(MessageChannel.direct("textInChannel")) //входной канал
			.<String, String>transform(t -> t.toUpperCase()) //преобразователь
			.handle(Files //операция записи в файл
				.outboundAdapter(new File("C:/Users/bbuhha/Desktop/ch10"))
				.fileExistMode(FileExistMode.APPEND)
				.appendNewLine(true))
			.get();
	}
}
```

Класс IntegrationFlow инициирует API построителя, с помощью которого определяется поток.

Поток начинается с получения сообщений из канала *textInChannel*. Затем сообщения передаются преоразователяю в верхний регистр. После сообщения обрабатываются адаптером выходного канала, созданным на основе типа Files, определенного в модуле file в Spring Integration. Наконец, вызов **get()** создает возвращаемый поток IntegrationFlow. Проще говоря. этот метод определяет тот же поток интеграции что и прошлые примеры.

Стоит обратить внимаени, что не пришлось явно объявлять компоненты канала, несмотря на наличие ссылки на "textInChannel". Этот канал будет создан фреймворком автоматически, потому что нигде в коде он не создается ясно. но при желании можно создать компонент канала.

Канал соединяющий преобразователь с адаптеором выходного канала мы нигде не используем. Но если потребуется явно настроить его, то можно соснать на него по имени в определении потока с помощью вызова **channel()**:
```java
	@Bean
	public IntegrationFlow fileWriterFlow() {
		return IntegrationFlow
			.from(MessageChannel.direct("textInChannel")) //входной канал
			.<String, String>transform(t -> t.toUpperCase()) //преобразователь

			.channel(MessageChannel.direct("FileWriterChannel"))

			.handle(Files //операция записи в файл
				.outboundAdapter(new File("C:/Users/bbuhha/Desktop/ch10"))
				.fileExistMode(FileExistMode.APPEND)
				.appendNewLine(true))
			.get();
	}
```
__
### Zero-Links
- [[Объявление простого потока интеграции]]

__
### Links
- 

