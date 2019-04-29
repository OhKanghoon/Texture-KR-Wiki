# ASTextNode

ASTextNode 는 Texture 의 기본 텍스트 노드로, 일반적으로 UILabel 을 사용할 때 언제든지 사용할 수 있다.  
또 모든 Rich Text 를 지원하고, ASControlNode 의 서브 클래스로 titleLabel 만 사용해 UIButton을 만들 때 언제든지 사용할 수 있다.

## 기본 사용법

ASTextNode 의 인터페이스는 UILabel 을 사용했던 사람들 모두에게 익숙해야 한다.  
다만, UILabel 과의 첫 번째 차이점은 ASTextNode 는 일반 String 대신 Attributed String 만을 사용한다는 것이다.

```swift
let attrs = [NSFontAttributeName: UIFont(name: "HelveticaNeue", size: 12.0)]
let string = NSAttributedString(string: "Hey, here's some text.", attributes: attrs)

node = ASTextNode()
node.attributedText = string
```

보다시피, 기본 Text Node 를 만드려면 표준 생성자를 사용한 뒤 표시할 Attributed String 을 설정하기만 하면 된다.

## Truncation

TextNode 가 모든 텍스트를 표시할 수 없다면, 텍스트는 가능한 한 많이 표시되고 잘려진 부분은 Truncation String 으로 대체된다.

```swift
textNode = ASTextNode()
textNode.attributedText = string
textNode.truncationAttributedText = NSAttributedString(string: "¶¶¶")
```

결과는 아래와 같다.

![Truncation Example](../.gitbook/assets/image.png)

기본적으로 Truncation String 은 '...' 이 되므로, 이 것만 필요하다면 따로 설정하지 않아도 된다.

## Link Attributes

텍스트를 링크로 지정하고 싶다면, 먼저 TextNode 에 `string` 의 배열인 `linkAttributes` 배열을 set 해야 한다. 이것은 `attributed string` 에서 link 들의 키로 사용이 될 것이다.

그리고 나면, 너의 `string` 에 `attribute` 를 설정할 때, 이 key 들이 알맞은 NSURL을 가리키도록 할 수 있다.

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

위의 코드는 대시 스타일의 밑줄이 그어진 연회색 링크를 만들어낼 것이다!

![Link Attributes Example](../.gitbook/assets/kittenlink.png)

여기서 볼 수 있듯이, 이것은 attributed string 의 range 에 따라서, 여러가지 스타일을 각각의 링크에 적용하기에 편리하다.

## ASTextNodeDelegate

ASTextNodeDelegate 를 따르면 Class 가 TextNode 와 관련된 다양한 이벤트에 반응할 수 있다.  
예를 들면, 아래 처럼 link 를 tap 하는 이벤트에 반응할 수 있다.

```swift
func textNode(_ textNode: ASTextNode, tappedLinkAttribute attribute: String, value: Any, at point: CGPoint, textRange: NSRange) {
    guard let url = value as? URL else { return }

    // Tap 됐을 때 URL Handling
    UIApplication.shared.openURL(url)
}
```

이와 유사한 방법으로 LongPress 와 Highlighting 에 대해 다음과 같이 반응할 수 있다.

* textNode\(\_ , shouldHighlightLinkAttribute: value: point:\)
* textNode\(\_ , shouldLongPressLinkAttribute: value: point:\)
* textNode\(\_ , longPressedLinkAttribute: value: point: textRange:\)

## 잘못된 줄 간격과 최대 라인 수

`NSParagraphStyle` 의 `lineSpacing` 을 사용하면 최대 라인 수를 가진 멀티라인 텍스트에서 문제가 발생할 수 있다.

예를 들어 다음 코드를 참조하세요.

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

`ASTextNode` 는 내부적으로 TextKit 을 사용하여 지정된 최대 라인 수를 산출하는 데 필요한 shrink 를 계산한다.

하지만 위의 예시에서는 텍스트가 너무 많이 줄어들게 될 것이다. 4줄의 텍스트 대신에, 3줄의 텍스트와 하단의 이상한 간격이 나타날 것이다. 이 문제를 해결하려면 TextNode 에서 `truncationMode` 를 `.byTruncatingTail` 로 설정해야 한다.

```swift
// ...
let textNode = ASTextNode()
textNode.maximumNumberOfLines = 4
textNode.truncationMode = .byTruncatingTail
textNode.attributedText = NSAttributedString(string: someLongString, attributes: attributes)
//...
```

