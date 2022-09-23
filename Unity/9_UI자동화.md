# 📺 UI 자동화

Inflearn에 있는 Rookiss님의 [C#과 유니티로 만드는 MMORPG 게임 개발 시리즈] Part3 유니티 강의를 듣고 복습한 내용입니다.

루키스님의 강의를 보고 학습하면서 UI 자동화가 중요한거 같아 이해도를 높이기 위해 정리하였습니다.

<br>

## ⌨ 스크립트 구조
---
`[UI 기본 구조]`
- 📝 UI_Base : 모든 UI의 조상 스크립트로 UI 관련 스크립트는 모두 이 스크립트를 상속받는다.

  - 📝 UI_Scene : Popup처럼 게임 도중 생성되는 것이 아닌 씬에 미리 자리잡고 있는 UI의 부모 스크립트다.

  - 📝 UI_Popup : 게임 도중에 생성되는 UI의 부모 스크립트다.

    - 📝 UI_Button : UI_Popup의 자식 스크립트로 
    
    <br>

`[기능성 관련 스크립트]`
- 📝 UI_EventHandler : UI 이벤트를 사용할 경우 사용하게될 스크립트.
- 📝 Util : 기능성 메소드를 가지고 있는 스크립트. (자식 객체, 컴포넌트 찾기 등)
- 📝 Define : 관련 키워드를 저장하고 있는 스크립트.

<br>

`[매니저]`
- 📝 UIManager : 다른 스크립트에서 UI를 사용하기 위한 매니저

<br>
<br>

## 📝 Define.cs
상태 변수를 저장하는 클래스다.
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
        Press,      // 꾹 누를 때 상태
        Click,      // 클릭 상태 (Press 상태가 끝난 상태)
    }

    public enum CameraMode
    {
        QuarterView,    // 디아블로 게임 같은 시점
    }
}
```
<br>

## 📝 UI_EventHandler.cs
객체가 EventSystems을 사용할 필요가 있을 때 Action으로 저장하여 사용하는 클래스다. (옵저버 패턴 형태?)
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

## 📝 Util.cs
기능성 메소드를 가지고 있는 클래스다. 
```C#
public class Util
{
    // 컴포넌트 찾은 후 추가하기
    public static T GetOrAddComponent<T>(GameObject go) where T : UnityEngine.Component
    {
        T component = go.GetComponent<T>();

        if (component == null)
            component = go.AddComponent<T>();

        return component;
    }

    // GameObjec는 컴포넌트가 아니므로 Transform을 통해 찾음
    public static GameObject FindChild(GameObject go, string name = null, bool recursive = false)
    {
        Transform transform = FindChild<Transform>(go, name, recursive);
        if (transform == null)
            return null;
        
        return transform.gameObject;
    }

