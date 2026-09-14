# Неизменяемые коллекции

Мы рассмотрели коллекции, которые можно безопасно изменять из нескольких потоков. Но иногда изменения после создания вообще не нужны. В таком случае проще **запретить изменение коллекции**.

Представим список доступных способов доставки:

```java
List<String> deliveryMethods = new ArrayList<>();

deliveryMethods.add("COURIER");
deliveryMethods.add("PICKUP");
```

Если метод сервиса вернёт этот список напрямую, вызывающий код получит ссылку на тот же объект:

```java
List<String> result = deliveryMethods;

result.clear();

System.out.println(deliveryMethods); // []
```

Изменение `result` очистило и `deliveryMethods`, потому что обе переменные ссылались на один список. В реальном приложении так можно случайно удалить поддерживаемые способы доставки или добавить значение, которое сервис не умеет обрабатывать.

Чтобы передать коллекцию только для чтения, её можно сделать недоступной для изменения.

> **Неизменяемая коллекция** — коллекция, в которую после создания нельзя добавлять элементы, из которой нельзя удалять элементы и в которой нельзя заменять элементы.

***

### Плюсы неизменяемых коллекций

Представим класс, хранящий способы доставки:

```java
final class DeliverySettings {

    private final List<String> deliveryMethods;

    DeliverySettings(List<String> deliveryMethods) {
        this.deliveryMethods = deliveryMethods;
    }

    public List<String> getDeliveryMethods() {
        return deliveryMethods;
    }
}
```

Поле объявлено `private`, но метод `getDeliveryMethods()` возвращает ссылку на список, а по ней внешний код может напрямую изменить внутреннее состояние объекта:

```java
settings.getDeliveryMethods().clear();
```

Неизменяемая коллекция помогает провести границу ответственности:

* `DeliverySettings` создаёт и хранит список;
* другой код может его прочитать, но не может изменить, даже получив ссылку на него.

Также возвращая неизменяемую коллекцию, метод явно показывает, что результат предназначен только для чтения.

При этом тип `List` всё равно содержит методы `add()`, `remove()` и `clear()`. Java не запрещает их вызов во время компиляции. Попытка изменения обнаружится только после запуска кода - дойдя до строки, изменяющей список, получим `UnsupportedOperationException`.

***

#### Преимущества при работе с несколькими потоками

Проблемы возникают, когда один поток изменяет коллекцию, пока другой её читает: читающий поток может увидеть неожиданное состояние или получить ошибку. Чтобы согласовать изменения, можно использовать потокобезопасные коллекции, но на синхронизацию нужны дополнительные память и время.

Если после создания коллекция больше не изменяется, несколько потоков могут одновременно читать её без какой-либо дополнительной синхронизации — между ними не возникнет конфликта.

Например, список доступных способов доставки создаётся при запуске приложения, а затем используется при обработке запросов:

```java
List<String> deliveryMethods = List.of("COURIER", "PICKUP", "EXPRESS");
```

Разные потоки могут выполнять:

```java
deliveryMethods.contains("EXPRESS");
deliveryMethods.get(0);
```

но ни один из них не сможет удалить или заменить элементы.

> Неизменяемость упрощает потокобезопасность, потому что данные, которые никто не изменяет, можно безопасно читать совместно.

***

### `final` не делает коллекцию неизменяемой

Модификатор `final` запрещает присвоить переменной ссылку на другой объект:

```java
final List<String> deliveryMethods = new ArrayList<>();
```

Так нельзя:

```java
deliveryMethods = new ArrayList<>(); // ошибка компиляции
```

Но изменять сам объект по-прежнему можно:

```java
deliveryMethods.add("COURIER");
deliveryMethods.add("PICKUP");
deliveryMethods.clear();
```

`final` означает, что **ссылка остаётся прежней**, а не то, что объект нельзя изменить.

***

## `Collections.unmodifiable...`

