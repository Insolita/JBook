## java.lang.Object#finalize
### Введение
Данный метод вызывается `GC` тогда, когда `GC` полагает, что объект более не нужен, никем не используется и его пора утилизировать.

> GC - Garbage Collector

В общем виде можно считать, что `GC` думает, что объект можно удалить, если на него не существует ссылок.
Но на самом деле все гораздо сложнее, существуют несколько стратегий поведения `GC` и все они сложнее, чем простой подсчет ссылок.

Однако, сейчас мы говорим все же про `finalize`!
И на первый взгляд, может показаться, что это отличный метод, в который хорошо поместить закрытие ресурсов и прочее, что нужно освобождать/удалять при уничтожении класса.
Но это очень обманчиво. Более того, использование `finalize` подвержено ошибкам и многие вообще считают, что использовать его - **неправильно**.

Почему?

Прежде всего потому что у нас нет **НИКАКИХ** гарантий, что этот метод вообще вызовется.

Это легко может произойти, если у нас где-то в коде есть забытая ссылка на объект - мы про ссылку ничего не знаем, но она существует и `GC` не будет удалять этот объект.
При этом существует вероятность, когда данный мтеод вообще вызван не будет - например, если наше приложение вдруг упадет или мы его остановим.


Еще одно **заблуждение**  - если мы в `finalize()` методе повесим процесс(ведь `GC` работает в фоне), то и наш `GC` перестанет работать.
Разумеется, это **не верно**.

И вот почему:

Наш `GC` не вызывает `finalize()` напрямую, а добавляет объекты в список, вызывая `java.lang.ref.Finalizer.register(Object)`.
Объект класса `java.lang.ref.Finalizer` представляет собой ссылку на объект, для которого надо вызвать `finalize()`, и хранит ссылки на следующий и предыдущий `Finalizer`, формируя двусвязный список.

Вызов `finalize()` происходит в отдельном потоке `java.lang.ref.Finalizer.FinalizerThread`, вызовы `finalize` идут последовательно так, как добавлялись. Поэтому, если `finalize` зависает - виснет именно `FinalizerThread`, если класс имеет пустой метод `finalize`, то объекты такого класса будут и дальше удаляться, если же метод не пуст - то добавляются в список и ждут, пока все не отвиснет или не упадет окончательно, либо мы вообще не завершим наше приложение.

Как выглядит `finalize()`:
```java
protected void finalize() throws Throwable { }
```

Как уже было сказано выше - в большинстве случаев не рекомендуется его переопределять и использовать в качестве очистки ресурсов и т.д.

Однако, в очень редких случаях - этот метод может помочь.
Это довольно специфичные случаи, но все же, давайте рассмотрим пример.

Например, у нас объект с `Weak/Soft References` и мы работаем с реально большим файлом. В какой-то момент нам не хватает памяти и `GC` начинает вычищать объекты по нашим ссылкам и убирает наш объект, но мы **ОБЯЗАНЫ** закрыть наш файловый дескриптор.

И вот в таком случае - переопределение метода поможет нам решить задачу с закрытием ресурсов.
Но стоит помнить, что тут мы имеем дело не со строгими ссылками и рассмотренный случай - скорее редкость.

### Заключение
* Помните, что никто не гарантирует, что произойдет вызов `finalize`.
* Старайтесь не использовать `finalize`.