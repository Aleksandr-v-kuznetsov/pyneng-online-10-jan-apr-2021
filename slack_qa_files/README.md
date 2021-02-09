# Вопросы и ответы из slack

[Вопросы с предыдущего курса](https://github.com/pyneng/pyneng-online-9-may-aug-2020/tree/master/slack_qa_files)

## Вопрос: в чем разница писать result=6 или result = 6?
 
В том что в Python так принято писать

```python
result = 5
```

а не так
```python
result=5
```

Разницы в работе кода нет, но в Python очень много внимания уделяется таким мелочам. Есть документ,
который описывает рекомендации по стилю написания кода - PEP 8: https://pep8.org/
Он описывает общие рекомендации как лучше писать те или иные выражения. Если соблюдать эти вещи,
вы привыкнете к тому как в Python принято писать код и читать чужой код будет намного проще,
потому что все в целом соблюдают эти рекомендации.


## Почему нельзя создать переменную так `print(str3 = str1.replace("Fast", "Gigabit"))`

Синтаксис name = "value" используется в python в двух местах:

1. При создании переменной

```python
a = 5
str1 = "FastEthernet0"
```

2. при передаче аргументов функции

```python
In [18]: print(1, 2, 3)
1 2 3

In [20]: print(1, 2, 3, sep="|")
1|2|3
```

выше sep - это параметр функции print. который указывает какой разделитель ставить между элементами.

Вызов

```python
print(str3 = str1.replace("Fast", "Gigabit"))
```

это НЕ создание переменной str3. Так как str3=...  написано внутри вызова функции,
это воспринимается как попытка указать параметр str3 функции print. А у print такого параметра нет, поэтому ошибка.

В целом, переменные всегда создаются отдельной операцией, никогда внутри вызова какой-то функции.


Если переменная str3 нужна, надо сделать так

```python
str1 = "FastEthernet0"
str3 = str1.replace("Fast", "Gigabit")
print(str3)
```

если не нужна, так
```python
str1 = "FastEthernet0"
print(str1.replace("Fast", "Gigabit"))
```

## Можно ли как-то при конвертации словаря/списка в строку с помощью функции str разбивать вывод на несколько строк

> В каких-то случаях может быть достаточно вывести словарь с помощью [pprint](https://pyneng.readthedocs.io/ru/latest/book/12_useful_modules/pprint.html).

Можно ли как-то при конвертации словаря/списка в строку с помощью функции str разбивать на
несколько строк? И чтобы не выводил скобки, запятые, а разнести по строкам

```python
In [1]: dict1 = {"a": "a", "b": "b"}

In [2]: dict1
Out[2]: {'a': 'a', 'b': 'b'}

In [3]: str(dict1)
Out[3]: "{'a': 'a', 'b': 'b'}"
```

А надо

```python
a: a
b: b
```

### ответ

Строковые представления для словарей/списков и тп нужны чтобы было отображение, например,
в print или при подстановке словаря при форматировании строк.
`str(dict1)` соответственно показывает как словарь выглядит в этом случае.

когда надо что-то такое

```
a: a
b: b
```

словарь перебирается в цикле и подставляются ключи и значения с помощью форматироавния строк в нужном виде

```python
In [5]: for key, value in dict1.items():
   ...:     print(f"{key}: {value}")
   ...:
a: a
b: b
```

Если это что-то что нужно часто, делается функция, которая делает такое преобразование для любого словаря.
Так как задач такого рода может быть бесконечно кол-во, их нет в готовом виде в Python, потому что их очень легко написать самостоятельно.

> Понятно что в начале курса это не очень легко, но буквально через месяц это уже будет легко сделать.



## Почему список/словарь в который собираются данные в функции, надо создавать внутри функции

Очень часто в решении заданий встречается такой нюанс: функция должна собрать какие-то данные в список/словарь
и список создан вне функции. Тогда вроде как функция работает правильно,
 но при этом тест не проходит. Это происходит потому что в таком варианте функция
 работает неправильно и каждый вызов добавляет элементы в тот же список:

```python
In [1]: result = []

In [2]: def func(items):
   ...:     for i in items:
   ...:         result.append(i*100)
   ...:     return result
   ...:

In [3]: func([1, 2, 3])
Out[3]: [100, 200, 300]

In [4]: func([7, 8])
Out[4]: [100, 200, 300, 700, 800]
```

Исправить это можно переносом списка в функцию

```python
In [20]: def func(items):
    ...:     result = []
    ...:     for i in items:
    ...:         result.append(i*100)
    ...:     return result
    ...:

In [21]: func([1, 2, 3])
Out[21]: [100, 200, 300]

In [22]: func([7, 8])
Out[22]: [700, 800]
```

Всё, что относится к функции лучше всегда писать внутри функции.
Тест тут не проходит потому что внутри файла задания функция вызывается первый раз - всё ок, а потом тест вызывает её второй раз и там вдруг в два раза больше данных чем нужно.


## a == b или a is b

## Есть ли какие-то рекомендации по поводу расположения функций в коде?

В [PEP8](https://pep8.org/) нет рекомендаций по этому поводу.

Если скрипт в одном файле, обычно порядок такой:
1. shebang, file encoding
2. docstring модуля
3. импорт
4. константы
5. все функции в условно произвольном порядке, то есть тут уже самому надо решить как удобнее 
6. функции/код для создания CLI если есть
7. Часто, если есть код который надо писать глобально, а не в функции, создают функцию main
8. `if __name__ == "__main__":` и вызов функции main или глобального кода, который вызывает функции


При этом среди функций обычно выбирают для себя какой-то порядок, чтобы он был плюс-минус однотипным
в разных файлах. Например, сначала пишем общие функцие, которые не зависят от других функций в файле,
потом те что зависят. При этом обычно есть какой-то порядок выполнения действий: подключились на оборудование
и считали вывод, парсим его, записали результат в файл - тогда соблюдаем этот порядок в функциях.

> [О структуре больших проектов](https://docs.python-guide.org/writing/structure/). И еще одна ссылка по этой же теме, с [примерами структуры проектов Flask/Django](https://realpython.com/python-application-layouts/).


## Что проверяет isinstance

isinstance Это функция, которая похожа на type. Только, например, с type  можно проверить только то что объект имеет такой-то тип (является экземпляром конкретного класса), а с isinstance проверяется не только конкретный класс, но и те классы, которые наследовались.
Например, с type мы можем проверить, что тип a это класс A
```python
In [2]: class B:
   ...:     pass
   ...:

In [3]: class A(B):
   ...:     pass
   ...:

In [4]: a = A()

In [5]: type(a)
Out[5]: __main__.A

In [7]: type(a) == A
Out[7]: True
```

Не можем проверить, что он также является экземпляром класса B из-за наследования
```python
In [8]: type(a) == B
Out[8]: False
```

isinstance умеет это проверять
```python
In [9]: isinstance(a, B)
Out[9]: True

In [10]: isinstance(a, A)
Out[10]: True
```

Поэтому с помощью isinstance можно проверять такие вещи
```python
In [11]: from collections.abc import Iterable, Iterator

In [12]: a = [1, 2, 3]

In [13]: isinstance(a, Iterable)
Out[13]: True

In [15]: result = map(str.lower, ["A", "B"])

In [16]: result
Out[16]: <map at 0xb3580e2c>

In [17]: isinstance(result, Iterator)
Out[17]: True
```
