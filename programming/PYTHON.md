# Python

## 1. Functional & variable.

![](img/python_1.png)

- In python memory manage by Python memory manager.

```python
a = 100
print(hex(id(a))) # Show memory 0x1014aa6d8
```

- Reference count:

![](img/python_2.png)


```python
a = [1,2,3]
print(sys.getrefcount(a)) # 2
```

- Garbage collection:
    - Circular reference, GC can detect and reclaim circular reference.
    - For python < 3.4: __del__ can cause order of circular reference important.
    
- Dynamically and statically type:  
  - Dynamically language type is interpret during runtime
  - Statically language type is interpret during compile time

- Re assignment variable: in pythons reassign create new variable and assign variable to it

![](img/python_3.png)

- Object mutabability:
  - Object can change internal state
  - Reference to object is not changed 

![](img/python_4.png)

![](img/python_5.png)

<br/>

- Immutable object are safe from passing through function.

```python

def func(x):
    x = x + "data"
    return x

my_x = "test_"
func(my_x)
my_x # test_ (string is immutable)

```

<br/>

- Share reference:
  - Immuatable object can share referenece

<br/>

- Variable equality:

    ```python
    a = [1,2,3]
    b = [1,2,3]
    print(b is a) # False
    print(b == a) # True

    c = "TEST"
    d = "TEST"

    print(c is d) # True
    print(c == d) # True

    ```


<br/>
<br/>

- Class, type, function in python is objects.
  - They have memory address.
  - Function can be pass to function
  - Function is first class citizen in python

<br/>
<br/>

- Python optimization, interning:
  - Interning meaning reuse object-on demand.
  - At startup, Python (CPython), pre-loads (caches) a global list of integers in the range [-5, 256].

![](img/python_6.png)

<br/>

- Python optimization, peepphole:
  -  Constant expression: 12 * 60 will be precalculated
  -  Membership test: mutable is replace by immutable counter part, because check membership in **set** is much faster, for example:

```python
if e in [1,2,3]:
    # Will convert to 
if e in (1,2,3):
```


## 2. Numeric data type

- Python type:
  - int
  - float
  - complex

## 3. Function parameters
- Parameter vs arguments
- Default values:
  
```python 

def afunc(a = 1000):
  # code


# ERROR
def bfunc(a, b=100, c):
  # Every parameter after default paramter must have default value
```

- Keyword argument:
  - But once you use a named argument, all arguments thereafter must be named too

```python

def some_func(a=1, b=2):
  # code

my_func(c=1, 2, 3):
  # Error

```

## 4. Iterables

- Note:

```python
a = 1 # is not a tuple
b = (1,) # is a tuple
c = () # empty tuple
```

- Unpack with iterables:

```python
for e in any_iterables:

```

- Swap in python: this is work because in python the right hand side is evaluate and return.
```python 
a,b = b,a
```

- The use case of * 
```python 
a,b = arr[0], arr[1:]
a,*b = arr # the same

# Unpack with *
l1 = [1,2,3]
l2 = [4,5,6]
l = [*l1, *l2]

# Unpack with unorder data structure do not make sense

# Unpack dict

d1 = { p 1, y 2 }
d2 = { t 3, h 4}
d3 = { h 5, o 6, n 7 }
d = { **d1, **d2, **d3 }
```

- The * operation can only use once the the left hand side.

```python
a, *b, *c = [1,2,3,4] # this is error, SyntaxError: multiple starred expressions in assignment
a, *b, (c, *d) = [1,2,3, [4,5,6] ] # this is ok
```


## 5. *args and *kwargs

- You cannot add more positional arguments after *args
```python

def some_func(a, *args):
    print(a)
    print(args) # print tuple
    
some_func(100, 'a', 'b', 'c')

100
('a', 'b', 'c')

def func(a, b, *c ,d = 100):
    print(a)
    print(b)
    print(c)
    print(d)
    

func(1, 2, 3, 4, 5, d= 10)

```

- **kwargs:
  - *args is used to scoop up variable amount of remaining positional arguments > tuple
  - The parameter name args is arbitrary - * is the real performer here
  - **kwargs is used to scoop up a variable amount of remaining keyword arguments > dictionary
  - Nothing can came after **kwargs
  
```python

def kw_func(a, b, **kwargs):
    print(a)
    print(b)
    print(kwargs)

kw_func(10, 20, v=100, d=30)

10
20
{'v': 100, 'd': 30}
```

