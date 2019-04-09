# ASTableNode

`ASTableNode` 는 UIKit 의 `UITableView` 와 동일하며 `UITableView` 대신 사용할 수 있다.  
`ASTableNode` 가 `UITableView` 의 필수 메서드를 대체함

```swift
func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell
```

`cellForRowAt` 은 아래의 두 메서드 중 하나를 선택해 대처할 수 있다.

#### 1. nodeForRowAt

```swift
func tableNode(_ tableNode: ASTableNode, nodeForRowAt indexPath: IndexPath) -> ASCellNode
```

#### 2. nodeBlockForRowAt

```swift
func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock
```

> tableNode 가 모든 Cell 을 동시에 준비하고 display 할 수 있도록, `nodeBlockForRowAt` 을 사용하는 것이 좋다  
> nodeBlock 은 모든 SubNode 의 initialization 메서드를 백그라운드에서 실행할 수 있게한다.

두 메서드는 `ASCellNode` 또는 `ASCellNodeBlock` 을 반환해야 한다.  
`ASCellNodeBlock` 은 백그라운드 쓰레드에서 `ASCellNode` 를 생성할 수 있는 블록이다.

> NOTE : ASCellNode 는 ASTableNode, ASCollectionNode, ASPagerNode에서 사용 된다.  
> NOTE : 두 메서드 모두 ReUse 메커니즘을 필요로 하지 않는다.

## UITableViewController 를 ASViewController 로 대체하기

Texture 는 `UITableViewController` 와 같은 기능을 제공하지 않는다.  
대신 `ASTableNode` 로 생성한 `ASViewController` 를 사용하세요.

`ASTableNode` 를 `ASViewController` 의 -init\(node:\) 을 사용하여 할당한다.

```swift
class ViewController: ASViewController<ASTableNode> {
    init() {
        let tableNode = ASTableNode(style: .plain)
    
        super.init(node: tableNode)
    }
}
```

## Node Block Thread Safety Warning

Node Block 이 Thread-Safe 하다는건 굉장히 중요하다.  
그 방법 중 하나는 데이터 모델이 Node Block 외부에서 접근가능한지 확인하는 것이다.  
그러므로, 블록 내부에서 인덱스를 사용해야 할 가능성은 거의 없다.

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

## ASTableView 에 접근하기

 `ASTableView`  `ASTableNode` 를 위해 제거되었다.

> UITableView 의 서브 클래스인 ASTableView 는 여전히 ASTableNode 에서 내부적으로 사용되고 있다. 직접 생성해서는 안 되지만 ASTableNode의 .view 프로퍼티로 직접 사용할 수 있다. 노드의 뷰 또는 레이어 프로퍼티 각각 -viewDidLoad 또는 -didLoad가 호출된 후에만 접근해야 한다는 점을 잊지 마세요.

예를 들어 테이블의 separator style 를 설정한다면 viewDidLoad\(:\) 에서 아래 예와 같이 TableNode 의 view 에 접근하면 된다.

```swift
override func viewDidLoad() {
  super.viewDidLoad()

  tableNode.view.allowsSelection = false
  tableNode.view.separatorStyle = .none
  tableNode.view.leadingScreensForBatching = 3.0  // default is 2.0
}
```

## Table Row Height

중요한 `ASTableNode` 가 `UITableView` 의 `tableView(:heightForRowAt:)` 와 같은 기능을 제공하지 않는다는 것이다.

이는 노드가 제공된 제약조건에 근거하여 자체 높이를 결정하는 책임이 있기 때문이다.  
이것은 당신이 더 이상 이 세부사항을 뷰 컨트롤러 수준에서 결정하기 위해 코드를 쓸 필요가 없다는 것을 의미한다.

노드는 `layoutSpecThatFits(:)` 메서드에서 반환된 layoutSpec 을 통해 높이를 정의한다.  
제한된 크기를 가진 모든 노드는 원하는 크기를 계산할 수 있다.

> 기본적으로 `ASTableNode` 는 최소 너비가 `tableNode` 의 너비이고 최소 높이가 0인 크기 범위 제약 조건을 Cell 에 제공한다. 최대 너비는 `tableNode` 의 너비이기도 하지만 최대 높이는 `FLT_MAX` 이다.
>
> 이것은 모두 'tableNode' 의 Cell 이 항상 'tableNode' 의 전체 너비를 채울 수 있지만, 그 높이는 유연해서 자동으로 늘어나는 self-sizing cell 을 만드는 것이다.

만약  `ASCellNode` 에서 `setNeedsLayout()` 를 호출하면, 그것은 자동으로 다른 레이아웃 패스를 수행하게 되고, 그것의 전체적인 원하는 크기가 바뀌면, table이 알고 스스로 업데이트 한다.

이것은 일반적으로 reload row / item 을 호출해야 하는 UIKit 과 다르다. 이는 수 톤의 코드를 절약할 수 있다.

## Examples

* [DoggyGram](https://github.com/GeekTree0101/DoggyGram)
* [RxMVVM-Texture](https://github.com/GeekTree0101/RxMVVM-Texture)
* [ASDKgram-Swift](https://github.com/TextureGroup/Texture/tree/master/examples_extra/ASDKgram-Swift)

