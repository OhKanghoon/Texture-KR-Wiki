# ASEditableTextNode

`ASEditableTextNode`는 UIKit에서 제공해주는 `UITextView`와 `UITextField`를 사용하듯이 사용할 수 있는 Node입니다. 

`UITextView`가 **wrapping**되어 있으며 `ASTextKitComponents` 를 통해서 glyph에 따른 노드의 **사이즈를 렌더링**합니다. 

```swift
let node = ASEditableTextNode()
let textView: UITextView = node.textView
let view: UIView = node.view
// view와 textView는 동일하지 않습니다. 
```



## 사용법 

### Basic initialization

기본적으로 아래와 같이 기본 attributedText와 textContainerInset를 지정하여 사용할 수 있습니다. 

```swift
let node = ASEditableTextNode()

node.attributedText = NSAttributedString(string: "안녕하세요.")
node.attributedPlaceholderText = NSAttributedString(string: "입력해주세요.")
node.textContainerInset = UIEdgeInsets(top: 8, left: 8, bottom: 8, right: 8)
```

가장 기본적인 사용방법이며 내부적으로는 ASTextKitComponents를 이용하여 초기화합니다.

```swift
let textKitComponents = ASTextKitComponents(attributedSeedString: nil,
                                            textContainerSize: CGSize.zero)
let placeholderComponents = ASTextKitComponents(attributedSeedString: nil, 
                                                textContainerSize: CGSize.zero)                                            
let node = ASEditableTextNode.init(textKitComponents: textKitComponents,
                                   placeholderTextKitComponents: placeholderComponent)
```

#### Advanced initialization

필요에 따라서 custom NSTextLayoutManager 및 NSTextStorage를 이용하여 초기화해야하는 경우도 있는데 이러한 경우는 아래의 코드와 같이 ASTextKitComponents를 사용해서 Initialization하는 방법이 있습니다. 

```swift
let textStorage = NSTextStorage.init()
let manager = NSTextLayoutManager.init()
        
let textKitComponents: ASTextKitComponents =
            .init(textStorage: textStorage,
                  textContainerSize: .zero,
                  layoutManager: manager)
        
let placeholderTextKit: ASTextKitComponents =
            .init(attributedSeedString: NSAttributedString(string: "insert:"),
                  textContainerSize: .zero)
        
let node = ASEditableTextNode.init(textKitComponents: textKitComponents,
                                   placeholderTextKitComponents: placeholderTextKit)
```

#### Typing Attribute

타이핑하는데 있어서 어트리뷰트를 지정하기 위해선 `typingAttributes`에 원하는 값을 지정하면 됩니다.

```swift
editableTextNode.typingAttributes = [NSForegroundColorAttributeName: UIColor.blue, 
                                      NSBackgroundColorAttributeName: UIColor.red]
```

### ASEditableTextNode Delegate 

`UITextViewDelegate`와 마찬가지로 `ASEditableTextNode`도 유사한 Delegate를 제공해줍니다. 

```swift
let node = ASEditableTextNode()
node.delegate = self
```



#### shouldEditing

editing가능 여부에 대한 제어 메서드로 제어 가능 여부를 Boolean값으로 반환합니다.

```swift
func editableTextNodeShouldBeginEditing(_ editableTextNode: ASEditableTextNode) -> Bool
```

#### didBeginEditing

editing 시작이후에 대한 이벤트를 받습니다.

```swift
func editableTextNodeDidBeginEditing(_ editableTextNode: ASEditableTextNode)
```

#### didFinishEditing

editing이 끝난 이후에 대한 이벤트를 받습니다. 

```swift
func editableTextNodeDidFinishEditing(_ editableTextNode: ASEditableTextNode)
```

#### shouldChangeText

Text변화에 대한 이벤트를 받습니다. 

_추가 또는 제거되는 text\(replacementText\)와 영역\(shouldChangeTextIn range\)를 받습니다._

```swift
func editableTextNode(_ editableTextNode: ASEditableTextNode, shouldChangeTextIn range: NSRange, replacementText text: String) -> Bool
```

#### didChangeSelection

TextView상 커서의 위치 및 selection의 변화에 대한 이벤트를 받습니다. 

```swift
func editableTextNodeDidChangeSelection(_ editableTextNode: ASEditableTextNode, fromSelectedRange: NSRange, toSelectedRange: NSRange, dueToEditing: Bool)
```

#### didUpdateText

shouldChangeText이후 업데이트 결과가 끝났을 때에 대한 이벤트를 받습니다.

```swift
func editableTextNodeDidUpdateText(_ editableTextNode: ASEditableTextNode)
```

ASEditableTextNode의 변화에 따라 상위 노드의 사이즈에 변화를 주기 위해선 `setNeedsLayout`을 호출하면됩니다. 

```swift
func editableTextNodeDidUpdateText(_ editableTextNode: ASEditaleTextNode) {
    editableTextNode.supernode?.setNeedsLayout()
}
```

### 이외 유용한 프로퍼티와 메서드들 

UITextView에서 제공해주는 기본 property와 동일하게 사용합니다. 

| Name | Description |
| :--- | :--- |
| autocapitalizationType | 자동 대문자 처리에 대한 옵션을 지정할 수 있습니다. |
| autocorrectionType | 자동 교정 작동에 대한 옵션을 지정할 수 있습니다. |
| spellCheckingType | 문자 또는 문장의 스펠링 처리에 대해서 교정해주는 옵션을 지정할 수 있습니다.  |
| keyboardType | 키보드 타입에 대한 옵션입니다. \(숫자 패널, 이메일 etc\) |
| returnKeyType | 키보드상 Return키에 대한 옵션입니다. |
| keyboardAppearance | 키보드 색상에 대한 옵션입니다. |
| enablesReturnKeyAutomatically | 기본값은 False이며, 자동 Return처리에 대한 플래그입니다.  |
| secureTextEntry | 기본값은 False이며, 보안상 필요한 Text처리에 대한 플래그입니다.  |

다음 내역은 ASEditableTextNode에서만 제공해주는 편의용 기능들 입니다.

| Name | Description |
| :--- | :--- |
| maximumLinesToDisplay | 최대 보여질 수 있는 라인수를 반환합니다. |
| isDisplayingPlaceholder | placehodler가 보여지는 상태인지에 대한 Bool값을 반환하는 method입니다.  |

