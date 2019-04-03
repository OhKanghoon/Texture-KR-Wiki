---
description: Texture에서 제공해주는 기본적인 디버깅 툴을 소개합니다.
---

# Debug Tool

## Hit Test Visualization

![](../.gitbook/assets/image%20%281%29.png)

개발자가 의도한 **Hit Test Slop 또는  targetAction**이 지정된 **터치영역**을 위의 사진과 같이 시뮬레이터 또는 디바이스에서 확인하실 수가 있습니다. 

* profileImage의 경우 hitTestSlop 값을 음수로 지정하여 확장 된것을 확인하실 수가 있습니다.
* username의 경우 별도의 hitTestSlop없이 calculated 된 layout size\(정사이즈\)로 터치영역이 잡힌 것을 확인 하실 수가 있습니다. 
* like count \(좋아요 수\) 의 경우 hitTestSlop을 음수가 아닌 양수로 지정하여서 실제 사이즈에 비해 터치영역이 줄어든 것을 확인 하실 수가 있습니다. 

_참고: hitTestSlop은 UIEdgeInsets 을 받습니다._ 

#### 사용법

didFinishLaunchingWithOptions method에서 ASControlNode enableHitTestDebug flag를 활성화 시켜주시면 됩니다. 

```swift
import AsyncDisplayKit

func application(_ application: UIApplication,
                 didFinishLaunchingWithOptions launchOptions: [UIApplication.LaunchOptionsKey: Any]?) -> Bool {
                 
    ASControlNode.enableHitTestDebug = true        
}
```

## Range Visualization

// 👷‍♀️ 공사중 👷

## Layout Debugging

// 👷‍♀️ 공사중 👷

