# Python 3 tricks

Disclaimer: This list is meant as a comprehension of cool tips and tricks I found on the internet. If you would like to contribute, or notice any mistakes or typos, please contact me or upload a pull request. If you think any material here can be considered personal property let me know and I will take it down.

### 1. Looping over a range of numbers

Using `range()` is better than using a list (ex. `[1, 2, 3]`), because the list takes up memory space, whereas the `range()` function generates values on demand, thus taking a fixed amount of memory whatever the size of the elements is:

```python
for i in range(10):
    print(i**2)
```

takes the same memory space as:

```python
for i in range(100000):
    print(i**2)
```

Note: This function used to create a list in python2, and `xrange()` used to do what `range()` currently does, but it got changed, so `range()` in python3 is `xrange()` in python2.

### 2. Looping backwards

Use the function `reversed()`:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

for lang in reversed(langs):
    print(lang)

# prints rust, kotlin, c++, java, python, c
```

### 3. Looping over a collection and indices

Use the function `enumerate()`:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

for i,lang in enumerate(langs):
    print(f'{i} --> {lang}')

# prints
# 0 --> c
# 1 --> python
# 2 --> java
# 3 --> c++
# 4 --> kotlin
# 5 --> rust
```

### 4. Looping over two collections

Use the function `zip()`. It returns tuples of the elements until one of the iterables is exahusted:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']
numbers = [1, 2, 3]

for number, lang in zip(numbers, langs):
    print(f'{number} --> {lang}')

# prints
# 1 --> c
# 2 --> python
# 3 --> java
```

Note: It takes any number of iterables and "zips" them into tuples.

Note 2: It's important to note that it generates tuples on demand, so it reuses memory space (it used to create a third list in python2, and `izip()` used to do what `zip()` does now in python3).

### 5. Looping in sorted order

Use the function `sorted()` or the method `sort()` of iterables.

By default, it sorts the iterable in ascending order:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

for lang in sorted(langs):
    print(lang)

# or

langs.sort()
print(*langs)

# prints c c++ java kotlin python rust
```

The second method sorts the iterable IN-PLACE, whilst the first returns a different iterable.

Both the functions can take 2 extra parameters which can specify a comparison function and if the iterable should be reversed:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

for lang in sorted(langs, key=len, reverse=True):
    print(lang)

# or

langs.sort(key=len, reverse=True)
print(*langs)

# prints kotlin python java rust c++ c
# notice that kotlin and python might be interchanged because they have the same size, same java and rust
```

### 6. Partial functions

A partial function is a function who has some parameters "frozen", in the sense that they are preset. The other parameters must be given when the partial function is called:

```python
from functools import partial

def func(x, y, z):
    return x + 2*y + 3*z

my_func = partial(func, 2, 3)  # assign (preset) 2 to x, 3 to y
my_func(3)  #   equivalent to func(2, 3, 3)
            #   prints 17
my_func(4)  #   equivalent to func(2, 3, 4)
            #   prints 20
```

### 7. Fastest way to format multiple strings

Going from fastest to slowest, they are:

```python
f'{s} {t}'  # fastest
s + ' ' + t
' '.join((s, t))
'%s %s' % (s, t)
'{} {}'.format(s, t)
Template('$s $t').substitute(s=s, t=t)  # slowest
```

### 8. Iterating until a sentinel value

This method has 2 forms:

1. `iter(iterable)` - this form simply returns an iterator from the iterable. You can call `next()` on the iterator and iterate through the iterable.

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

ir = iter(langs)
print(next(ir)) # prints c
print(next(ir)) # prints python
```

2. `iter(callable, sentinel)` - this form executes the function `callable` until it returns `sentinel` value.

```python
def func(langs = []):
    langs.append('c')
    return len(langs)

ir = iter(func, 5)
next(ir)    # prints 1
next(ir)    # prints 2
next(ir)    # prints 3
next(ir)    # prints 4
next(ir)    # raise StopIteration
```

Read 80 characters from file `f` into `line` and append to `text` until `f.read()` returns `''`:

```python
text = list()

for line in iter(partial(f.read, 80), ''):
    text.append(line)
```

### 9. For else in Python

Search a certain value in an iterable and do something if it is not there:

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

for lang in langs:
    if lang == 'scala':
        print('We found Scala!')
        break
else:
    print('Scala is not in the list...')
```

Note: Careful if you ever come back to this kind of code. Don't indent the `else` statement by accident!!!

### 10. Looping over dictionary keys

```python
d = {
    'foo': 'c',
    'bar': 'java',
    'baz': 'rust'
}

