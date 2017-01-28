Chapter 1. The Python Data Model
第一章－Python数据模型
********************************

*Guido’s sense of the aesthetics of language design is amazing. I’ve met many fine language designers who could build theoretically beautiful languages that no one would ever use, but Guido is one of those rare people who can build a language that is just slightly less theoretically beautiful but thereby is a joy to write programs in[3].*

*— Jim Hugunin creator of Jython, co-creator of AspectJ, architect of the .Net DLR*  

One of the best qualities of Python is its consistency. After working with Python for a while, you are able to start making informed, correct guesses about features that are new to you.  

Python的一个最佳特质就是其具有的一致性。在使用Python一段时间后，你就能够知道并正确地猜测要使用的心功能。  

However, if you learned another object oriented language before Python, you may have found it strange to spell `len(collection)` instead of `collection.len()`. This apparent oddity is the tip of an iceberg which, when properly understood, is the key to everything we call `Pythonic`. The iceberg is called the Python Data Model, and it describes the API that you can use to make your own objects play well with the most idiomatic language features.  

不过，要是你在学些Python之前，学习过其他的面向对象语言，你会奇怪地发现Python使用的是*len(collection)*而不是*collection.len().*这只是明显奇怪的的做法只是冰山一角，当你能够正确理解后，这也是我们称之“Python范儿”的关键。而冰山则称为Python数据模型，它描述了能够让你使用它来构建符合大多数语言特性的个人项目。  

You can think of the Data Model as a description of Python as a framework. It formalizes the interfaces of the building blocks of the language itself, such as sequences, iterators, functions, classes, context managers and so on.  

你可以认为数据模型作为一个使用Python语言描述的框架。它将语言自身所构建的语句块接口正式化了，比如队列，迭代器，函数，类，上下文管理器等等。  

While coding with any framework, you spend a lot of time implementing methods that are called by the framework. The same happens when you leverage the Python Data Model. The Python interpreter invokes special methods to perform basic object operations, often triggered by special syntax. The special method names are always spelled with leading and trailing double underscores, i.e. `__getitem__`. For example, the syntax `obj[key]` is supported by the `__getitem__` special method. To evaluate `my_collection[key]`, the interpreter calls `my_collection.__getitem__(key)`.  

在使用框架编写代码时，实际上你花了很多时间来实现可以被框架调用的方法。同样的事情也发生在你处理Python数据模型时。Python解释器调用会调用特殊方法以执行基本的对象操作，这个操作经常是通过对特殊语法的触发来实现。特殊方法名称常常利用首位的双下划线拼写而成，例如，*__getitem__*。例如，语法*obj[key]*由特殊方法*__getitem__*提供支持。为了计算*my_collection[key]*，解释器需要调用*my_collection.__getitem__(key)*。  

The special method names allow your objects to implement, support and interact with basic language constructs such as:  

特殊方法名称可以使对象实现并支持与基本语言结构的交互，比如：  

- iteration;
- collections;
- attribute access;
- operator overloading;
- function and method invocation;
- object creation and destruction;
- string representation and formatting;
- managed contexts (i.e. *with* blocks);

- 迭代；
- 集合；
- 属性访问；
- 运算符重载；
- 函数及方法的调用；
- 对象创建与销毁；
- 字符串的重表示与格式化；
- 管理上下文（例如，with语句块）；


##### MAGIC AND DUNDER 魔法和双下划线
The term magic method is slang for special method, but when talking about a specific method like `__getitem__`, some Python developers take the shortcut of saying “under-under-getitem” which is ambiguous, since the syntax __x has another special meaning[4]. But being precise and pronouncing “under-under-getitem-under-under” is tiresome, so I follow the lead of author and teacher Steve Holden and say “dunder-getitem”. All experienced Pythonistas understand that shortcut. As a result, the special methods are also known as dunder methods [5].  

术语魔法方式是特殊方法的俚语，当我们谈论一个类似`__getitem__`的专有方法时，有时候Python开发者会简称为“under-under-getitem”，这让其他人感到困惑，因为语法__x拥有特别的意义。

## A Pythonic Card Deck Python风格纸牌游戏
The following is a very simple example, but it demonstrates the power of implementing just two special methods, `__getitem__` and `__len__`.  

下面是一个非常简单的例子，但它足以说明只实现了`__getitem__` 和`__len__`特殊方法的强大之处：  

Example 1-1 is a class to represent a deck of playing cards:  

例子1-1是一个表示一副纸牌游戏的例子：  

*Example 1-1. A deck as a sequence of cards.*  
例子1-1. 一副按照顺序排列的纸牌。  

