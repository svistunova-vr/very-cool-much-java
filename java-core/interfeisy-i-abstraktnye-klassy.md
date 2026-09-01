# Интерфейсы и абстрактные классы

> **Интерфейс (`interface`)** — это контракт: он описывает, **какие возможности должен предоставлять объект**, но обычно не определяет, как именно они реализованы.

Например, в приложении есть разные способы отправки уведомлений:

```java
interface NotificationSender {
    void send(String text);
}
```

Интерфейс — контракт "любой `NotificationSender` должен уметь отправлять сообщение", как именно этот контракт реализовать — решает наследник.&#x20;

```java
class EmailNotificationSender implements NotificationSender {

    @Override
    public void send(String text) {
        System.out.println("Отправляем email: " + text);
    }
}
```

```java
class WebsiteNotificationSender implements NotificationSender {

    @Override
    public void send(String text) {
        System.out.println("Показываем на сайте: " + text);
    }
}
```

***

### `implements`

Класс реализует интерфейс через `implements`:

```java
class EmailNotificationSender implements NotificationSender {
    ...
}
```

Если интерфейс содержит обычный метод без реализации:

```java
interface NotificationSender {

    void send(String text);
}
```

то наследник **обязан** его реализовать:

```java
@Override
public void send(String text) {
    ...
}
```

Либо он сам должен быть `abstract` классом или интерфесом, тогда обязанность реализовать метод переходит уже его наследникам.

***

### Реализация должна быть `public`

Такую запись:

```java
interface NotificationSender {

    void send(String text);
}
```

можно понимать как:

```java
public abstract void send(String text);
```

Поэтому так нельзя:

```java
class EmailSender implements NotificationSender {

    @Override
    private void send(String text) {
    }
}
```

Вспоминаем, что у наследника не могут быть более строгие ограничения, чем у родителя.

***

### Можно ли создать объект интерфейса?

```java
NotificationSender sender = new NotificationSender();
```

Нельзя, потому что интерфейс — абстрактный контракт. Но можно так:

```java
NotificationSender sender = new EmailNotificationSender();
```

Это тот же принцип, который уже встречался:

```java
Animal animal = new Cat();
```

Статический тип — интерфейс или родительский тип, реальный объект — конкретная реализация.

***

### Сколько интерфейсов может реализовывать один класс?

В отличие от классов, для интерфесов Java разрешает **множественную реализацию**. Один класс может реализовывать несколько интерфейсов:

```java
class SmartPhone implements Camera, MusicPlayer, GPS {
}
```

При этом класс должен реализовать необходимые методы каждого интерфейса.

Но при этом нельзя наследоваться от нескольких классов:

```java
class Cat extends Animal, Pet {
}
```

Можно сочетать наследование от класса (не более 1) и интерфейсов (сколько угодно):

```java
class Cat extends Animal
        implements Pet, Playable, Trackable {
}
```

***

### Может ли интерфейс наследовать другой интерфейс?

Да. Для этого используется не `implements`, а `extends`:

```java
interface Animal {
    void eat();
}
```

```java
interface Pet extends Animal {
    void play();
}
```

Теперь класс-наследник `Pet` должен реализовать и `eat()` из `Animal`, и `play()` из `Pet`:

```java
class Cat implements Pet {

    @Override
    public void eat() {
        System.out.println("Кошка ест");
    }

    @Override
    public void play() {
        System.out.println("Кошка играет");
    }
}
```

Как и в случае наследования классов от интерфейсов, наследник-итерфейс тоже может наследовать несколько интерфейсов:

```java
interface Camera {
    void takePhoto();
}
```

```java
interface GPS {
    void navigate();
}
```

```java
interface SmartDevice extends Camera, GPS {
    void connectToInternet();
}
```

***

## Методы с реализацией в интерфейсе

Раньше в Java интерфейсах поддерживались только методы без реализации:

```java
void send(String text);
```

Но современные интерфейсы Java могут содержать и методы **с реализацией**.

***

### `default` методы

`default` позволяет определить в интерфейсе готовую реализацию метода:

```java
interface NotificationSender {

    void send(String text);

    default void sendWelcomeMessage() {
        send("Добро пожаловать!");
    }
}
```

Теперь класс обязан реализовать `send(...)`, но `sendWelcomeMessage()` уже имеет готовую реализацию:

```java
class EmailSender implements NotificationSender {

    @Override
    public void send(String text) {
        System.out.println("Email: " + text);
    }
}
```

Использование:

```java
EmailSender sender = new EmailSender();
sender.sendWelcomeMessage(); // Email: Добро пожаловать!
```

`EmailSender` сам не реализовал `sendWelcomeMessage`, он получил готовую реализацию из интерфейса.

***

Пример когда `default` полезен:

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

