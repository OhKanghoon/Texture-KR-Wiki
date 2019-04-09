# ASViewController

`ASViewController` 는 ASDisplayNode 계을 관리하기 위한 몇가지 기능들을 추가한 `UIViewController` 의 서브  클래스이다.

`ASViewController` 는 `UINavigationController`, `UITabBarController` , `UISplitViewController` 를 포함한 모든 UIViewController 대신 사용할 수 있다.

#### ASViewController 사용의 이점

1. **Save Memory** - 화면이 사라진 ASViewController 는 자동으로 가져오는 데이터의 사이즈와 자신의 children 의 display range 를 줄일 것이다. 이것이 대형 어플리케이션에서 메모리 관리를 위한 키 포인트이다.
2. [**ASVisibility**](http://texturegroup.org/docs/asvisibility.html) **기능** - `ASNavigationController` 또는 `ASTabBarController` 에서 사용할 경우, 자식 뷰컨트롤러들\(ASViewController\)은 뷰 컨트롤러를 표시하기 위해 필요한 정확한 사용자 탭 숫자를 알 있다.

## 사용

UIViewController 는 자신의 뷰를 제공한다. ASViewController 에는 지정된 initizer `init(node:)` 에서 관리할 노드가 할당된다.

TableNode 를 관리 노드로 사용하는 예제 [ASDKgram 프로젝트](https://github.com/TextureGroup/Texture/blob/master/examples_extra/ASDKgram-Swift)의 ASViewController 서브 클래스 PhotoFeedNodeController 를 참고하세요.

이 테이블 노드는 `init(node:)` 로 ASViewController 에 할당된다.

```swift
init() {

  super.init(node: ASTableNode())

  navigationItem.title = "ASDK"
}
```

