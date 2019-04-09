# ASCollectionNode

`ASCollectionNode` 는 UIKit 의 `UICollectionView` 와 동일하며, `UICollectionView` 대신 사용할 수 있다.

`ASCollectionNode` 가 `UICollectionView` 의 필수 메서드를 대체한다.

```swift
override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell
```

`cellForItemAt` 은 아래의 두 메서드 중 하나를 선택해 대처할 수 있다.

```swift
override func collectionNode(_ collectionNode: ASCollectionNode, nodeForItemAt indexPath: IndexPath) -> ASCellNode
```

또는

```swift
override func collectionNode(_ collectionNode: ASCollectionNode, nodeBlockForItemAt indexPath: IndexPath) -> ASCellNodeBlock
```

> collectionNode 가 모든 Cell 을 동시에 준비하고 display 할 수 있도록, `nodeBlockForItemAt` 을 사용하는 것이 좋다.

NOTE :

* ASCollectionNode 는 Cell 을 reuse 하지 않는다.
* nodeBlock 방식을 선호한다.
* nodeBlock 은 thread-safe 해야 한다.
* ASCellNode는 ASTableNode, ASCollectionNode, ASPagerNode에서 사용할 수 있다.

## Node Block Thread Safety Warning

Node Block 이 thread-safe 해야 하는건 중요하다.  
그 방법 중 하나는 데이터 모델이 Node Block 외부에서 접근가능한지 확인하는 것이다.  
그러므로, 블록 내부에서 인덱스를 사용해야 할 가능성은 거의 없다.

아래의 `nodeBlockForItemAt` 를 고려해보세요.

```swift
func collectionNode(_ collectionNode: ASCollectionNode, nodeBlockForItemAt indexPath: IndexPath) -> ASCellNodeBlock {
  guard photoFeed.count > indexPath.row else { return { ASCellNode() } }
    
  let photoModel = photoFeed[indexPath.row]
    
  // this may be executed on a background thread - it is important to make sure it is thread safe
  let cellNodeBlock = { () -> ASCellNode in
    let cellNode = PhotoCellNode(photo: photoModel)
    cellNode.delegate = self
    return cellNode
  }
    
  return cellNodeBlock
}
```

위의 예시에서 Node Block 을 생성하기 전에 인덱스로 사진 모델에 접근하는 것을 볼 수 있다.

## UICollectionViewController 를 ASViewController 로 대체하기

Texture 는 `UICollectionViewController` 와 같은 기능을 제공하지 않는다.  
대신 ASViewController 의 유연성을 이용해 모든 유형의 UIViewController 를 재생성할 수 있다.

`ASCollectionNode` 를 `ASViewController` 의 -init\(node:\) 을 사용하여 할당한다.

```swift
init() {
  flowLayout = UICollectionViewFlowLayout()
  collectionNode = ASCollectionNode(collectionViewLayout: flowLayout)

  super.init(node: collectionNode)

  flowLayout.minimumInteritemSpacing = 1
  flowLayout.minimumLineSpacing = 1
}
```

이것은 ASTableNode, ASPagerNode 등 모든 노드에서 잘 작동한다.

## ASCollectionView 에 접근하기

`ASCollectionView` 는 `ASCollectionNode` 를 위해 제거되었다.

> `UICollectionView` 서브 클래스인 `ASCollectionView` 는 여전히 내부에서 사용되고 있다. 직접 만들어서는 안 되지만 ASCollectionNode의 .view 속성에 접속해 직접 사용할 수 있다. 노드의 뷰 또는 레이어 프로퍼티 각각 -viewDidLoad 또는 -didLoad가 호출된 후에만 접근해야 한다는 점을 잊지 마세요.

아래의 `LocationCollectionNodeController` 는 `viewDidLoad(:)` 에서 직접 ASCollectionView 에 접한다.

```swift
override func viewDidLoad() {
  super.viewDidLoad()

  collectionNode.delegate = self
  collectionNode.dataSource = self
  collectionNode.view.allowsSelection = false
  collectionNode.view.backgroundColor = .white
}
```

## Cell Sizing and Layout

`ASCollectionNode` 와 `ASTableNode` 는 `ASCellNode` 의 높이를 추적할 필요가 없다.

현재, Cell 은 constrained size 에 맞게 커질 것이고, 제공된 UICollectionViewLayout에 의해 배치될 것이다.

또한 ASCollectionNode의 `contrentedSizeForItemAt(:)` 를 사용하여 CollectionNode 에서 사용되는 셀을 제한할 수 있다.

## Examples

ASCollectionNode Cell Layout 의 가장 자세한 예는 CustomCollectionView 앱이다. 이 앱은 ASCollectionNode를 사용하는 Pinterest 스타일의 셀 레이아웃과 사용자 정의 UICollectionViewLayout 을 포함한다.

**More Sample Apps with ASCollectionNodes**

* [ASDKgram](https://github.com/texturegroup/texture/tree/master/examples/ASDKgram)
* [CatDealsCollectionView](https://github.com/texturegroup/texture/tree/master/examples/CatDealsCollectionView)
* [ASCollectionView](https://github.com/texturegroup/texture/tree/master/examples/ASCollectionView)
* [CustomCollectionView](https://github.com/texturegroup/texture/tree/master/examples/CustomCollectionView)

## UICollectionViewCell 과의 상호 운용성

`ASCollectionNode` 는 기본 `ASCellNode` 와 함께 `UICollectionViewCell` 사용을 지원한다.

UIKit Cell 은 동일한 ASCollectionNode 내에서 혼합된 경우에도 ASCellNode 의 성능 이점 \(예: preloading, async layout, async drawing\) 이 없다.

하지만 모든 Cell 을 한꺼번에 변환할 필요 없이 프레임워크를 테스트할 수 있다.[ 여기](http://texturegroup.org/docs/uicollectionviewinterop.html)서 자세한 정보를 찾아보세요.

