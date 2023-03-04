2023-02-1420:08
Tags: #Java #SpringFramework #SpringBoot #SpringIntegraton


__
# Определение потоков Spring Integration в коде на Java

Есть вариант отказаться от XML в пользу конфигурации на Жабе. Такой стиль на Spring Boot стал естевственным, дополняющим автоконфигурацию.

Сделаем тоже самое что и в XML, но на жабе:
```java
@Configuration  
public class FileWriterIntegrationConfig {  
  
    @Bean  
    @Transformer(inputChannel = "textInChannel",  
                outputChannel = "fileWriterChannel")  
    public GenericTransformer<String, String> upperCaseTransformer() {  
        return text -> text.toUpperCase();  
    }  
  
    @Bean  
    @ServiceActivator(inputChannel = "fileWriterChannel")  
    public FileWritingMessageHandler fileWriter() {  
        FileWritingMessageHandler handler =  
                new FileWritingMessageHandler(  
                        new File("C:/Users/bbuhha/Desktop/ch10"));  
  
        handler.setExpectReply(false);  
        handler.setFileExistsMode(FileExistsMode.APPEND);  
        handler.setAppendNewLine(true);  
  
        return handler;  
    }  
  
}
```
Мы объявили два bean-компонента:
- преобразователь
- обработчик собщений для записи в файл

Роль преобразователя играет GenericTransformer - функциональный интерфейс, его реализацию можно оформить в виде лямбда-выражения, вызывающего toUpperCase() для преобразования текста в верхний регистр. Компоннет преобразователя снабжен аннотацией **@Transform**, превращающей его в преобразователь в потоке интеграции, который получает сообщения из входного канала *textInChannel* и записывает результат выполнения преобразования в выходной канал *fileWriterChannle*

Компонент, осуществляющий запись в файлы, снабжен аннотацией **@ServiceActivator**, которая указывает, что сообщения будут извлечены из *fileWriterChannel* и передаваться службе, представленной экземпляром **FileWritingMessageHandler**, где FileWritingMessageHandler - обработчик сообщений, записывающий полезную нагрузку сообщения в файлс именем в заголовке "file_name" сообщения в указанном каталоге. Также как в примере FileWritingMessageHandler настроен на добавление новых строк в конец файла.

Одна из уникальных особенностей **FileWritingMessageHandler** - вызов **setExpectReply(false)**, сллюзайющий, что активатор службы не должен ожидать наличия канала ответа (через который значение можно вернуть компонентам, стоящим выше в потоке). Без этого вызов все бы работало, но значение было бы **true**, в журнале бы появилось сообщение об ошибке, указывающее на то, что *канал ответа не был настроен*.

Также стоит отметить, что нет необходимости явно создавать каналы **textInChannel** и **fileWriterChannel** - они будут  созданы автоматически, если компоненты с таким именем не существуют. Но если нужна более тонкая настройка каналов, то можно явно сконструировать их:
```java
@Bean  
public MessageChannel textInChannel() {  
    return new DirectChannel();  
}  
  
@Bean  
public MessageChannel fileWriterChannel() {  
    return new DirectChannel();  
}
```


__
### Zero-Links
- [[Объявление простого потока интеграции]]

__
### Links
- 

