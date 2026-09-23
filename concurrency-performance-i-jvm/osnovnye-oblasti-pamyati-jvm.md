# Основные области памяти JVM

JVM описывается спецификацией от разработчиков Java — документом, в котором заданы правила, которым она должна следовать (должна уметь загружать классы, выполнять bytecode, управлять памятью, запускать сборщик мусора и т. д.)

Но спецификация JVM — только правила, не программа. Нужна конкретная реализация, которая всё это действительно делает. Одна из самых распространённых таких реализаций — HotSpot от Oracle. В конспекте ниже под JVM подразумевается именно HotSpot.

***

Представим сервис обработки платежей. В нём одновременно нужно хранить:

* сам объект платежа: его `id`, сумму, статус;
* временные данные конкретного вызова метода: номер попытки, ссылки на аргументы, промежуточные результаты вычислений;
* информацию о том, что вообще такое класс `Payment`: какие у него поля и методы, какие методы у него можно вызвать.

У этих данных очень **разный жизненный цикл**:

* объект платежа может жить довольно долго и обрабатываться в разных методах и потоках
* переменная внутри метода `attempt` нужна только пока выполняется один конкретный вызов этого метода
* описание класса `Payment` обычно требуется JVM в течение всего времени выполнения программы

Упрощенно память JVM можно представить так:

<figure><img src="../.gitbook/assets/image (10).png" alt=""><figcaption></figcaption></figure>

**Heap и данные классов используются всеми потоками совместно, а Stack у каждого потока свой.**

***

Пусть есть такой код:

```java
@RequiredArgsConstructor
class Payment {

    private final long id;
    private final long amount;

    public long getAmount() {
        return amount;
    }
}
```

```java
class PaymentService {

    public PaymentResult process(Payment payment) {
        int attempt = 1;

        long commission = calculateCommission(payment);

        return new PaymentResult(
                payment,
                commission,
                attempt
        );
    }

    private long calculateCommission(Payment payment) {
        return payment.getAmount() / 100;
    }
}
```

```java
Payment payment = new Payment(1001L, 50_000L);
PaymentService service = new PaymentService();
PaymentResult result = service.process(payment);
```

Теперь проследим, что происходит.

***

## Heap

> **Heap** — общая область памяти JVM, предназначенная для объектов и массивов.

Heap является общим для всех JVM threads и обслуживается автоматическим менеджером памяти — garbage collector.

Когда выполняется:

```java
new Payment(1001L, 50_000L)
```

появляется объект:

```
Heap

Payment@123
┌────────────────┐
│ id     = 1001  │
│ amount = 50000 │
└────────────────┘
```

Поля `long id`, `long amount` тоже находятся **внутри объекта в Heap**, не смотря на то, что это примитивы.

`payment` является локальной переменной метода, поэтому конкретная **ссылка** на нее находится в frame этого метода. Но **сам объект** находится в Heap.

{% hint style="danger" icon="circle-question" %}
Всегда ли ссылки находятся в Stack?
{% endhint %}

<details>

<summary>Ответ</summary>

Нет, например:

```java
class Order {

    private Payment payment;
}
```

Объект `Order` находится в Heap, а значит его поле `payment` (ссылка на `Payment`) тоже является частью объекта в Heap.

</details>

***

### Размер Heap

В HotSpot размер Heap контролируется через параметры:

* `-Xms` — размер Heap на старте работы приложения
* `-Xmx` — максимальный размер, до которого Heap разрешено расти&#x20;

Например, `-Xms512m -Xmx2g`: при запуске Heap выделяется 512 Мб, в процессе работы приложения создаются новые объекты и Heap увеличивается, но максимум до 2 Гб — если это значение будет превышено, приложение упадет с `OutOfMemory` error:

```
java.lang.OutOfMemoryError: Java heap space
```

В Intellij эти параметры можно настроить так:

<figure><img src="../.gitbook/assets/image (11).png" alt=""><figcaption></figcaption></figure>

Далее Modify Options:

<figure><img src="../.gitbook/assets/image (13).png" alt=""><figcaption></figcaption></figure>

