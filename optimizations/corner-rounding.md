# Corner Rounding

CALayer에서 기본적으로 UI를 원으로 또는 모퉁이를 둥글게 만들 때 주로  cornerRadius 을 사용합니다. 

하지만, CALayer의 cornerRadius를 사용하면 스크롤하는 동안 모든 프레임에서 Clipping 을 수행하기 때문에 오프 스크린 랜더링이 트리거가 되기 때문에 비용이 커서 오버헤드나 프레임 드랍과 같은 현상이 발생하기도 합니다. 

_구형 디바이스 \(iPhone 4, 4S, 5/5C\) 와 같은 기종에서는 성능이 크게 저하되지만 상위 디바이스의 경우 위의 문제점들에 대한 헤드룸이 줄어들어 영향을 직접적으로 볼 수 없을 뿐더러 프레임 드롭은 거의 발생하진 않습니다._  

## CornerRoundingType 과 종류   

Texture는 CornerRounding 에 대해서 좀 더 효율적인 방식을 고려해서 3가지 cornerRoundingType 을 제시합니다. 

* defaultSlowCALayer \(ASCornerRoundningTypeDefaultSlowCALayer\)
* precomposited \(ASCornerRoundingTypePrecomposited\)
* clipping \(ASCornerRoundingTypeClipping\) 

기본값은 defaultSlowCALayer 를 사용합니다.



#### DefaultSlowCALayer 

기본적인 CALayer에서 제공하는 cornerRounding 을 사용하여 처리합니다. \(cornerRoundingType의 default 값입니다. \) 

```swift
let photoImageNode = ASImageNode()
photoImageNode.cornerRoundingType = .defaultSlowCALayer
photoImageNode.cornerRadius = 20.0
```

#### 

#### Precomposited

사전혼합된 CornerRounding 기법으로, 모서리가 이미지의 일부가 되어 하나의 단일 CALayer로 **혼합**됩니다. 

**사전 합성 된 불투명한 모서리**를 사용하는 것이 좋으며, 만약 모서리가 둥근 이미지의 Position이 유동적이라면 배경은 투명도가 없는 단색인 것이 좋습니다. 

사전에 합성된 불투명한 모서리가 없는 경우에는 다른 대안으로 **사전 합성 된 투명도가 있는 모서리가 있는 Bézier Path** 를 사용하는 방법입니다. 이 방법은 앞서 말한 사전 합성된 불투명 모서리 사용하는거에 비해 유연성은 높지만 알파 블랜딩 하는데 비용이 추가되며 메모리 영향이 약 25% 증가합니다. 

제한사항:

* 모서리는 반드시 하나의 Node에만 닿고 이 외의 어떠한 SubNode 와 교차해서는 안됩니다.
* 레스터 사용은 권장하지 않습니다. \(shouldRasterizeDescendants\), 

_주의 해야할 점은 CALayer의 shouldRasterize는 Texture Node의 shouldRasterizeDescendents 과는 관련 없습니다.  shouldRasterizeDescendents 옵션을 사용할 경의 하위 노드에 대해서 레이어가 만들어지지 않습니다._ 

 

```swift
let photoImageNode = ASImageNode()
photoImageNode.cornerRoundingType = .precomposited
photoImageNode.cornerRadius = 20.0
```

#### 

#### Clipping

![Appleguy &#xC774;&#xBBF8;&#xC9C0; &#xC608;&#xC2DC;](../.gitbook/assets/image%20%284%29.png)

Corner값에 대해서 반올림이 필요한 부분에 대해서 각 4개의 모서리 영역에 불투명한 모서리를 배치하는 방식입니다. 

이 방법은 유연하고 좋은 성능을 제공해줍니다. 단, 각 모서리에 배치된 불투명한 모서리에 대해서만 약간의 CPU 오버헤드가 있을 수는 있습니다.  

* 모서리가 하나 이상의 Node에 닿거나 SubNode와 교차하는 상황에서도 모서리가 둥글게 됩니다.
* 배경사진 위에서 고정된 둥근 모서리를 적용할 때 유용합니다. 



```swift
let photoImageNode = ASImageNode()
photoImageNode.cornerRoundingType = .clipping
photoImageNode.backgroundColor = UIColor.white
photoImageNode.cornerRadius = 20.0
```

## CornerRoundingType 사용 결정 흐름도 

![&#xD30C;&#xB780;&#xC0C9;&#xC73C;&#xB85C; &#xAC15;&#xC870; &#xD45C;&#xC2DC;&#xB41C; &#xBAA8;&#xC11C;&#xB9AC; &#xC544;&#xB798;&#xC758; &#xC6C0;&#xC9C1;&#xC784;&#xACFC; &#xC8FC;&#xD669;&#xC0C9;&#xC73C;&#xB85C; &#xAC15;&#xC870; &#xD45C;&#xC2DC;&#xB41C; &#xBAA8;&#xC11C;&#xB9AC;&#xB97C; &#xD1B5;&#xACFC;&#xD558;&#xB294; &#xB3D9;&#xC791;&#xC744; &#xBCF4;&#xC5EC;&#xC90D;&#xB2C8;&#xB2E4;.](../.gitbook/assets/image%20%289%29.png)

Corner Rounding할 때 전략을 세울 때 고려해야하는 사항은 다음과 같습니다.

1. 모퉁이 밑으로 움직임이 있는가? \(주황색영역\)
2. 모퉁이 구석을 통과하는 움직임이 있는가? \(파란색영역\) 
3. 네 모서리가 같은 대상의 Node를 대상으로 하며 다른 Node가 모서리에 겹쳐지지 않는가?



#### 1. 모퉁이 밑으로 움직임이 있는가? 

모퉁이 밑으로의 움직임이란 둥근 모서리를 가진 UI가 뒤에 있는 UI 위로 움직이면 뒤에 있는 UI가 모서리 아래로 이동합니다. 즉, 파란색 영역을 통과함을 의미합니다. 



#### 2. 모퉁이 구석을 통과하는 움직임이 있는가? 

모퉁이 구석을 통과하는 움직임이란 둥근 모서리가 있는 스크롤 뷰 위에서  사진 확대 및 축소 행위를 하였을 때 사진이 스크롤 뷰의 모퉁이로 이동하는 행위를 예로 들 수가 있습니다. 즉 사진이 주황색 영역을 통과한다는 의미입니다. 



#### 3. 네 모서리가 같은 대상의 Node를 대상으로 하며 다른 Node가 모서리에 겹쳐지지 않는가? 

![](../.gitbook/assets/image%20%2811%29.png)

아주 쉽게 위의 그림으로 설명할 수 있습니다. 1, 2사분면의 UI는 겹쳐지는 UI가 없지만, 3, 4 사분면은 겹쳐지는 UI가 있는 걸을 확인 하 실수가 있습니다. 



따라서 위의 3가지 조건을 판단하여 아래의 흐름도를 따라서 좋은 결정을 하실수가 있습니다. 



![TODO: &#xD55C;&#xAE00; &#xD750;&#xB984;&#xB3C4;&#xB85C; &#xADF8;&#xB9AC;&#xAE30;](../.gitbook/assets/image%20%286%29.png)



