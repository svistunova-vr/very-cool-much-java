# ООП

> **ООП (объектно-ориентированное программирование)** — это подход к разработке, при котором программу представляют как набор **объектов**, взаимодействующих друг с другом.

У объекта есть:

* **данные**
* **поведение** — действия, которые объект умеет выполнять

Например, в приложении интернет-магазина могут существовать объекты `User` (пользователь), `Product` (товар), `Order` (заказ), `Cart` (корзина), `Payment` (платёж).

У объекта `Order` (заказ) могут быть данные `id` (идентификатор), `items` (товары), `status` (статус), `totalPrice` (итоговая стоимость) и поведение `addItem()` (добавить товар), `calculateTotal()` (рассчитать итоговую стоимость), `cancel()` (отменить заказ).

В Java объект создаётся на основе **класса**. Данные — поля, поведение — методы.

```java
class User {

    // Поля
    String name;
    int age;

    // Методы
    void sayHello() {
        System.out.println("Hello!");
    }
}
```

Создаём объект:

```java
User user = new User();

user.name = "Anna";
user.age = 20;

user.sayHello(); // Hello!
```

`User` — класс, то есть **шаблон** пользователя. А `user` Anna — конкретный объект этого класса.

{% hint style="danger" icon="circle-question" %}
Как бы вы спроектировали Java-класс для товара (`Product`)? Какие бы у него были поля и методы?
{% endhint %}

***

## Принципы ООП

### 1. Инкапсуляция

> **Инкапсуляция** — объединение данных и логики работы с ними внутри объекта + ограничение прямого доступа к внутреннему состоянию объекта.

Простыми словами, объект сам контролирует своё состояние.

Что будет, если нарушить этот принцип? Представим банковский счёт:

```java
class BankAccount {
    public double balance; // public - поле доступно извне класса
}
```

Теперь любой код может сделать:

```java
BankAccount account = new BankAccount();
account.balance = -1_000_000;
```

Сам объект никак не проверяет и не контролирует эти изменения — это опасно.

Чтобы исправить проблему, делаем поле приватным (`private`):

{% code overflow="wrap" %}
```java
class BankAccount {
    private double balance; // private - поле доступно только внутри класса
    
    public void deposit(double amount) { // методы public, доступны извне
        if (amount <= 0) {
            throw new IllegalArgumentException("Нельзя внести на счет <=0 денег!");
        }

        balance += amount;
    }

    public double getBalance() {
        return balance;
    }
}
```
{% endcode %}

Теперь нельзя написать `account.balance = -1000` — такой код даже не запустится. Внешний код должен пользоваться методами объекта:

```java
BankAccount account = new BankAccount();
account.deposit(1000);
System.out.println(account.getBalance());
```

Теперь сам `BankAccount` определяет, какие изменения разрешены с его данными.

***

Если просто сделать все поля `private` и добавить методы, **это еще не значит, что наш код следует инкапсуляции**.

Например:

```java
class User {

    private int age;

    public int getAge() {
        return age;
    }

    public void setAge(int age) {
        this.age = age;
    }
}
```

Поле действительно `private`, но по сути `User` никак не защищает свое состояние —  внешний код всё равно может сделать `user.setAge(-100500)`.

Исправление:

```java
public void changeAge(int age) {
    if (age < 0 || age > 150) {
         throw new IllegalArgumentException("Возраст должен быть от 0 до 150");
    }

    this.age = age;
}
```

***

Также инкапсуляция помогает разделить ответственность — класс сам отвечает за то, как именно в нем реализована логика, а другие разработчики могут его использовать, не вникая в детали.

Например, вчера класс хранил баланс просто в поле:

```java
private double balance;
```

А сегодня мы изменили внутреннюю реализацию и сделали список операций по счету (`+100`, `-50`, `+500`, ...):

```java
private List<Transaction> transactions;
```

