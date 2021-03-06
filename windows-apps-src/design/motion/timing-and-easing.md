---
Description: どの Fluent のモーションはタイミングと、イージング関数について説明します。
title: タイミングとイージング - UWP アプリでのアニメーション
label: Timing and easing
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
pm-contact: stmoy
design-contact: jeffarn
doc-status: Draft
ms.localizationpriority: medium
ms.custom: RS5
ms.openlocfilehash: b736a10a7284e3cc9aa193e082dc654e908afe40
ms.sourcegitcommit: cc0ef75f314658b14376eb60ef8e5bb4d7726e04
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/08/2019
ms.locfileid: "65444174"
---
# <a name="timing-and-easing"></a>タイミングとイージング

モーションが現実世界に基づいている場合、私たちは速度とパフォーマンスへの期待を伴うデジタル メディアでもあります。

## <a name="examples"></a>例

<table>
<tr>
<td><img src="images/xaml-controls-gallery-app-icon.png" alt="XAML controls gallery" width="168"></img></td>
<td>
    <p>ある場合、 <strong style="font-weight: semi-bold">XAML コントロール ギャラリー</strong>アプリをインストールするには、ここをクリックして<a href="xamlcontrolsgallery:/item/EasingFunction">アプリを開き、アクションのイージング関数を参照してください</a>します。</p>
    <ul>
    <li><a href="https://www.microsoft.com/store/productId/9MSVH128X2ZT">XAML コントロール ギャラリー アプリを入手する (Microsoft Store)</a></li>
    <li><a href="https://github.com/Microsoft/Xaml-Controls-Gallery">ソース コード (GitHub) を入手する</a></li>
    </ul>
</td>
</tr>
</table>

## <a name="how-fluent-motion-uses-time"></a>Fluent モーションが時間を使用する方法

タイミングは、UI 内で出入りしたり、移動したりするオブジェクトのモーションを自然に感じさせる重要な要素です。

1. ビューに入るオブジェクトまたはシーンはあっという間ですが、美しく彩ります。 これらのアニメーションは、通常、シーンの階層的なビルドアップを可能にするために、シーンから出るときより継続時間が長くなります。
1. ビューを出るオブジェクトまたはシーンは、非常にあっという間です。 ユーザーは、UI がどこに行ったかを理解できる必要があります。 ただし、UI が閉じたら、それが邪魔にならないようにする必要があります。
1. シーンを移動するオブジェクトは、移動距離に適した時間が必要です。

## <a name="timing-in-fluent-motion"></a>Fluent モーションのタイミング

Fluent のモーションのタイミングでは、ベースラインとして 500 ミリ秒 (0.5 秒) を使用します。これはユーザーが瞬間に認識する最長時間です。

![ヒーロー イメージ](images/time.gif)

### <a name="150ms-exit"></a>**150ミリ秒** (終了)

:::row:::
    :::column:::
オブジェクトまたはシーンの終了は、または終了しているページに使用します。
終了する UI の非常に速い方向性フィードバックを可能にします。ここでは、スムーズなアニメーションを実現するためにタイミングがフレーム レートを妨げることはありません。
    :::column-end:::
    :::column:::
        ![150ms motion](images/150msAlt.gif)
    :::column-end:::
:::row-end:::

### <a name="300ms-enter"></a>**300 ミリ秒** (入力)

:::row:::
    :::column:::
オブジェクトまたはシーンを入力するか、開くページに使用します。
シーンに入るときにコンテンツを彩るために適切な時間が確保されます。
    :::column-end:::
    :::column:::
        ![300ms motion](images/300ms.gif)
    :::column-end:::
:::row-end:::

### <a name="500ms-move"></a>**≤500 ミリ秒** (移動)

:::row:::
    :::column:::
シーンは 1 つまたは複数のシーンの間で翻訳をオブジェクトに使用します。 
    :::column-end:::
    :::column:::
        ![500ms motion](images/500ms.gif)
    :::column-end:::
:::row-end:::

## <a name="easing-in-fluent-motion"></a>Fluent モーションのイージング

イージングは、移動時のオブジェクトの速度を操作する方法です。 Fluent モーションのすべてのエクスペリエンスを結び付ける接着剤です。 極端ですが、システムで使用されるイージングは、システム内で移動するオブジェクトの物理的な操作感を統合することができます。 これは、現実世界を模倣し、移動中のオブジェクトがその環境に属しているように感じさせる 1 つの方法です。

![ヒーロー イメージ](images/easing.gif)