```python
import collections

Card = collections.namedtuple('Card', ['rank', 'suit'])


class FrenchDeck:
    ranks = [str(n) for n in range(2, 11)] + list('JQKA')
    suits = 'spades diamonds clubs hearts'.split()

    def __init__(self):
        self._cards = [Card(rank, suit) for suit in self.suits
                                        for rank in self.ranks]

    def __len__(self):
        return len(self._cards)

    def __getitem__(self, position):
        return self._cards[position]
```

The first thing to note is the use of collections.namedtuple to construct a simple class to represent individual cards. Since Python 2.6, namedtuple can be used to build classes of objects that are just bundles of attributes with no custom methods, like a database record. In the example we use it to provide a nice representation for the cards in the deck, as shown in the console session:  

要注意的第一件事情是使用collections.namedtuple构建一个简单的类来表示每张牌。从Python2.6起，namedtuple可以向数据库记录一样，只使用一组没有自定义方法的属性来构建对象的类。在这个例子中我们用它来为整副牌提供一个好看的外观，一如控制台会话所示：  

```python
>>> beer_card = Card('7', 'diamonds')
>>> beer_card
Card(rank='7', suit='diamonds')
```

But the point of this example is the `FrenchDeck` class. It’s short, but it packs a punch. First, like any standard Python collection, a deck responds to the `len()` function by returning the number of cards in it.  

```python
>>> deck = FrenchDeck()
>>> len(deck)
52
```

但是这个例子的关键点在于类`FrenchDeck`。这个类的代码略少，但是它直中要害。首先，和任何其他的Python集合一样，deck通过返回纸牌的数量已响应`len()`函数：  

Reading specific cards from the deck, say, the first or the last, should be as easy as `deck[0]` or `deck[-1]`, and this is what the `__getitem__` method provides.  

从整副牌中读取指定的牌，我是说第一张或者最后一张，应该简单的使用`deck[0]` 或者 `deck[-1]`，这也正是`__getitem__`方法所提供的功能。  

```python
>>> deck[0]
Card(rank='2', suit='spades')
>>> deck[-1]
Card(rank='A', suit='hearts')
```

Should we create a method to pick a random card? No need. Python already has a function to get a random item from a sequence: random.choice. We can just use it on a deck instance:  

```python
>>> from random import choice
>>> choice(deck)
Card(rank='3', suit='hearts')
>>> choice(deck)
Card(rank='K', suit='spades')
>>> choice(deck)
Card(rank='2', suit='clubs')
```

We’ve just seen two advantages of using special methods to leverage the Python Data Model:  

1. The users of your classes don’t have to memorize arbitrary method names for standard operations (“How to get the number of items? Is it .size() .length() or what?”)

2. It’s easier to benefit from the rich Python standard library and avoid reinventing the wheel, like the random.choice function.  


But it gets better.  

Because our `__getitem__ `delegates to the `[]` operator of `self._cards`, our deck automatically supports slicing. Here’s how we look at the top three cards from a brand new deck, and then pick just the aces by starting on index 12 and skipping 13 cards at a time:  

```python
>>> deck[:3]
[Card(rank='2', suit='spades'), Card(rank='3', suit='spades'),
Card(rank='4', suit='spades')]
>>> deck[12::13]
[Card(rank='A', suit='spades'), Card(rank='A', suit='diamonds'),
Card(rank='A', suit='clubs'), Card(rank='A', suit='hearts')]
```

Just by implementing the `__getitem__` special method, our deck is also iterable:  
```python
>>> for card in deck:  # doctest: +ELLIPSIS
...   print(card)
Card(rank='2', suit='spades')
Card(rank='3', suit='spades')
Card(rank='4', suit='spades')
...
```

The deck can also be iterated in reverse:  

```python
>>> for card in reversed(deck):  # doctest: +ELLIPSIS
...   print(card)
Card(rank='A', suit='hearts')
Card(rank='K', suit='hearts')
Card(rank='Q', suit='hearts')
...
```

##### ELLIPSIS IN DOCTESTS
Whenever possible, the Python console listings in this book were extracted from doctests to insure accuracy. When the output was too long, the elided part is marked by an ellipsis ... like in the last line above. In such cases, we used the `#` doctest: +ELLIPSIS directive to make the doctest pass. If you are trying these examples in the interactive console, you may omit the doctest directives altogether.  

Iteration is often implicit. If a collection has no `__contains__` method, the in operator does a sequential scan. Case in point: in works with our FrenchDeck class because it is iterable. Check it out:  

```python
>>> Card('Q', 'hearts') in deck
True
>>> Card('7', 'beasts') in deck
False
```

How about sorting? A common system of ranking cards is by rank (with aces being highest), then by suit in the order: spades (highest), then hearts, diamonds and clubs (lowest). Here is a function that ranks cards by that rule, returning 0 for the 2 of clubs and 51 for the ace of spades:  

