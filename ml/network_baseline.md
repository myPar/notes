### Схема бэйзлайна

#### Подготовка данных

__Загрузка датасета__
Средствами библиотеки __pandas__ происходит загрузка датасета в датафрейм. Затем происходит выборка требуемых признаков. Заранее имеется список входных и выходных признаков.  

__Дополнение датасета__

В датасете могут иметься пропуски. При загрузки датасета в датафрейм pandas происходит автоматическое заполнение пропусков значениями __nan__, чтобы их идентифицировать нельзя сравнивать эти значения с nan, как с типом данных по следующей причине:

``` python
>>> arr = np.array([1,2,3,np.nan])
>>> arr
array([ 1.,  2.,  3., nan])
>>> arr[3] == np.nan
False
>>>
```

Вместо этого можно сделать так:

```python
>>> arr = np.array([1,2,3,np.nan])
>>> np.isnan(arr[3])
True
```

или так:
``` python
>>> arr = np.array([1,2,3,np.nan])
>>> str(arr[3]) == 'nan'
True
```

Дополнять эти пропуски можно следующим образом: столбец датафрейма преобразуется в массив numpy с соответствующим типом данных (в моём случае - это float64), затем, каждый элемент nan заполняется определённым значением. В зависимости от типа признака: категориальный, не категориальный заполнение идёт либо [модой](https://ru.wikipedia.org/wiki/%D0%9C%D0%BE%D0%B4%D0%B0_(%D1%81%D1%82%D0%B0%D1%82%D0%B8%D1%81%D1%82%D0%B8%D0%BA%D0%B0)#:~:text=%D0%9C%D0%BE%CC%81%D0%B4%D0%B0%20%E2%80%94%20%D0%B7%D0%BD%D0%B0%D1%87%D0%B5%D0%BD%D0%B8%D0%B5%20%D0%B2%D0%BE%20%D0%BC%D0%BD%D0%BE%D0%B6%D0%B5%D1%81%D1%82%D0%B2%D0%B5%20%D0%BD%D0%B0%D0%B1%D0%BB%D1%8E%D0%B4%D0%B5%D0%BD%D0%B8%D0%B9,%D0%BC%D0%BE%D0%B6%D0%BD%D0%BE%20%D1%81%D0%BA%D0%B0%D0%B7%D0%B0%D1%82%D1%8C%2C%20%D1%87%D1%82%D0%BE%20%D1%81%D0%BE%D0%B2%D0%BE%D0%BA%D1%83%D0%BF%D0%BD%D0%BE%D1%81%D1%82%D1%8C%20%D0%BC%D1%83%D0%BB%D1%8C%D1%82%D0%B8%D0%BC%D0%BE%D0%B4%D0%B0%D0%BB%D1%8C%D0%BD%D0%B0.) значений признака, либо средним значением соответственно.  

Сделать это можно следующим образом:

```python
# получаем массив данных для признака
arr = np.array(df[column].to_numpy(), dtype=np.float64)

# проверяем категориальный он или нет
if is_categorical(column):
    get_supplement_value = np.mode
else:
    get_supplement_value = np.mean 

# дополняем значения 'nan'
for i in range(len(arr)):
    if str(arr[i]) == 'nan':
        arr[i] = get_supplement_value(arr[~np.isnan(arr)])
```
Заметим, что такое кумулятивное дополнение данных не меняет ни моды, ни среднего значения признака. Это легко проверить математически.  

#### Подготовка обучающей и тестовой выборки

В зависимости от типа архитектуры нейронной сети исходные данные требуется преобразовать к тензорам различных размерностей. Поверхностно рассмотрим архитектуры различных слоёв и определим размерность тензоров для каждой из них.

__Рекуррентные слои__

RNN, LSTM, GRU. Каждый из этих слоёв обрабатывает последовательность данных, вычисляя выход основываясь не только не текущем входе но и на выходе от предыдущего элемента обрабатываемой пос-ти. 

Разберёмся детальнее данные какой размерности подаются на вход рекурентым слоям и какой размерности получается выход. Параметр ___timesteps___ соответсвует кол-ву элементов во входной последовательности, которая подаётся на вход слою, таким образом, данный параметр соответствует числу рекуррентных блоков, соединённых последовательно, где каждый следующий блок, принимает на вход элемент пос-ти и выход предыдущего блока. Параметр ___units___ соответсвует размерности вектора входной пос-ти. Поскольку матрицы весов в Keras API являются квадратными, то параметр units соответствует, также, размерности вектора выходной пос-ти.  Параметр batch_size, указывающийся в ф-ции тренировки сети __не является__ параметром какого либо слоя, он лишь указывает какое количество элементов из тренировочного набора данных будет браться для каждого шага тренировки. По итогу, размерность входного тензора для рекуррентного слоя будет иметь вид: (batch_size, timesteps, units). Выходной тензор имеет аналогичный вид.  

Размерность тренировочной выборки имеет следующий формат: (N, timesteps, units), где N - суммарное число входных последовательностей. Модель самостоятельно разбивает эту выборку по батчам при тренировке. Также можно воспользоваться ф-цией __train_on_batch__ из Keras API, которая позволяет тренировать модель отдельно на одном батче. Это даёт возможность прогонять через модель батчи различного размера.  

Важно запомнить, что при тренировке (или тестировании) рекуррентной нейронной сети тренировачная выборка (и входная и выходная) всегда трёхмерная. Т.е. размерность выборки всегда имеет вид (N, timesteps, units). Формирование тренировочной и тестовой выборок осуществляется исходя из этого факта.  

Стоит отметить, что значение _timesteps_ или, другими словами, размера входной пос-ти должно быть __одинаковым__ в пределах __одного батча__!

По формату входа и выхода рекуррентный слой может иметь следующие типы:

* one-to-one

Слой состоит из одного рекуррентного блока. Таким образом размерность его входного тензора (batch_size, 1, input_vector_size)