# Двойное подключение
Следующий фрагмент кода на языке Си демонстрирует потенциальные проблемы, которые могут возникнуть, если пропустить #include guards:
```C++
Файл grandfather.h
 struct foo {
     int member;
 };
Файл father.h
 #include "grandfather.h"
 
Файл child.c
 #include "grandfather.h"
 #include "father.h"
Здесь к файлу «child.c» напрямую подключаются две копии заголовочного файла «grandfather.h». Это может вызвать ошибку компиляции, так как структура типа foo явным образом определяется дважды.

Применение #include guards
Файл grandfather.h
 #pragma once
 #ifndef H_GRANDFATHER
 #define H_GRANDFATHER

 struct foo
 {
     int member;
 };

 #endif
Файл father.h
 #include "grandfather.h"
Файл child.c
 #include "grandfather.h"
 #include "father.h"
 ```
В данном примере первое включение файла «grandfather.h» делает идентификатор макроса H_GRANDFATHER определённым. Далее, когда к «child.c» подключается «grandfather.h» второй раз, проверка этого идентификатора на неопределенность директивой #ifndef не проходит, и препроцессор пропускает всё вплоть до директивы #endif, таким образом избегая второго определения struct foo. В результате программа компилируется корректно. <br>

## pragma once 
Привеняется для тех- же целей

Источники: <br> 
https://ru.wikipedia.org/wiki/Include_guard <br> 
https://www.youtube.com/watch?v=ZwaE-JM7smI&ab_channel=%23SimpleCode
