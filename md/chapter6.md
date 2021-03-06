# 第六章：索引

索引用于通过位置来选择数组的项。本章主题包括：选择项，重新排列已经选定的项来构成新的数组，修改或更新数组中已经选定的项。

## 6.1 选择

动词`{`(左大括号)被叫做“选取(From)”。表达式`(x { y)`表示从`y`中选取由`x`指定位置的项。例如，在第二章中，`L`是一个列表，`L`中项的位置是数字 0 1 之类。表达式`(0 { L)`将给出`L`中第一个项的值，`1 { L`将给出第二个项的值。

`L =: 'abcdef'`|`0 { L`|`1 { L`
---------------|-------|-------
`abcdef`       |`a`    |`b`

`{`的左参数被称之为“索引”。

### 6.1.1 常见的选择模式

可以同时选择多个项：

`L`     |`0 2 4 { L`
--------|-----------
`abcdef`|`ace`

从`L`中选择的项可以重复，并可重新排序：

`L`     |`5 4 4 3 { L`
--------|-------------
`abcdef`|`feed`

索引值可以为负：`_1 `会选择最后一项，`_2`将会选择倒数第二项。正数索引与负数索引可以混用。

`L`     |`_1 { L`|`_2 1 { L`
--------|--------|----------
`abcdef`|`f`     |`eb`

选择表中的单个项通过如下方式：例如选择第1行第2列，使用索引`(< 1 ; 2)`。

`T =: 3 3 $ 'abcdefghi'`|`(< 1 ; 2) { T`
------------------------|---------------
`abc`<br>`def`<br>`ghi` |`f`

可以通过选择表中指定几个行和列包括的所有项，来产生一个较小的表（称之为子数组）。例如，选择一个包含列`1`和`2`，行`0`和`1`的子数组，使用索引 `(< 1 2; 0 1)`

`T`                    |`(< 1 2;0 1) { T`
-----------------------|-----------------
`abc`<br>`def`<br>`ghi`|`de`<br>`gh`

可以从表中选择完整的一行或几行。回忆一下，表示由行作为项，所组成的列表。所以，从表中选择行就和从列表中选择项一样。

`T`|`1 { T`|`2 1 { T`
---|-------|---------
`abc`<br>`def`<br>`ghi`|`def`|`ghi`<br>`def`

选择完整的一列或几列，一个简单的方法是选择所有行：

`T`                    |`(< 0 1 2 ; 1 ){ T`
-----------------------|-------------------
`abc`<br>`def`<br>`ghi`|`beh`

但是还有其他方法，详见下文。

### 6.1.2 提取(Take)，丢弃(Drop)，取首项(Head)，弃首项(Behead)，取尾项(Tail)，弃尾项(Curtail)

接下来我们看一组提供了方便索引形式的动词。内置动词`{.`(左大括号 点，叫做“提取”)。`(n {. L)`将会选取列表`L`的前`n`项。

`L`     |`2 {. L`
--------|--------
`abcdef`|`ab` 

如果我们通过`(n {. L)`在`L`中取`n`项，但是`n`大于`L`的长度，那么结果将会使用0，空格或空箱来扩充至长度为`n`。

例如，我们需要从给定的字符串创建出精确的长度为8的字符串。

`s =: 'pasta'`|`# s`|`z =: 8 {. s`|`# z`
--------------|-----|-------------|-----
`pasta`       |`5`  |`pasta   `   |`8`

内置动词`}.`(右大括号 点，叫做“丢弃”)。`L`中除了前`n`项外的所有项将会被表达式`(n }. L)`选中。

`L`     |`2 }. L`
--------|--------
`abcdef`|`cdef`

`L`中最后的`n`项可以通过表达式`(-n) {. L`选择。选择除最后`n`项以外的所有项可以使用表达式`(-n) }. L`

`L`     |`_2 {. L`|`_2 }. L`
--------|---------|---------
`abcdef`|`ef`     |`abcd`

当`n=1`时，提取和丢弃会有一些特殊缩写。可以通过单目的`{.`(左大括号 点，叫做“取首项”)选取第一项。选取除第一项外所有项使用`}.`(右大括号 点，叫做“弃首项”)。

`L`     |`{. L`|`}. L`
--------|------|------
`abcdef`|`a`   |`bcdef`
 
选取最后一项使用单目的`{:`(左大括号 冒号，叫做“取尾项”)。选取除最后一项外所有项使用`}:`(右大括号 冒号，叫做“弃尾项”)。

