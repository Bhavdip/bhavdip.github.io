---
layout: post
title:	Java Interview Question Part1
image:	java-interview-question.png
---

I want to write the series of topic on Java technology that is important and interested to learn. The most of the topics are asked during technical process by 
interviewer.{{ more }}I wanted to divide the topics in different parts and keep posting it when I found anything interested. The post contents the question and answer and detailed explanation on any topic. If you have any topic that you know and missed here let me know we will cover it. 

***


**<span style="color:#007697">1.How can you catch an exception thrown by another thread in Java?</span>**

This can be done using `Thread.UncaughtExceptionHandler`.

Here’s a simple example:

	// create our uncaught exception handler
	Thread.UncaughtExceptionHandler handler = new Thread.UncaughtExceptionHandler() {
	    public void uncaughtException(Thread th, Throwable ex) {
	        System.out.println("Uncaught exception: " + ex);
	    }
	};


	// create another thread
	Thread otherThread = new Thread() {
	    public void run() {
	        System.out.println("Sleeping ...");
	        try {
	            Thread.sleep(1000);
	        } catch (InterruptedException e) {
	            System.out.println("Interrupted.");
	        }
	        System.out.println("Throwing exception ...");
	        throw new RuntimeException();
	    }
	};

	// set our uncaught exception handler as the one to be used when the new thread
	// throws an uncaught exception
	otherThread.setUncaughtExceptionHandler(handler);


	// start the other thread - our uncaught exception handler will be invoked when
	// the other thread throws an uncaught exception
	otherThread.start();

**<span style="color:#007697">2.Why String is immutable in Java?</span>**

String is immutable because of security and Shared String pool between multiple thread in java. The designers of the String ensure that no one can override the behavior of the String class. There might be the multiple answers on this question
You can see the String class package it’s declared as final. 1Java.lang.String` as final this can be the reason.

The following are the reason that makes the sense that why String is immutable or final in java.

1. Considering the String pool in memory without immutable cannot imagine because string pool is shared between the multiple threads and anyone can asses it into the memory. Let’s say someone created String object/literal i.e “Test” and variable may be shared between other threads and someone try to changed its value will automatically affect the original which is not desirable. 

2. In java String widely used in many classes and parameters. You can’t imagine the single program without string. If incase String is not immutable that would create the security issue. Anyone can change the value and this is the reason it’s declare as final class in Java package.

3. In short string is immutable and no one can change it once it is created. Since String has been immutable with hascode method which guarantees that multiple invocations.

4. Since String in java is immutable. It is thread safe and can shared between multiple thread and not required to used the synchronize it.


## <span style="color:#c60">Synchronization in Java</span>
**<span style="color:#007697">3.Why do we need the Synchronized in java?</span>**

If your program is executing using multiple thread then you need to synchronize the objects, which are shared among multiple thread for avoid any kind of clash or unexpected behavior. 

In java Synchronize required among shared object which are mutable. If you shared objects is read only or immutable (no one can changed it) the does not require to use Synchronization.

Synchronization is degrade the performance therefore it’s true that if the purpose of you shared object is read only by multiple thread then do not used the synchronization. You should avoid using it. 

In java multithread programming language synchronization is possible using `synchronized` and `volatile` keyword.

JVM guarantees that the Java synchronized code will only executed by only single thread. In java Synchronization gain using Synchronized keyword and block. 

You can Synchronized the method and variable. Instead of Synchronized the variable, volatile the variable which instruct the JVM thread to used the volatile variable from memory do not cache it locally. 

Block Synchronized in java is preferred over method Synchronized in java because using block Synchronized only need to block the critical section of the code instead of the whole method. In java Synchronized comes with cost of performance therefore we need to synchronized only critical section of the code.


**<span style="color:#c60">Example of Synchronized method</span>**

Using Synchronized keyword along with the method is the easy way to use the Synchronized keyword in front of the method. We need to take care of the static and non static Synchronized method. Because static Synchronized method locks on class object lock and non static Synchronized method locks on current object. It’s possible that both method can run parallel.


	public class ViewCount{

	  private static int count = 0;

	  public static synchronized int getCount(){
	    return count;
	  }

	  public synchoronized setCount(int count){
	     this.count = count;
	  }

	}

In above example not implemented the synchronized code not properly synchronized getCount() and seCount() not lock on the same object and can be run on the parallel which may result the incorrect value. In example getCount() will lock on class object ViewCount.class  while setCount() will locked on current object (this).  To make this code proper synchronized in java you need either make both method static or non static or used synchronized block instead of synchronized method.


**<span style="color:#c60">Example of Synchronized Block</span>**

Using Synchronized block 	in java is also similar to Synchronized method. Only important lock the section that is critical. See the below example

	public class Singleton{

	private static volatile Singleton _instance; 

	public static Singleton getInstance(){
	 	if(_instance == null){ 
		 	synchronized(Singleton.class){	 	
		 	 if(_instance == null) _instance = new Singleton(); 
		 	} 
		} 
	 return _instance; 
	}

	}

The above example is double checked locking single tone. We only lock the critical section of the code.

**<span style="color:#c60">Important point of java synchronized keyword.</span>**

1.	You can used Synchronized keyword and block
2.	Whenever the java thread entered into the synchronize blocked it locked the critical section and release the lock when it completed. However they may be release the lock in case of exception or error.
3.	Java synchronized incurs the cost of performance.
4.	Java synchronized block is better than synchronized keyword.
5.	You can’t use the synchronized keyword with the variable. You can use the volatile to make the variable the synchronized.
6.	Do not use the synchronized on the non final variable on synchronized block in java.

		private static final String LOCK = "lock"; //not recommended 
		private static final Object OBJ_LOCK = new Object(); //better 
		public void process() {
			synchronized(LOCK) { 
			........ 
			} 
		}

7.	It’s not recommended to use the String object as the lock in Java synchronized block.

		private String lock = new String("lock");
		synchronized(lock){
		    System.out.println("locking on :"  + lock);
		}


7.	It’s not recommended to use the String object as the lock in Java synchronized block.
