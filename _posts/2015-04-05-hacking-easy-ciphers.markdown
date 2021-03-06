---
layout:     post
title:      "Взлом простых шифров"
date:       2015-04-05 12:00:00
author:     "Vasiliy Zemlyanov"
header-img: "img/post-bg-01.jpg"
---

# Взлом простых шифров

Нижеописанные шифры не используются на пратике. Они представляют историческую ценность и могут применяться в различных головоломках.

## Взлом шифра Цезаря

Суть [шифра Цезаря](https://ru.wikipedia.org/wiki/Шифр_Цезаря "Шифр Цезаря") очень проста. Мы просто заменяем каждый символ алфавита на следующий или предыдущий на n позиций. Например, для n = 3.

<pre>
- a -> c  
- b -> d  
- ...  
- x -> a  
- z -> b  
</pre>

Таким образом, сообщение `HELLOWORLD` будет зашифровано как `KHOORZRUOG`.

Лингвисты подсчитали насколько часто каждый символ алфавита встречается в текстах определенного языка. Например, в [английском языке](http://en.wikipedia.org/wiki/Letter_frequency "Letter frequency") символ *E* встречается чаще остальных *(12.702%)*. Применяя частотный анализ находим наиболее часто встречающийся в шифротексте символ. Т.е. если чаще всего в шифротексте встречается символ *K* - то, наиболее вероятно, что при шифровании *E* переходит в *K*, значит *n = 6*.
Зная *n* и метод шифрования ничего не стоит заменить каждый символ шифротекста на его *n*-го предшественника в алфавите и получить исходный текст.
Либо можно продолжить работать с частотами символов: взять второй по частоте символ в шифротексте и заменить его на *A*. И так далее, до победного конца.
Данный метод можно использовать для пар, троек и больших сочетаний символов.

Примером использования шифра Цезаря является Unix утилита [ROT13](https://ru.wikipedia.org/wiki/ROT13 "ROT13"), которая осуществляет шифрование сдвигом символа на 13 позиций. Так как в английском языке 26 букв - двухкратное применение ROT13 возвращает исходный текст.

## Взлом шифра Виженера

В [шифре Виженера](https://ru.wikipedia.org/wiki/Шифр_Виженера "Шифр Виженера") каждый символ открытого текста складывается по модулю длины алфавита с символом ключа. Для английского языка символы складываются по модулю *(+ mod 26)*. Если ключ короче шифруемого сообщения - он дублируется столько раз, сколько необходимо. Например, шифрование сообщения `HOUSTONWEHAVEAPROBLEM` ключом `NEVERMORE` будет иметь вид:

<pre>
HOUSTONWEHAVEAPROBLEM
NEVERMORENEVERMORENEV
---------------------
USPWKABNIUEQIRBFFFYIH
</pre>

Для дешефрования необходимо вычесть ключ из шифротекста по модулю.

Взлом шифра Виженера ненамного сложнее взлома шифра Цезаря.
Допустим, нам известна длина ключа *n = |key|*, тогда рассмотрим символы на позициях *0, n-1, 2n-1...*. Как и в случае с шифром Цезаря, можно предположить, что наиболее часто встречающимся символом будет зашифрованная *E*. Тогда, сложив по модулю *E* с наиболее частым символом мы получим первый символ ключа. Повторим для второго и последующих символов ключа. Зная ключ - дешифруем сообщение.
В случаях, когда длина ключа неизвестна - просто выполним указанную процедуру для ключа длины *1, 2, ..., n* пока не будет получен связный текст.

## Взлом одноразового шифроблокнота

Взлом шифра с правильным использованием [одноразового шифроблокнота](https://ru.wikipedia.org/wiki/Шифр_Вернама "Одноразовый шифроблокнот") невозможен. Идея шифра с одноразовым шифроблокнотом состоит в том, чтобы сложить исходное сообщение со случайной строкой. Данный шифр обладает абсолютной криптографической стойкостью и не может быть взломан при наличии у взломщика одного лишь шифротекста.
Почему этот метод практически не используется?
Для дешифрования зашифрованного сообщения необходимо знать ключ. Ключ может быть использован только один раз. При этом длина ключа равна длине сообщения.
А если мы можем передать ключ - почему бы вместо этого не передать сообщение?

В случае, если один ключ был использован для шифрования нескольких сообщений - например *m1* и *m2* все, что необходимо сделать взломщику - сложить их шифротексты по модулю.

<pre>
c1 = key ^ m1
c2 = key ^ m2
c1 ^ c2 = m1 ^ m2
</pre>

Естественные языки достаточно избыточны, поэтому имея эту информацию, можно восстановить оригинальные сообщения *m1* и *m2*. Например, зная что в ASCII кодировке *XOR* пробела с символами *A-Z* окажется в диапазоне [33, 58], можно получить символы открытого текста. Чем больше у взломщика сообщений зашифрованых одним ключом - тем проще осуществить взлом шифра.