`L`     |`{: L`|`}: L`
--------|------|------
`abcdef`|`f`   |`abcde`

## 6.2 选取的通常处理方式

一些术语有助于理解下面的内容。通常情况下我们使用n维数组，但接下来我们考虑三维数组的情况。每一个元素可以通过给出平面数行数及列数来确定。我们认为平面沿着第一维坐标轴分布，行沿着第二个，列沿着第三个。

索引本身没有什么特殊符号。然而`{`的左参数是是一个表示编码，选择和重排的数据结构。这个数据结构可以用任何方式构建。接下来将说明如何构建这个数据结构。

### 6.2.1 单独选择

用于索引的表达式，其一般形式是`index { array`。`index`以是一个标量数组。`index`中的每一个标量都会进行独立的选择，最后将结果结合在一起。

`L`     |`0 1 { L`
--------|---------
`abcdef`|`ab`

### 6.2.2 索引结果的外形

索引结果的外形依赖于`index`的外形。

`L`     |`index =: 2 2 $ 2 0 3 1`|`index { L`
--------|------------------------|-----------
`abcdef`|`2 0`<br>`3 1`          |`ca`<br>`db`

索引值必须在从`-#L`到`(#L)-1`的范围内。

`L`     |`#L`|`_7 { L`|`6 { L`
--------|----|--------|-------
`abcdef`|`6` |`error` |`error`

### 6.2.3 标量

`index`中的每一个标量必须是一个数字或一个箱（如果一个是箱，其余必须都是）。若果标量是一个数字，则将会从中`array`选取一项。

`A =: 2 3 $ 'abcdef'`|`1 { A`
---------------------|-------
`abc`<br>`def`       |`def`

如果`index`中的标量是箱，那么它将包含一列选择器，这些选择器被应用到连续的轴上。为了展示箱在此处的功能，我们用`SuAx`表示封箱函数。

```
    SuAx =: <
```

下面的例子将会从`A`中选取位置在行1列0的元素。

`A`           |`(SuAx 1 0) { A`
--------------|----------------
`abc`<br>`def`|`d` 

### 6.2.4 在一条轴上选取

表达式`(SuAx p, r, c)`表示在连续的轴上选取，其中`p`，`r`和`c`都是标量。这些标量可以是数字或者箱（如果一个是箱，其余的都是）。一个数字将会在其对应的轴上选择一个事物：一个平面，一行或一列。

如果选择器是一个箱，它需要包含在对应轴上所有的选择器所构成的一个列表。为了展示箱在此处的功能，我们定义`Sel`为封箱函数。

```
    Sel =: < 
```

例如，从`A`中选取列1，行0 2的所有元素：

`A`           |`(SuAx (Sel 1), (Sel 0 2)) { A`
--------------|-------------------------------
`abc`<br>`def`|`df`

### 6.2.5 排除内容

除了在特定轴上选择内容，我们也可以通过在额外的一层箱中放入一列需要排除的内容的编号来排除相应的内容。为了展示箱在此处的功能，我们定义 Excl 为封箱函数。

```
    Excl =: <
```

例如我们需要从`A`中选取列0，除列1以外所有列的元素：

`A`           |`(SuAx (Sel 0), (Sel (Excl 1))) { A`
--------------|------------------------------------
`abc`<br>`def`|`ac`

我么也可以选取特定轴上的所有内容，也就是不排除任何内容。也就是使用一个空列表`(0$0)`来排除。例如从`A`中选择行1所有列的元素：

`A`           |`(SuAx (Sel 1),(Sel (Excl 0$0))) { A`
--------------|-------------------------------------
`abc`<br>`def`|`def`

### 6.2.6 简化

表达式`(Excl 0$0)`表示一个封箱了的空列表。有一个J语言内置缩写表示相同的意思，叫做`(a:)`(字母a 冒号，叫做“Ace”)，在当前的用法下，我们也可以认为是“全部(all)”的意思。

`A`           |`(SuAx (Sel 1),(Sel a:)) { A`
--------------|-----------------------------
`abc`<br>`def`|`def`

在用形同`(SuAx p,q,..., z)`的表达式索引时，如果最后一个选择器`z`是选取“全部”的形式：`(Sel (Excl 0$0))`或`(Sel a:)`，那么它可以被省略。

