# [gameObject.transform.position.y += 100;]가 불가능한 이유
~~gameObject.transform.position.y = 10;마저도 안된다.~~

왜 저게안될까? 생각보도록 하자

## transform.position이란? Property에 대하여
Transform은 유니티엔진에서 객체의 위치정보를 관리하는 클래스이다.
position은 그 Transform의 프로퍼티이다.
C++로 되어 있을 내부 소스를 볼 수 없어서 조심스럽지만
아마도(?) c#으로 풀어보면 이렇게 되어 있을 것이다. ~~아님 말고~~

```csharp
public class Transform{
    Matrix4x4 m_mat44;
    public Vector3 postion{
      get{
          Vector3 pos;
          pos.x = m_mat44.m03;
          pos.y = m_mat44.m13;
          pos.z = m_mat44.m23;
          return pos;
      }
      set{
          m_mat44.m03 = value.x;
          m_mat44.m13 = value.y;
          m_mat44.m23 = value.z;
      }
    }
}
```

회전이나 스케일링 등을 할 때 계산에서 이점이 있기때문에 Matrix를 쓴다거나 하는 부분은
넘어가자. 요점은 position으로 가져오는 Vector3값은 매번 계산되어서 새로 만들어지는 값이라는 것이다.
Vector3 m_position이라는 값이 있고 이걸 직접넘겨준다고 가정을 해도 결과는 달라지지 않는다.
프로퍼티는 함수개념이고 m_position의 주소를 리턴하는게 아닌 복사된 Vector3 값을 리턴하기 때문이다.

## class vs struct
위 문제를 알기 위해서 일단 생각해야될 것은 class와 struct의 차이에 대하여 알고 있어야 한다.
올바른 모범생이라면 class는 heap memory에 struct는 stack memory를 
사용한다는 것 정도는 알고 있을 것이다. 여기서는 그런 자질구래(?)한 이야기는 남겨두고
실제 예를 통하여 뭐가 다른지 알아보자.

```csharp
///첫번째 예제
public class MyClass{
    public int power;
}

public struct MyStruct{
    public int power;
}
```

위 두가지의 객체를 통해 알아보자. 이 객체들이 함수의 변수로 사용될 때 어떻게 될지 알아보자
```csharp
public static class Program{
    public static void Main(){
        MyClass c = new MyClass();
        MyStruct s = new MyStruct();
        c.power = 1;
        s.power = 1;
        
        MyFunc(c);
        MyFunc2(s);
        
        Console.WriteLine(c.power);
        Console.WriteLine(s.power);
    }
    
    public void MyFunc(MyClass c){
        c.power += 100;
    }
    
    public void MyFunc2(MyStruct s){
        s.power += 100;
    }   
}
```
위 코드를 보고 결과를 예측해보자. 여기서 약간 의문이 들거나 결과를 틀리게 예측한다면 
class 와 struct, stack, heap memory등에 대하여 공부해야 한다.
여기서는 다음과 같이 결과가 출력될 것이다.

```
101
1
```

class는 함수의 변수로 사용될 때 주소값을 넘겨준다. 그래서 함수 내부에서 바꾼값이 외부에서도 유지가 된다.
반면 struct는 값자체를 복사해서 넘겨준다. 그래서 함수내부에서 값을 변경해도 함수가 끝나면 값이 사라질 뿐이다.

## gameObject.transform.position.y를 분석해보자.

자 그렇다면 제목의 코드를 하나하나 살펴보자.
```csharp
void Start(){
    gameObject.transform.position.y += 100f;
}
```

이 짧은 코드를 조각조각 내서 살펴보자. 

* gameObject.transform

이 함수는 GameObject의 transform이란 {get;} 프로퍼티이다. 연관된 Transform class의 주소를 리턴해준다.
따라서 이 값을 변경하면 해당하는 주소에 있는 값도 변경될 것이다. 그냥 가볍게 살펴보자.

```csharp
public GameObject : /*Skip*/{
    .
    .
    .
    public Transform transform {get;}
    .
    .
    .
}

void Start(){
    transform.position = Vector3.zero;
    Debug.Log(transform.position);//(0,0,0);

    var t = gameObject.transform;
    t.position = Vector3.one;
    
    Debug.Log(transform.position);//(1,1,1)
}
```

*여기서 이런 의문을 가질 수도 있다. {set;}이 없는데 왜 transform을 수정 할 수 있는건가요?
transform은 주소값이다. 주소를 수정하는건 불가능 하지만 그 주소값으로 접근한 transform의 맴버나 프로퍼티에 접근할 수 있다.
헷갈릴수도 있다고 생각되는데 이해가 안되면 곰곰히 차근차근 생각해보자.*

* transform.position

여기가 핵심이다. 정의를 먼저 보자. 다음과 같을 것이다.
```csharp
public class Transform : /*Skip*/{
    .
    .
    .
    public Vector3 position { get; set; }
    .
    .
    .
}
```
 위와 다른 점은 set;이 정의되어 있다는 점. Vector3가 struct라는 점. 두 가지를 생각해 볼수 있다.
