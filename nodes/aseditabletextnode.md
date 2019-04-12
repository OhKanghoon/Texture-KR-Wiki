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

#### Basic initialization

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