# cannot mutate dictionary here
for k in d:
    print(k)

# free to mutate the keys and values
for k in list(d.keys()):
    if k == 'foo':
        del d[k]
```

Note: `d.keys()` used to make a list copy of the keys, so there was no problem iterating and mutating the original dictionary at the same time. In modern Python3, `d.keys()` returns an iterable and can no longer be used to iterate and mutate a dictionary at the same time. To go around this, just wrap the method into a list as in the example.

Note 2: There is an 'alternative' to this, but it has worse performance and memory usage:

```python
d = {
    'foo': 'c',
    'bar': 'java',
    'baz': 'rust'
}

# Don't do this, performance is bad as it copies every element in a dictionary and can be really bad for really big dictionaries
s = {k: v for k, v in d.items() if k != 'foo'}
```

### 11. Looping over dictionary keys and values

```python
d = {
    'foo': 'c',
    'bar': 'java',
    'baz': 'rust'
}

for k, v in d.items():
    print(f'{k} --> {v}')
```

The `items()` method returns and iterator, so it uses the same amount of memory no matter how big the dictionary is.

Note: In python2, the `items()` method used to return a list of tuples, and the `iteritems()` used to do what `items()` does now in python3.

### 12. Construct a dictionary from 2 iterables

Use the `zip()` method to pack 2 iterables into a zip object, then use the `dict()` method to make that into a dictionary.

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']
colors = ['blue', 'green', 'red']

d = dict(zip(langs, colors))
print(d)    # prints {'c': 'blue', 'python': 'green', 'java': 'red'}

s = dict(enumerate(colors))
print(s)    # prints {1: 'blue', 2: 'green', 3: 'red'}
```

### 13. Populating a dictionary with default data (Counting with dictionary)

Use the `defaultdict()` method imported from `collections`. When a key is not in the dictionary, it creates a new key that has the default value.

```python
from collections import defaultdict

colors = ['red', 'green', 'red', 'blue', 'green', 'red']

d = defaultdict(int)
for color in colors:
    d[color] += 1

print(dict(d))    # prints {'blue': 1, 'green': 2, 'red': 3}
```

Note: This is a faster approach than `setdefault()` on most cases and faster than `get()` in all cases. Also, `defaultdict()` seems to work faster on native types like `int` or `string` and slower on `dict` or `list`. That being said, there are times when you cannot use `defaultdict()` and have to use either `setdefault()` or `get()`, for example when the default value of a certain key depends on the key itself, so `defaultdict()` cannot be used from the beginning to have a default value for every new key.

```python
colors = ['red', 'green', 'red', 'blue', 'green', 'red']

d = {}
for color in colors:
    d[color] = d.setdefault(color, 2 if color == 'red' else 0) + 1

print(d)    # prints {'blue': 1, 'green': 2, 'red': 5}
```

Note 2: A case where `get()` accomplishes nicely what `setdefault()` and `defaultdict()` would do in a more complicated manner is when you have to return a default value from a dictionary if the key is not in it.

```python
d = {
    1: 'Alice',
    2: 'Bob',
    3: 'Carla'
}

def hello(id):
    return f'Hi, {d.get(id, "random person")}'

print(hello(1)) # prints Hi, Alice
print(hello(4)) # prints Hi, random person
```

### 14. Creating a list with n elements

Say you want to create a list with 100 elements of 0. You can just do:

```python
lst = [0] * 100

print(lst)
```

### 15. Ternary operator in Python

This:

```c
#include <stdio.h>

int main() {
    int x;
    int y = 1;

    x = (y == 1 ? 1 : 0);

    printf("%d", x);

    return 0;
}
```

can be written like this in python:

```python
y = 1

x = (1 if y == 1 else 0)

print(x)
```

### 16. Grouping data with dictionaries

Say you want to group the items in a list based on some comparison function, for example `len()`:

```python
from collections import defaultdict

names = ['julia', 'mark', 'thomas', 'rachel', 'alex', 'maria']

d = defaultdict(list)
for name in names:
    key = len(name)
    d[key].append(name)

print(dict(d))    # prints {5: ['julia', 'maria'], 4: ['mark', 'alex'], 6: ['thomas', 'rachel']}
```

All you have to do to group based on some other function is change the `key` to something else.

### 17. Unpacking sequences

```python
p = 'alex', 'blue', 20, 'c'         # same as p = ('alex', 'blue', 20, 'c')

name, color, age, lang = p

print(p)                            # prints a tuple - ('alex', 'blue', 20, 'c')
print(name, color, age, lang)       # prints alex blue 20 c
```

