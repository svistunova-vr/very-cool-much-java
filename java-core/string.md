# String

> `String` — класс для хранения строк. Объекты `String` **неизменяемы (immutable)**, то есть после создания содержимое строки изменить нельзя.

Например:

```java
String name = "Anna";
```

Пробуем добавить текст:

```java
name = name + " Ivanova";
```

Может показаться, что мы изменили существующую строку, но на самом деле объект `"Anna"` не изменился. Была создана **новая строка** `"Anna Ivanova"`, после чего переменная `name` стала ссылаться уже на неё.

***

### Методы `String` не изменяют исходный объект

```java
String text = "hello";
text.toUpperCase();
System.out.println(text); // hello
```

`toUpperCase()` возвращает новую строку, а не меняет исходную. Если хотим изменить `text`, нужно явно прописать:

```java
String text = "hello";
text = text.toUpperCase();
System.out.println(text); // HELLO
```

То же самое относится и к другим методам:

```java
replace(...)
substring(...)
trim()
toLowerCase()
concat(...)
```

***

### Почему `String` сделали immutable?

Это даёт несколько преимуществ. Первое — строку можно безопасно переиспользовать:

```java
String first = "Java";
String second = first;

first = first + " 21";

System.out.println(first);  // Java 21
System.out.println(second); // Java
```

Обе переменные ссылаются на один объект. Если бы строку можно было изменить через `first`, неожиданно изменилось бы значение и для `second`. Но так как `String` immutable, этого не произойдёт.

Второе — `String` удобно использовать в многопоточном коде Несколько потоков могут читать один объект `String`, и один поток не сможет неожиданно изменить его содержимое для остальных.

Третье — String можно безопасно использовать как ключ `HashMap`:

```java
Map<String, User> users = new HashMap<>();
users.put("user-1", user);
```

Если бы содержимое строки `"user-1"` могло измениться после добавления в `HashMap`, поиск по ключу мог бы сломаться.

***

## Конкатенация строк

Конкатенация — объединение строк:

```java
String fullName = firstName + " " + lastName;
```

Для небольшого количества операций оператор `+` использовать нормально. Но если мы делаем это в цикле, может возникнуть проблема с производительностью:

```java
String result = "";

for (int i = 0; i < 10_000; i++) {
    result += i;
}
```

&#x20;`String` изменить нельзя, поэтому на каждом шаге создаются новые строки, а содержимое предыдущей строки приходится копировать:

```
""
↓
"0"
↓
"01"
↓
"012"
↓
"0123"
↓
...
```

При большом количестве операций это неэффективно. Для таких случаев существует `StringBuilder`.

***

## `StringBuilder`

> `StringBuilder` — изменяемый буфер для построения строки.

```java
StringBuilder builder = new StringBuilder();

builder.append("Hello");
builder.append(" ");
builder.append("World");
```

Здесь не создаётся новый `StringBuilder` после каждого `append()`. Меняется содержимое **того же объекта**. Можно после всех преобразований получить из него обычный `String`:

```java
String result = builder.toString();
System.out.println(result); // Hello World
```

***

### Основные методы `StringBuilder`

* Добавить в конец — `builder.append("!")` → `"Hello!"`
* Вставить по индексу `0` — `builder.insert(0, "Start: ")` → `"Start: Hello"`
* Удалить символы с индекса `0` (включительно) до `5` (не включительно) — `builder.delete(0, 5)` → удалится `"Hello"`
* Удалить символ с индексом `0` — `builder.deleteCharAt(0)` → `"ello"`
* Заменить символы с `0` (включительно) до `5` (не включительно) — `builder.replace(0, 5, "Hi")` → `"Hi"`
* Заменить символ с индексом `0` — `builder.setCharAt(0, 'A')` → `"Aello"`
* Развернуть — `builder.reverse()` → `"olleH"`
* Получить обычный `String` — `String result = builder.toString()`

***

### `StringBuilder` нужен не везде

Для простых случаев нормально:

```java
String fullName = firstName + " " + lastName;
```

Не нужно писать:

```java
String fullName = new StringBuilder()
        .append(firstName)
        .append(" ")
        .append(lastName)
        .toString();
```

ради одной простой конкатенации. Компилятор и JVM умеют эффективно обрабатывать простые строковые выражения. `StringBuilder` полезен именно когда строка **постепенно строится большим количеством операций**, например в цикле.

***

Методы вроде `append()` возвращают сам `StringBuilder`. Поэтому вместо:

```java
builder.append("Hello");
builder.append(" ");
builder.append(name);
builder.append("!");
```

можно написать:

```java
builder
        .append("Hello")
        .append(" ")
        .append(name)
        .append("!");
```

***

### `length` и `capacity`

У `StringBuilder` есть:

* `length` — сколько символов сейчас хранится
* `capacity` — сколько символов всего внутренний буфер может вместить без расширения

