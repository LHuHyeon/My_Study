# ๐ Delegate์ Event

์ฌ๋ฌ ๋ฉ์๋์ ๊ฐ์ ๋ฐํ๊ฐ๊ณผ ๊ฐ์๋ฅผ ๋๊ฒจ์ค์ผ ํ๋ ์ํฉ์ผ ๋ Delegate๋ฅผ ์ฌ์ฉํ์ฌ ๊ฐ๋์ฑ์ ๋์ผ ์ ์๋ค.

<br>

## ๐ป Delegate ์ฝ๋
```C#
public class TestA : MonoBehaviour
{
    // ๋ธ๋ฆฌ๊ฒ์ดํธ ์์ฑ
    public delegate void MyDelegate(int value);
    // ์ ์ธ
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
        // ๊ธฐ์กด ๋ฉ์๋ ํธ์ถ๋ฒ.
        SetHP(30);
        SetSP(30);

        // ๋ธ๋ฆฌ๊ฒ์ดํธ๋ฅผ ์ฌ์ฉํ ํธ์ถ๋ฒ
        myDelegate += SetHP;
        myDelegate += SetSP;

        myDelegate(30);
    }
}
```
์์ ์ฝ๋๋ HP์ SP๋ฅผ ๋๊ฐ์ด ํ๋ณต์์ผ์ค ๋๋ฅผ ๊ฐ์ ํ ์ฝ๋์ด๋ค. ๊ธฐ์กด์ ์ฝ๋ ๊ฐ์ผ๋ฉด SetHP(30), SetSP(30) ์ฒ๋ผ ํธ์ถ ํ๋๋ฐ ์ด๋ฌํ ๋ฉ์๋๊ฐ ๋ง์์ง๋ค๋ฉด ๊ทธ๋งํผ ์์ฑํ  ์ฝ๋๋ ๋์ด๋๊ณ  ๋ณด๊ธฐ์๋ ์์ข๋ค.

์ด๋ฌํ ๋ฌธ์ ๋ ๋ธ๋ฆฌ๊ฒ์ดํธ๋ก ํด๊ฒฐ์ด ๊ฐ๋ฅํ๋ค.

myDelegate += SetHp ์ด ์ฝ๋์ฒ๋ผ myDelegate ์์ ๋ฉ์๋๋ค์ ๋ํด์ฃผ๊ณ  ๋ง์ง๋ง์ myDelegate(๊ฐ)๋ฅผ ํธ์ถํ๋ฉด ์์ ์๋ ๋ฉ์๋๋ค์ด ์ ๋ถ ๊ฐ์ ๋๊ฒจ๋ฐ๊ณ  ์คํ๋๋ค.

๋ง์ฝ ๋ค๋ฅธ ํด๋์ค์ ์๋ ๋ฉ์๋๋ ๊ฐ์ง๊ณ  ์ค๊ณ  ์ถ๋ค๋ฉด Event๋ฅผ ์ฌ์ฉํ๋ฉด ๋๋ค.

<br>

## ๐ป Event ์ฝ๋
```C#
public class TestB : MonoBehaviour
{
    int damage;

    void Start()
    {
        // TestA์ ์๋ event ๋ธ๋ฆฌ๊ฒ์ดํธ ๋ณ์์ ๋ฉ์๋ ๋ํด์ฃผ๊ธฐ.
        TestA.OnStart += SetDamage;
    }

    public void SetDamage(int value)
    {
        damage += value;
    }
}

public class TestA : MonoBehaviour
{
    // ๋ธ๋ฆฌ๊ฒ์ดํธ ์์ฑ
    public delegate void MyDelegate(int value);
    // static Event๋ก ๋ธ๋ฆฌ๊ฒ์ดํธ ๋ณ์ ์ ์ธ
    public static event MyDelegate OnStart;
    // MyDelegate myDelegate; <- ๊ธฐ์กด ๋ธ๋ฆฌ๊ฒ์ดํธ๋ ํ์ ์์.

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

์์ ์ฝ๋์ฒ๋ผ event์ ๋ค๋ฅธ ํด๋์ค์์ ๋ฐ์ ์ ์๋๋ก static์ ์๋ ฅํด์ค๋ค. ์ด๋ฌ๋ฉด TestB์์ TestA.OnStart์ ๋ฉ์๋๋ฅผ ๋ํ  ์ ์๊ฒ ๋๋ค.

๋ฉ์๋๋ค์ ๋ชจ๋ ํธ์ถ ์ํฌ๋ ๋ธ๋ฆฌ๊ฒ์ดํธ์ ๋ฐฉ๋ฒ์ด ๋๊ฐ๋ค.

<br>

## ๐ก ์ฐธ๊ณ  ์ฌ์ดํธ
- [[์ผ์ด๋ C# ๊ฐ์ข]](https://www.youtube.com/watch?v=m9_D0DQ4SGU)