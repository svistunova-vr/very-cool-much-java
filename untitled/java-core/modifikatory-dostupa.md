# Модификаторы доступа

> **Модификаторы доступа** определяют, из каких мест программы можно обращаться к классу, полю, методу или конструктору.

Например:

```java
class BankAccount {

    // private - напрямую работать с balance можно только внутри BankAccount
    private double balance;

    public double getBalance() { // public - метод можно вызывать извне
        return balance;
    }
}
```

В Java есть четыре уровня доступа:

* `public`
* `protected`
* `package-private`
* `private`

У `package-private` **нет отдельного ключевого слова**. Это доступ, который используется, если вообще ничего не написать.

***

### Package

Чтобы понять уровни доступа, нужно знать, что такое **package (пакет)**. Пакеты позволяют группировать связанные классы.

Например:

```java
package com.shop.order;
```

В этом пакете могут находиться `Order`, `OrderItem`, `OrderCalculator`.

А пользователи приложения — в другом:

```java
package com.shop.user;
```

Модификаторы доступа могут разрешать или запрещать доступ в зависимости от того, находится ли другой класс **в том же пакете**.

***

### `public`

`public` — самый широкий доступ.

```java
public void deposit(double amount) {
    ...
}
```

Такой метод можно вызвать из любого места программы, если сам класс тоже `public`.

```java
account.deposit(1000);
```

***

### `private`

`private` — самый узкий доступ, доступность **только внутри класса**.

```java
class BankAccount {

    private double balance;

    public void deposit(double amount) {
        balance += amount;
    }
}
```

Внутри `BankAccount` можно сделать:

```java
balance += amount;
```

Но снаружи нельзя:

```java
BankAccount account = new BankAccount();
account.balance = 1000; // ошибка
```

`private` часто используют для внутренних данных объекта: внешний код не должен менять их напрямую.

***

### Package-private

Если модификатор вообще не указан:

```java
package com.shop.order;

class Order {

    void calculateTotal() {
        ...
    }
}
```

то используется **package-private** доступ (иногда говорят "доступ по умолчанию").

Не путать с:

```java
default void calculateTotal()
```

это другое ключевое слово, которое используется в интерфейсах, и не связано с доступом.

Другой класс из этого же пакета `package com.shop.order` может вызвать `package-private` метод:

```java
package com.shop.order;

class OrderService {

    void process(Order order) {
        order.calculateTotal();
    }
}
```

Но класс из другого пакета, например, `package com.shop.user` — нет.

***

### `protected`

`protected` по строгости находится между `public` и `package-private`.

Он разрешает доступ:

1. внутри самого класса;
2. классам того же package;
3. наследникам из других package.

Например:

```java
public class User {

    protected void validate() {
        ...
    }
}
```

Наследник:

```java
public class Admin extends User {

    public void createAdmin() {
        validate();
    }
}
```

может использовать `validate()`. НЕ-наследник из того-же пакета — тоже может. НЕ-наследник из другого пакета — не может.

***

### Сравнение

<table data-header-hidden><thead><tr><th width="157.04296875"></th><th align="right"></th><th width="109.58203125" align="right"></th><th align="right"></th><th align="right"></th></tr></thead><tbody><tr><td></td><td align="right"><code>public</code></td><td align="right"><code>protected</code></td><td align="right"><code>package-private</code></td><td align="right"><code>private</code></td></tr><tr><td>Тот же класс</td><td align="right">✅</td><td align="right">✅</td><td align="right">✅</td><td align="right">✅</td></tr><tr><td>Другой класс того же package</td><td align="right">✅</td><td align="right">✅</td><td align="right">✅</td><td align="right">❌</td></tr><tr><td>Наследник в другом package</td><td align="right">✅</td><td align="right">✅</td><td align="right">❌</td><td align="right">❌</td></tr><tr><td>НЕ наследник из другого package</td><td align="right">✅</td><td align="right">❌</td><td align="right">❌</td><td align="right">❌</td></tr></tbody></table>

***

### Модификаторы у полей и методов

У полей и методов можно использовать все четыре варианта:

```java
public String name;
protected String email;
String address;          // package-private
private String password;
```

То же самое с методами:

```java
public void create() {}
protected void validate() {}
void calculate() {}

priate void checkPassword() {}
```

***

### Модификаторы у конструкторов

Конструкторы тоже имеют доступ. Например:

```java
public User(String name) {
    this.name = name;
}
```

Такой объект можно создавать извне. Но можно сделать:

```java
private User(String name) {
    this.name = name;
}
```

Теперь внешний код не сможет написать `new User("Anna")`. Это используется, когда класс хочет **сам контролировать создание своих объектов**.

Например:

```java
class User {

    private User(String name) {
        ...
    }

    public static User create(String name) {
        return new User(name);
    }
}
```

Теперь объект создаётся через:

```java
User user = User.create("Anna");
```

а не напрямую через `new User(...)`.

***

### Модификаторы у классов

С обычным верхнеуровневым классом вариантов меньше.

Можно:

```java
public class User {}
class User {} // package-private
```

Нельзя:

```java
private class User {}
protected class User {}
```

`private` и `protected` могут использоваться у **вложенных классов**, то есть классов, объявленных внутри другого класса:

```java
class Order {

    private class OrderValidator {
    }
}
```