`A`           |`(SuAx (Sel 1),(Sel a:)) {A`|`(SuAx (Sel 1)) {A`
--------------|----------------------------|-------------------
`abc`<br>`def`|`def`                       |`def`

若果索引形同`(SuAx (Sel p),(Sel q),...)`完全没有选取“全部”的形式，那么可以简写为`(SuAx p;q;...)`。例如选取行1列0 2的所有元素：

`A`           |`(SuAx (Sel 1),(Sel 0 2)) {A`|`(SuAx 1;0 2) {A`
--------------|-----------------------------|-----------------
`abc`<br>`def`|`df`                         |`df`

最后，如果在每个轴上只选择一个事物，一个简单的未封箱列表就够了。例如选取行1列2的项：

`A`           |`(SuAx 1;2) { A`|`(SuAx 1 2) { A`
--------------|----------------|----------------
`abc`<br>`def`|`f`             |`f`

### 6.2.7 结果的外形

假设 B 是一个三维数组：

```
    B =: 10 + i. 3 3 3
```

之后我们定义`p`在`B`的第一轴选择平面，`r`在第二轴选择行，`c`在第三轴选择列：

``` 
    p =: 1 2
    r =: 1 2
    c =: 0 1
```

我们可以看到使用`p;r;c`选择，得到的结果`R`的外形是`p`，`r`和`c`外形的组合。 

`B`|`R =: (< p;r;c) { B`|`$ R`|`($p),($r),($c)`
---|--------------------|-----|----------------
`10 11 12`<br>`13 14 15`<br>`16 17 18`<br><br>`19 20 21`<br>`22 23 24`<br>`25 26 27`<br><br>`28 29 30`<br>`31 32 33`<br>`34 35 36`|`22 23`<br>`25 26`<br><br>`31 32`<br>`34 35`|`2 2 2`|`2 2 2`

`B`是三维的，`R`也是。当选择器（`r`）是长度为一的列表时，外形的组合结果也正如我们预期：

`r =: 1 $ 1`|`S =: (< p;r;c){B`    |`$ S`  |`($p),($r),($c)`
------------|----------------------|-------|----------------
`1`         |`22 23`<br><br>`31 32`|`2 1 2`|`2 1 2`

当选择器`r`时一个表量的时候，外形的组合如下：

`r =: 1`|`T =: (< p;r;c){B`|`$ T`|`($p),($r),($c)`|`$ r`
--------|------------------|-----|----------------|-----
`1`     |`22 23`<br>`31 32`|`2 2`|`2 2`           |` `

在最后一个例子中，`r`是一个标量，`r`的形状是一个空列表，`r`对应的轴消失了，所以结果`T`是二维的。

## 6.3 修改（或更新）数组

有时我们需要使用一个数组，其和一个已经存在的数组仅有少量的值不同。我们可以在指定的位置上“修改”或者“更新”这个数组。用于修改数组的J语言函数是`}`(右大括号，叫做“修改”)。

### 6.3.1 使用索引修改

修改一个数组我们需要三个要素：

* 原始数组
* 需要修改的位置的说明。这可以是一个索引，和之前在`{`中使用的类似
* 在指定位置用于替换现有元素的值

因此用于修改的J语言表达式一般形式如下：

```
    newvalues index } original
```

范例：修改列表`L`，将其第一项（在位置`0`）替换用'*'替换：

`L`     |`new=:'*'`|`index=:0`|`new index } L`
--------|----------|----------|---------------
`abcdef`|`*`       |`0`       |`*bcdef`

使用副词`}`，接受参数`index`来产生双目的修改动词`(index })`。

```
    ReplaceFirst =: 0 }

    '*' ReplaceFirst L
*bcdef
```

`(index })`是一个通过正常方式生成新值的双目动词。因此通过“修改”的方式来改变一个数组，需要指定原有的数组名来接受计算得到的结果。所以修改通常的使用方式如下：

```
    A =: new index } A
```
J系统确保这是一个高效运算，不进行不必要的数据移动。

在第行1列2处修改一个表，范例如下：

`A`           |`'*' (< 1 2) } A`
--------------|-----------------
`abc`<br>`def`|`abc`<br>`de*`

修改多个元素时，可以提供一列新值，这些新值将依次去替索引换选中的元素，并会被循环使用。

`L`     |`'*#' 1 2 } L`
--------|--------------
`abcdef`|`a*#def`

### 6.3.2 通过动词修改

