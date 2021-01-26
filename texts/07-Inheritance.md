## Наследование

### Понятие наследования

При большом количестве никак не связанных классов в программе, управлять ими становится практически невозможным (так же, как и большим количеством функций в большом проекте). Наследование позволяет справиться с этой проблемой путем упорядочивания и ранжирования классов, то есть объединения общих для нескольких классов свойств в одном классе и использования его в качестве базового.

**Наследование** - механизм передачи свойств одних классов другим классам в процессе проектирования иерархии классов. Исходные классы называют **базовыми(БК) (родителями)**, вторые - **производными (ПК) (потомками)**.

Наследование может быть **одиночным** и **множественным**, в зависимости от числа непосредственных родителей у конкретного класса.

Классы, находящиеся ближе к началу иерархии, объединяют в себе наиболее общие черты для всех нижележащих классов. По мере продвижения вниз по иерархии классы приобретают все больше конкретных черт. Множественное наследование позволяет одному классу обладать свойствами двух и более родительских классов.


## Дерево наследования

Для удобного изображения отношений классов при наследовании строят специальный граф: **дерево наследования**:


<img src="img/avto.png" width="50%">

Рисунок 1. Дерево наследования

Базовые классы изображают над производными.


### Пример с наследованием

```cpp
class Box    // тип ``коробка``
{
  protected:
    int width,height;
  public:
    void SetWidth(int w)  { 
       width=w;  
    }
    void SetHeight(int h) { 
       height=h; 
    }
};
class ColoredBox : public Box  // ``цветная коробка``
{
   int color;
 public:
   void SetColor(int c)  { 
      color=c; 
   }
};
```

### Ключи наследования

При описании производного класса в его заголовке перечисляются все базовые для него классы. Доступ к элементам этих классов регулируется с помощью ключей доступа: `private`, `protected` и `public`. Если базовых классов несколько, они перечисляются через запятую.

Использование ключей доступа вместе с разделами внутри БК позволяет сформировать правило, по которому будет осуществляться доступ к членам ПК.


|  Ключ доступа     | Раздел БК | Раздел ПК |
|-------------------|-----------|-----------|
| private           | private   | нет       |
| private           | protected | private   |
| private           | public    | private   |
| protected         | private   | нет       |
| protected         | protected | protected |
| protected         | public    | protected |
| public            | private   | нет       |
| public            | protected | protected |
| public            | public    | public    |


Из таблицы следует, что если, например, в БК некоторая переменная располагалась в разделе public, а ПК был объявлен с ключом private, то в ПК к данной переменной можно будет обращаться только членам ПК или его друзьям (эта переменная перейдет в раздел private ПК).
Когда происходит наследование без явного указания спецификатора, все имена базового класса в производном классе автоматически становятся приватными (или можно указать private).

Если наследовать с ключевым словом public - все общедоступные имена базового класса будут общедоступными в производном классе и все защищенные имена будут защищенными в производном классе.

## Правила наследования

Нельзя наследовать

- конструкторы
- деструкторы
- перегруженные new
- перегруженные =
- отношения дружественности

Правила для специальных методов

- Поскольку конструкторы не наследуются, ПК должен иметь собственные конструкторы.
- Если в конструкторе производного класса явный вызов конструктора базового класса отсутствует, автоматически вызывается конструктор БК.
- Для иерархии, состоящей из нескольких классов, конструкторы БК вызываются начиная с самого верхнего уровня. После этого выполняются конструкторы элементов класса, являющихся объектами, а затем - конструктор класса.
- В случае нескольких БК их конструкторы вызываются в порядке объявления.
- Для деструкторов эти правила справедливы, но порядок вызова обратный - сначала ПК, затем БК. Не требуется явно вызывать деструкторы, поскольку они будут вызваны автоматически.

## Передача параметра в конструктор БК

Данная ситуация возникает в том случае, когда конструктор БК должен быть вызван с параметром (параметрами). При записи конструктора ПК необходимо указать через двоеточие имя конструктора БК со списком фактических параметров.

