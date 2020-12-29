---
description: >-
  UIKit에서 제공해주는 UITableViewDataSourcePrefetching 및
  UICollectionViewDataSourcePrefetching 보다 훨씬 정교한 Intelligent Preloading 메커니즘을
  소개합니다.
---

# Intelligent Preloading

비동기적으로 동시에 측정되고 렌더링 되는 노드의 능력도 강력하지만, Texture 에서 결정적으로 중요한 것은 Intelligent Preloading 입니다.

노드 컨테이너 밖에서 노드를 사용하는 것은 이점이 거의 없습니다. 이는 노드가 현재 Interface State 에 대한 개념을 가지고 있기 때문입니다.

`InterfaceState` 프로퍼티는 모든 컨테이너가 내부적으로 생성 및 유지되는 `ASRangeController` 에 의해 지속적으로 업데이트 됩니다.

컨테이너 밖에서 사용되는 노드는 Range Controller 에 의해 업데이트 되는 상태를 가지고 있지 않습니다.  
이것은 종종 노드가 아무런 경고 없이 이미 화면에 나타났다는 것을 인지한 후에 렌더링될 때 깜빡입니다.

## Interface State Range

노드를 스크롤 혹은 페이징 인터페이스 에 추가할 때 보통 다음 범위 중 하나에 있습니다. 이는 스크롤 뷰가 스크롤될 때 Interface State 가 이들을 통과할 때 업데이트 된다는걸 의미합니다.

![A node will be in one of following ranges:](../.gitbook/assets/image%20%286%29%20%281%29.png)

| **Interface State** | **Description** |
| :--- | :--- |
| **Preload** | visible 과 가장 거리가 멉니다. API 나 Local Disk 같은 외부 소스에서 컨텐츠를 가져오는 곳입니다. |
| **Display** | Text Rasterization 과 이미지 디코딩이 이루어집니다. |
| **Visible** | 노드가 적어도 한 픽셀씩 화면에 표시됩니다. |

### ​

### 관련 영상

{% embed url="https://youtu.be/wrctPJxskhI" caption="" %}

## ASRangeTuningParameters

각 Range 의 size 는 "Screenful"로 측정합니다. default size 는 일반적으로 잘 작동하지만, 스크롤 노드에서 range type 에 대한 tuning parameters 를 설정하면 쉽게 조정할 수 있습니다.

![visualization of a scrolling collection](../.gitbook/assets/image%20%283%29.png)

위의 사진을 보면 사용자는 아래로 스크롤 하고 있습니다. 보다시피 선행 방향의 범위의 사이즈는 사용자가 이동 중인 컨텐츠\(후행방향\)보다 많이 큽니다. 사용자가 방향을 변경할 경우, 메모리 사용량을 최적으로 유지하기 위해 선행 과 후행이 동적으로 교환될 것입니다. 이렇게 하면 변화하는 사용자의 스크롤 방향에 반응할 걱정 없이 선행 및 후행 사이를 정할 수 있습니다.

`Intelligent preloading` 은 다차원에서도 작용니다.

샘플 코드는 [여기](https://texture-kr.gitbook.io/wiki/advanced-guide/debug-tool#range-visualization)를 참고하세요.

## Interface State Callbacks

사용자가 스크롤할 때 노드는 범위를 통과하여 이동하며, 데이터 로드, 렌더링 등을 통해 적절하게 대응합니다. 커스텀 노드는 해당 메서드를 구현하여 이 메커니즘을 쉽게 이용할 수 있습니다.

추가로 상태에 대한 아래의 override method 는 **메인스레드**에서 처리됩니다.

#### Visible Range

```swift
override func didEnterVisibleState() {
  super.didEnterVisibleState()
}

override func didExitVisibleState() {
  super.didExitVisibleState()
}
```

#### Display Range

```swift
override func didEnterDisplayState() {
  super.didEnterDisplayState()
}

override func didExitDisplayState() {
  super.didExitDisplayState()
}
```

#### **Preload Range**

```swift
override func didEnterPreloadState() {
  super.didEnterPreloadState()
}

override func didExitPreloadState() {
  super.didExitPreloadState()
}
```

