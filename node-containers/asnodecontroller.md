# ASNodeController (Beta)

`이 기능을 사용하려면 'ASNodeController+Beta.h'를 import해야합니다.`

Texture팀은 ASNodeController를 확장하기 위한 흥미진진한 아이디어들를 가지고 있습니다. 노드 컨트롤러의 미래를 구성하는 데 참여하려면 [여기](https://github.com/facebookarchive/AsyncDisplayKit/issues/2964)로 오세요.

현재 ASNodeController는 심플하지만, 파워풀한 클래스입니다.

## 예제

[예제 프로젝트](https://github.com/facebookarchive/AsyncDisplayKit/pull/2945) 초기 PR 수정사항 내에 첨부된 [ASDKgram](https://github.com/texturegroup/texture/tree/master/examples/ASDKgram) 프로젝트는  `ASNodeController`를 사용했습니다. `PhotoCellNodeController`는 사진이 preload 범위에 들어가면 사진 피드에서 사진에 대한 설명 데이터를 가져오는 작업을 관리하는데 사용됩니다. 이 노드 컨트롤러를 사용하여  `PhotoCellNode` "뷰" 클래스에 있었던 preload 로직을 분리할 수 ​​있습니다.

우리는 ASDKgram이 `ASNodeController`를 사용하도록 변환하기위해 먼저 `PhotoCellNodeController` 클래스를 작성했습니다.

이 노드 컨트롤러는 `ASNodeController`의  `-loadNode`메서드를 재정의하여 `PhotoCellNode`에 필요한 항목을 만듭니다 . 이 메서드에서는 super를 호출 할 필요가 없습니다.

이 노드 컨트롤러는 노드의 인터페이스 상태를 관찰하여 PhotoCellNode가 preload 상태 (사진 셀이 곧 화면에서 스크롤 될 가능성이 높음을 나타냄)로 들어갈 때 사진의 설명 및 피드 모델 데이터를 지능적으로 미리 로드합니다 .

 기존에 "뷰"(`PhotoCellNode` 클래스)에 위치하던 로직들을 제거 할 수 있으므로 보다 간결하고 MVC 친화적인 뷰 클래스가 될 수 있습니다.

```swift
final class PhotoCellNodeController: ASNodeController<PhotoCellNode> {
    
    override func loadNode() {
        self.node = PhotoCellNode(photoObject: photoModel)
    }
    
    override func didEnterPreloadState() {
        super.didEnterPreloadState()
        
        let commentFeedModel = photoModel.commentFeed
        commentFeedModel.refreshFeedWithCompletionBlock { [weak self] newComments in
            // load comments for photo
            if commentFeedModel.numberOfItemsInFeed > 0 {
                self?.node.photoCommentsNode.updateWithCommentFeedModel(commentFeedModel)
                self?.node.setNeedsLayout()
            }
        }
    }
}
```

다음으로 `PhotoFeedNodeController` 노드 컨트롤러를 저장하고 초기화 메소드에서 이를 인스턴스화 할 수 있도록 변경할 수 있는 배열을 추가합니다. 

```swift
final class PhotoFeedNodeController: PhotoFeedBaseController {
    
    let photoFeed: PhotoFeedModel
    let tableNode: ASTableNode = ASTableNode()
    var photoCellNodeControllers: [PhotoCellNodeController] = []
    
    init() {
        super.init(node: tableNode)
        
        navigationItem.title = "Texture"
        navigationController.isNavigationBarHidden = true
        
        tableNode.dataSource = self
        tableNode.delegate = self
    }
}
```

 노드 컨트롤러를 사용하도록 하기 위해, 테이블 ​​행 삽입 로직을 수정하여 (`PhotoCellNode`를 바로 생성하지 말고)  `PhotoCellNodeController`를 만들고 노드 컨트롤러 배열에 추가합니다.  

```swift
func insertNewRowsInTableNode(newPhotos: [PhotoFeedModel]) {
    let section = 0
    var indexPaths: [NSIndexPath] = []
    let newTotalNumberOfPhotos = photoFeed.numberOfItemsInFeed
    let firstRow = newTotalNumberOfPhotos - newPhotos.count
    
    (firstRow..<newTotalNumberOfPhotos).forEach { row in
        // create photoCellNodeControllers for the new photos
        let cellController = PhotoCellNodeController()
        cellController.photoModel = photoFeed[row]
        photoCellNodeControllers.append(cellController)
        
        // include this index path in the insert rows call for the table
        let path = IndexPath(row: row, section: section)
        indexPaths.append(path)
    }
    
    tableNode.insertRows(at: indexPaths, with: .none)
}
```

cellNode가 아닌 노드 컨트롤러를 반환하도록 table data source 메서드를 수정하는것을 잊지마세요.

```swift
func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock {
    let cellController = photoCellNodeControllers[indexPath.row]
    // this will be executed on a background thread - important to make sure it's thread safe
    let cellNodeBlock = { () -> ASCellNode in
        let cellNode = cellController.node
        return cellNode
    }
    
    return cellNodeBlock
}

```
