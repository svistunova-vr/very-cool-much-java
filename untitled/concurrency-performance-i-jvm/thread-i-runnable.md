# Thread и Runnable

## Класс `Thread`

В Java поток представлен классом `java.lang.Thread`.

> `Thread` — класс, объект которого представляет отдельный поток выполнения в Java-приложении.

Но создание объекта `Thread` **ещё не означает, что новый поток уже выполняет код:**

```java
Thread thread = new Thread(); // поток создан, но не запущен
```

Чтобы поток начал выполняться, используется метод:

```
thread.start();
```

После вызова `start()` JVM запускает новый поток. Когда операционная система даст ему процессорное время, этот поток начнёт выполнять метод `run()`.

"Даст процессорное время" означает, что **процессор начнёт выполнять инструкции именно этого потока** — после `start()` новый поток существует и готов работать, но в системе одновременно могут быть десятки или сотни других потоков. Процессор не может выполнять их все сразу, особенно если ядер мало.

Поэтому операционная система распределяет процессор между потоками, например:

```
время →

Thread 1  ████
Thread 2      ████
Thread 1          ████
Thread 3              ████
```

***

### Метод `run()`

> `run()` содержит код, который должен выполнить поток.

Например, можно создать собственный класс-наследник `Thread`:

```java
class PaymentThread extends Thread {

    @Override
    public void run() {
        System.out.println("Обрабатываем платеж");
    }
}
```

Создаём объект:

```java
PaymentThread thread = new PaymentThread();
```

И запускаем:

```java
thread.start();
```

После этого новый поток выполнит свой метод `run()`, то есть в нашем случае:

```java
System.out.println("Обрабатываем платеж");
```

Но есть важный момент: сам по себе вызов метода `run()` **новый поток не создаёт**.

***

### `start()` и `run()` — в чём разница

Это один из частых вопросов на собеседованиях.

Рассмотрим:

```java
class PaymentThread extends Thread {

    @Override
    public void run() {
        System.out.println(Thread.currentThread().getName());
    }
}
```

`Thread.currentThread()` возвращает объект `Thread`, соответствующий потоку, который **прямо сейчас выполняет этот код** — то есть текущий поток.

Теперь создадим:

```java
PaymentThread thread = new PaymentThread();
```

*   Если вызвать `thread.run()`, это обычный вызов Java-метода. **Никакого нового потока JVM не запускает.**

    <figure><img src="../.gitbook/assets/image (22).png" alt=""><figcaption></figcaption></figure>
*   Если вызвать `start()`, JVM понимает, что этот `Thread` нужно запустить как **отдельный поток выполнения**. А уже сама JVM запускает выполнение `run()` этим новым потоком.

    <figure><img src="../.gitbook/assets/image (23).png" alt=""><figcaption></figcaption></figure>

То есть `thread.run()` означает "выполни метод `run()` **прямо сейчас в текущем потоке**", а `thread.start()` — "запусти **новый поток**, который затем выполнит `run()`".

***

Важно понимать, что после `start()` метод `run()` может выполниться не сразу.

Например:

```java
System.out.println("A");

Thread thread = new Thread(() -> {
    System.out.println("C");
});

thread.start();

System.out.println("B");
```

На первый взгляд можно ожидать:

```
A
C
B
```

Потому что после `start()` кажется, что новый поток сразу выполнит `run()` и выведет `C`. Но вполне можно получить:

```
A
B
C
```

Причина в том, что `start()` запускает новый поток, но не ждёт, пока тот выполнит `run()`. После `start()` одновременно готовы продолжать работу два потока: main (`println("B")`) и новый поток (`run()` -> `println("C")`). Какой из них получит CPU первым, **заранее не известно**.

После `start()` JVM делает новый поток доступным для выполнения, но конкретный момент, когда ему дадут CPU, определяется планированием потоков.

Поэтому после `A` возможны оба порядка: `A C B` или `A B C` (но `A` всегда будет первым, потому что новый поток запускается только после того, как `A` уже вывели).

Это ещё одно отличие от `thread.run()` — там все выполняется в одном потоке, поэтому все команды идут по порядку, и код в `main` продолжается только после того, как выполнены все строки из `run()`.

***

### Можно ли вызвать `start()` два раза?

Один объект `Thread` можно запустить только один раз. Повторный вызов `start()` приводит к `IllegalThreadStateException`.

```java
Thread thread = new Thread(...);

thread.start();
thread.start(); // IllegalThreadStateException
```

Если нужно выполнить тот же код ещё одним потоком, нужен новый объект `Thread`:

```java
new Thread(...).start();
```

***

