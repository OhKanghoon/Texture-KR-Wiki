# Layout API Sizing

Texture Layout API를 사용하는데 있어서 복합 차원 유형을 이해하는데 가장 쉬운 방법으로는 서로 관련된 모든 단위를 파악하는 것입니다. 

![](../.gitbook/assets/image%20%2821%29.png)

## ASDimension

```swift
let dimension = ASDimension(unit: ASDimensionUnit, value: CGFloat)
```

`ASDimension`을 생성하는데 있어서 가장 기본적인 unit는 크게 **point**와 **백분율** 값으로 나눠지며 value값은 CGFloat를 받습니다. 

```swift
let percentage1 = ASDimensionMake("50%")
let percentage2 = ASDimension(unit: .fraction, value: 0.5)

let point1 = ASDimensionMake("100pt")
let point2 = ASDimension(unit: .points, value: 100.0)
```



### 사용법 

\`\`[`ASStackLayoutSpec`](https://texture-kr.gitbook.io/wiki/layout-api/layoutspecs#3-asstacklayoutspec)에서 각 child element layout의 **flexBasis**에 대한 속성을 정의할 때 유용하기 때문에 예시를 준비했습니다.

![](../.gitbook/assets/image%20%2824%29.png)

위의 그림과 같이 place에 대한 정보를 horizontal stack으로 배치하되 

category를 좌측에 40% 비율로 배치하고 detail information을 우측에 60%비율로 배치합니다. 

위와 같이 구성한다 하면 아래의 코드와 같이 나타낼 수가 있습니다.

```swift
self.categoryLayout.style.flexBasis = ASDimension(unit: .fraction, value: 0.4)
self.detailInfoLayout.style.flexBasis = ASDimension(unit: .fraction, value: 0.6)

let stackLayout = ASStackLayoutSpec()
stackLayout.direction = .horizontal
stackLayout.children = [self.categoryLayout, self.detailInfoLayout]
```

## ASLayoutSize

```swift
ASLayoutSize.init(width: ASDimension, height: ASDimension)
```

`ASLayoutSize`는 `CGSize`와 유사하지만 **width와 height** 에 대해서 `CGFloat`로 point로 처리하는 방법과 **백분율** 값으로 나타내는 방식이 있습니다.

```swift
let size1 = ASLayoutSize.init(width: .init(unit: .points, value: 100.0),
                              height: .init(unit: .points, value: 100.0))

let size2 = ASLayoutSize()
size2.width = .init(unit: .points, value: 100.0)
size2.height = .init(unit: .fraction, value: 0.5) // 50%
```

### 사용법

```swift
let imageNode = ASNetworkImageNode()

let imageLayoutSize = ASLayoutSize()
imageLayoutSize.width = .init(unit: .points, value: 100.0)
imageLayoutSize.height = .init(unit: .fraction, value: 0.5) // 50%

imageNode.style.preferredLayoutSize = imageLayoutSize
```

만약 **Relative한 사이즈가 필요없이 단순히 고정된 사이즈**값이 필요할 경우에는 `preferredSize`, `minSize`, `maxSize`를 이용하는 방법이 있습니다. 

```swift
let imageNode = ASNetworkImageNode()
imageNode.style.preferredSize = .init(width: 500.0, height: 500.0)

let imageNode2 = ASNetworkImageNode()
imageNode2.style.minSize = .init(width: 100.0, height: 100.0)
imageNode2.style.maxSize = .init(width: 500.0, height: 500.0)
```



## ASSizeRange

`UIKit`는 **minimum** 및 **maximum** 묶음 구조 대한 `CGSize`  값을 제공해주지 않습니다. 

따라서 `ASSizeRange`목적은 **최소, 최대 사이즈를 묶어서** 제공해주는 역할을 합니다. 

```swift
let sizeRange = ASSizeRange()
sizeRange.min = CGSize.init(width: 100.0, height: 100.0)
sizeRange.max = CGSize.init(width: 200.0, height: 100.0)

let sizeRange2 = ASSizeRange(min: CGSize.zero, 
                             max: CGSize.init(width: 10.0, height: 10.0)))
```

대표적으로 사용되는 사례로는 `layoutSpecThatFits:` 을 예로 들 수가 있습니다. 

```swift
func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec
```



