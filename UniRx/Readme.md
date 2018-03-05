UniRx Exmaples
=====
작성예정 모든 방향자에 대한 예시를 쓰는것을 목표로 함.

- array to observable, Where(filter)
```csharp
var array = new int[]{0,1,2,3,4};
array.ToObservable().Where(Where => number > 2).Subscribe(n=>Debug.Log(n));
```
result
```
3
4
```

- Select , Where, First
특정영역에 들어왔을때 한번메세지를 전달
```csharp
Observable.EveryUpdate()
  .Select(frameCount=>transform.position)
  .Where(pos => pos.x > 0 && pos.y > 0)
  .First()
  .Subscribe(_=>Debug.Log("Enter"));
```
