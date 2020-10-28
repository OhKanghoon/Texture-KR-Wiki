# ASVideoNode

`ASVideoNode`는 편리하면서도 향상된 성능으로 앱에서 비디오를  보여줄 수 있게 합니다.

> NOTE: `ASVideoNode`를 앱에서 사용하게 되면, `AVFoundation`를 link 해야 합니다. 왜냐하면 내부에서 `AVPlayerLayer` 와 `AVFoundation` 클래스들을 사용하고 있기 때문입니다.

## 기본 사용법

가장 쉽게 `ASVideoNode`를 사용하는 방법은 `AVAsset` 을 할당하는 것 입니다.

```swift
let videoNode = ASVideoNode()

let asset = AVAsset(url: URL(string: "http://www.w3schools.com/html/mov_bbb.mp4")!)
videoNode.asset = asset
```

## 자동 재생, 자동 반복 재생, 무음

간단한 `BOOL` 값으로 VideoNode 가 여러가지 이벤트에 반응할 수 있도록 설정할 수 있습니다.

Visible Range 에 진입하게 되었을 때 비디오를 자동으로 재생하게 하고 싶다면, `shouldAutoplay` 프로퍼티를 `true` 로 설정하면 됩니다. `shouldAutoRepeat` 프로퍼티를 `true` 로 설정하면 비디오가 무한 반복 재생되고, `muted` 를 `true` 로 설정하면 비디오의 소리가 꺼집니다.

만약 비디오를 무음으로 한 번 자동 재생하게 하고 싶다면 아래와 같이 하면 됩니다.

```swift
videoNode.shouldAutoplay = true
videoNode.shouldAutorepeat = false
videoNode.muted = true
```

## Placeholder Image

VideoNode 는 `ASNetworkImageNode` 를 상속받고 있기 때문에 `URL` 프로퍼티를 사용해서 placeholder 이미지를 할당할 수 있습니다.  
만약 이렇게 하지 않았다면, 비디오의 첫 번째 프레임이 자동으로 디코딩 되어 placeholder 이미지로 사용됩니다.

![GIF Example](../.gitbook/assets/video.gif)

## ASVideoNode Delegate

비디오에서 일어나는 일들에 반응할 수 있도록 하기 위해 여러가지 딜리게이트 메서드들이 제공됩니다.   
예를 들어 플레이어의 상태값 변화에 반응하고 싶다면 아래 메서드를 사용하세요.

```swift
videoNode(_:willChange:to:)
```

딜리게이트 메서드는 `ASVideoNode` 헤더 파일을 참조하세요.



## ASVideoNode로 피드 구현시 유의사항

ASVideoNode내부적으로 AVAsset또는  AVAsset URL이 있는경우 didEnterPreloadState 에서 비동기적으로 미디어 서버로 부터 비디오를 받아오게 됩니다.

```c
- (void)didEnterPreloadState
{  
  // ... 생략 ... //

  NSArray<NSString *> *requestedKeys = @[@"playable"];
  [asset loadValuesAsynchronouslyForKeys:requestedKeys completionHandler:^{
    ASPerformBlockOnMainThread(^{
      if (_delegateFlags.delegateVideoNodeDidFinishInitialLoading) {
        [self.delegate videoNodeDidFinishInitialLoading:self];
      }
      [self prepareToPlayAsset:asset withKeys:requestedKeys];
    });
  }];
}
```

`visibleState`, `didEnterDisplayState`, `didEnterPreloadState`  각각에 비디오가 최소 3개 ~ 4개 이상 있다고 가정하면 총 최소 9개에서 12개를 호출해옵니다. 여기서 문제점은 실질적으로 비디오를 로딩하는 영역이 `ASVideoNode`가 dealloc또는 asset이 nil처리 되지않는 이상 cancel되지 않기 때문에 피드 스크롤하는데 있어서 프레임드랍이 발생할 수 밖에 없습니다. 

가장 이상적인 방법은 

* preload에서 비동기적으로 미디어를 받아옵니다.
* didEnterVisibleState에서 play를 시켜줍니다.
* didExitVisibleState에서 loading이 끝나지 않은 비디오의 동작 대해서 cancel처리 해줍니다. 

가장 잘 해결한 사례로는 Youtube 14.X 버젼 기준의 메인 피드를 예로 들 수 있습니다.

더 자세한 방법은 [여기](https://gist.github.com/GeekTree0101/5956f850cebdaf9c54aefbe9f3bf7b75)를 참고하셔도 좋습니다. 