и баланс теперь расчитываем по этим операциям. Если внешний код работал через `account.getBalance()`, то ему не обязательно знать, **как именно** внутри считается баланс, и для него ничего не поменяется.

{% hint style="danger" icon="circle-question" %}
Представим, что внутри `withdraw()` проверки баланса нет, но во внешнем коде мы ее добавили: `if (account.getBalance() >= amount) account.withdraw(amount)`. Это инкапсуляция или нет?
{% endhint %}

***

### 2. Наследование

> **Наследование** позволяет создать новый класс на основе существующего. Новый класс получает поля и методы родительского класса и может использовать их как есть или переопределять, меняя логику под себя.

В Java для наследования используется `extends`.

Например, есть базовый класс — животное:

```java
class Animal {

    void eat() {
        System.out.println("Eating");
    }
}
```

Создадим наследника — кошку:

```java
class Cat extends Animal {

    void meow() {
        System.out.println("Meow");
    }
}
```

Теперь:

```java
Cat cat = new Cat();
cat.eat(); // метод достался от родителя
cat.meow(); // свой новый метод
```

***

#### Отношение is-a

Наследование обычно описывает отношение **is-a** (является): `Cat` is an `Animal` (кошка является животным), `Manager` is an `Employee` (менеджер — это сотрудник).

Если фраза "X является Y" не имеет смысла, наследование делать неправильно. Например, "`Engine` — это `Car`" не имеет смысла. Двигатель — часть автомобиля. Здесь скорее отношение "`Car` has an `Engine`", и нужно использовать **композицию**:

```java
class Car {
    private Engine engine; // поле - Car has an Engine - композиция
}
```

а не:

```java
class Car extends Engine { // extends - Car is an Engine - наследование (не подходит)
}
```

***

#### Переопределение методов

Наследник может изменить поведение метода родителя.

```java
class Animal {
    void makeSound() {
        System.out.println("Some sound");
    }
}
```

Кошка:

```java
class Cat extends Animal {

    @Override // значит, что этот метод есть у родителя, но Cat его меняет 
    void makeSound() {
        System.out.println("Meow");
    }
}
```

Собака:

```java
class Dog extends Animal {

    @Override
    void makeSound() {
        System.out.println("Woof");
    }
}
```

Аннотация `@Override` показывает, что метод переопределяет метод родительского класса. Если ее не поставить, код запустится, но компилятор может не обнаружить критические ошибки (например, что вы неправильно переопределили метод). Поэтому эту аннотацию нужно ставить всегда при переопределении.

***

Иногда начинающие используют наследование так:

> В этом классе уже есть нужные мне три метода, значит я от него унаследуюсь.

Это плохая причина. Наследование задаёт довольно сильную связь: дочерний класс является разновидностью родительского.

Например, это логично:

```java
class Vehicle {
}

class Car extends Vehicle {
}
```

А так странно писать только потому, что логгеру понадобилось хранить список строк:

```java
class FileLogger extends ArrayList<String> {
}
```

Лучше использовать поле:

```java
class FileLogger {
    private List<String> logs;
}
```

***

#### Недостаток наследования

Наследник сильно зависит от родителя. **Изменения родительского класса могут повлиять на все наследники.**

Поэтому проверяем себя правилом `is-a`, в реальных проектах гораздо чаще используется композиция.

{% hint style="danger" icon="circle-question" %}
Разработчик хотел переопределить `save()`, но написал `void save(String value)`, тогда как у родителя был `void save(Object value)`. Код запустился. Почему?
{% endhint %}

{% hint style="danger" icon="circle-question" %}
Могут ли два класса одновременно быть связаны и отношением is-a, и отношением has-a? Если да, приведите пример. Если нет, объясните, почему.
{% endhint %}

***

### 3. Полиморфизм

В ООП полиморфизм позволяет работать с разными объектами через **общий тип**, при этом каждый объект выполняет действие по-своему.

Например:

```java
interface Animal {
    // для interface такой метод значит, что нет реализации метода "по умолчанию",
    // наследники должны обязательно сделать свою
    void makeSound(); 
}
```

```java
class Cat implements Animal {

    @Override
    public void makeSound() {
        System.out.println("Мяу");
    }
}
```

```java
class Dog implements Animal {

    @Override
    public void makeSound() {
        System.out.println("Гав");
    }
}
```

```java
Animal catAnimal = new Cat();
Animal dogAnimal = new Dog();
```

Тип переменной одинаков — `Animal`, но реальные объекты разные. Вызываем:

```java
catAnimal.makeSound(); // Мяу
dogAnimal.makeSound(); // Гав
```

Java выбирает реализацию метода в зависимости от **реального объекта**.

***

Зачем это нужно? Чтобы объекты одного типа обрабатывать общими методами или помещать в одну коллекцию, а не дублировать одну и ту же логику для каждого наследника.

Например, в приложении есть разные виды уведомлений: по email или на сайте.

```java
// abstract означает что нельзя создать общий Notification, только конкретного наследника
abstract class Notification { 

    private final String text;

    public Notification(String text) {
        this.text = text;
    }

    public String getText() {
        return text;
    }

    public abstract void send(); // наследники должны переопределить этот метод
}
```

Email-уведомление:

```java
class EmailNotification extends Notification {

    private final String email;

    public EmailNotification(String text, String email) {
        super(text); // заполняем поле, доставшееся от родителя
        this.email = email; // заполняем свое поле
    }

    @Override
    public void send() {
        System.out.println(
                "Отправляем на email " + email + ": " + getText()
        );
    }
}
```

Уведомление на сайте:

```java
class WebsiteNotification extends Notification {

    private final long userId;

    public WebsiteNotification(String text, long userId) {
        super(text);
        this.userId = userId;
    }

    @Override
    public void send() {
        System.out.println(
                "Показываем пользователю " + userId + ": " + getText()
        );
    }
}
```

Теперь разные уведомления можно положить в один список:

```java
List<Notification> notifications = List.of(
        new EmailNotification("Ваш заказ успешно оформлен", "user@mail.ru"),
        new WebsiteNotification("У вас новое сообщение", 15L),
        new EmailNotification("Пароль был изменён", "admin@mail.ru")
);
```

И написать один общий код обработки:

```java
for (Notification notification : notifications) {
    notification.send();
}
```

```
Отправляем на email user@mail.ru: Ваш заказ успешно оформлен
Показываем пользователю 15: У вас новое сообщение
Отправляем на email admin@mail.ru: Пароль был изменён
```

Плюс полиморфизма в том, что если позже нужно будет добавить SMS-уведомления:

```java
class SmsNotification extends Notification {

    @Override
    public void send() {
        // отправка SMS
    }
}
```

их тоже можно положить в `List<Notification>`, а существующий код менять не потребуется.

***

#### Статический тип и реальный тип

Мы можем написать (и так рекомендуется):

```java
Animal animal = new Cat();
```

Здесь `Animal` — **статический тип ссылки**. А `Cat` — **реальный тип объекта**.

Статический тип определяет, какие методы компилятор разрешит вызвать.

Например:

```java
interface Animal {
    void makeSound();
}
```

```java
class Cat implements Animal {

    public void makeSound() {
    }

    public void catchMouse() {
    }
}
```

```java
Animal animal = new Cat();
```

Можно вызвать:

```java
animal.makeSound();
```

но нельзя:

```java
animal.catchMouse();
```

потому что у типа `Animal` такого метода нет.

Однако если вызывается переопределённый метод:

```java
animal.makeSound();
```

Java выбирает реализацию по реальному объекту `Cat`.

Это называется **динамическим полиморфизмом**.

***

#### Перегрузка и переопределение

Важно не путать переопределение и **перегрузку методов**.

Это перегрузка (overloading):