그렇다면 아래 예제를 보고 결과를 예측해보자. 핵심은 위 두가지이다.
 ```csharp
 void Start(){
    transform.position = Vector3.one;
    var pos = transform.position;
    pos.y + = 10f;
    
    Debug.Log(transform.position);//(1,1,1) or (1,10,1)
 } 
 ```

당연히 (1,1,1)이 정답이다. transform.position은 복사되어 pos에 저장한 것이고
pos값을 변경해봤자. transform.position값은 변경되지 않는다.
transform.position으로 가져온 pos를 변경해도 원본값은 그대로 1,1,1인 것이다.

* position.y

position은 Vector3이다. Vector3는 다음과 같을 것이다.

```csharp
public struct Vector3{
    public float x;
    public float y;
    public float z;
    .
    .
    .
}
```

프로퍼티가 아닌 맴버변수임을 확인할 수 있다. 
float은 기본 자료형임을 heap에 저장할 필요는 없다. 그냥 숫자자체를 던져주면 된다.
그래서 struct와 같은 식으로 생각하면 된다. 어떻게 보면 좀 바보예제인데 위와 같은방식으로 보면 다음과 같다.

```csharp

void Start(){
	var pos = new Vector3(1,1,1);
	var x = pos.x;
	x = 3;
	
	Debug.Log(pos); // (1,1,1)
}
```

숨쉬듯이 알고 있어서 당연한 예제이다. 이런 결과가 스택메모리의 특성이라는 것정도만 
다시 집고 넘어가자.


## 만약 위 코드가 된다고 가정해보자.
여기까지 읽었으면 어렴풋이 왜 안되는지 알 것이다. 확실하게 하기 위해서
된다는 가정하에 어떤 일이 일어나는지 생각해보자.
```csharp
void Start(){
    gameObject.transform.position.y += 100f;
    gameObject.transform.position.y =  gameObject.transform.position.y + 100f;
    //or
    gameObject.transform.position.y = 100;
}
```

위나 아래나 똑같은데 아래가 더 간단하니 아래기준으로 풀어서 보자.
```csharp
void Start(){
    gameObject.transform.position.y = 100;
    //를 풀면
    var t = gameObject.transform;
    var pos = t.position;//position은 프로퍼티이다. 값의 복제가 일어남.
    pos.y = 100;
}
```

... 아무일도 일어나지 않았다! 만약 된다고 컴파일러가 알려주지 않으면
transform.position.y = 100; 이라는 코드는 코더의 의도와는 달리
아무일도 일어나지 않는 코드가 되는 것이다.

## 이런 경우는 된다.
만약 Transform의 position이 Property가 아니라 단순 맴버였으면 그냥 된다. 간단하게 생각해서 다음과 같은 경우는 아무런 문제 없이 잘 된다. 
여기서는 position으로 접근해도 복제가 이루어지지 않고 그냥 값이 잘 변경된다.
```csharp
public class MyTransform{
	public Vector3 position;
}

void Start(){
	var t = new MyTransform();
	t.position.y = 10f; //가능
}
```

## 그럼 왜 javascript(unityScript)에서는 됨?
놀랍게도(?) 다음과 같은 코드가 동작하는것을 확인할 수 있다.(5.6에서 확인)
```javascript
#pragma strict
function Start () {
    transform.position.y = 100;
}
function Update () {
	
}
```

이유는 간단하다. unity3d의 js컴파일러가 위 코드를 아래와 같이 바꾸기 때문이다.
```csharp
public class mono : Monobehavour {
    public void Start(){
        var temp = transform.postion;
        temp.y = 100;
        transform.position = 100;
    }
}
```

## 그럼 어떻게 해야되는데?

필자가 생각하는 방법들은 다음과 같다.

```csharp
void Start(){
    //transform.position.y += 10f;
    transform.position += Vector3.up * 10;
    
    transform.Translate(0,10,0);
    
    
    //transform.position.y = 10f;
    var temp = transform.position;
    temp.y = 10f;
    transform.position.;
    
    var p = transform.position;
    transform.position = new Vector3(p.x, 10f, p.z);
    
    transform.SetY(10f);    
}

public static TransformUtil {
    public static void SetY(this Transform t, float y){
	    var temp = transform.position;
	    temp.y = 10f;
	    transform.position = temp;
    }
}
```

이렇게 쓰면 원하는 결과를 얻을 수 있다. 아이러니하게도
단순 대입하는 연산자가 더 길다.

## 결론
 간단한 예시이고 별것 아닌것 같지만 이 모든 과정을 이해하고 설명할 수 있으면 
유니티를 사용하는데 있어서 C++,C는 안 배워도 될정도라 생각한다. C#에서 메모리란 무엇인지 어떻게 사용되는지
GC와의 관계는 스마트포인터는 무엇인가 등등 뭐 알아야 할 것은 많지만 가장 중요한 것은
그 메모리가 코드에서 어떻게 활용되는가이다. 
이 예제를 통하여 그 과정을 한번 살펴보고 정리하는 시간이 되었으면 한다.

