---
layout: post
title: Observer Pattern (옵저버 패턴)
author: sarah
date: 2023-05-18 16:53:00 +0900
categories: [Study, Design Pattern]
tags: [Design Pattern, Observer, HeadFirst, GoF]
---

## 가상 스테이션
### 다이어그램
<img src="_data/assets/../../../_data/assets/images/ObserverDiagram.png">

![ObserverDiagram](https://github.com/beomine/beomine.github.io/assets/118417247/9028c8d8-5d45-40f4-8f5e-483174baad2b)

#### IDisplay.cs
``` cs
public interface IDisplay
{
    void Display();
}
```

#### IObserver.cs
``` cs
public interface IObserver
{
    void Update();
}
```

#### ISubject.cs
``` cs
public interface ISubject
{
    void RegisterObserver(IObserver observer);
    void RemoveObserver(IObserver observer);
    void NotifyObserver();
}
```

#### WeatherData.cs
  ``` cs
public class WeatherData : ISubject
{
    private readonly List<IObserver> _observers = new List<IObserver>();

    public float Temperature { get; set; }
    public float Humidity { get; set; }
    public float Pressure { get; set; }

    public void MeasurementsChanged()
    {
        NotifyObserver();
    }

    public void RegisterObserver(IObserver observer)
    {
        _observers.Add(observer);
    }

    public void RemoveObserver(IObserver observer)
    {
        _observers.Remove(observer);
    }

    public void NotifyObserver()
    {
        _observers.ForEach(item => item.Update());
    }

    public void SetMeasurements(float temp, float humidity, float pressure)
    {
        Temperature = temp;
        Humidity = humidity;
        Pressure = pressure;
        MeasurementsChanged();
    }
}
  ```

#### CurrentConditionsDisplay.cs
``` cs
public class CurrentConditionsDisplay : IObserver,IDisplay
{
    private float _temperature;
    private float _humidity;
    private readonly WeatherData _weatherData;

    public CurrentConditionsDisplay(WeatherData weatherData)
    {
        _weatherData = weatherData;
        _weatherData.RegisterObserver(this);
    }

    public void Display()
    {
        Console.WriteLine($"Current conditions : temperature : {_temperature}, humidity : {_humidity}%");
    }

    public void Update()
    {
        _temperature = _weatherData.Temperature;
        _humidity = _weatherData.Humidity;
        Display();
    }
}
```

나머지 코드는 git에...

git 주소 : [Design Patterns with CS](https://github.com/beomine/DesignPatterns_CS)