Note: In the same manner, swapping 2 variables in python might the most elegant way out of all the languages:

```python
x, y = 1, 2

# swap x and y
x, y = y, x

print(x, y) # prints 2 1
```

### 18. Concatenating strings

Use the `join()` method to concatenate strings from an iterable.

```python
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

# join the strings from langs, having ', ' as separator
text = ', '.join(langs)

print(text) # prints c, python, java, c++, kotlin, rust
```

### 19. Atomicity of builtin data types

Most (!not all) of the builtin data types methods are implemented using C function calls, so that makes it atomic.

For a better explanaton check [here](https://webcache.googleusercontent.com/search?q=cache:9ATPT7NPHg0J:effbot.org/pyfaq/what-kinds-of-global-value-mutation-are-thread-safe.htm+&cd=4&hl=en&ct=clnk&gl=in).

Also, dictionaries' `popitem()` is atomic, while `pop()` may not, based on the key type (if the key is not a builtin data type, Python has to call that object's `__hash__()` implementation), so better use `popitem()` where atomicity is needed.

```python
d = {
    'foo': 'c',
    'bar': 'java',
    'baz': 'rust'
}

while d:
    key, value = d.popitem()
    print(f'{key} --> {value}')

# prints
# foo --> c
# bar --> java
# baz --> rust

# d is empty at the end
```

Note: If unsure, don't hesitate to use mutexes!

### 20. Linking and overriding dictionaries with defaults

When you have a dictionary that has some default values and you want to override it with another dictionary, use `ChainMap()`. `ChainMap()` has the advantage that it doesn't copy anything, it just "links" the dictionaries, using the initial memory (this also means that any change in the initial dictionary will be reflected in the `ChainMap()` as well).

```python
from collections import ChainMap

defaults = {
    'bar': 'c',
    'foo': 'java'
}

overwritten = {
    'foo': 'rust',
    'barn': 'c++'
}

d = ChainMap(overwritten, defaults)

print(dict(d))    # prints {'foo': 'rust', 'barn': 'c++', 'bar': 'c'}
```

Note: Don't use `copy()` and then `update()`, it is really bad performance-wise and can be replaced in 99% of the cases by a `ChainMap()`.

```python
d1 = {
    'bar': 'c',
    'foo': 'java'
}

d2 = {
    'foo': 'rust',
    'barn': 'c++'
}

# Don't do this!!
d = d1.copy()
d.update(d2)
```

