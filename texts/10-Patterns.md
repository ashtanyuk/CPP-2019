## Паттерны

### Введение


### Паттерн Observer (наблюдатель)

![](./img/Observer_UML.png)


Рассмотрим реализацию паттерна **Observer** (Наблюдатель)

```cpp
#include <vector>
#include <cstdio>



class Observer {
  public:
    virtual void update (float temperature, float humidity, int pressure)=0;
};

class Observable {
  public:
    virtual void registerObserver(Observer* o)=0;
    virtual void removeObserver(Observer* o)=0;
    virtual void notifyObservers()=0;
};

class WeatherData : public Observable {
  private:
    std::vector<Observer*> observers;
    float temperature=0;
    float humidity=0;
    int pressure=0;
  public:
    
    void registerObserver(Observer *o) override {
        observers.push_back(o);
    }

    void removeObserver(Observer *o) override {
        observers.erase(std::remove(observers.begin(), observers.end(), o), observers.end());
    }

    void notifyObservers() override {
        for (Observer* observer : observers)
            observer->update(temperature, humidity, pressure);
    }
         
    void setMeasurements(float temperature, float humidity, int pressure) {
        this->temperature = temperature;
        this->humidity = humidity;
        this->pressure = pressure;
        notifyObservers();
    }
};

class CurrentConditionsDisplay : public Observer {
  private:
    float temperature=0;
    float humidity=0;
    int pressure=0;
  public:
    
    void update(float temperature, float humidity, int pressure) {
        this->temperature = temperature;
        this->humidity = humidity;
        this->pressure = pressure;
        display();
    }

    void display() {
        printf("Сейчас значения:%.1f градусов цельсия и %.1f %% влажности. Давление %d мм рт. ст.\n", temperature, humidity, pressure);
    }
};

int main() {
   WeatherData* weatherData = new WeatherData;
   Observer* currentDisplay = new CurrentConditionsDisplay;
   weatherData->registerObserver(currentDisplay);
   weatherData->setMeasurements(29, 65, 745);
   weatherData->setMeasurements(39, 70, 760);
   weatherData->setMeasurements(42, 72, 763);
}
```