```python
suit_values = dict(spades=3, hearts=2, diamonds=1, clubs=0)

def spades_high(card):
    rank_value = FrenchDeck.ranks.index(card.rank)
    return rank_value * len(suit_values) + suit_values[card.suit]
```

Given `spades_high`, we can now list our deck in order of increasing rank:  
```python
>>> for card in sorted(deck, key=spades_high):  # doctest: +ELLIPSIS
...      print(card)
Card(rank='2', suit='clubs')
Card(rank='2', suit='diamonds')
Card(rank='2', suit='hearts')
... (46 cards ommitted)
Card(rank='A', suit='diamonds')
Card(rank='A', suit='hearts')
Card(rank='A', suit='spades')
```

Although FrenchDeck implicitly inherits from `object`[6], its functionality is not inherited, but comes from leveraging the Data Model and composition. By implementing the special methods `__len__` and `__getitem__` our `FrenchDeck` behaves like a standard Python sequence, allowing it to benefit from core language features—like iteration and slicing—and from the standard library, as shown by the examples using `random.choice`, `reversed` and `sorted`. Thanks to composition, the `__len__` and `__getitem__` implementations can hand off all the work to a `list` object, `self._cards`.  

##### HOW ABOUT SHUFFLING?
As implemented so far, a `FrenchDeck` cannot be shuffled, because it is *immutable*: the cards and their positions cannot be changed, except by violating encapsulation and handling the `_cards` attribute directly. In Chapter 11 that will be fixed by adding a one-line `__setitem__` method.

## How special methods are used
The first thing to know about special methods is that they are meant to be called by the Python interpreter, and not by you. You don’t write `my_object.__len__()`. You write `len(my_object)` and, if `my_object` is an instance of a user defined class, then Python calls the `__len__` instance method you implemented.  

But for built-in types like `list`, `str`, `bytearray` etc., the interpreter takes a shortcut: the CPython implementation of `len()` actually returns the value of the `ob_size` field in the `PyVarObject C` struct that represents any variable-sized built-in object in memory. This is much faster than calling a method.  

More often than not, the special method call is implicit. For example, the statement `for i in x`: actually causes the invocation of `iter(x)` which in turn may call `x.__iter__()` if that is available.  

Normally, your code should not have many direct calls to special methods. Unless you are doing a lot of metaprogramming, you should be implementing special methods more often than invoking them explicitly. The only special method that is frequently called by user code directly is `__init__`, to invoke the initializer of the superclass in your own `__init__` implementation.  
If you need to invoke a special method, it is usually better to call the related built-in function, such as `len`, `iter`, `str` etc. These built-ins call the corresponding special method, but often provide other services and—for built-in types—are faster than method calls. See for example `A closer look at the iter function` in `Chapter 14`.  

Avoid creating arbitrary, custom attributes with the `__foo__` syntax because such names may acquire special meanings in the future, even if they are unused today.  

### Emulating numeric types
Several special methods allow user objects to respond to operators such as +. We will cover that in more detail in Chapter 13, but here our goal is to further illustrate the use of special methods through another simple example.

We will implement a class to represent 2-dimensional vectors, i.e. Euclidean vectors like those used in math and physics (see Figure 1-1).  

图片：略  

Figure 1-1. Example of 2D vector addition. Vector(2, 4) + Vector(2, 1) results in Vector(4, 5).  

##### TIP
The built-in complex type can be used to represent 2D vectors, but our class can be extended to represent n-dimensional vectors. We will do that in Chapter 14.  

We will start by designing the `API` for such a class by writing a simulated console session which we can use later as doctest. The following snippet tests the vector addition pictured in Figure 1-1:  

```python
>>> v1 = Vector(2, 4)
>>> v2 = Vector(2, 1)
>>> v1 + v2
Vector(4, 5)
```

Note how the + operator produces a `Vector` result which is displayed in a friendly manner in the console.

The `abs` built-in function returns the absolute value of integers and floats, and the magnitude of `complex` numbers, so to be consistent our `API` also uses `abs` to calculate the magnitude of a vector:  

```python
>>> v = Vector(3, 4)
>>> abs(v)
5.0
```

We can also implement the `*` operator to perform scalar multiplication, i.e. multiplying a vector by a number to produce a new vector with the same direction and a multiplied magnitude:  

```python
>>> v * 3
Vector(9, 12)
>>> abs(v * 3)
15.0
```

Example 1-2 is a `Vector` class implementing the operations just described, through the use of the special methods `__repr__`, `__abs__`, `__add__` and `__mul__`:  

*Example 1-2. A simple 2D vector class.*  

