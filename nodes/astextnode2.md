# ASTextNode2

ASTextNode는 ASTextKit을 사용하지만 ASTextNode2는 [YYText](https://github.com/ibireme/YYText)의 Text Rendering Engine을 기반으로한 Text Node입니다. 

ASTextNode와 비교했을 때 **truncation 처리하는데 상대적으로 특화**가 잘 되어있지만, **ASTextNode**에서 제공해주는 여러 편의용 **기능중 일부는 제공되고 있지 않습니다.** 

사용법은 ASTextNode와 큰 차이점은 없습니다. 하지만 truncation의 이득이 필요할 때만 사용해야합니다. _ASTextNode처럼 사용하면 AS\_TEXT\_ALERT\_UNIMPLEMENTED\_FEATURE Crash를 경험하실 수가 있습니다._

## 사용법

#### 1. attributedText

NSAttributedString을 받습니다. 

```swift
let textNode = ASTextNode2()
textNode.attributedText = NSAttributedString(string: "test", attributes: [:])
```

#### 2. truncationAttributedText

Truncate될 때 attributedText뒤에 붙는 attributedText입니다.

```swift
let textNode = ASTextNode2()
textNode.truncationAttributedText = NSAttributedString(string: "More see", attributes: [:])
```

#### 3. additionalTruncationMessage

Truncate될 때 truncationAttributedText다음에 붙는 truncationAttributedText입니다. 쉽게 말해서 두번째 truncationAttributedText라고 이해하시면 됩니다.

```swift
let textNode = ASTextNode2()
textNode.truncationAttributedText = NSAttributedString(string: "More see", attributes: [:])
textNode.additionalTruncationMesssage = NSAttributedString(string: "About", attributes: [:])
```

#### 4. maximumNumberOfLines

UInt 값을 받으며 최대라인수를 지정합니다. Default는 0 입니다. 

```swift
let textNode = ASTextNode2()
textNode.maximumNumberOfLines = 1
```

#### 5. truncationMode

NSLineBreakMode를 받으며, 기본값은 NSLineBreakByWordWrapping입니다.

```swift
let textNode = ASTextNode2()
textNode.truncationMode = .byTruncatingTail
```

#### 6. isTruncated \(get-only\) 

truncated 된 상태를 Boolean값으로 받습니다. 하지만 **AS\_TEXT\_ALERT\_UNIMPLEMENTED\_FEATURE** 상태여서 사용시 크래시 납니다. 

```swift
let textNode = ASTextNode2()
let isTruncated = textNode.isTruncated // !Crash AS_TEXT_ALERT_UNIMPLEMENTED_FEATURE
```

