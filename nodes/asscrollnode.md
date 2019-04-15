# ASScrollNode

`ASScrollNode` 는 하위의 뷰가 `UIScrollView` 인 `ASDisplayNode` 입니다. 이 클래스는 자동으로 `ASLayoutSpec` 의 크기를 스크롤 가능한 contentSize 에 맞춰줍니다.

## automaticallyManagesContentSize

이 값이 활성화 되어있으면, `ASScrollNode`의 layout spec 에 의해 계산된 크기가 스크롤뷰의 `.contentSize` 를 결정하게 됩니다. 이는 `layoutSpec` 의 사이즈가 bounds\(결국 frame\) 에 적용되는 대부분의 노드들과 다릅니다. 이 모드에서는 스크롤뷰의 bounds 는 항상 부모의 크기를 꽉 채우게 됩니다.

`automaticallyManagesContentSize` 는 `layoutSpecThatFits:` 를 구현한 `ASScrollNode` 의 자식 클래스들에게 유용하며, 또한 `.layoutSpecBlock` 와 함께 base 클래스에서도 사용될 수 있습니다.

```swift
// 1. ASScrollNode상속받아서 구현하는 
class SubScrollNode: ASScrollNode {

    override init() {
       super.init()
       self.automaticallyManagesSubnodes = true
    }
    
    override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
         // TODO:     
    }
}

// 2. layoutSpecBlock활용하는 

let node = ASScrollNode()
node.automaticallyManagesSubnodes = true
node.layoutSpecBlock = { (scrollNode, constrainedSize) -> ASLayouotSpec in 
   // TODO:
}

```

두가지 경우 모두 layout spec 에 있는 노드들이 스크롤 가능한 영역에 자동으로 추가될 수 있도록 하기 위해 `.automaticallyManagesSubnodes`를 사용하는 것이 일반적입니다.

이렇게 한다면, layout size 를 잡아둘\(captured\) 필요가 없으며, wrapper 로서의 절대적인 layout spec 을 사용할 필요도 없고, 코드 상에서 `contentSize` 를 설정할 필요도 없습니다. **layout 변화와 함께 사이즈는 업데이트 될 것**입니다. 간단하게 `ASStackLayoutSpec`을 반환하여, 스크롤 가능한 영역이 모든 컨텐츠를 보여줄 수 있도록 하는 것이 일반적이고 유용합니다.

* 새로운 자식 추가/제거에 따른 변화를 주기 위해 **setNeedsLayout**을 호출해주세요. 
* 부모\(ASScrollNode\)에 비해 자식들의 합이 작을 경우 스크롤은 자동으로 비활성화됩니다. 
* 부모\(ASScrollNode\)에 비해 자식들의 합이 클 경우 스크롤은 자동으로 활성화됩니다. 

## scrollableDirections

이 옵션은 `automaticallyManagesContentSize`를 사용할 때 유용합니다. 특히 만약 당신이 가로로 긴 콘텐츠를 사용하게 된다면 더 유용합니다. 

_default 는 세로\(vertical\)이기 때문입니다._

이 프로퍼티는 콘텐츠의 사이즈를 결정할 때 `constrainedSize` 가 어떻게 해석될 지에 관여합니다.

| **Name** | Description |
| :--- | :--- |
| vertical | \`constrainedSize\` 는 무한한 \`.height\` \(\`CGFLOAT\_MAX\`\) 을 갖고 있다고 해석되어서, layout spec 에 있는 스택과 다른 콘텐츠들이 확장할 수 있도록 하고, 스크롤 가능한 영역을 만들어냅니다. |
| horizontal | \`constrainedSize\` 는 무한한 \`.width\` \(\`CGFLOAT\_MAX\`\) 을 갖고 있다고 해석됩니다. |
| vertical & horizontal | \`constrainedSize\` 는 양방향으로 무한한 것으로 해석됩니다. |
| none | 자식 노드들의 합에 대한 사이즈와 상관없이 스크롤을 비활성화합니다.  |
| right | 우측 방향 스크롤에 대해서만 활성화합니다. |
| left | 좌측 방향 스크롤에 대해서만 활성화합니다. |
| up | 상향 스크롤에 대해서만 활성화합니다. |
| down | 하향 스크롤에 대해서만 활성화합니다.  |

```swift
let node = ASScrollNode()
node.scrollableDirections = .vertical
node.scrollableDirections = .horizontal
node.scrollableDirections = [.top, .bottom]
node.scrollableDirections = [.left, .right]
```

## 예제

당신이 스크롤뷰와 친숙하지 않을 수 있다는 점을 고려해서 말하자면, 스크롤뷰란? 특정 영역을 벗어나 더 많은 공간을 차지하는 콘텐츠를 보여주는 창문입니다.

가령 매우 커다란 이미지가 있는데, 스크린의 200pt x 200pt 공간만을 차지하고 싶다고 해봅시다.

```swift
// NOTE: If you are using a horizontal stack, set scrollNode.scrollableDirections.

let scrollNode = ASScrollNode()
scrollNode.automaticallyManagesSubnodes = true
scrollNode.automaticallyManagesContentSize = true

scrollNode.layoutSpecBlock = { node, constrainedSize in
  let stack = ASStackLayoutSpec.vertical()
  // Add children to the stack.
  return stack
}
```

