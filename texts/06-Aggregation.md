## Отношения между классами

В этом разделе мы обсудим два основных типа отношений между классами: **агрегацию** и **наследование**. Оба этих типа широко применяются на практике.

**Агрегация** (*горизонтальное* отношение) предполагает, что один объект входит в состав другого.

**Наследование** (*вертикальное* отношение) подразумевает, что один объект является разновидностью другого. 

В качестве примера рассмотрим систему классов **Auto**, представляющих автомобили и их компоненты.

```c++
#include <iostream>
using namespace std;

typedef unsigned short power_t;
enum Fuel {Petrol,Diesel};

class Engine
{
private:
	power_t power;
	Fuel fuel;
	double consume; // расход топлива
public:
	Engine(power_t p, Fuel f, double c)
	{
		power = p;
		fuel = f;
		consume = c;
	}
	double conFuel(size_t path)
	{
		return path*consume;
	}
};
```

В качестве первого класса рассмотрим **Engine** (двигатель). У двигателя есть определенная мощность, рабочее топливо и его расход (литры на 1 км.)

```c++
class Tank
{
private:
	double capacity;
public:
	Tank(double cap) : capacity(cap) {}
	bool isEmpty() const { return capacity <= 0.0; }
	void consume(double value) {
		if (!isEmpty())
			capacity -= value;
	}
};
```

Вторым классом выступает **Tank** - топливный бак, который характеризуется емкостью. мы можем проверят, пуст ли бак, а также уменьшать количество топлива в нем на оперделенную величину.

При наличии двух узлов автомобиля попробуем создать класс **Auto**:

```c++
class Auto
{
protected:
	Engine *engine;
	Tank *tank;
public:
	Auto(Engine *e, Tank *t) :engine(e), tank(t)
	{}
	void move(size_t path)
	{
		size_t current = 0;
		while (current < path && tank->isEmpty() == false)
		{
			tank->consume(engine->conFuel(1.0));
			cout << "Current dist: " << current << "km" << endl;
			current++;
		}
		cout << "Stop!" << endl;
	}
};
```

Автомобиль содержит двигатель и бак и умеет передвигаться на некоторое расстояние, расходуя при этом горючее. При опустошении бака автомобиль останавливается.

Класс **Auto** выступает в качестве *базового* класса для нескольких типов автомобилей.

Рассмотрим *легковой автомобиль* (**Car**):

```c++
class Car : public Auto
{
protected:
	size_t passengers;
public:
	Car(Engine *e, Tank *t, size_t pass) :Auto(e, t)
	{
		passengers = pass;
	}
};
```

Главная особенность нового класса: перевозка пассажиров.

Аналогично можно создать грузовик, перевозящий грузы:

```c++
class Lorry : public Auto
{
protected:
	size_t cargo;
public:
	Lorry(Engine *e, Tank *t, size_t c) :Auto(e, t)
	{
		cargo = c;
	}
};
```

В производных классах **Carr** и **Lorry** мы не определяем заново двигатель и бак - они переходят при наследовании от родительского класса **Auto**.

Конструктор производного класса должен вызвать конструктор базового и передать ему необходимые параметры.