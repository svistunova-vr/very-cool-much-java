# Перегрузка и переопределение методов

Основы есть в [#peregruzka-i-pereopredelenie](oop.md#peregruzka-i-pereopredelenie "mention").

***

## Перегрузка (overloading)

> **Перегрузка** — несколько методов с одинаковым названием, но **разными параметрами**.

Пример:

```java
void send(String text) {
    System.out.println("Отправляем текст");
}

void send(String text, String email) {
    System.out.println("Отправляем на email");
}

void send(String text, long userId) {
    System.out.println("Отправляем пользователю сайта");
}
```

По параметрам Java понимает, какой вариант мы хотим использовать:

```java
send("Привет"); // String
send("Привет", "user@mail.ru"); // String, String
send("Привет", 15L); // String, long
```

Перегруженные методы могут отличаться:

* Количеством параметров — `void print(String text)` и `void print(String text, int count)`.
* Типами параметров — `void print(String value)` и `void print(int value)`.
* Порядком параметров — `void send(String text, int priority)` и `void send(int priority, String text)`.

Но нельзя перегрузить метод только по возвращаемому типу:

```java
String getValue() {
    return "Hello";
}

int getValue() {
    return 10;
}
```

Если у методов одинаковые название и параметры, Java не сможет определить, какой метод мы хотели вызвать.

Также нельзя поменять только названия параметров:

{% code overflow="wrap" %}
```java
void print(String text) {
}

void print(String textWithDifferentName) {
}
```
{% endcode %}

***

Перегруженный метод может быть у наследника. Например, у родителя:

```java
class Animal {

    void feed(Object food) {
        System.out.println("Animal eats something");
    }
}
```

У наследника:

```java
class Cat extends Animal {

    void feed(String food) {
        System.out.println("Cat eats " + food);
    }
}
```

`Cat` получает `feed(Object food)` от родителя и добавляет `feed(String food)`. Это **перегрузка**, а не переопределение, так как параметры разные.

***

> Конкретный перегруженный метод выбирается **во время компиляции**, то есть ещё до запуска программы.

Это называется **compile-time binding** — связывание во время компиляции. Также встречается название "static binding".

***

### Как выбирается перегрузка, если подходят несколько вариантов?

Например:

```java
void print(Object value) {
    System.out.println("Object");
}

void print(String value) {
    System.out.println("String");
}
```

Для `print("Hello")` технически подходят оба метода:

```
print(Object)
print(String)
```

потому что `String` является `Object`. Но Java выбирает **более конкретный подходящий вариант**: `print(String)`.

***

#### Важный нюанс со статическим типом

Есть два метода:

```java
void print(Object value) {
    System.out.println("Object");
}

void print(String value) {
    System.out.println("String");
}
```

Вызов `print("Hello")` выведет `"String"`, потому что `"Hello"` имеет тип `String`.

Но теперь:

```java
Object value = "Hello";
print(value);
```

будет вызван `print(Object value)` и получим `"Object"`, хотя реальный объект внутри `value` — строка.

Почему? Потому что при перегрузке Java ориентируется на **статический тип**:

```java
Object value
```

***

#### Преобразования типов

Java может подобрать перегрузку даже если типы не совпадают буквально.

Например:

```java
void print(long value) {
}
```

можно вызвать:

```java
print(10);
```

Хотя `10` имеет тип `int`, Java может расширить его до `long` (`int` меньше чем `long` по диапазону, поэтому помещается в него) ⇒ метод подходит.

***

#### Перегрузка конструкторов

Перегружать можно не только обычные методы, но и конструкторы:

```java
class User {

    private String name;
    private int age;

    User(String name) {
        this.name = name;
    }

    User(String name, int age) {
        this.name = name;
        this.age = age;
    }
}
```

Теперь объект можно создать по-разному:

```java
new User("Anna");
new User("Anna", 20);
```

***

## Переопределение — overriding

> **Переопределение** — наследник получает метод от родителя и делает **свою реализацию этого метода**.&#x20;

Например:

```java
class Animal {

    void makeSound() {
        System.out.println("Какие-то звуки");
    }
}
```

Наследник:

```java
class Cat extends Animal {

    @Override
    void makeSound() {
        System.out.println("Мяу");
    }
}
```

***

### Правила переопределения

#### **Должна совпадать сигнатура метода**

**Сигнатура метода** — его название и типы параметров.

Например:

```java
void save(Object value)
```

переопределяется как:

```java
@Override
void save(Object value)
```

Но если мы напишем так:

```java
void save(String value)
```

это уже другой метод, получится перегрузка, а не переопределение.

Именно поэтому полезно писать `@Override` — если разработчик случайно напишет:

```java
@Override
void save(String value)
```

а у родителя есть только только `void save(Object value)`, Java сразу сообщит об ошибке.

***

#### Возвращаемый тип должен совпадать или быть уже

Например:

```java
Animal create() {
    return new Animal();
}
```

Можно переопределить:

```java
@Override
Animal create() {
    return new Cat();
}
```

Также Java разрешает вернуть **более конкретный тип**:

```java
@Override
Cat create() {
    return new Cat();
}
```

Проверить себя можно по is-a: `Cat` is an `Animal` ⇒ можно вернуть `Cat` вместо `Animal`. Это называется **ковариантный возвращаемый тип**.

Для начала достаточно помнить, что нельзя произвольно заменить возвращаемый тип на совершенно другой.

***

#### Нельзя уменьшать доступность метода

Например, у родителя:

```java
public void send() {
}
```

Так нельзя:

```java
@Override
private void send() {
}
```

Наследник не должен сделать доступный метод родителя внезапно менее доступным.

***

#### `final` метод нельзя переопределить

`final` нужен как раз чтобы явно запретил изменение реализации:

```java
final void send() {
}
```

наследник не сможет написать:

```java
@Override
void send() {
}
```

***

#### `private` метод нельзя переопределить

Например:

```java
class Parent {

    private void calculate() {
    }
}
```

`calculate()` доступен только внутри `Parent`.

Если наследник создаст:

```java
class Child extends Parent {

    private void calculate() {
    }
}
```

это будет **собственный новый метод `Child`**, а не переопределение метода `Parent`.

***

#### Конструкторы не переопределяются

У родителя может быть:

```java
Animal() {
}
```

а у наследника:

```java
Cat() {
}
```

Но это не переопределение. Конструктор принадлежит только своему классу и не наследуется как обычный метод.

***

#### `static` методы можно только **перегружать**

```java
static void print(String value) {
}

static void print(int value) {
}
```

Переопределения у `static` методов нет. Например:

```java
class Animal {

    static void printType() {
        System.out.println("Animal");
    }
}
```

```java
class Cat extends Animal {

    static void printType() {
        System.out.println("Cat");
    }
}
```

Это называется **скрытием метода**, а не переопределением.

***

### Как Java выбирает переопределённый метод?

В этом отличие переопределения  от перегрузки.

```java
Animal animal = new Cat();
```

Статический тип — `Animal`, реальный тип — `Cat`. Вызываем `animal.makeSound()` — получаем "Мяу", потому что Java во время выполнения программы смотрит на **реальный объект**.

Это называется **runtime binding** или **dynamic binding** — связывание во время выполнения.

***

Можно условно разделить работу Java на два этапа:

*   **До запуска**

    Java смотрит на статический тип `animal` — это `Animal` — и проверяет: есть вообще у класса `Animal` метод `makeSound()`? Если нет, код даже не запустится.
*   **Во время выполнения**

    Java доходит до строки с вызовом метода и проверяет: на какой реальный объект в данный момент ссылается переменная `animal`? Это `Cat`, поэтому вызывается версия `makeSound()` именно из класса `Cat`, если в нем переопределен этот метод.

***

## Перегрузка + переопределение одновременно

{% hint style="success" icon="star" %}
<mark style="color:cyan;">Вопрос на собеседовании</mark>
{% endhint %}

Есть:

```java
class Animal {

    void feed(Object food) {
        System.out.println("Animal: Object");
    }
}
```

Наследник:

```java
class Cat extends Animal {

    @Override
    void feed(Object food) {
        System.out.println("Cat: Object");
    }

    void feed(String food) {
        System.out.println("Cat: String");
    }
}
```

Создаём:

```java
Animal animal = new Cat();
```

И вызываем:

```java
animal.feed("Fish");
```

Что произойдёт?

***

На этапе компиляции — Java смотрит на статический тип `Animal`. У `Animal` есть только `feed(Object)` поэтому выбирается именно эта **сигнатура метода**. Грубо говоря, при компиляции остается пометка "нужно вызвать `feed(Object)`".

Во время выполнения Java видит реальный объект `Cat`, но ищет в нем, согласно пометке, метод для `Object`. Поэтому будет `Cat: Object`.

А вот:

```java
Cat animal = new Cat();
animal.feed("Fish");
```

даст `Cat: String`, потому что теперь на этапе компиляции Java смотрит на методы уже в `Cat`, а не `Animal`:

```
feed(Object)
feed(String)
```

и выбирает более подходящий — `feed(String)`.
