---
description: 싱글 및 리스트형태의 스크린을 예시로 하였습니다.
---

# Quick Example

## 1. Single Screen Example

![Orientation&#xBCC0;&#xD654;&#xC5D0; &#xC720;&#xB3D9;&#xC801;&#xC778; Layout Example](../.gitbook/assets/2019-04-04-2.18.48.png)

![LayoutSpec &#xAD6C;&#xC870; ](../.gitbook/assets/2019-04-04-2.26.37.png)

### 1. ViewController에서 Component를 정의 및 사용하는 경우  

ViewController의 부모노드에 들어갈 자식 노드를 정의해서 사용하는 방법입니다. 비즈니스 로직이나 명세에 따라서 복잡도가 증가할수록 Massive 해질 수도 있습니다. 

```swift
class TestNodeController: ASViewController<ASDisplayNode> {

    let imageNode: ASImageNode = {
        let node = ASImageNode()
        node.image = UIImage(named: "image")
        node.borderColor = UIColor.gray.cgColor
        node.borderWidth = 1.0
        node.cornerRadius = 15.0
        node.contentMode = .scaleAspectFit
        return node
    }()
    
    let titleNode: ASTextNode = {
        let node = ASTextNode()
        let paragraphStyle = NSMutableParagraphStyle()
        paragraphStyle.alignment = .center
        node.attributedText =
            NSAttributedString(string: "Welcome to Texture-KR",
                               attributes: [.font: UIFont.boldSystemFont(ofSize: 15.0),
                                            .foregroundColor: UIColor.gray,
                                            .paragraphStyle: paragraphStyle])
        return node
    }()
    
    init() {
        super.init(node: ASDisplayNode.init())
        self.node.backgroundColor = .white
        self.node.automaticallyManagesSubnodes = true
        self.node.automaticallyRelayoutOnSafeAreaChanges = true
        self.node.layoutSpecBlock = { [weak self] (node, constraintedSize) -> ASLayoutSpec in
            return self?.layoutSpecThatFits(constraintedSize) ?? ASLayoutSpec()
        }
    }
    
    private func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec {
        let imageRatioLayout = ASRatioLayoutSpec(ratio: 1.0, child: imageNode)
        imageRatioLayout.style.flexShrink = 1.0
        let containerLayout = ASStackLayoutSpec(direction: .vertical,
                                            spacing: 10.0,
                                            justifyContent: .center,
                                            alignItems: .center,
                                            children: [imageRatioLayout, titleNode])
        
        var containerInsets: UIEdgeInsets = self.node.safeAreaInsets
        containerInsets.left += 15.0
        containerInsets.right += 15.0
        containerInsets.top = containerInsets.bottom
        return ASInsetLayoutSpec(insets: containerInsets, child: containerLayout)
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}
```

### 2. Container에 들어갈 노드를 Subclass 화 시켜서 넣어주기. \(Avoid Massive ViewController\) 

[앞서 설명했듯이](https://texture-kr.gitbook.io/wiki/newbie-guide/node#1-texture-node-container-viewcontroller) ASViewController는 제네릭형태로 ASDisplayNode의 모든 Subclass를 받아서 사용할 수 있습니다.

따라서 아래의 코드와 같이 ViewController에 들어간 화면 구성요소들을 모듈화 시켜서 **ViewController가 Massive**해지는 것을 피할 수도 있습니다. 

#### TestNode.swift

```swift
class TestNode: ASDisplayNode {
    
    let imageNode: ASImageNode = {
        let node = ASImageNode()
        node.image = UIImage(named: "image")
        node.borderColor = UIColor.gray.cgColor
        node.borderWidth = 1.0
        node.cornerRadius = 15.0
        node.contentMode = .scaleAspectFit
        return node
    }()
    
    let titleNode: ASTextNode = {
        let node = ASTextNode()
        let paragraphStyle = NSMutableParagraphStyle()
        paragraphStyle.alignment = .center
        node.attributedText =
            NSAttributedString(string: "Welcome to Texture-KR",
                               attributes: [.font: UIFont.boldSystemFont(ofSize: 15.0),
                                            .foregroundColor: UIColor.gray,
                                            .paragraphStyle: paragraphStyle])
        return node
    }()
    
    override init() {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.automaticallyRelayoutOnSafeAreaChanges = true
    }
    
    override func layoutSpecThatFits(_ constraintedSize: ASSizeRange) -> ASLayoutSpec {
        let imageRatioLayout = ASRatioLayoutSpec(ratio: 1.0, child: imageNode)
        imageRatioLayout.style.flexShrink = 1.0
        let containerLayout = ASStackLayoutSpec(direction: .vertical,
                                 spacing: 10.0,
                                 justifyContent: .center,
                                 alignItems: .center,
                                 children: [imageRatioLayout, titleNode])
        
        var containerInsets: UIEdgeInsets = self.safeAreaInsets
        containerInsets.left += 15.0
        containerInsets.right += 15.0
        containerInsets.top = containerInsets.bottom
        
        return ASInsetLayoutSpec(insets: containerInsets, child: containerLayout)
    }
}

```

#### TestNodeController.swift

```swift
class TestNodeController: ASViewController<TestNode> {

    init() {
        super.init(node: TestNode.init())
        self.node.backgroundColor = .white
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

```

## 2. List Screen Example 

[앞서 설명했듯이](https://texture-kr.gitbook.io/wiki/newbie-guide/node#1-texture-node-container-viewcontroller) ASViewController는 제네릭형태로 ASDisplayNode의 모든 Subclass를 받아서 사용할 수 있습니다. ASTableNode는 ASDisplayNode의 subclass이므로 ASViewController를 만들때 **ASVIewController&lt;ASTableNode&gt;** 를 상속받아 ViewController를 만듭니다. 



![List &#xD615;&#xD0DC;&#xC758; Screen &#xC608;&#xC2DC;](../.gitbook/assets/2019-04-04-3.21.13%20%281%29.png)

![LayoutSpec &#xAD6C;&#xC870;](../.gitbook/assets/2019-04-04-3.29.45%20%282%29.png)

![imageView&#xC640; textView&#xAC04;&#xC758; fraction &#xC815;&#xC758; &#xC608;&#xC2DC; ](../.gitbook/assets/2019-04-04-3.29.50.png)

#### TestCellNode.swift

```swift
class TestCellNode: ASCellNode {
    
    let imageNode: ASImageNode = {
        let node = ASImageNode()
        node.image = UIImage(named: "image")
        node.borderColor = UIColor.gray.cgColor
        node.borderWidth = 1.0
        node.cornerRadius = 15.0
        node.contentMode = .scaleAspectFit
        return node
    }()
    
    let titleNode: ASTextNode = {
        let node = ASTextNode()
        node.maximumNumberOfLines = 1
        return node
    }()
    
    init(item: String) {
        super.init()
        self.automaticallyManagesSubnodes = true
        self.selectionStyle = .none
        self.backgroundColor = .white
        self.titleNode.attributedText =
            NSAttributedString(string: item,
                               attributes: [.font: UIFont.boldSystemFont(ofSize: 15.0),
                                            .foregroundColor: UIColor.gray])
    }
    
    override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
        let imageRatioLayout = ASRatioLayoutSpec(ratio: 1.0, child: imageNode)
        imageRatioLayout.style.flexBasis =  ASDimension(unit: .fraction, value: 0.3)
        titleNode.style.flexBasis =  ASDimension(unit: .fraction, value: 0.7)
       
        let containerLayout = ASStackLayoutSpec(direction: .horizontal,
                                                spacing: 10.0,
                                                justifyContent: .start,
                                                alignItems: .stretch,
                                                children: [imageRatioLayout, titleNode])
        
        var containerInsets: UIEdgeInsets = .zero
        containerInsets.left = 15.0
        containerInsets.right = 15.0
        containerInsets.top = 15.0
        containerInsets.bottom = 15.0
        return ASInsetLayoutSpec(insets: containerInsets, child: containerLayout)
    }
}

```

#### TestNodeController.swift

```swift
class TestNodeController: ASViewController<ASTableNode> {
    
    var items: [String] = ["Welcome to Texture-KR",
                           "Welcome to Texture-KR",
                           "Welcome to Texture-KR, long test!!!!!!!!!!!!!!!!!!!!!!!!!!!!!"]

    init() {
        super.init(node: ASTableNode.init(style: .plain))
        self.node.backgroundColor = .white
        self.node.delegate = self
        self.node.dataSource = self
    }
    
    required init?(coder aDecoder: NSCoder) {
        fatalError("init(coder:) has not been implemented")
    }
}

extension TestNodeController: ASTableDelegate, ASTableDataSource {
    
    func numberOfSections(in tableNode: ASTableNode) -> Int {
        return 1
    }
    
    func tableNode(_ tableNode: ASTableNode, numberOfRowsInSection section: Int) -> Int {
        return items.count
    }
    
    func tableNode(_ tableNode: ASTableNode, nodeBlockForRowAt indexPath: IndexPath) -> ASCellNodeBlock {
        return {
            let item = self.items[indexPath.row]
            return TestCellNode(item: item)
        }
    }
}

```



