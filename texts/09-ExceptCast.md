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