Note 2: For a better example when this is useful, see [this](https://docs.python.org/3/library/collections.html#collections.ChainMap).

### 21. Ordered dictionary

A dictionary is not guaranteed to preserve the order of insertion. It actually optimizes keys for faster lookup. However there is one way to have a dictionary preserve insertion order, using `OrderedDict()` from `collections`.

```python
from collections import OrderedDict

d = OrderedDict()
d['bar'] = 'c'
d['foo'] = 'java'
d['baz'] = 'rust'

print(dict(d))  # prints {'bar': 'c', 'foo': 'java', 'baz': 'rust'}
```

### 22. Using deque instead of a list when updating

Deques (double ended queues) are really fast in python3. They are implemented using doubly-linked lists, so inserting and removing at the end or at the beginning is O(1) complexity. Lists are implemented as normal arrays, so they have to sometimes `realloc()` to accomodate for the number of elements (only sometimes because by default it `realloc()`s more memory at the time than necessary'), so that makes them have O(n) complexity when inserting or removing at the beginning because they have to copy the rest of the elements.

Generally, updating a sequence is MUCH faster when using a `deque()` as opposed to using a `list()` (though keep in mind that accessing a random element in a `deque()` is expensive, whereas accessing a random element in a `list()` is O(1)).

```python
from collections import deque

# Wrong!
langs = ['c', 'python', 'java', 'c++', 'kotlin', 'rust']

del langs[0]
langs.pop(0)
langs.insert(0, 'scala')

# Right!
langs = deque(['c', 'python', 'java', 'c++', 'kotlin', 'rust'])

del langs[0]
langs.popleft(0)
langs.appendleft(0, 'scala')
```

### 23. Temporary contexts

Usually there is the case that code like this is written in other languages:

```python
old_context = getcontext().copy()
getcontext().prec = 50
print(Decimal(355) / Decimal(113))
setcontext(old_context)
```

This can easily be replaced with contexts:

```python
with localcontext(Context(prec=50)):
    print(Decimal(355) / Decimal(113))
```

Other examples:

1. Writing or reading from file

```python
f = open('data.txt')
try:
    data = f.read()
    # do something with data
finally:
    f.close()
```

can be replaced with:

```python
with open('data.txt') as f:
    data = f.read()
    # do something with data
```

2. Deleting a file (getting rid of the try-except-pass idiom):

```python
try:
    os.remove('sometempfile.tmp')
except OSError:
    pass
```

can be replaced with:

```python
from contextlib import suppress

with suppress(FileNotFoundError):
    os.remove('sometempfile.tmp')
```

Note: `suppress()` is a reentrant context manager. More info [here](https://docs.python.org/3/library/contextlib.html#reentrant-context-managers).

3. Using a lock

```python
lock = threading.Lock()

lock.acquire()
try:
    # critical section
finally:
    lock.release()
```

can be replaced with:

```python
lock = threading.Lock()

with lock:
    # critical section
```

Note: For reentrant lock context manager, see [threading.RLock](https://docs.python.org/3/library/threading.html#threading.RLock).

4. Redirecting output from stdout to file

```python
with open('help.txt', 'w') as f:
    sldstdout = sys.stdout
    sys.stdout = f
    try:
        help(pow)
    finally:
        sys.stdout = oldstdout
```

can be replaced with:

```python
with open('help.txt', 'w') as f:
    with redirect_stdout(f):
        help(pow)
```

Note: `redirect_stdout()` is also a reentrant context manager.

More on context managers [here](https://docs.python.org/3/library/contextlib.html).

### 24. Using the cache for optimized function calls

For example, looking up a webpage numerous times is expensive, and usually the result is the same. So use the `lru_cache()` decorator:

```python
from functools import lru_cache

@lru_cache
def web_lookup(url):
    return urllib.urlopen(url).read()
```

More can be found [here](https://docs.python.org/3/library/functools.html#functools.lru_cache).

### 25. Test a sequence or generator for truthness

Using the `any()` function, you can check if at least one value in the iterable is `True`. It makes use of the `bool()` function.

```python
false_lst = [0, False, '', 0.0, [], {}, None]   # all of these return False when using bool() on them

print(any(false_lst))   # prints False


true_lst = [1, True, 'x', 3.14, ['x'], {'a': 'b'}]  # all of these return True when using bool() on them

print(any(true_lst))    # prints True


falst_lst.append(-1)    # any integer different from 0 is considered True

print(any(false_lst))   # prints True
```

Note: This function shortcircuits, meaning the first time it finds `True` it returns; it does **NOT** check for the rest of the values to be `True`.

Note 2: It is really useful with generators:

```python
print(any(range(1000000))   # prints True after 2 values evaluated, as range() is a generator

print(any([range(1000000)]))   # prints True after the whole list of 1000000 elements has been initialized, as range() has to populate the list first
```

### 26. Use namedtuples instead of tuples

Aside from the fact that `namedtuple()`s are more verbose, they also offer better usage, as they can be treated as regular tuples, classes or even dictionaries.

For example, having a point:

```python
pt1 = (2, 3)

print(pt1[0], pt1[1])   # prints 2 3
```

can be replaced with the better alternative `namedtuple()`:

```python
from collections import namedtuple

Point = namedtuple('Point', 'x y')  # a tuple named 'Point' with attributes 'x' and 'y'
# alternatively this means the exact same thing
# Point = namedtuple('Point', ['x', 'y'])

pt1 = Point(2, 3)
print(pt1)                  # prints Point(x=2, y=3)
print(pt1.x, pt1.y)         # prints 2 3
print(pt1[0], pt1[1])       # prints 2 3
print(dict(pt1._asdict()))  # prints {'x': 2, 'y': 3}
print(pt1._replace(x=50))   # prints Point(x=50, y=3)
                            # Note however that _replace() returns a modified copy. The original is still a tuple, so it cannot be modified
```

Another common example:

```python
from collections import namedtuple

Person = namedtuple('Person', 'age color lang')

person = Person(31, 'blue', 'c')
print(person)   # prints Person(age=31, color='blue', lang='c')
```

Note: When the values from a `namedtuple()` are invalid (e.g. having one of the fields named `class` or having the same field twice), it throws a `ValueError`. To avoid this you can possibly provide a third parameter named `rename`. If set to `True`, it will rename the field that is incorrect.

```python
from collections import namedtuple

Person = namedtuple('Person', 'age color age', rename=True)
print(Person(31, 'blue', 'whatever'))   # prints Person(age=31, color='blue', _2='whatever')
``` 
