# SOLID

> **SOLID** — набор из пяти принципов проектирования объектно-ориентированного кода. Они помогают делать код понятнее и проще для изменения.

Если все части программы тесно связаны между собой, изменение одной функциональности может неожиданно потребовать изменений ещё во многих местах: добавили новый способ отправки уведомлений ⇒ пришлось менять `OrderService` ⇒ случайно сломали создание заказа ⇒ забыли что новый тип уведомлений надо было добавить еще и в `UserService` ⇒ пользователи перестали получать чеки на почту.&#x20;

SOLID помогает проектировать код так, чтобы разные части системы были **как можно меньше связаны между собой** и каждая отвечала за понятную задачу. Но это только рекомендации, а не жесткие правила — их нужно использовать в меру, иначе код наоборот станет сложнее поддерживать.

***

## `S` — Single Responsibility Principle

> **SRP — принцип единственной ответственности:** у класса должна быть одна основная ответственность и одна причина для изменения.

Иногда этот принцип упрощают до "один класс должен делать только одну вещь", но это не так — например:

```java
class User {

    void changeName(String name) {
        ...
    }

    void changeEmail(String email) {
        ...
    }

    void block() {
        ...
    }
}
```

Здесь три метода, но все они относятся к одной общей ответственности — управление состоянием пользователя. Это не нарушение SRP, и если мы для каждого отдельного метода будем делать свой класс, нам наоборот станет сложнее переиспользовать общую логику.

***

### Пример нарушения

Представим сервис оформления заказа:

```java
class OrderService {

    public void createOrder(Order order) {
        calculatePrice(order);
        saveOrder(order);
        sendEmail(order);
        generateReceipt(order);
    }

    private void calculatePrice(Order order) {
        // расчет стоимости
    }

    private void saveOrder(Order order) {
        // сохранение информации о заказе в базу данных
    }

    private void sendEmail(Order order) {
        // отправка email о том, что заказ подтвержден
    }

    private void generateReceipt(Order order) {
        // генерация чека
    }
}
```

`OrderService` одновременно отвечает за логику расчета стоимости, сохранение заказа, отправку email, создание чека, плюс он управляет последовательностью этих шагов. Из-за этого класс **может измениться по совершенно разным причинам**:

* поменялся расчёт стоимости;
* поменялся способ хранения заказов;
* изменился текст email;
* поменялся формат чека.

***

### Разделяем ответственность

```java
class OrderService {

    private final OrderStorage storage;
    private final EmailSender emailSender;
    private final ReceiptGenerator receiptGenerator;

    ...

    public void createOrder(Order order) {
        storage.save(order);
        emailSender.send(order);
        receiptGenerator.generate(order);
    }
}
```

Теперь:

* OrderService — управляет процессом создания заказа
* OrderStorage — хранит заказ
* EmailSender — отправляет письмо
* ReceiptGenerator — создаёт чек

Если изменился формат чека, скорее всего, нужно будет изменить только `ReceiptGenerator`, а не весь `OrderService`.

При поиске нарушения SRP в первую очередь проверяем огромные классы с названиями вроде:

```
Manager
Helper
Utils
CommonService
ApplicationService
```

— в них постепенно накапливается много несвязанного функционала.

***

## `O` — Open/Closed Principle

> **OCP — принцип открытости/закрытости:** код должен быть **открыт для расширения**, но **закрыт для изменения**.

Если появляется новый вариант поведения, желательно иметь возможность **добавить новую реализацию**, а не каждый раз переписывать большой существующий класс.

***

### Пример нарушения

Есть разные способы доставки:

```java
class DeliveryService {

    public int calculatePrice(String type) {

        if (type.equals("COURIER")) {
            return 500;
        }

        if (type.equals("PICKUP")) {
            return 0;
        }

        return 0;
    }
}
```

Потом появляется доставка по почте — добавляем:

```java
if (type.equals("POST")) {
    return 300;
}
```

Потом `EXPRESS` — снова меняем тот же класс. Со временем получим:

```java
if (...) {
    ...
} else if (...) {
    ...
} else if (...) {
    ...
} else if (...) {
    ...
} else if ...
```

Каждый новый тип доставки заставляет нас менять уже существующий `DeliveryService`.

***

### Как исправить

Вместо строк лучше описать типы доставки через `enum`:

```java
enum DeliveryType {
    COURIER,
    PICKUP,
    POST
}
```

Теперь создаём общий контракт:

```java
interface Delivery {

    DeliveryType getType();

    int calculatePrice();
}
```

