## Всякая всячина (1)

### Содержание

- [Трейты](#Трейты)



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