```cpp
 class X   {
   int a,b,c;
  public:
   X(int x,int y,int z) { a=x; b=y; c=z; }
};
class Y : public Х  {
   int val;
  public:
   Y(int d) : X(d,d+1,d+5) { val=d; }
   Y (int d, int e);
}
Y::Y(int d, int e) : X(d,e,12)  {
   val=d+e;
}
```

## Работа с производными классами

Рассмотрим класс `Работник`, который содержит персональные данные, а также дату принятия на работу и увольнения

```cpp
struct Date {
  int dd,mm,yy;
};

class Employee {
private:
  string name, surname;
  Date hire_date, fire_date;
public:
  Employee(string _name,
           string _surname);
  ~Employee();
  void hire(Date d);
  void fire(Date d);
  string name() const;
  string surname() const;
  void print() const;
};
```
На основе этого класса создадим `Программиста`

```cpp
class Programmer: public Employee
{
private:
  string team;
public:
  Programmer(string _name,
             string _surname,
             string _team);
  ~Programmer();
  void set_team (string _team);
  string team() const;
  void print() const;
};
```

В результате у нас получается следующий набор методов

```cpp
Employee::Employee()
Employee::~Employee()
Employee::hire()
Employee::fire()
Employee::name()
Employee::surname()
Employee::print()

Programmer::Programmer()
Programmer::~Programmer()
Programmer::set_team()
Programmer::team()
Programmer::print()

Programmer::Employee::hire()
Programmer::Employee::fire()
Programmer::Employee::name()
Programmer::Employee::surname()
Programmer::Employee::print()
```

Проанализируйте результаты выполнения следующего кода:

```cpp
Date start_date(1,1,2004), end_date(31,12,2007);

Employee emp("Vasya", "Pupkin");
emp.hire(start_date);
cout << emp.name();
cout << emp.surname();
emp.print()
emp.fire(end_date);

Programmer prog("Petr", "Petrov", "GM00");
prog.hire(start_date);
prog.set_team("GM12");
cout << prog.name();
cout << prog.surname();
cout << prog.team();
prog.print();
prog.Employee::print()
prog.fire(end_date);
```

### Производные классы и указатели

```cpp
Programmer *prog1 = new Programmer("Petr", "Petrov", "GM12");
Employee *emp1 = prog1;   // хорошо
Employee *emp2 = new Employee("vasya", "Pupkin");
Programmer *prog2 = emp2; // ошибка 
prog2->set_team("GM00");  // нет team

void test_function(Employee& emp);

Programmer prog3("Ivan", "Ivanov", "GM00");
test_function (prog3);  // хорошо
```

С объектом производного класса можно обращаться как с объектом базового класса при обращении к нему при помощи указателей и ссылок.

### Функции-члены


```cpp
class Employee {
  string name, surname;
  //...
public:
 void print() const;
 string surname() const;
};

class Programmer: public Employee { //...
public:
 void print() const;
 //...
};

void Programmer::print() const
{
  cout << surname() << endl;
}
void Programmer::print() const
{
  cout << surname << endl;
}
void Programmer::print() const
{
  Employee::print();
  cout << team << endl;
}

int main()
{
  Employee emp("Vasya", "Ivanov");
  Programmer prog("Petr", "Petrov", "GM12");
 
  emp.print(); // Vasya Ivanov
  emp.surname(); // Ivanov
 
  prog.print(); // Petr Petrov GM12
  prog.surname(); // Petrov
}

```

### Копирование

```cpp
class Employee {
  //...
  Employee(const Employee&);
  Employee& operator=(const Employee&)
};

void f(const Programmer& rPrg)
{
   Employee emp = rPrg;
   emp = rPrg;
};
```
Копируется только Employee-часть Programmer – срезка.

```cpp
class Employee {
  string name, surname;
public:
  Employee(const Employee&);
  Employee& operator=(const Employee&)
  //...
};
class Programmer: public Employee {
  string team;
public:
   Programmer(const Programmer &);
   Programmer& operator=(const Programmer &)
  //...
};
Programmer::Programmer (const Programmer& rp)
  : Employee(rp), team(rp.team)
{
}

Programmer& Programmer::operator=(const Programmer &rp)
{
  Employee::operator=(rp);
  team = rp.team;
}
```

































