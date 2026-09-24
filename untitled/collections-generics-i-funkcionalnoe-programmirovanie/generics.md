# Generics

Ранее мы уже использовали `List<Order>`, `Set<Long>` и `Map<Long, String>`. В угловых скобках указываем, объекты какого типа мы собираемся хранить.

Теперь разберём, зачем Java нужен этот механизм, почему `List<ExpressOrder>` нельзя присвоить переменной `List<Order>` и как читать конструкции вроде `List<? super Order>`.

***

### Какую проблему решают generics

Представим сервис доставки. Приложению нужен список заказов, которые ожидают, пока будет назначен курьер для доставки.

```java
@AllArgsConstructor
@Getter
class Order {

    private final long id;

    @Override
    public String toString() {
        return "#" + id;
    }
}
```

Попробуем создать список без указания типа элементов:

```java
List orders = new ArrayList();

orders.add(new Order(10L));
orders.add("Срочная доставка!!!");
```

Такой код компилируется, хоть и с предупреждениями, и мы можем его запустить, хотя в список поместили совсем разные по типу объекты — `Order` и строку.

Так как тип мы явно не указали, при получении элемента компилятор использует наиболее общий тип в Java — `Object`. Чтобы работать с ним как с заказом (например, использовать методы из класса `Order`), приходится явно менять тип:

```java
Order first = (Order) orders.get(0);
System.out.println(first.getId()); // 10

Order second = (Order) orders.get(1); // ClassCastException
```

Первая операция успешна: под индексом `0` действительно находится `Order`. Вторая падает: под индексом `1` лежит `String`.

Фактически ошибка была допущена еще при добавлении строки — мы добавили строку в список, где ожидали получить только заказы. Но программа успешно запустилась и реально исключение мы получили только при выполнении строки с `(Order) orders.get(1)`.

***

Укажем тип элементов заранее:

```java
List<Order> orders = new ArrayList<>();

orders.add(new Order(10L));
orders.add("Срочная доставка"); // Ошибка компиляции
```

Теперь компилятор сразу запрещает неподходящий элемент, и не дает запустить программу, пока мы не исправим ошибку.

При чтении менять тип вручную больше не нужно:

```java
Order order = orders.get(0);
System.out.println(order.getId());
```

> Generics позволяют задавать типы данных как параметры классов, интерфейсов и методов. Компилятор использует эти параметры, чтобы проверять совместимость типов до запуска программы.

Это даёт три основных преимущества:

* ошибки вроде добавления строки в список `Order` обнаруживаются на этапе компиляции, а не позже, после запуска программы;
* при чтении не приходится постоянно писать изменения типов;
* одну реализацию можно использовать с разными типами данных — например, `List<Integer>`, `List<Order>`, `List<User>`.

***

### Что означают `T`, `E`, `K` и `V`

Если заглянуть в интерфейс `List`, увидим `List<E>`:

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Буква `E` — **параметр для типа данных**. Например, при использовании `List<Order>` вместо `E` подставляется конкретный тип `Order`.

Обозначения выбирает разработчик (можно использовать любые буквы), но есть общепринятые варианты:

<table><thead><tr><th width="145.609375">Обозначение</th><th>Обычный смысл</th><th>Пример</th></tr></thead><tbody><tr><td><code>T</code></td><td>Type — произвольный тип</td><td><code>Box&#x3C;T></code></td></tr><tr><td><code>E</code></td><td>Element — тип элемента (если делаем свою коллекцию)</td><td><code>List&#x3C;E></code></td></tr><tr><td><code>K</code></td><td>Key — тип ключа</td><td><code>Map&#x3C;K, V></code></td></tr><tr><td><code>V</code></td><td>Value — тип значения</td><td><code>Map&#x3C;K, V></code></td></tr></tbody></table>

Вместо `T` можно написать более длинное имя, это не будет ошибкой. Но короткие обозначения привычны для Java-кода и понятны другим разработчикам.

В выражении `new ArrayList<>()` пустые угловые скобки означают: **компилятор должен сам определить нужный тип из контекста**:

