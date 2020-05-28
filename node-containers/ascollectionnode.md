# ASCollectionNode

`ASCollectionNode` 는 UIKit 의 `UICollectionView` 와 동일하며, `UICollectionView` 대신 사용할 수 있다. `ASCollectionNode` 가 `UICollectionView` 의 필수 메서드를 대체합다.

```swift
override func collectionView(_ collectionView: UICollectionView, cellForItemAt indexPath: IndexPath) -> UICollectionViewCell
```

`cellForItemAt` 은 아래의 두 메서드 중 하나를 선택해 대체할 수 있습다.

```swift
override func collectionNode(_ collectionNode: ASCollectionNode, nodeForItemAt indexPath: IndexPath) -> ASCellNode
```

또는

```swift
override func collectionNode(_ collectionNode: ASCollectionNode, nodeBlockForItemAt indexPath: IndexPath) -> ASCellNodeBlock
```

> collectionNode 가 모든 Cell 을 동시에 준비하고 보여줄 수 있도록, `nodeBlockForItemAt` 을 사용하는 것이 좋습니다.

NOTE :

* ASCollectionNode 는 Cell 을 reuse 하지 않는다.
* nodeBlock 방식을 선호한다.
* nodeBlock 은 thread-safe 해야 한다.
* ASCellNode는 ASTableNode, ASCollectionNode, ASPagerNode에서 사용할 수 있다.

## Node Block Thread Safety Warning

Node Block 이 thread-safe 해야 하는건 중요합니다.  
그 중 하나는 데이터 모델이 Node Block 외부에서 접근하도록 하는 것입니다.  
그러므로, 블록 내부에서 인덱스를 사용해야 할 일은 거의 없습다.

아래의 `nodeBlockForItemAt` 를 확인해보세.

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

위의 코드에서 Node Block 을 생성하기 전에 외부에서 인덱스로 사진 모델에 접근하는 것을 확인할 수 있습니다.

## UICollectionViewController 를 ASViewController 로 대체하기

Texture 는 `UICollectionViewController` 와 동일하지는 않습니다.  
대신 ASViewController 의 유연성을 이용해 모든 유형의 UI...ViewController 를 재생성할 수 있습다.

`ASCollectionNode` 를 `ASViewController` 의 -init\(node:\) 을 사용하여 생성합니다.

```swift
init() {
  flowLayout = UICollectionViewFlowLayout()
  collectionNode = ASCollectionNode(collectionViewLayout: flowLayout)

  super.init(node: collectionNode)

  flowLayout.minimumInteritemSpacing = 1
  flowLayout.minimumLineSpacing = 1
}
```

생성자에ASTableNode, ASPagerNode 등 모든 노드를 사용할 수 있니다.

## ASCollectionView 에 접근하기

`ASCollectionView` 는 `ASCollectionNode` 를 위해 제거되었습니다.

하지만 `UICollectionView(ASCollectionView)` 의 속성을 변경해야하는 경우에는 접근할 수 있어야 합니다.

> `UICollectionView` 서브 클래스인 `ASCollectionView` 는 여전히 내부에서 사용되고 있습니다. 직접 만들어서는 안 되지만 ASCollectionNode의 .view 속성에 접속해 직접 사용할 수 있습니다. 노드의 뷰 또는 레이어 프로퍼티 각각 -viewDidLoad 또는 -didLoad가 호출된 후에만 접근해야 한다는 점을 잊지 마세요.

아래의 `LocationCollectionNodeController` 는 `viewDidLoad(:)` 에서 직접 ASCollectionView 에 접근합니다.

```swift
// LocationCollectionNodeController.swift

override func viewDidLoad() {
  super.viewDidLoad()

  collectionNode.delegate = self
  collectionNode.dataSource = self
  collectionNode.view.allowsSelection = false
  collectionNode.view.backgroundColor = .white
}
```

## Cell Sizing and Layout

`ASCollectionNode` 와 `ASTableNode` 를 사용하 `ASCellNode` 의 높이를 관리할 필요가 없습다.

Cell 은 constrained size 에 맞게 커질 것이고, 제공된 UICollectionViewLayout에 의해 배치됩니.

또한 ASCollectionNode의 `contrentedSizeForItemAt(:)` 를 사용하여 CollectionNode 에서 사용되는 셀의 사이즈를 제한할 수 있습다.

## Examples

