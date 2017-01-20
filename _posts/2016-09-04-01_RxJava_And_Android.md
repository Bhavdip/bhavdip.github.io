---
layout: post
title:	Reactive Extension Series
image:	android_runtime_permission_1.jpg
---

----
# Rx - From .Net to Java

Reactive Programming is Programming Paradigm based on the concept of an asynchronous data flow.
A data flow is like a river:it can be the observed, filtered, manipulated, or merged with a second
flow to create flow for a new consumer.

{{ more }}

A key concept of the Reactive Programming is the event. Events can be awaited, can trigger, procedures,
and can trigger other events.In the same way, we want the total to update when we change some value in
our spreadsheet.Nowadays one the most common case for reactive programming is update UIs;
we have mobile app that we want to rect to network call, connection checker, monitor installed
application. In this wold, the software has to be event-driven.

## Microsoft Reactive Extension
Functional Reactive Programming is an idea from late 90s that inspired. Rx is a Reactive Extension from Microsoft
.NET. Rx provide an easy way to create asynchronous,event-driven programs using observers sequence.

Rx makes the well-know concepts,such as the 'push concepts', easy to implement and consume.

The push approach revers the problem: instead of asking for a result and waiting, the develop simply ask for the
result and get notify when the result is available. The developer provides the clear sequence of reactions that
are going to happen. As you see this push approach, the developer doesn't wait for the result. He will be notified
when the result is arrives.

## Observables
The observable is one of most well know design pattern discussed in popular Design Pattern by Gangs of four.
When one object change, all the other objects depending on it are notified and update automatically.

## The RxJava Observer pattern toolkit
It's include the four players:

* Observable
* Observer
* Subscriber
* Subjects

**Observables** and **Subjects** are two "Producing" entities. **Observer** and **Subscriber** are are two "Consuming" entities.

Observable : When we have to something execute from little level of complexity. Java provide us the classic classes, Thread, Future, FutureTask. When our complexity level goes up, then the solution become more messy and difficult to
maintain.

RxJava Observable were designed to solve these issue.They are easy to use, they can be changed, on sequence.
The Observable life cycle method contains three different events that can easy to compare with the Iterable
of java life cycle events.

| Event | Iterable (pull)(synch)|  Observable (push)(Asynch) |
--- | --- | ---
| Retrieve the data | T next() | onNext(T |
| Discover the error | throws Exception | onError(Throwable)  |
| Complete  | !hasNext() | onCompleted()  |

In Iterable the consumer synchronously pull the values from the producer and the that time the thread is blocked
until these value arrives. Using Observable, the producer asynchronously push the values to the Observer when
the values are available.

## Hot and Cold Observable

There are two type of Observable: Hot Observable and Cold Observable. A hot Observable, typically it start
emitting the values as soon as it created, so any observer who subscribe it may be start receiving the sequence
in the middle.

A cold Observable wait until any Observer subscribe to it before to emit items, so any Observable
is guaranteed to see the sequence from beginning.

## Creating a Observable

Observable.create()

    Observable<Integer> observableStrings = Observable.create(new Observable.OnSubscribe<Integer>)(){
        @override
        public void call(Subscriber<? super Integer> observer){
            for(int i = 0; i < 5 ; i++){
              observer.onNext(i);
            }
            observer.onCompleted();
        }
    });

    observableStrings.subscribe(new observer<Integer>{
      @Override
    public void onCompleted() {
    System.out.println("Observable completed");
    }
      @Override
      public void onError(Throwable e) {
        System.out.println("Oh no! Something wrong happened!");
      }
        @Override
        public void onNext(Integer item) {
          System.out.println("Item is " + item);
        }
    });

In above example, we just keep it simple for loop. We created the Observable Integer items that execute the
for loop on five elements, and emitting them one by one and its complete.

On the other side we subscribe it, and receive the Subscription object. The moment we subscribe, we start to
receive the integer we do not know how many integer we will received.
