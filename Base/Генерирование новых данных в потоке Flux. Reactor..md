2023-02-2521:54
Tags: #Java #SpringFramework #SpringBoot #Reactor

__
# Генерирование новых данных в потоке Flux
Иногда нет никаких изначальных данных для передачи и нужно, чтобы Flux работал как счетчик, выдавая последовательность чисел.

Чтобы создать счетчик на основе Flux, можно использовать статический метод **range()**:
![[Pasted image 20230225215612.png]]
Демонстраницонный тест:
```java
@Test
public void createAFlux_range() {
	Flux<Integer> intervalFlux
		Flux.range(1, 5);

	StepVerifier.create(intervalFlux) 
		.expectNext(1) 
		.expectNext(2) 
		.expectNext(3) 
		.expectNext(4) 
		.expectNext(5) 
		.verifyComplete(); 
}
```


Другой метод Flux, похожий на *range()* - **interval()**. Подобно *range()* он выдает последовательноть возрастающий значений. Но **особенность** в том, что **вместо начального начального и конечного значений методу interval передается продолжительность паузы перед выдачей очередного значения**
![[Pasted image 20230225220018.png]]

Например, Flux, генерирующий новое значения каждую секунду, можно вызвать статический метод interval():
```java
@Test
public void createAFlux_interval() {
	Flux<Long> intevalFlux =
		Flux.inteval(Duration.ofSeconds(1))
			.take(5);

	StepVerifier.create(intervalFlux) 
		.expectNext(0L) 
		.expectNext(1L) 
		.expectNext(2L) 
		.expectNext(3L) 
		.expectNext(4L)
		.verifyComplete();
}
```
Cтоит обратить внимание на то, что начальное значение идет от 0. Кроме того, *interval()* не имеет параметра, определяющего максимальное значение, он может работать вечно. Поэтому здесь также использована операция **take()**, чтобы ограничить набор результатов первыми пятью.
__
### Zero-Links
- [[Использование распространенных реактивных операций]]

__
### Links
- 

