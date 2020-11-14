---
description: '[주의] 3.0 이상부터 더 이상 제공되지 않습니다.'
---

# ASViewController \(DEPRECATED\)

`ASViewController` 는 ASDisplayNode 계열을 관리하기 위해, 몇가지 기능들을 추가한 `UIViewController` 의 서브 클래스입니다.

`ASViewController` 는 `UINavigationController`, `UITabBarController` , `UISplitViewController` 를 포함한 모든 UIViewController 대신 사용할 수 있습니다.

### ASViewController 사용의 이점

1. **Save Memory** - 화면이 사라진 ASViewController 는 자동으로 가져오는 데이터의 사이즈와 자신의 children 의 display range 를 줄여줍니다. 이것은 대형 어플리케이션에서 메모리 관리를 위한 키 포인트입니다.
2. [**ASVisibility**](http://texturegroup.org/docs/asvisibility.html) **기능** - `ASNavigationController` 또는 `ASTabBarController` 에서 사용할 경우, 자식 뷰 컨트롤러 들\(ASViewController\)은 뷰 컨트롤러를 표시하기 위해 필요한 정확한 사용자 탭 숫자를 알고 있습니다.

## 사용

UIViewController 는 자신의 뷰를 제공합니다. ASViewController 에는 지정된 initizer `init(node:)` 에서 관리할 노드가 할당됩니다.

ASTableNode 를 관리 노드로 사용하는 예제 [ASDKgram 프로젝트](https://github.com/TextureGroup/Texture/blob/master/examples_extra/ASDKgram-Swift)의 ASViewController 서브 클래스 PhotoFeedNodeController 를 참고하세요.

이 테이블 노드는 `init(node:)` 로 ASViewController 에 할당됩니다.

```swift
init() {

  super.init(node: ASTableNode())

  navigationItem.title = "ASDK"
}
```

