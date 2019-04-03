# ASNetworkImageNode

- [http://texturegroup.org/docs/network-image-node.html](http://texturegroup.org/docs/network-image-node.html)


`ASNetworkImageNode` 는 원격 서버에서 호스팅된 이미지를 보여주고 싶을 때 사용할 수 있습니다. 당신이 해야 할 모든 것은 `.URL` 프로퍼티에 적합한 `NSURL` 인스턴스를 set 하는 것입니다. 그러면 당신을 위해 이미지는 비동기로 로드되고, concurrently 하게 랜더링 될 것입니다. 

```swift
let imageNode = ASNetworkImageNode()
imageNode.url = URL(string: "https://someurl.com/image_uri")
```

### 네트워크 이미지 노드의 레이아웃 잡기

`ASNetworkImageNode` 는 이것이 만들어 질 때 `intrinsic content size` 를 가지고 있지 않기 때문에,   당신이 명시적으로 이것들이 어떻게 레이아웃 되어야 하는지 설정해 주어야 합니다.

#### Option 1. `.style.preferredSize`
만약 이미지 노드의 frame size 에 적합한 표준 사이즈를 갖고 있다면 `.style.preferredSize`
를 사용하세요.

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
	imageNode.style.preferredSize = CGSize(width: 100, height: 200)
	...
	return finalLayoutSpec
}
```


#### Option 2: `ASRatioLayoutSpec`
이 곳은 `ASRatioLayoutSpec` 를 사용하기에 최적의 장소입니다. 당신은 이미지에 정적인 사이즈를 할당하는 대신, 비율을 할당할 수 있고, 이미지는 로딩을 마쳤을 때 해당 비율을 유지하면서 디스플레이 될 것 입니다. 

```swift
override func layoutSpecThatFits(_ constrainedSize: ASSizeRange) -> ASLayoutSpec {
	let ratio: CGFloat = 3.0/1.0
	let imageRatioSpec = ASRatioLayoutSpec(ratio:ratio, child:self.imageNode)
	...
	return finalLayoutSpec
}
```

### 이면

> 만약 당신이 `PINRemoteImage` 와 `PINCache` 의존성을 포함하지 않기로 결정했다면, progressive jpeg 지원을 받지 못하게 될 것이고, `ASImageCacheProtocol` 을 따르는 당신의 커스텀한 캐시를 포함해야만 합니다.

#### Progressive JPEG 지원
`PINRemoteImage`를 포함한 덕분에 네트워크 이미지 노드는 이제 Progressive JPEG 이미지를 완전히 지원하게 되었습니다. 만약 당신의 서버가 Progressive JPEG 이미지를 제공한다면, 당신의 이미지들은 저화질로 빠르게 디스플레이된 후에 더 많은 데이터가 로드되면 scale up 하게 됩니다.

progressive loading (점진적 이미지 로딩) 을 활성화 하려면 `shouldRenderProgressImages` 값을 `YES`로 설정하세요.

```swift
networkImageNode.shouldRenderProgressImages = true
```

점진적으로 로드되는 하나의 이미지만을 사용한다는 것을 기억하는 것이 중요합니다. 만약 서버가 일반적인 JPEG 포맷을 지원하고, 여러가지 버전으로 퀄리티 향상을 제공한다면, [ASMultiplexImageNode](http://texturegroup.org/docs/multiplex-image-node.html)를 대신 참고해보세요.

### 자동 캐싱
`ASNetworkImageNode` 는 네트워크 이미지들을 자동으로 캐시하기 위해 이제 `PINCache` 를 default 로 사용합니다. 

### GIF 지원
`ASNetworkImageNode` 는 `PINRemoteImage` 의 베타 `PINAnimatedImage` 를 통해 GIF 를 지원합니다. 주의 하세요! 이러한 지원은 `shouldCacheImage` 가 `NO`로 설정되어 있지 않는 한 local 파일들에는 지원되지 않습니다. 