- Positional arguments: 
  - specific may have default values 
  - *args
  - can have keyword arguments after that.
  
- Keyword arguments:
  - after positional arguments have been exhausted
  - **kwargs: must be at the end of function

## 6. First class function
- Can be pass as a object in Python.
- Docstring of function is the first comment string in function, store in __doc__ attribute of function
- Function annotation, store in the __annotations__ of function.

```python
def my_func(a: <expression>, b: <expression>) <expression>:
  pass

def my_func(a: 'a string', b: 'a positive integer') -> 'a string':
  return a + b

def my_func(a str,  b 'int > 0') -> str:
  return a*b

```

- Anonymous function using lambda:
```python 
def func(x, fm):
  return fn(x)
func(3, lambda x: x**2)
```
- Limit of lambda:
  - Limit to single expression
  - No assignment, no annotations

- Lambda and sorted:

```python
l = ['B', 'D', 'a', 'c']
print(sorted(l, key=lambda x: x.upper())) # ['a', 'B', 'c', 'D']


m =  { 'a': 30, 'b': 12, 'c': 24 }
print(sorted(m, key=lambda x: m[x])) # ['b', 'c', 'a']

```

- Randomize a list using sorted

```python 

# Randomize an iterable using sorted
import random

N = 10
a = [n for n in range (10)]
ran_a = sorted(a, key=lambda x : (random.random() * 10 % N) )
print (ran_a) # [6, 1, 8, 3, 5, 4, 0, 2, 7, 9]
```
- Using **dir** to introspect function

- **Callable**: are object that can be call using ()
  - Using **callable** method to check if object is callable
  - Callable type in python:
    - method, function
    - lambda
    - class with constructor
    - class instance that implement __call__

```python 
print(callable(print))

class MyClass:
    def __call__(self):
        pass


print(callable(MyClass)) # true

a = MyClass()

print(callable(a)) # true
```

- zip in python
```python 
a = ("John", "Charles", "Mike")
b = ("Jenny", "Christy", "Monica")
x = zip(a, b)
```

- list comprehendsion
```python
newlist = [expression for item in iterable if condition == True]
```

- reduce function in python:
```python
functools.reduce(function, iterable[, initializer])

numbers = [0, 1, 2, 3, 4]
r = reduce(lambda x , y : x + y , numbers, 0)
print(r) # 10

```

- partial fucntion python:
```python
def pow(base, exponent):
  return base ** exponent

square = partial(pow, exponent=2)
```

## 7. Global and local scoping
- There is no concept of truly global scope in Python.
- The only exceptions are built-in global variable.
- Scope are essentially module scope, it spans a single file.
- Using __global__ in function to get global scope in Python


- Closure in python: closure in python meaning that the inner function have the ability to access outer function data even, the outer function is returned.
```python
def outer_function(outer_variable):
    def inner_function():
        print(outer_variable)  # Accessing outer_variable from the outer function

    return inner_function

closure = outer_function("I am outside!")
closure()  # Output: "I am outside!"
```
- Python create an intermediary object for this.

- Closure applications:
  - Use as a memo;
```python 
def averager():
    numbers = []
    def add(number):
        numbers.append(number)
        total = sum(numbers)
        count = len(numbers)
        return total / count
    return add

a = averager()
a(10) # 10
a(20) # 15
a(30) # 20
```
  - Using as counter:
```python
def counter(init_val):
  def inc(increment=1):
    init_val = init_val + increment
    return init_val
  return inc
```

- Decorator function:
  - Take input of a function and return closure 
  - Python convenient way of declare decorator:
  
```python 
@counter
def add(a, b):
  return a+b
```

- Example of decorators:

```python 
# 1. Cache

def fib():
    cache = {1: 1, 2: 2}
    
    def calc_fib(n):
        if n not in cache:
            print('Calculating fib({0})'.format(n))
            cache[n] = calc_fib(n-1) + calc_fib(n-2)
        return cache[n]
    
    return calc_fib

# 2. Logged 
def logged(fn):
    from functools import wraps
    from datetime import datetime, timezone
    
    @wraps(fn)
    def inner(*args, **kwargs):
        run_dt = datetime.now(timezone.utc)
        result = fn(*args, **kwargs)
        print('{0}: called {1}'.format(fn.__name__, run_dt))
        return result
        
    return inner
    
@logged
def func():
    print('Do some stupid shits')
    
func()
# Do some stupid shits
# func: called 2023-05-21 14:21:53.143483+00:00

```

- Parameterize decorators