Представим, что этот интерфейс уже реализуют десятки классов. Если мы добавляем новый обычный метод:

```java
void refund(double amount);
```

то его надо добавить и во всех существующих наследников. Иногда это неудобно, и лучше при добавлении метода прописать поведение по умолчанию:

```java
default void refund(double amount) {
    System.out.println("Возврат пока не поддерживается");
}
```

Существующие классы продолжат работать. А наследник, которому нужна другая логика, может переопределить метод:

```java
@Override
public void refund(double amount) {
    System.out.println("Возвращаем " + amount);
}
```

***

#### Что если у двух интерфейсов одинаковый `default` метод?

```java
interface EmailSender {

    default void send() {
        System.out.println("Email");
    }
}
```

```java
interface SmsSender {

    default void send() {
        System.out.println("SMS");
    }
}
```

Если наследуемся от обоих интерфейсов, какую реализацию `send()` выбрать — `Email` или `SMS`?

```java
class NotificationService implements EmailSender, SmsSender {
}
```

Java видит конфликт, и выдает ошибку — класс должен решить конфликт сам:

```java
class NotificationService implements EmailSender, SmsSender {

    @Override
    public void send() {
        System.out.println("Своя реализация");
    }
}
```

Если подходит готовая реализация одного из родителей, можно написать так:

```java
@Override
public void send() {
    EmailSender.super.send();
}
```

Или:

```java
@Override
public void send() {
    SmsSender.super.send();
}
```

***

### `static` методы

Интерфейс может иметь `static` метод:

```java
interface NotificationSender {

    static boolean isValid(String text) {
        return text != null && !text.isBlank();
    }
}
```

Вызывается он через имя **самого интерфейса**:

```java
boolean valid = NotificationSender.isValid("Hello");
```

Так же, как в классах: `Math.max(10, 20)`.

`static` метод не принадлежит объектам реализаций:

```java
class EmailSender implements NotificationSender {
}

...

EmailSender.isValid("Hello"); // так НЕПРАВИЛЬНО
NotificationSender.isValid("Hello") // ок
```

***

#### `static` методы интерфейса не переопределяются

```java
interface NotificationSender {

    static void printInfo() {
        System.out.println("NotificationSender");
    }
}
```

Класс может объявить свой:

```java
class EmailSender implements NotificationSender {

    static void printInfo() {
        System.out.println("EmailSender");
    }
}
```

Но это не переопределение, а два разных `static` метода:

```java
NotificationSender.printInfo();
EmailSender.printInfo();
```

***

### `private` методы

Начиная с Java 9 интерфейс может содержать `private` методы. Например:

```java
interface NotificationSender {

    default void sendSuccess() {
        log("SUCCESS");
    }

    default void sendError() {
        log("ERROR");
    }

    private void log(String status) {
        System.out.println("Status: " + status);
    }
}
```

Зачем это нужно? Без `private` пришлось бы повторять одну и ту же внутреннюю логику:

```java
default void sendSuccess() {
    System.out.println("...");
}

default void sendError() {
    System.out.println("...");
}
```

С `private` можно вынести общую часть в `private void log(...)`.

***

Реализация:

```java
class EmailSender implements NotificationSender {
}
```

не может вызвать `log("SUCCESS")` и не должна его реализовывать. `private` существует только внутри самого интерфейса.

***

#### `private static`

Можно объединить `private static`, например:

```java
interface Validator {

    static boolean isEmailValid(String email) {
        return email != null && checkLength(email);
    }

    private static boolean checkLength(String value) {
        return value.length() <= 100;
    }
}
```

`checkLength()` нужен только внутренней реализации самого интерфейса. Снаружи `Validator.checkLength(...)` недоступен.

***

## Поля интерфейса

Интерфейс может содержать поля:

```java
interface HttpConfig {
    int MAX_RETRIES = 3;
}
```

Но это не обычное поле, как у объекта. Все поля интерфейса автоматически `public`, `static` и `final` — публичные константы.

Поэтому обращаемся к полю снаружи так:

```java
HttpConfig.MAX_RETRIES;
```

И изменить значение нельзя:

```java
HttpConfig.MAX_RETRIES = 10; // ошибка
```

Например, так нельзя использовать интерфейс:

```java
interface User {
    String name;
}
```

в надежде, что у каждого пользователя будет своё `name`. Поле интерфейса на самом деле станет общей для всех объектов `User` константой:

```java
public static final String name = ...;
```

***

### **Можно ли переопределить поле интерфейса?**

Нет. **Поля вообще не участвуют в переопределении так, как методы.**

Например:

{% code overflow="wrap" %}
```java
interface Config {
    int TIMEOUT = 10;
}
```
{% endcode %}

Класс может объявить своё поле с таким же именем:

{% code overflow="wrap" %}
```java
class AppConfig implements Config {
    static int TIMEOUT = 20;
}
```
{% endcode %}