```java
List<Order> orders = new ArrayList<>();
```

Здесь он сам определяет, что нужен `ArrayList<Order>`, потому что слева тип указан явно.

***

## Собственный generic-класс

В сервисе доставки можно доставлять разные вещи: заказ еды из ресторана или посылку. У любой доставки есть адрес и статус, но **тип содержимого отличается**.

Если хранить содержимое в поле типа `Object`, при получении придётся приводить его к `FoodOrder` или `Parcel`. Можно создать два отдельных класса, но тогда поля адреса, статуса и методы работы с ними придётся дублировать.

Вместо этого создадим класс `Delivery<T>`, в котором `T` — тип содержимого:

```java
class Delivery<T> {

    private final T content;
    private final String address;
    private String status;

    public Delivery(T content, String address) {
        this.content = content;
        this.address = address;
        this.status = "Создана";
    }

    public T getContent() {
        return content;
    }

    public String getAddress() {
        return address;
    }

    public String getStatus() {
        return status;
    }

    public void setStatus(String status) {
        this.status = status;
    }
}
```

`T` — тип поля `content`, аргумента конструктора и результата `getContent()`. У других полей может быть другой тип — например, в нашем случае `address` и `status` имеют тип `String`.

```java
Delivery<FoodOrder> foodDelivery = new Delivery<>(foodOrder, "Москва, ул. Лесная, 7");
Delivery<Parcel> parcelDelivery = new Delivery<>(parcel, "Москва, ул. Тверская, 12");
```

Для `foodDelivery` компилятор подставляет вместо `T` тип `FoodOrder`, для `parcelDelivery` — `Parcel`. Содержимое можно получить без явного преобразования типов:

```java
FoodOrder deliveredFood = foodDelivery.getContent();
Parcel deliveredParcel = parcelDelivery.getContent();
```

Перепутать типы компилятор не позволит:

```java
Parcel wrongContent = foodDelivery.getContent(); // Ошибка компиляции
```

При этом общий код управления доставкой одинаков:

```java
foodDelivery.setStatus("Передана курьеру");
parcelDelivery.setStatus("Передана курьеру");
```

Generics позволяют описать общую логику доставки один раз, сохранив конкретный тип её содержимого.

***

## Наследование объектов и наследование списков

Другой пример — у сервиса есть разные виды заказов:

```java
class ExpressOrder extends Order {

    public ExpressOrder(long id) {
        super(id);
    }
}

class ScheduledOrder extends Order {

    public ScheduledOrder(long id) {
        super(id);
    }
}
```

`ExpressOrder` — срочный заказ, `ScheduledOrder` — заказ к определённому времени. Оба являются разновидностями `Order`. Поэтому такой код корректен:

```java
Order order = new ExpressOrder(10L);
```

**Но со списками аналогичное присваивание запрещено:**

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
List<Order> orders = expressOrders; // Ошибка компиляции
```

***

Представим, что присваивание было бы разрешено:

```java
List<ExpressOrder> expressOrders = new ArrayList<>();

// Представим, что следующая строка разрешена:
List<Order> orders = expressOrders;

orders.add(new ScheduledOrder(20L));

ExpressOrder express = expressOrders.get(0);
```

Переменные `orders` и `expressOrders` ссылались бы на **один и тот же список**.

Через `orders` можно было бы добавить любого наследника `Order`, включая `ScheduledOrder`. Но через `expressOrders` программа ожидала бы получить только `ExpressOrder`. В результате список содержал бы заказ другого вида.

**Компилятор запрещает опасное присваивание заранее, чтобы такая ситуация не возникла.**

При этом создать список общего типа и хранить в нём разные виды заказов совершенно нормально:

```java
List<Order> orders = new ArrayList<>();

