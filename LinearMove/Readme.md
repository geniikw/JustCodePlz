- Super simple move

![e1](teleport.gif)
```csharp
public class MoveToTarget : MonoBehaviour {
    public Transform target;
    void Start () {
        if (target == null)
            return;
 
        transform.position = target.position;
    }
}
```

- move 1m/s.

![e2](move1mpersec.gif)
```csharp
public class MoveToTarget : MonoBehaviour {
 
    public Transform target;
    public float speed = 1f;

    private void Update()
    {
        if (target == null)
            return;
            
        transform.positon = Vector3.MoveTowards(transform.position, target.position, Time.deltatime*speed);
    }
 
}
```

- move 1m for 1sec

![e3](move1mfor1sec.gif)
```csharp
public class MoveToTarget : MonoBehaviour {
 
    public Transform target;
    public float moveTime = 1f;
 
    private void Start()
    {
        if (target == null)
            return;
        StartCoroutine(Tween(target.position, moveTime));
    }
    IEnumerator Tween(Vector3 end, float time)
    {
        var t = 0f;
        var start = transform.position;
        while(t < 1f)
        {
            t += Time.deltaTime/time;
            transform.position = Vector3.Lerp(start, end, t);
            yield return null;
        }
    }
}

```

- move 1m for 1sec with curve

![e3](movecurvetime1.gif)
![e3](movecurvetime.gif)
![e3](movecurvetime3.gif)

```csharp
public class MoveToTarget : MonoBehaviour {
 
    public Transform target;
    public float moveTime = 1f;
    public AnimationCurve curve = AnimationCurve.Linear(0,0,1,1);
    
    IEnumerator Start()
    {
        var t = 0f;
        var start = transform.position;
        var end = target.position;
        while(t < 1f)
        {
            t += Time.deltaTime / moveTime;
            transform.position = Vector3.LerpUnclamped(start, end, curve.Evaluate(t));
            yield return null;
        }
    }
}


```
