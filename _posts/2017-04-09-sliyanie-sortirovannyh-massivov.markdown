---
layout: post
title:  "Слияние двух отсортированных массивов"
date:   2017-04-09 18:15:20 +0300
categories: pascal алгоритмы
---
Недавно я натолкнулся на интересную задачу, которую решил на языке программирования Pascal. Эта задача о том, как склеить два отсортированных массива в третий, который должен быть тоже отсортированным. Ниже приводится условие данной задачи и разбор решения.

## Условие задачи:

Даны целочисленные массивы А[1..10] и B[1..10], причем А [1] ≤ А [2] ≤ ... ≤ А [10], В [1] ≤ В [2] ≤ ... ≤ В [10] Постройте массив C[1..20], содержащий все элементы массивов А и В, в котором С [1] ≤ С [2] ≤ ... ≤ С [20].

*Пример ввода:*

1 3 5 7 8 9 11 13 15 17 2 4 6 8 10 12 14 16 18 20

*Пример вывода:*

1
2
3
4
5
6
7
8
8
9
10
11
12
13
14
15
16
17
18
20

## Интерпретация условия задачи:

У нас есть два отсортированных по возрастанию массива А и В. Нужно получить отсортированный по возрастанию массив С из массивов А и В.

## Идея  решения:

Представим оба массива A и B, как ленточные конвейеры, находящиеся на одном уровне и передвигающиеся числа справа налево. А на уровне ниже находится третий конвейер C, на который будут падать числа. За один ход мы передвигаем конвейер C влево для, того чтобы словить одно падающее число. В это же время сверху передвигается один из конвейеров A или B. За каждый такой ход необходимо, чтобы всегда сверху падало минимальное из оставшихся чисел конвейеров A или B. Если случится так, что на обоих конвейерах A и B слева находится одинаковое число, то не имеет значения какой из конвейеров передвигать, чтобы число упало. Если же на каком-то из конвейеров чисел не осталось, то передвигаем только тот конвейер, на котором остались числа. Всего таких ходов будет 20 - столько же, сколько нужно заполнить числами в массиве C. Таким образом, мы гарантируем что на конвейере C получится массив чисел отсортирован в порядке возрастания.

![Контейнеры](/images/cnt.gif)

## Моё решение:

Массив А, В и С;
Указатели X, Y и I;
Х - указатель на то, какой элемент еще не упал с конвейера и может упасть на следующем шаге, если он окажется меньшим из массива А.
Y - указатель на то, какой элемент еще не упал с конвейера и может упасть на следующем шаге, если он окажется меньшим из массива В.
I - указывает на то, который элемент мы заполняем на текущем шаге.

{% highlight pascal %}
A: 1 3 5 7 8 9 11 13 15 17
X: ^
X=1

B: 2 4 6 8 10 12 14 16 18 20
Y: ^
Y=1
{% endhighlight %}

Массив С пустой сначала;

{% highlight pascal %}
C: 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
I: ^
I=1
{% endhighlight %}

Первоначально x, y имеют значение = 1, и указывают на первый элемент массива.
На первом шаге берется  первый элемент массива А[1]=1 и  первый элемент массива В[1]=2,из них выбираем наименьший - это 1, ложим его в массив C в то место, куда указывает переменная i на первом шаге, это будет первый элемент… Таким образом, после первого шага в массиве C будет находится один элемент C[1] = 1.

Так как мы взяли элемент из массива A, то увеличиваем его счетчик на 1, помечая, что первый элемент этого массива удален. Теперь указатель в массиве А ссылается на второй элемент.

{% highlight pascal %}
A: 1 3 5 7 8 9 11 13 15 17
X:   ^
X=2

B: 2 4 6 8 10 12 14 16 18 20
Y: ^
Y=1

C: 1 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0
I:   ^
I=2
{% endhighlight %}

Далее берётся А[2]=3 и сравнивается с B[1]=2, выбираем наименьший это 2, ложим его в массив С.

Счетчик Y увеличиваем на 1. И так далее…

{% highlight pascal %}
A: 1 3 5 7 8 9 11 13 15 17
X:   ^
X=2

B: 2 4 6 8 10 12 14 16 18 20
Y:   ^
Y=2

C: 1 2 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 0 
I:     ^
I=3
{% endhighlight %}