## <a name="apply-easing-to-motion"></a>モーションへのイージングの適用

これらのイージングは、より自然な操作感を得るのに役立ち、Fluent モーションのために使用するベースラインとなります。

コード例では、推奨されるイージング値を Storyboard アニメーション (XAML) または Composition アニメーション (C#) に適用する方法を示しています。

### <a name="accelerate-exit"></a>**高速化**(終了)

:::row:::
    :::column:::
UI またはシーンを終了するオブジェクトを使用します。

オブジェクトは、電源になるし、エスケープ ベロシティに到達するまで、運動量を取得します。
結果として得られる感覚は、オブジェクトが、ユーザーの邪魔に活用し付属する新しいコンテンツの場所を確保する最も難しいその試行されています。
    :::column-end:::
    :::column:::
        ![accelerate easing](images/accelEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.7 , 0 , 1 , 0.5)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.15">
        <DoubleAnimation.EasingFunction>
            <ExponentialEase Exponent="4.5" EasingMode="EaseIn" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction accelerate =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.7f, 0.0f), new Vector2(1.0f, 0.5f));
_exitAnimation = _compositor.CreateScalarKeyFrameAnimation();
_exitAnimation.InsertKeyFrame(0.0f, _startValue);
_exitAnimation.InsertKeyFrame(1.0f, _endValue, accelerate);
_exitAnimation.Duration = TimeSpan.FromMilliseconds(150);
```

### <a name="decelerate-enter"></a>**減速**(入力)

:::row:::
    :::column:::
オブジェクトまたはシーンを入力する UI を移動するか、起動に使用します。

シーンのオブジェクトは、極端な摩擦が rest にオブジェクトの速度は満たされます。
結果として得られる感覚は、オブジェクトの旅行時間の長い距離を離れてからするか、極端な速度では、時に入力したして、rest の状態にすばやく返すことがしたです。

無応答の少し前に、場合でも、受信オブジェクトの速度は高速で応答性を感じての効果を持ちます。
    :::column-end:::
    :::column:::
        ![decelerate easing](images/decelEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.1 , 0.9 , 0.2 , 1)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.3">
        <DoubleAnimation.EasingFunction>
            <ExponentialEase Exponent="7" EasingMode="EaseOut" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction decelerate =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.1f, 0.9f), new Vector2(0.2f, 1.0f));
_enterAnimation = _compositor.CreateScalarKeyFrameAnimation();
_enterAnimation.InsertKeyFrame(0.0f, _startValue);
_enterAnimation.InsertKeyFrame(1.0f, _endValue, decelerate);
_enterAnimation.Duration = TimeSpan.FromMilliseconds(300);
```

### <a name="standard-easing-move"></a>**標準的なイージング**(移動)

:::row:::
    :::column:::
これは、システム内でアニメーション化されたパラメーターの変更のイージング基準です。
単純な位置変更など、画面上で状態ごとに変化するオブジェクトに標準的なイージングを使用します。 また、拡大するオブジェクトのように、シーン内でモーフィングするオブジェクトに使用します。

結果として得られる感覚は、A から B に状態を変更するオブジェクトの解決は、自然の強制によって実行される、です。
    :::column-end:::
    :::column:::
        ![standard easing](images/standardEase.gif)
    :::column-end:::
:::row-end:::

```
cubic-bezier(0.8 , 0 , 0.2 , 1)
```

```xaml
<!-- Use for XAML Storyboard animations. -->
<Storyboard x:Name="Storyboard">
    <DoubleAnimation Storyboard.TargetName="Translation" Storyboard.TargetProperty="X" From="0" To="200" Duration="0:0:0.5">
        <DoubleAnimation.EasingFunction>
            <CircleEase EasingMode="EaseInOut" />
        </DoubleAnimation.EasingFunction>
    </DoubleAnimation>
</Storyboard>
```

```csharp
// Use for Composition animations.
CubicBezierEasingFunction standard =
    _compositor.CreateCubicBezierEasingFunction(new Vector2(0.8f, 0.0f), new Vector2(0.2f, 1.0f));
 _moveAnimation = _compositor.CreateScalarKeyFrameAnimation();
 _moveAnimation.InsertKeyFrame(0.0f, _startValue);
 _moveAnimation.InsertKeyFrame(1.0f, _endValue, standard);
 _moveAnimation.Duration = TimeSpan.FromMilliseconds(500);
```

## <a name="related-articles"></a>関連記事

- [アニメーションの概要](index.md)
- [方向性と重力](directionality-and-gravity.md)
