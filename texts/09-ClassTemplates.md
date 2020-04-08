## Шаблоны классов

## Введение

Ранее, мы изучали шаблоны функций. По такому же принципу строятся **шаблоны классов**. 

```cpp
template <typename T> 
class MyType
{
  private:
     T value;
  public:
     void setValue (const T& newValue) { value = newValue; } 
     T& setValue()                     { return value;     }
};
```

На основе шаблона класса инстанцируются классы, которые, в свою очередь, используются для создания объектов.

```cpp
...
MyType<int> a;
a.setValue(5);
cout<<a.getValue()<<endl;
...
```

Давайте рассмотрим в качестве примера шаблон класса **Stack**:

```cpp
template<class T> 
class Stack {
public:
  Stack(int size);
  ~Stack();
  Stack(const Stack&);
  
  void         push(T);
  T            pop();
  
  Stack& operator= (const Stack&);

private:
  int size;
  int top;
  T  *store;
};
```

Описания методов шаблонного класса можно писать в пределах области класса, а можно выносить за эту область:

```cpp
// Constructor
template<class T> 
Stack<T>::Stack(int _size) :
    size(_size), top(0)
{
    store = new T[size];
}

// Destructor
template<class T> 
Stack<T>::~Stack()
{
    delete[] store;
}
// Method to put a character onto the stack
template<class T> 
void Stack<T>::push(T value)
{
    if (top<size)
        store[top++] = value;
}

// Method to get the upper element from the stack
// Returns:
//   - upper character, if stack is not empty,
//   - 0 (zero), if stack is empty.
template<class T> 
T Stack<T>::pop()
{
  return top ? store[--top] : 0;
}

```

**ВАЖНО!**

В любом случае, описание самого шаблонного класса и описание методов должно помещаться внутрь заголовочных файлов. Такого "классического" разделения на .h и .cpp здесь нет, поскольку описание шаблона не может быть ЕДИНИЦЕЙ ТРАНСЛЯЦИИ, в отличие от описания методоы обычного класса.


## Параметры шаблонов

У шаблонов классов может быть несколько параметров:

```cpp

template <typename T1, typename T2> class MyPair
{
  private:
    T1 value1;
    T2 value2; 
  public:
    MyPair (const T1& val1, const T2& val2)
    {
       value1 = val1;
       value2 = val2; 
    }
    ...
};
...
MyPair <int,char> pair(5,'a');
```

Могут быть параметры различных типов:

```cpp
template<class T, int size> 
class Buffer {
  public:
     T* get(int i) {
           return (i>=0 && i<size) ? &array[i] : 0;
     } 
  private:
     T array[size];
};
```

Инстанцирование выполняется с указанием значения параметра

```cpp
Buffer<char, 128> buf;
```

В шаблонах допускается использование различных видов параметров

```cpp
template
< class T1, // параметр-тип
  typename T2, // параметр-тип
  int I, // параметр обычного типа
  T1 DefaultValue, // параметр обычного типа
  template< class > class T3,// параметр-шаблон
  class Character = char // параметр по умолчанию 
>
```

Вот пример

```cpp
template< class Type,
template< class > class Container >
class CrossReferences {
    Container< Type > mems;
    Container< Type* > refs;
    /* ... */
};
  CrossReferences< Date, vector > cr1;
  CrossReferences< string, set > cr2;
```


## Специализация шаблонов

Рассмотрим специализацию на следующем примере:

```cpp
#include < iostream >
using namespace std;

template < class T > 
class Bag { 
  T* elem;
  int size;
  int max_size; 
public:
  Bag() : elem(0), size(0), max_size(1) {}
  void add(T t) 
  {
     T* tmp;
     if (size + 1 >= max_size) {
        max_size *= 2;
        tmp = new T [max_size];
        for (int i = 0; i < size; i++)
           tmp[i] = elem[i]; 
        tmp[size++] = t; 
        delete[] elem;
        elem = tmp;
      }
      else
        elem[size++] = t;
  }
  void print() {
     for (int i = 0; i < size; i++) 
       cout << elem[i] << " ";
     cout << endl;
  }
};
template <class T> 
class Bag<T*> { 
  T* elem;
  int size;
  int max_size; public:
  Bag() : elem(0), size(0), max_size(1) {} 
  void add(T* t) 
  {
    T* tmp;
    if (t == NULL) { // Check for NULL
         cout << "Null pointer!" << endl;
         return; 
    }
    if (size + 1 >= max_size) {
       max_size *= 2;
       tmp = new T [max_size];
       for (int i = 0; i < size; i++)
         tmp[i] = elem[i];
       tmp[size++] = *t; // Dereference delete[] elem;
       elem = tmp;
    }
    else
       elem[size++] = *t; // Dereference
  }
  void print() {
    for (int i = 0; i < size; i++) cout << elem[i] << " ";
      cout << endl;
    }
};
```