Класс `Collections` предоставляет методы, создающие недоступное для изменения представление существующей коллекции:

```java
Collections.unmodifiableCollection(collection);
Collections.unmodifiableList(list);
Collections.unmodifiableSet(set);
Collections.unmodifiableMap(map);
```

Есть также для более специфичных случаев, например `unmodifiableSortedSet()` и `unmodifiableNavigableMap()`.

Рассмотрим `unmodifiableList()`:

```java
List<String> original = new ArrayList<>();

original.add("COURIER");
original.add("PICKUP");

List<String> readOnly = Collections.unmodifiableList(original);
```

Читать данные можно:

```java
System.out.println(readOnly.get(0)); // COURIER
System.out.println(readOnly);        // [COURIER, PICKUP]
```

Изменить список через эту ссылку нельзя:

```java
readOnly.add("EXPRESS"); // UnsupportedOperationException
```

Такая же ошибка возникнет при вызове `remove()`, `clear()`, `set()` и других изменяющих операций.

***

`unmodifiableList()` не создает копию коллекции. Объекты `original` и `readOnly` ссылаются на одну и ту же исходную коллекцию:

```java
original.add("EXPRESS");

System.out.println(readOnly); // [COURIER, PICKUP, EXPRESS]
```

Через `readOnly` список изменить нельзя, но через `original` - все еще можно, и эти изменения автоматически увидит и `readOnly`, так как меняется общий объект в памяти. Поэтому такой объект называют неизменяемым **представлением** (**unmodifiable view**.

Следовательно, один только вызов:

```java
Collections.unmodifiableList(original);
```

не делает исходный список потокобезопасным. Если один поток изменяет `original`, пока другой обходит `readOnly`, они по-прежнему работают с одной коллекцией.

Такой вариант подходит, когда объект, который изначально отвечает за список, может продолжать изменять данные, но запрещает делать это внешнему коду.

***

### Как получить отдельную копию

Сначала можно скопировать исходный список, а затем запретить изменение копии:

```java
List<String> snapshot = Collections.unmodifiableList(
        new ArrayList<>(original)
);
```

Теперь существуют два разных списка:

* `original` можно продолжать изменять;
* `snapshot` не изменится вместе с ним.

```java
original.add("EXPRESS");

System.out.println(original); // [COURIER, PICKUP, EXPRESS]

System.out.println(snapshot); // [COURIER, PICKUP]
```

***

## `List.of()`, `Set.of()` и `Map.of()`

Начиная с Java 9 небольшую неизменяемую коллекцию можно создать сразу с нужными элементами.

### `List.of()`

```java
List<String> deliveryMethods = List.of("COURIER", "PICKUP", "EXPRESS");
```

Полученный список сохраняет порядок аргументов и разрешает повторяющиеся элементы:

```java
List<String> statuses = List.of("NEW", "PAID", "NEW");
```

Но изменить его нельзя:

```java
deliveryMethods.add("DRONE");
deliveryMethods.set(0, "DRONE");
deliveryMethods.remove("PICKUP");
// UnsupportedOperationException
```

`List.of()` также не разрешает `null`:

```java
List<String> values = List.of("COURIER", null); // NullPointerException
```

***

### `Set.of()`

`Set.of()` создаёт неизменяемое множество:

```java
Set<String> roles = Set.of("CUSTOMER", "COURIER", "MANAGER");
```

Так как это `Set`, повторяющиеся элементы не допускаются. При передаче дублей коллекция не создастся:

```java
Set<String> roles = Set.of("CUSTOMER", "CUSTOMER"); // IllegalArgumentException
```

`null` также запрещён.

***

### `Map.of()`

`Map.of()` создаёт неизменяемый набор пар ключ-значение:

```java
Map<String, Integer> deliveryTimes = Map.of(
        "COURIER", 60,
        "PICKUP", 20,
        "EXPRESS", 30
);
```

После создания нельзя добавлять, удалять и заменять записи:

```java
deliveryTimes.put("DRONE", 10); // UnsupportedOperationException
```

Метод не допускает:

* ключи и значения `null`;
* повторяющиеся ключи.

```java
Map<String, Integer> deliveryTimes = Map.of(
        "COURIER", 60,
        "COURIER", 30
); // IllegalArgumentException
```

У `Map.of()` есть перегрузки максимум для десяти пар. Если записей больше, можно использовать `Map.ofEntries()`:

```java
Map<String, Integer> deliveryTimes = Map.ofEntries(
        Map.entry("COURIER", 60),
        Map.entry("PICKUP", 20),
        Map.entry("EXPRESS", 30)
);
```

***

<table data-header-hidden><thead><tr><th width="137.53125"></th><th></th><th width="238.80859375"></th><th></th></tr></thead><tbody><tr><td>Метод</td><td>Что создаёт</td><td>Повторы</td><td><code>null</code></td></tr><tr><td><code>List.of(...)</code></td><td>Список с заданным порядком</td><td>Разрешены</td><td>Запрещён</td></tr><tr><td><code>Set.of(...)</code></td><td>Множество уникальных элементов</td><td>Приводят к <code>IllegalArgumentException</code></td><td>Запрещён</td></tr><tr><td><code>Map.of(...)</code></td><td>Набор пар ключ-значение</td><td>Повтор ключа приводит к <code>IllegalArgumentException</code></td><td>Запрещён и для ключей, и для значений</td></tr></tbody></table>

***

## `List.copyOf()`, `Set.copyOf()` и `Map.copyOf()`

Начиная с Java 10 можно создать неизменяемую коллекцию из уже существующей:

```java
List<String> original = new ArrayList<>();

original.add("COURIER");
original.add("PICKUP");

List<String> snapshot = List.copyOf(original);
```

Дальнейшие изменения исходного списка не влияют на копию:

```java
original.add("EXPRESS");

System.out.println(original);
// [COURIER, PICKUP, EXPRESS]

System.out.println(snapshot);
// [COURIER, PICKUP]
```

Сам `snapshot` изменить нельзя:

```java
snapshot.add("DRONE"); // UnsupportedOperationException
```

Аналогичные методы существуют для других типов:

```java
Set<String> setSnapshot = Set.copyOf(originalSet);
Map<String, Integer> mapSnapshot = Map.copyOf(originalMap);
```

Эти методы также не допускают `null`.

***

### Пример использования

Вернёмся к классу настроек доставки:

```java
final class DeliverySettings {

    private final List<String> deliveryMethods;

    DeliverySettings(List<String> deliveryMethods) {
        this.deliveryMethods = List.copyOf(deliveryMethods);
    }

    public List<String> getDeliveryMethods() {
        return deliveryMethods;
    }
}
```

Теперь объект настроек создаёт неизменяемую копию возможных способов доставки:

```java
List<String> methods = new ArrayList<>();

methods.add("COURIER");
methods.add("PICKUP");

DeliverySettings settings = new DeliverySettings(methods);
```

Изменение исходного списка не повлияет на уже сохраненные для заказа настройки:

```java
methods.clear();

System.out.println(settings.getDeliveryMethods()); // [COURIER, PICKUP]
```

Изменить список через getter тоже нельзя:

```java
settings.getDeliveryMethods().add("DRONE"); // UnsupportedOperationException
```

***

## Элементы внутри коллекции

`List.of()` и `List.copyOf()` запрещают менять **структуру коллекции**:

* добавлять элементы;
* удалять элементы;
* заменять одну ссылку другой.

Но они не копируют сами объекты и не делают их неизменяемыми. Например, `StringBuilder` — изменяемый объект:

```java
StringBuilder channel = new StringBuilder("EMAIL");
List<StringBuilder> channels = List.of(channel);
```

Добавить другой элемент в `channels` нельзя, но изменить существующий `StringBuilder` — можно:

```java
channel.append("_DISABLED");

System.out.println(channels); // [EMAIL_DISABLED]
```

В списке по-прежнему находится ссылка на тот же объект `StringBuilder`, но его содержимое изменилось.

Для полной неизменяемости нужно, чтобы не изменялась не только структура коллекции, но и содержащиеся в ней объекты. Например, для списка объектов `String` не будет этой проблемы, потому что строки неизменяемы.

***

## `Arrays.asList()`

Иногда путают `of()` или `copyOf()` с `Arrays.asList()`. `Arrays.asList()`не создаёт неизменяемый список:

```java
List<String> methods = Arrays.asList("COURIER", "PICKUP");
```

Его размер фиксирован, поэтому добавление и удаление не поддерживаются:

```java
methods.add("EXPRESS"); // UnsupportedOperationException
```

Но заменить существующий элемент можно:

```java
methods.set(0, "EXPRESS");

System.out.println(methods); // [EXPRESS, PICKUP]
```

**Фиксированный размер и неизменяемость — разные свойства**.

***

{% hint style="danger" icon="circle-question" %}
Что будет выведено?

```java
List<String> original = new ArrayList<>();
original.add("PUSH");

List<String> view = Collections.unmodifiableList(original);
List<String> snapshot = List.copyOf(original);

original.add("EMAIL");

System.out.println(view);
System.out.println(snapshot);
```
{% endhint %}

<details>

<summary>Ответ</summary>

```
[PUSH, EMAIL]
[PUSH]
```

`view` — представление исходного списка. Оно запрещает изменение через свою ссылку, но видит изменения `original`.

`snapshot` — отдельная неизменяемая копия содержимого `original` на момент вызова `List.copyOf()`. Последующее добавление `EMAIL` на нее не влияет.

</details>

***

{% hint style="info" icon="gitbook" %}
**В чем заключаются преимущества неизменяемых коллекций с точки зрения потокобезопасности и проектирования API?**

Несколько потоков могут совместно читать неизменяемую коллекцию без конфликта, не нужны дополнительные ресурсы для того, чтобы обеспечить потокобезопасность.

В API (например, при создании своего интерфейса, описывающего контракт) такие коллекции защищают внутреннее состояние объекта: внешний код может прочитать коллекцию, но не может добавить, удалить или заменить элементы и нарушить тем самым состояние объекта. Они делают поведение программы более предсказуемым и явно показывают, что полученные данные предназначены только для чтения.

При этом `Collections.unmodifiableList()` возвращает представление исходного списка, а не независимую копию. Если исходный список изменяется через другую ссылку, изменения будут видны, поэтому само по себе оно не обеспечивает потокобезопасность.
{% endhint %}

{% hint style="info" icon="gitbook" %}
**Какие способы создания неизменяемых коллекций предоставляет Java (методы `Collections.unmodifiable`..., `List.of()`, `Set.of()`, `Map.of()`)?**

Методы `Collections.unmodifiableList()`, `unmodifiableSet()` и `unmodifiableMap()` создают недоступное для изменения представление существующей коллекции. Через представление изменить данные нельзя, но можно через исходную ссылку. Изменения исходной коллекции будут видны и через неизменяемое представление. Независимую копию можно создать, сначала скопировав коллекцию, а затем обернув копию в unmodifiable: `Collections.unmodifiableList(new ArrayList<>(original))`.

Начиная с Java 9 появились методы `List.of()`, `Set.of()` и `Map.of()` — они создают неизменяемые коллекции из переданных элементов. Они запрещают `null`; `Set.of()` не допускает дубли элементов, а `Map.of()` — дубли ключей. До 10 включительно пар ключ-значение можно перечислять в `Map.of()`, если нужно больше — используется `Map.ofEntries()`.

Начиная с Java 10 появились методы `List.copyOf()`, `Set.copyOf()` и `Map.copyOf()` — они позволяют получить неизменяемую копию существующей коллекции.&#x20;
{% endhint %}
