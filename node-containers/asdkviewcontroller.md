---
description: 3.0 이상부터 제공됩니다.
---

# ASDKViewController

`ASDKViewController` 는 ASDisplayNode 계을 관리하기 위한 몇가지 기능들을 추가한 `UIViewController` 의 서브 클래스입니다.

`ASDKViewController`는 Texture 3.0부터 생겼습니다. 정확히 말하자면 Xcode 12에서의 `AuthenticationServices`와의 [충돌을 피하기 위해](https://github.com/TextureGroup/Texture/pull/1876) `ASViewController`가 `AS_EXTERN`와 함께 이름만 변경되었습니다.

`ASDKViewController` 는 `UINavigationController`, `UITabBarController` , `UISplitViewController` 를 포함한 모든 UIViewController 대신 사용할 수 있습니다.

### ASDKViewController 사용의 이점

1. **Save Memory** - 화면이 사라진 ASDKViewController 는 자동으로 가져오는 데이터의 사이즈와 자신의 children 의 display range 를 줄여줍니다. 이것은 대형 어플리케이션에서 메모리 관리를 위한 키 포인트입니다.
2. [**ASVisibility**](http://texturegroup.org/docs/asvisibility.html) **기능** - `ASNavigationController` 또는 `ASTabBarController` 에서 사용할 경우, 자식 뷰 컨트롤러 들\(ASViewController\)은 뷰 컨트롤러를 표시하기 위해 필요한 정확한 사용자 탭 숫자를 알 수 있습니다.

## 사용

UIViewController 는 뷰를 제공합니다. 유사하게 ASDKViewController 에는 지정된 initizer `init(node:)` 에서 관리할 노드가 할당됩니다.

TableNode 를 관리 노드로 사용하는 예제 [ASDKgram 프로젝트](https://github.com/TextureGroup/Texture/blob/master/examples_extra/ASDKgram-Swift)의 ASDKViewController 서브 클래스 PhotoFeedNodeController 를 참고하세요.

이 테이블 노드는 `init(node:)` 로 ASDKViewController 에 할당됩니다.

```swift
final class ExampleViewController: ASDKViewController<ASTableNode> { 

  override init() {
    super.init(node: ASTableNode())
    self.navigationItem.title = "ASDK"
  }
  
}
```

## 

