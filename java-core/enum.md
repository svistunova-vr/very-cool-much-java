# enum

> **`enum`** (от англ. enumeration — перечисление) — специальный тип в Java для описания ограниченного фиксированного набора значений.

Например, у заказа может быть только несколько статусов:

```java
NEW        // новый
PAID       // оплачен
DELIVERING // доставляется
COMPLETED  // выполнен
CANCELLED  // отменен
```

Можно было бы хранить статус строкой:

```java
String status = "PAID";
```

Но тогда ничто не мешает случайно ошибиться и долго искать причину бага:

```java
String status = "PAYD";
```

Java не понимает, какие строки допустимы, а какие нет. Поэтому для фиксированного набора значений лучше использовать `enum`:

```java
enum OrderStatus {
    NEW,
    PAID,
    DELIVERING,
    COMPLETED,
    CANCELLED
}
```

Теперь использовать несуществующий статус нельзя:

```java
class Order {

    private OrderStatus status;

    public Order() {
        this.status = OrderStatus.NEW;
    }

    public OrderStatus getStatus() {
        return status;
    }
}
```

Заказ не может случайно получить статус `"PAYD"`, потому что он не входит в список допустимых значений типа `OrderStatus`.

***

### Когда использовать `enum`?

`enum` подходит, когда существует **заранее известный ограниченный набор вариантов**.

Например:

```java
enum OrderStatus {
    NEW,
    PAID,
    COMPLETED,
    CANCELLED
}
```

```java
enum UserRole {
    USER,
    MODERATOR,
    ADMIN
}
```

```java
enum Priority {
    LOW,
    MEDIUM,
    HIGH
}
```

`LOW`, `MEDIUM` и `HIGH` — объекты типа `Priority`:

```java
Priority priority = Priority.HIGH;
```

Статический тип переменной — `Priority`, значение — `Priority.HIGH`.

***

### Когда `enum` использовать не стоит?

Например, список городов:

```
Москва
Лондон
Париж
Тбилиси
...
```

теоретически можно записать в `enum`, но города постоянно добавляются и обычно приходят из базы данных или внешнего источника. То же самое с пользователями и товарами.

`enum` лучше использовать для значений, которые являются **частью логики программы**, а не просто данными, которые могут свободно добавляться во время её работы.

***

## Обращение к значениям

Обращаемся через имя `enum`:

```java
OrderStatus.NEW
OrderStatus.PAID
OrderStatus.COMPLETED
```

Так же, как к `static` членам класса:

```java
Math.PI
User.MAX_AGE
```

Фактически каждая константа `enum` существует в единственном экземпляре.

Значения `enum` сравнивают через `==`:

```java
if (status == OrderStatus.PAID) {
    System.out.println("Заказ оплачен");
}
```

***

## `enum` может содержать поля

У `enum` в Java больше возможностей, чем у обычных констант. Например, хотим хранить русское название каждого приоритета:

```java
enum Priority {

    LOW("Низкий"),
    MEDIUM("Средний"),
    HIGH("Высокий");

    private final String title;

    Priority(String title) {
        this.title = title;
    }

    public String getTitle() {
        return title;
    }
}
```

```java
Priority priority = Priority.HIGH;
System.out.println(priority.getTitle()); // Высокий
```

То есть каждое значение может иметь собственные данные.

***

Выражение `HIGH("Высокий")` вызывает конструктор `Priority`:

```java
Priority(String title) {
    this.title = title;
}
```

Но создать новое значение вручную во внешнем коде нельзя:

```java
Priority newPriority = new Priority("Очень высокий");
```

Набор доступных значений `enum` определяется **только внутри него самого**. Поэтому Java запрещает конструктор `enum` делать `public` — чтобы не было возможности создавать новые значения извне через `new`.

Обычно модификатор опускают:

```java
Priority(String title) {
    this.title = title;
}
```

***

## `enum` может содержать обычные методы

Например:

```java
enum OrderStatus {

    NEW,
    PAID,
    COMPLETED,
    CANCELLED;

    public boolean isFinished() {
        return this == COMPLETED || this == CANCELLED;
    }
}
```

```java
OrderStatus status = OrderStatus.COMPLETED;
System.out.println(status.isFinished()); // true
```

***

### Разные значения `enum` могут иметь разную реализацию методов

Например, есть тип доставки — обычный или экспресс:

```java
enum DeliveryType {

    EXPRESS {
        @Override
        public int calculatePrice(int distance) {
            return distance * 500;
        }
    },

    REGULAR {
        @Override
        public int calculatePrice(int distance) {
            return distance * 100;
        }
    };

    public abstract int calculatePrice(int distance);
}
```

Теперь:

```java
DeliveryType.COURIER.calculatePrice(2); // 1000
DeliveryType.PICKUP.calculatePrice(2);  // 200
```

То есть конкретные значения `enum` могут переопределять методы.

***

## `values()`

У любого `enum` автоматически есть метод `values()` — он возвращает все значения.

Например:

```java
Priority[] priorities = Priority.values(); // [ LOW, MEDIUM, HIGH ]
```

Значения в массиве идут в том же порядке, в котором они объявлены в `enum`. Можно их перебрать:

```java
for (Priority priority : Priority.values()) {
    System.out.println(priority);
}
```

Например, так можно вывести список вариантов для пользователя.

***

## `valueOf()`

`valueOf()` получает значение `enum` по его имени.

Например:

```java
OrderStatus status = OrderStatus.valueOf("PAID"); // OrderStatus.PAID
```

`valueOf()` чувствителен к регистру: `OrderStatus.valueOf("PAID")` сработает, `OrderStatus.valueOf("paid")` — нет, имя должно совпадать в точности.

```java
```

Если пытаемся запросить несуществующее значение — например, `OrderStatus.valueOf("abc11")` — будет `IllegalArgumentException`. Нужно учитывать возможность ошибки, если строку вводит пользователь

```java
try {
    OrderStatus status = OrderStatus.valueOf(value);
} catch (IllegalArgumentException e) {
    System.out.println("Неизвестный статус"); // можно через values() вывести варианты
}
```

***

## `name()`

`name()` —  возвращает название константы строкой:

```java
OrderStatus status = OrderStatus.PAID;
String name = status.name(); // "PAID"
```

***

## `toString()`

Если написать `System.out.println(OrderStatus.PAID)`, по умолчанию тоже увидим `PAID`, но `toString()` при желании можно переопределить:

```java
enum Priority {

    LOW("Низкий"),
    HIGH("Высокий");

    private final String title;

    Priority(String title) {
        this.title = title;
    }

    @Override
    public String toString() {
        return title;
    }
}
```

Теперь:

```java
System.out.println(Priority.HIGH); // Высокий
```

А:

```java
Priority.HIGH.name(); // HIGH
```

> `name()` — настоящее имя константы, его изменить его нельзя.
>
> `toString()` — отображаемое название объекта, его можно переопределить.

***

## `ordinal()`

У каждого значения есть порядковый номер:

```java
Priority.LOW.ordinal();    // 0
```

Нумерация начинается с `0` и зависит от порядка объявления:

```java
enum Priority {
    LOW,       // 0
    MEDIUM,    // 1
    HIGH       // 2
}
```

Но использовать `ordinal()` как бизнес-значение обычно **не стоит**. Например, нельзя надёжно считать:

```
LOW = 0
MEDIUM = 1
HIGH = 2
```

и сохранять эти числа в базу как смысловые значения. Если потом поменять порядок:

```java
enum Priority {
    HIGH,
    MEDIUM,
    LOW
}
```

`ordinal()` изменится. Если нужен постоянный числовой код, лучше задать его явно:

```java
enum Priority {

    LOW(10),
    MEDIUM(20),
    HIGH(30);

    private final int code;

    Priority(int code) {
        this.code = code;
    }
}
```

***

## `enum` в `switch`

`enum` очень удобно использовать в `switch`.

Например:

```java
OrderStatus status = OrderStatus.PAID;

switch (status) {
    case NEW:
        System.out.println("Заказ создан");
        break;

    case PAID:
        System.out.println("Заказ оплачен");
        break;

    case COMPLETED:
        System.out.println("Заказ завершён");
        break;

    case CANCELLED:
        System.out.println("Заказ отменён");
        break;
}
```

Внутри `case` не нужно писать `case OrderStatus.PAID`, достаточно `case PAID`. Java уже знает, что `switch` работает с `OrderStatus`.

***

В современных версиях Java можно писать короче, без `break`:

```java
switch (status) {
    case NEW -> System.out.println("Заказ создан");
    case PAID -> System.out.println("Заказ оплачен");
    case COMPLETED -> System.out.println("Заказ завершён");
    case CANCELLED -> System.out.println("Заказ отменён");
}
```

Также `switch` может возвращать значение:

```java
String message = switch (status) {
    case NEW -> "Заказ создан";
    case PAID -> "Заказ оплачен";
    case COMPLETED -> "Заказ завершён";
    case CANCELLED -> "Заказ отменён";
};
```

```java
System.out.println(message);
```

Такой вариант `switch` называется **switch expression** — `switch` используется как выражение и возвращает результат.

***

Без `enum` тоже можно было бы написать:

```java
String status = "PAID";

switch (status) {
    case "NEW" -> ...
    case "PAID" -> ...
    case "COMPLETED" -> ...
}
```

Но строка может содержать что угодно. С `enum` набор вариантов ограничен и Java знает все возможные значения. Поэтому такой код безопаснее.

***

## `enum` может реализовывать интерфейс

Например:

```java
interface HasTitle {

    String getTitle();
}
```

```java
enum Priority implements HasTitle {

    LOW("Низкий"),
    MEDIUM("Средний"),
    HIGH("Высокий");

    private final String title;

    Priority(String title) {
        this.title = title;
    }

    @Override
    public String getTitle() {
        return title;
    }
}
```

***

## Но `enum` не может наследоваться от класса

Так нельзя:

```java
enum Priority extends SomeClass {
    ...
}
```

Каждый `enum` в Java на самом деле неявно наследуется от специального класса:

```java
java.lang.Enum
```

Поэтому другого родительского класса у него быть не может.

***

### Можно ли наследоваться от `enum`?

Тоже нет. Например:

```java
class CustomPriority extends Priority {
}
```

— так нельзя.