<figure><img src="../.gitbook/assets/image (14).png" alt=""><figcaption></figcaption></figure>

Появится поле ввода, в нем указываем нужные параметры:

<figure><img src="../.gitbook/assets/image (15).png" alt=""><figcaption></figcaption></figure>

***

## Stack

Когда поток создаётся, для него появляется его Stack. Другой поток не может напрямую взять frame из этого Stack и работать с его локальными переменными.

Главная задача Stack — хранить состояние **выполняющихся вызовов методов**. Stack состоит из **stack frames**.

Допустим, выполняется цепочка:

```java
main()
    ↓
process()
    ↓
calculateCommission()
    ↓
getAmount()
```

Пока выполняется `getAmount()`, Stack выглядит так:

<figure><img src="../.gitbook/assets/image (16).png" alt="" width="563"><figcaption></figcaption></figure>

Каждый вызов метода создаёт новый **frame**. Когда `getAmount()` заканчивается, его frame удаляется, и текущим снова становится `calculateCommission()`:

<figure><img src="../.gitbook/assets/image (17).png" alt="" width="563"><figcaption></figcaption></figure>

После его завершения удаляется его frame и управление возвращается в `process()`, и т. д.&#x20;

***

### Что находится внутри frame?

#### Local Variables

```java
public PaymentResult process(Payment payment) {
    int attempt = 1;
    ...
}
```

```
process() frame

Local Variables
┌─────────┬──────────────────────┐
│ slot 0  │ this → PaymentService│
│ slot 1  │ payment → Payment    │
│ slot 2  │ attempt = 1          │
└─────────┴──────────────────────┘
```

Причём `payment` — ссылка, сам объект находится в Heap.

***

#### Operand Stack

Не путать с JVM Stack, это разные уровни: JVM Stack хранит вызовы методов, а operand stack внутри frame используется JVM для **промежуточных вычислений**.

Например:

```java
int sum(int a, int b) {
    int result = a + b;
    return result;
}
```

Для instance-метода local variables будут:

```
0 → this
1 → a
2 → b
3 → result
```

Bytecode выполняет вычисление примерно следующим образом:

```
iload_1  // берет a из local variables и кладет его в operand stack
iload_2
iadd
istore_3
iload_3
ireturn
```

Допустим, `a = 2, b = 3`. После `iload_1` получаем:

```
Operand Stack

2
```

После `iload_2`:

```
Operand Stack

3
2
```

`iadd` забирает два верхних значения — `3` и `2` — складывает их и кладёт в stack результат, т. е. `5`.

`istore_3` убирает `5` из operand stack и записывает его в local variable `result`.

***

### StackOverflowError

Рассмотрим ошибочную рекурсию:

```java
void process() {
    process();
}
```

Первый вызов `process()` создаёт frame, потом второй создает еще один и т. д.

```
process()
process()
process()
process()
...
```

Frames продолжают накапливаться. В какой-то момент Stack больше не может вместить очередной frame и возникает `java.lang.StackOverflowError`.

***

### Размер Stack

Для stack настройка только одна:

```bash
-Xss
```

Например:

```bash
-Xss1m
```

задаёт размер stack для каждого потока 1 Мб.

***

## Metaspace

При выполнении:

```java
new Payment(...)
```

JVM должна уже знать:

* что такое `Payment`
* какие у него поля
* какие у него методы и их сигнатуры (названия и типы параметров)
* как связаны классы
* runtime constant pool (числа, строки и т. п.)
* служебную информацию JVM о классе

В спецификации JVM есть понятие **Method Area**. Спецификация говорит, что это общая для потоков область, содержащая данные классов: runtime constant pool, данные о полях и методах и информацию, необходимую для выполнения методов. При этом в спецификации намеренно не описано, как Method Area должна быть устроена в конкретной JVM.

**Metaspace** — термин реализации Method Area в **HotSpot JVM**.

В HotSpot здесь находятся **метаданные загруженных классов**. Например:

```
Metaspace

Payment class metadata
├── имя класса
├── информация о полях
│   ├── id : long
│   └── amount : long
├── информация о методах
│   ├── getAmount()
│   └── <init>()
├── runtime constant pool
└── другая служебная class metadata
```

