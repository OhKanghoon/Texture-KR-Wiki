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



