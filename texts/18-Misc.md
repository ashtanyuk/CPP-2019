## Всякая всячина (2)

### Содержание

- [Тип данных std::optional](#Optional)
- [std::to_array](#to_array)

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