Вот так можно использовать **Bag**:

```cpp
int main() 
{
   Bag<int> xi;
   Bag<char> xc;
   Bag<int*> xp; 

   xi.add(10);
   xi.add(9);
   xi.add(8);
   xi.print();
   xc.add('a');
   xc.add('b');
   xc.add('c');
   xc.print();
   int i = 3, j = 87, *p = new int[2];
   *p = 8;
   *(p + 1) = 100;
   xp.add(&i);
   xp.add(&j);
   xp.add(p);
   xp.add(p + 1);
   p = NULL;
   xp.add(p);
   xp.print();
}
```

## Шаблоны и наследование

При описании шаблонов можно использовать наследование.

Рассмотрим пример класса **Vector** (без деталей реализации):


```cpp
#include <iostream>

using namespace std;

const int MAX_VECTOR_SIZE = 100000000;
const int MAX_MATRIX_SIZE = 10000;

// Шаблон вектора
template <class ValType>
class TVector
{
protected:
  ValType *pVector;
  int Size;       // размер вектора
  int StartIndex; // индекс первого элемента вектора
public:
  TVector(int s = 10, int si = 0);
  TVector(const TVector &v);                // конструктор копирования
  ~TVector();
  int GetSize()      { return Size;       } // размер вектора
  int GetStartIndex(){ return StartIndex; } // индекс первого элемента
  ValType& operator[](int pos);             // доступ
  bool operator==(const TVector &v) const;  // сравнение
  bool operator!=(const TVector &v) const;  // сравнение
  TVector& operator=(const TVector &v);     // присваивание

  // скалярные операции
  TVector  operator+(const ValType &val);   // прибавить скаляр
  TVector  operator-(const ValType &val);   // вычесть скаляр
  TVector  operator*(const ValType &val);   // умножить на скаляр

  // векторные операции
  TVector  operator+(const TVector &v);     // сложение
  TVector  operator-(const TVector &v);     // вычитание
  ValType  operator*(const TVector &v);     // скалярное произведение

  // ввод-вывод
  friend istream& operator>>(istream &in, TVector &v)
  {
    for (int i = 0; i < v.Size; i++)
      in >> v.pVector[i];
    return in;
  }
  friend ostream& operator<<(ostream &out, const TVector &v)
  {
    for (int i = 0; i < v.Size; i++)
      out << v.pVector[i] << ' ';
    return out;
  }
};

template <class ValType>
TVector<ValType>::TVector(int s, int si)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> //конструктор копирования
TVector<ValType>::TVector(const TVector<ValType> &v)
{
} /*-------------------------------------------------------------------------*/

template <class ValType>
TVector<ValType>::~TVector()
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // доступ
ValType& TVector<ValType>::operator[](int pos)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // сравнение
bool TVector<ValType>::operator==(const TVector &v) const
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // сравнение
bool TVector<ValType>::operator!=(const TVector &v) const
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // присваивание
TVector<ValType>& TVector<ValType>::operator=(const TVector &v)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // прибавить скаляр
TVector<ValType> TVector<ValType>::operator+(const ValType &val)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // вычесть скаляр
TVector<ValType> TVector<ValType>::operator-(const ValType &val)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // умножить на скаляр
TVector<ValType> TVector<ValType>::operator*(const ValType &val)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // сложение
TVector<ValType> TVector<ValType>::operator+(const TVector<ValType> &v)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // вычитание
TVector<ValType> TVector<ValType>::operator-(const TVector<ValType> &v)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // скалярное произведение
ValType TVector<ValType>::operator*(const TVector<ValType> &v)
{
} /*-------------------------------------------------------------------------*/
```

Теперь на основе шаблона **Vector** создадим шаблон **Matrix**:

```cpp
// Верхнетреугольная матрица
template <class ValType>
class TMatrix : public TVector<TVector<ValType> >
{
public:
  TMatrix(int s = 10);                           
  TMatrix(const TMatrix &mt);                    // копирование
  TMatrix(const TVector<TVector<ValType> > &mt); // преобразование типа
  bool operator==(const TMatrix &mt) const;      // сравнение
  bool operator!=(const TMatrix &mt) const;      // сравнение
  TMatrix& operator= (const TMatrix &mt);        // присваивание
  TMatrix  operator+ (const TMatrix &mt);        // сложение
  TMatrix  operator- (const TMatrix &mt);        // вычитание

  // ввод / вывод
  friend istream& operator>>(istream &in, TMatrix &mt)
  {
    for (int i = 0; i < mt.Size; i++)
      in >> mt.pVector[i];
    return in;
  }
  friend ostream & operator<<( ostream &out, const TMatrix &mt)
  {
    for (int i = 0; i < mt.Size; i++)
      out << mt.pVector[i] << endl;
    return out;
  }
};

template <class ValType>
TMatrix<ValType>::TMatrix(int s): TVector<TVector<ValType> >(s)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // конструктор копирования
TMatrix<ValType>::TMatrix(const TMatrix<ValType> &mt):
  TVector<TVector<ValType> >(mt) {}

template <class ValType> // конструктор преобразования типа
TMatrix<ValType>::TMatrix(const TVector<TVector<ValType> > &mt):
  TVector<TVector<ValType> >(mt) {}

template <class ValType> // сравнение
bool TMatrix<ValType>::operator==(const TMatrix<ValType> &mt) const
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // сравнение
bool TMatrix<ValType>::operator!=(const TMatrix<ValType> &mt) const
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // присваивание
TMatrix<ValType>& TMatrix<ValType>::operator=(const TMatrix<ValType> &mt)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // сложение
TMatrix<ValType> TMatrix<ValType>::operator+(const TMatrix<ValType> &mt)
{
} /*-------------------------------------------------------------------------*/

template <class ValType> // вычитание
TMatrix<ValType> TMatrix<ValType>::operator-(const TMatrix<ValType> &mt)
{
} /*-------------------------------------------------------------------------*/
```


## Наследование и специализация


Мы можем использовать специализацию при наследовании.


Рассмотрим следующую ситуацию. Допустим мы хотим создать класс, услуги которого базируются на сходных по назначению, но отличных в их реализации классах **BaseA** и **BaseB**. Если оба базовых класса обладают схожим интерфейсом, то логично оформить наш класс в виде шаблона:

```cpp
template < typename Base > 
class Derived : public Base {
};
```

если разработчику необходима информация о базовых классах объектов, основанных на шаблоне **Derived**. Как ее получить?


Следующий метод основан на введении вспомогательного шаблона класса (или структуры) **BaseClassInstance**, не содержащего ничего, кроме статической константы типа **BaseClass**, и специализированного для разных фактических типов базовых классов.

```cpp
enum BaseClass {
     Unknown,
     BaseClassA,
     BaseClassB
};
template < typename > struct BaseClassInstance 
{
   static const BaseClass value = Unknown; 
 };

template <>
struct BaseClassInstance<class BaseA> 
{
   static const BaseClass value = BaseClassA; 
};
template <>
struct BaseClassInstance<class BaseB> 
{
   static const BaseClass value = BaseClassB; 
};
```
Описываем шаблон класса-наследника, в который помещается метод **GetBaseClass**:

```cpp
template < typename Base > class Derived : public Base 
{
public:
   BaseClass GetBaseClass() const;
 };
template < typename Base >
BaseClass Derived< Base >::GetBaseClass() const 
{
   return BaseClassInstance< Base >::value; 
}
```

Вот так можно воспользоваться созданными шаблонами:

```cpp
class BaseA {}; 
class BaseB {};

int main() {
  Derived<BaseA> da; 
  Derived<BaseB> db;
  BaseClass b=db.GetBaseClass(); 
  return 0;
}
```

## Статические проверки

Начиная с **C++11**, появилась возможность создавать статические проверки, выполняющиеся во время построения программы

```cpp
template<int P>
struct fact {
   static_assert(P>0, "a positive value expected");
   static const int value=P*fact<P - 1>::value;
};
template<>
struct fact<0> {
    static const int value=1;
}
```

## Тип, как результат инстанцирования шаблона

```c++
template<typename T>
struct settype {
    using type = T;
};

template<typename T>
struct settype<const T> {
    using type = T;
};

settype<int>::type a1;
settype<const int>::type a2;
```