## Интерфейс `Runnable`

Наследоваться от `Thread` часто неудобно. Вернёмся к примеру:

```java
class PaymentThread extends Thread {

    @Override
    public void run() {
        processPayment();
    }
}
```

Здесь в одном классе смешаны две разные ответственности: что нужно сделать (`processPayment()`) и кто это будет делать `Thread`.

Если нас интересует только задача — обработать конкретный платёж — и нам не нужно управлять детально механизмом, который эту задачу будет выполнять — `Thread` — то лучше использовать функциональный интерфейс `Runnable`:

```java
@FunctionalInterface
public interface Runnable {

    void run();
}
```

Он представляет **операцию, которую можно выполнить и которая не возвращает результат**. У него один абстрактный метод — `run()`, поэтому `Runnable` является функциональным интерфейсом.

Например:

```java
class PaymentTask implements Runnable {

    @Override
    public void run() {
        System.out.println("Обрабатываем платеж");
    }
}
```

Теперь `PaymentTask` вообще ничего не знает о потоках. Он описывает только действие, которое нужно выполнить — обработать платеж. Это лучше соответствует принципу Single Responsibility из SOLID.

Важно — сам по себе:

```java
PaymentTask task = new PaymentTask();
```

никакого нового потока не создаёт.

```
task.run();
```

тоже выполнит обычный метод в текущем потоке. Чтобы выполнить эту задачу новым потоком, передаём её в `Thread`:

```java
Runnable task = new PaymentTask();
Thread thread = new Thread(task);
thread.start();
```

Теперь обязанности разделены:

* `Runnable` — ЧТО выполнить? — `PaymentTask.run()`
* `Thread` — КАКОЙ поток это выполнит? — `thread.start()`

Если `Thread` создан с переданным `Runnable`, то `Thread.run()` вызывает `run()` этого `Runnable`.

Упрощённо можно представить так:

```java
class Thread {

    private Runnable task;

    public void run() {
        if (task != null) {
            task.run();
        }
    }
}
```

***

### Почему тогда у `Thread` тоже есть `run()`?

Потому что сам `Thread` реализует интерфейс `Runnable`:

```java
public class Thread implements Runnable
```

Это позволяет использовать оба подхода. Первый — наследование:

```java

class PaymentThread extends Thread {

    @Override
    public void run() {
        processPayment();
    }
}
```

Второй — отдельно создать задачу и передать её потоку:

```java
class PaymentTask implements Runnable {

    @Override
    public void run() {
        processPayment();
    }
}
```

```java
Thread thread = new Thread(new PaymentTask());
thread.start();
```

На практике почти всегда используется второй подход.

***

### `Runnable` через lambda

Механизм тот же, как у других функциональных интерфейсов. Вместо

```java
class PaymentTask implements Runnable {

    @Override
    public void run() {
        processPayment();
    }
}
```

можно написать:

```java
Runnable task = () -> processPayment();
```

Тогда полный код:

```java
Runnable task = () -> processPayment();
Thread thread = new Thread(task);
thread.start();
```

Часто это сокращают ещё сильнее:

```java
Thread thread = new Thread(() -> processPayment());
thread.start();
```

Или даже:

```java
new Thread(() -> processPayment()).start();
```

***

Отдельный `Runnable` также используется гораздо чаще, чем наследование от `Thread`, еще и из-за ограничения наследования в Java: класс может наследоваться только от одного класса.

Если написать:

```java
class PaymentTask extends Thread
```

он уже не сможет наследоваться от другого класса.

А интерфейс:

```java
implements Runnable
```

такого ограничения не создаёт.

***

### Когда использовать `Runnable`, а когда `Thread`?

Это не взаимоисключающие альтернативы — почти всегда они используются **вместе**:

```java
Runnable task = ...;
Thread thread = new Thread(task);
thread.start();
```

`Runnable` описывает работу, `Thread` запускает её в отдельном потоке.

Если нужно описать некоторую задачу — отправить уведомление, обработать платеж, создать отчет — лучше представлять её как `Runnable`, чтобы следовать принципу разделения ответственности.

```java
Runnable paymentTask = () -> {
    validatePayment();
    sendToBank();
    saveResult();
};
```

```java
Thread thread = new Thread(paymentTask);
thread.start();
```

Наследоваться непосредственно от `Thread` имеет смысл значительно реже — когда нам действительно нужен **собственный специализированный тип потока**, а не просто задача, которую нужно выполнить.

***

```java
Runnable
описывает задачу
        ↓
new Thread(task)
создаём объект потока
        ↓
thread.start()
запускаем отдельный поток
        ↓
Thread.run()
        ↓
task.run()
        ↓
выполняется код задачи
```

