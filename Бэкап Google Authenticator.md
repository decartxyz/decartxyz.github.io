**Совет:** А лучше пользуйтесь опенсорсным andOTP, который имеет большой функционал и умеет бэкапы в том числе с шифрованием ключа

При переносе Google Authenticator на другую прошивку Android, либо телефон все сохранённые коды к приложениям слетают, что очень неудобно.

Во-первых необходим root. Во-вторых установленный эмулятор терминала непосредственно в самом Android.<br>

Терминал можно взять [этот](https://play.google.com/store/apps/details?id=jackpal.androidterm&hl=nl). 

Заходим в терминал, получаем права root и копируем файлик с базами Google Authenticator, например, на sdcard (путь к sdcard может отличаться, узнать его можно по `df`):

```
$ su
#
# cp /data/data/com.google.android.apps.authenticator2/databases/databases /sdcard
```

Скорее всего файлик не найдётся потому что он под защитой SELinux. Поэтому сначала выполняем:

```
# restorecon -F /data/data/com.google.android.apps.authenticator2/databases/databases
```

А потом повторяем предыдущий шаг. 

На новом телефоне ставим Google Authenticator, предварительно копируем сохранённый ранее файлик databases на sdcard, далее выполняем примерно следующее:

```
$ su
# restorecon -F /data/data/com.google.android.apps.authenticator2/databases/databases
# cp /sdcard/databases /data/data/com.google.android.apps.authenticator2/databases/databases
```

Всё, все доступы восстановлены.

Кроме этого не забываем что можно все эти действия выполнять непосредственно с компьютера. Для этого необходимо в настройках телефона в разделе для разработчиков разрешить отладку по USB, а далее выполнить на компьютере:

```
$ adb shell
```