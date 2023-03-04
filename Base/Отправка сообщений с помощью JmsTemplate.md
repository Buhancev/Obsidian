2023-02-1200:33
Tags: #Java #SpringFramework #SpringBoot #JMS

__
# Отправка сообщений с помощью JmsTemplate

Благодаря включению зависимости JMS в спеках сборки Spring Boot автоматически настроить механизм JmsTemplate, который мы сможем внедрить и использовать для отправки и получения сообщений.

**JmsTemplate** - центральный элемент интеграции Spring с JMS. Подобно другим компонентам, ореинтированным на шаблоны, JmsTemplate избавляет от необходимости писать большой шаблонный код, открывающий соединение с брокером и создающий сеанс, а также доп. код для обработки любых исключений, которые могут возникнуть в процессе отправки сообщения.

**JmsTemplate** имеет несколько методов отправки сообщений, в том числе:

*отправка неструктурированных сообщений*
```java
void send(MessageCreator messageCreator) throws JmsException;

void send(Destination destination, MessageCreator messageCreator) throws JmsException;

void send(String destinationName, MessageCreator messageCreator) throws JmsException;
```

*Отправка сообщений в формате объектов*
```java
void convertAndSent(Object message) throws JmsException;

void convertAndSent(Destination destination, Object message) throws JmsException;

void convertAndSent(String destinationName, Object message) throws JmsException;
```

*Отправка сообщений в форме объектов с постобработкой*
```java
void convertAndSent(Object message,
				   MessagePostProcessor postProcessor) throws JmsException;
				   
void convertAndSent(Destination destination, Object message,
				   MessagePostProcessor postProcessor) throws JmsException;
				   
void convertAndSent(String destinationName, Object message,
				   MessagePostProcessor postProcessor) throws JmsException;
```

Видно, что есть всего два метода **send()** и **convertAndSend()**, каждый из которых имеет перегруженные верси, поддерживающие разный набор параметров.

Можно заметить две разные формы **convertAndSend()**, можно разделить на две подкатегории:
- три метода send() принимают MessageCreator для создания объекта Message
- три метода convertAndSend() принимают объект Object и автоматически преобразуют его в сообщение
- три метода convertAndSend() также автоматически преобразуют объект Object в сообщение, а также принимают MessagePostProcessor для доп. настройки сообщения Message перед его фактической отправкой

Кроме того, каждая из этих категорий включает три перегруженные версии метода, которые различаются способом определения места назвачения в JMS (очередь или тема), а именно:
- один метод не принимает никаких параметров и отправляет сообщение в место назначения по умолчанию
- один метод принимает объект Destination, определяющий место назначения для сообщения
- один метод принимает строку, указывающую имя места назначения для сообщения

Пример реализации службы JmsOrderMessagingService, использующей самую простую версию метода send():
```java
@Service
public class JmsOrderMessagingService implements OrderMessagingService {

	private JmsTemplate jsmTemplate;

	@Autowired
	public JmsOrderMessagingService(JmsTemplate jsmTemplate) {
		this.jsmTemplate = jsmTemplate;
	}

	@Override
	public void sendOrder(TacoOrder order) {
		jsmTemplate.send(new MessageCreator() {
			@Override
			public MessageCreator(Session sessin) throws JMSException {
				return session.createObjectMessage(order);
			}
		})
	}

}
```
Метод **sendOrder()** вызывает **jsmTemplate.send()** и передает анонимную реализацию **MessageCreator**, объявленную внутри класса. Эта реализация переопределяет метод **createMessage()**, создающий новый объект сообщения из данного объекта TacoOrder.

Так как служба JmsOrderMessagingService ореитирована на использование JMS и реализует общий интерфейс OrderMessagingService, мы можем внедрить её в OrderApiController и вызвать её метод sendOrder() при создании заказа:
```java
@RestController
@RequestMapping(path="api/", produces="application/json")
@CrossOrigin(origins="http://localhost:8080")
public class OrderApiController {

	private OrderRepository repo;
	private OrderMessagingService messageService;

	public OrderApiController(OrderRepository repo,
							  OrderMessagingService messagingService) {

		this.repo = repo;
		this.messagingSevice = messagingService;
	}

	@PostMapping(cosumes="application/json")
	@ResponseStatus(Http.CREATED)
	public TacoOrder postOrder(@RequestBody TacoOrder order) {
		messagingService.sendOrder(order);
		return repo.save(order);
	}
	...
}
```

Можем упростить MessageCreator, ведь это функциональный интерфейс, используя лямбда выражение:
```java
@Override
public void sendOrder(TacoOrder order) {
	jms.send(session -> session.createObjectMessage(order));
}
```
Стоит обратить внимание, что вызов **jms.send()** не определяет место назначения. Чтобы такой подход сработал, нужно задать имя место назначения по умолчанию с помощью свойства **spring.jms.template.default-destination**, например:
```java
spring:
	jms:
		template:
			default-destination: tacocloud.order.queue
```

