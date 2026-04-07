---
layout: post
title: Decorator Pattern (데코레이터 패턴)
author: beomine
date: 2023-05-23 08:53:00 +0900
categories: [Study, Design Pattern]
tags: [Design Pattern, Decorator, HeadFirst, GoF, C#]
---

## 스타버즈
### 다이어그램

![DecoratorDiagram](https://github.com/beomine/beomine.github.io/assets/118417247/cd53fa59-fba8-437f-be2a-4bbecb5e9202)

#### Beverage.cs
``` cs
public abstract class Beverage
{
    public string Description = string.Empty;
    public enum Size { TALL, GRANDE,VENTI}
    public Size BevSize { get; set; } = Size.TALL;

    public virtual string GetDescription()
    {
        return Description;
    }

    public virtual double Cost()
    {
        double cost = 0;

        switch (BevSize)
        {
            case Size.TALL:
                cost = 0;
                break;
            case Size.GRANDE:
                cost = 0.15;
                break;
            case Size.VENTI:
                cost = 0.2;
                break;
            
            default:
                break;
        }
        return cost;
    }
}
```

#### Espresso.cs
``` cs
public class Espresso : Beverage
{
    public Espresso()
    {
        Description = "에스프레소";
    }

    public override double Cost()
    {
        return base.Cost()+ 1.99;
    }

}
```

#### CondimentDecorator.cs
``` cs
public abstract class CondimentDecorator : Beverage
{
    internal Beverage _beverage = null!;
    public abstract override string GetDescription();
}
```

#### Mocha.cs
``` cs
public class Mocha : CondimentDecorator
{
    public Mocha(Beverage beverage)
    {
        _beverage = beverage;
    }

    public override double Cost()
    {
        return _beverage.Cost() + 0.20;
    }

    public override string GetDescription()
    {
        return _beverage.GetDescription() + ", 모카";
    }
}
```