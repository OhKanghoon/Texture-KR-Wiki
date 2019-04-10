---
description: Texture에서 제공해주는 기본적인 디버깅 툴을 소개합니다.
---

# Debug Tool

## Hit Test Visualization

![](../.gitbook/assets/image%20%282%29.png)

개발자가 의도한 **Hit Test Slop 또는  targetAction**이 지정된 **터치영역**을 위의 사진과 같이 시뮬레이터 또는 디바이스에서 확인하실 수가 있습니다. 

* profileImage의 경우 hitTestSlop 값을 음수로 지정하여 확장 된것을 확인하실 수가 있습니다.
* username의 경우 별도의 hitTestSlop없이 calculated 된 layout size\(정사이즈\)로 터치영역이 잡힌 것을 확인 하실 수가 있습니다. 
* like count \(좋아요 수\) 의 경우 hitTestSlop을 음수가 아닌 양수로 지정하여서 실제 사이즈에 비해 터치영역이 줄어든 것을 확인 하실 수가 있습니다. 

_참고: hitTestSlop은 UIEdgeInsets 을 받습니다._ 

#### 사용법

didFinishLaunchingWithOptions method에서 ASControlNode enableHitTestDebug flag를 활성화 시켜주시면 됩니다. 

```swift
import AsyncDisplayKit

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                 
    ASControlNode.enableHitTestDebug = true        
}
```

## Range Visualization

![](../.gitbook/assets/image%20%2812%29.png)

우측 하단에 보시면 Table 및 Collection에 대한 플로팅 위젯을 볼 수가 있습니다. 이것이 **ASRangeController 튜닝 매개 변수 시각화 도구**라고 합니다. 

![](../.gitbook/assets/image%20%2819%29.png)

Texture의 Table과 Collection은 UIKit와 다르게 UITableView와 UICollectionView에서 제공해주는 Prefetching을 사용하지 않고 ASRangeController를 통해서 Preload, Display 영역에 대한 사이즈를 튜닝할 수가 있습니다. 

\(Intelligent Preloading 참고\) 



#### 매개변수 튜닝 방법 예 

```swift
let collectionNode = ASCollectionNode()

let preloadParams: ASRangeTuningParameters = 
ASRangeTuningParameters(leadingBufferScreenfuls: 1, trailingBufferScreenfuls: 1)
let displayParams: ASRangeTuningParameters = 
ASRangeTuningParameters(leadingBufferScreenfuls: 1, trailingBufferScreenfuls: 1)

collectionNode.setTuningParameters(preloadParams, for: .full, rangeType: .preload)
collectionNode.setTuningParameters(displayParams, for: .full, rangeType: .display)
```



**ASRangeController 튜닝 매개 변수 시각화 도구 활성화** 

didFinishLaunchingWithOptions method에서 ASDisplayNode shouldShowRangeDebugOverlay flag를 활성화 시켜주시면 됩니다. 

```swift
import AsyncDisplayKit

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                 
    ASDisplayNode.shouldShowRangeDebugOverlay = true    
}
```



## Layout Debugging

LLDB를 통해서 간단히 LayoutSpec에 대해서 asciiArtString 형태로 출력할 수 있습니다.

```swift
class SuperNode: ASDisplayNode {
    
    let childNode1: ASButtonNode = {
        let node = ASButtonNode()
        node.backgroundColor = .red
        return node
    }()
    
    let childNode2: ASDisplayNode = {
        let node = ASDisplayNode()
        node.backgroundColor = .blue
        return node
    }()
    
    override init() {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.backgroundColor = .white
    }
    
    override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
        let insets: UIEdgeInsets = .init(top: 50.0, left: 20.0, bottom: 50.0, right: 20.0)
        let insetLayout = ASInsetLayoutSpec.init(insets: insets, child: childNode1)
        return ASOverlayLayoutSpec.init(child: childNode2, overlay: insetLayout)
    }
}

let node = SuperNode()
let output = node.layoutSpecThatFits(ASSizeRangeZero).asciiArtString()
print(output)

```

![](../.gitbook/assets/2019-04-09-4.49.56.png)

따라서 내부 코드를 공개하지 않고도 어떠한 형태로 LayoutSpec를 설계했는지 공유할 수 있으며, 여러 커뮤니티를 통해서 손쉽게 개선점에 대한 피드백을 받을 수가 있습니다. 

