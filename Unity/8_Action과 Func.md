# 🛒 Action과 Func

Action과 Func는 델리게이트(delegate)를 편하게 사용하기 위해 만들어진 클래스로 두 클래스의 차이는 반환값이 있냐 없냐이다.

Action : 반환값 X <br>
Func : 반환값 O

<br>

## 💻 코드 사용법
```C#
[ 선언 방법 ]
// 델리게이트 선언
delegate void MyDelegate<T1, T2>(T1 a, T2 b);
MyDelegate<int, int> myDelegate;

// Action 선언
Action<int, string> myAction;
// Func 선언
Func<int, string, string> myFunc;

[ 사용 법 ]
myDelegate.Invoke(10, 20);
myAction(30, "Hello");
myFunc(20, "World");

// 람다식 사용법
myDelegate = (int a, int b) => { int sum = a+b; };
```
델리게이트 방식은 선언하고 사용하는 방식이라면 Action과 Func은 클래스안에 구성되어 있기 때문에 쉽게 사용이 가능하다.
<br><br>

- `코드 설명`
```C#
Action<T1, T2, ...> 변수이름
Func<T1, T2, ..., 반환매개변수> 변수이름
```
받을 변수의 개수는 0~16개 까지 가능하며 Func 같은 경우 <> 마지막에는 반환시킬 매개변수를 넣으면 된다. 

델리게이트를 쉽게 응용할 수 있는 Action과 Func이 있기 때문에
델리게이트를 쓸 상황이 거의 없다. <br>
하지만 방법의 차이이므로 Action, Func은 네임스페이스를 호출할 약간의 번거로움이 있다.<br>
델리게이트가 많아진다면 Action, Func을 써준다면 가독성이 좋아질 것 같다.

<br>

## 💡 참고 사이트
- [[케이디 C# 강좌]](https://www.youtube.com/watch?v=7H3MHXfFkhI)
- [[루키스 유니티 Part3 강좌]](https://www.inflearn.com/course/mmorpg-%EC%9C%A0%EB%8B%88%ED%8B%B0)