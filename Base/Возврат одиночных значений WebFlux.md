2023-02-2821:03
Tags: #Java #SpringFramework #SpringBoot #Reactor #RESTful #WebFlux

__
# Возврат одиночных значений WebFlux

В качестве примера рассмотрим еще один метод TacoController'a:
```java
@GetMapping("/{id}")
public Taco tacoById(@PathVariable("id") Long id) {
	Optional<Taco> optTaco = tacoRepo.findById(id);
	if(optTaco.isPresent()) {
		return optTaco.get();
	}
	return null;
}
```

Предположим что findById() можно переписать так:
```java
@GetMapping("/{id}")
public Mono<Taco> tacoById(@PathVariable("id") Long id) {
	return tacoRepo.findById(id);
}
```
Мало того что метод проще, важнее то, что возвращая Taco(Mono) мы позволяем фреймворку WebFlux обрабатывать ответ в реактивной парадигме, а значит, наш API будет лучше масштабироваться при больших нагрузках.
__
### Zero-Links
- [[Создание реактивных контроллеров]]

__
### Links
- 

