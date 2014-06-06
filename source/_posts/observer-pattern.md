title: 'Observer Pattern'
date: 2014-05-12 19:59:21
categories: Design Pattern
tags: Observer Pattern, Subject, Observer 
---
观察者模式定义了对象之间的一对多依赖,这样一来,当一个对象改变状态时,它的所有依赖者都会收到通知并自动更新。观察者模式定义了一系列对象之间的一对多的关系，当其中一个对象改变状态时，其他依赖者都会收到通知。
> The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.It is mainly used to implement distributed event handling systems. The Observer pattern is also a key part in the familiar model–view–controller (MVC) architectural pattern. ---wikipedia

<!--more--> 

## Observer pattern 类图
![](http://never-blog.qiniudn.com/2014_05_1.jpg)


## Observer pattern 代码

#### Observer interface
```java
	public interface Observer {
		public void update(float temp, float humidity);
	}
```
#### Subject interface
```java
	public interface Subject {
		public void registerObserver(Observer o);
		public void removeObserver(Observer o);
		public void notifyObservers();
	}
```
#### Class WeatherData Implements the Interface of Subject
```java
	import java.util.*;
	public class WeatherData implements Subject {
		private ArrayList<Observer> observers;
		private float temperature;
		private float humidity;
		public WeatherData() {
			observers = new ArrayList<Observer>();
		}
		public void registerObserver(Observer o) {
			observers.add(o);
		}
		public void removeObserver(Observer o) {
			int i = observers.indexOf(o);
			if (i >= 0) {
				observers.remove(i);
			}
		}
		public void notifyObservers() {
			for (Observer observer : observers) {
				observer.update(temperature, humidity);
			}
		}
		public void measurementsChanged() {
			notifyObservers();
		}
		public void setMeasurements(float temperature, float humidity) {
			this.temperature = temperature;
			this.humidity = humidity;
			measurementsChanged();
		}
		public float getTemperature() {
			return temperature;
		}
		public float getHumidity() {
			return humidity;
		}
	}
```
#### Class ForecastDisplay Implements the Interface of Observer
```java
	public class ForecastDisplay implements Observer {
		private float currentHumidity = 29.92f;  
		private float lastHumidity;
		private WeatherData weatherData;
		public ForecastDisplay(WeatherData weatherData) {
			this.weatherData = weatherData;
			weatherData.registerObserver(this);
		}
		public void update(float temp, float humidity) {
		lastHumidity = currentHumidity;
			currentHumidity = humidity;
			display();
		}
		public void display() {
			System.out.print("Forecast: ");
			if (currentHumidity > lastHumidity) {
				System.out.println("Improving weather on the way!");
			} else if (currentHumidity == lastHumidity) {
				System.out.println("More of the same");
			} else if (currentHumidity < lastHumidity) {
				System.out.println("Watch out for cooler, rainy weather");
			}
		}
	}
```

#### Class CurrentConditionsDisplay Implements the Interface of Observer
```java
	public class CurrentConditionsDisplay implements Observer {
		private float temperature;
		private float humidity;
		private Subject weatherData;
		public CurrentConditionsDisplay(Subject weatherData) {
			this.weatherData = weatherData;
			weatherData.registerObserver(this);
		}
		public void update(float temperature, float humidity) {
			this.temperature = temperature;
			this.humidity = humidity;
			display();
		}
		public void display() {
			System.out.println("Current conditions: " + temperature + "F degrees and " + humidity + "% humidity");
		}
	}
```
#### Class StatisticsDisplay Implements the Interface of Observer
```java
	public class StatisticsDisplay implements Observer {
		private float maxTemp = 0.0f;
		private float minTemp = 200;
		private float tempSum= 0.0f;
		private int numReadings;
		private WeatherData weatherData;
		public StatisticsDisplay(WeatherData weatherData) {
			this.weatherData = weatherData;
			weatherData.registerObserver(this);
		}
		public void update(float temp, float humidity) {
			tempSum += temp;
			numReadings++;

			if (temp > maxTemp) {
				maxTemp = temp;
			}
	 
			if (temp < minTemp) {
				minTemp = temp;
			}

			display();
		}
		public void display() {
			System.out.println("Avg/Max/Min temperature = " + (tempSum / numReadings) + "/" + maxTemp + "/" + minTemp);
		}
	}
```
## 设计原则
__找出程序中会变化的方面,然后将其和固定:__在观察者模式中,会改变的是主题的状态,以及观察者的数目和类型。用这个模式,你可以改变依赖主题状态的对象,却不必改变主题。这就叫提前规划!
__针对接口编程,不针对实现编程:__
主题与观察者都使用接口:观察者利用主题的接口向主题注册,而主题利用观察者接口通知观察者。这样可以让两者之间运作正常,又同时具有松耦合的优点。
__多用组合,少用继承:__
观察者模式利用“组合”将许多观察者组合进主题中。对象之间的这种关系不是通过继承产生的,而是在运行时利用组合的方式而产生的。
__为交互对象之间的松耦合设计而努力:__观察者和可观察者之间用松耦合方式结合(loosecoupl-ing),可观察者不知道观察者的细节,只知道观察者实现了观察者接口。