    // 자식 객체 컴포넌트 찾기
    public static T FindChild<T>(GameObject go, string name = null, bool recursive = false) where T : UnityEngine.Object
    {
        if (go == null)
            return null;

        // recursive : 자기 자신의 자식 객체를 가져올지 판단
        if (recursive == false){
            // go의 자식객체 수 만큼
            for(int i=0; i<go.transform.childCount; i++){
                // 지정된 자식객체를 transform에 반환
                Transform transform = go.transform.GetChild(i);

                // string.IsNullOrEmpty = 빈문자열이면 true (null 또는 "")
                if (string.IsNullOrEmpty(name) || transform.name == name){
                    // 해당 T(Button, Text, ...) 컴포넌트 반환
                    T component = transform.GetComponent<T>();
                    if (component != null)
                        return component;
                }
            }
        }
        else{
            // true일 경우 자식의 자식까지 다 가져온다.
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

`GetOrAddComponent() 메소드` <br>
1. 오브젝트에 컴포넌트를 읽을 수 있고 없을 시 추가할 수 있다.

<br>

`FindChild<T>() 메소드` <br>
1. Canvas 객체안에 있는 자식객체의 컴포넌트를 찾는 메소드이다.
2. GetChild()를 통해 자식객체를 찾을 수 있다.
3. 찾은 객체의 T 컴포넌트를 return 한다.

<br>

`FindChild() 메소드` <br>
1. 찾을 객체가 Canvas가 아닌 GameObject일 경우 기본적으로 제공되는 Transform 컴포넌트를 통해 찾을 수 있다.

<br>

## 📝 UI_Base.cs
모든 UI 스크립트의 조상 클래스로 부모객체안에 있는 객체들의 이름을 딕셔너리에 저장하고 코드로 불러와서 사용할 수 있다.

```C#
public class UI_Base : MonoBehaviour
{
    // 컴포넌트 타입 별로 담아줄 딕셔너리(자료구조)
    protected Dictionary<Type, UnityEngine.Object[]> _objects = new Dictionary<Type, UnityEngine.Object[]>();

    // where T : 부모 클래스의 자식 클래스만 가능
    protected void Bind<T>(Type type) where T : UnityEngine.Object
    {
        // C++과 다르게 C#은 enum안에 있는 내용을 읽을 수 있다!
        string[] names = Enum.GetNames(type);   // C# 기능
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

    // 사용 메소드
    protected T Get<T>(int idx) where T : UnityEngine.Object
    {
        UnityEngine.Object[] objects = null;
        if (_objects.TryGetValue(typeof(T), out objects) == false)
            return null;

        return objects[idx] as T;
    }

    // 자주 사용하는 컴포넌트는 사용하기 좋게 메소드 생성
    protected Text GetText(int idx) { return Get<Text>(idx); }
    protected Button GetButton(int idx) { return Get<Button>(idx); }
    protected Image GetImage(int idx) { return Get<Image>(idx); }

    // Event 핸들러에 관한 메소드
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

`_objects<Type, UnityEngine.Object[]>` : Key에는 컴포넌트 Type을 Value에는 오브젝트를 담는 딕셔너리다.

- `Bind() 메소드` <br>
   1. 해당 객체가 가지고 있는 자식객체를 _objects에 저장할 수 있다.
   2. C++같은 경우 Enum을 읽을 수 없는데 C#은 읽을 수 있다.
   3. 읽어드린 Enum 안의 개수 만큼 오브젝트를 생성 후 기능성 스크립트인 Util.FindChild을 통해 자식 객체를 저장한다.

<br>

- `Get() 메소드` <br> 
   1. Bind()를 통해 저장한 오브젝트를 사용할 수 있다.
   2. _objects.TryGetValue(Type, 받을 변수)를 통해 _objects에 저장되어 있는 Key값의 Value를 빼올 수 있다.

<br>

- `AddUIEvent() 메소드` <br>
   1. Util안에 있는 GetOrAddComponent()를 사용하여 객체에 필요한 컴포넌트를 추가 및 읽어올 수 있다. ( evt에 저장 )
   2. Util을 통해 해당 객체에 EventSystem을 추가하면 마우스 이벤트를 사용할 수 있다. 

<br>

## 📝 UI_Popup.cs와 UI_Scene.cs
해당 스크립트는 UI_Base를 기본적으로 상속 받는다. <br>
UI_Popup.cs는 게임 진행 중 생성되는 Canvas에 사용되는 스크립트,<br> UI_Scene.cs는 기본적으로 배치되어 있는 Canvas에 사용되는 스크립트이다.

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

## 📝 UI_Button.cs
UI자동화를 사용하는 Test 스크립트로 버튼을 클릭하면 Score 값이 증가하는 코드이다. <br>
UI_Popup을 상속 받고 enum으로 자식 객체를 저장한다.

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

    // Start를 override 하는 것 보다 Init를 사용하는게 좋음.
    public override void Init()
    {
        base.Init();

        Bind<Button>(typeof(Buttons));
        Bind<Text>(typeof(Texts));
        Bind<GameObject>(typeof(GameObjects));
        Bind<Image>(typeof(Images));

        // 1번째 사용방법 (Extension 사용)
        GetButton((int)Buttons.PointButton).gameObject.AddUIEvent(OnButtonClicked);

        // 2번째 사용방법
        GameObject go = GetImage((int)Images.ItemIcon).gameObject;
        AddUIEvent(go, ((PointerEventData data) => { go.transform.position = data.position; }), Define.UIEvent.Drag);
        // ↑ 코드로 대체됨.
        // UI_EventHandler evt = go.GetComponent<UI_EventHandler>();
        // evt.OnDragHandler += ((PointerEventData data) => { evt.gameObject.transform.position = data.position; });
    }

    public void OnButtonClicked(PointerEventData data)
    {
        score++;

        GetText((int)Texts.ScoreText).text = $"점수 : {score}";
    }
}
```
<br>

`[Enum으로 자식 객체 이름을 저장하는 이유]` <br>

기존 방법으로는 public이나 [SerializeField]를 사용하여 오브젝트를 가져와서 해당 컴포넌트를 사용하는 방법인데 UI 객체가 많아지고 그 객체들을 사용해야 한다면 지저분해지고 가독성이 떨어질 수 밖에 없다. 그렇기 때문에 이름으로 관리하고 UI 자동화를 사용하여 실수를 줄이고 가독성을 높일 수 있다.

<br>

`[UI 자동화 사용법]` <br>
1. 부모 스크립트 UI_Base에 있는 Bind()를 사용하여 해당 Enum을 저장한다.
2. GetButton, GetImage, ..와 같은 UI_Base에 있는 메소드를 통해 사용하고 싶은 객체의 이름을 요청하여 사용할 수 있다.

<br>

## 📝 UIManager.cs
UI를 매니저로 관리하여 다른 클래스에서 접근이 쉽고 프리팹에 저장되어 있는 Canvas를 생성 및 제거할 수 있다.
```C#
public class UIManager
{
    int _order = 10;

    Stack<UI_Popup> _popupStack = new Stack<UI_Popup>();
    UI_Scene _sceneUI = null;

    // 프리팹 오브젝트 부모 (하이라이커 깔끔하게 정리하려고 사용)
    public GameObject Root
    {
        get{
            GameObject root = GameObject.Find("@UI_Root");// 오브젝트 찾기

            if (root == null)
                root = new GameObject{name = "@UI_Root"}; // 오브젝트 이름 설정

            return root;
        }
    }

    // Canvas의 우선순위를 정해주는 메소드
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

    // UI에 만들어질 프리팹을 stack에 넣어 order을 관리
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

    // 스택의 마지막 위치에 popup이 있나 확인 후 삭제
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

    // Stack pop 진행
    public void ClosePopupUI()
    {
        if (_popupStack.Count == 0)
            return;
        
        UI_Popup popup = _popupStack.Pop();
        Managers.Resource.Destroy(popup.gameObject);
        popup = null;

        _order--;
    }

    // Stack 안에 있는 전체 pop
    public void CloseAllPopupUI()
    {
        while (_popupStack.Count > 0){
            ClosePopupUI();
        }
    }
}
```
<br>

`SetCanvas() 메소드` <br>
- UI가 게임 도중 생성될 때 우선순위를 설정할 수 있다.

<br>

`ShowPopupUI() 메소드` <br>
- 프리팹의 이름을 받고 생성 후 Stack으로 저장한다.

<br>

`ClosePopupUI() 메소드` <br>
- Stack에 있는 오브젝트를 Pop한다.

<br>

## 💡 참고 사이트
- [[Rookiss 인프런 강의]](https://www.inflearn.com/course/mmorpg-%EC%9C%A0%EB%8B%88%ED%8B%B0)
- [[공부하는 식빵맘]](https://ansohxxn.github.io/unity%20lesson%202/ch7-4/)