***

{% hint style="success" icon="star" %}
Что будет выведено?

```java
Thread thread = new Thread(
        () -> System.out.println(Thread.currentThread().getName()),
        "worker"
);

System.out.println("A");
thread.run();
System.out.println("B");
thread.start();
System.out.println("C");
```
{% endhint %}

<details>

<summary>Ответ</summary>

Может быть два варианта:

```
A
main
B
worker
C
```

или:

```
A
main
B
C
worker
```

По шагам:

1. `new Thread(...)` создает объект, но пока не запускает поток.
2. Основной поток выводит `A`.
3. `thread.run()` выполняется как обычный метод в текущем потоке, поэтому выводится `main`.
4. После завершения `run()` выводится `B`.
5. `thread.start()` запускает отдельный поток `worker`.
6. Теперь `main` выводит `C`, а новый поток - `worker`. Их порядок может быть любым.

</details>

{% hint style="success" icon="star" %}
Что произойдет в результате выполнения кода?

```java
Runnable task = () -> System.out.println("Работа");

Thread thread = new Thread(task);

thread.start();
thread.start();

System.out.println("Готово");
```
{% endhint %}

<details>

<summary>Ответ</summary>

Второй вызов `thread.start()` выбросит `IllegalThreadStateException`. Один объект `Thread` можно запустить **только один раз**, даже если его поток уже завершился.

По шагам:

1. Первый `start()` запускает поток, который выводит `Работа`.
2. Второй `start()` выбрасывает исключение в `main`.
3. Исключение здесь не перехватывается, поэтому строка `Готово` не выполняется.

Исправление — создать два объекта `Thread`:

```java
Runnable task = () -> System.out.println("Работа");
new Thread(task).start();
new Thread(task).start();
```

Получим:

```
Работа
Работа
```

**Объект задачи можно переиспользовать. Объект запущенного потока - нельзя.**

</details>

{% hint style="success" icon="star" %}
Что произойдет в результате выполнения кода?

```java
Runnable task = () -> System.out.println("Отчет готов");
task.start();
```
{% endhint %}

<details>

<summary>Ответ</summary>

Код **не скомпилируется**: у интерфейса `Runnable` нет метода `start()`.

Если написать:

```
task.run();
```

код скомпилируется и выведет:

```
Отчет готов
```

Но задача выполнится в **текущем потоке**. Нового потока не появится.

Для запуска в отдельном потоке нужно создать `Thread` с `task`:

```java
Thread thread = new Thread(task);
thread.start();
```

</details>

***

## Вопросы

{% hint style="info" icon="gitbook" %}
#### Расскажите про класс `Thread`. Что представляет собой метод `run()`? В чем разница между `start()` и `run()`?

* `Thread` — класс Java, представляющий поток выполнения. Создание объекта `Thread` само по себе новый поток ещё не запускает.
* Метод `run()` содержит код, который должен выполняться потоком. Можно либо создать наследника `Thread` и переопределить у него метод `run`, либо передать в `Thread` объект `Runnable` — тогда `Thread.run()` выполнит `Runnable.run()`.
* `run()`, если вызвать его напрямую, является обычным методом и выполняется текущим потоком. Новый поток он сам по себе не создает.
* `start()` запускает **отдельный поток выполнения**, и уже этот поток выполняет `run()`. Один объект `Thread` можно запустить через `start()` только один раз.
{% endhint %}

{% hint style="info" icon="gitbook" %}
#### Расскажите про интерфейс `Runnable`. Когда лучше использовать `Runnable`, а когда `Thread`?

`Runnable` — функциональный интерфейс, представляющий задачу. Он содержит один метод:

```java
void run();
```

`Runnable` сам по себе не создаёт и не запускает поток. Чтобы выполнить задачу отдельным потоком, её нужно передать в `Thread`:

```java
Runnable task = () -> processPayment();
Thread thread = new Thread(task);
thread.start();
```

На практике почти всегда используется именно `Runnable`, а не наследование от `Thread`:

* `Runnable` разделяет задачу и механизм её выполнения, что лучше соответствует принципу Single Responsibility из SOLID. `Runnable` отвечает за то, **что выполнить**, а `Thread` — за отдельный поток, который это выполнит
* `Runnable` — интерфейс, наследование от него не запрещает наследоваться от других интерфейсов и классов, а `Thread` — класс, если от него унаследоваться, от других классов наследоваться уже будет нельзя

Наследование от `Thread` используют значительно реже, когда действительно требуется специализированный тип потока.
{% endhint %}
