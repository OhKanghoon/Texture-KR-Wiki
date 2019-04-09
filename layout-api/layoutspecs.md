# LayoutSpecs

## 1. ASWrapperLayoutSpec

ASLayoutElement를 Wrapping하며,  ASLayoutSpec중 가장 기본적이고 간단한 Subclass 입니다.

상위 레이아웃에서 정의된 constrainedSize\(ASSizeRange\)를 기반으로 Wrapping된 사이즈를 계산합니다. 

따라서 기본적인 **단일 Layout을 반환**하는데 사용하는것이 **이상적**입니다. 

단, layout에 대한 postion\(CGPoint\) 값이 적용되어야할 경우 **ASAbsoluteLayoutSpec**사용해야합니다. 



### 사용법 

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    return ASWrapperLayoutSpec.init(layoutElement: childNode1)
}
```

부모에서 전달된 **constrainedSize**가 375pt x 812pt 일 때 **calculatedLayoutThatFits**를 통해서 사이즈를 계산하면 childNode를 wrapping한 layout size가 부모의 사이즈와 동일하게 계산되는 것을 확인 하 실 수가 있습니다. 

```bash
po ASWrapperLayoutSpec.init(layoutElement: childNode1).calculateLayoutThatFits(constrainedSize)
<ASLayout: 0x600002137700; size = {375, 812}>
```

_Wrapping되는 child element에 preferredSize를 적용하더라도 의미가 없습니다._ 

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    childNode1.style.preferredSize = .init(width: 100.0, height:100.0) // 의미없음 
    return ASWrapperLayoutSpec.init(layoutElement: childNode1)
}
```

단일 노드 뿐만 아니라 Array형태로 elements를 받아서 처리할 수도 있습니다. 

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    return ASWrapperLayoutSpec.init(layoutElements: [childNode1, childNode2])
}
```



### 요점 정리 

* 단일 노드 반환에 이상적
* position이 필요한 경우 ASAbsoluteLayoutSpec사용 할 것

## 2. ASInsetLayoutSpec

child element에 inset값을 적용 시켜주는 LayoutSpec입니다. 

```swift
ASInsetLayoutSpec.init(insets: UIEdgeInsets, child: ASLayoutElement)
```

ASInsetLayoutSpec은 constrainedSize.max size 값을 자식에게 전달해주며 전달된 값을 정의된 insets값에 따라 자식의 margin을 더해줍니다. 

### 사용법

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    let insets: UIEdgeInsets = .init(top: 50.0, left: 50.0, bottom: 50.0, right: 50.0)
    return ASInsetLayoutSpec.init(insets: insets, child: childNode1)
}
```

insets의  top, left, bottom, right에 inifinity를 지정하게 되면 자식노드의 instrinsic size로 결정됩니다. 

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    let insets: UIEdgeInsets = .init(top: .infinity, left: 50.0, bottom: .infinity, right: 50.0)
    childNode1.style.height = .init(unit: .points, value: 100.0)
    let insetLayout = ASInsetLayoutSpec.init(insets: insets, child: childNode1)
    
    // ...
}
```

![childNode&#xC758; height&#xB97C; &#xBA85;&#xC2DC;&#xC801;&#xC73C;&#xB85C; 100.0pt&#xB85C; &#xC815;&#xC758; ](../.gitbook/assets/image%20%2813%29.png)

![childNode&#xC758; &#xBA85;&#xC2DC;&#xC801;&#xC778; &#xC0AC;&#xC774;&#xC988;&#xB97C; &#xC54C; &#xC218; &#xC5C6;&#xC74C; \(unknown height\) ](../.gitbook/assets/image%20%283%29.png)

즉, 다시 말하자면 child element의 크기에 따라 ASInsetLayoutSpec의 크기가 결정되기 때문에 ASInsetLayoutSpec가 사이즈를 가지기 위해서 본질적인 사이즈 또는 계산에 따른 사이즈가 설정 되어야합니다. 

### 요점 정리 

* insets값은 child element의 margin값을 정의
* ASInsetLayoutSpec가 사이즈를 가지기 위해서 본질적인 사이즈 또는 계산에 따른 사이즈가 설정 되어야합니다. 

## 3. ASStackLayoutSpec

// 👷‍♀️ 공사중 👷

## 4. ASOverlayLayoutSpec

명칭 그대로 Overlay 해주는 LayoutSpec으로써, 특정 Overlay 대상 노드를 특정 child 노드위에 Overlay시키는 LayoutSpec입니다. 

```swift
ASOverlayLayoutSpec.init(child: ASLayoutElement, overlay: ASLayoutElement)
```

최상단에 **overlay되는 대상은 overlay:** 에서 매개변수로 받으며 child: 에서 **overlay되는 대상을 자식으로 가지는 layout element**를 받습니다. 



### 사용법

![](../.gitbook/assets/image%20%2815%29.png)

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
   let overlayLayout = ASOverlayLayoutSpec.init(child: blueNode, overlay: redNode)
   
   // ... 
}
```



### 요점 정리

*  크기에 대해서 임의로 조절하기 위해선 child에 해당하는 layout element는 반드시 본질적인 사이즈 또는 계산에 따른 사이즈가 설정 되어야합니다. 
* overlay에 해당하는 layout element는 child에서 계산된 constrainedSize값을 받아서 처리합니다. _\(즉, blueNode의 사이즈가 줄어들면 redNode의 사이즈도 같이 줄어들게 됩니다. \)_



