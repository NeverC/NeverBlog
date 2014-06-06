title: 'Decorator Pattern'
date: 2014-05-15 12:24:42
categories: Design Pattern
tags: Decorator Pattern
---
修饰模式，是面向对象编程领域中，一种动态地往一个类中添加新的行为的设计模式。就功能而言，修饰模式相比生成子类更为灵活，这样可以给某个对象而不是整个类添加一些功能。装饰者模式动态的将责任附加到对象上，若要扩展对象，装饰者模式提供了比继承更有弹性的替代方案。

<!--more-->

## 装饰者模式定义
> In object-oriented programming, the decorator pattern (also known as Wrapper, an alternative naming shared with the Adapter pattern) is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. --wiki

## 装饰者模式类图
![](http://never-blog.qiniudn.com/2014_05_6.png)

1. ConcreteComponent是我们将(包装一个)组件,也就是要动态地加上新行为的对说,装饰者有一个实例变象,它扩展自Component。
2. 每个装饰者都“有一个”(包装一个)组件,也就是说,装饰者有一个实例变量以保存某个Component的引用。
3. Decorator是装饰者共同实现的接口（也可以是抽象类）
4. ConcreteDecoratort有一个实例变量，可以记录所装饰的事物（装饰者包着Component）
5. 装饰者可以扩展Component的状态。
6. 装饰者可以加上新的方法。新行为是通过在旧行为前面或后面做一些计算来添加的。

## 咖啡与调料的示例代码（来自Head First设计模式）
在购买咖啡时，可以在咖啡中加入各种调料，例如：蒸奶（Steamed Milk）、豆浆（Soy）、摩卡（Mocha）或覆盖奶泡。咖啡店会根据所加入的调料收取不同的费用，所以咖啡店订单系统必须考虑到这些调料部分。
![](http://never-blog.qiniudn.com/2014_05_7.jpg)
## 示例代码
先从Beverage类下手，所有类的基类，Beverage是一个抽象类，其实很大程度是是子类继承了Beverage的类型，而不是方法。
```java
	public abstract class Beverage {
		String description = "Unknown Beverage";
	  
		public String getDescription() {
			return description;
		}
	 
		public abstract double cost();
	}
```
Beverage很简单。接下来实现Condiment(调料)抽象类,也就是装饰者类吧,为了使CondimentDecorator能够取代Beverage,所以CondimentDecorator继承了Beverage类。
```java
	public abstract class CondimentDecorator extends Beverage {
		public abstract String getDescription();
	}
```
现在,已经有了基类,让我们开始开始实现一些饮料吧!先从浓缩咖啡(Espresso)开始。别忘了,我们需要为具体的饮料设置描述,而且还
必须实现cost()方法。首先,让EsEspresso继承Beverage类，为了要设置饮料的描述,我们写了一个构造器。记住,description实例变量继承自Beverage。最后实现cost方法。
```java
	public class Espresso extends Beverage {
	  
		public Espresso() {
			description = "Espresso";
		}
	  
		public double cost() {
			return 1.99;
		}
	}
```
DarkRoast、Decaf、HouseBlend类的实现与Espresso类似，不多解释，下面是详细代码。
```java
	public class DarkRoast extends Beverage {
		public DarkRoast() {
			description = "Dark Roast Coffee";
		}
	 
		public double cost() {
			return .99;
		}
	}
```
```java
	public class HouseBlend extends Beverage {
		public HouseBlend() {
			description = "House Blend Coffee";
		}
	 
		public double cost() {
			return .89;
		}
	}
```
```java
	public class Decaf extends Beverage {
		public Decaf() {
			description = "Decaf Coffee";
		}
	 
		public double cost() {
			return 1.05;
		}
	}
```
如果你回头去看看装饰者模式的类图,将发现我们已经完成了抽象组件(Beverage),有了具体组件(HouseBlend),也有了抽象装饰者(CondimentDecorator)。现在,我们就来实现具体装饰者。先从摩卡下手:摩卡是一个装饰者,所以让它扩展CondimentDecorator，为了使Mocha能够引用一个Beverage，需要使用一个实例变量来记录饮料，也就是被装饰者。将被装饰者当作构造函数的参数，再有构造函数负责记录在实例变量中。在计算Mocha饮料的价钱时，首先委托给被装饰者计算价钱，然后在加上Mocha的价钱，最后得出结果。

```java
	public class Mocha extends CondimentDecorator {
		Beverage beverage;
	 
		public Mocha(Beverage beverage) {
			this.beverage = beverage;
		}
	 
		public String getDescription() {
			return beverage.getDescription() + ", Mocha";
		}
	 
		public double cost() {
			return .20 + beverage.cost();
		}
	}
```
Milk、Soy、Whip类的实现与Mocha类似，不多解释，下面是详细代码。
```java
	public class Milk extends CondimentDecorator {
		Beverage beverage;

		public Milk(Beverage beverage) {
			this.beverage = beverage;
		}

		public String getDescription() {
			return beverage.getDescription() + ", Milk";
		}

		public double cost() {
			return .10 + beverage.cost();
		}
	}
```
```java
	public class Soy extends CondimentDecorator {
		Beverage beverage;

		public Soy(Beverage beverage) {
			this.beverage = beverage;
		}

		public String getDescription() {
			return beverage.getDescription() + ", Soy";
		}

		public double cost() {
			return .15 + beverage.cost();
		}
	}
```
```java
	public class Whip extends CondimentDecorator {
		Beverage beverage;
	 
		public Whip(Beverage beverage) {
			this.beverage = beverage;
		}
	 
		public String getDescription() {
			return beverage.getDescription() + ", Whip";
		}
	 
		public double cost() {
			return .10 + beverage.cost();
		}
	}
```
最后是测试类的代码
```java
	public class StarbuzzCoffee {
	 
		public static void main(String args[]) {
			Beverage beverage = new Espresso();
			System.out.println(beverage.getDescription() + " $" + beverage.cost());
	 
			Beverage beverage2 = new DarkRoast();
			beverage2 = new Mocha(beverage2);
			beverage2 = new Mocha(beverage2);
			beverage2 = new Whip(beverage2);
			System.out.println(beverage2.getDescription() + " $" + beverage2.cost());
	 
			Beverage beverage3 = new HouseBlend();
			beverage3 = new Soy(beverage3);
			beverage3 = new Mocha(beverage3);
			beverage3 = new Whip(beverage3);
			System.out.println(beverage3.getDescription() + " $" + beverage3.cost());
		}
	}
```
## 总结
相对来说，装饰者模式还是很难懂的，而且应用场景不明显，还需要好好理解理解。最后paste了个问题，希望可以讨论下：
__我们在星巴兹的朋友决定开始在菜单上加上咖啡的容量大小,供顾客可以选择小杯(tall)、中杯(grande)、大杯(venti)。星巴兹认为这是任何咖啡都必须具备的,所以在Beverage类中加上了getSize()与setSize()。他们也希望调料根据咖啡容量收费,例如:小中大杯的咖啡加上豆浆,分别加收0.10、0.15、0.20美金__。