orders.add(new ExpressOrder(10L));
orders.add(new ScheduledOrder(20L));
```

Разница в том, что этот список изначально обещает хранить любые `Order`, а не только `ExpressOrder`.

***

## Инвариантность

> **Вариантность** описывает, как связь между типами элементов влияет на связь между типами контейнеров.

Обычные generic-типы Java **инвариантны**. Это означает: если `ExpressOrder` является подтипом `Order`, из этого **не следует**, что `List<ExpressOrder>` является подтипом `List<Order>`.

```java
List<ExpressOrder> expressOrders = new ArrayList<>();

List<Order> orders = expressOrders;   // Нельзя

List<Object> objects = expressOrders; // Тоже нельзя
```

Запрет работает и в обратную сторону: список любых `Order` нельзя считать списком исключительно `ExpressOrder`.

Но наследование самих коллекций продолжает работать:

```java
ArrayList<ExpressOrder> arrayList = new ArrayList<>();
List<ExpressOrder> list = arrayList; // Можно
```

Здесь аргумент типа не изменился: в обоих случаях это `ExpressOrder`.

***

## Зачем нужны wildcard

Допустим, нужно вывести номера заказов:

```java
static void printOrders(List<Order> orders) {
    for (Order order : orders) {
        System.out.println(order.getId());
    }
}
```

Метод ничего не добавляет. Ему достаточно знать, что каждый элемент является заказом и имеет `getId()`.

Однако передать в него `List<ExpressOrder>` нельзя из-за инвариантности:

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
expressOrders.add(new ExpressOrder(10L));

printOrders(expressOrders); // Ошибка компиляции
```

Писать отдельные методы для каждого вида заказа неудобно. Нам нужно выразить более гибкое требование: метод должен принимать список некоторого типа заказов. Какого именно — неважно, если его элементы можно читать как `Order`.

Для этого используется **wildcard — знак `?`, обозначающий неизвестный тип**.

***

## Ковариантность: `? extends T`

Изменим параметр метода:

```java
static void printOrders(List<? extends Order> orders) {
    for (Order order : orders) {
        System.out.println(order.getId());
    }
}
```

`List<? extends Order>` — список некоторого неизвестного типа, который является `Order` или его подтипом.

Теперь подойдут:

```java
List<Order> orders = new ArrayList<>();
List<ExpressOrder> expressOrders = new ArrayList<>();
List<ScheduledOrder> scheduledOrders = new ArrayList<>();

printOrders(orders);
printOrders(expressOrders);
printOrders(scheduledOrders);
```

Такой способ использования wildcard называют **ковариантностью**: список более конкретного типа (наследника) можно использовать там, где ожидается источник элементов общего типа (родителя).

Сам `List<E>` при этом остаётся инвариантным. Гибкость задаётся в месте использования через `? extends Order`.

***

### Почему читать можно?

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
expressOrders.add(new ExpressOrder(10L));

List<? extends Order> source = expressOrders;

Order order = source.get(0);
System.out.println(order.getId()); // 10
```

Компилятор не знает точный тип элементов через переменную `source`. Но знает, что каждый элемент является `Order`. Поэтому результат `get()` можно сохранить в `Order`, и можно использовать методы `Order`.

***

### Почему добавлять нельзя?

```java
source.add(new Order(20L));          // Ошибка компиляции
source.add(new ExpressOrder(30L));   // Ошибка компиляции
source.add(new ScheduledOrder(40L)); // Ошибка компиляции
```

**По типу переменной `source` не понятно, какой конкретно перед нами список**. Это может быть список `Order` — тогда добавить `ExpressOrder` можно, а может быть список `ScheduledOrder` — тогда добавить ни `Order`, ни `ExpressOrder` нельзя.

Так как возможен любой из этих вариантов, и компилятор не знает, какой из них реально будет при выполнении программы, он на всякий случай запрещает любые добавления еще на этапе компиляции.

***

#### `extends` не делает коллекцию неизменяемой

Через `List<? extends Order>` нельзя **добавлять** произвольные элементы (кроме `null`), но можно, например, удалить элемент или очистить изменяемый список:

```java
source.remove(0);
source.clear();
```

Добавление `null` также в теории разрешено, хотя уже конкретная реализация коллекции может запрещать `null`.

***

## Контрвариантность: `? super T`

Теперь противоположная задача: метод должен добавить срочный заказ в переданный список.

```java
static void addExpressOrder(List<ExpressOrder> destination) {
    destination.add(new ExpressOrder(10L));
}
```

Но срочный заказ можно безопасно добавить не только в `List<ExpressOrder>`. Он подходит и для `List<Order>`.

Чтобы принимать все подходящие варианты, используем `super`:

```java
static void addExpressOrder(List<? super ExpressOrder> destination) {
    destination.add(new ExpressOrder(10L));
}
```

`List<? super ExpressOrder>` — список некоторого неизвестного типа, который является `ExpressOrder` или его предком.

Для нашей иерархии подходят:

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
List<Order> orders = new ArrayList<>();
List<Object> objects = new ArrayList<>();

addExpressOrder(expressOrders);
addExpressOrder(orders);
addExpressOrder(objects);
```

