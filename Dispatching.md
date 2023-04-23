# Function overloading

## In Ada
Compile with `gnatmake overloading.adb`
```Ada
procedure overloading is

  function add_delta(x : integer) return integer is
    begin
      return x + 1;
    end;

  function add_delta(x : float) return float is
    begin
      return x + 0.1;
    end;

  test_int   : integer := 0;
  test_float : float   := 0.0; 

begin

  test_int   := add_delta(test_int);
  test_float := add_delta(test_float);

end overloading;
```

## In C
Compile with `gcc overloading.c`

```C
int add_delta(int x){
  return x + 1;
}
float add_delta(float x){
  return x + 0.1;
}

int main(){

  int test_int   = 0;
  int test_float = 0.0;

  test_int   = add_delta(test_int);
  test_float = add_delta(test_float);
}

```
It does not compile:
```
overloading.c: In function ‘main’:
overloading.c:6:9: error: conflicting types for ‘add_17’
    6 |   float add_17(float x){
      |         ^~~~~~
overloading.c:3:7: note: previous definition of ‘add_17’ was here
    3 |   int add_17(int x){
      |       ^
```
**No Static Dispatch in C**.


## In C++
Comile with `g++ overloading.c`



**Static Dispatch as expected in C++**
## In Python
```Python
def add_delta(x):
  return x + 1

def add_delta(x):
  return x + 0.1
```
This does not work.

Try with type hints:
```Python
def add_delta(x : int):
  return x + 1

def add_delta(x : float):
  return x + 0.1

test_int   = 0
test_float = 0.0

test_int   = test_int + add_delta(test_int)
test_float = test_float + add_delta(test_float)
print(test_int)
print(test_float)
```
As type hints have no effect, this does not work.

With dynamic typing, it is impossible.

**No Static Dispatch in Python**

# Dynamic Dispatch
## In Python
Python requires classes.
```Python
class MyInt:
  def __init__(self):
    self.value = 0
  def add_delta(self):
    self.value = self.value + 1

class MyFloat:
  def __init__(self):
    self.value = 0.0
  def add_delta(self):
    self.value = self.value + 0.1

test_int   = MyInt()
test_float = MyFloat()

test_int.add_delta()
test_float.add_delta()

print(test_int.value)
print(test_float.value)
```
## Be Dynamic

```Python
import random

def create_value():
  if random.random() < 0.5:
    return MyInt()
  else:
    return MyFloat()

test = create_value()
test.add_delta()
print(test.value)
```
Only for the the argument `self` it works as expected.

**Single Dynamic Dispatch with classes in Python**

## In C, with C++ compiler
Comile with g++ overloading.c
```C
#include <stdlib.h>
#include <time.h>

int add_delta(int* x){
  return x + 1;
}

float add_delta(float* x){
  return x + 0.1;
}

void* create_value(){
  if(rand()%2)
    return new int(0);
  else
    return new float(0.0);
}

int main(){
  time_t seed;
  srand((unsigned) time(&seed))
  void* test = create_value();
  add_delta(test)
  free(test)
}
```
It does not compile.
```
overloading.c: In function ‘int main()’:
overloading.c:19:17: error: no matching function for call to ‘add_delta(void*&)’
   19 |   add_delta(test);
      |                 ^
overloading.c:3:5: note: candidate: ‘int add_delta(int*)’ (near match)
    3 | int add_delta(int* x){
      |     ^~~~~~~~~
overloading.c:3:5: note:   conversion of argument 1 would be ill-formed:
overloading.c:19:13: error: invalid conversion from ‘void*’ to ‘int*’ [-fpermissive]
   19 |   add_delta(test);
      |             ^~~~
      |             |
      |             void*
overloading.c:6:7: note: candidate: ‘float add_delta(float*)’ (near match)
    6 | float add_delta(float* x){
      |       ^~~~~~~~~
overloading.c:6:7: note:   conversion of argument 1 would be ill-formed:
overloading.c:19:13: error: invalid conversion from ‘void*’ to ‘float*’ [-fpermissive]
   19 |   add_delta(test);
      |             ^~~~
      |             |
      |             void*
```
As it is not known at compile time, it does not work.

