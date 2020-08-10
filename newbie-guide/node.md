# Node?

![](../.gitbook/assets/image%20%2815%29.png)

디바이스 화면상 나타나는 모든 객체는 CALayer 로 표현되며, CALayer 에 대한 터치 이벤트와 각종 편의기능 들을 객체로 감싼 형태가 여러분들이 알고 계시는 UIView 입니다.

하지만 View 와 Layer 는 Main-Thread 에서 주로 작동하기 때문에 피드처럼 다양한 컨텐츠가 담긴 스크롤 형태의 UI 에서 Frame Drop 은 불가피 합니다. 뿐만 아니라 이미지 다운로드 및 attributed text decoding 와 관련 로직을 주니어 개발자들이 개발하기에는 어려움이 있습니다.

Texture는 이러한 로직들과 Frame Drop 을 개선하기 위해 View 객체를 감싼 Node 라는 객체를 만들었고, 화면상 나타날 수 있는 모든 구성요소를 스레드 안전성을 유지하면서 유연하고 신속하게 처리합니다.

![ASDisplayNode &#xC11C;&#xBE0C;&#xB178;&#xB4DC; &#xAD6C;&#xC870;&#xB3C4;](../.gitbook/assets/image%20%2818%29.png)

UIKit 에 UIView 를 기준으로 다양한 SubClass\(UIImageView, UIButton etc.\) 가 있듯이, Texture 도 ASDisplayNode 를 기준으로 다양한 SubClass를 제공합니다.

## Texture Nodes

| UIKit | Texture |
| :--- | :--- |
| UIView | ASDisplayNode |
| UIScrollView | ASScrollNode |
| UITableViewCell / UICollectionViewCell | ASCellNode |
| UILabel | ASTextNode |
| UITextView | ASEditableTextNode |
| UIImage | ASImageNode |
| AVPlayerLayer | ASVideoNode |
| UIMoviePlayer | ASVideoPlayerNode |
| UIControl | ASControlNode |
| UIButton | ASButtonNode |
| MKMapView | ASMapNode |

Texture 는 UIKit 의 다양한 View Component 에서 제공하는 가장 기본적인 attribute property\(attributedText, cornerRadius etc\) 를 동일하게 제공합니다. 더 나아가 UIKit 에서 제공하지 않는 특수한 편의기능들도 제공합니다.

예를 들어 UILabel 의 터치영역을 지정하거나 tail truncate attributed string 를 추가하기 위해 보통 [TTTAttributedLabel](https://github.com/TTTAttributedLabel/TTTAttributedLabel) 과 같은 라이브러리를 사용합니다. 하지만 Texture 의 ASTextNode 는 hitTestSlop, link touch, tail truncate attributed string 등의 기능을 추가로 제공합니다.

## Texture Node Containers

| UIKit | Texture |
| :--- | :--- |
| UIViewController | ASDKViewController |
| UICollectionView | ASCollectionNode |
| UITableView | ASTableNode |
| UIPageViewController | ASPagerNode |
| UINavigationController | ASNavigationController |
| UITabBarController | ASTabBarController |

#### Node Container 를 사용하는 이유

Node Container 는 Node 의 Intelligent Preloading 을 자동으로 관리합니다.

> Intelligent Preloading 은 모든 Node 의 Layout Measurement, Data Fetching, Decoding, Rendering 이 비동기적으로 수행됨을 의미합니다.

Node Container 없이 Node 를 직접 사용할 수 있지만, 추가적인 호출을 추가하지 않는 한 Node 는 UIKit 같이 화면에 나타나는 경우에만 렌더링 됩니다. 이로 인해 성능이 저하되고, 컨텐츠가 깜박일 수 있습니다.

## 사용법

### 1. Texture Node Container를 기반으로 한 ViewController 만들기

```swift
// UIViewController
class ViewController: UIViewController {

    init() {
        super.init(nibName: nil, bundle: nil)
    }
}

// ASViewController with ASDisplayNode (default)
class ViewController: ASDKViewController<ASDisplayNode> {

    override init() {
        super.init(node: ASDisplayNode.init())
    }
}
```

ASDKViewController는 Generic형태로 ASDisplayNode Subclass를 기반으로 ViewController를 만들 수 있습니다.

예를 들어 UITableViewController와 같은 ViewController를 만들려면 ASDisplayNode대신 ASTableNode를 사용하면 됩니다.

```swift
// UITableViewController
class ViewController: UITableViewController {

    init() {
        super.init(nibName: nil, bundle: nil)
    }
}

// ASViewController with ASTableNode
class ViewController: ASDKViewController<ASTableNode> {

    override init() {
        super.init(node: ASTableNode.init())
    }
}
```

[ASViewController에 대해서 더 자세히 알아보기](https://texture-kr.gitbook.io/wiki/node-containers/asviewcontroller).

### 2. Texture Node를 기반으로 한  Subclass 만들기

![Lego&#xCC98;&#xB7FC; &#xC870;&#xB9BD;&#xD574;&#xC11C; UI&#xB97C; &#xB9CC;&#xB4E4;&#xC5B4;&#xAC00;&#xB294; Texture ](../.gitbook/assets/image%20%2816%29.png)

Texture 는 여러분들이 일반적으로 생각하시는 Storyboard 및 Xib 기반으로 UI 를 만들지 않습니다. UI 의 각 컴포넌트를 재사용할 수 있도록 개발이 가능하며 여러 컴포넌트 들을 레고 조합하듯 개발합니다.

필요에 따라서 Texture 에서 제공해주는 Node 를 Subclass 로 만들어서 요구사항에 맞게 개발합니다.

```swift
class TalkMessageNode: ASDisplayNode {

    override init() {
        super.init()
    }
}

class LikeButtonNode: ASButtonNode {

    override init() {
        super.init()
    }
}
```

[ASDisplayNode에 대해서 더 자세히 알아보기](https://texture-kr.gitbook.io/wiki/nodes/asdisplaynode)

## Node의 기본 메서드와 Life Cycle

| UIKit | Thread | Texture | Thread |
| :--- | :--- | :--- | :--- |
| init | Main | init | Background |
| layoutSubviews | Main | didLoad | Main |
|  |  | layoutSpecThatFits | Background |
|  |  | layout | Main |

### init:

UIKit 과는 다르게 Main Thread 가 아닌 Background Thread 에서 동작합니다.

**경고\)** layer, view, gesture recognizer 등 **Main Thread** 에서 접근 가능한 속성들은 **didLoad 메소드**에서 접근해야 합니다.

### didLoad:

initialization 이 끝이나고 실질적으로 Node 객체가 load 됐음을 의미합니다. 한번만 호출되며, 여기서 주로 Main Thread 에서 접근 가능한 속성들을 사용할 수 있습니다. \(ex. gesture recognizer나 또는 layer, view property 접근 등등.\)

### layoutSpecThatFits:

Texture는 UIKit 다르게 Auto-Layout 을 사용하지 않습니다.

대신, [Yoga Layout Flex-Box](https://yogalayout.com/) 기반으로 Layout 을 설계합니다. 해당 method 는 Background Thread 에서 동작하며, 레이아웃 설계에 대한 무거운 로직들을 처리하고 개발자 의도에 따라 설계된 레이아웃을 빌드합니다.

주의할 점 : **순수하게 Texture에서 제공해주는 LayoutSpec 및 Layout Elements Properties 만 사용**해야하며 그 이외 Main Thread에서 사용가능한 API나 GCD, Lock과 같이 Layout Building 과정에 방해되는 요소를 사용해서는 안됩니다.

[LayoutSpec에 대해서 자세히 알아보기 ](https://texture-kr.gitbook.io/wiki/newbie-guide/layoutspec)

### layout:

layoutSpecThatFits 에서 Layout Building 이 끝난 이후 Main Thread 에서 호출되는 메서드입니다. layout 이 변경될 때 마다 호출되며, 여기선 주로 Layout Build 가 완료된 UI 를 Clip Corner한다던가 Gradient 를 추가하는 등 **layout 변화에 따른 업데이트가 필요한 추가적인 요소를 처리합니다.**

```swift
class ExampleNode: ASDisplayNode {

   let imageNode = ASImageNode()

   override init() {
      super.init()
      // TODO: Background Thread에서 동작
      self.addSubnode(imageNode)
   }

   override func didLoad() {
       super.didLoad()
       // TODO: Main Thread에서 접근 가능한 Property를 사용
   }

   override func layout() {
       super.layout()
       // TODO: layout변화에 따른 업데이트가 필요한 추가적인 요소를 처리합니다.
   }

   override func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec {
      // TODO: 순수하게 Texture에서 제공해주는 LayoutSpec 및 Layout Elements Properties 만 사용
      let imgLayout = ASInsetLayoutSpect(insets: .zero, child: imageNode)
      imgLayout.style.height = .init(unit: .points, value: 500.0)
      return imgLayout
   }
}
```

## Automatic Subnode Management \(ASM\)

layout build과정에서 node 를 관리하는 flag 이며, layoutSpecThatFits 에서 직접적으로 영향받는 node를 자동으로 추가 및 제거해주는 것을 의미합니다.

예를 들자면 다음과 같습니다.

```swift
class ExampleNode: ASDisplayNode {

   let buttonNode = ASButtonNode()
   let textNode = ASTextNode()

   var isButtonNodeOnly: Bool = false

   override init() {
      super.init()
      self.automaticallyManagesSubnodes = true // <- HERE!
   }

   override func didLoad() {
      buttonNode.addTarget(self,
                           action: #selector(didTapButton),
                           forControlEvents: .touchUpInside)
   }

   @objc func didTapButton() {
      self.isButtonNodeOnly = !self.isButtonNodeOnly
      self.setNeedsLayout() // Layout을 다시 그려줘!
   }

   override func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec {
      var elements: [ASLayoutElement] = [buttonNode]
      if !isButtonNodeOnly {
          elements.append(imageNode)
      }
      return ASAbsoluteLayoutSpec(children: elements)
   }
}
```

위의 코드와 같이 automaticallyManagesSubnodes 를 활성화 시켜주면 개발자 의도에 따라 node를 removeFromSupernode 혹은 addSubnode 를 layout building 과정에서 알아서 처리합니다.

