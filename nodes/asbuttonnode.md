# ASButtonNode

## 기본 사용법

`ASButtonNode` 는 `UIButton` 이 `UIControl` 의 서브 클래스인 것 처럼 `ASControlNode` 의 서브 클래스이다.  
대조적으로, 모든 버튼의 서브노드들을 Layer back 할 수 있는건 `UIButton` 에 비해  Main thread Impact 를 상당히 가볍게 할 수 있다.

## 상태 제어

기존에 `setTitle(_:for:)` 를 사용해 봤다면 ASButtonNode 를 어떻게 세팅할지 이미 알고 있는 것이다.  
`ASButtonNode` 는 속성을 쉽게 세팅하기 위해서 몇가지 매개변수를 추가했다.

```swift
buttonNode.setTitle("Button Title Normal", with: nil, with: .blue, for: .normal)
```

더 많은 제어가 필요하다면 AttributeString 을 직접 사용하도록 선택할 수 있다.

```swift
buttonNode.setAttributedTitle(attributedTitle, for: .normal)
```

## Target-Action Pairs

UIKit 과 마찬가지로, 다양한 이벤트에 반응하기 위해 target-action pairs 를 추가할 수 있다.

```swift
buttonNode.addTarget(self, action: #selector(buttonPressed), forControlEvents: .touchUpInside)
```

## 컨텐츠 정렬

`ASButtonNode` 는 `contentVerticalAlignment` 와 `contentHorizontalAlignment` 프로퍼티 모두 제공한다. 이렇게 하면 버튼에 사용중 titleLabel 과 이미지의 정렬을 쉽게 설정할 수 있다.

```swift
buttonNode.contentVerticalAlignment = .top
buttonNode.contentHorizontalAlignment = .middle
```

> 현재 LayoutSpecThatFits\(:\) 를 사용하지 않으면 이 프로퍼티 작동하지 않는다.

## tintColor 설정

ASButtonNode 에서 tintColor 값을 변경해도 이미지의 컬러가 변하지 않습니다. 아래의 코드를 사용해서 이미지의 컬러를 변경해주세요.

```swift
buttonNode.imageNode.imageModificationBlock = ASImageNodeTintColorModificationBlock(.black)
```

