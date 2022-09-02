# 🎆 유니티 EventSystems 🥢

EventSystem은 키보드, 마우스, 드래그, 드롭, 터치 등을 사용하고 싶을 때 입력 기반 애플리케이션의 오브젝트에 이벤트를 전송하는 방법이다. 

<br>

## ✨ EventSystem 사용방법
1. Raycast를 받을 수 있어야 한다.
2. 어떤 종류의 이벤트인지(클릭, 드래그, 드롭 등등) 감지할 수 있어야 한다. <br> (Image에서 Raycast true/false 체크 가능, 기본값=true)

<br>

## 💻 EventSystem 코드 작성
```C#
using UnityEngine;
using UnityEngine.EventSystems;

public class MouseTest : MonoBehaviour, IPointerClickHandler, IBeginDragHandler, IDragHandler, IEndDragHandler, IDropHandler
{
    // Interface : IPointerClickHandler
    public void OnPointerClick(PointerEventData eventData)
    {
        // 마우스 우클릭 시 마우스 커서에 위치한 오브젝트에서 실행.
        if (eventData.button == PointerEventData.InputButton.Right){

        }
    }

    // Interface : IBeginDragHandler
    public void OnBeginDrag(PointerEventData eventData)
    {
        // 마우스를 드래그한 순간
    }

    // Interface : IDragHandler
    public void OnDrag(PointerEventData eventData)
    {
        // 마우스가 드래그 중일 때 
    }

    // Interface : IEndDragHandler
    public void OnEndDrag(PointerEventData eventData)
    {
        // 마우스 커서에서 손을 땔 때 (아무대서나)
    }

    // Interface : IDropHandler
    public void OnDrop(PointerEventData eventData)
    {
        // 마우스 커서에서 손을 땔 때 (OnDrop()을 가진 객체 위에서)
    }
}
```

`[IPointerClickHandler] Interface`
- OnPointerClick() 메소드를 정의한다.
- 마우스 커서 위치에 존재하는 객체가 해당 메소드를 가지고 있을 경우 실행할 수 있다.

`[IPointerEnterHandler, IPointerExitHandler] Interface`
- OnPointerEnter(), OnPointerExit() 메소드를 정의한다.
- 해당 메소드를 가진 객체에 마우스 커서를 갔다 놓거나, 빠져 나올 때 실행되는 메소드이다.

`Mouse 드래그&드롭`
- 인벤토리에서 아이템을 옮겨야할 경우 아래와 같은 메소드를 정의하면 된다.
- OnBeginDrag() -> OnDrag() -> OnEndDrag()
- OnBeginDrag() -> OnDrag() -> OnDrop() -> OnEndDrag()
- OnEndDrag()와 OnDrop()을 호출할 때 OnDrop() 먼저 호출된다.

<br>

최근 인벤토리를 어떤식으로 구현해야할지 고민 중이었는데 유니티에서 제공해주는 인터페이스를 통해 편하게 구현할 수 있을 것 같다.

<br>

## 💡 참고 사이트
- [강좌 : 케이디 FPS 서바이벌 디펜스 Part 16 인벤토리 드래그&드롭](https://www.youtube.com/watch?v=qkRZ8kaChuQ&list=PLUZ5gNInsv_Nzex8Cvxce_1zjUf0cNWY9&index=19)
- [공부하는 식빵맘 Blog](https://ansohxxn.github.io/unitydocs/unityengine-eventsystems/)
- [Unity Documentation 유니티 공식 홈페이지](https://docs.unity3d.com/kr/530/Manual/EventSystem.html)