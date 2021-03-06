---
layout:     post
title:      "Установка XGBoost на Windows"
date:       2016-03-20 00:00:00 +0200
author:     "Vasiliy Zemlyanov"
header-img: "img/post-bg-07.jpg"
---

*`XGBoost`* - это *очень* эффективная библиотека, которая реализует алгоритмы адаптивного бустинга. Если вам понадобилось ее установить, то я предполагаю что вы знаете что это и зачем нужно. 

Но, перед началом использования, пользователей Windows ожидает нетривиальный квест с установкой данной библиотеки. Об этом и пойдет речь в данной заметке.

Нижеописанное представляет собой микс из [официальной документации](https://xgboost.readthedocs.org/en/latest/build.html#building-on-windows/ "XGBoost official documentation installation.") и различных ответов на Stack Oveflow и Kaggle, с помощью которого мне удалось установить `x64` пакет `Python` на `Windows 8.1`.

Если у вас есть возможность использовать XGBoost под `*nix` системой - так и сделайте. Весь процесс установки для *unix: `pip install xgboost`.

## Пошаговая инструкция для Windows

1\. Устанавливаем [mingw64](https://sourceforge.net/projects/mingw-w64/ "mingw64 installation link."). 

Путь установки не должен содержать кириллицы и желательно избежать пробелов. При установке выбираем:

<pre>
Architecture: x86_64
Threads: win32
</pre>

2\. Добавляем папку `...\mingw64\bin` в переменные среды.

3\. Находим в папке `...\mingw64\bin` файл `mingw32-make.exe`, копируем и переименовываем в `make.exe`.

4\. Устанавливаем
[Cygwin-x86_64](https://cygwin.com/install.html "Cygwin-x86_64 installation link.").

5\. Если по какой-то причине не установлен [Git](https://git-for-windows.github.io "git for windows installation link."), устанавливаем и добавляем в переменные среды.

6\. Переходим непосредственно к процессу установки. Вместо обычного терминала открываем `Cygwin64` и выполняем:

<pre>
cd c:\
>
git clone --recursive https://github.com/dmlc/xgboost
cd xgboost
git submodule init
git submodule update
cp make/mingw64.mk ./config.mk
</pre>

Если необходимо внести какие-то изменения в конфигурацию, это можно сделать в файле `config.mk`.
Остается одна команда:

<pre>
make -j4
</pre>

Возможно, у вас она выполнится нормально и в папке `C:\xgboost` появится файл `xgboost.exe`. Значит билд выполнен успешно.

У меня билд падал с ошибкой `g++: error: unrecognized option '-pthread'`.

Лечится эта ошибка изменением аргумента `-pthread` на `-lpthread` в `42` строке файла `C:\xgboost\Makefile`.
После этого наконец должен появиться файл `xgboost.exe`, это значит что мы все сделали правильно.

7\. Осталось сделать из всего этого `python` пакет.

<pre>
cd python-package
python setup.py install
</pre>

Для выполнения этой команды могут понадобиться `distutils`, которые входят в [setuptools](https://pypi.python.org/pypi/setuptools "setuptools for windows installation link.").

8\. Тестируем

<pre>
python
>>> import xgboost
>>> xgboost.__version__
'0.4'
</pre>

*`XGBoost`* установлен. Радуемся и начинаем обучать адаптивные леса.

Дополнительно проверить надежность установки можно на официальных [примерах](https://github.com/dmlc/xgboost/tree/master/demo/guide-python "official XGBoost examples.").
