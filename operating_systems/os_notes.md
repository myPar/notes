# Операционная система UNIX System V Realise 4 (SVR4)
##### Лекционный материал по устройсту операционной системы UNIX (семейства UNIX-подобных операционных систем). Выделяется и описывается то общее, что они имеют, а именно: базовый пользовательский и программный интерфейсы, назначение основных компонент, их архитектура и взаимодействие.

### хронология UNIX System V
* __UNIX System V__ - была выпущена в 1983 году.  
* __System V Release 3 (SVR3)__ - появилась в 1987 году. В этой версии был добавлен ряд дополнительных возможностей:
     * Подсистему ввода/вывода, основанную на архитектуре STREAMS.
     * Переключатель файловой системы (File System Switch), обеспечивавший одновременную поддержку различных файловых систем.
     * Разделяемые(динамические) библиотеки. Библиотеки, которые могут использоваться несколькими приложениями одновременно на стадии исполнения.
     * Программный интерфейс сетевых приложений Transport Layer Interface (TLI).
* __System V Release 4 (SVR4)__ - Была выпущена в 1989 году. Объединяла возможности нескольких версий UNIX: SunOS, BSD UNIX, и предыдущие версии System V. Новые черты системы:
     * Командные интерпретаторы Korn dhell и С shell (BSD)
     * Систему терминального ввода/вывода, основанную на STREAMS (System V)  
     * Быстрая файловую систему FFS (BSD)
     * Сетевой программный интерфейс сокетов (BSD)
     * Отображение файлов в оперативную память (SunOS)
     * Поддержку диспетчеризации реального времени
Также многие компоненты системы были поддержаны стандартами ANSI и POSIX.

### Стандартизация UNIX

UNIX является первой действительно переносимой операционной системой. В свободно распространяемые версии UNIX постоянно вносили изменения, что порождало большое кол-во версий данной системы. И чем дальше, тем больше были различия между существующими версиями. Это вело к отсутствию канонического UNIX и, как следствия усложнению переносимости приложений, написанных под ту или иную версию.

Поэтому появилась необходимость описать стандарты системы. Их наличие обеспечило бы простоту переносимости приложений. В результате возникло несколько организаций по стандартизации, которыми был разработан ряд стандартов, повлиявших на дальнейшую разработку систем UNIX.

### POSIX

Стандарт POSIX (Portable Operating System Interface for Computer Environment) описывает набор услуг, предоставляемых системой приложениям (API), в частности стандартную библиотеку языка С. Тем самым, обеспечивается переносимость приложений на уровне исходного кода.
Опишем несколько значимых стандартов POSIX:
* POSIX 1003.1 - был разработан в 1988 году. Определял API.
* POSIX 1003.2 1992 - Включает определение командного интерпретатора UNIX и набора утилит.
* POSIX Содержит дополнения, относящиеся к поддержке приложений реального времени

* POSIX 1003.1c-1995 - Включает определения "нитей" (threads) POSIX, известных также как pthreads.


### Устройство операционной системы

##### Ядро системы
Ядро обеспечивает базовую функциональность операционной системы. Оно создаёт процессы, управляет ими, распределяет память и обеспечивает доступ к файлам и переферийным устройствам.

Взаимодействие прикладных приложений с ядром происходит через ___интерфейс системных вызовов___, который реализован в виде функций стандартной библиотеки языка C. Он представляет из себя набор услуг ядра, который могут использовать приложения.

Процесс, в котором исполняется приложение, запрашивает услугу ядра посредством ___системного вызова___. Ядро выполняет данный запрос от имени процесса и возвращает ему необходимые данные.

<a href="https://ibb.co/HYw8n6T"><img src="https://i.ibb.co/FJZTg24/image.jpg" alt="image" border="0"></a>

# POSIX THREAD LIBRARY

#####Описание библиотечных функций для Solaris 10 и концепций POSIX THREAD LIBRARY

