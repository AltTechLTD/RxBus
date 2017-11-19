# RxBus
An event bus in 27 LOC. 

1. Add Kotlin, [RxJava 1](https://github.com/ReactiveX/RxJava) and [RxAndroid](https://github.com/ReactiveX/RxAndroid) to you project.

2. Copy and past the RxBus code into project 

```kotlin
object RxBus {

  private val mBusSubject = SerializedSubject(BehaviorSubject.create<Any>())
  lateinit var uiScheduler: Scheduler

  operator fun invoke(sch: Scheduler) {
    uiScheduler = sch
  }

  fun <T> register(eventClass: Class<T>): Observable<T>
      = mBusSubject.filter({ event -> eventClass.isInstance(event) })
      .observeOn(uiScheduler)
      .onBackpressureBuffer()
      .map<T>({ eventClass.cast(it) })

  fun post(event: Any) = mBusSubject.onNext(event)

}
```

3. Create your event class eg
```kotlin
  class Event
```

4. Instantiate RxBus in `onCreate` of your application class with `RxBus(AndroidSchedulers.mainThread())`

5. Register the event and wait to consume them.
```kotlin
RxBus.register(Event::class.java)
    .subscribe{ event ->
        // trigger something
    }
```

6. Post and event with 
```kotlin
RxBus.post(Event())
```

7. Be happy!!!