***

Metaspace находится **не в Java Heap**. Это особенно важно при ошибках памяти. Heap можно ограничить:

```bash
-Xmx2g
```

Но Metaspace использует **другую область памяти**. Для него существует отдельное ограничение:

```bash
-XX:MaxMetaspaceSize=256m
```

Если этот параметр не задан, HotSpot по умолчанию не устанавливает для Metaspace лимит, но все равно можно упереться в лимит доступной память процесса на конкретной ОС. Тогда получим:

```
java.lang.OutOfMemoryError: Metaspace
```

***

## Другие области памяти

Спецификация JVM также выделяет, например:

* `pc` register для каждого thread — хранит адрес команды, которую сейчас выполняет поток (грубо говоря, какой поток на какой строке находится)
* Native Method Stacks — стеки для нативных методов, например на C++, которые JVM использует для некоторых низкоуровневых операций
* runtime constant pool как часть Method Area.

В HotSpot тоже помимо Heap/Metaspace/Stack есть Code Cache, Direct Buffers, нативные библиотеки и т. п.

Все эти области сейчас знать не обязательно, но важно понимать: если мы выделили на все приложение 2 Гб (например, через Kubernetes), а в настройках JVM поставили `-Xmx1800m`, приложение может упасть из-за нехватки памяти. Казалось бы, Heap максимум 1.8 GB, значит 2 GB приложению хватит, но кроме Heap JVM должна разместить:

```
Heap
Metaspace
Thread stacks
Code Cache
Direct buffers
GC structures
JVM native memory
...
```

И суммарно процесс может превысить лимит. Тогда система может убить процесс даже если лимит по Java Heap не превышен, и мы не увидели `OutOfMemoryError`.

***

Полный пример:

1\. Загружается класс `PaymentService` ⇒ HotSpot создаёт class metadata:

```
Metaspace
    ↓
PaymentService metadata
```

2\. Создаём объект `Payment payment = new Payment(...)` ⇒ появляется:

```
Heap
    ↓
Payment object
```

А frame текущего метода содержит reference:

```
Stack
    ↓
payment → Payment object
```

3\. Вызываем метод `service.process(payment)` — появляется новый Stack Frame с локальными:

```
this
payment
attempt
...
```

4\. Метод вызывает другой метод `calculateCommission(payment)` — появляется ещё один frame.

5\. `calculateCommission(payment)` заканчивается — его frame сразу удаляется.

6\. `process()` заканчивается — удаляется frame `process()`. Но объекты Heap остаются, пока они достижимы.

7\. Объект становится недостижимым (кончается программа) — он не удаляется в тот же момент, но становится кандидатом на удаление, и через время его удалит отдельный процесс, сборщик мусора.

8\. Класс больше не нужен — class metadata очищается и может переиспользоваться.

***

{% hint style="info" icon="gitbook" %}
**Опишите основные области памяти JVM: Heap, Stack, Metaspace.**

**Heap** — общая для всех потоков область памяти JVM, в которой размещаются объекты и массивы. Максимальный размер Heap в HotSpot можно ограничить через `-Xmx`. Если памяти Heap не хватает, возможен `OutOfMemoryError: Java heap space`.

**Stack** свой у каждого потока. При каждом вызове метода в stack создается новый frame для этого вызова. В frame хранятся local variables, operand stack и служебная информация. Например, локальная переменная хранится в frame и содержит ссылку на объект Heap. После завершения метода его frame удаляется. При слишком глубокой цепочке вызовов, например из-за бесконечной рекурсии, возникает `StackOverflowError`. В HotSpot размер stack для каждого потока можно настраивать через `-Xss`.

**Metaspace** — общая область HotSpot для метаданных классов: информации о самих классах, их полях, методах, константах и т. п. В спецификации JVM соответствующее понятие называется Method Area, а Metaspace — конкретная реализация в HotSpot JVM. В современной Java Metaspace находится в памяти отдельно от Heap. Размер Metaspace можно ограничить через `-XX:MaxMetaspaceSize`; при его исчерпании возможен `OutOfMemoryError: Metaspace`.
{% endhint %}
