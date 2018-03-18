UniRx Exmaples
=====
작성예정 모든 방향자에 대한 예시를 쓰는것을 목표로 함.

# Factory

## Observable.Return

## Observable.Repeat

## Observable.Range

## Observable.Defer

## Observable.Timer

## Observable.TimerFarme

## Observable.Interval

## Observable.TimerFrame

## Observable.IntervalFrame

## Observable.Create

## Observable.Throw

## Observable.Empty

## Observable.Never

## Observable.FromEvent

## Observable.EveryUpdate

## Observable.FixedEveryUpdate


# Message Filter

## Where

## Distinct

## DistinctUntilChanged

## Throttle

## ThrottleFrame

## ThrottleFirst

## ThrottleFirstFrame

## First

## Single/SingleOrDefault

## Last/LastOrDefault

## Take

## TakeWhile

## TakeUntil

## Skip

## SkipWhile

## SkipUntil

## OfType

## IgnoreElements

# Stream Merge

## Amp

## Zip

## ZipLatest

## CombineLastest

## Merge

## Concat

## SelectMany

# Transform Stream

## ToReactiveProperty

## ToReadOnlyReactiveProperty

## ToArray

## ToAwaitableEnumerator

## ToLazyTask

# stream branch?

## Publish

## PublishLast

## Replay

## Multicast

## RefCount

## Scan

## Buffer

## Select

## Cast

## SelectMany

## Materialize

## TimeInterval

## TimeStamp

## AsUnitObservable

# Handle Async

## Observable.ToAsync/Start

## ObserveOn

## ObserveOnMainThread

## SubscribeOn

## StartAsCoroutine // ToLazyTask

# Handle Error 

## Retry

## Catch

## CatchIgnor

## OnErrorRetry

# OnComplete

## Repeat, RepeatSafe, RepeadtUntilDisable, RepeatUntilDestroy

## Finally

# other

## StartWith

## Wait

## Do


# Mix

## array to observable, Where(filter)
```csharp
var array = new int[]{0,1,2,3,4};
array.ToObservable().Where(Where => number > 2).Subscribe(n=>Debug.Log(n));
```
result
```
3
4
```

## 1sec Timer

```csharp
Observable.Interval(TimeSpan.FromSeconds(1))
      .SkipWhile(t=>t<4)
      .TakeWhile(t=>t<10)
      .Subscribe(t=>Debug.Log(t), ()=>Debug.Log("Complete"));
```
result
```
(4sec lator)4
(1sec later)5
.
.
.
(1sec later)9
(1sec later)Complete
```

# Control

## Select , Where, First
특정영역에 들어왔을때 한번메세지를 전달
```csharp
Observable.EveryUpdate()
  .Select(frameCount=>transform.position)
  .Where(pos => pos.x > 0 && pos.y > 0)
  .First()
  .Subscribe(_=>Debug.Log("Enter"));
```
