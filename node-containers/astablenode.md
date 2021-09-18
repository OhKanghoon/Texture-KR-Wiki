# ASTableNode

`ASTableNode` 는 UIKit 의 `UITableView` 와 동일하며 `UITableView` 대신 사용할 수 있습니다.  
`ASTableNode` 가 `UITableView` 의 필수 메서드를 대체합니다.

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
```

`cellForRowAt` 은 아래의 두 메서드 중 하나를 선택해 대체할 수 있습니다.

### 1. nodeForRowAt

```swift
func tableNode(_ tableNode: ASTableNode, nodeForRowAt indexPath: IndexPath) -> ASCellNode
```

### 2. nodeBlockForRowAt

```swift
func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock
```

> tableNode 가 모든 Cell 을 동시에 준비하고 보여줄 수 있도록, `nodeBlockForRowAt` 을 사용하는 것이 좋습니다  
> nodeBlock 은 모든 SubNode 의 생성자를 백그라운드에서 실행할 수 있게합니다.

`ASCellNodeBlock` 은 백그라운드 쓰레드에서 `ASCellNode` 를 생성할 수 있는 블록입니다.

NOTE

* ASCellNode는 ASTableNode, ASCollectionNode, ASPagerNode 에서 모두 사용할 수 있습니다.
* ASTableNode 는 Cell 을 재사용하지 않습니다.
* nodeBlock 방식을 선호합니다.
* nodeBlock 은 thread-safe 해야 합니다.

## Node Block Thread Safety Warning

Node Block 이 thread-safe 해야 하는건 중요합니다. 그 중 하나는 데이터 모델이 Node Block 외부에서 접근하도록 하는 것입니다. 그러므로, 블록 내부에서 인덱스를 사용해야 할 일은 거의 없습니다.

```swift
func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock {
  guard photoFeed.count > indexPath.row else { return { ASCellNode() } }

  let photoModel = photoFeed[indexPath.row]

  // 이것은 Background Thread에서 실행될 수 있다. 이 Thread가 안전한지 확인해야 한다.
  let cellNodeBlock = { () -> ASCellNode in
    let cellNode = PhotoCellNode(photo: photoModel)
    cellNode.delegate = self
    return cellNode
  }

  return cellNodeBlock
}
```

위의 예시에서 Node Block 을 만들기 전에 인덱스를 사용하여 사진 모델에 접근하는 방법을 확인할 수 있습니다.

## UITableViewController 를 ASDKViewController 로 대체하기

Texture 는 `UITableViewController` 와 같은 기능을 제공하지 않습니다.  
대신 `ASTableNode` 로 생성한 `ASDKViewController` 를 사용하세요.

`ASTableNode` 를 `ASDKViewController` 의 -init\(node:\) 을 사용하여 생성합니다.

```swift
final class ViewController: ASDKViewController<ASTableNode> {
  init() {
    let tableNode = ASTableNode(style: .plain)
    super.init(node: tableNode)
  }
}
```

## ASTableView 에 접근하기

`ASTableView` `ASTableNode` 를 위해 제거되었습니다. 하지만 UITableView\(ASTableView\) 의 속성을 변경해야하는 경우에는 접근할 수 있어야 합니다.

> UITableView 의 서브 클래스인 ASTableView 는 여전히 ASTableNode 에서 내부적으로 사용되고 있습니다. 직접 생성해서는 안 되지만 ASTableNode의 .view 프로퍼티로 직접 사용할 수 있습니다. 노드의 뷰 또는 레이어 프로퍼티 각각 -viewDidLoad 또는 -didLoad가 호출된 후에만 접근해야 한다는 점을 잊지 마세요.

예를 들어 테이블의 separator style 를 설정한다면 viewDidLoad\(:\) 에서 아래 예와 같이 ASTableNode 의 view 에 접근하면 됩니다.

```swift
override func viewDidLoad() {
  super.viewDidLoad()

  self.tableNode.view.allowsSelection = false
  self.tableNode.view.separatorStyle = .none
  self.tableNode.view.leadingScreensForBatching = 3.0  // default is 2.0
}
```

## Table Row Height

`ASTableNode` 가 `UITableView` 의 `tableView(:heightForRowAt:)` 와 같은 기능을 제공하지 않습니다.

노드가 제공된 제약조건에 근거하여 자체 높이를 결정하는 책임이 있기 때문입니다.  
이는 더 이상 이 뷰 컨트롤러에서 셀 사이즈를 결정하기 위한 코드를 쓸 필요가 없다는 것을 의미합니다.

노드는 `layoutSpecThatFits(:)` 메서드에서 반환된 layoutSpec 을 통해 높이를 정의합니다.  
제한된 크기를 가진 모든 노드는 원하는 크기를 계산할 수 있다.

> 기본적으로 `ASTableNode` 는 최소 너비가 `tableNode` 의 너비이고 최소 높이가 0인 크기 범위 제약 조건을 Cell 에 제공합니다. 최대 너비는 `tableNode` 의 너비이기도 하지만 최대 높이는 `FLT_MAX` 입니다.
>
> 이것은 모두 'tableNode' 의 Cell 이 항상 'tableNode' 의 전체 너비를 채울 수 있지만, 그 높이는 유연해서 자동으로 늘어나는 self-sizing cell 이 된다는 것을 의미합니다.

`ASCellNode` 에서 `setNeedsLayout()` 를 호출하면 레이아웃을 변경하고, 계산된 사이즈가 바뀌었다면 tableNode 가 스스로 높이를 업데이트 합니다.

이것은 일반적으로 reload row / item 을 호출해야 하는 UIKit 과 다릅니다. 이로 인해 수 많은 코드를 절약할 수 있습니다.

## Examples

* [DoggyGram](https://github.com/GeekTree0101/DoggyGram)
* [RxMVVM-Texture](https://github.com/GeekTree0101/RxMVVM-Texture)
* [ASDKgram-Swift](https://github.com/TextureGroup/Texture/tree/master/examples_extra/ASDKgram-Swift)

