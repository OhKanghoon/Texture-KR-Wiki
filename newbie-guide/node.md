# Node?

![](../.gitbook/assets/image%20%2810%29.png)

디바이스 화면상 나타나는 모든 객체는 CALayer로 표현되며, CALayer에 대한 터치 이벤트와 각종 편의용 기능들을 객체로 감싼 형태가 여러분들이 알고 계시는 UIView입니다.

하지만 View와 Layer는 Main-Thread에서 주로 작업이 되기 때문에 피드와 같이 다양한 컨텐츠가 담긴 스크롤 형태의 UI상에서 Frame Drop은 불가피 합니다. 뿐만 아니라 image fetching 및 attributed text decoding 과 같은 과정들에 대한 처리 로직을 뉴비나 쥬니어 개발자들이 처리할 방법에 대해선 익숙하지 않은 부분들도 있습니다. 따라서 시니어 개발자가 없는 환경이나 그런 기반이 갖추어져있지 않은 스타트업에서 이러한 부분들을 감당하기에는 힘든 부분들이 많습니다.

Texture는 이러한 모든 로직들과 Frame Drop을 개선하기 위해서 View 객체를 감싼 Node라는 객체를 만들어서 디바이스 화면상 나타날 수 있는 모든 구성요소를 스레드로 부터 안전을 유지하면서 유연하고 신속하게 처리합니다.

