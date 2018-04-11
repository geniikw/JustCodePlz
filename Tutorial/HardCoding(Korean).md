*본 글에 적힌 코드는 테스트하지 않았습니다.

# 하드코딩(1)

하드코딩이란 말을 자주 씁니다. 하드코딩은 무엇이고 왜 나쁠까요?

다음은 간단하게 작성한 하드코딩 입니다.

```csharp

public class HardCoding : Monobehaviour{
	public void Update(){	
		if(Input.GetMouseDown(0)){
			transform.Translate(Vector.Forward * Time.deltaTime * 10f);
		}
	}
}
```

 마우스버튼이 눌러져있는지 확인해서 앞으로 이동하는 코드입니다.

이렇게 간단한 코드도 하드코딩이라고 합니다. 왜 그럴까요? 

하드코딩의 정의는 위키피디아에 찾아서 간략하게 말하면 

[데이터를 코딩하는 것]을 말합니다. 그렇다면 위 코드에서

하드코딩되어 있는 것은 무엇일까요?

조금 아는 사람이라면 Speed가 10f으로 고정되어 있다고 말할 것입니다.

쪼끔 더 아는 사람이라면 방향이 Vector3.Forward으로 되어 있다고 말할 것 같습니다.

만약 KeyCode까지 생각한 사람이면 90점 정도일 것 같습니다.

여기까지만 고쳐보죠.

```csharp 
public class LittleHardCoding : Monobehaviour {
	public Vector3 direction = Vector3.Forward;
	public float speed = 10f;
	public KeyCode keyCode = KeyCode.Mouse0;

	public void Update(){
		if(Input.GetKeyDown(keyCode)){
			transform.Translate(direction  * Time.deltaTime * speed);
		}
	}
}
```

이렇게 작성하는 함으로써 어떤 이득이 있을까요?

만약 뒤로 가는 코드를 작성해야할 경우 간단히 Direction만 고치면 됩니다.

코딩없이 자유롭게 speed와 direction을 바꿀 수 있고 키또한 자유롭게 고칠 수 있습니다.

이렇게 Data에 따라서 변하게 프로그래밍 하는 것을 Data Driven 이라고 합니다.

마지막 10점을 얻기위한 요소는 무엇일까요?

제가 생각한 답은 Update가 고정되어 있다는 것이입니다.

Update종류도 다양해서 필요에따라 LateUpdate나 FixedUpdate를 써야될 수도 있겠죠.

이런 것을 전부 코딩하면 글자수가 비약적으로 늘어납니다. 

제가 생각하기에도 저기에 각각 모든 Update문을 작성하고 

public enum UpdateType {
	Update,
.
.
.
	LateUpdate	
}

이런 enum을 작성해서 하나의 Update만 돌아가게하는건 좀 이상해보입니다.

나머지는 다음에 2편에 작성하겠습니다. 'ㅅ'/