Такую возможность называют **контрвариантностью**: контейнер более общего типа (родителя) подходит для добавления в него объектов более конкретного типа (наследников).

***

### Почему добавлять можно?

Любой из подходящих списков умеет хранить `ExpressOrder`:

<table data-header-hidden><thead><tr><th width="231.48828125"></th><th></th></tr></thead><tbody><tr><td>Фактический список</td><td>Почему принимает <code>ExpressOrder</code></td></tr><tr><td><code>List&#x3C;ExpressOrder></code></td><td>Точный тип элемента</td></tr><tr><td><code>List&#x3C;Order></code></td><td>Срочный заказ является заказом</td></tr><tr><td><code>List&#x3C;Object></code></td><td>Срочный заказ является объектом</td></tr></tbody></table>

Можно добавлять и наследников `ExpressOrder`, если они существуют. Но добавлять других наследников `Order` нельзя:

```java
static void addExpressOrder(List<? super ExpressOrder> destination) {
    destination.add(new ExpressOrder(10L));   // Можно
    destination.add(new Order(20L));          // Нельзя
    destination.add(new ScheduledOrder(30L)); // Нельзя
}
```

Если бы в метод передали `List<ExpressOrder>`, а он бы разрешал добавление `ScheduledOrder`, получили бы несовместимые типы в одном списке.

***

### Что можно прочитать?

Через `List<? super ExpressOrder>` результат `get()` можно безопасно получить как `Object`:

```java
List<Object> objects = new ArrayList<>();
objects.add("Служебная запись");

List<? super ExpressOrder> destination = objects;
destination.add(new ExpressOrder(10L));

Object first = destination.get(0); // Здесь строка

ExpressOrder order = destination.get(0); // Ошибка компиляции
```

`super` разрешает добавлять срочные заказы, но **не обещает, что все уже находящиеся в списке элементы — срочные заказы**.

***

## `List<?>` и `List<Object>`

`List<?>` — список неизвестного типа элементов. Он удобен, когда тип вообще не важен для выполнения операции:

```java
static void printSize(List<?> values) {
    System.out.println(values.size());
}
```

Сюда можно передать `List<Order>`, `List<String>` или любой другой список — независимо от типа элементов, у любого списка есть размер.

А `List<Object>` — список, в который разрешено добавлять разные объекты:

```java
List<Object> objects = new ArrayList<>();

objects.add(new Order(10L));
objects.add("Комментарий");
```

Через `List<?>` так сделать нельзя:

```java
List<String> strings = new ArrayList<>();
List<?> unknown = strings;

unknown.add(new Order(10L)); // Ошибка компиляции
unknown.add("Комментарий");  // Тоже ошибка компиляции
```

***

| Тип ссылки              | Что можно получить через `get()` без явного преобразования типа | Какие не-`null` элементы можно добавлять   |
| ----------------------- | --------------------------------------------------------------- | ------------------------------------------ |
| `List<Order>`           | `Order`                                                         | `Order` и его наследников                  |
| `List<? extends Order>` | `Order`                                                         | Произвольный новый элемент добавить нельзя |
| `List<? super Order>`   | `Object`                                                        | `Order` и его наследников                  |
| `List<?>`               | `Object`                                                        | Произвольный новый элемент добавить нельзя |
| `List<Object>`          | `Object`                                                        | Любые объекты                              |

