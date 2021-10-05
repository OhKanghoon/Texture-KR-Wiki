# ASTextNode

ASTextNode는 Texture의 기본 텍스트 노드로, 일반적으로 UILabel을 사용할 때 언제든지 사용할 수 있습니다.  
또 모든 Rich Text를 지원하고, ASControlNode의 서브 클래스로 titleLabel 만 사용해 UIButton을 만들 때 언제든지 사용할 수 있습니다.

## 기본 사용법

UILabel을 사용했던 사람들에게 ASTextNode의 인터페이스는 익숙하게 느껴질 수 있습니다.  
다만, UILabel과의 첫 번째 차이점은 ASTextNode는 일반 String 대신 Attributed String만을 사용한다는 것입니다.

```swift
let attrs = [NSFontAttributeName: UIFont(name: "HelveticaNeue", size: 12.0)]
let string = NSAttributedString(string: "Hey, here's some text.", attributes: attrs)

node = ASTextNode()
node.attributedText = string
```

위 예시처럼, 기본 Text Node를 만들려면 표준 생성자를 사용한 뒤 표시할 Attributed String 을 설정하기만 하면 됩니다.

## Truncation

TextNode가 모든 텍스트를 표시할 수 없다면, 텍스트는 가능한 한 많이 표시되고 잘려진 부분은 Truncation String으로 대체됩니다.

```swift
textNode = ASTextNode()
textNode.attributedText = string
textNode.truncationAttributedText = NSAttributedString(string: "¶¶¶")
```

결과는 아래와 같습니다.

![Truncation Example](../.gitbook/assets/image.png)

Truncation String은 디폴트로 '...' 이 되므로, 이 설정으로 충분하다면 따로 설정하지 않아도 됩니다.

## Link Attributes

텍스트를 링크로 지정하고 싶다면, 먼저 TextNode에 `string` 의 배열인 `linkAttributes` 배열을 설정해야 합니다. 이 배열은 `attributed string` 에서 링크들의 key로 사용됩니다.

이렇게하면  `string` 에 `attribute` 를 설정할 때, 이 key들이 알맞은 NSURL을 가리키도록 할 수 있습니다.

```swift
textNode.linkAttributeNames = [kLinkAttributeName]

let blurb: NSString = "kittens courtesy placekitten.com 😸"
let attributedString = NSMutableAttributedString(string: blurb as String)

attributedString.addAttribute(.font, value: UIFont(name: "HelveticaNeue-Light", size: 16.0)!, range: NSRange(location: 0, length: blurb.length))

attributedString.addAttributes([NSAttributedString.Key(kLinkAttributeName): NSURL(string: "http://placekitten.com/")!,
                                .foregroundColor: UIColor.gray,
                                .underlineStyle: (NSUnderlineStyle.single.rawValue | NSUnderlineStyle.patternDashDot.rawValue)],
                               range: blurb.range(of: "placekitten.com"))
textNode.attributedText = attributedString
textNode.isUserInteractionEnabled = true
```

위의 코드를 통해 아래 그림과 같이 전체 문장 중 "placekitten.com" 부분에 점선 스타일의 밑줄이 그어진 회색 링크가 적용됩니다.

![Link Attributes Example](../.gitbook/assets/kittenlink.png)

여기서 볼 수 있듯이, attributed string의 range에 따라서 여러가지 스타일을 각각의 링크에 적용하기에 편리합니다.

## ASTextNodeDelegate

ASTextNodeDelegate를 채택하면 해당 Class가 TextNode와 관련된 다양한 이벤트에 반응할 수 있습니다.
예를 들면, 아래처럼 구현하면 link를 tap하는 이벤트에 반응할 수 있습니다.

```swift
func textNode(_ textNode: ASTextNode, tappedLinkAttribute attribute: String, value: Any, at point: CGPoint, textRange: NSRange) {
    guard let url = value as? URL else { return }

    // Tap 됐을 때 URL Handling
    UIApplication.shared.openURL(url)
}
```

또한 LongPress와 Highlighting에 대해 다음과 같이 반응할 수 있습니다.

* textNode\(\_ , shouldHighlightLinkAttribute: value: point:\)
* textNode\(\_ , shouldLongPressLinkAttribute: value: point:\)
* textNode\(\_ , longPressedLinkAttribute: value: point: textRange:\)

## 잘못된 줄 간격과 최대 라인 수

`NSParagraphStyle` 의 `lineSpacing` 을 사용하면 최대 라인 수를 가진 멀티라인 텍스트에서 문제가 발생할 수 있습니다.

예를 들면, 아래 코드의 경우 문제가 발생합니다.

```swift
let someLongString = "..."

let paragraphStyle = NSMutableParagraphStyle()
paragraphStyle.lineSpacing = 10.0

let font = UIFont(name: "SomeFontName", size: 15.0)

let attributes = [
    NSAttributedString.Key.font: font,
    NSAttributedString.Key.paragraphStyle: paragraphStyle
]

let textNode = ASTextNode()
textNode.maximumNumberOfLines = 4
textNode.attributedText = NSAttributedString(string: someLongString, attributes: attributes)
```

`ASTextNode` 는 내부적으로 TextKit 을 사용하여 지정된 최대 라인 수를 산출하는 데 필요한 shrink를 계산합니다.

하지만 위의 예시에서는 텍스트가 너무 많이 줄어들게 됩니다. 4줄의 텍스트 대신에, 3줄의 텍스트와 하단의 이상한 간격이 나타날 것입니다. 이 문제를 해결하려면 TextNode 에서 `truncationMode` 를 `.byTruncatingTail` 로 설정해야 합니다.

```swift
// ...
let textNode = ASTextNode()
textNode.maximumNumberOfLines = 4
textNode.truncationMode = .byTruncatingTail
textNode.attributedText = NSAttributedString(string: someLongString, attributes: attributes)
//...
```

