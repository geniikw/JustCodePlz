1.Super simple move

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

2.move 1m/s.

![e2](move1mper1sec.gif)
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