{% hint style="danger" icon="circle-question" %}
Скомпилируется ли код? Если да, что он выведет?

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
expressOrders.add(new ExpressOrder(10L));

List<? extends Order> view = expressOrders;

expressOrders.add(new ExpressOrder(20L));
view.remove(0);

System.out.println(expressOrders);
```
{% endhint %}

<details>

<summary>Ответ</summary>

Код скомпилируется и выведет `[#20]`.

Обе переменные — `expressOrders` и `view` — ссылаются на один и тот же список в памяти.

Через `expressOrders` можно добавить `ExpressOrder` потому что у `expressOrders` тип `List<ExpressOrder>`.

Через `view` можно удалить элемент по индексу: мы не знаем точно тип элементов в `view`, знаем только что это `Order` или кто-то из его наследников, но для удаления нам и не требуется знать точный тип, оно одинаково работает у любой коллекции.

</details>

***

## Generic-методы

Выше мы рассмотрели generic в параметрах методов, но их можно использовать и для обозначения типа, который метод возвращает. Например, хотим получить первый элемент непустого списка:

```java
static <T> T first(List<T> values) {
    return values.get(0);
}
```

* `<T>` перед возвращаемым типом объявляет, что метод использует generic с названием `T`;
* `List<T>` принимает список элементов этого типа;
* возвращаемый `T` означает, что метод вернёт элемент того же типа.

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
expressOrders.add(new ExpressOrder(10L));

ExpressOrder order = first(expressOrders);
```

Компилятор понимает, что `T = ExpressOrder`. Явно указывать `T` при вызове не требуется.

***

### Ограничение параметра типа

Если метод должен работать только с заказами, можно ограничить `T`:

```java
static <T extends Order> T firstOrder(List<T> orders) {
    T order = orders.get(0);

    System.out.println("Выбран заказ " + order.getId());

    return order;
}
```

Теперь компилятор знает, что у `T` есть методы `Order`, включая `getId()`. При этом конкретный тип результата в вызывающем коде будет зависеть от коллекции, которую передали в параметры:

```java
ExpressOrder order = firstOrder(expressOrders);
```

Важно различать:

<table><thead><tr><th width="214.3125">Запись</th><th>Смысл</th></tr></thead><tbody><tr><td><code>&#x3C;T extends Order></code></td><td>Объявить параметр типа с названием T, который можно использовать в нескольких местах - в параметрах метода, в возвращаемом значении, внутри метода</td></tr><tr><td><code>? extends Order</code></td><td>Указать неизвестный тип с границей, не давая ему имени</td></tr></tbody></table>

В таких ограничениях `extends` используется и для классов, и для интерфейсов. Например, `<T extends Comparable<T>>` требует, чтобы `T` реализовывал соответствующий `Comparable`. А вот super использовать с именованными типами нельзя (`<T super ExpressOrder>` — ошибка), только с wildcards (`<? super ExpressOrder>` — ок).

***

## PECS

Предположим, нужно скопировать срочные заказы в общий список заказов для обработки. Один список отдаёт элементы, другой принимает:

```java
static void copyOrders(
        List<? extends Order> source,
        List<? super Order> destination
) {
    for (Order order : source) {
        destination.add(order);
    }
}
```

Для `source` используем `extends`: читаем из него объекты как `Order`. Для `destination` используем `super`: добавляем в него `Order`.

```java
List<ExpressOrder> expressOrders = new ArrayList<>();
expressOrders.add(new ExpressOrder(10L));
expressOrders.add(new ExpressOrder(20L));

List<Order> processingOrders = new ArrayList<>();
processingOrders.add(new ScheduledOrder(5L));

copyOrders(expressOrders, processingOrders);

System.out.println(processingOrders);
```

Вывод:

```
[#5, #10, #20]
```

Отсюда принцип **PECS — Producer Extends, Consumer Super**:

> **Источник элементов — `extends`. Приёмник элементов — `super`.**

Слова источник и приёмник относятся к роли коллекции **в конкретном методе**:

* метод получает из неё значения — она источник;
* метод передаёт в неё значения — она приёмник.

***

Сделаем копирование универсальным. Алгоритму выше не нужны методы заказа. Он просто переносит ссылки из одного списка в другой.

Поэтому можно заменить `Order` параметром `T`:

```java
static <T> void copyAll(
        List<? extends T> source,
        List<? super T> destination
) {
    for (T element : source) {
        destination.add(element);
    }
}
```

Один и тот же `T` связывает источник и приёмник: типы должны позволять безопасно прочитать элемент из первого списка и добавить во второй.

```java
List<ExpressOrder> source = new ArrayList<>();
source.add(new ExpressOrder(10L));

List<Order> destination = new ArrayList<>();

copyAll(source, destination);

System.out.println(destination); // [#10]
```

Метод также сможет копировать строки или числа.

**Копируются ссылки, а не сами объекты.** После вызова заказ остаётся в исходном списке, а список назначения содержит ссылку на тот же заказ.

Если метод должен и читать элементы как `T`, и записывать `T` в одну коллекцию, часто нужен обычный `List<T>`. Wildcard не следует добавлять автоматически ко всем параметрам.

***

## Стирание типов — type erasure

До сих пор мы рассматривали, как generics помогают компилятору. Но как выглядят такие классы при выполнении? Создаёт ли Java отдельные версии `Box<Order>` и `Box<String>`?

На самом деле нет — Java использует **стирание типов**, то есть после проверки generic-кода параметры типов заменяются их границами.

Если границы нет, используется `Object`. Если есть ограничение, используется его стирание; при нескольких границах — первая, самая левая.

***

Абстрактный пример:

```java
class Box<T> {

    private final T value;

    public Box(T value) {
        this.value = value;
    }

    public T getValue() {
        return value;
    }
}
```

После стирания его устройство можно **упрощённо** представить так:

```java
class Box {

    private final Object value;

    public Box(Object value) {
        this.value = value;
    }

    public Object getValue() {
        return value;
    }
}
```

В месте использования:

```java
Box<Order> box = new Box<>(new Order(10L));
Order order = box.getValue();
```

компилятор сам вставляет необходимую проверку приведения. Упрощённо:

```java
Box box = new Box(new Order(10L));
Order order = (Order) box.getValue();
```

Мы не пишем приведение сами, потому что до стирания компилятор уже проверил правильность типов. Если бы класс был объявлен как `Box<T extends Order>`, поле и результат `getValue()` после стирания имели бы тип `Order`, а не `Object`.

Стирание позволило добавить generics с сохранением совместимости с предыдущими версиями Java, в которых generics не было.

***

### Как стирание влияет на код

#### Нельзя проверить содержимое списка через `instanceof List<String>`

```java
Object value = new ArrayList<String>();

if (value instanceof List<String>) { // Ошибка компиляции
    System.out.println("Список строк");
}
```

Во время выполнения нельзя таким способом проверить, что неизвестный объект является именно списком строк. Но можно проверить, что это просто какой-то список:

```java
if (value instanceof List<?>) {
    System.out.println("Это список");
}
```

Такая проверка ничего не говорит о типах его элементов.

***

#### Нельзя создать `new T()`

```java
class Box<T> {

    public T create() {
        return new T(); // Ошибка компиляции
    }
}
```

`T` не предоставляет конкретного класса для создания объекта и не гарантирует наличие нужного конструктора.

***

#### Нельзя напрямую создать массив `new T[]`

```java
T[] values = new T[10]; // Ошибка компиляции
```

Также нельзя написать:

```java
List<String>[] lists = new List<String>[10]; // Ошибка компиляции
```

***

#### Нельзя перегрузить методы только аргументом generic-типа

```java
void process(List<String> values) {
}

void process(List<Order> values) {
}
```

В одном классе такие методы объявить нельзя: после стирания оба получают параметр типа `List`. Можно дать им разные имена или изменить параметры так, чтобы методы различались и после стирания.

***

#### Примитивы нельзя использовать как аргументы типов

```java
List<int> numbers;     // Нельзя
List<Integer> numbers; // Можно
```

Для примитивов используются классы-обёртки: `Integer`, `Long`, `Double` и другие.

***

### Raw types

Запись `List` без аргумента типа называется **raw type — сырой тип**. Она оставлена для совместимости со старым кодом. Через raw type можно обойти часть проверок компилятора:

```java
List<Order> orders = new ArrayList<>();

List raw = orders;
raw.add("Не заказ"); // Компилятор выдаст unchecked-предупреждение

Order order = orders.get(0); // ClassCastException
```

Здесь `raw` и `orders` ссылаются на один список.

Через `raw` мы добавили строку. При чтении через `orders` компилятор ожидает `Order` и сам вставляет приведение, которое в этом случае завершается ошибкой.

***

## Вопросы

{% hint style="info" icon="gitbook" %}
#### **Какова цель введения generics в Java?**

Generics позволяют задать параметр для типа классов, интерфейсов и методов. Благодаря этому одну реализацию можно использовать для разных типов данных. Компилятор проверяет, что типы действительно совместимы. Например, `ArrayList<Order>` хранит заказы, а `ArrayList<String>` — строки, хотя реализация списка одна, и компилятор не даст добавить в `ArrayList<Order>` случайный `String`.
{% endhint %}

{% hint style="info" icon="gitbook" %}
#### **Какие проблемы они решают?**

Generics помогают обнаруживать неподходящие типы до запуска программы, на этапе компиляции, уменьшают необходимость явных преобразований типов и позволяют писать универсальные алгоритмы без потери безопасности. Например, строку нельзя добавить в `List<Order>`, а результат `get()` можно сразу получить как `Order`, без явного преобразования `Object` -> `Order`. Защиту можно нарушить использованием raw types и неправильных явных преобразований типов.
{% endhint %}

{% hint style="info" icon="gitbook" %}
#### **Объясните концепции вариантности: инвариантность, ковариантность (`? extends T`) и контрвариантность (`? super T`).**

* Инвариантность означает, что наследование типов элементов не переносится автоматически на generic-контейнеры: `List<ExpressOrder>` не является наследником `List<Order>`.
* `? extends T` позволяет работать с контейнером неизвестного типа `T` или его наследника как с источником: элементы можно читать (как `T`), но нельзя добавлять произвольные не-`null` значения.
* `? super T` позволяет работать с контейнером неизвестного типа `T` или его предка как с приёмником: можно добавлять `T` и его наследников, но при чтении тип будет `Object`.

Эти ограничения относятся к доступу через конкретную ссылку. Они не создают копию исходной коллекции и не делают её неизменяемой.
{% endhint %}

{% hint style="info" icon="gitbook" %}
#### **Что такое стирание типов (type erasure) и как оно влияет на работу с generics?**

Стирание типов — преобразование, при котором после проверки generic-кода компилятор заменяет параметры типов на их границы. Если же параметр не ограничен, он заменяется на `Object`. Компилятор сам добавляет необходимые преобразования типов.

Из-за стирания типов нельзя создавать `new T()`, напрямую создавать массив `new T[]`, проверять неизвестный объект через `instanceof List<String>` или перегружать методы так, чтобы после стирания их сигнатуры совпадали.
{% endhint %}

{% hint style="info" icon="gitbook" %}
#### **Объясните принцип PECS.**

PECS означает Producer Extends, Consumer Super. Если метод получает из коллекции элементы типа `T`, подходит `? extends T`. Если метод добавляет в коллекцию элементы типа `T`, подходит `? super T`.

Например, в методе копирования источник имеет тип `List<? extends T>`, а список, в который копируем — `List<? super T>`. Если одну и ту же коллекцию нужно использовать и для чтения как `T`, и для записи `T`, нужен обычный `List<T>`.
{% endhint %}
