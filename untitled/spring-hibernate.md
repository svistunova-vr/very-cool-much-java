# Spring, Hibernate

<details>

<summary>Вопросы</summary>

1. Что такое клиент-серверная архитектура? Преимущества и недостатки.
2. Что такое HTTP? Основные методы. Классы HTTP-статусов (`1xx`–`5xx`), основные коды. Отличия HTTP от HTTPS.
3. Comet, polling, long polling, WebSocket, Server-Sent Events. Отличия WS от WSS.
4. Что такое REST и RESTful API? Требования и преимущества REST-архитектуры. Примеры CRUD-операций в REST.&#x20;
5. Что такое Spring? Принцип IoC и Dependency Injection (DI).
6. Что такое Spring Bean? Способы создания бинов.
7. Способы внедрения зависимостей (конструктор, сеттер, поле). Что такое циклические зависимости и как их исправить? Что делать, если для внедрения подходят несколько бинов? `@Primary`, `@Qualifier`
8. Что такое `ApplicationContext` в Spring? Его основные функции. Чем `ApplicationContext` отличается от `BeanFactory`? Что такое `FactoryBean`? Какие существуют реализации `ApplicationContext` (`ClassPathXmlApplicationContext`, `AnnotationConfigApplicationContext` и др.)?
9. Как происходит инициализация контекста? Для чего используется `BeanFactoryPostProcessor`? Иерархия контекстов.
10. Жизненный цикл бина. `@PostConstruct`, `@PreDestroy`, `InitializingBean` и `BeanPostProcessor`. В чем разница между `BeanFactoryPostProcessor` и `BeanPostProcessor`?
11. Области видимости (scopes) бинов (`singleton`, `prototype`, `request`, `session` и др.). Что произойдёт при внедрении `prototype`-бина в `singleton`?
12. Использование reflection в Spring. Возможности и ограничения.
13. Что такое AOP (Aspect-Oriented Programming)? Какие проблемы решает? Основные понятия AOP: Aspect, Advice, Pointcut, Join Point, Target Object.&#x20;
14. Реализация AOP в Spring: Proxy (JDK Dynamic Proxy vs CGLIB). Ограничения.&#x20;
15. AspectJ и его отличия от Spring AOP (weaving, возможности).&#x20;
16. JSON сериализация: Jackson, кастомные сериализаторы, аннотации (`@JsonIgnore`, `@JsonProperty`).&#x20;
17. Разница между `@Controller` и `@RestController`.
18. Аннотации `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping`.
19. Аннотации `@RequestParam`, `@PathVariable`, `@RequestBody`, `@RequestHeader`.
20. Формирование ответа: `@ResponseBody`, `ResponseEntity`, `@ResponseStatus`
21. Жизненный цикл HTTP-запроса в Spring MVC: `DispatcherServlet`, `HandlerMapping`, `HttpMessageConverter`, `ViewResolver`.&#x20;
22. `@ControllerAdvice` / `@RestControllerAdvice`, обработка исключений (`@ExceptionHandler`), `HandlerExceptionResolver`.
23. Jakarta Bean Validation (`@NotNull`, `@Size`, `@Pattern`). `@Valid`, `@Validated`, валидация тела и параметров запроса в Spring MVC.
24. Что такое SpEL? Приведите примеры использования.&#x20;
25. Spring Boot и его отличия от Spring.
26. Как работает auto configuration в Spring Boot? `AutoConfiguration.imports`, `@AutoConfiguration`, `@Conditional` и `@EnableAutoConfiguration` аннотации.
27. Что такое Spring Boot Starter? В чем разница между стартером и библиотекой? Как написать свой стартер?
28. Как задавать configuration properties? Получение через `@ConfigurationProperties` и `@Value`, валидация.
29. Профили: `@Profile`, условная конфигурация, как активировать профиль.
30. Как работает встроенный веб-сервер (Tomcat, Jetty) в Spring Boot?&#x20;
31. Actuator: назначение, ключевые эндпойнты (`health`, `metrics`, `info`), кастомизация.&#x20;
32. Асинхронное выполнение в Spring: `@Async`, `TaskExecutor`, асинхронная обработка HTTP-запросов.
33. Что такое JPA и Hibernate, JDBC, DataSource и JdbcTemplate.
34. Аннотации `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column` (`name`, `nullable`, `length`). Требования к классу сущности.&#x20;
35. Связи в JPA: `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`. `@JoinColumn`, `mappedBy`.
36. `EntityManager`, Persistence Context, жизненный цикл Entity. Как работает dirty checking?
37. Стратегии загрузки `LAZY` и `EAGER`. `CascadeType` и `orphanRemoval`.
38. `JpaRepository`: CRUD, derived queries, `@Query`, `nativeQuery`, JPQL/HQL, проекции.
39. Динамические запросы: Specification API, Criteria API, QueryDSL/jOOQ (базовое представление), сравнение инструментов.
40. `@Transactional`
    1. Как работает в Spring? `TransactionManager`, `PlatformTransactionManager`
    2. Self-invocation — почему `@Transactional` не работает при вызове из того же класса? как это обойти?
    3. `propagation`
    4. `isolation`
    5. `readOnly`
    6. Правила rollback
    7. `ApplicationEventPublisher`, `@EventListener`, `@TransactionalEventListener`
    8. Оптимистическая и пессимистическая блокировки в JPA/Hibernate: `@Version`, `LockModeType`, `@Lock`, `SELECT FOR UPDATE`
