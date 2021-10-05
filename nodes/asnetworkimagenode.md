# ASNetworkImageNode

`ASNetworkImageNode` 는 원격 서버에서 호스팅된 이미지를 보여주고 싶을 때 사용할 수 있습니다. `ASNetworkImageNode` 의 `.url`  프로퍼티에 적합한 URL을 넣어주면 이미지는 비동기로 로드되고, 동시에 렌더링 됩니다.

```swift
let imageNode = ASNetworkImageNode()
imageNode.url = URL(string: "https://someurl.com/image_uri")
```

## NetworkImageNode의 레이아웃 잡기

`ASNetworkImageNode` 는 만들어 질 때 `intrinsic content size` 를 가지고 있지 않기 때문에, layout을 명시적으로 설정해 주어야 합니다.

#### _Option 1: .style.preferredSize_

만약 이미지 노드의 프레임 크기를 표준 크기로 지정하려는 경우 `.style.preferredSize` 를 사용할 수 있습니다.

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    imageNode.style.preferredSize = CGSize(width: 100, height: 200)
    ...
    return finalLayoutSpec
}
```

#### _Option 2: ASRatioLayoutSpec_

ASNetworkImageNode는 `ASRatioLayoutSpec` 를 사용하기에 최적의 장소입니다. 당신은 이미지에 정적인 사이즈를 할당하는 대신 비율을 할당할 수 있고, 이미지는 로딩을 마쳤을 때 해당 비율을 유지하면서 보여집니다.

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
    let ratio: CGFloat = 3.0/1.0
    let imageRatioSpec = ASRatioLayoutSpec(ratio:ratio, child:self.imageNode)
    ...
    return finalLayoutSpec
}
```

## 이면

> 만약  `PINRemoteImage` 와 `PINCache` 의존성을 포함하지 않는다면, progressive jpeg 지원을 받지 못하고  `ASImageCacheProtocol` 을 따르는 커스텀 캐시를 포함해야 합니다.

### Progressive JPEG 지원

`PINRemoteImage`를 포함하면 Network Image Node는 이제 Progressive JPEG 이미지를 완벽하게 지원합니다. 만약 서버가 Progressive JPEG 이미지를 제공한다면, 이미지들은 저화질로 빠르게 보여진 후에 더 많은 데이터가 로드되면 scale up 하게 됩니다.

progressive loading \(점진적 이미지 로딩\) 을 활성화 하려면 `shouldRenderProgressImages` 값을 `true`로 설정해야 합니다.

```swift
networkImageNode.shouldRenderProgressImages = true
```

이 옵션은 하나의 이미지를 사용해 점진적으로 로드합니다. 서버가 일반적인 JPEG 포맷을 지원하고 이미지의 여러 퀄리티 버전을 제공할 때, ASMultiplexImageNode를 사용해보면 좋습니다.

## 자동 캐싱

`ASNetworkImageNode` 는 네트워크 이미지들을 자동으로 캐시하기 위해 default 로  `PINCache` 를 사용합니다.

## GIF 지원

`ASNetworkImageNode` 는 `PINRemoteImage` 의 베타 `PINAnimatedImage` 를 통해 GIF를 지원합니다. 

*주의 하세요!* 이러한 지원은 `shouldCacheImage` 가 `NO`로 설정되어 있지 않는 한 local 파일들에는 지원되지 않습니다.

## Image Downloader 에 URLSessionConfiguration 추가하기

`ASNetworkImageNode` 는 기본적으로 이미지를 다운로드를 할 때 `ASPinRemoteImageDownloader` 를 사용합니다. 만약 이미지 요청에 추가로 configuration이 필요한 경우 아래의 코드로 대응 가능합니다.

```swift
var config = URLSessionConfiguration.ephemeral
config.httpAdditionalHeaders = [
    "User-Agent": userAgent
]
ASPINRemoteImageDownloader.setSharedImageManagerWith(config)
```