```java
void print(String value) {
}

void print(int value) {
}

void print(double value) {
}
```

Выбор подходящего метода происходит во время компиляции программы (то есть еще до запуска) в зависимости от переданного типа: `print("Hello") -> print(String value)`.

А это переопределение (overriding):

```java
Animal animal = new Cat();
animal.makeSound();
```

Конкретная реализация `makeSound()` выбирается во время выполнения программы.

{% hint style="danger" icon="circle-question" %}
В чем разница между `Animal cat = new Cat()` и `Cat cat = new Cat()`?
{% endhint %}

{% hint style="danger" icon="circle-question" %}
Сработает ли такой код?

{% code overflow="wrap" %}
```java
 Animal animal = new Cat();
 animal = new Dog();
```
{% endcode %}

А такой?

{% code overflow="wrap" %}
```java
Cat animal = new Cat();
animal = new Dog();
```
{% endcode %}
{% endhint %}

***

### 4. Абстракция

> **Абстракция** — скрытие деталей, которые не нужны пользователю этого объекта. Мы описываем, **что объект умеет делать**, не заставляя пользователя знать, **как именно он это делает**.

Например, когда человек заводит автомобиль, он не обязан знать, как именно топливо поступает в двигатель. Для водителя существует простая абстракция — запустить двигатель. Сложная внутренняя реализация скрыта.

Пример в Java — есть обработчик платежей:

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

Пользователь этого интерфейса знает, что `PaymentProcessor` умеет выполнять платёж. Ему не обязательно знать, куда именно и как отправляется запрос по сети, как проверяется карта и подтверждается операция. Можно просто написать:

```java
paymentProcessor.pay(1000);
```

***

Абстракцию и инкапсуляцию иногда путают, но это не одно и то же. Абстракция = что объект предоставляет пользователю:

```java
account.withdraw(100); // "с аккаунта можно снять деньги"
```

Инкапсуляция = что объект НЕ предоставляет, что он защищает и каким образом.

```java
private double balance;

...

if (balance < amount) {
    throw new InsufficientFundsException();
}
```

{% hint style="danger" icon="circle-question" %}
Можно ли изменить внутреннюю реализацию объекта, не изменив его абстракцию? Приведите пример.
{% endhint %}

***

## Пример комбинации принципов

Предположим, что мы разрабатываем backend для сервиса заказа продуктов.

Есть абстракция:

```java
interface Delivery {
    void deliver(Order order);
}
```

Реализация доставки курьером:

```java
class CourierDelivery implements Delivery {

    @Override
    public void deliver(Order order) {
        System.out.println("Заказ доставляется курьером...");
    }
}
```

Реализация доставки самовывозом:

```java
class PickupDelivery implements Delivery {

    @Override
    public void deliver(Order order) {
        System.out.println("Клиет сам забирает заказ...");
    }
}
```

Сервис:

```java
class OrderService {

    private final Delivery delivery;

    public void setDeliveryType(Delivery delivery) {
        if (isValid(delivery)) { // какая-то логика проверки
            this.delivery = delivery;
        }
    }

    public void completeOrder(Order order) {
        delivery.deliver(order);
    }
}
```

*   **Абстракция**

    `interface Delivery` описывает общую возможность — `deliver(Order order)`, как-то доставить заказ, не раскрывая детали реализации
*   **Полиморфизм**

    В `OrderService` можно одинаково передать как `new CourierDelivery()`, так и `new PickupDelivery()`. Его код от этого не поменяется, но во время выполнения программы автоматически будет выбрана нужная реализация `deliver(...)`.
*   **Инкапсуляция**

    У `OrderService` поле `delivery` защищено — нельзя извне установить напрямую невалидное значение, класс сам контролирует доступ к своим данным.
*   **Наследование**

    Есть общий класс `Delivery` и конкретные его наследники — `CourierDelivery` и `PickupDelivery`.
