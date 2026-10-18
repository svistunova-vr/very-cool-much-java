# Spring, Hibernate

<details>

<summary>Вопросы</summary>

1. <mark style="background-color:green;">Что такое клиент-серверная архитектура? Преимущества и недостатки.</mark>
2. <mark style="background-color:green;">Что такое HTTP? Основные методы. Классы HTTP-статусов (</mark><mark style="background-color:green;">`1xx`</mark><mark style="background-color:green;">–</mark><mark style="background-color:green;">`5xx`</mark><mark style="background-color:green;">), основные коды. Отличия HTTP от HTTPS.</mark>
3. Comet, polling, long polling, WebSocket, Server-Sent Events. Отличия WS от WSS.
4. Что такое <mark style="background-color:blue;">REST и RESTful API</mark>? Требования и преимущества REST-архитектуры. Примеры CRUD-операций в REST.
5. Что такое Spring? <mark style="background-color:blue;">Принцип IoC и Dependency Injection (DI)</mark>.
6. Что такое Spring Bean? <mark style="background-color:blue;">Способы создания бинов</mark>.
7. <mark style="background-color:blue;">Способы внедрения зависимостей</mark> (<mark style="background-color:blue;">конструктор</mark>, сеттер, поле). Что такое циклические зависимости и как их исправить? <mark style="background-color:blue;">Что делать, если для внедрения подходят несколько бинов?</mark> `@Primary`, `@Qualifier`
8. Что такое `ApplicationContext` в Spring? Его основные функции. Чем `ApplicationContext` отличается от `BeanFactory`? Что такое `FactoryBean`? Какие существуют реализации `ApplicationContext` (`ClassPathXmlApplicationContext`, `AnnotationConfigApplicationContext` и др.)?
9. <mark style="background-color:blue;">Как происходит инициализация контекста?</mark> Для чего используется `BeanFactoryPostProcessor`? Иерархия контекстов.
10. <mark style="background-color:blue;">Жизненный цикл бина.</mark> `@PostConstruct`, `@PreDestroy`, `InitializingBean` и `BeanPostProcessor`. В чем разница между `BeanFactoryPostProcessor` и `BeanPostProcessor`?
11. Области видимости (scopes) бинов (`singleton`, `prototype`, `request`, `session` и др.). <mark style="background-color:blue;">Что произойдёт при внедрении</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`prototype`</mark><mark style="background-color:blue;">-бина в</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`singleton`</mark><mark style="background-color:blue;">?</mark>
12. Использование reflection в Spring. Возможности и ограничения.
13. Что такое AOP (Aspect-Oriented Programming)? Какие проблемы решает? Основные понятия AOP: Aspect, Advice, Pointcut, Join Point, Target Object.
14. Реализация AOP в Spring: Proxy (<mark style="background-color:blue;">JDK Dynamic Proxy vs CGLIB</mark>). Ограничения.
15. AspectJ и его отличия от Spring AOP (weaving, возможности).
16. JSON сериализация: Jackson, кастомные сериализаторы, аннотации (`@JsonIgnore`, `@JsonProperty`).
17. <mark style="background-color:blue;">Разница между</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`@Controller`</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">и</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`@RestController`</mark><mark style="background-color:blue;">.</mark>
18. Аннотации `@RequestMapping`, `@GetMapping`, `@PostMapping`, `@PutMapping`, `@PatchMapping`, `@DeleteMapping`.
19. Аннотации `@RequestParam`, `@PathVariable`, `@RequestBody`, `@RequestHeader`.
20. Формирование ответа: `@ResponseBody`, `ResponseEntity`, `@ResponseStatus`
21. Жизненный цикл HTTP-запроса в Spring MVC: `DispatcherServlet`, `HandlerMapping`, `HttpMessageConverter`, `ViewResolver`.
22. `@ControllerAdvice` / `@RestControllerAdvice`, обработка исключений (`@ExceptionHandler`), `HandlerExceptionResolver`.
23. Jakarta Bean Validation (`@NotNull`, `@Size`, `@Pattern`). `@Valid`, `@Validated`, валидация тела и параметров запроса в Spring MVC.
24. Что такое SpEL? Приведите примеры использования.
25. <mark style="background-color:blue;">Spring Boot и его отличия от Spring.</mark>
26. Как работает <mark style="background-color:blue;">auto configuration в Spring Boot</mark>? `AutoConfiguration.imports`, `@AutoConfiguration`, `@Conditional` и `@EnableAutoConfiguration` аннотации.
27. Что такое Spring Boot Starter? В чем разница между стартером и библиотекой? <mark style="background-color:blue;">Как написать свой стартер?</mark>
28. Как задавать configuration properties? Получение через `@ConfigurationProperties` и `@Value`, валидация.
29. Профили: `@Profile`, условная конфигурация, как активировать профиль.
30. Как работает встроенный веб-сервер (Tomcat, Jetty) в Spring Boot?
31. Actuator: назначение, ключевые эндпойнты (`health`, `metrics`, `info`), кастомизация.
32. Асинхронное выполнение в Spring: `@Async`, `TaskExecutor`, асинхронная обработка HTTP-запросов.
33. Что такое JPA и Hibernate, JDBC, DataSource и JdbcTemplate.
34. Аннотации `@Entity`, `@Table`, `@Id`, `@GeneratedValue`, `@Column` (`name`, `nullable`, `length`). Требования к классу сущности.
35. Связи в JPA: `@OneToOne`, `@OneToMany`, `@ManyToOne`, `@ManyToMany`. `@JoinColumn`, `mappedBy`.
36. `EntityManager`, Persistence Context, жизненный цикл Entity. Как работает dirty checking?
37. Стратегии загрузки <mark style="background-color:blue;">`LAZY`</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">и</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`EAGER`</mark>. `CascadeType` и `orphanRemoval`.
38. `JpaRepository`: CRUD, derived queries, `@Query`, `nativeQuery`, JPQL/HQL, проекции.
39. Динамические запросы: Specification API, Criteria API, QueryDSL/jOOQ (базовое представление), сравнение инструментов.
40. <mark style="background-color:blue;">`@Transactional`</mark>
    1. Как работает в Spring? `TransactionManager`, `PlatformTransactionManager`
    2. <mark style="background-color:blue;">Self-invocation — почему</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`@Transactional`</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">не работает при вызове из того же класса? как это обойти?</mark>
    3. <mark style="background-color:blue;">`propagation`</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">--</mark>
    4. `isolation`
    5. `readOnly`
    6. <mark style="background-color:blue;">Правила rollback</mark>
    7. `ApplicationEventPublisher`, `@EventListener`, `@TransactionalEventListener`
    8. <mark style="background-color:blue;">Оптимистическая и пессимистическая блокировки в JPA/Hibernat</mark>e: `@Version`, `LockModeType`, `@Lock`, `SELECT FOR UPDATE`
