

-![Super Simple move](LinearMove/teleport.gif)
```csharp
public class MoveToTarget : MonoBehaviour {
 
    public Transform target;
 
    // Use this for initialization
    void Start () {
        if (target == null)
            return;
 
        transform.position = target.position;
    }
    
}
```