__pthread_create__  - функция создания новой нити в текущем процессе. [Официальная документация.](https://docs.oracle.com/cd/E26505_01/html/816-5168/pthread-create-3c.html#scrolltoc)  

__сигнатура:__
``` C
#include <pthread.h> 

int pthread_create(pthread_t *restrict thread,
     const pthread_attr_t *restrict attr,
     void *(*start_routine)(void*), void *restrict arg);
```
__описание работы:__
Функция создаёт новую нить в текущем процессе с атрибутами указанными параметром ___attr___. Если данный параметр равен ___NULL___ используются атрибуты по умолчанию. При последующей модификации атрибутов указанных в ___attr___ атрибуты самой нити не модифицируются.  

После создания поток исполняет функцию ___start_routine___ с параметром, указанным в ___arg___. В случае, если данная функции возвращает результат, то это имеет тот же механизм работы, что и вызов __pthread_exit()__ с возвращаемым значением в качестве статуса выхода.  

Однако, для потока, в котором исполняется _main()_ ситуация отличается:
Когда происходит возврат из функции _main()_ это эквивалентно вызову _exit()_ с возвращаемым значением _main()_ в качестве кода выхода.  

В случае успеха, создаётся новый поток, как описано выше и __pthread_create()__ возвращает 0. В противном случае, нового потока не создаётся, содержание данных, на которые указывает аргумент ___thread___ не определено. Также функция возвращает код ошибки:  

```
     EAGAIN - системе не хватает ресурсов для создания нового потока, или было превышенно 
     установленное системой максимальное кол-во потоков в процессе.

     EINVAL - значение attr некорректно.

     EPERM - у вызывающего потока нет необходимого разрешения для установки 
     необходимых параметров планирования или политики планирования.
```

__атрибуты:__
Подробнее про описание атрибутов нити.
* __scope (область действия)__ - допустимые значения:  ___PTHREAD_SCOPE_SYSTEM___ и ___PTHREAD_SCOPE_PROCESS___.
     * _PTHREAD_SCOPE_SYSTEM_ означает, что нить планируется системным планировщиком и соревнуется за системные ресурсы с другими процессами __?__. 
     * _PTHREAD_SCOPE_PROCESS_ означает, что нить планируется пользовательским планировщиком и считается частью своего роцесса. 

     В Solaris 10 все нити исполняются внутри своего процесса, поэтому установка данного атрибута не имеет практического значения.   

* __detachstate (присоединён/отсоединён)__ - Допустимые значения: __PTHREAD_CREATE_JOINABLE__ и __PTHREAD_CREATE_DETACHED__.
     * _PTHREAD_CREATE_DETACHED_ означает, что ресурсы нити будут озвобождениы сразу после её завершения.
     * _PTHREAD_CREATE_JOINABLE_ означает, что ресурсы нити будут освобождены после того, как другая нить вызовет _pthread_join()_ к ней.

* __stacksize (размер стека)__ - допустимые значения: 0 или размер стека в байтах. 0 означает, что расмер стека определяется операционой системой. В Solaris 10 на 32-битной архитектуре стек выделяется размером __1mb__, на 64-битной - __2mb__. Минимальный размер стека, который можно выделить нити, определён константой _PTHREAD_STACK_MIN_ в <pthread.h>.

* __stack_addr (адрес начала стека)__ - Допустимые значения: NULL(адрес определяется ОС) или указатель на область памяти для размещения стека нити. Не рекомендуется выделять память под стек самостоятельно, поскольку, в таком случае, система не будет освобождать эту память автоматически, это должен реализовать программист __?__.

| атрибут | значение по умолчанию | описание |
| ----| ---| ---|
| scope | PTHREAD_SCOPE_PROCESS | Нить использует процессорное время, выделяемое процессу. В Solaris 9 и последующих версиях Solaris этот параметр не имеет практического значения|
| detachstate | PTHREAD_CREATE_JOINABLE | нить создаётся присоединённой |
|stackaddr | NULL | адрес начала стека нити определяется системой | 
| stacksize | 0 | размер стека нити определяется системой | 
| priority | 0 | нить имеет приоритет 0 |
| inheritsched | PTHREAD_EXPLICIT_SCHED | Нить не наследует приоритет у родительской нити |
| schedpolicy | SCHED_OTHER | Нить использует фиксированные приоритеты, задаваемые ОС. Используется вытесняющая многозадачность (нить исполняется, пока не будет вытеснена другой нитью или не заблокируется на примитиве синхронизации) |

## Прерывание нити

Здесь описываются концепции прерывания нити определённые в POSIX thread. Прерывание позволяет потоку процесса завершить своё исполнение.

__Функции__:
| имя | описание |
|---|---|
| pthread_cancel() | прерывание исполнения потока |
| pthread_setcancelstate() | установка атрибута потока ___cancel state___ |
| pthread_setcanceltype() | установка атрибута потока ___cancel_type___ |
| pthread_testcancel() | установка ___точки прерывания___ у текущего потока|
| pthread_cleanup_push() | добавление функции обработчика прерывания |
| pthread_cleanup_pop() | изымание функции обработчика прерывания |

* __pthread_cancel()__ - данная функция принудительно завершает нить. [Документация](https://docs.oracle.com/cd/E26505_01/html/816-5168/pthread-cancel-3c.html#scrolltoc).

__сигнатура:__

``` C
#include <pthread.h> 

int pthread_cancel(pthread_t target_thread);
```

В зависимости от св-в нити и других обстоятельств она может продолжить своё исполнение на некоторое время после вызова _pthread_cancel()_ к ней. Механизм прерывания контролируется атрибутами _cancellation state_ и _cancellation type_.  

В случае успеха, функция возвращает 0, в противном случае - код ошибки.  

__Коды ошибок:__
```
ESRCH - некорректное значение идентефикатора потока - target_thread.
```
----
* __pthread_setcancelstate()__ - устанавливает значение атрибута _cancellation state_ делает это _атомарно_. Это значение определяет разрешено ли прерывание нити в принцыпе. [Документация](https://docs.oracle.com/cd/E26505_01/html/816-5168/pthread-setcancelstate-3c.html#scrolltoc)  

__сигнатура:__
``` C
#include <pthread.h>

int pthread_setcancelstate(int state, int *oldstate);
```
Параметр _state_ - значение, которое будет установлено в атрибут _cancellation state_, параметр _old state_ служит для получения старого значения _cancellation state_. Если _old state_ не равен NULL, то в область памяти, на которую указывает _old state_ будет помещено предыдущее значение _cancellation state_.

Параметр _state_ может принимать следующие значения:

```
PTHREAD_CANCEL_ENABLE - прерывание нити разрешено. (По-умолчанию)

PTHREAAD_CANCEL_DISABLE - прерывание нити запрещено.
```
__Возвращаемые значения:__

В случае успеха, функция _pthread_setcancelstate()_ вернёт 0. В противном случае - код ошибки.

__Коды ошибок:__

```
EINVAL - значение state не является одним из предложенных - PTHREAD_CANCEL_ENABLE, PTHREAAD_CANCEL_DISABLE.  
```
---
* __pthread_setcanceltype()__ - установка атрибута потока  _cancellation type_, который определяет в каких точках программы поток может быть прерван.  

__сигнатура:__

``` C
#include <pthread.h>

int pthread_setcanceltype(int type, int *oldtype);
```

Параметры _type_ и _oldtype_ - значение, которое будет установлено в атрибут _cancelation type_ и ссылка на область памяти, в которую будет помещено старое значение _cancelation type_ соответсвенно. Всё аналогично ф-ции _pthread_setcancelstate()_.

Параметр _type_ может принимать следующие значения:
```
PTHREAD_CANCEL_DEFERRED - когда прерывание нити разрешено (cancelation state=PTHREAD_CANCEL_ENABLE) нить получает сообщение о 
прерывании и, собственно, ожидает прерывания по достижении точки отмены (cancelation point). Если прерывание запрещено, 
то все сообщения о прерывании остаются в ожидании.
(По-умолчанию)

PTHREAD_CANCEL_ASYNCHRONOUS - если прерывание нити разрешено, то оно происходит как можно скорее. Если прерывание запрещено,
то сообщения о прерывании остаются в ожидании. Когда прерывание вновь станет разрешено, все накопренные запросы на отмену
выполнятся немедленно.
```

__Возвращаемые значения:__

В случае успеха, функция _pthread_setcanceltype()_ вернёт 0. В противном случае - код ошибки.

__Коды ошибок:__

```
EINVAL - значение type не является одним из предложенных - PTHREAD_CANCEL_DEFERRED, PTHREAD_CANCEL_ASYNCHRONOUS.  
```

Для упрощения понимания контроля прерывания, ниже приведена таблица где каждой паре значений _cancelation type_ и _cancelation state_ соответствует описание того, как происходит прерыание нити:

|Cancelation Type \ Cancelation State|PTHREAD_CANCEL_ENABLE|PTHREAAD_CANCEL_DISABLE|
|--|--|--|
|__PTHREAD_CANCEL_DEFERRED__|Прерывание происходит, когда нить ожидает прерывания по достижении точки отмены. (По-умолчанию)|Все запросы на прерывание нити остаются в ожидании.|
|__PTHREAD_CANCEL_ASYNCHRONOUS__|Вызов _pthread_cancel()_ осуществляет прерывание нити, как можно скорее.|Все запросы на прерывание остаются в ожидании. Когда прерывание снова будет доступно, все ожидающие запросы выполнятся немедленно.|

#### Точки отмены

Операционной системой определены так называемые _точки отмены_ (_cancelation points_), по достижении которых, может происходить прерывание нити. Это некоторые библиотечные ф-ции и системные вызовы, список которых можно посмотреть [здесь](https://docs.oracle.com/cd/E26505_01/html/816-5175/cancellation-5.html#REFMAN5cancellation-5).  

Также, программист может указать точки прерывания явно, посредством вызова ф-ции _pthread_test_cancel()_.

* __pthread_testcancel()__ - устанавливает явную точку отмены.

__сигнатура:__
``` C
#include <pthread.h>

void pthread_testcancel(void);
```

__Возвращаемые значения:__

Данная ф-ция возвращает _void_.

__Коды ошибок:__

Не возвращает ошибок.

#### Обработчики прерывания

При завершении нити может возникнуть необходимость освободить некоторые ресурсы, вернуть данные в соглассованное состояние или провести какие-то другие манипуляции, которые необходимы именно перед завершением нити.

Для таких целей можно добавить _обработчики прерывания_. Функции, которые будут вызваны нитью, после получения сообщения о прерывании. Данные обработчики, по сути, являются обычными функциями, объединённых в стековую структуру, над которой можно производить операции добавления и извлечения.  

Обработчики прерывания необходимо добавлять до точек отмены. В случае асинхронного прерывания - до момента, где это может произойти.  

* __pthread_cleanup_push()__ - добавляет функцию обработки прерывания в стек (имеется в виду не стек процесса, а отдельная структура для каждой нити) вызывающей нити.

__сигнатура:__
``` C
#include <pthread.h>

void pthread_cleanup_push(void (*handler) (void *), void *arg);
````

Аргумент _handler_ - это, собственно, вызываемая функция-обработчик, _arg_ - её аргумент.  

Когда поток завершается или прерывается и стек обработчиков не пуст, они извлекаются и выполняются в порядке LIFO, т.е. в порядке извлечения элементов из стека.

__Возвращаемые значения:__

Ф-ция ничего не возвращает.

__Коды ошибок:__

Не возвращает ошибок.

---

* __pthread_cleanu_pop()__ - извлекает ф-цию обработчик с вершины стека и, в зависимости от аргумента, исполняет её.  

__сигнатура:__

``` C
#include <pthread.h>

void pthread_cleanup_pop(int execute);
```

Если аргумент _execute_ не нулевой, то происходит извлечение ф-ции обработчика с вершины стека и её исполнение. Если _execute_ = 0, то ф-ция извлекается с вершины стека без последующего исполнения.  

__Возвращаемые значения:__

Ф-ция ничего не возвращает.  

__Коды ошибок:__

Не возвращает ошибок.  

Ф-ции _pthread_cleanup_push()_ и _pthread_cleanup_pop()_ реализованы в виде макрокоманд. Вызов _pthread_cleanup_push()_ должен сопровождаться парным вызовом _pthread_cleanup_pop()_ в одной и той же лексической области:

``` C
void *print_text() {
  pthread_cleanup_push(cleanup_handler, NULL);  // push cleanup routine to the stack

  while (1) {
    pthread_testcancel();
    printf("%s\n", printing_text);
    usleep(PRINT_DELTA_TIME);
  }
  pthread_cleanup_pop(1);

  return NULL;
}
```

Это связано с тем, что макрос _pthread_cleanup_push()_ начинается токеном '{', а _pthread_cleanup_pop()_ завершается токеном '}', поэтому, попытка вызвать любую из этих команд без соответсвующей пары приведёт к ошибке компиляции.

#### Безопасность прерывания


