# Intelligent Preloading

비동기적으로 동시에 측정되고 렌더링되는 노드의 능은 강력하지만, Texture 에서 결정적으로 중요한 것은 Intelligent Preloading 이다.

노드 컨테이너 밖에서 노드를 사용하는 것은 이점이 거의 없다. 이는 노드가 현재 Interface State 에 대한 개념을 가지고 있기 때문이다.

InterfaceState 프로퍼티는 모든 컨테이너가 내부적으로 생성 및 유지되는 ASRangeController 에 의해 지속적으로 업데이트 된다.

컨테이너 밖에서 사용되는 노드는 Range Controller 에 의해 업데이트 되는 상태를 가지고 있지 않는다.  
이것은 종종 노드가 아무런 경고 없이 이미 화면에 나타났다는 것을 인지한 후에 렌더링될 때 깜빡인다.

## Interface State Range

노드를 스크롤 혹은 페이징 인터페이스 에 추가할 때 보통 다음 범위 중 하나에 있다. 이는 스크롤 뷰가 스크롤될 때 Interface State 가 이들을 통과할 때 업데이트 된다는걸 의미한다.

![A node will be in one of following ranges:](../.gitbook/assets/image%20%285%29.png)

| **Interface State** | **Description** |
| :--- | :--- |
| **Preload** | visible 과 가장 거리가 멀다. API 나 Local Disk 같은 외부 소스에서 컨텐츠를 가져오는 곳이다. |
| **Display** | Text Rasterization 과 이미지 디코딩이 이루어진다. |
| **Visible** | 노드가 적어도 한 픽셀씩 화면에 표시된다. |

## ASRangeTuningParameters



