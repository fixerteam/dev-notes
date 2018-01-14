---
title: "Otto"
date: "2016-07-29T12:12:03.284Z"
tags: ["android", "event bus", "otto", "square"]
categories: ["android library"]
---

Очередная библиотека от [Square](http://square.github.io/) для удобного взаимодействия между компонентами приложения, разработанная специально для Android платформы. Реализует паттерн [Publish–subscribe](<https://ru.wikipedia.org/wiki/%D0%98%D0%B7%D0%B4%D0%B0%D1%82%D0%B5%D0%BB%D1%8C-%D0%BF%D0%BE%D0%B4%D0%BF%D0%B8%D1%81%D1%87%D0%B8%D0%BA_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)>). Android сообщество не рекомендует использовать Event Bus, так как данное решение довольно противоречиво и напоминает небезызвестную конструкцию go to. Если же вы все же решили воспользоваться такой возможностью, то рассмотрим базовую настройку Otto:
Подключаем зависимости в build.gradle

```groovy
dependencies {
   compile 'com.squareup:otto:1.3.8'
 }
```

По умолчанию Otto работает в UiThread и выбрасывает исключение `IllegalStateException("Event bus accessed from non-main thread")`. Если требуется использовать event-ы в многопоточной среде, в конструктор добавьте `ThreadEnforcer.ANY`

```java
// в случае singleton
Bus otto = new Bus();

// в случае, когда требуются несколько объектов
Bus otto = new Bus("id_event_bus");

// в случае, когда требуются multithread event-ы
Bus otto = new Bus(ThreadEnforcer.ANY);

// в случае, когда требуются разные объекты для multithread event-ов
Bus otto = new Bus(ThreadEnforcer.ANY, "id_event_bus");
```

Разработчики рекомендуют использовать singleton реализацию, либо инъектить с помощью DI (например [Dagger 2](http://google.github.io/dagger/)).
Для того, чтобы послать event, пишем следующий код:

```java
otto.post("Какой то event");
```

Для того, чтобы принять данный event, необходимо зарегистрироваться для приема event-ов:

```java
otto.register(this);
```

Раз есть возможность подписаться, то должна быть и отписка. Старайтесь не забывать об этом, чтобы не было неожиданных event-ов:

```java
otto.unregister(this);
```

Для обработки event-а вешаем аннотацию `@Subscribe` на тело метода и в качестве параметра метода указываем переменную с необходимым типом:

```java
@Subscribe public final void onStringEvent(String message) {
  Toast.makeText(this, message, Toast.LENGTH_SHORT).show();
}
```

Если в вашем проекте используется proguard (если же нет, то стоит задуматься об этом), добавьте в `proguard-project.txt`:

```markup
-keepattributes *Annotation*
-keepclassmembers class ** {
    @com.squareup.otto.Subscribe public *;
    @com.squareup.otto.Produce public *;
}
```

Полный код примера всегда можно найти на [GitHub](https://github.com/square/otto) данной библиотеки
