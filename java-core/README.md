---
description: Срок на изучение - 4 рабочих дня
---

# Java Core

<details>

<summary>Вопросы</summary>

1. <mark style="background-color:green;">Модификаторы доступов.</mark>
2. <mark style="background-color:green;">Ключевое слово</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`static`</mark><mark style="background-color:green;">.</mark>
3. <mark style="background-color:green;">Что такое ООП? Четыре основных принципа (инкапсуляция, наследование, полиморфизм, абстракция) с примерами.</mark>
4. <mark style="background-color:green;">Перегрузка и переопределение: в чем разница, правила, связывание (compile-time vs runtime).</mark>
5. <mark style="background-color:green;">Интерфейсы, методы интерфейсов (</mark><mark style="background-color:green;">`default`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`static`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`private`</mark><mark style="background-color:green;">).</mark>
6. <mark style="background-color:green;">Сколько интерфейсов может реализовывать класс? Может ли интерфейс наследовать другой интерфейс?</mark>
7. <mark style="background-color:green;">Поля в интерфейсе:</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`public static final`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">по умолчанию. Можно ли переопределить? Как вызвать статический метод?</mark>
8. <mark style="background-color:green;">Абстрактный класс: может ли иметь конструктор? Может ли быть</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`final`</mark><mark style="background-color:green;">? Может ли содержать</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`final`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">методы?</mark>
9. <mark style="background-color:green;">Когда использовать интерфейс, а когда абстрактный класс?</mark>
10. <mark style="background-color:green;">Enum: особенности, когда использовать,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`values()`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`valueOf()`</mark><mark style="background-color:green;">, перегрузка методов, использование в</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`switch`</mark><mark style="background-color:green;">.</mark>
11. <mark style="background-color:green;">`String`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`StringBuilder`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">и</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`StringBuffer`</mark><mark style="background-color:green;">: особенности, производительность, потокобезопасность.</mark>
12. <mark style="background-color:green;">Пул строк и строковые литералы:</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`==`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">и</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`.equals()`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`String.intern()`</mark><mark style="background-color:green;">.</mark>
13. <mark style="background-color:green;">Иерархия</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`Throwable`</mark><mark style="background-color:green;">:</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`Error`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">и</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`Exception`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`RuntimeException`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">и checked-исключения.</mark>
14. <mark style="background-color:green;">`try`</mark><mark style="background-color:green;">/</mark><mark style="background-color:green;">`catch`</mark><mark style="background-color:green;">/</mark><mark style="background-color:green;">`finally`</mark><mark style="background-color:green;">: что выполняется, если в</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`try`</mark> <mark style="background-color:green;">`return`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`throw`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`System.exit()`</mark><mark style="background-color:green;">?</mark>
15. <mark style="background-color:green;">try-with-resources: требования к ресурсам (</mark><mark style="background-color:green;">`AutoCloseable`</mark><mark style="background-color:green;">), порядок закрытия.</mark>
16. <mark style="background-color:green;">Обработка нескольких исключений в одном</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`catch`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">(multi-catch), порядок catch-блоков (от конкретного к общему).</mark>
17. <mark style="background-color:green;">`throws`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">и</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`throw`</mark><mark style="background-color:green;">. Что происходит, если unchecked exception не перехвачен?</mark>
18. <mark style="background-color:green;">Создание кастомного исключения: наследование от</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`Exception`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">(checked) или</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`RuntimeException`</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">(unchecked).</mark>
19. <mark style="background-color:green;">Что такое</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`Maven`</mark><mark style="background-color:green;">? Зачем он нужен?</mark>
20. <mark style="background-color:green;">Жизненный цикл Maven (</mark><mark style="background-color:green;">`clean`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`default`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`site`</mark><mark style="background-color:green;">). Фазы (</mark><mark style="background-color:green;">`compile`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`test`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`package`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`install`</mark><mark style="background-color:green;">). Теги (</mark><mark style="background-color:green;">`groupId`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`artifactId`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`version`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`name`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`description`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`properties`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`dependencies`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`build`</mark><mark style="background-color:green;">,</mark> <mark style="background-color:green;"></mark><mark style="background-color:green;">`profiles`</mark><mark style="background-color:green;">).</mark>
21. <mark style="background-color:green;">Что такое SOLID? Расшифровка и объяснение каждого принципа.</mark>
22. Что такое паттерн проектирования? Классификация (порождающие, структурные, поведенческие).
23. Паттерн Builder: проблема (множество параметров, особенно опциональных), fluent interface, пример.
24. Паттерн Фабрика (Simple Factory / Factory Method): проблема (зависимость от конкретных реализаций), пример.
25. Паттерн Singleton: проблема (управление единственным экземпляром), реализации: `private static final` + `getInstance()` (основной), `enum` (хитрый способ).
26. `System.out`, `System.in`, `System.err`: отличия (`out` и `err` — разные потоки, `err` не буферизован). `System.out.printf()` vs `String.format()` vs `DecimalFormat`.
27. Логирование: уровни (`TRACE`, `DEBUG`, `INFO`, `WARN`, `ERROR`), slf4j vs log4j2 vs `java.util.logging`, параметризованные логи (`logger.info("User {} logged in", user)`).

