2023-01-2716:55
Tags: #

__
# Условное создание bean-компонентов для профилей

Иногда полезно организовывать разные наборы bean-компонентов для разных профилей. Обычно при запуске приложения создаются все bean-компоненты объявляенные в конфигурации Java, независимо от выбранного профиля.

Но допустим, нужно чтобы некоторые bean-компоненты создавались, только если активирован определенный профиль. В таких ситуациях поможет аннотация **@Profile**.

Например, положим, есть bean-компонент *CommandLineRunner*, объявленный в TacoCloudApplication, который заполняет встроенную БД информацией об ингредиентах на этапе запуска приложения. Этот компонент помогает во время этапа разработки, но совершенно не нужен во время нормальной эксплуатации приложения.

Чтобы исключить загрузку данных при каждом запуске приложения в промышленном окружении, можно применить аннотацию **@Profile** к методу компонента CommandLineRunner:
```java
@Bean
@Profile("dev")
public CommandLineRunner dataLoader(IngredientRepository repo, UserRepository userRepo, PasswordEncoder encoder) {
	...
}
```

Или нам нужно, чтобы данные закружались если активен профиль **dev или qa**
```java
@Bean
@Profile("dev", "qa")
public CommandLineRunner dataLoader(IngredientRepository repo, UserRepository userRepo, PasswordEncoder encoder) {
	...
}
```

Но если нужно, чтобы bean-компонент создавался всегда, когда не активен профиль **prod**, то:
```java
@Bean
@Profile(!"prod")
public CommandLineRunner dataLoader(IngredientRepository repo, UserRepository userRepo, PasswordEncoder encoder) {
	...
}
```

Также аннотацию **@Profile** можно применить ко всему классу, отмеченному аннотацией **@Configuration**.

Например, преположим, что нам потребовалось извлечь bean-компонент CommandLineRunner в отдельный конфигурационный класс с именем DevelopmentConfig. Тогда мы могли снабить этот класс аннотацией **@Profile**
```java
@Profile({"!prod", "!qa"})
@Configuration
public class DevelopmentConfig {

	@Bean
	public CommandLineRunner dataLoader(
		IngredientRepository repo, 
		UserRepository   userRepo, 
		PasswordEncoder encoder) {
		...
	}
}
```

Здесь компонент СоmmandLineRunner (и все другие, определенные в этом классе) будет создан, только если неактивнгы профили **prod и qa**.
__
### Zero-Links
- [[Работа с конфигурацией]]
- [[Настройка с помощью профилей]]
- [[Определение свойств профиля]]
- [[Активация профилей]]

__
### Links
- 

