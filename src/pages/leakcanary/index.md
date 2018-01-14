---
title: "LeakCanary"
date: "2016-07-30T12:12:03.284Z"
tags: ["android", "memory leaks", "leak canary", "square"]
categories: ["android library"]
---

Полезная библиотека от [Square](http://square.github.io/) для поиска утечек памяти в андроид приложении.
Подключаем зависимости в build.gradle

```groovy
dependencies {
   debugCompile 'com.squareup.leakcanary:leakcanary-android:1.4-beta2'
   releaseCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2'
   testCompile 'com.squareup.leakcanary:leakcanary-android-no-op:1.4-beta2'
 }
```

добавляем инициализацию в Application класс приложения

```java
public class ExampleApp extends Application {

  @Override public void onCreate() {
    super.onCreate();
    LeakCanary.install(this);
  }
}
```

и все готово. Этого достаточно для базовой настройки библиотеки. LeakCanary при запуске приложения будет следить за возможными утечками activity (по умолчанию) и предупреждать о них. Изображение взято с [github](https://github.com/square/leakcanary) авторов библиотеки
![Внешний вид](https://github.com/square/leakcanary/blob/master/assets/screenshot.png?raw=true)

Также библиотека позволяет отслеживать любые объекты вашего приложения на возможные утечки (например fragment-ы). Для этого необходимо дописать код Application класса:

```java
public class ExampleApp extends Application {
  private RefWatcher refWatcher;

  public static RefWatcher getRefWatcher(Context context) {
    ExampleApp app = (ExampleApp) context.getApplicationContext();
    return app.refWatcher;
  }

  @Override public void onCreate() {
    super.onCreate();
    refWatcher = LeakCanary.install(this);
  }
}
```

затем в fragment-е добавить следующее:

```java
public class ExampleFragment extends Fragment {
    ...
  @Override public void onDestroy() {
    super.onDestroy();
    Context context = getContext();
    if (context != null) {
      ExampleApp.getRefWatcher(context).watch(this);
    }
  }
    ...
}
```
