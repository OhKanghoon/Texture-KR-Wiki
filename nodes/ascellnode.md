# ASCellNode

`ASCellNode` 는 Texture 에서 쓰이는 Cell 다.   
UIKit 의 다양한 Cell \(UITableViewCell, UICollectionViewCell\) 과 다르게 `ASCellNode` 는 `ASTableNode`, `ASCollectionNode`, `ASPagerNode` 에서 공통적으로 사용할 수 있다.

### 구현 방법 3가지

#### Subclassing

ASCellNode 를 subclassing 하는 건 ASDisplayNode 를 subclassing 하는 것과 유사합니다.

이 것들을 따르며 작성하면 됩니다:

* init : Thread safe 한 생성자
* layoutSpecThatFits : cell 을 정의하는 layout spec 을 반환한다
* didLoad : 메인 쓰레드에서 호출된다. gesture recognizer 추가하기에 적절
* layout : 메인 쓰레드에서 호출된다. super.layout\(\) 이후에 레이아웃이 완료되고, 여기서 필요한 추가 수정을 할 수 있다.

#### ASViewController 를 사용한 Initializing

ViewController 의 view 를 cell 로 사용할 수 있다.  
예를 들면 `ASTableNode` 를 사용하고 있는 ViewController 를 `ASPagerNode` 의 페이지로 사용하려면, `ASCellNode(viewControllerBlock:)` 를 사용하면 된다.

```swift
func pagerNode(_ pagerNode: ASPagerNode, nodeAt index: Int) -> ASCellNode {
    let animals = allAnimals[index]
    
    let node = ASCellNode(viewControllerBlock: { () -> UIViewController in
        return AnimalTableNodeController(animals: animals)
    }, didLoad: nil)
    
    node.style.preferredSize = pagerNode.bounds.size
    
    return node
}
```

그리고 이건 어떤 스크롤 가능한 컨테이너 노드와 `UIViewController` 서브 클래스에도 적용된다.

#### UIView 혹은 CALayer 를 사용한 initializing

이미 `UIView` 혹은 `CALayer` 서브 클래스를 가지고 있다면, 바로 Cell 로 사용할 수 있다.

```swift
func pagerNode(_ pagerNode: ASPagerNode, nodeAt index: Int) -> ASCellNode {
    let animal = animals[index]
    
    let node = ASCellNode { () -> UIView in
        return SomeAnimalView(animal: animal)
    }

    node.style.preferredSize = pagerNode.bounds.size
    
    return node
}
```

보다시피, 위의 viewController 를 사용하는 것과 같은 방식이다.  
`UIView` 서브 클래스를 `ASCellNode` 로 변환하면 비동기적으로 뷰를 보여주는 이점을 얻을 수 있다.

### Placeholder 보여주지 않기

일반적으로 Cell 이 화면에 도달하기 전에 Display Pass 를 완료하지 못하면 컨텐츠를 다 그리기 전까지 placeholder 를 보여준다.

Placeholder 를 원하지 않는다면 `ASCellNode` 의 `neverShowPlaceholders` 프로퍼티를 `true` 로 주면 된다.

```swift
node.neverShowPlaceholders = true
```

이 프로퍼티를 true 주면, Main thread 는 Cell 의  Display 가 완료되기 전까지 block 된다.  
이것은 UIKit 과 유사하지만, Texture 는 Scroll 을 훨씬 빠르게 만들어 준다.

> 이 옵션을 사용한다고 해서 Texture 의 성능상 이점이 모두 사라지는 것은 아니다. 일반적으로 Cell  preloading 중이기 때문에 block 되는 시간은 매우 짧다. `rangeTuningParameters` 가 0으로 설정되어 있더라 이 옵션은 UIKit 보다 월등하다. Main thread 가 대기 중일 때 , SubNode 의 display 가 동시에 실행된다.

### UITableViewCell 의 특정 프로퍼티

`UITableViewCell` 은 `selectionStyle`, `accessoryType`, `seperatorInset` 등의 프로퍼티를 가지고 있다.  
이 경우 `ASCellNode` 또한 동일한 프로퍼티들을 가지고 있다.

> UIKit 의 `UITableViewCell` 은 `ASCellNode` 를 subview 로 포함하고 있다.  
> `ASLayoutSpec` 을 정의하는 방법 따라 레이아웃이 `UITableViewCell.accessoryView` 와 겹쳐서 보이지 않을 수 있다. 레이아웃이 `UITableViewCell` 의 특정 프로퍼티 겹치지 않도록 해야한다.

