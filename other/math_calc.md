Фроловская Остапенко - введение в теорию разностных схем
Самарский Буллин - введение в теорию разностных схем
$\forall z$
$ M = \begin{bmatrix} 
    -1 & 1  \\
    3 & 3   \\  
    \end{bmatrix}
$

$
M = \begin{bmatrix}
    2 & 5 \\
    4 & 5
    \end{bmatrix}
    \times
    \begin{bmatrix}
    2 & 5 \\
    4 & 5
    \end{bmatrix}
$

$\int y^2(y-1)^4\;\mathrm{d}x$

### Численные решения алгебраических уравнений

$f(x)=0$

В рамках численного метода для вычисления корней проделываем следующие шаги.

1. локализация корней. Выделяем отрезки по числу корней в каждом из которых расположен ровно один корень.
$f(a)*f(b) < 0$ - на разных концах отрезка значения функции имеют разные знаки.  

2. Для каждого такого отрезка проделываем шаги алгоритма _деления отрезка пополам_.

$c=\frac{a+b}{2}$ если $f(a)*f(c) < 0 =>$

__Вопрос 1__
Метод бисекции нахождения изолированного корня алгебраического ур-ия. Графическая иллюстрация сх-ти итерационного метода решения алгебраического уравнения.  

__Вопрос 2__
Теорема о сх-ти итерационного метода нахождения изолированного корня алгебаического ур-ия.

### Итерационные методы решения алгебрический ур-ий

$y(x) = 0 \rightarrow x = g(x) = x + \tau y(x)  \hspace{10mm} 1 << \tau > 0$ 
$\vdots$
$x_{n+1} = g(x_n) \hspace{10mm} |x_{n+1}-x_{n}| < \epsilon$

__Определения:__
* __Метрическое пр-во__ - Мн-во $M$, на котором задана метрика.
* __Метрика__ - $\rho(x,y): M^2 \rightarrow R$, обладающее следующими св-ми
    * $\rho(x,x) = 0 \hspace{5mm} \forall x$
    * $\rho(x,y) = \rho(y,x) \hspace{5mm} \forall x,y$
    * $\rho(x,y) > 0 \hspace{5mm} \forall x \ne y$
    * $\rho(x,y) + \rho(y,c) \geq \rho(x,c)$

__Теорема__



# Эл-тех

преподаватель - Константин Михайлович

Эл заряд - физзическая величина показывающая возможность тела быть источником эл-магнитного поля

$U_{ab} = \phi_a - \phi_b = \frac{A{ab}}{q} = \int E dl$

Сопротивление - показывает св-во проводников оказывать сопротивление протеканию тока.

$R = \rho \frac{l}{s}$

Ёмкость - физическая величина, характеризующая способность системы накапливать эл.заряд.

Индуктивность - величина, характеризующая магнитные св-ва контура.

### Основные понятия

* Эл-цепь - совокупность элементов соединённых между собой.

* Эл-схема - выполненное, согласно правилам, изображение эл-цепи, с помощью условных знаков, обозначающих элементы и связи между ними.

* Генератор - источник эл-энергии.

* Нагрузка - потребитель эл-энергии.

* Вольт-амперная характеристика - зависимость напряжения от тока на элементе цепи.

* Проводимость - величина, обратная сопротивлению.

* Ветвь - участок цепи, состоящий из последовательно соединённых элементов.

* Узел - точка соединения 3 и более ветвей.

* Контур - замкнутый путь в цепи, проходящий через узлы и ветви.

* N-полюсник - электрическая цепь, имеющая два вывода (концы ветвей).

#### Источники энергии

* Источник напряжения - ЭДС.

* Источник тока.

ВАХ - у идеального источника ЭДС - внутреннее сопротивления ноль, у идеального источника тока - бесконечность.

Ток ветви - отношение разности потенциалов на начальной и конечной точки ветви, с учётом алгебраического значения ЭДС в ней, и суммарного сопротивления ветви.

#### Правила Киркгоффа

* Первое праило - алгебраическая сумма токов через узел цепи равна нулю. Направленный к узлу тока принято считать положительным, а от узла - отрицательным.

* Второе правило - алгебраическая сумма падений напряжений на всех ветвях замкнутого контура равна алгебраической сумме ЭДС ветвей этого контура.

* Эквивалентные преобразования схем - изменение части эл-цепи, при котором параметры остальной части цепи не меняются.

### Переменные величины

$F(x) = F(x+T) \hspace{5mm} \forall x \hspace{1mm}$ - переодическая велчина; $T$ - период  

$u(t) = U_m sin(\omega t + \phi_u)$
$i(t) = I_m sin(\omega t + \phi_i)$

$\phi_u, \phi_i - сдвиги \hspace{1mm} по \hspace{1mm} фазе.$