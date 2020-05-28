# Layout Transition API

#### ![](../.gitbook/assets/progress.gif)

Layout Transition API는 Texture에서 제공하는 모든 컴포넌트들에 대해서 애니메이션을 쉽게 만들 수 있도록 설계되었습니다. 심지어 전체 뷰 집합을 완전히 다른 뷰 집합으로 변형 할 수도 있습니다.

Layout Transition 시스템이 동작을 시작할 때 우선 사전에 내부적으로 레이아웃 **변형 전/후**간의 파악하는 작업을 진행하며, 자동으로 새로운 노드 **추가 및 제거**, 요소의 **위치**에 대해서도 **업데이트**를 합니다.

## 사용법

### 1. animateLayoutTransition을 override합니다.

```swift
class TestNode: ASDisplayNode {

// ...

    override func animateLayoutTransition(_ context: ASContextTransitioning) {

        // 1. transition되기 이전의 frame 값을 가져옵니다.
        let beforeFrame = context.initialFrame(for: TARGET_NODE)

        // 2. transition되고 난 이후의 frame 값을 가져옵니다.
        let afterFrame = context.finalFrame(for: TARGET_NODE)

        self.TARGET_NODE.frame = beforeFrame

        UIView.animate(withDuration: 2.0,
                       delay: 0.5,
                       options: .curveEaseOut,
                       animations: {
                        self.TARGET_NODE.frame = afterFrame
        }, completion: { isCompleted in
            // 3. ASContextTransitioning을 완료 시킵니다. 
            context.completeTransition(isCompleted)
        })
    }

// ...
}
```

Texture로 개발된 모든 UI는 `animationLayoutTransition` method내에서 수행합니다.

animation 동작 호출이 시작될 때 `ASContextTransitioning`을 받아오는데 `ASContextTransitioning`에서 제공해주는 methods 다음과 같습니다.

| Name | Return Type | Description |
| :--- | :--- | :--- |
| initialFrame\(for: ASDisplayNode\) | CGRect | 특정 노드에 대해서 사전에 계산된 레이아웃 **변경전의 Frame값**을 가져옵니다. |
| finalFrame\(for: ASDisplayNode\) | CGRect | 특정 노드에 대해서 사전에 계산된 레이아웃 **변경의 Frame값**을 가져옵니다. |
| completeTransition\(\_ didComplete: Bool\) | Void | transition 완료에 대해서 제어합니다. |
| isAnimated\(\) | Bool | animate 상태인지에 대해서  반환합니다. |
| insertedSubnodes\(\) | Array&lt;ASDisplayNode&gt; | 변경이후에 **추가**될 예정인 노드들을 반환합니다. |
| removedSubnodes\(\) | Array&lt;ASDisplayNode&gt; | 변경이후에 **제거**될 예정인 노드들을 반환합니다. |

### 2. 에니메이션 처리를 위해 Transition Layout API를 호출합니다.

```swift
self.transitionLayout(withAnimation: Bool,
                      shouldMeasureAsync: Bool,
                      measurementCompletion: () -> Void)
```

`animateTransitionLayout` method가 override된 노드에 **`transitionLayout`** method를 호출해주면 됩니다.

| Parameter Name | Description |
| :--- | :--- |
| withAnimation | 필요에 따라 선택사항이지만 false로 지정하더라도 animateTransitionLayout은 동작합니다. 단 animation처리는 되지 않습니다. |
| shouldMeasureAsync | 레이아웃을 **비동기**적으로 측정합니다. |
| measurementCompletion | **새로운 레이아웃이 계산된 경우**에만 호출되는 블록입니다. |

## Example

#### ![](../.gitbook/assets/progress.gif)

```swift
class ProgressBarNode: ASDisplayNode {

    lazy var progressEngageNode: ASDisplayNode = {
        let node = ASDisplayNode()
        node.backgroundColor = .red
        return node
    }()

    private var ratio: CGFloat = 0.0

    override init() {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.backgroundColor = .lightGray
        self.style.height = .init(unit: .points, value: 50.0)
    }

    override func animateLayoutTransition(_ context: ASContextTransitioning) {
        // 1. transition되기 이전의 frame 값을 가져옵니다.
        let beforeFrame = context.initialFrame(for: progressEngageNode)

        // 2. transition되고 난 이후의 frame 값을 가져옵니다.
        let afterFrame = context.finalFrame(for: progressEngageNode)

        progressEngageNode.frame = beforeFrame
        progressEngageNode.alpha = 0.0

        UIView.animate(withDuration: 0.5,
                       delay: 0.0,
                       options: .curveEaseOut,
                       animations: {
                        self.progressEngageNode.alpha = 1.0
                        self.progressEngageNode.frame = afterFrame
        }, completion: { isCompleted in
            // 3. ASContextTransitioning을 완료 시킵니다. 
            context.completeTransition(isCompleted)
        })
    }

    public func setRatio(_ ratio: CGFloat) {
        self.ratio = ratio
        self.transitionLayout(withAnimation: true,
                              shouldMeasureAsync: true,
                              measurementCompletion: nil)
    }

    override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
        progressEngageNode.style.flexBasis = .init(unit: .fraction, value: ratio)
        let spaceLayout = ASLayoutSpec()
        spaceLayout.style.flexBasis = .init(unit: .fraction, value: 1.0 - ratio)

        return ASStackLayoutSpec(direction: .horizontal,
                                 spacing: 0.0,
                                 justifyContent: .start,
                                 alignItems: .stretch,
                                 children: [progressEngageNode, spaceLayout])
    }
}
```

