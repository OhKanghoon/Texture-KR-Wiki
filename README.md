---
description: Keeps the most complex iOS user interfaces smooth and responsive.
---

# Texture-kr에 오신것을 환영합니다.



![Built at Pinterest](.gitbook/assets/image%20%288%29.png)

## **Texture?**

Texture\(a.k.a AsyncDisplayKit\)는 부드럽고 반응이 빠른 인터페이스를 위한 iOS UI 프레임워크입니다. 사용자 인터페이스를 스레드로 부터 안전하게하는 것이 주요 핵심입니다. 즉, UI 구성요소에 대한 측정 및 렌더링이 백그라운드 스레드에서 동시에 발생할 수 있습니다.

프레임워크가 커짐에 따라 현대 iOS 앱에서 흔히 발생하는 공통적인 상용구 스타일 구조를 제거하여 개발자가 시간을 절약 할 수있는 많은 기능이 추가되어있으며, auto-layout 및 xib, storyboard를 사용하지 않고 Texture Layout API를 사용하여 레이아웃 설계 함으로써 UI컴포넌트에 대한 모듈화 및 코드리뷰에도 큰 이점을 누리실 수가 있습니다. 즉, 소규모 및 대규모 프로젝트 구분 없이 생산성을 확실하게 보장해줍니다.

 특히, 셀\(UITableViewCell, UICollectionViewCell\) 재사용 버그 경험중 대부분 페이지 또는 스크롤 스타일 인터페이스의 데이터를 미리 미리로드\(preload\)하려고 할 때 프레임드랍을 경험하신 적이 있으실 것입니다. 하지만 Texture를 사용하게 된 다면 앞서 말한 프레임드랍과 같은 UI로 부터 일어나는 퍼포먼스 이슈나 다양한 버그 해결에 대한 부담감을 줄일 수가 있습니다.

## Motivation & Benefit

Texture는 UIKit의 Auto-Layout 사용하지않고 Texture에서 제공해주는 Layout API를 사용합니다. UIKit의 Auto-Layout은 복잡한 레이아웃에 대한 처리 계산이 무겁고 복잡합니다. 하지만 Texture에서 제공하는 LayoutAPI를 사용할 경우 다음과 같은 이점을 누리실 수가 있습니다.

* Fast: Texture Layout API를 이용하여 코드로 작성하며 Auto-Layout 보다 레이아웃을 설계하는데 있어서 빠릅니다. 
* Asynchronous & Concurrent: 어떠한 레이아웃이든 백그라운드 스레드에서 처리하며 사용자 인터렉션을 방해하지 않으며 메인스레드에 대한 오버헤드를 줄여줍니다. 
* Declarative: 레이아웃은 변경 불가능한 데이터 구조로 선언됩니다. 따라서 레이아웃 코드를 보다 쉽게 ​​개발, 문서화, 코드 검토, 테스트, 디버그, 프로파일링 및 유지보수 관리를 할 수 ​​있습니다.
* Cacheable: 레이아웃 결과물은 변경 불가능한 데이터 구조이므로 백그라운드에서 미리 계산되고 캐싱되어 사용자인터페이스 성능을 향상시킬 수 있습니다.
* Extensible: 클래스 간 코드 공유가 용이합니다.

## History

Texture의 이전 이름은 AsyncDisplayKit라 불렸었습니다. Facebook의 페이퍼 프로젝트에 큰 힘을 불어주는 원동력이 된 UI  Framework였습니다. 당시 어플리케이션 치고는 페이퍼는 다른 앱과 비교될 정도로 상당히 부드럽고 유동적인 피드 스크롤 기능을 제공하는 몇 안되는 앱 중 하나 였으며, 또한 모바일 엔지니어링 분야의 게임 체인저였습니다. 

그 당시의 수석 엔지니어는 Scott Goodson\(2012-2014\)이었습니다. 그 Facebook에 합류하기 전에는 Apple의 iOS 팀에서 엔지니어로 일했으며 주로 UIKit 개발분야에서 일했습니다. 그렇기 때문에 그가 UIKit에 대한 강약점에 대해 깊이 이해하고 있고 Texture\(AsyncDisplayKit\)를 개발하게 된 것은 놀라운 일이 아닐 수가 없습니다.

이후 2014년에 Facebook은 AsyncDisplayKit을 공개했으며 Scott은 현재까지 메인 컨트리뷰터로 남아있습니다. 

2015년 그는 Pinterest에 합류 한 후 사측의 동료 엔지니어와들과 함께 AsyncDisplayKit의 기존코드 전체중 약 70%이상 기여하였으며, 2017년 Pinterest는 AsyncDisplayKit의 명칭 Texture로 바꾸었으며 Github에 프로젝트를 지속적으로 유지보수하고 있습니다.

## [https://texture-kr.gitbook.io](https://texture-kr.gitbook.io) 에 오신것을 환영합니다.

국내 iOS개발자 및 Texture\(AsyncDisplayKit\) 팬여러분들 Texture-kr gitbook에 오신것을 환영합니다. [컨트리뷰트](https://github.com/OhKanghoon/Texture-KR-Wiki)는 언제든지 환영합니다. 

### Authors

* StyleShare iOS Developer
  * [https://github.com/OhKanghoon](https://github.com/OhKanghoon)
* 당근마켓 iOS Developer
  * [https://github.com/GeekTree0101](https://github.com/GeekTree0101)

### Contributors

* [https://github.com/amywork](https://github.com/amywork)
* [https://github.com/gkdlfm](https://github.com/gkdlfm)

### Open KakaoTalk

{% embed url="https://open.kakao.com/o/gwLzjpkb" %}

### Texture Slack

{% embed url="http://texturegroup.org/slack.html" caption="Slack Invite Link" %}

### Repository

오타 수정 및 내용 추가가 필요하다면 아래 Repository 에 pr 을 날려주세요.

{% embed url="https://github.com/OhKanghoon/Texture-KR-Wiki" %}





