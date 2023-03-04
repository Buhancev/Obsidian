2023-03-0221:12
Tags: #Java #SpringFramework #SpringBoot #Reactor #RESTful #WebFlux

__
# Функциональный аналог класса TacoController

```java
@Configuration
public class RouterFunctionConfig {
	@Autowired
	private TacoRepostory tacoRepo;

	@Bean
	public RouterFunction<?> routerFunction() {
		return 
			route(GET("/api/tacos")
				.and(queryParam("recent"), t -> t != null)), this::recents)
			andRoute(POST("/api/tacos"), this::postTaco		
		);
	}

	public Mono<ServerResponse> recents(ServerRequest request) {
		return ServerResponse.ok()
			.body(tacoRepo.findAll().take(12), Taco.class);
	}

	public Mono<ServerResponse> postTaco(ServerRequest request) {
		return request.bodyToMono(Taco.class)
			.flatMap(taco -> tacoRepo.save(taco))
			.flatMap(savedTaco -> {
				return ServerResponse //что за грязь?
					.created(URI.create(
						"http:/localhost:8080/api/tacos/" +
							savedTaco.getId()))
					.body(savedTaco, Taco.class);
			})
	}
}
```
Запросы POST к конечной точке /api/tacos обрабатываются методом postTaco(), который извлекает Mono из тела входящего запроса ServerRequest. 

Затем метод postTaco() применяет ряд операций flatMap() для сохранения полученного рецепта в TacoRepository и создания ServerResponse с кодом состояния HTTP 201 (CREATED) и сохраненным объектом Taco в теле ответа. 

Операции flatMap() обеспечивают заключение результата отображения на каждом шаге в поток Mono, начиная с Mono после первого flatMap() и заканчивая Mono, который возвращается из postTaco().
__
### Zero-Links
- [[Разработка реактивных API]]
- [[Определение обработчиков запросов в функциональном стиле]]

__
### Links
- 

