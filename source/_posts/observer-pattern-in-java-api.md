title: 'Observer Pattern in Java API'
date: 2014-05-13 07:50:53
categories: Java
tags: Observer, Observable, Observer Pattern
---
Java API包含了基本的Observer接口和Observable类，和上一篇文章[Observer Pattern](http://neverc.github.io/2014/05/12/observer-pattern/)的Subject接口与Observer接口很相似，但也有所差异。Java API中的观察者模式中传送数据的方式支持推送(push)和拉取(pull)两种方式，方便控制。

<!--more-->

## java.uitl.Observer与java.util.Observable分析
为了更好的理解Observer接口与Observable接口的功能，分析源码是很必要的，下面是Obsever的源码：
```java
	package java.util;
	/**
	* A class can implement the <code>Observer</code> interface when it
	* wants to be informed of changes in observable objects.
	*/
	public interface Observer {
		/**
		* This method is called whenever the observed object is changed. An
		* application calls an <tt>Observable</tt> object's
		* <code>notifyObservers</code> method to have all the object's
		* observers notified of the change.
		*/
		void update(Observable o, Object arg);
	}
```
Observable class的源代码：
```java
	package java.util;
	/**
	 * This class represents an observable object, or "data"
	 * in the model-view paradigm. It can be subclassed to represent an
	 * object that the application wants to have observed.
	 * <p>
	 * An observable object can have one or more observers. An observer
	 * may be any object that implements interface <tt>Observer</tt>. After an
	 * observable instance changes, an application calling the
	 * <code>Observable</code>'s <code>notifyObservers</code> method
	 * causes all of its observers to be notified of the change by a call
	 * to their <code>update</code> method.
	 * <p>
	 * The order in which notifications will be delivered is unspecified.
	 * The default implementation provided in the Observable class will
	 * notify Observers in the order in which they registered interest, but
	 * subclasses may change this order, use no guaranteed order, deliver
	 * notifications on separate threads, or may guarantee that their
	 * subclass follows this order, as they choose.
	 * <p>
	 * Note that this notification mechanism is has nothing to do with threads
	 * and is completely separate from the <tt>wait</tt> and <tt>notify</tt>
	 * mechanism of class <tt>Object</tt>.
	 * <p>
	 * When an observable object is newly created, its set of observers is
	 * empty. Two observers are considered the same if and only if the
	 * <tt>equals</tt> method returns true for them.
	 */
	public class Observable {
		private boolean changed = false;
		private Vector obs;

    /** Construct an Observable with zero Observers. */

    public Observable() {
        obs = new Vector();
    }

    /**
     * Adds an observer to the set of observers for this object, provided
     * that it is not the same as some observer already in the set.
     * The order in which notifications will be delivered to multiple
     * observers is not specified. See the class comment.
     *
     * @param   o   an observer to be added.
     * @throws NullPointerException   if the parameter o is null.
     */
    public synchronized void addObserver(Observer o) {
        if (o == null)
            throw new NullPointerException();
        if (!obs.contains(o)) {
            obs.addElement(o);
        }
    }

    /**
     * Deletes an observer from the set of observers of this object.
     * Passing <CODE>null</CODE> to this method will have no effect.
     * @param   o   the observer to be deleted.
     */
    public synchronized void deleteObserver(Observer o) {
        obs.removeElement(o);
    }

    /**
     * If this object has changed, as indicated by the
     * <code>hasChanged</code> method, then notify all of its observers
     * and then call the <code>clearChanged</code> method to
     * indicate that this object has no longer changed.
     * <p>
     * Each observer has its <code>update</code> method called with two
     * arguments: this observable object and <code>null</code>. In other
     * words, this method is equivalent to:
     * <blockquote><tt>
     * notifyObservers(null)</tt></blockquote>
     *
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#hasChanged()
     * @see     java.util.Observer#update(java.util.Observable, java.lang.Object)
     */
    public void notifyObservers() {
        notifyObservers(null);
    }

    /**
     * If this object has changed, as indicated by the
     * <code>hasChanged</code> method, then notify all of its observers
     * and then call the <code>clearChanged</code> method to indicate
     * that this object has no longer changed.
     * <p>
     * Each observer has its <code>update</code> method called with two
     * arguments: this observable object and the <code>arg</code> argument.
     *
     * @param   arg   any object.
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#hasChanged()
     * @see     java.util.Observer#update(java.util.Observable, java.lang.Object)
     */
    public void notifyObservers(Object arg) {
        /*
         * a temporary array buffer, used as a snapshot of the state of
         * current Observers.
         */
        Object[] arrLocal;

        synchronized (this) {
            /* We don't want the Observer doing callbacks into
             * arbitrary code while holding its own Monitor.
             * The code where we extract each Observable from
             * the Vector and store the state of the Observer
             * needs synchronization, but notifying observers
             * does not (should not).  The worst result of any
             * potential race-condition here is that:
             * 1) a newly-added Observer will miss a
             *   notification in progress
             * 2) a recently unregistered Observer will be
             *   wrongly notified when it doesn't care
             */
            if (!changed)
                return;
            arrLocal = obs.toArray();
            clearChanged();
        }

        for (int i = arrLocal.length-1; i>=0; i--)
            ((Observer)arrLocal[i]).update(this, arg);
    }

    /**
     * Clears the observer list so that this object no longer has any observers.
     */
    public synchronized void deleteObservers() {
        obs.removeAllElements();
    }

    /**
     * Marks this <tt>Observable</tt> object as having been changed; the
     * <tt>hasChanged</tt> method will now return <tt>true</tt>.
     */
    protected synchronized void setChanged() {
        changed = true;
    }

    /**
     * Indicates that this object has no longer changed, or that it has
     * already notified all of its observers of its most recent change,
     * so that the <tt>hasChanged</tt> method will now return <tt>false</tt>.
     * This method is called automatically by the
     * <code>notifyObservers</code> methods.
     *
     * @see     java.util.Observable#notifyObservers()
     * @see     java.util.Observable#notifyObservers(java.lang.Object)
     */
    protected synchronized void clearChanged() {
        changed = false;
    }

    /**
     * Tests if this object has changed.
     *
     * @return  <code>true</code> if and only if the <code>setChanged</code>
     *          method has been called more recently than the
     *          <code>clearChanged</code> method on this object;
     *          <code>false</code> otherwise.
     * @see     java.util.Observable#clearChanged()
     * @see     java.util.Observable#setChanged()
     */
    public synchronized boolean hasChanged() {
        return changed;
    }

    /**
     * Returns the number of observers of this <tt>Observable</tt> object.
     *
     * @return  the number of observers of this object.
     */
    public synchronized int countObservers() {
        return obs.size();
    }
}
```
Java API中的Observer接口和一般意义上Observer接口没什么不同，都是提供了一个被主题调用的update()方法。而Observable类不同于Subject接口，主要区别是类和接口的差异。
## Java API的观察者模式如何运作
#### 如何把对象变成观察者......
对象实现观察者接口(java.uitl.Observer),然后调用任何Observable对象的addObserver()方法。不想再当观察者时,调用deleteObserver()方法就可以了。
#### 可观察者要如何送出通知......
首先,你需要利用扩展java.util.Observable接口产生“可观察者”类,然后,需要两个步骤:
1. 先调用setChanged()方法,标记状态已经改变的事实。
2. 然后调用两种notifyObservers()方法中的一个:notifyObservers()或notifyObservers(Object arg)

#### 观察者如何接收通知......
观察者实现了更新(update)的方法,但是方法的签名不太一样:update(Observable o, Object arg),主题(Observable o)本身当作第一个变量,好让观察者知道是哪个主题通知它的. ((Object arg)是传入notifyObservers()的数据对象。如果没有说明则为空。
#### 传送数据的方式......
如果你想“推送”(push)数据给观察者,你可以把数据当作数据对象传送给notifyObservers(arg)方法。否则,观察者就必须从可观察者对象中“拉取”(pull)数据。

## java.util.Observable的黑暗面
可观察者Observable是一个“类”而不是一个“接口”,更糟的是,它甚至没有实现一个接口。不幸的是,java.util.Observable的实现
有许多问题,限制了它的使用和复用。
#### Observable是一个类，而不是接口，
Observable是一个类，而不是一个接口，设计原则中很重要的一点就是面对接口编程，而非面向实现编程。因为Observable是一个“类”,你必须设计一个类继承它。如果某类想同时具有Observable类和另一个超类的行为,就会陷入两难,毕竟Java不支持多重继承。
这限制了Observable的复用潜力(而增加复用潜力不正是我们使用模式最原始的动机吗?)。
#### Observable将关键的方法保护起来
Java.util.Observable的setChanged()方法被声明为protected类型，这意味着除非你继承自Observable,否则你无法创建Observable实例并组合到你自己的对象中来。这个设计违反了设计原则:“多用组合,少用继承”。

## 总结
由于java.util.Observable的特定特点，一些需求可以满足，比如能接受继承Observable类实现特定功能。但是如果某些特定需求不能满足，比如要求多继承，maybe实现自己的一整套观察者模式是最好的选择。
