</details>

<details>

<summary>Задание</summary>

_На данном этапе реализуйте классы с методами, которые вызываются последовательно в main методе. Позже мы перенесем этот функционал в Spring сервисы и подключим к REST API._

> Вы начинаете работу над новой банковской системой. Первым этапом является проектирование объектной модели для финансовых продуктов и заявок. Это основа всей системы.

#### **Задача**

Реализовать прототип системы для создания финансовых продуктов (кредиты, вклады) и обработки заявок на них.

#### Требования

**Иерархия финансовых продуктов**&#x20;

* Создайте интерфейс для финансовых продуктов с общими методами: получение названия, описания, проверка активности продукта.
* Создайте абстрактный класс, реализующий этот интерфейс, с общими полями для всех продуктов (`id`, название, описание, флаг активности, минимальная и максимальная сумма, валюта).
* Создайте два конкретных класса-наследника:
  * Класс для кредитных продуктов с уникальными атрибутами: тип кредита (например, через `enum`), наличие залога, график погашения.
  * Класс для депозитных продуктов с уникальными атрибутами: возможность пополнения, условия досрочного закрытия.

**Фабрика продуктов**

Создайте фабрику с методом, который на основе типа продукта (`"CREDIT"`, `"DEPOSIT"`) и параметров создает соответствующие экземпляры кредитных или депозитных продуктов.

**Создание заявок на продукты**

* Создайте класс заявки с множеством полей: обязательные (`id`, `id` клиента, `id` продукта, сумма) и опциональные (срок, статус, дата создания).
* Реализуйте статический вложенный класс-builder для пошагового конструирования объекта заявки.
* Реализуйте метод `build()`, который проверяет обязательные поля и выбрасывает исключение, если какое-то поле не заполнено.

**Валидация заявок и обработка ошибок**&#x20;

* Создайте базовое проверяемое исключение для всех бизнес-ошибок системы.
* Создайте специализированные исключения-наследники: для случаев, когда продукт неактивен, и когда параметры заявки не соответствуют требованиям продукта.
* Создайте сервис валидации с методом проверки заявки и продукта. Он должен проверять:
  * Что продукт активен. Если нет — бросать соответствующее исключение.
  * Что сумма заявки находится в допустимом диапазоне для продукта. Если нет — бросать другое исключение.

**Логирование операций**

Внедрите логгер в ключевые сервисы.

Логируйте на уровне `INFO` успешное создание продукта и заявки.

Логируйте на уровне `ERROR` все исключения в процессе валидации.

**Сервис обработки заявок**&#x20;

Создайте класс по паттерну Singleton. Его метод обработки заявки должен:

* Найти продукт по ID
* Вызвать сервис валидации.
* Сохранить заявку и залогировать результат.

</details>

***

{% content-ref url="modifikatory-dostupa.md" %}
[modifikatory-dostupa.md](modifikatory-dostupa.md)
{% endcontent-ref %}

{% content-ref url="klyuchevoe-slovo-static.md" %}
[klyuchevoe-slovo-static.md](klyuchevoe-slovo-static.md)
{% endcontent-ref %}

{% content-ref url="oop.md" %}
[oop.md](oop.md)
{% endcontent-ref %}

{% content-ref url="peregruzka-i-pereopredelenie-metodov.md" %}
[peregruzka-i-pereopredelenie-metodov.md](peregruzka-i-pereopredelenie-metodov.md)
{% endcontent-ref %}

{% content-ref url="interfeisy-i-abstraktnye-klassy.md" %}
[interfeisy-i-abstraktnye-klassy.md](interfeisy-i-abstraktnye-klassy.md)
{% endcontent-ref %}

{% content-ref url="enum.md" %}
[enum.md](enum.md)
{% endcontent-ref %}

{% content-ref url="string.md" %}
[string.md](string.md)
{% endcontent-ref %}

{% content-ref url="isklyucheniya.md" %}
[isklyucheniya.md](isklyucheniya.md)
{% endcontent-ref %}

{% content-ref url="maven.md" %}
[maven.md](maven.md)
{% endcontent-ref %}

{% content-ref url="solid.md" %}
[solid.md](solid.md)
{% endcontent-ref %}
