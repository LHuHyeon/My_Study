# ๐ ์ ๋ํฐ EventSystems ๐ฅข

EventSystem์ ํค๋ณด๋, ๋ง์ฐ์ค, ๋๋๊ทธ, ๋๋กญ, ํฐ์น ๋ฑ์ ์ฌ์ฉํ๊ณ  ์ถ์ ๋ ์๋ ฅ ๊ธฐ๋ฐ ์ ํ๋ฆฌ์ผ์ด์์ ์ค๋ธ์ ํธ์ ์ด๋ฒคํธ๋ฅผ ์ ์กํ๋ ๋ฐฉ๋ฒ์ด๋ค. 

<br>

## โจ EventSystem ์ฌ์ฉ๋ฐฉ๋ฒ
1. Raycast๋ฅผ ๋ฐ์ ์ ์์ด์ผ ํ๋ค.
2. ์ด๋ค ์ข๋ฅ์ ์ด๋ฒคํธ์ธ์ง(ํด๋ฆญ, ๋๋๊ทธ, ๋๋กญ ๋ฑ๋ฑ) ๊ฐ์งํ  ์ ์์ด์ผ ํ๋ค. <br> (Image์์ Raycast true/false ์ฒดํฌ ๊ฐ๋ฅ, ๊ธฐ๋ณธ๊ฐ=true)

<br>

## ๐ป EventSystem ์ฝ๋ ์์ฑ
```C#
using UnityEngine;
using UnityEngine.EventSystems;

public class MouseTest : MonoBehaviour, IPointerClickHandler, IBeginDragHandler, IDragHandler, IEndDragHandler, IDropHandler
{
    // Interface : IPointerClickHandler
    public void OnPointerClick(PointerEventData eventData)
    {
        // ๋ง์ฐ์ค ์ฐํด๋ฆญ ์ ๋ง์ฐ์ค ์ปค์์ ์์นํ ์ค๋ธ์ ํธ์์ ์คํ.
        if (eventData.button == PointerEventData.InputButton.Right){

        }
    }

    // Interface : IBeginDragHandler
    public void OnBeginDrag(PointerEventData eventData)
    {
        // ๋ง์ฐ์ค๋ฅผ ๋๋๊ทธํ ์๊ฐ
    }

    // Interface : IDragHandler
    public void OnDrag(PointerEventData eventData)
    {
        // ๋ง์ฐ์ค๊ฐ ๋๋๊ทธ ์ค์ผ ๋ 
    }

    // Interface : IEndDragHandler
    public void OnEndDrag(PointerEventData eventData)
    {
        // ๋ง์ฐ์ค ์ปค์์์ ์์ ๋ ๋ (์๋ฌด๋์๋)
    }

    // Interface : IDropHandler
    public void OnDrop(PointerEventData eventData)
    {
        // ๋ง์ฐ์ค ์ปค์์์ ์์ ๋ ๋ (OnDrop()์ ๊ฐ์ง ๊ฐ์ฒด ์์์)
    }
}
```

`[IPointerClickHandler] Interface`
- OnPointerClick() ๋ฉ์๋๋ฅผ ์ ์ํ๋ค.
- ๋ง์ฐ์ค ์ปค์ ์์น์ ์กด์ฌํ๋ ๊ฐ์ฒด๊ฐ ํด๋น ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์์ ๊ฒฝ์ฐ ์คํํ  ์ ์๋ค.

`[IPointerEnterHandler, IPointerExitHandler] Interface`
- OnPointerEnter(), OnPointerExit() ๋ฉ์๋๋ฅผ ์ ์ํ๋ค.
- ํด๋น ๋ฉ์๋๋ฅผ ๊ฐ์ง ๊ฐ์ฒด์ ๋ง์ฐ์ค ์ปค์๋ฅผ ๊ฐ๋ค ๋๊ฑฐ๋, ๋น ์ ธ ๋์ฌ ๋ ์คํ๋๋ ๋ฉ์๋์ด๋ค.

`Mouse ๋๋๊ทธ&๋๋กญ`
- ์ธ๋ฒคํ ๋ฆฌ์์ ์์ดํ์ ์ฎ๊ฒจ์ผํ  ๊ฒฝ์ฐ ์๋์ ๊ฐ์ ๋ฉ์๋๋ฅผ ์ ์ํ๋ฉด ๋๋ค.
- OnBeginDrag() -> OnDrag() -> OnEndDrag()
- OnBeginDrag() -> OnDrag() -> OnDrop() -> OnEndDrag()
- OnEndDrag()์ OnDrop()์ ํธ์ถํ  ๋ OnDrop() ๋จผ์  ํธ์ถ๋๋ค.

<br>

์ต๊ทผ ์ธ๋ฒคํ ๋ฆฌ๋ฅผ ์ด๋ค์์ผ๋ก ๊ตฌํํด์ผํ ์ง ๊ณ ๋ฏผ ์ค์ด์๋๋ฐ ์ ๋ํฐ์์ ์ ๊ณตํด์ฃผ๋ ์ธํฐํ์ด์ค๋ฅผ ํตํด ํธํ๊ฒ ๊ตฌํํ  ์ ์์ ๊ฒ ๊ฐ๋ค.

<br>

## ๐ก ์ฐธ๊ณ  ์ฌ์ดํธ
- [๊ฐ์ข : ์ผ์ด๋ FPS ์๋ฐ์ด๋ฒ ๋ํ์ค Part 16 ์ธ๋ฒคํ ๋ฆฌ ๋๋๊ทธ&๋๋กญ](https://www.youtube.com/watch?v=qkRZ8kaChuQ&list=PLUZ5gNInsv_Nzex8Cvxce_1zjUf0cNWY9&index=19)
- [๊ณต๋ถํ๋ ์๋นต๋ง Blog](https://ansohxxn.github.io/unitydocs/unityengine-eventsystems/)
- [Unity Documentation ์ ๋ํฐ ๊ณต์ ํํ์ด์ง](https://docs.unity3d.com/kr/530/Manual/EventSystem.html)