# ๐บ UI ์๋ํ

Inflearn์ ์๋ Rookiss๋์ [C#๊ณผ ์ ๋ํฐ๋ก ๋ง๋๋ MMORPG ๊ฒ์ ๊ฐ๋ฐ ์๋ฆฌ์ฆ] Part3 ์ ๋ํฐ ๊ฐ์๋ฅผ ๋ฃ๊ณ  ๋ณต์ตํ ๋ด์ฉ์๋๋ค.

๋ฃจํค์ค๋์ ๊ฐ์๋ฅผ ๋ณด๊ณ  ํ์ตํ๋ฉด์ UI ์๋ํ๊ฐ ์ค์ํ๊ฑฐ ๊ฐ์ ์ดํด๋๋ฅผ ๋์ด๊ธฐ ์ํด ์ ๋ฆฌํ์์ต๋๋ค.

<br>

## โจ ์คํฌ๋ฆฝํธ ๊ตฌ์กฐ
---
`[UI ๊ธฐ๋ณธ ๊ตฌ์กฐ]`
- ๐ UI_Base : ๋ชจ๋  UI์ ์กฐ์ ์คํฌ๋ฆฝํธ๋ก UI ๊ด๋ จ ์คํฌ๋ฆฝํธ๋ ๋ชจ๋ ์ด ์คํฌ๋ฆฝํธ๋ฅผ ์์๋ฐ๋๋ค.

  - ๐ UI_Scene : Popup์ฒ๋ผ ๊ฒ์ ๋์ค ์์ฑ๋๋ ๊ฒ์ด ์๋ ์ฌ์ ๋ฏธ๋ฆฌ ์๋ฆฌ์ก๊ณ  ์๋ UI์ ๋ถ๋ชจ ์คํฌ๋ฆฝํธ๋ค.

  - ๐ UI_Popup : ๊ฒ์ ๋์ค์ ์์ฑ๋๋ UI์ ๋ถ๋ชจ ์คํฌ๋ฆฝํธ๋ค.

    - ๐ UI_Button : UI_Popup์ ์์ ์คํฌ๋ฆฝํธ๋ก 
    
    <br>

`[๊ธฐ๋ฅ์ฑ ๊ด๋ จ ์คํฌ๋ฆฝํธ]`
- ๐ UI_EventHandler : UI ์ด๋ฒคํธ๋ฅผ ์ฌ์ฉํ  ๊ฒฝ์ฐ ์ฌ์ฉํ๊ฒ๋  ์คํฌ๋ฆฝํธ.
- ๐ Util : ๊ธฐ๋ฅ์ฑ ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์๋ ์คํฌ๋ฆฝํธ. (์์ ๊ฐ์ฒด, ์ปดํฌ๋ํธ ์ฐพ๊ธฐ ๋ฑ)
- ๐ Define : ๊ด๋ จ ํค์๋๋ฅผ ์ ์ฅํ๊ณ  ์๋ ์คํฌ๋ฆฝํธ.

<br>

`[๋งค๋์ ]`
- ๐ UIManager : ๋ค๋ฅธ ์คํฌ๋ฆฝํธ์์ UI๋ฅผ ์ฌ์ฉํ๊ธฐ ์ํ ๋งค๋์ 

<br>
<br>

## ๐ Define.cs
์ํ ๋ณ์๋ฅผ ์ ์ฅํ๋ ํด๋์ค๋ค.
```C#
public class Define : MonoBehaviour
{
    public enum UIEvent
    {
        Click,
        Drag,
    }
    
    public enum MouseEvent
    {
        Press,      // ๊พน ๋๋ฅผ ๋ ์ํ
        Click,      // ํด๋ฆญ ์ํ (Press ์ํ๊ฐ ๋๋ ์ํ)
    }

    public enum CameraMode
    {
        QuarterView,    // ๋์๋ธ๋ก ๊ฒ์ ๊ฐ์ ์์ 
    }
}
```
<br>

## ๐ UI_EventHandler.cs
๊ฐ์ฒด๊ฐ EventSystems์ ์ฌ์ฉํ  ํ์๊ฐ ์์ ๋ Action์ผ๋ก ์ ์ฅํ์ฌ ์ฌ์ฉํ๋ ํด๋์ค๋ค. (์ต์ ๋ฒ ํจํด ํํ?)
```C#
public class UI_EventHandler : MonoBehaviour, IPointerClickHandler, IDragHandler
{
    public Action<PointerEventData> OnClickHandler = null;
    public Action<PointerEventData> OnDragHandler = null;

    public void OnPointerClick(PointerEventData eventData)
    {
        if (OnClickHandler != null)
            OnClickHandler.Invoke(eventData);
    }

    public void OnDrag(PointerEventData eventData)
    {
        if (OnDragHandler != null)
            OnDragHandler.Invoke(eventData);
    }   
}
```
<br>

## ๐ Util.cs
๊ธฐ๋ฅ์ฑ ๋ฉ์๋๋ฅผ ๊ฐ์ง๊ณ  ์๋ ํด๋์ค๋ค. 
```C#
public class Util
{
    // ์ปดํฌ๋ํธ ์ฐพ์ ํ ์ถ๊ฐํ๊ธฐ
    public static T GetOrAddComponent<T>(GameObject go) where T : UnityEngine.Component
    {
        T component = go.GetComponent<T>();

        if (component == null)
            component = go.AddComponent<T>();

        return component;
    }

    // GameObjec๋ ์ปดํฌ๋ํธ๊ฐ ์๋๋ฏ๋ก Transform์ ํตํด ์ฐพ์
    public static GameObject FindChild(GameObject go, string name = null, bool recursive = false)
    {
        Transform transform = FindChild<Transform>(go, name, recursive);
        if (transform == null)
            return null;
        
        return transform.gameObject;
    }

    // ์์ ๊ฐ์ฒด ์ปดํฌ๋ํธ ์ฐพ๊ธฐ
    public static T FindChild<T>(GameObject go, string name = null, bool recursive = false) where T : UnityEngine.Object
    {
        if (go == null)
            return null;

        // recursive : ์๊ธฐ ์์ ์ ์์ ๊ฐ์ฒด๋ฅผ ๊ฐ์ ธ์ฌ์ง ํ๋จ
        if (recursive == false){
            // go์ ์์๊ฐ์ฒด ์ ๋งํผ
            for(int i=0; i<go.transform.childCount; i++){
                // ์ง์ ๋ ์์๊ฐ์ฒด๋ฅผ transform์ ๋ฐํ
                Transform transform = go.transform.GetChild(i);

                // string.IsNullOrEmpty = ๋น๋ฌธ์์ด์ด๋ฉด true (null ๋๋ "")
                if (string.IsNullOrEmpty(name) || transform.name == name){
                    // ํด๋น T(Button, Text, ...) ์ปดํฌ๋ํธ ๋ฐํ
                    T component = transform.GetComponent<T>();
                    if (component != null)
                        return component;
                }
            }
        }
        else{
            // true์ผ ๊ฒฝ์ฐ ์์์ ์์๊น์ง ๋ค ๊ฐ์ ธ์จ๋ค.
            foreach(T component in go.GetComponentsInChildren<T>()){
                if (string.IsNullOrEmpty(name) || component.name == name)
                    return component;
            }
        }

        return null;
    }
}
```
<br>

`GetOrAddComponent() ๋ฉ์๋` <br>
1. ์ค๋ธ์ ํธ์ ์ปดํฌ๋ํธ๋ฅผ ์ฝ์ ์ ์๊ณ  ์์ ์ ์ถ๊ฐํ  ์ ์๋ค.

<br>

`FindChild<T>() ๋ฉ์๋` <br>
1. Canvas ๊ฐ์ฒด์์ ์๋ ์์๊ฐ์ฒด์ ์ปดํฌ๋ํธ๋ฅผ ์ฐพ๋ ๋ฉ์๋์ด๋ค.
2. GetChild()๋ฅผ ํตํด ์์๊ฐ์ฒด๋ฅผ ์ฐพ์ ์ ์๋ค.
3. ์ฐพ์ ๊ฐ์ฒด์ T ์ปดํฌ๋ํธ๋ฅผ return ํ๋ค.

<br>

`FindChild() ๋ฉ์๋` <br>
1. ์ฐพ์ ๊ฐ์ฒด๊ฐ Canvas๊ฐ ์๋ GameObject์ผ ๊ฒฝ์ฐ ๊ธฐ๋ณธ์ ์ผ๋ก ์ ๊ณต๋๋ Transform ์ปดํฌ๋ํธ๋ฅผ ํตํด ์ฐพ์ ์ ์๋ค.

<br>

## ๐ UI_Base.cs
๋ชจ๋  UI ์คํฌ๋ฆฝํธ์ ์กฐ์ ํด๋์ค๋ก ๋ถ๋ชจ๊ฐ์ฒด์์ ์๋ ๊ฐ์ฒด๋ค์ ์ด๋ฆ์ ๋์๋๋ฆฌ์ ์ ์ฅํ๊ณ  ์ฝ๋๋ก ๋ถ๋ฌ์์ ์ฌ์ฉํ  ์ ์๋ค.

```C#
public class UI_Base : MonoBehaviour
{
    // ์ปดํฌ๋ํธ ํ์ ๋ณ๋ก ๋ด์์ค ๋์๋๋ฆฌ(์๋ฃ๊ตฌ์กฐ)
    protected Dictionary<Type, UnityEngine.Object[]> _objects = new Dictionary<Type, UnityEngine.Object[]>();

    // where T : ๋ถ๋ชจ ํด๋์ค์ ์์ ํด๋์ค๋ง ๊ฐ๋ฅ
    protected void Bind<T>(Type type) where T : UnityEngine.Object
    {
        // C++๊ณผ ๋ค๋ฅด๊ฒ C#์ enum์์ ์๋ ๋ด์ฉ์ ์ฝ์ ์ ์๋ค!
        string[] names = Enum.GetNames(type);   // C# ๊ธฐ๋ฅ
        UnityEngine.Object[] objects = new UnityEngine.Object[names.Length];
        _objects.Add(typeof(T), objects);

        for(int i = 0; i < names.Length; i++){
            if (typeof(T) == typeof(GameObject))
                objects[i] = Util.FindChild(gameObject, names[i], true);
            else
                objects[i] = Util.FindChild<T>(gameObject, names[i], true);

            if (objects[i] == null)
                Debug.Log($"Failed to bind({names[i]})");
        }
    }

    // ์ฌ์ฉ ๋ฉ์๋
    protected T Get<T>(int idx) where T : UnityEngine.Object
    {
        UnityEngine.Object[] objects = null;
        if (_objects.TryGetValue(typeof(T), out objects) == false)
            return null;

        return objects[idx] as T;
    }

    // ์์ฃผ ์ฌ์ฉํ๋ ์ปดํฌ๋ํธ๋ ์ฌ์ฉํ๊ธฐ ์ข๊ฒ ๋ฉ์๋ ์์ฑ
    protected Text GetText(int idx) { return Get<Text>(idx); }
    protected Button GetButton(int idx) { return Get<Button>(idx); }
    protected Image GetImage(int idx) { return Get<Image>(idx); }

    // Event ํธ๋ค๋ฌ์ ๊ดํ ๋ฉ์๋
    public static void AddUIEvent(GameObject go, Action<PointerEventData> action, Define.UIEvent type = Define.UIEvent.Click)
    {
        UI_EventHandler evt = Util.GetOrAddComponent<UI_EventHandler>(go);

        switch (type){
            case Define.UIEvent.Click:
                evt.OnClickHandler -= action;
                evt.OnClickHandler += action;
                break;
            case Define.UIEvent.Drag:
                evt.OnDragHandler -= action;
                evt.OnDragHandler += action;
                break;
        }
    }
}
```
<br>

`_objects<Type, UnityEngine.Object[]>` : Key์๋ ์ปดํฌ๋ํธ Type์ Value์๋ ์ค๋ธ์ ํธ๋ฅผ ๋ด๋ ๋์๋๋ฆฌ๋ค.

- `Bind() ๋ฉ์๋` <br>
   1. ํด๋น ๊ฐ์ฒด๊ฐ ๊ฐ์ง๊ณ  ์๋ ์์๊ฐ์ฒด๋ฅผ _objects์ ์ ์ฅํ  ์ ์๋ค.
   2. C++๊ฐ์ ๊ฒฝ์ฐ Enum์ ์ฝ์ ์ ์๋๋ฐ C#์ ์ฝ์ ์ ์๋ค.
   3. ์ฝ์ด๋๋ฆฐ Enum ์์ ๊ฐ์ ๋งํผ ์ค๋ธ์ ํธ๋ฅผ ์์ฑ ํ ๊ธฐ๋ฅ์ฑ ์คํฌ๋ฆฝํธ์ธ Util.FindChild์ ํตํด ์์ ๊ฐ์ฒด๋ฅผ ์ ์ฅํ๋ค.

<br>

- `Get() ๋ฉ์๋` <br> 
   1. Bind()๋ฅผ ํตํด ์ ์ฅํ ์ค๋ธ์ ํธ๋ฅผ ์ฌ์ฉํ  ์ ์๋ค.
   2. _objects.TryGetValue(Type, ๋ฐ์ ๋ณ์)๋ฅผ ํตํด _objects์ ์ ์ฅ๋์ด ์๋ Key๊ฐ์ Value๋ฅผ ๋นผ์ฌ ์ ์๋ค.

<br>

- `AddUIEvent() ๋ฉ์๋` <br>
   1. Util์์ ์๋ GetOrAddComponent()๋ฅผ ์ฌ์ฉํ์ฌ ๊ฐ์ฒด์ ํ์ํ ์ปดํฌ๋ํธ๋ฅผ ์ถ๊ฐ ๋ฐ ์ฝ์ด์ฌ ์ ์๋ค. ( evt์ ์ ์ฅ )
   2. Util์ ํตํด ํด๋น ๊ฐ์ฒด์ EventSystem์ ์ถ๊ฐํ๋ฉด ๋ง์ฐ์ค ์ด๋ฒคํธ๋ฅผ ์ฌ์ฉํ  ์ ์๋ค. 

<br>

## ๐ UI_Popup.cs์ UI_Scene.cs
ํด๋น ์คํฌ๋ฆฝํธ๋ UI_Base๋ฅผ ๊ธฐ๋ณธ์ ์ผ๋ก ์์ ๋ฐ๋๋ค. <br>
UI_Popup.cs๋ ๊ฒ์ ์งํ ์ค ์์ฑ๋๋ Canvas์ ์ฌ์ฉ๋๋ ์คํฌ๋ฆฝํธ,<br> UI_Scene.cs๋ ๊ธฐ๋ณธ์ ์ผ๋ก ๋ฐฐ์น๋์ด ์๋ Canvas์ ์ฌ์ฉ๋๋ ์คํฌ๋ฆฝํธ์ด๋ค.

```C#
// UI_Popup.cs
public class UI_Popup : UI_Base
{
    public virtual void Init()
    {
        Managers.UI.SetCanvas(gameObject, true);
    }
    
    public virtual void ClosePopupUI()
    {
        Managers.UI.ClosePopupUI(this);
    }
}

// UI_Scene.cs
public class UI_Scene : UI_Base
{
    public virtual void Init()
    {
        Managers.UI.SetCanvas(gameObject, false);
    }
}
```
<br>

## ๐ UI_Button.cs
UI์๋ํ๋ฅผ ์ฌ์ฉํ๋ Test ์คํฌ๋ฆฝํธ๋ก ๋ฒํผ์ ํด๋ฆญํ๋ฉด Score ๊ฐ์ด ์ฆ๊ฐํ๋ ์ฝ๋์ด๋ค. <br>
UI_Popup์ ์์ ๋ฐ๊ณ  enum์ผ๋ก ์์ ๊ฐ์ฒด๋ฅผ ์ ์ฅํ๋ค.

```C#
public class UI_Button : UI_Popup
{
    int score = 0;

    enum Buttons
    {
        PointButton,
    }

    enum Texts
    {
        PointText,
        ScoreText,
    }

    enum GameObjects
    {
        TestObject,
    }

    enum Images
    {
        ItemIcon,
    }

    private void Start() {
        Init();
    }

    // Start๋ฅผ override ํ๋ ๊ฒ ๋ณด๋ค Init๋ฅผ ์ฌ์ฉํ๋๊ฒ ์ข์.
    public override void Init()
    {
        base.Init();

        Bind<Button>(typeof(Buttons));
        Bind<Text>(typeof(Texts));
        Bind<GameObject>(typeof(GameObjects));
        Bind<Image>(typeof(Images));

        // 1๋ฒ์งธ ์ฌ์ฉ๋ฐฉ๋ฒ (Extension ์ฌ์ฉ)
        GetButton((int)Buttons.PointButton).gameObject.AddUIEvent(OnButtonClicked);

        // 2๋ฒ์งธ ์ฌ์ฉ๋ฐฉ๋ฒ
        GameObject go = GetImage((int)Images.ItemIcon).gameObject;
        AddUIEvent(go, ((PointerEventData data) => { go.transform.position = data.position; }), Define.UIEvent.Drag);
        // โ ์ฝ๋๋ก ๋์ฒด๋จ.
        // UI_EventHandler evt = go.GetComponent<UI_EventHandler>();
        // evt.OnDragHandler += ((PointerEventData data) => { evt.gameObject.transform.position = data.position; });
    }

    public void OnButtonClicked(PointerEventData data)
    {
        score++;

        GetText((int)Texts.ScoreText).text = $"์ ์ : {score}";
    }
}
```
<br>

`[Enum์ผ๋ก ์์ ๊ฐ์ฒด ์ด๋ฆ์ ์ ์ฅํ๋ ์ด์ ]` <br>

๊ธฐ์กด ๋ฐฉ๋ฒ์ผ๋ก๋ public์ด๋ [SerializeField]๋ฅผ ์ฌ์ฉํ์ฌ ์ค๋ธ์ ํธ๋ฅผ ๊ฐ์ ธ์์ ํด๋น ์ปดํฌ๋ํธ๋ฅผ ์ฌ์ฉํ๋ ๋ฐฉ๋ฒ์ธ๋ฐ UI ๊ฐ์ฒด๊ฐ ๋ง์์ง๊ณ  ๊ทธ ๊ฐ์ฒด๋ค์ ์ฌ์ฉํด์ผ ํ๋ค๋ฉด ์ง์ ๋ถํด์ง๊ณ  ๊ฐ๋์ฑ์ด ๋จ์ด์ง ์ ๋ฐ์ ์๋ค. ๊ทธ๋ ๊ธฐ ๋๋ฌธ์ ์ด๋ฆ์ผ๋ก ๊ด๋ฆฌํ๊ณ  UI ์๋ํ๋ฅผ ์ฌ์ฉํ์ฌ ์ค์๋ฅผ ์ค์ด๊ณ  ๊ฐ๋์ฑ์ ๋์ผ ์ ์๋ค.

<br>

`[UI ์๋ํ ์ฌ์ฉ๋ฒ]` <br>
1. ๋ถ๋ชจ ์คํฌ๋ฆฝํธ UI_Base์ ์๋ Bind()๋ฅผ ์ฌ์ฉํ์ฌ ํด๋น Enum์ ์ ์ฅํ๋ค.
2. GetButton, GetImage, ..์ ๊ฐ์ UI_Base์ ์๋ ๋ฉ์๋๋ฅผ ํตํด ์ฌ์ฉํ๊ณ  ์ถ์ ๊ฐ์ฒด์ ์ด๋ฆ์ ์์ฒญํ์ฌ ์ฌ์ฉํ  ์ ์๋ค.

<br>

## ๐ UIManager.cs
UI๋ฅผ ๋งค๋์ ๋ก ๊ด๋ฆฌํ์ฌ ๋ค๋ฅธ ํด๋์ค์์ ์ ๊ทผ์ด ์ฝ๊ณ  ํ๋ฆฌํน์ ์ ์ฅ๋์ด ์๋ Canvas๋ฅผ ์์ฑ ๋ฐ ์ ๊ฑฐํ  ์ ์๋ค.
```C#
public class UIManager
{
    int _order = 10;

    Stack<UI_Popup> _popupStack = new Stack<UI_Popup>();
    UI_Scene _sceneUI = null;

    // ํ๋ฆฌํน ์ค๋ธ์ ํธ ๋ถ๋ชจ (ํ์ด๋ผ์ด์ปค ๊น๋ํ๊ฒ ์ ๋ฆฌํ๋ ค๊ณ  ์ฌ์ฉ)
    public GameObject Root
    {
        get{
            GameObject root = GameObject.Find("@UI_Root");// ์ค๋ธ์ ํธ ์ฐพ๊ธฐ

            if (root == null)
                root = new GameObject{name = "@UI_Root"}; // ์ค๋ธ์ ํธ ์ด๋ฆ ์ค์ 

            return root;
        }
    }

    // Canvas์ ์ฐ์ ์์๋ฅผ ์ ํด์ฃผ๋ ๋ฉ์๋
    public void SetCanvas(GameObject go, bool sort = true)
    {
        Canvas canvas = Util.GetOrAddComponent<Canvas>(go);
        canvas.renderMode = RenderMode.ScreenSpaceOverlay;
        canvas.overrideSorting = true;

        if (sort){
            canvas.sortingOrder = _order;
            _order++;
        }
        else{
            canvas.sortingOrder = 0;
        }
    }

    public T ShowSceneUI<T>(string name = null) where T : UI_Scene
    {
        if (string.IsNullOrEmpty(name)){
            name = typeof(T).Name;
        }

        GameObject go = Managers.Resource.Instantiate($"UI/Scene/{name}");
        T sceneUI = Util.GetOrAddComponent<T>(go);
        _sceneUI = sceneUI;

        go.transform.SetParent(Root.transform);

        return sceneUI;
    }

    // UI์ ๋ง๋ค์ด์ง ํ๋ฆฌํน์ stack์ ๋ฃ์ด order์ ๊ด๋ฆฌ
    public T ShowPopupUI<T>(string name = null) where T : UI_Popup
    {
        if (string.IsNullOrEmpty(name)){
            name = typeof(T).Name;
        }

        GameObject go = Managers.Resource.Instantiate($"UI/Popup/{name}");
        T popup = Util.GetOrAddComponent<T>(go);
        _popupStack.Push(popup);

        go.transform.SetParent(Root.transform);

        return popup;
    }

    // ์คํ์ ๋ง์ง๋ง ์์น์ popup์ด ์๋ ํ์ธ ํ ์ญ์ 
    public void ClosePopupUI(UI_Popup popup)
    {
        if (_popupStack.Count == 0)
            return;
        
        if (_popupStack.Peek() != popup){
            Debug.Log("Close Popup Failed!");
            return;
        }

        ClosePopupUI();
    }

    // Stack pop ์งํ
    public void ClosePopupUI()
    {
        if (_popupStack.Count == 0)
            return;
        
        UI_Popup popup = _popupStack.Pop();
        Managers.Resource.Destroy(popup.gameObject);
        popup = null;

        _order--;
    }

    // Stack ์์ ์๋ ์ ์ฒด pop
    public void CloseAllPopupUI()
    {
        while (_popupStack.Count > 0){
            ClosePopupUI();
        }
    }
}
```
<br>

`SetCanvas() ๋ฉ์๋` <br>
- UI๊ฐ ๊ฒ์ ๋์ค ์์ฑ๋  ๋ ์ฐ์ ์์๋ฅผ ์ค์ ํ  ์ ์๋ค.

<br>

`ShowPopupUI() ๋ฉ์๋` <br>
- ํ๋ฆฌํน์ ์ด๋ฆ์ ๋ฐ๊ณ  ์์ฑ ํ Stack์ผ๋ก ์ ์ฅํ๋ค.

<br>

`ClosePopupUI() ๋ฉ์๋` <br>
- Stack์ ์๋ ์ค๋ธ์ ํธ๋ฅผ Popํ๋ค.

<br>

## ๐ก ์ฐธ๊ณ  ์ฌ์ดํธ
- [[Rookiss ์ธํ๋ฐ ๊ฐ์]](https://www.inflearn.com/course/mmorpg-%EC%9C%A0%EB%8B%88%ED%8B%B0)
- [[๊ณต๋ถํ๋ ์๋นต๋ง]](https://ansohxxn.github.io/unity%20lesson%202/ch7-4/)