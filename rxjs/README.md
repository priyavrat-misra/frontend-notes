# RxJS

### Observable, Observer and Subcriptions
- The callback function passed in the constructor will run when `.subscribe()` is called.
- If no observer is passed in `.subscribe()` then the `.next()`'s will not execute.
- As strings are emitted by `observable$`, that's why the type is `Observable<string>`.
- When the Subscription ends, the _Teardown logic_ runs. It provides a way to cancel ongoing processes that were initialized by the Observable.

<a href="https://stackblitz.com/edit/rxjs-aaqebw?devToolsHeight=50&file=index.ts" target="_blank"><img align="right" src="https://img.shields.io/badge/Run_with_Stackblitz-gray?logo=Stackblitz&logoColor=1389FD" alt="Run in Stackblitz"></a><br>
```typescript
import { Observable, Subscription, Observer, Subscriber } from 'rxjs';

const observable$: Observable<string> = new Observable<string>(
  (subscriber: Subscriber<string>) => {
    console.log('Observable executed.');
    subscriber.next('Alice'); // emit 'Alice'
    setTimeout(() => subscriber.next('Bob'), 1000); // emit 'Bob' after 1000 ms
    setTimeout(() => {
      subscriber.next('Charlie');
      subscriber.complete();
    }, 3000); // will not be emitted for Subscription 2
    setTimeout(
      () => subscriber.error(new Error('Something went wrong.')),
      4000
    );
    return () => {
      // This function will run during the Teardown phase of the Subscription
      console.log('Teardown logic ran.');
    };
  }
);

const observer: Observer<string> = {
  next: (value: string) => console.log('Subscription 1:', value),
  error: (err) => console.error(err.message),
  complete: () => console.log('Subcription 1 completed.'),
};

// --A----B--------C|-->
const subscription1: Subscription = observable$.subscribe(observer);
// --A----B----|------->
const subscription2: Subscription = observable$.subscribe((value: string) =>
  console.log('Subscription 2:', value)
);
// -------------------->
const subscription3: Subscription = observable$.subscribe();

setTimeout(() => {
  console.log('Unsubscribed 2.');
  subscription2.unsubscribe();
}, 2000);

subscription3.unsubscribe();
```
> **Note** :
> When the Subscriptions ends, the notifications will not be passed to the Observer. This happens because of the intermediate Subscriber object created automatically by **RxJS**, which checks whether the Subscription is still active before passing the notifications to the Observer.

<a href="https://stackblitz.com/edit/rxjs-wpyvpt?devToolsHeight=50&file=index.ts" target="_blank"><img align="right" src="https://img.shields.io/badge/Run_with_Stackblitz-gray?logo=Stackblitz&logoColor=1389FD" alt="Run with Stackblitz"></a><br>
```typescript
const interval$: Observable<number> = new Observable<number>(
  (subscriber: Subscriber<number>) => {
    let counter: number = 0;
    const intervalId: number = setInterval(() => {
      console.log('Emitted: ', counter);
      subscriber.next(counter++);
    }, 1000);
    return () => clearInterval(intervalId);
  }
);

// --0--1--2--3--4|---->
const subscription: Subscription = interval$.subscribe((value: number) =>
  console.log(value)
);
setTimeout(() => {
  console.log('Unsubscribed.');
  subscription.unsubscribe();
}, 5000);
```
> **Warning** :
> Here, if we don't keep the teardown logic, the code inside the Observable will keep on running even after we unsubscribe. Always cleanup the Observable after the Subcription ends to avoid left over code running and memory leaks.