**In general no Dynamic Dispatch in C++**

# Summary

Concepts are important.

Programming languages work against you.

## Static Dispatch
* Function Overloading
* Ada
* C++
* Not In Python

## Dynamic Dispatch
* Polymorphism
* Object Oriented Programming is the way how programming languages provide Dynamic Dispatching.
* Python, Java, C++, ...



# Further Information

## Consequences
### Workarounnds for Static Dispatch
Through appropriate naming of functions.
```C
int add_delta_to_int(int x);
float add_delta_to_float(float x);
double add_delta_to_double(double x);
...
int test_int = 0;
test_int = add_delta_to_int(test_int);
```
### Working with Pointers and Casts
```C
void* test = new int(0);
*((int*)test) = add_delta((int*)test);
```
### Inappropriate Design Decisions
* Using Object Oriented Programming where this is not recommended.
### Extra Code and Complicated Design Patterns
```C
#include <stdlib.h>
#include <stdio.h>
#include <time.h>

int add_delta(int* x){
  return *x + 1;
}
float add_delta(float* x){
  return *x + 0.1;
}

enum DynamicType{INTEGER, FLOAT};

struct TypedValue {
  DynamicType type;
  void* ptr;
};

TypedValue create_value(){
  struct TypedValue result;
  if(rand()%2){
    result.type = INTEGER;
    result.ptr = new int(0);
  }
  else{
    result.type = FLOAT;
    result.ptr = new float(0.0);
  }
  return result;
}

int main(){
  time_t seed;
  srand((unsigned) time(&seed));

  struct TypedValue test = create_value();
  if(test.type == INTEGER)
    *((int*)test.ptr) = add_delta((int*)test.ptr);
  if(test.type == FLOAT)
    *((float*)test.ptr) = add_delta((float*)test.ptr);

  if(test.type == INTEGER)
    printf("%d", (*(int*)test.ptr));
  if(test.type == FLOAT)
    printf("%f", (*(float*)test.ptr));

  free(test.ptr)
}
```
## Dynamic Dispatch, Object Oriented Programming, Polymorphy in C++
```C++
#include <stdlib.h>
#include <stdio.h>
#include <time.h>

int add_delta(int* x){
  return *x + 1;
}
float add_delta(float* x){
  return *x + 0.1;
}

class MyType{
  public:
    virtual void add_delta() = 0;
};

class MyInt : public MyType{
  public:
    int value;
    MyInt() : value(0){}
    virtual void add_delta(){
      value = value + 1;
    }
};

class MyFloat : public MyType{
  public:
    float value;
    MyFloat() : value(0.0){}
    virtual void add_delta(){
      value = value + 0.1;
    }
};

void printMyType(MyType* x){
  MyInt* x_as_int = dynamic_cast<MyInt*>(x);
  if(x_as_int)
    printf("%d", x_as_int->value);
  MyFloat * x_as_float = dynamic_cast<MyFloat*>(x);
  if(x_as_float)
    printf("%f", x_as_float->value);
}

MyType* create_value(){
  if(rand()%2)
    return new MyInt();
  else
    return new MyFloat();
}

int main(){
  time_t seed;
  srand((unsigned) time(&seed));

  MyType* test = create_value();
  test->add_delta();

  printMyType(test);

  delete(test);
}
```
## Alternatives
> Cambrige Dictionary:
>
> A "method" is a way of doing something.


> Wikipedia
>
> A method in object-oriented programming (OOP) is a procedure associated with a message and an object.

Object Oriented languages regards a method as a function, with single dispatching.

For some traditional reasons the syntax for methods is classes and objects. The single dispatching argument is the `this` pointer in Java and C++ and the `self` object in Python.

In other programming languages (Lisp, Dylan, Julia), a method is a more general concept: A function with dispatching in all arguments. A method represents a modular part of the behavior of a generic function.
