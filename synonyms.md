# Синонимы
## Синонимы типов
Одна из возможностей языка C, делающая его столь мощным, - это возможность создавать собственные типы. Эти типы базируются на нескольких фундаментальных, таких как int, char, float и другие. Но, используя указатели, массивы и структуры, из этих простых типов можно получать достаточно сложные. Правда, в этом есть одно неудобство. Рассмотрим пример:
```c++
 #include <windows.h>
 #include <stdio.h>

 void thread (void *pInt)
 {
  // Печать параметра
  printf ("%d\n", *((int*) pInt));
 }
```
```c++
 int main (void)
 {
  HANDLE hLib;
  // О том, что такое HANDLE, я расскажу позже, пока что
  // считайте его void*

  int i = 5;
  int (*BeginThread)(void (*)(void*), void*);

  hLib = LoadLibrary ("MYLIB.DLL");
  BeginThread = (int (*)(void (*)(void*), void))
   GetProcAddress (hLib, "MyBeginThread");
  (*BeginThread) (thread, &i);

  FreeLibrary (hLib);

  return 0;
 }
```
В данном случае мы загружаем библиотеку в память, выделяем из нее функцию и вызвываем ее. Но функция этого примера в данном случае не важна. Важно другое: даже в таком коротком участке кода дважды встретилось достаточно громоздкое определение типа int (*)(void (*)(void*), void). А в большой программе определения типов таких размеров встретятся десятки раз. Чтобы избавить программиста от необходимости повторять такие громоздкие описания каждый раз, в С введены синонимы типов.

Что такое синоним типа? Это просто лишь его альтернативное имя. Тип и его синоним полностью идентичны, можно пользоваться как одним, так и другим. У типа может быть сколько угодно синонимов. Описываются синонимы как переменные, с той разницей, что в начало описания добавляется ключевое слово typedef :
 typedef unsigned long ULONG, DWORD;
В данном примере объявляются два новых типа: ULONG и DWORD, которые являются синонимами unsigned long. Теперь мы можем писать так:
 unsigned long a;
 ULONG b;
 DWORD c;
 // Все три переменные одного и того же типа
А теперь начинается самое интересное. Как я говорил, описание синонимов сходно с описанием переменных. То есть мы имеем полное право написать так:
 typedef unsigned long DWORD, *LPDWORD, DWORD_ARRAY [10];
Теперь мы объявили тип DWORD, являющийся синонимом unsigned long, тип LPDWORD, являющийся синонимом указателя на unsigned long, и тип DWORD_ARRAY, являющийся синонимом массива из 10 значений типа unsigned long.

Переменные, объявленные с помощью синонима типа, ничем не отлючаются от обычных. С ними можно так же оперировать:
```c++
 #include <stdio.h>
 #include <stdlib.h>
 typedef int *PINT;

 int main (void)
 {
  PINT p;

  p = (PINT) malloc (sizeof (int));
  *p = 5;
  printf ("%d\n", *p);
  free (p);

  return 0;
 }
```
Теперь мы можем переписать пример из начала следующим образом:
```c++
 #include <windows.h>
 #include <stdio.h>

 void thread (void *pInt)
 {
  // Печать параметра
  printf ("%d\n", *((int*) pInt));
 }

 // Теперь я могу рассказать, что такое HANDLE
 typedef struct HANDLE__ { void *pUnused; } HANDLE;
 // Вот так HANDLE описан в windows.h. Почему именно так - не знаю.

 // А вот теперь мы определим наш тип
 typedef int (*BEGINTHREAD) (void (*)(void*), void*);

 int main (void)
 {
  HANDLE hLib;
  int i = 5;
  BEGINTHREAD BeginThread;

  hLib = LoadLibrary ("MYLIB.DLL");
  BeginThread = (BEGINTHREAD) GetProcAddress (hLib, "MyBeginThread");
  (*BeginThread) (thread, &i);

  FreeLibrary (hLib);

  return 0;
 }
 ```
Обратите внимание на то, что код функции main существенно упростился. Что конкретно представляет из себя тип BEGINTHREAD, вы, я думаю, в состоянии разобраться сами.

Синонимы применяются еще в одном случае. Как вы помните, объявив структуру MY_STRUCT, мы должны описывать все экземпляры этой структуры как struct MY_STRUCT MyStruct;. Если же мы напишем так:
 typedef struct __MY_STRUCT {
  int a;
  char *b;
  // Еще элементы
 } MY_STRUCT;
- то теперь объявлять экземпляры этой структуры можно так:
 MY_STRUCT MyStruct; // синоним struct __MY_STRUCT MyStruct;
- что тоже проще. Ненамного, но все же приятнее. На этом с typedef закончим.

Источник: https://subscribe.ru/archive/comp.soft.prog.c4beginers/200109/27231156.html
