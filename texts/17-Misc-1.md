## Всякая всячина (1)

### Содержание

- [Константные if](#if-constexp)
- [Трейты типов](#Трейты-типов)
- [Pairs and Tuples](#Pairs-and-tuples)
- [Структурные связывания](#Структурные-связывания)


### if constexp

Данное средство позволяет вычислять оператор **if** в процессе построения программы, что дает возможность организовать условную компиляцию (аналог препроцессора).

```cpp
template <typename T> 
auto get_value(T t)
{
   if constexpr (std::is_pointer_v<T>) 
     return *t;
   else
     return t;
}
```


### Трейты типов

**Трейты типов (характеристики типов)** - это шаблонный механизм, позволяющий анализировать типы.


```cpp
#include <iostream>
#include <type_traits>

class Class {};

int main() 
{
    std::cout << std::is_floating_point<Class>::value << '\n';  // 0
    std::cout << std::is_floating_point<float>::value << '\n';  // 1
    std::cout << std::is_floating_point<int>::value << '\n';    // 0
}
```

Проверки происходят во время построения программы.

Как устроены трейты?

В результате компиляции будут сгенерированы следующие структуры:

```cpp
struct is_floating_point_Class {
    static const bool value = false;
};

struct is_floating_point_float {
    static const bool value = true;
};

struct is_floating_point_int {
    static const bool value = false;
};
```
Обращения к ним во время выполнения приведут к печати нужных значений.

С помощью трейтов можно проверять параметры шаблонов, проводить условную компиляцию (без использования препроцессора)

```cpp
if constexpr(std::is_signed<T>::value)
        algorithm_signed(t);
else if constexpr (std::is_unsigned<T>::value)
        algorithm_unsigned(t);
```

Табличка полезных трейтов:

<img src="img/traits.png" width="50%" height="50%">

Более удобная запись трейтов типов появилась в стандарте **C++14**:

```cpp
std::is_pointer_v<T> // вместо std::is_pointer<T>::value
```

### Pairs and tuples

Пары и кортежи появились в С++ в разное время, но завоевали определенную известность.

Например, при работе с контейнером **map** используем пары:

```cpp
std::pair<std::string,std::string> key;
edMap.insert(make_pair(key,d));

// или так

std::pair<std::string,std::string> key;
edMap[key] = d;
```

При просмотре содержимого **map** пары очень полезны:

```cpp
for (auto it=mymap.begin(); it!=mymap.end(); ++it)
    std::cout << it->first << " => " << it->second << '\n';
```


Еще один пример использования **pair**, когда из функции необходимо вернуть 2 значения:

```cpp
template <typename T>
std::pair<bool, T> parse_string(const std::wstring &s)
{
    std::wistringstream iss(s);
    T t;
    bool success = !(iss >> t).fail();
    return std::make_pair(success, t);
}

auto r1 = parse_string<int>(L"123");
if (r1.first)
    cout << r.second;
else
    cout << "fail";
```

Теперь о кортежах.

Их также можно использовать для возвращения из функции набора значений:

```cpp
std::tuple<int, int, double> foo(int a, int b) {
  return std::make_tuple(a+b, a*b, double(a)/double(b));
}
```

Традиционный способ получения значения кортежа:

```cpp
t.get<n>();
// или
get<n>(t);
```

по номеру элемента **n**.

В стандарте С++17 появилось несколько упрощений при работе с кортежами.

Во-первых, стало проще их формировать:

```cpp
std::tuple<int, int, int, int> foo(int a, int b)    {
    return {a + b, a - b, a * b, a / b};
}
```

Во-вторых, появились **структурные связывания**, которые упрощают получение элементов:

```cpp
auto [add, sub, mul, div] = foo(5,12);
```

При работе с **lvalue** ссылками нужно использовать **std::tie**:

```cpp
std::tuple<int&, int&> minmax( int& a, int& b ) {
  if (b<a)
    return std::tie(b,a);
  else
    return std::tie(a,b);
}
```


### Структурные связывания

Связывания (bindings) уже упоминались выше. Теперь можно привести полезные примеры их использования

```cpp
std::map<std::string, int> m;
...
for (auto const& [key, value] : m) {
    std::cout << "The value for " << key << " is " << value << '\n';
}
```