Каждая реализация знает:

* какой у неё тип доставки;
* как рассчитывается её стоимость.

Доставка курьером:

```java
class CourierDelivery implements Delivery {

    @Override
    public DeliveryType getType() {
        return DeliveryType.COURIER;
    }

    @Override
    public int calculatePrice() {
        return 500;
    }
}
```

Самовывоз:

```java
class PickupDelivery implements Delivery {

    @Override
    public DeliveryType getType() {
        return DeliveryType.PICKUP;
    }

    @Override
    public int calculatePrice() {
        return 0;
    }
}
```

Теперь перепишем `DeliveryService`.

```java
class DeliveryService {

    private final List<Delivery> deliveries;

    public DeliveryService(List<Delivery> deliveries) {
        this.deliveries = deliveries;
    }

    public int calculatePrice(DeliveryType type) {
        for (Delivery delivery : deliveries) {
            if (delivery.getType() == type) {
                return delivery.calculatePrice();
            }
        }

        throw new IllegalArgumentException("Неизвестный тип доставки: " + type);
    }
}
```

Создаём сервис:

```java
List<Delivery> deliveries = List.of(
        new CourierDelivery(),
        new PickupDelivery()
);

DeliveryService service = new DeliveryService(deliveries);
```

Теперь можно:

```java
service.calculatePrice(DeliveryType.COURIER); // 500
service.calculatePrice(DeliveryType.PICKUP);  // 0
```

***

Позже появилась доставка по почте. Добавляем новый тип `POST` и новую реализацию:

```java
class PostDelivery implements Delivery {

    @Override
    public DeliveryType getType() {
        return DeliveryType.POST;
    }

    @Override
    public int calculatePrice() {
        return 300;
    }
}
```

Передаём её вместе с остальными:

```java
List<Delivery> deliveries = List.of(
        new CourierDelivery(),
        new PickupDelivery(),
        new PostDelivery()
);
```

И всё:

```java
service.calculatePrice(DeliveryType.POST); // 300
```

Сам класс `DeliveryService` **вообще менять не пришлось**. Он как работал по общему алгоритму:

```
найти Delivery нужного типа
        ↓
вызвать calculatePrice()
```

так и продолжает работать.

> Сервис **закрыт для постоянного изменения его алгоритма**, но **открыт для добавления новых реализаций `Delivery`**.

Если `CourierDelivery` и `PickupDelivery` уже работают, протестированы и используются пользователями, то при добавлении нового `PostDelivery` **чем меньше старого работающего кода приходится изменять, тем меньше вероятность случайно его сломать**.

***

### Не нужно заранее проектировать расширение на всё

Если сегодня есть:

```java
class DiscountCalculator {

    double calculate(Order order) {
        return order.getPrice() * 0.1;
    }
}
```

и никаких других видов скидок **даже не планируется**, не обязательно сразу применять все известные паттерны:

```
Discount
DiscountStrategy
DefaultDiscountStrategy
DiscountProvider
DiscountFactory
```

на случай, если когда-нибудь требования изменятся. OCP полезен там, где действительно есть или ожидается изменение, но это не значит, что всегда нужно заранее усложнять любой код.

***

## `L` — Liskov Substitution Principle

> **LSP — принцип подстановки Лисков:** объект наследника должно быть можно безопасно использовать везде, где ожидается объект родительского типа.

Мы уже изучали полиморфизм:

```java
Animal animal = new Cat();
```

Так можно, потому что `Cat is an Animal`.

LSP добавляет важное требование:

> Наследник должен не только формально соответствовать родителю по типу, но и **соблюдать его смысл и обещанное поведение**.

***

### Пример нарушения

Есть банковский счёт:

```java
class Account {

    public void withdraw(double amount) {
        ...
    }
}
```

Затем появился счёт, с которого нельзя снимать деньги:

```java
class LockedAccount extends Account {

    @Override
    public void withdraw(double amount) {
        throw new UnsupportedOperationException();
    }
}
```

Теперь общий код:

```java
void pay(Account account) {
    account.withdraw(100);
}
```

С каким-то видом `Account` сработает, а с каким-то неожиданно получит исключение. В итоге вызывающий код должен знать детали реализации, хотя вообще-то контракт `Account` обещает, что у любого наследника должен быть реализован метод `withdraw`. Значит, иерархию спроектировали неудачно.

***

Проблема может быть не только в исключениях:

```java
interface Storage {

    void save(String value);
}
```