`Y`是一列数，我们需要将其中大于`X`的所有数字替换为`X`。（在这个例子中我们先忽视J语言的内置动词`(<.)`）

`Y`中需要被改变的索引位置可以通过`X`与`Y`计算得到。这里有一个函数`f`用于计算这些索引：

```
    f =: 4 : '(y > x) # (i. # y)'
```

`X =: 100`|`Y =: 98 102 101 99`|`Y > X`  |`X f Y`
----------|--------------------|---------|-------
`100`     |`98 102 101 99`     |`0 1 1 0`|`1 2`

接下来修改可以用我们之前看到的方式，用`(X f Y)`作为索引来进行修改：

`Y`            |`X (X f Y) } Y`
---------------|---------------
`98 102 101 99`|`98 100 100 99`
 
“修改”副词`}`允许将表达式`(X (X f Y) } Y)`简写为`(X f } Y)`。

`X (X f Y) } Y`|`X f } Y`
---------------|---------
`98 100 100 99`|`98 100 100 99`

所以，副词`}`可以接受索引值`(X f Y)`作为参数，也可以接受动词`f`。 

```
    cap =: f }

    10 cap 8 9 10 11
8 9 10 10
```

注意：动词`f`如果要作为参数提供给副词`}`，`f`就必须是是双目动词，虽然它可以忽略参数`X`或`Y`。

### 6.3.3 线性索引

我们之前仅仅介绍了使用动词修改列表，这个动词的目的是在列表中找出要修改的位置，即计算出要修改位置的索引。对于一个表而不是列，它的索引将会有2维，构建索引时，动词的任务将会变得更加困难。但是将表展开为线型列表，并重构为表将相对容易实现。

首先我们有如下的表：

```
    M =: 2 2 $ 13 52 51 14
```

接下类使用索引寻找动词`f`，展开、修改、重构的流程如下：

`M`|`LL =: ,M`|`Z =: 50 f } LL`|`($M) $ Z`
---|----------|----------------|----------
`13 52`<br>`51 14`|`13 52 51 14`|`13 50 50 14`|`13 50`<br>`50 14`

然而还有一种更好的方式。首先注意，索引寻找动词`f`接受的参数不是`M`而是`(LL =: , M)`，因此`M`的外形信息无法被索引寻找函数`f`得到。在这个例子中这没什么问题，但在一般情况下，索引寻找函数可能同时需要`M`的外形和值。所以将`M`整体最为`f`的参数可能会更好。在这种情况下`f`就必须自己实现展开。重新定义`f`：

```
    f =: 4 : 0
y =. , y
(y > x) # (i. # y)
)
```

`M`               |`50 f M`
------------------|--------
`13 52`<br>`51 14`|`1 2`
 
现在索引寻找函数`f`接受一个数组作为参数并且得到再已经展开的数组中的索引，所以叫做“线性索引”。使用新定义的`f`进行修改操作的方法如下：

`M`|`($M) $ 50 (50 f M) } (, M)`
---|----------------------------
`13 52`<br>`51 14`|`13 50`<br>`50 14`

最终`f`提供了线性索引。`(})`允许对最后这个表达式变为的简化形式：

`M`               |`50 f } M`
------------------|----------
`13 52`<br>`51 14`|`13 50`<br>`50 14`
 
## 6.4 数组项合并

我们之前看到`}`是一个副词，它被用来通过一个参数生成一个动词`(m })`。我们看到这个动词的双目形式可以用来修改数组。

接下来我们看一下`(m })`的单目形式。这个动词被叫做“修改项”，接下来是一个例子。存在表 `T`：

```
    T =: 3 4 $ 'ABCDEFGHIJKL'
```

令 `m`是一列索引。`m`中的每一项用于指定`T`中对应列中的索引。

```
    m =: 1 2 0 2 
```

对`T`应用动词`m }`，可以看到结果是对每一列分别索引而得到的。

`T`|`m`|`(m }) T`
---|---|---------
`ABCD`<br>`EFGH`<br>`IJKL`|`1 2 0 2`|`EJCL`

另一种描述这个结果的方式是说`T`中的项根据向量`m`进行重新组合。

当在两个数组中选择时，副词`}`的参数可以是一个布尔值数组。范例如下：

```
    A =: 'pot'  NB. first alternative
    B =: 'dig'  NB. second alternative
    1 0 1 }  A ,: B
dog
```