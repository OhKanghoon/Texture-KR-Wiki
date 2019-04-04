# ASDisplayNode

## Basic Node

`ASDisplayNode` 는 `UIView` 와 `CALayer` 를 추상화 한 메인 뷰이다.   
`UIView` 가 `CALayer` 를 만들고 소유하는 것과 같은 방식으로 `UIView` 를 생성하고 소유한다.

```swift
let node = ASDisplayNode()
node.backgroundColor = .orange
node.bounds = CGRect(x: 0, y: 0, width: 100, height: 100)

print("Underlying view: \(node.view)")
```

node 는 `UIView` 가 가지고 있는 프로퍼티를 동일하게 가지고 있어, UIKit 에 익숙한 사람들이라면 친숙하게 받아들일 수 있다.

```swift
let node = ASDisplayNode()
node.clipsToBounds = true			     // not .masksToBounds
node.borderColor = UIColor.blue.cgColor  // layer name when there is no UIView equivalent

print("Backing layer: \(node.layer)")
```

위의 코드를 보면 알 수 있듯이 node 에서 일반적인 UIView 를 다룰 때와 마찬가지로 CALayer 에 접근할 수 있다.

Node Container 와 함께 사용하면 node 의 프로퍼티는 background thread 에서 설정되고, node 에 캐시된 프로퍼티로 뷰와 레이어는 lazy 하게 구성된다.

## View Wrapping

Node 를 생성할 때 UIView 도 사용할 수 있다. \( 생성자의 viewBlock 에 반환 \)  
이 경우 Node 의  Display 단계가 동시에 발생한다. 왜냐하면 Node 가 UIView 가 아닌 \_ASDisplayView 를 Wrapping 하고 있을 때만 비동기 적으로 Display 되기 때문이다.

```swift
let node = ASDisplayNode(viewBlock: { () -> UIView in
    let view = UIView()
    return view
})
```

위의 예시처럼 UIView 서브 클래스를 ASDisplayNode 서브 클래로 쉽게 변환할 수 있다.

## SafeArea Insets

`Texture 2.7`부터 safeAreaInsets을 지원합니다. **\#available\(iOS 11.0, \*\)** 로 safeAreaInsets사용에 대해서 분기하실 필요가 없습니다. 

사용시 유의해야할 점은 

* ViewController와 **가장 밀접한 Node**에서 접근하셔야됩니다. \(그 이외 자식노드에선 항상 UIEdgeInsets이 zero 값입니다.\) 
* **automaticallyRelayoutOnSafeAreaChanges** flag를 활성화 시켜야합니다. 활성화시키면 safeArea 값이 변화할 때마다 layout을 다시 그려냅니다. 

```swift
let node = ASDisplayNode()
node.automaticallyRelayoutOnSafeAreaChanges = true
let safeAreaInsets: UIEdgeInsets = self.node.safeAreaInsets
```