![ASDisplayNode &#xC11C;&#xBE0C;&#xB178;&#xB4DC; &#xAD6C;&#xC870;&#xB3C4;](../.gitbook/assets/image%20%2812%29.png)

UIKit와 마찬가지로 UIView를 기준으로 다양한 SubClass\(UIImageView, UIButton etc.\) 가 있듯이, Texture도 마찬가지로 ASDisplayNode를 기준으로 UIKit와 마찬가지로 다양한 SubClass를 제공합니다. 



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

UIKit의 다양한 View Component에서 제공해주는 가장 기본적인 attribute property\(attributedText, cornerRadius etc\)를 Texture에서도 마찬가지로 제공해줍니다. 더 나아가 UIKit에서 제공해주지않는 특수한 편의용 기능들도 제공해줍니다. 예를 들어 UILabel의 터치영역 및 tail truncate attributed string 를 추가하기위해선 보통 [TTTAttributedLabel](https://github.com/TTTAttributedLabel/TTTAttributedLabel)과 같은 라이브러리를 사용합니다. 하지만 Texture의 ASTextNode는 link touch 및 tail truncate attributed string 과 같은 편의용 기능을 제공한다는 점입니다. 

## Texture Node Containers

| UIKit | Texture |
| :--- | :--- |
| UIViewController | ASViewController |
| UICollectionView | ASCollectionNode |
| UITableView | ASTableNode |
| UIPageViewController | ASPagerNode |
| UINavigationController | ASNavigationController |
| UITabBarController | ASTabBarController |

####  Node Container 를 사용하여 얻는 점

Node Container 는 Node 의 Intelligent Preloading 을 자동으로 관리한다.  
Intelligent Preloading 은 모든 Node 의 Layout Measurement, Data Fetching, Decoding, Rendering 이 비동기적으로 수행됨을 의미한다. 이것이 여러가지 장점들 중에서도 Node Container 내에서 Node 를 사용하는 것을 권장하는 이유이다.

> Node Container 없이 Node 를 직접 사용할 수 있지만, 추가적인 호출을 추가하지 않는 한 노드는 UIKit  같이 화면에 나타나는 경우에만 display 한다. 이로 인해 성능이 저하되고, 컨텐츠가 깜박일 수 있다.

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
class ViewController: ASViewController<ASDisplayNode> {
    
    init() {
        super.init(node: ASDisplayNode.init())
    }
}
```

ASViewController는 Generic형태로 ASDisplayNode Subclass를 기반으로 ViewController를 만들 수 있습니다.

예를 들어 UITableViewController와 같은 ViewController를 만들려면 ASDisplayNode대신 ASTableNode를 사용하면 됩니다.

```swift
// UITableViewController
class ViewController: UITableViewController {
    
    init() {
        super.init(nibName: nil, bundle: nil)
    }
}

// ASViewController with ASTableNode
class ViewController: ASViewController<ASTableNode> {
    
    init() {
        super.init(node: ASTableNode.init())
    }
}

```

[ASViewController에 대해서 더 자세히 알아보기](https://texture-kr.gitbook.io/wiki/~/edit/drafts/-Lan3E8D_zgPv5yksm1R/node-containers/asviewcontroller). 

### 2. Texture Node를 기반으로 한  Subclass 만들기

![Lego&#xCC98;&#xB7FC; &#xC870;&#xB9BD;&#xD574;&#xC11C; UI&#xB97C; &#xB9CC;&#xB4E4;&#xC5B4;&#xAC00;&#xB294; Texture ](../.gitbook/assets/image%20%2811%29.png)

Texture는 여러분들이 일반적으로 생각하시는 Storyboard 및 Xib를 기반으로 UI를 만들지 않습니다. 그렇기 때문에 UI의 각 컴포넌트를 재사용할 수 있게 개발이 가능하며 여러 컴포넌트들을 어셈블리하여 레고조합하듯이 개발합니다.

필요에 따라서 Texture에서 제공해주는 Node를 Subclass 로 만들어서 필요에 따른 요구사항에 맞게 개발합니다. 

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

[ASDisplayNode에 대해서 더 자세히 알아보기](https://texture-kr.gitbook.io/wiki/~/drafts/-Lan3E8D_zgPv5yksm1R/primary/nodes/asdisplaynode) 

### 

## Node의 기본 메서드와 Life Cycle

| UIKit | Thread | Texture | Thread |
| :--- | :--- | :--- | :--- |
| init | Main | init | Background |
| layoutSubviews | Main | didLoad | Main |
|  |  | layoutSpecThatFits | Background |
|  |  | layout | Main |

#### init:

UIKit와는 다르게 Main Thread가 아닌 Background Thread에서 동작합니다. 하지만 주의 해야할 점은 layer및 view property 접근은 MainThread에서 해야함으로 gesture recognizer와 같은 **main thread**에서 접근이 필요로 하는 기능에 대해선 **didLoad method**에서 처리해주시길 바랍니다.



#### didLoad:

initialization 이 끝이나고 실질적으로 Node 객체가 load됬음을 의미합니다. 한번만 호출되며, 여기서 주로 Main Thread에서 접근 가능한 Property를 사용할 수 있습니다. \(ex. gesture recognizer나 또는 layer, view property 접근 등등.\)



#### layoutSpecThatFits:

Texture는 UIKit와 다르게 Xib나 Storyboard위에서 설계하지 않습니다. 즉, Auto-Layout을 사용하지 않습니다.

대신, [Yoga Layout Flex-Box](https://yogalayout.com/) 기반으로 Layout을 설계합니다. 해당 method는 Background Thread에서 동작하며, 레이아웃 설계에 대한 무거운 로직들을 처리하고 개발자 의도에 따라 설계된 레이아웃을 빌드합니다. 

주의 해야될점은 여기선 **순수하게 Texture에서 제공해주는 LayoutSpec 및 Layout Elements Properties 만 사용**해야하며 그 이외 Main Thread에서 사용가능한 API나 GCD, Lock과 같이 Layout Building 과정에 방해되는 요소를 사용해서는 안됩니다.

[LayoutSpec에 대해서 자세히 알아보기 ](https://texture-kr.gitbook.io/wiki/newbie-guide/layoutspec)



#### layout:

layoutSpecThatFits에서 Layout Building이 끝난 이후 Main Thread에서 호출되는 메서드입니다. layout이 변경될 때 마다 호출되며, 여기선 주로 Layout Build가 완료된 UI를 Clip Corner한다던가 Gradient를 추가하는 등 **layout변화에 따른 업데이트가 필요한 추가적인 요소를 처리합니다.**

\*\*\*\*

\*\*\*\*

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

ASM\(Automatic Subnode Management\)란? layout build과정에서 node를 관리하는 flag이며, layoutSpecThatFits 에서 직접적으로 영향받는 node를 addSubnode하거나 이전 layout에서 제거되는 node를 removeFromSupernode 를 자동으로 처리해주는 것을 의미합니다.

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

위의 코드와 같이 automaticallyManagesSubnodes를 활성화 시켜주면 개발자 의도에 따라 node를 removeFromSupernode or addSubnode를 layout building과정에서 알아서 처리해줍니다. 

_didTapButton에서 setNeedsLayout를 호출해주는 이유는 layout 를 다시 rendering하기 위함입니다._