41. `flush()`
    1. Разница между `flush()` и `commit()`?
    2. Можно ли вызывать `flush()` несколько раз в одной транзакции?
    3. Если после `flush()` выбросить исключение и вызвать `rollback()`, сохранятся ли изменения в БД?
    4. `FlushMode.AUTO`.
    5. Когда `flush()` может вызываться автоматически?
    6. Порядок SQL-операций при `flush()`.
42. Аннотации `@PrePersist`, `@PreUpdate`, `@PostLoad` — назначение.
43. <mark style="background-color:blue;">Проблема N+1. Решения через</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`@BatchSize`</mark><mark style="background-color:blue;">,</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`JOIN FETCH`</mark><mark style="background-color:blue;">,</mark> <mark style="background-color:blue;"></mark><mark style="background-color:blue;">`@EntityGraph`</mark><mark style="background-color:blue;">.</mark>
44. DTO (Data Transfer Object). Почему не рекомендуется использовать Entity в ответах API? `LazyInitializationException` — причины и решения.
45. MapStruct: Как реализовать преобразование между DTO и Entity.
46. Auditing: `@CreatedBy`, `@CreatedDate`, `@LastModifiedBy`, `@LastModifiedDate`, автоматическое заполнение с помощью `@EnableJpaAuditing`.
47. `save()` в цикле: влияние на производительность. JDBC batching, `flush()/clear()`, bulk update/delete, `StatelessSession`.
48. Что такое Hibernate Envers?
49. <mark style="background-color:blue;">Hibernate кэширование: L1, L2, query cache, стратегии инвалидации.</mark>
50. Spring Cache: `@Cacheable`, `@CacheEvict`, `@CachePut`, <mark style="background-color:blue;">Redis</mark>. Инвалидация кэша. Ошибки использования аннотаций.
51. <mark style="background-color:blue;">Security filter chain</mark>: что это, основные фильтры
52. Что такое <mark style="background-color:blue;">JWT</mark>?
53. `AuthenticationManager`, `ProviderManager` и `AuthenticationProvider`.
54. OAuth2 Resource Server: работа с JWT, извлечение кастомных claims, настройка ролевой модели.
55. Method security (`@PreAuthorize`, `@PostAuthorize`, `@Secured`).
56. CSRF protection: что это, как работает в Spring Security, когда отключать?
57. Spring MVC vs Spring <mark style="background-color:blue;">WebFlux</mark>: архитектурные отличия, когда что использовать?
58. Как передавать контекст между потоками при `@Async` и `CompletableFuture`? Почему `ThreadLocal` не всегда подходит для WebFlux?
59. Как работает Jetty/Netty в контексте реактивного стека?
60. Gradle: основные понятия (`tasks`, `plugins`, `dependencies`), сравнение с Maven.