Это самый простой способ, но если нужно изменить место назначение, то:

Один из способов - передать объект **Destination** в первом параметре. Самый просто способ - объявить компонент Destination, а затем внедрить его в компонент, посылающий сообщение, например:
```java
@Bean
public Destination orderQueue() {
	return ActiveMQQueue("tacocloud.order.queue");
}
```
Затем этот метод можно добавить в любой конфиг. класс в приложении, которые будет отправлять или получать сообщения через JMS. 

Внедрив компонент Destination в JmsOrderMessagingService, сможем использовать его в вызове:
```java
private Destination orderQueue;

@Autowired
public JmsOrderMessagingService(JmsTemplate jmsTemplate, Destination orderQueue) {
	this.jmsTemplate = jmsTemplate;
	this.orderQueue = orderQueue;
}

...

@Override
public void sendOrder(TacoOrder order) {
	jms.send(orderQueue,
			session -> session.createObjectMessage(order));
}
```
Указание места назначения с помощью **Destination** открывает более широкие возможности настройки. Но на практике редко бывает нужно настраивать что-то еще в месте назначения, кроме имени. 

Чаще проще указать имя в пермо параметре, как здесь:
```java
@Override
public void sendOrder(TacoOrder order) {
	jms.send("tacocloud.order.queue",
			session -> session.createObjectMessage(order));
}
```

Метод **send()** нужен для простых сообщений, иначе его сложность заметно увеличивается. Если нужно указать тольк объект, который нужно отправить (и мб место назначения), то такую возможность даёт **convertAndSend()**.

Метод **convertAndSend()** упрощает публикацию сообщений, избавляя от необходимости предоставлять свою реализацию MessageCreator. Достаточно просто передать объек, который будет автоматически преобразован перед отправкой, например:
```java
@Override
public void sendOrder(TacoOrder) {
	jms.converAndSend("tacocloud.order.queue", order);
}
```

**MessageConverter** - интерфейс, определенный в Spring, который имеет только два метода:
```java
public interface MessageConverter {
	Message toMessage(Object object, Session session) 
								throws JMSException, MessageConversionException;

	Object fromMessage(Message message);
}
```
Итерфейс прост в реализации, но писать свои реализации нужно крайне редко. Spring предлагает несоклько готовых реализаций по умолчанию. 

По умолчанию используется **SimpleMessageConverter**, но этот конвертер требует, чтобы отправляемый объект реализовал интерфейс **Serializable**. Это неплохой выбор, но иногда лучше юзать другие конвертеры, например, **MappingJackson2MessageConverter**, чтобы избежать этого ограничения.
![[Pasted image 20230212223011.png]]

Чтобы применить другой конвертер, нужно лишь объявить его компонентов. Например, используем **MappingJackson2MessageConverter** вместо **SimpleMessageConverter**:
```java
@Bean
public MappingJackson2MessageConverter messageConverter() {
	MappingJackson2MessageConverter messageConverter = 
		new MappingJackson2MessageConverter();

	messageConverter.setTypeIdPropertyName("_typeId")

	return messageConverter;
}
```
Этот метод можно поместить в любой конфиг. класс в приложении, которое будет отправлять и получать сообщеия через JMS.

Стоит обратить внимание, что здесь используется метод **setTypeIdPropertyName()**, компонента *MappingJackson2MessageConverter* перед его возвратом. Это важный шаг, позволяющий получателю узнать, в какй тип нужно преобразовать входящее сообщение.

По умолчанию это свойство содержит полное имя класса преобразуемого типа. Но данное решение не очень гибкое и требует, чтобы получатель также тот же тип с тем же полным именем класса.

Чтобы повысить гибкость, можно отобразить имя синтетического типа в фактический тип, вызвав **setTypeIdMappings()** конвертера сообщений. Следующий код отобразит ID синтетического типа TacoOrder в класс TacoOrder:
```java
MappingJackson2MessageConverter messageConverter = 
	new MappingJackson2MessageConverter();

messageConverter.setTypeIdPropertyName("_typeId")

Map<String, Class<?>> typeIdMappings = new HashMap<>();

typeIdMappings.put("order", TacoOrder.class);
messageConverter.setTypeIdMappings(typeIdMappings);

return messageConverter;
```

Вместо полного имени класса, отправляемого в свойстве **typeId** сообщения, будет отправлено TacoOrder. Приложение-получатель настроит аналогичный конвертер сообщений, отображаюзий TacoOrder в свое представление заказа. Это представление заказа может находится в другом пакете, иметь другое имя и даже иметь лишь часть свойств TacoOrder на стороне отправителя.



__
### Zero-Links
- [[Настройка JMS]]
- [[Асинхронная передача сообщений]]

__
### Links
- 

