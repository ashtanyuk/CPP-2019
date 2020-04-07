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
     T& setValue() {return value;}
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

## Пример: шаблон стека

Ниже предлагается законченный пример шаблона класса **Stack**. Стек организуется на статическом массиве фиксированной длины. Шаблон содержит перегруженный оператор вывода в поток.


```cpp
#ifndef _H_STSTACK_H_
#define _H_STSTACK_H_
#include <iostream>
const int MaxSize=10;

template <class T>
class STStack
{
public:
    bool push(const T& item);
    bool pop(T& item);
    template<class D>
    friend std::ostream& operator<<(std::ostream& os,const STStack<D>& stack)
    {
        for(int i=0;i<stack.top;++i)
            os<<stack.storage[i]<<" ";
        os<<std::endl;
        return os;      
    }
    STStack();
    STStack(const STStack<T>& stack);
private:
    T storage[MaxSize];
    int top;
};

//----------------------------
template<class T>
STStack<T>::STStack()
{
    top=0;
}
//----------------------------
template<class T>
STStack<T>::STStack(const STStack<T>& stack)
{
    top=stack.top;
    for(int i=0;i<top;i++)
        storage[i]=stack.storage[i];
}
//-----------------------------
template<class T>
bool STStack<T>::push(const T& item)
{
    if(top<MaxSize) {
        storage[top++]=item;
        return true;
    }
    else 
        return false;
}
//-------------------------------
template<class T>
bool STStack<T>::pop(T& item)
{
    if(top>0) {
        item=storage[--top];
        return true;
    }
    else
        return false;
}
//-------------------------------
#endif
```


## Специализация шаблонов



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