</details>

<details>

<summary>Задание</summary>

_Разработать Spring Boot микросервис с REST API для банковской системы, интегрировав и эволюционировав код из предыдущих модулей._

**Задача:**

Разработать микросервис для управления каталогом финансовых продуктов с полным CRUD функционалом и бизнес-логикой.

При разработке задачи почти никогда не приходят в виде полного и идеально описанного ТЗ. Чаще всего процесс выглядит так:

* Бизнес формулирует идею или потребность, например, «Мы хотим, чтобы был бот, который обрабатывает запросы клиентов».
* Это описание превращается в **бизнес-требование** — оно отвечает на вопрос «что нужно», но обычно не содержит деталей реализации.
* Затем появляются **функциональные требования**, например:
  * система должна принимать запросы клиентов;
  * запросы должны сохраняться в базе данных;
  * система должна отвечать на вопросы с помощью ИИ.

Отдельно формулируются **нефункциональные требования**, например: время ответа — не более 1 секунды, и **пользовательские требования:** система должна быть доступна через Telegram, должен быть веб-интерфейс в браузере.

После этого начинается этап **системного анализа**, где проектируется API, описываются Use Cases, проектируется модель данных, принимаются архитектурные решения и т. д.

**Важно!** До разработчиков часто доходят неполные или не до конца понятные требования. Это нормально и происходит почти в каждом проекте. В таких ситуациях от разработчика ожидается, что он задаёт уточняющие вопросы, обсуждает непонятные моменты, старается сам подумать, как сделать решение лучше и надёжнее.

В этом задании важно не только написать код, но и:

* понять требования;
* увидеть, где информации не хватает;
* сформулировать вопросы;
* предложить разумные решения.

Пример с ботом приведён только для объяснения жизненного цикла задачи. В самом задании вам нужно будет применить этот подход к предложенному кейсу.

Если в процессе выполнения задания у вас возникают вопросы — это ожидаемо. С продуктовыми и архитектурными вопросами можно и нужно приходить к куратору.

**Требования:**

* Слоистая архитектура:
  * Controller Layer - HTTP API
  * Service Layer - бизнес-логика (интеграция модулей 2-3)
  * Repository Layer - доступ к данным
  * Domain Layer - объектная модель (эволюция модуля 1)
  * DTO Layer - объекты передачи данных
  * Configuration Layer – настройки
* Доменная модель — cоздайте сущности:
  * Клиент: id, ФИО, email, телефон, дата рождения, дата регистрации
  * Финансовый продукт: id, код, название, тип (enum), процентная ставка, мин/макс сумма, валюта, статус
  * Счет: id, номер счета, тип (enum), баланс, валюта, статус, дата открытия, клиент-владелец
  * Заявка: id, сумма, срок, валюта, статус (enum), даты, клиент, продукт, целевой счет
  * Настройте связи между сущностями
  * Создайте DTO для входящих и исходящих данных API
* REST API Endpoints

```
Управление продуктами

GET    /api/products   - список с пагинацией и фильтрацией
GET    /api/products/{id} - получить продукт по ID
POST   /api/products   - создать новый продукт
PUT    /api/products/{id} - обновить продукт
PATCH  /api/products/{id}/status  - изменить статус продукта
DELETE /api/products/{id}  - деактивировать продукт
GET    /api/products/search   - расширенный поиск с фильтрами
GET    /api/products/types/{type} - продукты по типу с пагинацией
```

