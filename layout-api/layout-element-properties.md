# Layout Element Properties

Node에서 ASLayoutElementStyle이라는 layout element properties를 제공해주며, ASDisplayNode는 ASLayoutElement를 상속받고 있습니다.

```swift
let node = ASDisplayNode()
node.style. .... // ASLayoutElementStyle

let element: ASLayoutElement = node
element.style. ... // ASLayoutElementStyle
```

Texture Layout API는 layout을 build하는 과정에 있어서 개발자에게 필요한 layout element properties 를 style property를 접근하여 설정해줍니다. 용도에 따라 Stack형태와 Absolute 및 Basic Layout으로 총 3가지로 나누어집니다. 용도에 맞게 사용하시는 것을 권장드리며, 용도와 상관없이 모든 ASLayoutElement에서 접근가능합니다.

## 1. Texture Layout Element Properties에서 주로사용하는 property type 정리

| Name | Description |
| :--- | :--- |
| ASDimension | unit과 value값을 받습니다. unit는 **auto, points, fraction** 3가지가 있으며 value는 **CGFloat**형태로 넣는다던가 **String형태**로 %, pt와 같은 **suffix**를 추가하여 사용할 수 있습니다. |
| ASLayoutSize | ASLayoutSize는 CGSize와 다르게 CGFloat가 아닌 **ASDimension Type의 width, height**를 받습니다. |

```swift
// ASDimension Example

let point = ASDimension(unit: .points, value: 100.0)
let point2 = ASDimensionMake("100.0pt")

let fraction = ASDimension(unit: .fractions, value: 0.5) // 50%
let fraction2 = ASDimensionMake("50%") // 50%

// ASLayoutSize Example
let width = ASDimension(unit: .points, value: 100.0)
let height = ASDimension(unit: .points, value: 100.0)
let relativeSize = ASLayoutSize(width: width, height: height)

let width2 = ASDimension(unit: .fractions, value: 0.5)
let height2 = ASDimension(unit: .fractions, value: 0.5)
let relativeSize2 = ASLayoutSize(width: width, height: height)
```

