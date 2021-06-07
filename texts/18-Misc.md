## Всякая всячина (2)

### Содержание

- [Тип данных std::optional](#Optional)
- [std::to_array](#to_array)
- [static_assert](#static_assert)

### Optional

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

### to_array

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