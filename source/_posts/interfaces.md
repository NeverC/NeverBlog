title: interfaces
date: 2014-05-11 10:23:14
categories: Java
tags: Java, interface
---
There are a number of situations in software engineering when it is important for disparate groups of programmers to agree to a "contract" that spells out how their software interacts. Each group should be able to write their code without any knowledge of how the other group's code is written. Generally speaking, interfaces are such contracts.

<!--more-->

## Interfaces in Java
In the Java programming language, an interface is a reference type, similar to a class, that can contain **only constants, method signatures, default methods, static methods, and nested types. Method bodies exist only for default methods and static methods**. Interfaces cannot be instantiated—they can only be implemented by classes or extended by other interfaces.Defining an interface is similar to creating a new class:
```java
	public interface Animal {
		// constant declarations, if any
		// method signatures
		void display();
		void information();
		// default methods or static methods
		// nested types
	}
```
Note that the method signatures have no braces and are terminated with a semicolon.To use an interface, you write a class that implements the interface. When an instantiable class implements an interface, it provides a method body for each of the methods declared in the interface. For example:
```java
	public class Cat implements Animal {
		public void display(){
			System.out.println("I am a cat!");
		}
		public  information(){
			System.out.println("My name is lala");
		}
	}
```
## Implementing an Interface
To declare a class that implements an interface, you include an implements clause in the class declaration. Your class can implement more than one interface, so the implements keyword is followed by a comma-separated list of the interfaces implemented by the class. By convention, the implements clause follows the extends clause, if there is one.
#### A Sample Interface, Relatable
```java
	public interface Relatable {
		// this (object calling isLargerThan)
		// and other must be instances of 
		// the same class returns 1, 0, -1 
		// if this is greater than, 
		// equal to, or less than other
		public int isLargerThan(Relatable other);
	}
```
If you want to be able to compare the size of similar objects, no matter what they are, the class that instantiates them should implement Relatable.
If you know that a class implements Relatable, then you know that you can compare the size of the objects instantiated from that class.
#### Implementing the Relatable Interface
Here is the Student class written to implement Relatable.
```java
	public calss Student implements Relatable {
		private String sName;
		private int sAge;
		private String sSex;
		
		public Student(String sName, int sAge, String sSex){
			this.sName = sName;
			this.sAge = sAge;
			this.sSex = sSex;
		}
		public getAge(){
			return sAge;
		}
		// a method required to implement the Relatable interface
		public int isLargerThan(Relatable student){
			Student otherStudent = (Student)student;
			if (this.getAge() < otherStudent.getAge())
				return -1;
			else if (this.getAge() > otherStudent.getAge())
				return 1;
			else
				return 0;	
		}
	}
```
Because RectanglePlus implements Relatable, the age of any two Student objects can be compared.
Note: The isLargerThan method, as defined in the Relatable interface, takes an object of type Relatable. The line of code"Student otherStudent = (Student)student" shown in the previous example, casts other to a Student instance. Type casting tells the compiler what the object really is. Invoking getAge directly on the other instance (other.getAge()) would fail to compile because the compiler does not understand that other is actually an instance of Student. 
## Using an Interface as a Type
When you define a new interface, you are defining a new reference data type. You can use interface names anywhere you can use any other data type name. If you define a reference variable whose type is an interface, any object you assign to it must be an instance of a class that implements the interface.
## Evolving Interfaces
Consider an interface that you have developed called DoIt:
```java
	public interface DoIt {
		void doSomething(int i, double x);
		int doSomethingElse(String s);
	}
```
Suppose that, at a later time, you want to add a third method to DoIt, so that the interface now becomes:
```java
	public interface DoIt {
		void doSomething(int i, double x);
		int doSomethingElse(String s);
		boolean didItWork(int i, double x, String s);
	}
```
If you make this change, then all classes that implement the old DoIt interface will break because they no longer implement the old interface. Programmers relying on this interface will protest loudly.

Try to anticipate all uses for your interface and specify it completely from the beginning. If you want to add additional methods to an interface, you have several options. You could create a DoItPlus interface that extends DoIt:
```java
	public interface DoItPlus extends DoIt {
		boolean didItWork(int i, double x, String s);
	}
```
Now users of your code can choose to continue to use the old interface or to upgrade to the new interface.