ASCollectionNode Cell Layout 의 가장 자세한 예는 CustomCollectionView 앱입다. 이 앱은 ASCollectionNode를 사용하는 Pinterest 스타일의 셀 레이아웃과 사용자 정의 UICollectionViewLayout 을 포함합니다.

**More Sample Apps with ASCollectionNodes**

* [ASDKgram](https://github.com/texturegroup/texture/tree/master/examples/ASDKgram)
* [CatDealsCollectionView](https://github.com/texturegroup/texture/tree/master/examples/CatDealsCollectionView)
* [ASCollectionView](https://github.com/texturegroup/texture/tree/master/examples/ASCollectionView)
* [CustomCollectionView](https://github.com/texturegroup/texture/tree/master/examples/CustomCollectionView)

## UICollectionViewCell 과의 상호 운용성

`ASCollectionNode` 는 기본 `ASCellNode` 와 함께 `UICollectionViewCell` 사용을 지원한다.

UIKit Cell 은 동일한 ASCollectionNode 내에서 혼합된 경우에도 ASCellNode 의 성능 이점 \(예: preloading, async layout, async drawing\) 이 없다.

하지만 모든 Cell 을 한꺼번에 변환할 필요 없이 프레임워크를 테스트할 수 있다.[ 여기](http://texturegroup.org/docs/uicollectionviewinterop.html)서 자세한 정보를 찾아보세요.

## ASCellLayoutMode \(&lt;~ 2.8\)

어플리케이션 내에서 Texture를 적용하는데 있어서 융통성을 향상시키기 위해서 제안된 기능입니다.

`ASCellLayuotMode`를 제어함으로서 `ASCollectionNode`가 비동기 레이아웃을 생성하는 동안 `ASCollectionDataSource` 내부적인 기술적 부채문제 및 잠재적인 문제를 디버깅 하거나 해결하는 방향으로 작업이 가능하게 해줍니다. 



```swift
let node = ASCollectionNode.init()
node.cellLayoutMode = .alwaysReloadData 
```

<table>
  <thead>
    <tr>
      <th style="text-align:center">Name</th>
      <th style="text-align:center">Default</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:center">none</td>
      <td style="text-align:center">-</td>
      <td style="text-align:left">&#xC635;&#xC158;&#xC774; &#xC124;&#xC815; &#xB418;&#xC9C0;&#xC54A;&#xACE0;
        none &#xC678;&#xD55C; &#xBAA8;&#xB4E0; &#xC694;&#xC18C; &#xB300;&#xD574;&#xC11C;
        default&#xAC12;&#xC73C;&#xB85C; &#xC9C0;&#xC815;&#xB418;&#xC5B4; &#xC0AC;&#xC6A9;&#xB429;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:center">syncForSmallContent</td>
      <td style="text-align:center">-</td>
      <td style="text-align:left">&#xC0C8;&#xB85C;&#xC6B4; &#xCEE8;&#xD150;&#xCE20;&#xC758; &#xAC2F;&#xC218;&#xAC00;
        &#xC801;&#xC73C;&#xBA74; ASDataController&#xAC00; &#xBC31;&#xADF8;&#xB77C;&#xC6B4;&#xB4DC;
        &#xB300;&#xAE30;&#xC5F4;&#xC5D0;&#xC11C; &#xB300;&#xAE30;&#xD558;&#xAC8C;
        &#xB429;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:center">alwaysSync</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">
        <p>ASDataController&#xAC00; &#xBC31;&#xADF8;&#xB77C;&#xC6B4;&#xB4DC; &#xB300;&#xAE30;&#xC5F4;&#xC5D0;&#xC11C;
          &#xB300;&#xAE30;&#xD558;&#xBA74;&#xC11C; CATransaction &#xBC0F; frame draw&#xC774;&#xD6C4;&#xC5D0;
          hierarchy&#xC0C1; &#xC788;&#xB294; &#xC140;&#xC758; &#xC0C8;&#xB85C;&#xC6B4;
          &#xCD94;&#xAC00; &#xBC0F; &#xBCC0;&#xD654;&#xAC00; <b>&#xB3D9;&#xAE30;&#xC801;</b>&#xC73C;&#xB85C;
          &#xC77C;&#xC5B4;&#xB0A9;&#xB2C8;&#xB2E4;.</p>
        <p><em>alwaysSync &#xB610;&#xB294; alwaysAsync &#xAC00; &#xC124;&#xC815;&#xB418;&#xC9C0; &#xC54A;&#xC744; &#xACBD;&#xC6B0; &#xB370;&#xC774;&#xD130;&#xC18C;&#xC2A4;&#xC0C1; &#xC140;&#xC774; 0&#xAC1C; &#xB610;&#xB294; 1&#xAC1C;&#xC77C; &#xACBD;&#xC6B0; &#xB3D9;&#xAE30;&#xC801;&#xC73C;&#xB85C; &#xAE30;&#xBCF8;&#xB3D9;&#xC791;&#xC744; &#xD558;&#xC9C0;&#xB9CC; 2&#xAC1C; &#xC774;&#xC0C1;&#xC758; &#xACBD;&#xC6B0; &#xBE44;&#xB3D9;&#xAE30;&#xC801;&#xC73C;&#xB85C; &#xB3D9;&#xC791;&#xC744; &#xD558;&#xAC8C; &#xB429;&#xB2C8;&#xB2E4;. </em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:center">alwaysAsync</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">ASDataController&#xAC00; &#xBC31;&#xADF8;&#xB77C;&#xC6B4;&#xB4DC; &#xB300;&#xAE30;&#xC5F4;&#xC5D0;&#xC11C;
        &#xB300;&#xAE30;&#xD558;&#xBA74;&#xC11C; CATransaction &#xBC0F; frame draw&#xC774;&#xD6C4;&#xC5D0;
        hierarchy&#xC0C1; &#xC788;&#xB294; &#xC140;&#xC758; &#xC0C8;&#xB85C;&#xC6B4;
        &#xCD94;&#xAC00; &#xBC0F; &#xBCC0;&#xD654;&#xAC00; <b>&#xBE44;&#xB3D9;&#xAE30;&#xC801;&#xC73C;</b>&#xB85C;
        &#xC77C;&#xC5B4;&#xB0A9;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:center">alwaysReloadData</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">performBatchUpdates&#xC0AC;&#xC6A9; &#xD558;&#xAE30; &#xBCF4;&#xB2E4;
        reloadData &#xC0AC;&#xC6A9;&#xC744; &#xC120;&#xD638;&#xD558;&#xB294; &#xC635;&#xC158;&#xC785;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:center">disableRangeController</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">&#xD574;&#xB2F9; &#xD50C;&#xB798;&#xAE30;&#xADF8; &#xD65C;&#xC131;&#xD654;
        &#xB418;&#xC5B4; &#xC788;&#xC744; &#xACBD;&#xC6B0; &#xC140;&#xC5D0; &#xB300;&#xD574;&#xC11C;
        &#xBC94;&#xC704; &#xAD00;&#xB9AC;&#xBC29;&#xBC95;&#xC774; &#xC801;&#xC6A9;&#xB418;&#xC9C0;
        &#xC54A;&#xC2B5;&#xB2C8;&#xB2E4;. (preload, display, visible)</td>
    </tr>
    <tr>
      <td style="text-align:center">serializeNodeCreation</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">&#xC140; &#xC0DD;&#xC131;&#xC2DC; Data Controller &#xB0B4;&#xC5D0;&#xC11C;
        &#xBCD1;&#xB82C;&#xB85C; &#xC218;&#xD589; &#xB418;&#xC9C0; &#xC54A;&#xB294;
        &#xACBD;&#xC6B0;&#xC5D0; &#xB300;&#xD55C; &#xC5EC;&#xBDF0;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:center">alwaysBatchUpdateSectionReload</td>
      <td style="text-align:center">OFF</td>
      <td style="text-align:left">&#xC139;&#xC158;&#xC5D0; &#xB300;&#xD574;&#xC11C; Reload &#xD638;&#xCD9C;&#xC2DC;
        performBatchUpdate&#xAC00; &#xC0AC;&#xC6A9;&#xB418;&#xB294; &#xC635;&#xC158;&#xC785;&#xB2C8;&#xB2E4;.
        &#xC774; &#xB54C; alwaysReloadData &#xAC00; &#xC790;&#xB3D9;&#xC73C;&#xB85C;
        &#xD65C;&#xC131;&#xD654; &#xB418;&#xACE0; &#xC140; &#xB192;&#xC774;&#xC5D0;
        &#xB300;&#xD55C; &#xC560;&#xB2C8;&#xBA54;&#xC774;&#xC158;&#xC774; &#xD544;&#xC694;&#xD55C;
        &#xACBD;&#xC6B0; &#xC720;&#xC6A9;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
  </tbody>
</table>