```python
from math import hypot

class Vector:

    def __init__(self, x=0, y=0):
        self.x = x
        self.y = y

    def __repr__(self):
        return 'Vector(%r, %r)' % (self.x, self.y)

    def __abs__(self):
        return hypot(self.x, self.y)

    def __bool__(self):
        return bool(abs(self))

    def __add__(self, other):
        x = self.x + other.x
        y = self.y + other.y
        return Vector(x, y)

    def __mul__(self, scalar):
        return Vector(self.x * scalar, self.y * scalar)
```

Note that although we implemented four special methods (apart from `__init__`), none of them is directly called within the class or in the typical usage of the class illustrated by the console listings. As mentioned before, the Python interpreter is the only frequent caller of most special methods. In the next sections we discuss the code for each special method.  

### String representation
The `__repr__` special method is called by the `repr` built-in to get string representation of the object for inspection. If we did not implement `__repr__`, vector instances would be shown in the console like `<Vector object at 0x10e100070>.`  

The interactive console and debugger call `repr` on the results of the expressions evaluated, as does the `'%r'` place holder in classic formatting with `%` operator, and the `!r` conversion field in the new Format String Syntax used in the `str.format` method[7].  

Note that in our `__repr__` implementation we used `%r` to obtain the standard representation of the attributes to be displayed. This is good practice, as it shows the crucial difference between `Vector(1, 2)` and `Vector('1', '2')`—the latter would not work in the context of this example, because the constructors arguments must be numbers, not `str`.  

The string returned by `__repr__` should be unambiguous and, if possible, match the source code necessary to recreate the object being represented. That is why our chosen representation looks like calling the constructor of the class, e.g. `Vector(3, 4)`.  

Contrast `__repr__` with with `__str__`, which is called by the `str()` constructor and implicitly used by the print function. `__str__` should return a string suitable for display to end-users.  

If you only implement one of these special methods, choose `__repr__`, because when no custom `__str__` is available, Python will call `__repr__` as a fallback.  

##### TIP
Difference between `__str__` and `__repr__` in Python is a StackOverflow question with excellent contributions from Pythonistas Alex Martelli and Martijn Pieters.  

### Arithmetic operators
Example 1-2 implements two operators: `+` and `*`, to show basic usage of `__add__` and `__mul__`. Note that in both cases, the methods create and return a new instance of Vector, and do not modify either operand—`self` or `other` are merely read. This is the expected behavior of infix operators: to create new objects and not touch their operands. I will have a lot more to say about that in Chapter 13.  

##### WARNING
As implemented, Example 1-2 allows multiplying a `Vector` by a number, but not a number by a `Vector`, which violates the commutative property of multiplication. We will fix that with the special method `__rmul__` in Chapter 13.  

### Boolean value of a custom type 自定义类型的布尔值
Although Python has a `bool` type, it accepts any object in a boolean context, such as the expression controlling an `if` or `while` statement, or as operands to `and,` `or` and `not`. To determine whether a value `x` is `truthy` or `falsy`, Python applies `bool(x)`, which always returns `True` or `False`.  

By default, instances of user-defined classes are considered truthy, unless either `__bool__` or `__len__` is implemented. Basically, `bool(x)` calls`x.__bool__()` and uses the result. If `__bool__` is not implemented, Python tries to invoke `x.__len__()`, and if that returns `zero`, `bool` returns `False`. Otherwise `bool` returns `True`.  

Our implementation of `__bool__` is conceptually simple: it returns `False` if the magnitude of the vector is `zero`, `True` otherwise. We convert the magnitude to a boolean using `bool(abs(self))` because `__bool__` is expected to return a boolean.  

Note how the special method `__bool__` allows your objects to be consistent with the truth value testing rules defined in the Built-in Types chapter of the Python Standard Library documentation.  

##### NOTE
A faster implementation of `Vector.__bool__` is this:  

```python
    def __bool__(self):
        return bool(self.x or self.y)
```

This is harder to read, but avoids the trip through `abs`, `__abs__`, the squares and square root. The explicit conversion to `bool` is needed because `__bool__` must return a boolean and or returns either operand as is: `x` or `y` evaluates to `x` if that is `truthy`, otherwise the result is `y`, whatever that is.  

## Overview of special methods
The `Data Model` page of the Python Language Reference lists 83 special method names, 47 of which are used to implement arithmetic, bitwise and comparison operators.  

As an overview of what is available, see Table 1-1 and Table 1-2.  

##### NOTE
The grouping shown in the following tables is not exactly the same as in the official documentation.  

*Table 1-1. Special method names (operators excluded).*  

|category                   |method names                                  |
|---------------------------|:---------------------------------------------|
string/bytes representation |`__repr__`,`__str__`,`__format__`,`__bytes__`