더 자세한 설명은 [여기](https://texture-kr.gitbook.io/wiki/layout-api/layout-api-sizing)를 참고해주세요.  

## 2. Basic Layout Element Properties

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Type</th>
      <th style="text-align:left">Defult Value</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>width</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">
        <p>&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xB113;&#xC774;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.</p>
        <p><em>&#xC720;&#xC758;&#xC0AC;&#xD56D;: width&#xB97C; &#xC124;&#xC815;&#xC2DC; minWidth&#xC640; maxWidth&#xAC00; width&#xB85C; &#xC7AC;&#xC124;&#xC815;&#xB429;&#xB2C8;&#xB2E4;.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>height</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">
        <p>&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xB192;&#xC774;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.</p>
        <p><em>&#xC720;&#xC758;&#xC0AC;&#xD56D;: height&#xB97C; &#xC124;&#xC815;&#xC2DC; minHeight&#xC640; maxHeight&#xAC00; height&#xB85C; &#xC7AC;&#xC124;&#xC815;&#xB429;&#xB2C8;&#xB2E4;.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>minWidth</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xCD5C;&#xC18C; &#xB113;&#xC774;&#xB97C;
        &#xC9C0;&#xD569;&#xB2C8;&#xB2E4;. &#xC9C0;&#xC815;&#xB41C; &#xAC12;&#xBCF4;&#xB2E4;
        &#xC791;&#xC544;&#xC9C0;&#xB294; &#xAC83;&#xC744; &#xBC29;&#xC9C0;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxWidth</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xCD5C;&#xB300; &#xB113;&#xC774;&#xB97C;
        &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;. &#xC9C0;&#xC815;&#xB41C; &#xAC12;&#xBCF4;&#xB2E4;
        &#xCEE4;&#xC9C0;&#xB294; &#xAC83;&#xC744; &#xBC29;&#xC9C0;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>minHeight</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xCD5C;&#xC18C; &#xB192;&#xC774;&#xB97C;
        &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;. &#xC9C0;&#xC815;&#xB41C; &#xAC12;&#xBCF4;&#xB2E4;
        &#xC791;&#xC544;&#xC9C0;&#xB294; &#xAC83;&#xC744; &#xBC29;&#xC9C0;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxHeight</b>
      </td>
      <td style="text-align:left">ASDimension</td>
      <td style="text-align:left">ASDimensionAuto</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3; &#xCD5C;&#xB300; &#xB192;&#xC774;&#xB97C;
        &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;. &#xC9C0;&#xC815;&#xB41C; &#xAC12;&#xBCF4;&#xB2E4;
        &#xCEE4;&#xC9C0;&#xB294; &#xAC83;&#xC744; &#xBC29;&#xC9C0;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>preferredSize</b>
      </td>
      <td style="text-align:left">CGSize</td>
      <td style="text-align:left">
        <p>zero,</p>
        <p>&#xAE30;&#xBCF8;&#xC801;&#xC73C;&#xB85C;calculatedSizeThatFits: &#xC5D0;
          &#xC758;&#xD574; &#xACB0;&#xC815;&#xB429;&#xB2C8;&#xB2E4;.</p>
      </td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3; &#xC694;&#xC18C;&#xC5D0; &#xC81C;&#xC548;&#xD558;&#xACE0;&#xC790;
        &#xD558;&#xB294; &#xC0AC;&#xC774;&#xC988;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.
        &#xD558;&#xC9C0;&#xB9CC; &#xC9C0;&#xC815;&#xD55C; &#xAC12;&#xBCF4;&#xB2E4;
        &#xCEE4;&#xC9C0;&#xAC70;&#xB098; &#xC791;&#xC544;&#xC9C0;&#xBA74; &#xC0C1;&#xD669;&#xC5D0;
        &#xB530;&#xB77C; minSize &#xB610;&#xB294; maxSize&#xAC00; &#xC801;&#xC6A9;&#xB429;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>minSize</b>
      </td>
      <td style="text-align:left">CGSize</td>
      <td style="text-align:left"><b>setter (set only)</b>
      </td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC5D0; &#xB300;&#xD55C; &#xCD5C;&#xC18C;
        &#xC0AC;&#xC774;&#xC988;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.
        &#xC720;&#xC758;&#xC0AC;&#xD56D;: <em>&#xB9CC;&#xC57D; &#xBD80;&#xBAA8; &#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xCD5C;&#xC18C; &#xD06C;&#xAE30;&#xAC00; &#xC790;&#xC2DD;&#xC758; &#xCD5C;&#xC18C; &#xD06C;&#xAE30;&#xBCF4;&#xB2E4; &#xC791;&#xC740; &#xACBD;&#xC6B0; &#xC790;&#xC2DD;&#xC758; &#xCD5C;&#xC18C; &#xD06C;&#xAE30;&#xB85C; &#xACB0;&#xC815;&#xB429;&#xB2C8;&#xB2E4;.</em>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxSize</b>
      </td>
      <td style="text-align:left">CGSize</td>
      <td style="text-align:left"><b>setter (set only)</b>
      </td>
      <td style="text-align:left">
        <p>&#xB808;&#xC774;&#xC544;&#xC6C3;&#xC5D0; &#xB300;&#xD55C; &#xCD5C;&#xB300;
          &#xC0AC;&#xC774;&#xC988;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.</p>
        <p><em>&#xC720;&#xC758;&#xC0AC;&#xD56D;: &#xB9CC;&#xC57D; &#xC790;&#xC2DD; &#xB808;&#xC774;&#xC544;&#xC6C3;&#xC758; &#xCD5C;&#xB300; &#xD06C;&#xAE30;&#xAC00; &#xBD80;&#xBAA8;&#xC758; &#xCD5C;&#xB300; &#xD06C;&#xAE30;&#xBCF4;&#xB2E4; &#xC791;&#xC740; &#xACBD;&#xC6B0; &#xC790;&#xC2DD;&#xC758; &#xCD5C;&#xB300;&#xD06C;&#xAE30;&#xAC00; &#xC801;&#xC6A9;&#xB429;&#xB2C8;&#xB2E4;.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>preferredLayoutSize</b>
      </td>
      <td style="text-align:left">ASLayoutSize</td>
      <td style="text-align:left">
        <p>zero,</p>
        <p>&#xAE30;&#xBCF8;&#xC801;&#xC73C;&#xB85C;&#xB294; calculatedSizeThatFits:
          &#xC5D0; &#xC758;&#xD574; &#xACB0;&#xC815;&#xB429;&#xB2C8;&#xB2E4;.</p>
      </td>
      <td style="text-align:left">
        <p>&#xB808;&#xC774;&#xC544;&#xC6C3; &#xC694;&#xC18C;&#xC5D0; &#xB300;&#xD574;
          &#xC0C1;&#xB300;&#xC801; &#xD06C;&#xAE30;&#xB97C; &#xC81C;&#xACF5;&#xD569;&#xB2C8;&#xB2E4;.</p>
        <p><em>&#xC720;&#xC758;&#xC0AC;&#xD56D;: ASLayoutSize&#xB294; unit&#xAC12;&#xC774; point&#xAC00; &#xC544;&#xB2C8;&#xB77C; percentage(%, 0.0 ~ 1.0) &#xC785;&#xB2C8;&#xB2E4;.</em>
        </p>
      </td>
    </tr>
    <tr>
      <td style="text-align:left"><b>minLayoutSize</b>
      </td>
      <td style="text-align:left">ASLayoutSize</td>
      <td style="text-align:left">layout spec&#xC5D0; &#xB530;&#xB77C; &#xACB0;&#xC815;</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3; &#xC694;&#xC18C;&#xC5D0; &#xB300;&#xD574;
        &#xCD5C;&#xC18C;&#xD55C;&#xC758; &#xC0C1;&#xB300;&#xD06C;&#xAE30;&#xB97C;
        &#xC81C;&#xACF5;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
    <tr>
      <td style="text-align:left"><b>maxLayoutSize</b>
      </td>
      <td style="text-align:left">ASLayoutSize</td>
      <td style="text-align:left">layout spec&#xC5D0; &#xB530;&#xB77C; &#xACB0;&#xC815;</td>
      <td style="text-align:left">&#xB808;&#xC774;&#xC544;&#xC6C3; &#xC694;&#xC18C;&#xC5D0; &#xB300;&#xD574;
        &#xCD5C;&#xB300;&#xD55C;&#xC758; &#xC0C1;&#xB300;&#xD06C;&#xAE30;&#xB97C;
        &#xC81C;&#xACF5;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
  </tbody>
</table>## 3. Stack Layout Element Properties \(ASStackLayoutElement\)

| Name | Type | Default Value | Description |
| :--- | :--- | :--- | :--- |
| **flexGrow** | CGFloat | 0.0 | 레이아웃의 증가 너비 비율을 지정합니다. |
| **flexShrink** | CGFloat | 0.0 | 레이아웃의 감소 너비 비율을 지정합니다. |
| **flexBasis** | ASDimension | ASDimensionAuto | 레이아웃이 Stack상 공간이 배분되기전 기본 너비를 지정합니다. |
| **alignSelf** | ASStackLayoutAlignSelf | auto | 교차 축상에서 레이아웃의 정렬 방법을 지정합니다. |
| **spacingBefore** | CGFloat | 0.0 | Stack방향을 기준으로 동등한 위치 상에서 이전 레이아웃과의 간격을 지정합니다. |
| **spacingAfter** | CGFloat | 0.0 | Stack방향을 기준으로 동등한 위치 상에서 다음 레이아웃과의 간격을 지정합니다. |
| **ascender** | CGFloat | 0.0 | 기준선 정렬에 사용됩니다. 레이아웃의 맨 위에서 기준선까지의 거리를 의미합니다. |
| **descender** | CGFloat | 0.0 | 기준선 정렬에 사용됩니다. 레이아웃의 기준선에서 맨 아래까지의 거리를 의미합니다. |

## 4. Absolute Layout Element Properties \(ASAbsoluteLayoutElement\)

<table>
  <thead>
    <tr>
      <th style="text-align:left">Name</th>
      <th style="text-align:left">Type</th>
      <th style="text-align:left">Default Value</th>
      <th style="text-align:left">Description</th>
    </tr>
  </thead>
  <tbody>
    <tr>
      <td style="text-align:left"><b>layoutPosition</b>
      </td>
      <td style="text-align:left">CGPoint</td>
      <td style="text-align:left">
        <p>zero</p>
        <p>(x: 0.0, y: 0.0)</p>
      </td>
      <td style="text-align:left">&#xBD80;&#xBAA8; &#xB808;&#xC774;&#xC544;&#xC6C3; &#xC0C1;&#xC5D0;&#xC11C;
        &#xACE0;&#xC815; &#xC704;&#xCE58;&#xB97C; &#xC9C0;&#xC815;&#xD569;&#xB2C8;&#xB2E4;.</td>
    </tr>
  </tbody>
</table>