Для того, чтобы не выйти за рамки массивов A и B, нужно следить за их счетчиками x и y соответственно. Так как, если X=11 это означает, все элементы из массива А использованы. Так же и со счетчиком Y в массиве В. В этом случае, если один из массивов стал ’’пустой’’ X=11 или Y=11, тогда нужно просто добавлять все остальные элементы другого массива.

##Решение на языке Pascal:

Для начала описываем переменные и массивы:


{% highlight pascal %}
Var
 A:array [1..10] of integer;
 B:array [1..10] of integer;
 C:array [1..20] of integer;
 x, y, i:integer;
{% endhighlight %}

Считываем массивы циклом:

{% highlight pascal %}
for i:=1 to 10 do read(a[i]);
for i:=1 to 10 do read(b[i]);
{% endhighlight %}

Далее счетчикам присвоим на начальный момент 1:

{% highlight pascal %}
x:=1;
y:=1;
{% endhighlight %}

Запускаем цикл I от 1 до 20, который проходит по всем элементам массива C по-порядку слева направо. В цикле у нас выполняется основная идея решения. Если текущий элемент из массива А меньше текущего элемента массива В, тогда мы добавляем A[X] в массив С, иначе добавляем B[Y]. Частные случаи, когда X=11 или Y=11 обработаем отдельно, дабы не усложнять основную ветвь алгоритма.

{% highlight pascal %}
if a[x]<b[y] then addandinc(a[x],i,x)
     else addandinc(b[y],i,y);
{% endhighlight %}

Я выполнял действие добавление в массив С, как простую процедуру:

{% highlight pascal %}
procedure addandinc(k,j:integer; var h:integer);
 begin
  addmassC(k,j);
  inc(h);
end;
{% endhighlight %}

Процедура *addandinc* добавляет элемент в массив С и увеличивает счетчик на 1. Здесь H - это указатели X или Y, в зависимости от того, с какого массива мы добавляем число в массив C.

{% highlight pascal %}
procedure addmassC(k,j:integer);
 begin
  c[j]:=k;
end;
{% endhighlight %}

Процедура принимает K – элемент который нужно добавить в массив С, J – указатель на какое место ставить этот элемент.

А сейчас о частных случаях. Если X=11, что значит массив А исчерпан, тогда просто по порядку добавляем элемент B[Y] из массива В, с помощью процедуры *addandinc* и продолжаем цикл.

{% highlight pascal %}
if x>10 then begin
 addandinc(b[y],i,y);
 continue;
end;
{% endhighlight %}

Если Y=11 тогда:

{% highlight pascal %}
if y>10 then begin
  addandinc(a[x],i,x);
  continue;
 end;
{% endhighlight %}

После того, как основной цикл I завершился, выводим циклом весь массив С.

{% highlight pascal %}
for i:=1 to 20 do writeln(c[i]);
{% endhighlight %}

Вот код программы:

{% highlight pascal %}
var
 a: array [1..10] of integer;
 b: array [1..10] of integer;
 c: array [1..20] of integer;
 i,x,y:integer;
 procedure addmassC(k,j:integer);
 begin
  c[j]:=k;
 end;
 procedure addandinc(k,j:integer; var h:integer);
 begin
  addmassC(k,j);
  inc(h);
 end;

begin
  assign(input, 'input.txt');
  reset(input);
  assign(output,'output.txt');
  rewrite(output);
  for i:=1 to 10 do read(a[i]);
  for i:=1 to 10 do read(b[i]);
  x:=1;
  y:=1;

  for i:=1 to 20 do begin
   if x>10 then begin
    addandinc(b[y],i,y);
    continue;
   end;

   if y>10 then begin
    addandinc(a[x],i,x);
    continue;
   end;

   if a[x]<b[y] then addandinc(a[x],i,x)
    else addandinc(b[y],i,y);

  end;

  for i:=1 to 20 do writeln(c[i]);
  close(input);
  close(output);
end.
{% endhighlight %}

Программа  сразу настроена на чтение из файла *input* и вывод в файл *output*.
Сложность алгоритма О(N).

Данное решение работает только тогда, когда A и B отсортированы, иначе алгоритм работать не будет. Потому как,  если взять не отсортированные массивы А и В, в итоге мы получим не правильно отсортированный массив С.
