## 前言

js中为了提升面向对象的一致性，为数组也封装了好多方法，供用于操作，但是其中一切方法有些混乱，比如操作是否对原数组进行改动，这里做个总结

### 1.首尾增删方法

#### pop()

pop()从数组末尾移除最后一项,减少数组的length值,然后返回移除的项

![array.pop()][1]

#### push(*arg)

push()方法可以接收任意数量的参数，把它们逐个添加到数组末尾,并返回修改后数组的长度

![array.push()][2]

#### shift()

shift()删除并返回数组的第一个元素,同时减少数组的length值

![array.shift()][3]

#### unshift(*arg)

unshift()向数组的开头添加一个或更多元素，并返回新的长度

![array.unshift()][4]

**利用push()和pop方法()方法,可以实现类似栈的行为,即先进后出**

**利用push()和shift方法()方法,可以实现类似队列的行为,即先进先出**

### 2.转换方法

#### join(arg)

对原数组中的元素通过指定的分隔符进行分隔

![join()][5]

#### toString()

toString()返回由数组中每个值的字符串形式拼接而成的一个以逗号分隔的字符串

![toString()][6]

### 3.复杂操作方法

#### concat(*arg)

该方法连接两个或更多的数组,不改变原有的数组,仅返回一个拼接元素的数组副本

![array.concat()][7]

#### slice(start,end)

该方法从某个已有的数组返回选定区域的元素,不会改变原有的数组,仅返回一个截取元素的数组副本

![array.slice()][8]

#### splice()

1.splice(start,num) 从指定下标删除相应数量的数组元素,返回一个删除元素的数组副本

![array.splice()][9]

2.splice(start,num,*arg) 从指定下标删除相应数量的数组元素，并用指定元素替换(元素个数不限),返回一个删除元素的数组副本

![array.splice()][10]

3.splice(start,0,*arg) 从指定下标开始插入指定元素(元素个数不限),返回一个空数组副本

![array.splice()][11]

### 4.排序方法

#### reverse()

reverse()会颠倒数组中元素的顺序,返回一个颠倒元素的数组副本

![reverse()][12]

#### sort(null | function)

sort()对数组的元素进行排序,默认按照字符编码(ASCII)的顺序进行排序,

或者按照参数中的排序函数进行排序: **排序函数返回为负值,升序排列;排序函数返回正值,降序排列**

![sort()][13]

### 5.位置方法

indexOf()从数组的起始下标开始向后查找,返回查找元素在数组中的位置,没找到的情况下返回-1

![indexOf()][14]

lastIndexOf()从数组的终止下标开始向前查找,返回查找元素在数组中的位置,没找到的情况下返回-1

![lastIndexOf()][15]

indexOf()和lastIndexOf()方法要求查找的项必须严格相等(===)

### 6.其他方法

这里只是介绍了数组最常用的基本方法,除此之外,还有toLocalString(),valueOf()等方法，感兴趣的可以打开Chorme的Dev Tools,在console中自行探索

![other_methods][16]

### 总结

增删方法一定会对数组产生改变(内部元素和length属性)

转换方法未对数组产生改变

复杂操作中的concat()和slice()未对数组产生改变,而splice()对数组产生改变

排序方法均对数组产生改变

位置方法未对数组产生改变

 [1]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/1.jpg
 [2]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/2.jpg
 [3]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/3.jpg
 [4]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/4.jpg
 [5]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/12.jpg
 [6]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/13.jpg
 [7]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/5.jpg
 [8]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/6.jpg
 [9]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/17.jpg
 [10]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/8.jpg
 [11]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/9.jpg
 [12]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/10.jpg
 [13]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/11.jpg
 [14]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/14.jpg
 [15]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/15.jpg
 [16]: http://ohc0a3roa.bkt.clouddn.com/blog/javascript/array/16.jpg