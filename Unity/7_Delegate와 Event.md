# 🛒 Delegate와 Event

여러 메소드에 같은 반환값과 개수를 넘겨줘야 하는 상황일 때 Delegate를 사용하여 가독성을 높일 수 있다.

<br>

## 💻 Delegate 코드
```C#
public class TestA : MonoBehaviour
{
    // 델리게이트 생성
    public delegate void MyDelegate(int value);
    // 선언
    MyDelegate myDelegate;

    int hp;
    int sp;

    public void SetHP(int value)
    {
        hp += value;
    }
    public void SetSP(int value)
    {
        sp += value;
    }

    void Start()
    {
        // 기존 메소드 호출법.
        SetHP(30);
        SetSP(30);

        // 델리게이트를 사용한 호출법
        myDelegate += SetHP;
        myDelegate += SetSP;

        myDelegate(30);
    }
}
```
위의 코드는 HP와 SP를 똑같이 회복시켜줄 때를 가정한 코드이다. 기존의 코드 같으면 SetHP(30), SetSP(30) 처럼 호출 했는데 이러한 메소드가 많아진다면 그만큼 작성할 코드도 늘어나고 보기에도 안좋다.

이러한 문제는 델리게이트로 해결이 가능하다.

myDelegate += SetHp 이 코드처럼 myDelegate 안에 메소드들을 더해주고 마지막에 myDelegate(값)를 호출하면 안에 있는 메소드들이 전부 값을 넘겨받고 실행된다.

만약 다른 클래스에 있는 메소드도 가지고 오고 싶다면 Event를 사용하면 된다.

<br>

## 💻 Event 코드
```C#
public class TestB : MonoBehaviour
{
    int damage;

    void Start()
    {
        // TestA에 있는 event 델리게이트 변수에 메소드 더해주기.
        TestA.OnStart += SetDamage;
    }

    public void SetDamage(int value)
    {
        damage += value;
    }
}

public class TestA : MonoBehaviour
{
    // 델리게이트 생성
    public delegate void MyDelegate(int value);
    // static Event로 델리게이트 변수 선언
    public static event MyDelegate OnStart;
    // MyDelegate myDelegate; <- 기존 델리게이트는 필요 없음.

    int hp;
    int sp;

    public void SetHP(int value)
    {
        hp += value;
    }
    public void SetSP(int value)
    {
        sp += value;
    }

    void Start()
    {
        OnStart += SetHP;
        OnStart += SetSP;

        OnStart(30);
    }
}
```

위의 코드처럼 event에 다른 클래스에서 받을 수 있도록 static을 입력해준다. 이러면 TestB에서 TestA.OnStart에 메소드를 더할 수 있게 된다.

메소드들을 모두 호출 시킬땐 델리게이트와 방법이 똑같다.

<br>

## 💡 참고 사이트
- [[케이디 C# 강좌]](https://www.youtube.com/watch?v=m9_D0DQ4SGU)