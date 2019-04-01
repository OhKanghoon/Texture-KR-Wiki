# ASTextNode

ASTextNode 는 Texture 의 기본 텍스트 노드로, 일반적으로 UILabel 을 사용할 때 언제든지 사용할 수 있다.  
또 모든 Rich Text 를 지원하고, ASControlNode 의 서브 클래스로 titleLabel 만 사용해 UIButton을 만들 때 언제든지 사용할 수 있다.

### 기본 사용법

ASTextNode 의 인터페이스는 UILabel 을 사용했던 사람들 모두에게 익숙해야 한다.  
다만, UILabel 과의 첫 번째 차이점은 ASTextNode 는 일반 String 대신 Attributed String 만을 사용한다는 것이다.

```swift
let attrs = [NSFontAttributeName: UIFont(name: "HelveticaNeue", size: 12.0)]
let string = NSAttributedString(string: "Hey, here's some text.", attributes: attrs)

node = ASTextNode()
node.attributedText = string
```

보다시피, 기본 Text Node 를 만드려면 표준 생성자를 사용한 뒤 표시할 Attributed String 을 설정하기만 하면 된다.



### Truncation

TextNode 가 모든 텍스트를 표시할 수 없다면, 텍스트는 가능한 한 많이 표시되고 잘려진 부분은 Truncation String 으로 대체된다.

```swift
textNode = ASTextNode()
textNode.attributedText = string
textNode.truncationAttributedText = NSAttributedString(string: "¶¶¶")
```

결과는 아래와 같다.

![Truncation Example](../.gitbook/assets/image.png)

기본적으로 Truncation String 은 '...' 이 되므로, 이 것만 필요하다면 따로 설정하지 않아도 된다.



### Link Attributes



