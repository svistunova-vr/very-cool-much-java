# Ключевое слово static

> `static` означает, что поле или метод принадлежит **классу в целом**, а не конкретному объекту этого класса.

Обычно у каждого объекта свои поля:

```java
class User {

    private String name;

    public User(String name) {
        this.name = name;
    }
}
```

```java
User anna = new User("Anna");
User ivan = new User("Ivan");
```

У каждого объекта своё значение `name`. Такие поля называются **полями экземпляра (instance fields)**.

Если добавить `static`:

```java
class User {
    static String maxAge = 150;
}
```

поле `maxAge` уже относится не к конкретному `User`, а к самому классу `User`.

Обращаться к нему принято через название класса:

```java
System.out.println(User.maxAge);
```

а не:

```java
User user = new User();
System.out.println(user.maxAge);
```

Второй вариант Java разрешает (то есть ошиюку не выкинет), но так писать **не рекомендуется**: создаётся впечатление, будто значение принадлежит конкретному `user`.

`static` поле **общее для всех объектов класса** — не существует отдельного `maxAge` для Анны и отдельного для Ивана. Есть одно общее поле `maxAge`, и если изменить его, новое значение будет общим для всех.

***

#### Пример: счетчик созданных объектов

Например, хотим считать количество созданных пользователей:

```java
class User {

    private static int count = 0;

    private String name;

    public User(String name) {
        this.name = name;
        count++;
    }

    public static int getCount() {
        return count;
    }
}
```

Создаём:

```java
new User("Anna");
new User("Ivan");
new User("Kate");
```

Теперь:

```java
System.out.println(User.getCount()); // 3
```

Если бы `count` не был `static`, каждый объект получил бы **свой собственный счётчик**, что здесь не имеет смысла — для каждого пользователя получили бы `1`.

***

#### `static` методы

Метод тоже может принадлежать классу, а не конкретному объекту:

```java
class MathUtils {

    public static int square(int number) {
        return number * number;
    }
}
```

Чтобы вызвать его, объект создавать не нужно:

```java
int result = MathUtils.square(5);
System.out.println(result); // 25
```

Это используется в стандартной библиотеке Java:

```java
Math.max(10, 20);
Math.min(10, 20);
Math.abs(-100);
```

Мы не пишем:

```java
Math math = new Math();
```

потому что вычисление максимального числа не относится к состоянию какого-то конкретного объекта `Math`.

***

#### Когда метод имеет смысл делать `static`?

Сравним два метода. Есть заказ:

```java
class Order {

    private List<Product> products;

    public double calculateTotal() {
        ...
    }
}
```

`calculateTotal()` зависит от товаров **конкретного заказа**. Поэтому логично писать `order.calculateTotal()`, а не делать метод `static`.

Но метод:

```java
public static int max(int first, int second) {
    ...
}
```

не использует состояние какого-либо объекта. Мы передали ему всё необходимое в параметрах: `MathUtils.max(10, 20)`. Такой метод лучше сделать `static`.

> Если поведение относится к **конкретному объекту и его состоянию** — используем обычный метод.
>
> Если операция относится к **классу в целом** и конкретный объект ей не нужен — используем `static`.

***

## Ограничения `static`&#x20;

```java
class User {

    private String name;

    private static int count;

    public static void printCount() {
        System.out.println(count);
    }
}
```

Так можно, потому что и `printCount()`, и `count` оба `static`. Но так нельзя:

```java
public static void printName() {
    System.out.println(name);
}
```

Почему? `name` принадлежит **конкретному объекту**, а `printName()` — `static`, и вызывается без объекта: `User.printName()`. Какое имя он должен вывести — Анны или Ивана? Java этого определить не может. Поэтому:

> `static` метод не может напрямую обращаться к не-`static` полям и методам объекта.

***

Если все-таки нужно, можно получить объект явно. Например, в параметрах:

```java
class User {

    private String name;

    public static void printName(User user) {
        System.out.println(user.name);
    }
}
```

```java
User anna = new User("Anna");
User.printName(anna);
```

`static` метод получил конкретный объект через параметр и может работать с его данными.

***

Наоборот, обычный метод **может** использовать `static`:

```java
class User {

    private String name;

    private static int count;

    public void printInfo() {
        System.out.println(name);
        System.out.println(count);
    }
}
```

Обычный метод вызывается у конкретного объекта:

```java
user.printInfo();
```

поэтому он знает:

* какой сейчас используется конкретный `user`;
* и при этом может обращаться к общим данным класса.

***

С обычными методами мы могли писать:

```java
class User {

    private String name;

    public void changeName(String name) {
        this.name = name;
    }
}
```

`this` означает "текущий объект, у которого сейчас вызван метод". Но `static` метод вызывается без конкретного объекта, поэтому внутри него нельзя использовать `this`.

***

## `static final` — константы

Очень часто `static` используется вместе с `final`. Например:

```java
class Order {

    public static final int MAX_PRODUCTS = 100;
}
```

* `static` — значение относится ко всему классу `Order`, а не конкретному заказу;
* `final` — значение нельзя заменить после инициализации.

```java
if (products.size() > Order.MAX_PRODUCTS) {
    ...
}
```

Константы принято называть в `UPPER_SNAKE_CASE`:

```java
public static final int MAX_RETRIES = 3;
public static final String DEFAULT_LANGUAGE = "ru";
public static final double TAX_RATE = 0.20;
```

***

## `static` блок

Кроме полей и методов существует **static initialization block**:

```java
class Application {

    static {
        System.out.println("Инициализация класса");
    }
}
```

Он выполняется **один раз при инициализации класса**. Его используют, когда статические данные нужно инициализировать несколькими действиями.

Например:

```java
class Settings {

    static String language;
    static int maxAttempts;

    static {
        language = SomeUtility.loadUserLanguage(...);
        if (language == null) {
            throw new Exception("Не удалось определить язык пользователя");
        }    
        maxAttempts = SomeUtility.loadUserMaxAttempts(...);
        ...
    }
}
```

Обычно достаточно обычной инициализации:

```java
static String language = "ru";
static int maxAttempts = 3;
```

Поэтому `static` блок используется редко.

***

### Порядок инициализации

Статические поля и блоки выполняются **сверху вниз в том порядке, в котором написаны**:

```java
class Example {

    static int first = 10;

    static {
        System.out.println(first);
    }

    static int second = 20;
}
```

Сначала инициализируется `first`, затем выполняется `static` блок, затем `second`.

***

## `static` вложенный класс

`static` можно использовать и у класса, если он находится **внутри другого класса**:

```java
class Order {

    static class Validator {

        boolean isValid(Order order) {
            ...
        }
    }
}
```

Такой класс называется **static nested class** — статический вложенный класс. Создать его можно без объекта внешнего `Order`:

```java
Order.Validator validator = new Order.Validator();
```

В отличие от обычного внутреннего класса, ему не нужен конкретный объект внешнего класса. При этом обычный верхнеуровневый класс сделать `static` нельзя:

```java
static class User {
}
```

***

Также `static` используется в `static import`:

```java
import static java.lang.Math.PI;
```

После этого вместо `Math.PI` можно писать просто `PI`.