## 5. ASBackgroundLayoutSpec

Background를 정의해주는 LayoutSpec입니다.

```swift
ASBackgroundLayoutSpec.init(child: ASLayoutElement, background: ASLayoutElement)
```

ASBackgroundLayoutSpec은 ASOverlayLayoutSpec과 비슷해보지만 큰 차이점이라면 background에 해당하는 layout element가 child에 해당하는 layout element의 사이즈에 영향을 받습니다. 

즉, background의 layout element의 사이즈는 child의 layout elements 사이즈에 의해 결정됩니다. 

따라서, child에서 계산된 constrainedSize가 background layout element로 전달 되기 때문에, 크기에 대해서 임의로 조절하기 위해선 child에 해당하는 layout element는 반드시 본질적인 사이즈 또는 계산에 따른 사이즈가 설정 되어야합니다. 



### 사용법 

![](../.gitbook/assets/image%20%2815%29.png)

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
   let layout = ASBackgroundLayoutSpec.init(child: redNode, background: blueNode)
   
   // ... 
}
```



### 요점 정리

*  크기에 대해서 임의로 조절하기 위해선 child에 해당하는 layout element는 반드시 본질적인 사이즈 또는 계산에 따른 사이즈가 설정 되어야합니다. 
* background에 해당하는 layout element는 child에서 계산된 constrainedSize값을 받아서 처리합니다. _\(즉, redNode의 사이즈가 줄어들면 blueNode의 사이즈도 같이 줄어들게 됩니다. \)_



## 6. ASAbsoluteLayoutSpec

flexible하지 않으며 정의된 사이즈형태로 유지되며 layoutPosition에 따라서 움직임이 가능한 LayoutSpec입니다. 

제공되는 API는 기본형태와 sizing option 형태 두가지로 나눠집니다. 

```swift
ASAbsoluteLayoutSpec.init(children: [ASLayoutElement]) // default
ASAbsoluteLayoutSpec.init(sizing: ASAbsoluteLayoutSpecSizing, children: [ASLayoutElement])
```

여기서 눈여겨볼 점은 sizing options입니다.

| OptionType | Description |
| :--- | :--- |
| .default | 기본값으로써 가능한 최대크기로 랜더링을 합니다. |
| .sizeToFit | 모든 children의 frame 결합체에 대해서 사이즈를 최소화된 크기로 계산을 합니다.  |

### 사용법

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    // layoutPosition 및 size를 설정합니다. 
    childNode1.style.layoutPosition = .init(x: 100.0, y: 100.0)
    childNode1.style.preferredSize = .init(width: 100.0, height: 100.0)   
    return ASAbsoluteLayoutSpec.init(sizing: .default, children: [childNode1])
}
```

layoutPosition이나 size는 ASAbsoluteLayoutSpec이 아닌 children에 해당하는 layout elements 각각에 대해서 설정되어야합니다. 



### 요점정리 

* position및 size는 각 child element에서 정의할 것
* 상황에 따라 sizing option 적절히 활용할 것

## 7. ASCenterLayoutSpec

특정 layout element를 가운데로 정렬하며 해당하는 layout element에 **constraintedSize.max**값을 전달해서 size를 계산합니다. 

![](../.gitbook/assets/image%20%2817%29.png)

```swift
ASCenterLayoutSpec.init(centeringOptions: ..., 
                        sizingOption: ...,
                        child: ...)
                        
ASCenterLayoutSpec.init(horizontalPosition: ...,
                        verticalPosition: ...,
                        sizingOption: ...,
                        child: ...)
```

#### 

#### centerOptions \(ASCenterLayoutSpecCenteringOptions\)

center처리에 대한 option값을 의미하며, **X, Y, XY, None** 총 4가지로 나눠집니다. \(default: none\)



#### sizingOptions \(ASCenterLayoutSpecSizingOptions\)

center 처리 되는 layout element의 size처리에 대한 옵션을 의미합니다.

| Option name | description |
| :--- | :--- |
| default | 기본값으로 크기를 최대한으로 지정합니다. |
| minimumX | X축을 기점으로 사이즈를 최소한으로 지정합니다. |
| minimumY | Y축을 기점으로 사이즈를 최소한으로 지정합니다.  |
| minimumXY | XY축을 기점으로 사이즈를 최소한으로 지정합니다.  |

#### 

#### child \(ASLayoutElement\)

중앙으로 정렬되는 대상을 의미합니다. 



#### horizontalPosition & verticalPosition \(ASRelativeLayoutSpecPostion\)

horizontal 및 vertical 에 대한 정렬기준옵션입니다. **none, start, center, end** 4가지로 나눠지며, ASRelativeLayoutSpec에서 자세히 다뤄집니다. \(기본값: none\)



### 사용법

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    let centerLayout = ASCenterLayoutSpec(centeringOptions: .XY,
                                          sizingOptions: .minimumXY
                                          child: childNode1)
                                          
    let centerLayout2 = ASCenterLayoutSpec(horizontalPosition: .start,
                                           verticalPosition: .end,
                                           sizingOption: [], // 기본
                                           child: childNode2)
                                              
    // ...
}
```



### 요점정리

* 요구스펙에 맞게 sizingOption 및 centeringOption 또는 horizontal/vertical position을 지정해서 사용합니다.
* 기본값으로 지시 **빈 Array \[ \]** 를 사용합니다.   

## 8. ASRelativeLayoutSpec

// 👷‍♀️ 공사중 👷







