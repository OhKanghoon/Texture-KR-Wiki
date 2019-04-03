# ASVideoNode

* [http://texturegroup.org/docs/video-node.html](http://texturegroup.org/docs/video-node.html)

`ASVideoNode`는 편리하면서도 향상된 성능으로 앱에서 비디오를 디스플레이할 수 있도록 합니다.

> NOTE: `ASVideoNode`를 앱에서 사용하게 되면, `AVFoundation`를 링크해야 합니다. 왜냐하면 이면에서 `AVPlayerLayer` 와 `AVFoundation` 클래스들을 사용하고 있기 때문입니다.

## 기본 사용법

가장 쉽게 `ASVideoNode`를 사용하는 방법은 `AVAsset` 을 할당하는 것입니다.

```swift
let videoNode = ASVideoNode()

let asset = AVAsset(url: URL(string: "http://www.w3schools.com/html/mov_bbb.mp4")!)
videoNode.asset = asset
```

## 자동 재생, 자동 반복 재생, 무음

간단한 `BOOL` 값을 가지고 당신의 비디오 노드가 여러가지 이벤트에 반응할 수 있도록 설정할 수 있습니다.

만약 보여지는 영역에 진입하게 되었을 때 비디오를 자동으로 재생하게 하고 싶다면, `shouldAutoplay` 프로퍼티를 `YES` 로 설정하세요. `shouldAutoRepeat` 프로퍼티를 `YES` 로 설정하면 비디오가 무한으로 반복 재생될 것입니다. `muted` 를 `YES` 로 설정하면 비디오의 사운드를 Off 시키게 됩니다.

만약 비디오를 무음으로 한 번 자동 재생하게 하고 싶다면 아래와 같이 하면 됩니다.

```swift
videoNode.shouldAutoplay = true
videoNode.shouldAutorepeat = false
videoNode.muted = true
```

## Placeholder Image

비디오 노드는 `ASNetworkImageNode` 를 상속받고 있기 때문에 `URL` 프로퍼티를 사용해서 placeholder 이미지를 할당할 수 있습니다.

만약 이렇게 하지 않았다면, 당신의 비디오의 첫 번째 프레임이 자동으로 디코딩 되어 placeholder 이미지로 사용되게 될 것입니다.

![GIF Example](../.gitbook/assets/video.gif)

## ASVideoNode Delegate

당신의 비디오에서 일어나는 일들에 반응할 수 있도록 하기 위해 여러가지 델리게이트 메소드들이 제공됩니다. 예를 들어서 플레이어의 상태값 변화에 반응하고 싶다면 아래를 사용하세요.

```swift
videoNode(_:willChange:to:)
```

모든 것들을 파악하는 가장 쉬운 방법은 `ASVideoNode` 헤더 파일을 참조하시면 됩니다.