По смыслу ожидаем, что после успешного выполнения `save()` значение должно быть сохранено. Но реализация ничего не сохраняет:

```java
class FakeStorage implements Storage {

    @Override
    public void save(String value) {
        System.out.println("Saved");
    }
}
```

Сигнатура соблюдена:

```java
void save(String value)
```

но **смысл контракта нарушен**.

***

## `I` — Interface Segregation Principle

> **ISP — принцип разделения интерфейсов:** класс не должен быть вынужден реализовывать методы, которые ему не нужны.

### Пример нарушения

Допустим, есть:

```java
interface FileStorage {

    void read();

    void write();

    void delete();

    void archive();
}
```

А некоторые реализации должны только читать файлы. Если они вынуждены делать:

```java
@Override
public void delete() {
    throw new UnsupportedOperationException();
}
```

лучше подумать о разделении:

```java
interface FileReader {
    void read();
}
```

```java
interface FileWriter {
    void write();
}
```

```java
interface FileRemover {
    void delete();
}
```

Теперь класс реализует только необходимые возможности. **Но не нужно всегда делать отдельный интерфейс на каждый метод.**

Например:

```java
interface UserStorage {

    User findById(long id);

    void save(User user);

    void delete(User user);
}
```

может быть совершенно нормальным интерфейсом. Разделять его на отдельные стоит только если реализации постоянно вынуждены реализовывать ненужные им методы или кидать в них `UnsupportedOperationException`.&#x20;

***

## `D` — Dependency Inversion Principle

> **DIP — принцип инверсии зависимостей:** основная логика программы должна зависеть от **абстракций**, а не быть жёстко привязана к конкретным реализациям.

### Что такое зависимость?

Представим:

```java
class EmailSender {

    public void send(String text) {
        System.out.println("Email: " + text);
    }
}
```

Сервис заказов:

```java
class OrderService {

    private final EmailSender emailSender = new EmailSender();

    public void createOrder() {
        ...

        emailSender.send("Заказ создан");
    }
}
```

`OrderService` использует `EmailSender` ⇒ `EmailSender` — **зависимость `OrderService`**. (Не путать с зависимостью в Maven.)

### Проблема

Через месяц заказчик решает, что вместо email уведомления должны приходить на сайте. Нужно менять `OrderService`:

```java
class OrderService {

    private final WebsiteSender sender = new WebsiteSender();

    ...
}
```

Получается, что изменилась изменилась техническая деталь отправки уведомления, но приходится менять логику работы заказов.

***

### Как исправить

Создадим интерфейс:

```java
interface NotificationSender {
    void send(String text);
}
```

Email:

```java
class EmailSender implements NotificationSender {

    @Override
    public void send(String text) {
        System.out.println("Email: " + text);
    }
}
```

Уведомление на сайте:

```java
class WebsiteSender implements NotificationSender {

    @Override
    public void send(String text) {
        System.out.println("Website: " + text);
    }
}
```

Теперь `OrderService` использует не конкретную реализацию, а абстрактный "отправитель уведомлений":

```java
class OrderService {

    private final NotificationSender sender;

    public OrderService(NotificationSender sender) {
        this.sender = sender;
    }

    public void createOrder() {
        ...

        sender.send("Заказ создан");
    }
}
```

Когда создаем сервис, выбираем конкретный sender — email:

```java
NotificationSender sender = new EmailSender();
OrderService service = new OrderService(sender);
```

Или сайт:

```java
NotificationSender sender = new WebsiteSender();
OrderService service = new OrderService(sender);
```

Сам `OrderService` при этом не меняется.&#x20;

***

### Передача зависимости через конструктор

В примере:

```java
public OrderService(NotificationSender sender) {
    this.sender = sender;
}
```

зависимость передаётся объекту извне через конструктор. То есть `OrderService` сам не делает `new EmailSender()` — кто-то снаружи решает, какую реализацию передать, `new EmailSender()` или `new WebsiteSender()`.

Позже Spring сможет автоматизировать создание и передачу таких зависимостей, но сама идея существует независимо от Spring.

***

Сам по себе `new SomeObject()` — не проблема. Например,

```java
User user = new User("Anna");
```

абсолютно нормально.

Проблема возникает, когда важный класс сам жёстко выбирает конкретную реализацию своей зависимости:

```java
class OrderService {
    private final NotificationSender sender = new EmailSender();
}
```

Теперь изменить способ уведомления без изменения `OrderService` нельзя.

***

### Интерфейс нужен не для каждого класса