Но это **другое поле**, а не переопределение `Config.TIMEOUT`.

{% code overflow="wrap" %}
```java
Config.TIMEOUT;    // 10
AppConfig.TIMEOUT; // 20
```
{% endcode %}

Поле интерфейса `Config.TIMEOUT` изменить нельзя, потому что оно неявно `final`. Поле `AppConfig.TIMEOUT` менять можно, потому что оно уже в `class`, а не `interface` ⇒ нет "неявных" ключевых слов ⇒ чтобы поле было `final`, надо явно написать `final`.

***

## `interface extends`, класс `implements`

Класс наследует класс:

```java
class Cat extends Animal {
}
```

Класс реализует интерфейс:

```java
class Cat implements Pet {
}
```

Интерфейс наследует интерфейс:

```java
interface Pet extends Animal {
}
```

Можно совместить:

```java
class Cat extends Animal implements Pet, Playable {
}
```

***

## Интерфейс и абстрактный класс

Оба позволяют задать общий тип, но смысл немного разный.

* Интерфейс в первую очередь описывает, **что объект умеет делать**: `NotificationSender` умеет `send(Notification notification`, `PaymentProcessor` умеет `process(Payment payment)` и т. п.
* Абстрактный класс может дополнительно хранить **общую структуру** (набор полей), которую наследники получают от родителя:

```java
abstract class Notification {

    private final String text; // у всех уведомлений есть текст
    
    // в interface так нельзя, text превратится в константу

    ...
}
```

Другое важное отличие:

* `class` может `extends` только один класс
* `class` может `implements` несколько интерфейсов

***

**Интерфейс** обычно подходит, когда мы описываем отдельную **возможность** или контракт, которую могут поддерживать совершенно разные классы:

{% code overflow="wrap" %}
```java
interface Printable {
    void print();
}

class Invoice implements Printable { ... }
class Report implements Printable { ... }
class Ticket implements Printable { ... }
```
{% endcode %}

`Invoice`, `Report` и `Ticket` не обязаны иметь общего родителя или общие поля. Их объединяет только возможность `print()`.

**Абстрактный класс** обычно подходит, когда классы действительно являются разновидностями одной сущности и им нужны **общее состояние и/или общая реализация**.

{% code overflow="wrap" %}
```java
abstract class Notification {

    private final String text;
    private final LocalDateTime createdAt;

    public Notification(String text) {
        this.text = text;
        this.createdAt = LocalDateTime.now();
    }

    public String getText() {
        return text;
    }

    public abstract void send();
}
```
{% endcode %}

`EmailNotification` и `WebsiteNotification` — разные виды `Notification`, у которых есть общие данные и поведение.

Наличие общего поведения по умолчанию не означает, что обязательно нужен абстрактный класс — у интерфейсов есть `default` методы, через которые его можно реализовать. Главный вопрос скорее в том, нужна ли объектам **общая базовая сущность и состояние**. Если да, лучше выбрать `abstract class`, но чаще всего достаточно `interface`.

***

### Особенности абстрактного класса

#### Может ли абстрактный класс иметь конструктор?

Да, например:

{% code overflow="wrap" %}
```java
abstract class Notification {

    private final String text;

    public Notification(String text) {
        this.text = text;
    }
}
```
{% endcode %}

Просто в коде написать `new Notification(...)` нельзя, если класс `abstract`, но можно использовать его внутри конструктора наследника:

{% code overflow="wrap" %}
```java
class EmailNotification extends Notification {

    public EmailNotification(String text) {
        super(text); // вызвали конструктор родителя
    }
}
```
{% endcode %}

***

#### Может ли абстрактный класс быть `final`?

Нет, так нельзя.&#x20;

* `abstract` означает, что класс предполагает создание наследника
* `final` означает, что наследоваться от класса нельзя.

Они противоречат друг другу.

***

#### Может ли абстрактный класс содержать `final` методы?

Да, например:

{% code overflow="wrap" %}
```java
abstract class Notification {

    public final void validate() {
        System.out.println("Проверка");
    }

    public abstract void send();
}
```
{% endcode %}

Наследник обязан реализовать `send()`, но не сможет переопределить `validate()`.

`abstract` метод не может одновременно быть `final`: `abstract` требует реализации в наследнике, а `final` запрещает её переопределять.

***

{% hint style="danger" icon="circle-question" %}
Класс `Device` реализует интерфейсы `Camera` и `GPS`. Позже в оба интерфейса добавили метод `default void reset()`. Надо ли что-то менять в классе?
{% endhint %}

{% hint style="danger" icon="circle-question" %}
Есть `interface Pet extends Animal`. Класс `Cat implements Pet`, не написано что он `implements Animal`. Должен ли он реализовывать методы `Animal`?
{% endhint %}