```
Управление счетами

GET    /api/accounts/{id} - информация о счете
GET    /api/accounts/search - поиск счетов с фильтрами
GET    /api/clients/{clientId}/accounts - все счета клиента с пагинацией
POST   /api/accounts   - открыть новый счет
POST   /api/accounts/{id}/deposit  - пополнить счет
POST   /api/accounts/{id}/withdraw   - снять средства
POST   /api/accounts/transfer  - перевод между счетами
PATCH  /api/accounts/{id}/status  - изменить статус счета
GET    /api/accounts/{id}/statement  - выписка по счету
```

```
Управление заявками

POST   /api/applications   - подать заявку
GET    /api/applications/{id} - получить заявку
GET    /api/applications/search - поиск заявок с фильтрами
GET    /api/applications   - все заявки с пагинацией
PATCH  /api/applications/{id}/status  - изменить статус заявки
GET    /api/clients/{clientId}/applications - заявки клиента
GET    /api/products/{productId}/applications - заявки на продукт
```

```
Аналитика

GET    /api/analytics/products/unpopular       - невостребованные продукты
GET    /api/analytics/applications/stats       - статистика по заявкам
GET    /api/analytics/accounts/balance-stats   - статистика по балансам
GET    /api/analytics/clients/activity         - активность клиентов
GET    /api/analytics/products/rates-distribution - распределение ставок
```

* Система поиска с пагинацией:
  * **Обязательные параметры для всех списковых эндпоинтов:**
    * `page` - номер страницы (0-based, по умолчанию: 0)
    * `size` - элементов на странице (по умолчанию: 20, максимум: 100)
    * `sort` - поле сортировки (формат: `fieldName`,`direction`)
  * **Фильтры для продуктов:**
    * `type`
    * `status`
    * `minRate`, `maxRate` - диапазон ставок
    * `currency` - валюта продукта
    * `name` - поиск по названию (case-insensitive)
  * **Фильтры для счетов:**
    * `clientId` - ID владельца
    * `type`
    * `status`
    * `minBalance`, `maxBalance` - диапазон баланса
    * `currency` - валюта счета
  * **Фильтры для заявок:**
    * `clientId` - ID клиента
    * `productId` - ID продукта
    * `status`
    * `minAmount`, `maxAmount` - диапазон суммы
    * `dateFrom`, `dateTo` - период создания

#### Интеграция предыдущих модулей

**Из Модуля 1 (Java Core)**

* Преобразуйте иерархию продуктов в JPA-сущности
* Используйте паттерн Factory для создания разных типов продуктов
* Замените ручные синглтоны на Spring-бины
* Преобразуйте checked исключения в `RuntimeException` + `ControllerAdvice`

**Из Модуля 2 (Collections & Stream API)**

* Перенесите аналитические методы в сервисный слой

**Из Модуля 3 (Concurrency)**

* Сделайте операции с балансом потокобезопасными
* Реализуйте асинхронную обработку заявок через `@Async`
* Настройте кастомный пул потоков для фоновых задач
* Добавьте асинхронную загрузку курсов валют из внешних источников

#### Технические требования

**1. Маппинг:**

* Создание отдельных DTO для запросов и ответов
* Мапперы для всех сущностей
* Кастомные методы для сложных преобразований

**2. Валидация**

* Аннотации в DTO (`@NotNull`, `@Min`, `@Max`, `@Email`, `@Pattern`)
* Кастомные валидаторы для бизнес-правил
* Группы валидаций для разных сценариев

**3. Обработка ошибок**

* Единая иерархия бизнес-исключений (`RuntimeException`)
* `@ControllerAdvice` с обработкой всех типов исключений

**4. Кэширование**

* Настройте кэширование часто запрашиваемых данных через Spring Cache
* Реализуйте инвалидацию кэша при изменении данных

**5. Транзакционность**

* `@Transactional` для всех модифицирующих операций
* Настройка уровней изоляции для разных сценариев
* Оптимистичные блокировки через `@Version`
* Обработка `OptimisticLockException`

</details>

***

{% content-ref url="klient-servernaya-arkhitektura-http.md" %}
[klient-servernaya-arkhitektura-http.md](klient-servernaya-arkhitektura-http.md)
{% endcontent-ref %}