После изучения DIP может появиться желание писать:

```
UserService
UserServiceImpl

PriceCalculator
PriceCalculatorImpl

OrderValidator
OrderValidatorImpl
```

даже если существует ровно одна реализация и никакой изменяемости здесь нет. Но SOLID этого не требует.

Один

```java
class PriceCalculator {

    public double calculate(Order order) {
        ...
    }
}
```

может быть лучше, чем лишняя пара:

```java
interface PriceCalculator {
    ...
}
```

```java
class PriceCalculatorImpl
        implements PriceCalculator {
    ...
}
```

если абстракция пока ничего полезного не даёт.

***

## Как принципы связаны друг с другом?

Рассмотрим оплату заказа. Общий контракт:

```java
interface PaymentProcessor {
    void pay(double amount);
}
```

Оплата картой:

```java
class CardPaymentProcessor implements PaymentProcessor {

    @Override
    public void pay(double amount) {
        ...
    }
}
```

Оплата наличными:

```java
class CashPaymentProcessor implements PaymentProcessor {

    @Override
    public void pay(double amount) {
        ...
    }
}
```

Сервис заказа:

```java
class OrderService {

    private final PaymentProcessor processor;

    public OrderService(PaymentProcessor processor) {
        this.processor = processor;
    }

    public void pay(Order order) {
        processor.pay(order.getTotalPrice());
    }
}
```

***

* **Single Responsibility** — OrderService занимается заказом, PaymentProcessor занимается оплатой. Ответственность разделена.
* **Open/Closed** — можно добавить новый способ оплаты (`class SbpPaymentProcessor implements PaymentProcessor`), не переписывая `OrderService`.
* **Liskov Substitution** — любая реализация `PaymentProcessor` должна действительно уметь выполнить обещанную операцию `pay()`. Не должно быть реализации, которая формально реализует `PaymentProcessor`, но при вызове `pay()` неожиданно кидает `UnsupportedOperationException`.
* **Interface Segregation** — если некоторым объектам нужна только оплата, не надо заставлять их зависеть от огромного `interface` с методами `pay()`, `refund()`, `createSubscription()`, `cancelSubscription()`, `generateReport()` и т. д., если в реальности они могут только `pay()`, а на остальные методы будут кидать `UnsupportedOperationException` или просто ничего не делать.
* **Dependency Inversion** — `OrderService` зависит от `PaymentProcessor`, а не конкретно от `CardPaymentProcessor`.&#x20;

***

{% hint style="success" icon="star" %}
Какой из принципов SOLID ты считаешь самым важным и почему?
{% endhint %}

<details>

<summary>Ответ</summary>

Если нужно выбрать один, то **SRP — Single Responsibility Principle**. Это базовая идея хорошего проектирования: класс должен иметь одну понятную ответственность и не собирать в себе несвязанные причины для изменения. Если SRP соблюдается, код обычно проще читать, тестировать и менять, а изменения одной функциональности меньше затрагивают остальные.

При этом по критичности серьезнее нарушение **LSP**. Нарушение SRP приводит к сложному и плохо поддерживаемому коду, но он все еще может правильно работать, а вот нарушение LSP может буквально сломать полиморфизм. Если я работаю с типом `PaymentProcessor`, должна быть возможность подставить **любую** его реализацию и рассчитывать на соблюдение контракта. Если одна реализация внезапно бросает `UnsupportedOperationException` или ведёт себя принципиально иначе, абстракции уже нельзя доверять, и могут возникнуть критические баги.

</details>

{% hint style="success" icon="star" %}
Какие, на ваш взгляд, есть недостатки у SOLID?
{% endhint %}

<details>

<summary>Ответ</summary>

Недостатки SOLID в основном связаны не с самими принципами, а с их **слишком буквальным применением**. Если пытаться соблюдать их везде, можно легко получить overengineering: много мелких классов, интерфейсов и дополнительных уровней абстракции там, где простого решения было бы достаточно.

Например, ради DIP иногда создают `UserService` + `UserServiceImpl`, хотя существует только одна реализация, и вряд ли может появиться другая. Код формально лучше соответствует SOLID, но читать его становится сложнее.

SOLID — это всегда компромисс: гибкость достигается ценой усложнения структуры. Поэтому применять SOLID лучше там, где есть реальная сложность или ожидаемые изменения, а не проектировать все возможные расширения заранее.

Если применение принципа делает конкретный код сложнее без реальной выгоды, значит, скорее всего, мы применяем его слишком формально.

</details>