Alternatively, you can define your new methods as **default methods**. The following example defines a default method named didItWork:
```java
	public interface DoIt {
		void doSomething(int i, double x);
		int doSomethingElse(String s);
		default boolean didItWork(int i, double x, String s) {
			// Method body 
		}
	}
```
Note that you must provide an implementation for default methods. You could also define new static methods to existing interfaces. Users who have classes that implement interfaces enhanced with new default or static methods do not have to modify or recompile them to accommodate the additional methods.
## Default Methods
Default methods enable you to add new functionality to the interfaces of your libraries and ensure binary compatibility with code written for older versions of those interfaces.
Consider the following interface of TimeClient
```java
	import java.time.*; 
	public interface TimeClient {
		void setTime(int hour, int minute, int second);
		void setDate(int day, int month, int year);
		void setDateAndTime(int day, int month, int year, int hour, int minute, int second);
		LocalDateTime getLocalDateTime();
	}
```
The following class of SimpleTimeClient implements TimeClient:
```java
	import java.time.*;
	import java.lang.*;
	import java.util.*;

	public calss SimpleTimeClient implements TimeClient {
		private LocalDateTime dateAndTime;
		public SimpleTimeClient() {
			dateAndTime = LocalDateTime.now();
		}
		public void setTime(int hour, int minute, int second) {
			LocalDate currentDate = LocalDate.from(dateAndTime);
			LocalTime timeToSet = LocalTime.of(hour, minute, second);
			dateAndTime = LocalDateTime.of(currentDate, timeToSet);
		}
		public void setDate(int day, int month, int year) {
			LocalDate dateToSet = LocalDate.of(day, month, year);
			LocalTime currentTime = LocalTime.from(dateAndTime);
			dateAndTime = LocalDateTime.of(dateToSet, currentTime);
		}
		public void setDateAndTime(int day, int month, int year,int hour, int minute, int second) {
			LocalDate dateToSet = LocalDate.of(day, month, year);
			LocalTime timeToSet = LocalTime.of(hour, minute, second); 
			dateAndTime = LocalDateTime.of(dateToSet, timeToSet);
		}
		public LocalDateTime getLocalDateTime() {
			return dateAndTime;
		}
		public String toString() {
			return dateAndTime.toString();
		}
		public static void main(String... args) {
			TimeClient myTimeClient = new SimpleTimeClient();
			System.out.println(myTimeClient.toString());
		}
	}
```
Suppose that you want to add new functionality to the TimeClient interface, such as the ability to specify a time zone through a ZonedDateTime object (which is like a LocalDateTime object except that it stores time zone information):
```java
	public interface TimeClient {
		void setTime(int hour, int minute, int second);
		void setDate(int day, int month, int year);
		void setDateAndTime(int day, int month, int year, int hour, int minute, int second);
		LocalDateTime getLocalDateTime();
		ZonedDateTime getZonedDateTime(String zoneString);
	}
```
Following this modification to the TimeClient interface, you would also have to modify the class SimpleTimeClient and implement the method getZonedDateTime. However, rather than leaving getZonedDateTime as abstract (as in the previous example), you can instead define a default implementation. Remember that an abstract method is a method declared without an implementation.

```java
	import java.time.*;
	public interface TimeClient {
		void setTime(int hour, int minute, int second);
		void setDate(int day, int month, int year);
		void setDateAndTime(int day, int month, int year, int hour, int minute, int second);
		default ZonedDateTime getZonedDateTime(String zoneString) {
			return ZonedDateTime.of(getLocalDateTime(), getZoneId(zoneString));
		}
		static ZoneId getZoneId (String zoneString) {
			try {
				return ZoneId.of(zoneString);
			} catch (DateTimeException e) {
				System.err.println("Invalid time zone: " + zoneString + "; using default time zone instead.");
			return ZoneId.systemDefault();
			}
		}
	}
```
You specify that a method definition in an interface is a default method with the default keyword at the beginning of the method signature. All method declarations in an interface, including default methods, are implicitly public, so you can omit the public modifier.With this interface, you do not have to modify the class SimpleTimeClient, and this class (and any class that implements the interface TimeClient), will have the method getZonedDateTime already defined. 
#### Extending Interfaces That Contain Default Methods
When you extend an interface that contains a default method, you can do the following:
* Not mention the default method at all, which lets your extended interface inherit the default method.
* Redeclare the default method, which makes it abstract.
* Redefine the default method, which overrides it.

## Static Methods
In addition to default methods, you can define static methods in interfaces. (A static method is a method that is associated with the class in which it is defined rather than with any object. Every instance of the class shares its static methods.) This makes it easier for you to organize helper methods in your libraries; you can keep static methods specific to an interface in the same interface rather than in a separate class. The following example defines a static method that retrieves a ZoneId object corresponding to a time zone identifier; it uses the system default time zone if there is no ZoneId object corresponding to the given identifier. (As a result, you can simplify the method getZonedDateTime):
```java
	public interface TimeClient {
		// ...
		static public ZoneId getZoneId (String zoneString) {
			try {
				return ZoneId.of(zoneString);
			} catch (DateTimeException e) {
				System.err.println("Invalid time zone: " + zoneString + "; using default time zone instead.");
				return ZoneId.systemDefault();
			}
		}
		default public ZonedDateTime getZonedDateTime(String zoneString) {
			return ZonedDateTime.of(getLocalDateTime(), getZoneId(zoneString));
		}    
	}
```
Like static methods in classes, you specify that a method definition in an interface is a static method with the static keyword at the beginning of the method signature. All method declarations in an interface, including static methods, are implicitly public, so you can omit the public modifier.