Чтобы не расширять буфер при каждом добавлении элементов, он обычно создается с запасом. Например, изначально создался буфер на 10 элементов (`capacity = 10`, `length = 0`), сохранили `"hello"` (`capacity = 10`, `length = 5`), сохранили `"abcdef"` (`capacity = 10`, `length = 11` ⇒ место кончилось ⇒ `capacity` увеличивается с запасом, например `capacity = 50`, `length = 11` ⇒ продолжаем работу).&#x20;

```java
StringBuilder builder = new StringBuilder();

builder.append("Hello");

System.out.println(builder.length());   // 5
System.out.println(builder.capacity()); // обычно больше 5
```

`StringBuilder` автоматически увеличивает внутренний буфер при необходимости. Обычно разработчику об этом заботиться не нужно. Но если заранее известно, что строка будет большой, можно сразу задать приблизительную необходимую ёмкость:

```java
StringBuilder builder = new StringBuilder(10_000);
```

&#x20;Так `StringBuilder` не надо будет делать лишние расширения в процессе.

***

## `StringBuffer`

`StringBuffer` очень похож на `StringBuilder`. Например:

```java
StringBuffer buffer = new StringBuffer();

buffer.append("Hello");
buffer.append(" ");
buffer.append("World");
```

У него практически те же основные методы:

```java
append()
insert()
delete()
replace()
reverse()
toString()
```

Главное отличие — **потокобезопасность**. `StringBuilder` **не потокобезопасен**. Представим, что один объект одновременно изменяют два потока:

```java
StringBuilder builder = new StringBuilder();
```

Один выполняет:

```java
builder.append("AAA");
```

а другой одновременно:

```java
builder.append("BBB");
```

При совместном изменении одного объекта из нескольких потоков результат может оказаться некорректным или непредсказуемым (например, второй поток может затереть результат первого, и получим `"BBB"` вместо ожидаемого `"AAABBB"`).

`StringBuffer` защищён от одновременного выполнения несколькими потоками. Поэтому один объект:

```java
StringBuffer buffer = new StringBuffer();
```

можно безопаснее использовать из нескольких потоков. Но из-за этого `StringBuffer` должен выполнять дополнительную синхронизацию при работе. Поэтому выбираем то, что нам важнее: если безопасность при одновременном изменении из разных потоков — `StringBuffer`, если скорость — `StringBuilder`.

***

## Сравнение строк

Для объектов в Java есть два разных способа сравнения:

* `==` — проверяет, ссылаются ли переменные на **один и тот же объект в памяти**
* `.equals()` — может сравнивать **содержимое объектов**, если класс переопределил этот метод. По умолчанию работает так же, как `==`

Для `String` метод `.equals()` переопределён и сравнивает именно текст.

Например:

```java
String first = new String("Java");
String second = new String("Java");
```

Здесь созданы **два разных объекта**. Поэтому:

```java
System.out.println(first == second);      // false
System.out.println(first.equals(second)); // true
```

`==` возвращает `false`, потому что объекты разные.

`.equals()` возвращает `true`, потому что текст внутри них одинаковый.

***

#### Почему нельзя сравнивать строки через `==`

Например:

```java
String first = "Java";
String second = "Java";

System.out.println(first == second); // true
```

Может показаться, что `==` нормально сравнивает строки. Но здесь `true` получилось только потому, что одинаковые строковые литералы могут ссылаться на один объект из **String Pool** (об этом позже). Если изменить создание второй строки:

```java
String first = "Java";
String second = new String("Java");
```

получим:

```java
System.out.println(first == second);      // false
System.out.println(first.equals(second)); // true
```

Текст остался одинаковым, но объекты разные. Поэтому на всякий случай **для сравнения содержимого строк используем `.equals()`, а не `==`**.

***

### Что если строка может быть `null`?

```java
String status = null;
status.equals("PAID");
```

Получим `NullPointerException`, потому что пытаемся вызвать метод у `null`. Поэтому иногда пишут наоборот:

```java
"PAID".equals(status);
```

Если `status == null`, просто будет `false`.

Ещё один вариант:

```java
Objects.equals(status, "PAID");
```

`Objects.equals()` тоже безопасно работает с `null`.

```java
Objects.equals(null, null);     // true
Objects.equals(null, "PAID");   // false
Objects.equals("PAID", "PAID"); // true
```

***

### Сравнение `StringBuilder`

У `StringBuilder` ситуация другая.

```java
StringBuilder first = new StringBuilder("Java");
StringBuilder second = new StringBuilder("Java");
```

Содержимое одинаковое, но:

```java
System.out.println(first.equals(second)); // false
```

`StringBuilder` **не переопределяет `.equals()` для сравнения текста**. Он использует стандартное поведение `equals()` из `Object`, которое фактически проверяет, один ли это объект в памяти. Поэтому:

```java
first == second          // false
first.equals(second)     // false
```

`true` получим только если обе переменные действительно ссылаются на **один объект**:

```java
StringBuilder first = new StringBuilder("Java");
StringBuilder second = first;
```

```java
first == second      // true
first.equals(second) // true
```

***

#### Как тогда сравнить содержимое `StringBuilder`?

Один простой способ — сначала преобразовать в `String`:

```java
StringBuilder first = new StringBuilder("Java");
StringBuilder second = new StringBuilder("Java");

System.out.println(
        first.toString().equals(second.toString())
); // true
```

Теперь сравниваются уже два объекта `String`, а их `.equals()` сравнивает текст.

То же самое относится к `StringBuffer`.

***

## Пул строк

**Литерал** — это значение, которое прямо записано в коде. Например:

{% code overflow="wrap" %}
```java
String name = "Java"; // "Java" — строковый литерал
int age = 20;         // 20 — числовой литерал
boolean active = true; // true — логический литерал
char letter = 'A';    // 'A' — символьный литерал
```
{% endcode %}

**String Pool — пул строк** — специальное место в памяти Java, где хранятся строковые литералы. Его цель — **не создавать много одинаковых объектов `String`, если можно переиспользовать уже существующий**.

Например:

```java
String first = "Java";
String second = "Java";
```

Когда JVM встречает строковый литерал `"Java"`, она проверяет пул строк:

* если `"Java"` там ещё нет — строка добавляется в пул;
* если уже есть — используется существующий объект.

Поэтому обе переменные могут ссылаться на один и тот же объект в памяти:

```
String Pool

"Java"
  ↑
  ├── first
  └── second
```

И поэтому:

```java
System.out.println(first == second); // true
```

Здесь `==` возвращает `true` не потому, что сравнил текст, а потому что `first` и `second` ссылаются на **один объект из String Pool**.

***

### `new String(...)`

`new` явно создаёт **новый объект**.

```java
String first = "Java"; // "Java" из пула
String second = new String("Java"); // новый String "Java"
```

Поэтому:

```java
first == second // false
```

Это разные объекты.

Но:

```java
first.equals(second) // true
```

потому что содержимое одинаковое.

***

### Конкатенация и String Pool

Например:

```java
String first = "Java";
String second = "Ja" + "va";
```

Получим:

```java
first == second // true
```

Почему? Выражение `"Ja" + "va"` состоит только из констант, поэтому Java может вычислить его **ещё до запуска программы, во время компиляции исходного кода**: `"Ja" + "va" = "Java"`. В итоге используется строка из пула.

Другой пример:

```java
String part = "Ja";

String first = "Java";
String second = part + "va";
```

Теперь:

```java
first == second // false
```

`part` — переменная, поэтому значение конкатенации формируется уже во время выполнения программы. Получается новый объект `String`, а не просто ссылка на уже существующий литерал `"Java"` из пула.

При этом:

```java
first.equals(second) // true
```

***

### `String.intern()`

> `intern()` позволяет получить **строку из String Pool с таким же текстом**.

Например:

```java
String first = "Java";
String second = new String("Java");
```

`first` ссылается на строку `"Java"` из String Pool. А `second` — на отдельный объект, потому что мы явно использовали `new`.

```java
first == second // false
```

Текст одинаковый, но это два разных объекта.

Теперь вызовем:

```java
String third = second.intern();
```

`intern()` смотрит, есть ли в String Pool строка с текстом `"Java"`. Она уже есть — на неё ссылается `first`. Поэтому `intern()` возвращает **ссылку именно на этот объект из пула**.

Теперь:

```java
first == second // false
first == third  // true
```

&#x20;`intern()` **не перемещает `second` в пул и не изменяет его**. Он возвращает другую ссылку — на подходящую строку из String Pool. Если такой строки в пуле ещё нет, `intern()` **добавит её туда и вернёт ссылку на неё**.

Для обычного кода вручную использовать `intern()` почти никогда не нужно. Главное понимать, как он связан со String Pool и почему после него `==` может вернуть `true` — это частый вопрос на собеседованиях.

***

### Строковые литералы уже интернированы

Например:

```java
String first = "Java";
String second = "Java";
```

дополнительно писать:

```java
"Java".intern();
```

бессмысленно. Литерал `"Java"` и так уже использует String Pool.

***

### `StringBuilder.toString()` и String Pool

Например:

```java
StringBuilder builder = new StringBuilder();

builder.append("Ja");
builder.append("va");

String value = builder.toString();
```

`toString()` создаёт обычный объект `String`. Он не обязан автоматически становиться ссылкой на литерал `"Java"` из пула. Поэтому:

```java
String literal = "Java";
literal == value // false
```

Но:

```java
literal.equals(value) // true
```

А после:

```java
String interned = value.intern();
```

получим:

```java
literal == interned // true
```
