## Исключения. Преобразования типов

###  Традиционная обработка ошибок

Техники обработки ошибок

- прекратить выполнение
- возвратить значение «ошибка»
- возвратить допустимое значение и оставить программу в ненормальном состоянии
- вызвать функцию обработки ошибок

_Вариант 1. Прекратить выполнение программы_

```cpp
char Stack::pop()
{
  assert( top != 0 );
  return store[--top];
}
```

Результат:

```
stack_assert: simple_stack.cpp:61:
char Stack::pop (): Assertion ‘top != 0’ failed.
Abort (core dumped)
```

_Вариант 2. Возвратить ошибку_

```cpp
char Stack::pop() {
  return top ? store[--top] : 0;
}
```

Или так:

```cpp
enum tRC {
  OK,
  BAD_SIZE,
  OVERFLOW,
  UNDERFLOW
};

tRC Stack::pop(char* c)
{
  if (!top) return UNDERFLOW;
  *c=store[--top];
  return OK;
}

char Stack::pop(tRC* rc) {
  if (top) { 
    *rc=OK;
    return store[--top];
  }
  *rc=UNDERFLOW;
  return 0;
}
```

Проблемы:

- Может не быть подходящего значения
- Результат каждого вызова должен проверяться на «ошибку»


_Вариант 3. Оставить программу в ненормальном состоянии_

```cpp
char Stack::pop() {
  if (top!=0) { 
   return store[--top];
  }
  g_Error=UNDERFLOW;
  return 0;
}
 int main()
{
  Stack stack;
  char c = stack.pop();
  if (g_Error != OK)  {
    // error occured
  }
  else  {
    // OK
  }
}
```

_Вариант 4. Вызвать функцию обработки ошибок_

```cpp
void* new (size_t size)
{
  for(;;)
  {
    if (void* p = malloc(size))
      return p;
    if (!find_memory_somewhere())
      return 0;
  }
}
```


### Исключения

Механизм исключений (**exceptions**):

- Генерация сообщения об ошибке (**throw**)
- Перехват этих сообщений (**catch**)

В программе может одновременно существовать только одно исключение.

```cpp
class Stack {
public:
  Stack(int size);
  void push(char c);
  //...
};
class Overflow {};

class WrongSize {
public:
  int wsize;
   Wrong_size(int i):
         wsize(i) {}
};
void Stack::push(char c) {
 if (top<maxSize)
     storage[top++] = c;
 else
    throw Overflow();
}
void f() {
  try {
    Stack s(10);
    s.push('a');
  } catch (Overflow ex)  { 
       cerr << "Stack overflow";
  }
}
```

#### Выбор исключений

```cpp
Stack::Stack(int size)
{
  if ( size > 10000) {
    throw WrongSize(size);
  } //...
}
char Stack::pop()
{
  if (top==0)
    throw Underflow();
  
  return storage[--top];
}
void f(unsigned int size)
{
  try {
     Stack s(size);
     s.push('a');
     char c = s.pop();
     char d = s.pop();
  }
  catch (WrongSize ws) {
    cerr << "Wrong size:" << ws.wsize;
  }
  catch (Overflow) { /*...*/
  }
  catch (Underflow) { /*...*/
  } 
}
```

#### Группировка исключений

```cpp
class Exception {};

class StackError: public Exception {};

class Overflow:  public StackError {};
class Underflow: public StackError {};
class WrongSize: public StackError {};
...
try {
     Stack s(size);
     // ...
}
catch (WrongSize size_exc) {
   // process wronsize exception
}
catch (StackError) {
   // general processing
}
```

#### Перехват исключений

![](./img/exceptions.png)