41. `flush()`
    1. Разница между `flush()` и `commit()`?
    2. Можно ли вызывать `flush()` несколько раз в одной транзакции?
    3. Если после `flush()` выбросить исключение и вызвать `rollback()`, сохранятся ли изменения в БД?&#x20;
    4. `FlushMode.AUTO`.&#x20;
    5. Когда `flush()` может вызываться автоматически?
    6. Порядок SQL-операций при `flush()`.
42. Аннотации `@PrePersist`, `@PreUpdate`, `@PostLoad` — назначение.&#x20;
43. Проблема N+1. Решения через `@BatchSize`, `JOIN FETCH`, `@EntityGraph`.
44. DTO (Data Transfer Object). Почему не рекомендуется использовать Entity в ответах API? `LazyInitializationException` — причины и решения.
45. MapStruct: Как реализовать преобразование между DTO и Entity.&#x20;
46. Auditing: `@CreatedBy`, `@CreatedDate`, `@LastModifiedBy`, `@LastModifiedDate`, автоматическое заполнение с помощью `@EnableJpaAuditing`.
47. `save()` в цикле: влияние на производительность. JDBC batching, `flush()/clear()`, bulk update/delete, `StatelessSession`.
48. Что такое Hibernate Envers?
49. Hibernate кэширование: L1, L2, query cache, стратегии инвалидации.
50. Spring Cache: `@Cacheable`, `@CacheEvict`, `@CachePut`, Redis. Инвалидация кэша. Ошибки использования аннотаций.
51. Security filter chain: что это, основные фильтры&#x20;
52. Что такое JWT?
53. `AuthenticationManager`, `ProviderManager` и `AuthenticationProvider`.
54. OAuth2 Resource Server: работа с JWT, извлечение кастомных claims, настройка ролевой модели.&#x20;
55. Method security (`@PreAuthorize`, `@PostAuthorize`, `@Secured`).&#x20;
56. CSRF protection: что это, как работает в Spring Security, когда отключать?&#x20;
57. Spring MVC vs Spring WebFlux: архитектурные отличия, когда что использовать?
58. Как передавать контекст между потоками при `@Async` и `CompletableFuture`? Почему `ThreadLocal` не всегда подходит для WebFlux?
59. Как работает Jetty/Netty в контексте реактивного стека?&#x20;
60. Gradle: основные понятия (`tasks`, `plugins`, `dependencies`), сравнение с Maven.&#x20;

</details>

<details>

<summary>Задание</summary>

<br>

</details>

***
