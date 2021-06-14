## Всякая всячина (2)

### Содержание

- [Тип данных std::optional](#Тип-данных-Optional)
- [Тип данных std::any](#Тип-данных-any)
- [Тип_данных std::variant](#Тип-данных-variant)
- [std::to_array](#to_array)
- [static_assert](#static_assert)
- [using-объявления](#using-объявления)
- [Псевдонимы для типов](#Пресвонимы-для-типов)
- [Проверка существования элемента](#Проверка-существования-элемента)
- [std::span](#stdspan)
- [Библиотека ranges](#Библиотека-ranges)
- [Концепты](#Концепты)
- [Контейнеры unordered](#Контейнеры-unordered)
- [Строковое представление enum](#Строковое-представление-enum)

### Тип данных optional

Для понимания того, зачем нужен новый тип данных **std::optional** необходимо рассмотреть ситуацию, когда внутри функции возникает ошибка и это влияет на возвращаемый результат (или на его отсутствие). Если ошибки нет, то результат определен и может содержать значение. Если возникла ошибка, то возвращается специальное значение.  


```cpp
#include <optional>
#include <string>
#include <iostream>

class Input {

    std::string data = "";
    bool valid = false;
public:
    Input(std::string _data) {
        if(_data!="") {
            data = _data;
            valid = true;
        }
    }
    bool isValid() const {
        return valid;
    }
    std::string asString() const {
        return data;
    }
};

std::optional<std::string> TryParse(Input input)
{
    if (input.isValid())
        return input.asString();

    return std::nullopt;
}

int main() {
    Input input{""};
    std::optional<std::string> result = TryParse(input);
    try {
        std::cout << *result << " " << result.value() << std::endl;
    }
    catch (const std::bad_optional_access& e)
    {
       std::cout << e.what() << "\n"; 
    }
    
    return 0;
}
```

**std::optional** является частью словарных типов C++ на ряду с **std::any, std::variant и std::string_view**.

### Тип данных any

Этот тип позволяет хранить данные любых типов о в объекте и сообщает об ошибке при обращении к неправильному типу.

- **std::any** — не шаблонный класс как **std::optional** или **std::variant**
- по умолчанию он не содержит значения, вы можете это проверить с помощью метода `has_value()`
- вы можете сбросить любой объект с помощью метода `reset()`
- когда мы присваиваем переменной значение нового типа, старое значение и его тип стираются
- вы можете получить доступ с помощью метода `std::any_cast`, который выбросит исключение `bad_any_cast`, если сейчас переменная хранить знаечние не типа «T»
- можно узнать активный сейчас тип с помощью метода `type()`, который возвращает **std::type_info** этого типа

```cpp
std::any a(12);

// можем записать любое значение:
a = std::string("Hello!");
a = 16;
// чтение из переменной:

// мы можем использовать a как число
std::cout << std::any_cast<int>(a) << '\n'; 

// но не как строку:
try 
{
    std::cout << std::any_cast<std::string>(a) << '\n';
}
catch(const std::bad_any_cast& e) 
{
    std::cout << e.what() << '\n';
}

// сбросим и проверим содержит ли наша переменная какое-то значение:
a.reset();
if (!a.has_value())
{
    std::cout << "a is empty!" << "\n";
}
```

Ключевым для безопасности std::any является отсутствие утечки ресурсов. Для достижения этой цели std::any уничтожит любой активный объект перед тем, как присваивать новое значение.

### Тип данных variant

Этот тип представляет из себя шаблон-объединение.

```cpp
#include <variant>
#include <string>
#include <cassert>
 
int main()
{
    std::variant<int, float> v, w;
    v = 12; // v  содержит число
    int i = std::get<int>(v);
    w = std::get<int>(v);
    w = std::get<0>(v); // тоже самое, что и в предыдущей строке
    w = v; // тоже самое
 
//  std::get<double>(v); // error: no double in [int, float]
//  std::get<3>(v);      // error: valid index values are 0 and 1
 
    try {
      std::get<float>(w); // w содержит int, не float: выброс исключения
    }
    catch (const std::bad_variant_access&) {}
 
    using namespace std::literals;
 
    std::variant<std::string> x("abc");
    x = "def"; 
 
    std::variant<std::string, void const*> y("abc");
    assert(std::holds_alternative<void const*>(y)); // succeeds
    y = "xyz"s;
    assert(std::holds_alternative<std::string>(y)); // succeeds
}
```

### to_array

Данная функция из стандартной библиотеки пытается создать массив (std::array) из объекта.

```cpp
#include <iostream>
#include <array>

int main() {
    std::to_array("foo"); // returns `std::array<char, 4>`
    std::to_array<int>({1, 2, 3}); // returns `std::array<int, 3>`

    int a[] = {1, 2, 3};
    std::to_array(a); // returns `std::array<int, 3>`
}
```

Компиляция в unix-системах:

`g++-10 -std=c++2a file.cpp`

### static_assert

В "классическом" C++ имеется макрос **assert**, вставляющий в текст программы условие и прерывающий ее выполнение, если условие ложное.

В C++11 добавили еще один тип assert-а — **static_assert**. В отличие от **assert**, который срабатывает во время выполнения программы, **static_assert** срабатывает во время компиляции, вызывая ошибку компилятора, если условие не является истинным. Если условие ложное, то выводится диагностическое сообщение.

```cpp
static_assert(sizeof(int) == 4, "int must be 4 bytes");
```

В случае, если размер int равен 4, компиляция продолжается, если нет, - прерывается с выдачей диагностического сообщения

### using-объявления

При работе с пространствами имен возникает необходимость импорта не всех имен, а только нужных. Для этой цели подойдут **using**:

```cpp
#include <iostream>
int main()
{
   using std::cout;         // "using-объявление" сообщает компилятору, что cout следует обрабатывать, как std::cout
   cout << "Hello, world!"; // теперь используем без префикса
   return 0;
}
```

Строка **using std::cout;** сообщает компилятору, что мы будем использовать объект `cout` из пространства имен `std`. 

### Псевдонимы для типов

```cpp
using time_t = double; // используем time_t в качестве псевдонима для типа double
```

### Проверка существования элемента

```cpp
std::map<int, char> map {{1, 'a'}, {2, 'b'}};
map.contains(2); // true
map.contains(123); // false

std::set<int> set {1, 2, 3};
set.contains(2); // true
```

### std::span 

Часто нужно получить доступ к элементам контейнера (например, выполнить перебор), но создавать копию при передаче в функцию слишком затратно. **span** позволяет сконструировать дешевый **view** объект, без права владения данными.

```cpp
void f(std::span<int> ints) {
    std::for_each(ints.begin(), ints.end(), [](auto i) {
        // ...
    });
}

std::vector<int> v = {1, 2, 3};
f(v);
std::array<int, 3> a = {1, 2, 3};
f(a);
```

Рекомендация по использованию **span**:

Используйте `span<T>` (соответственно, `span<const T>` ) вместо отдельно стоящего `T*` (соответственно `const T*`), для которого у вас есть значение длины. Итак, замените такие функции, как:

```
void read_into(int* buffer, size_t buffer_size);
```    
    
на
    
```
void read_into(span<int> buffer);
```    
    
### Библиотека ranges

**Ranges** (диапазоны) являются абстракцией для диапазона элементов, с которыми нужно выполнить какие-то операции. Например, отсортировать. Диапазоны основаны на итераторах.
    
Ключевой особенностью **std::views** является концепция **ленивых вычислений**, то есть объект не будет вычислен до тех пор, пока он не понадобится
    
    
```cpp    
#include <iostream>
#include <vector>
#include <ranges>

int main() {
    std::vector vec{1, 2, 3, 4, 5, 6};
    auto v = std::views::reverse(vec);
    std::cout << *v.begin() << '\n';
}
```
    
```cpp
std::vector vec2{1, 2, 3, 4, 5, 6};
auto v2 = vec2 | std::views::reverse | std::views::drop(2);
for(auto i: v2)
   std::cout << i << ", ";  // 4,3,2,1
```

Пример с преобразованием:

```cpp
#include <iostream>
#include <ranges>
#include <vector>

int main() {

    std::vector<int> numbers = {1, 2, 3, 4, 5, 6};
  
    auto results = numbers | std::views::filter([](int n){ return n % 2 == 0; })
                           | std::views::transform([](int n){ return n * 2; });
                           
    for (auto v: results) std::cout << v << " ";     // 4 8 12

}
```
### Концепты

**Концепт** — новая языковая сущность на основе синтаксиса шаблонов. У концепта есть имя, параметры и тело — предикат, возвращающий константное (т.е. вычисляемое на этапе компиляции) логическое значение, зависящее от параметров концепта.

Формальное определение:

```
template < template-parameter-list >
concept concept-name = constraint-expression;
```

Пример:

```cpp
template<int I> 
concept Even = I % 2 == 0;  

template<typename T>
concept FourByte = sizeof(T)==4;
```

Вот пример использования более сложного концепта:

```cpp
template <typename T>
concept integral = std::is_integral_v<T>;

template <typename T>
concept signed_integral = integral<T> && std::is_signed_v<T>;
```

Интегральный тип:

- bool, char, char8_t, char16_t, char32_t, wchar_t, short, int, long, long long, 
- псевдонимы описанных выше типов


с концептами тесно связано использование ключевого слова **requires** 

Вот пример, в котором создается процедура сравнения двух значений:

```cpp
#include <type_traits>

template <class T>
T Abs(T x) {
    return x >= 0 ? x : -x;
}

// вариант для чисел с плавающей точкой
template<class T>
requires(std::is_floating_point_v<T>)
bool AreClose(T a, T b) {
    return Abs(a - b) < static_cast<T>(0.000001);
}

// вариант для других объектов
template<class T>
bool AreClose(T a, T b) {
    return a == b;
}
```


### Контейнеры unordered

Неупорядоченные ассоциативные контейнеры были добавлены в STL и теперь существуют там наряду с "классическими" ассоциативными контейнерами. Суть их добавления была в том, чтобы создать набор контейнеров, основанных на хеш-таблицах, а не на деревьях.

Представлены:

- unordered_set
- unordered_map
- unordered_multiset
- unordered_multimap

### Строковое представление enum

```cpp
enum class rgba_color_channel { red, green, blue, alpha };

std::string_view to_string(rgba_color_channel my_channel) {
  switch (my_channel) {
    using enum rgba_color_channel;
    case red:   return "red";
    case green: return "green";
    case blue:  return "blue";
    case alpha: return "alpha";
  }
}
```
