2023-02-1419:31
Tags: #Java #SpringFramework #SpringBoot #SpringIntegraton


__
# Определение потоков Spring Integration в XML

```XML
<?xml version="1.0" encoding="UTF-8"?>
	 <beans xmlns="http://www.springframework.org/schema/beans"
		 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
		 xmlns:int="http://www.springframework.org/schema/integration"
		 xmlns:int-file="http://www.springframework.org/schema/integration/file"
		 xsi:schemaLocation="http://www.springframework.org/schema/beans
		 http://www.springframework.org/schema/beans/spring-beans.xsd
		 http://www.springframework.org/schema/integration
		 http://www.springframework.org/schema/integration/spring-integration.xsd
		 http://www.springframework.org/schema/integration/file
		 http://www.springframework.org/schema/integration/file/springintegration-file.xsd">

<!-- Объявление textInChannel -->
<int:channel id="textInChannel" />

<!-- Настройка преобразования текста -->
<int:transformer id="upperCase"
				 input-channel="textInChannel"
				 output-channel="fileWriterChannel"
				 expression="payload.toUpperCase()" /> 

 <!-- Объявление fileWriterChannel -->
<int:channel id="fileWriterChannel" />

<!-- настройка записи файла-->
<int-file:outbound-channel-adapter id="writer"
								   channel="fileWriterChannel"
								   ditectory="/tmp/sia6/files"
								   mode="APPEND"
								   append-new-line="true" /> 
```

Рассмотрим настройки:
- первым объявляется канал **textInChannel**. Этот же канал, который выбран в качестве канала запроса для FileWriterGateway. Когда вызывается метод **writeTofile()** экземпляра *FileWriterGateway*, сгенерированное сообщение публикуется в этом канале;
- затем настраивается преобразователь, который получает сообщения из textInChannel. Он использует выражение на языке Spring Expression Language (SpEL) для применения **toUpperCase()** к телу сообщения, после чего результат публикутеся в **fileWriterChannel**. Он соединяет преобразователь с адаптером канала вывода;
- далее настраивается канал **fileWriterChannel**. Он соединяет преобзователь с адаптером канала вывода;
- наконец, настривается адаптер канала вывода с использованем пространства имен **int-file**. Это пространство имен XML поддерживается модулем Spring Inregration, предназначенным для записи в файлы. Согласно приведенным настройкам, он будет получать сообщение из fileWriterChannel и записывать их полезную нагрузку в файл, имя которого указано в заголовке "file_name" сообщения, находящемся в каталоге, который определяется атрибутом **directory**. Если файл существует, новые сообщения будут добавляться в конец.

Получившийся поток интеграции изображен на рисунке ниже
![[Pasted image 20230214194700.png]]
Поток состоит из пяти компонентов: шлюза, двух каналов, преобразователя и адаптера кнала. Всё это необходимо для получения потока интеграции. 

Чтобы использовать конфигурационные файлы XML в приложении Spring Boot, необходимо ипортировать XML в качестве ресурса. Самый просто способ сделать это - использовать аннотацию **@ImportResource**, как показано на следующем примере, в одном из конфигурационных Java-классов:
```java
@Configutation
@ImportResource("classpath:/filewriter-config.xml")
public class FileWriterIntegrationConfig {...}
```
__
### Zero-Links
- [[Объявление простого потока интеграции]]

__
### Links
- 

