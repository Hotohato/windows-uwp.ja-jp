---
Description: 視覚的なフィードバックを使用して、UWP アプリとの対話が検出、解釈、および処理されるときにユーザーを表示します。
title: 視覚的なフィードバック
ms.assetid: bf2f3672-95f0-4c8c-9a72-0934f2d3b767
label: Visual feedback
template: detail.hbs
keywords: 視覚的なフィードバック, フォーカス フィードバック, タッチ フィードバック, 接触の視覚エフェクト, 入力, 操作
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: bba80403934987569c25b96eced9a610226431b5
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257911"
---
# <a name="guidelines-for-visual-feedback"></a>視覚的なフィードバックのガイドライン

視覚的なフィードバックは、対話式操作が検出、解釈、処理されていることをユーザーに示すために使います。 視覚的なフィードバックは、対話式操作を促進することによってユーザーを支援します。 対話式操作の成功を示すことによって、ユーザーのコントロール感を向上させます。 また、システム状態の中継やエラーの削減も可能になります。

> **重要な API**: [**Windows.Devices.Input**](https://docs.microsoft.com/uwp/api/Windows.Devices.Input)、[**Windows.UI.Input**](https://docs.microsoft.com/uwp/api/Windows.UI.Input)、[**Windows.UI.Core**](https://docs.microsoft.com/uwp/api/Windows.UI.Core)

## <a name="recommendations"></a>推奨事項

- 広範囲な変更はコントロールとアプリケーションの両方のパフォーマンスとアクセシビリティに影響を与える可能性があるため、コントロール テンプレートの変更を、設計意図に直接関連するものに制限するようにしてください。 
    - 表示状態のプロパティなど、コントロールのプロパティのカスタマイズの詳細については、「[XAML スタイル](https://docs.microsoft.com/windows/uwp/design/controls-and-patterns/xaml-styles)」を参照してください。
    - コントロール テンプレートに対する変更の詳細については、「[UserControl クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.usercontrol)」を参照してください。
    - コントロール テンプレートに大幅な変更を加える必要がある場合は、独自にテンプレート化したカスタム コントロールを作成することを検討してください。 テンプレート化したカスタム コントロールの例については、「[カスタム編集コントロールのサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/CustomEditControl)」を参照してください。
- タッチの視覚エフェクトがアプリの使用を妨げる可能性がある場合は、使わないでください。 詳しくは、「[**ShowGestureFeedback**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.showgesturefeedback)」をご覧ください。
- どうしても必要な場合以外は、フィードバックを表示しないでください。 その場所でしか意味がない場合を除き、視覚的なフィードバックを表示せずに、UI の簡潔さを維持してください。
- Windows の組み込みジェスチャの視覚的なフィードバックの動作は大幅にカスタマイズしないでください。この動作をカスタマイズすると、ユーザー エクスペリエンスに一貫性がなくなり、混乱する可能性があります。

## <a name="additional-usage-guidance"></a>その他の使い方のガイダンス

正確性が求められるタッチ操作では、接触の視覚エフェクトが特に重要です。 たとえば、アプリでタップの位置を正確に示し、対象から外れていたかどうか、どの程度外れていたか、合わせるにはどうすればよいかをユーザーが把握できるようする必要があります。

利用可能な既定の XAML プラットフォーム コントロールを使うと、すべてのデバイスおよびすべての入力状況でアプリが正しく動作します。 カスタマイズされたフィードバックが必要なカスタム操作をアプリに実装する場合は、そのフィードバックが適切であり、各入力デバイスでそのフィードバックが示されることを確認してください。また、フィードバックがユーザーの作業を妨げないようにする必要もあります。 このことは、視覚的なフィードバックが重要な UI と競合したり、重要な UI を隠したりする可能性があるゲームや描画アプリでは特に重要です。

> [!Important]
> 組み込みジェスチャの操作の動作を変更することはお勧めしません。

**デバイス間のフィードバック**

視覚的なフィードバックは、一般に入力デバイス (タッチ、タッチバッド、マウス、ペン/スタイラス、キーボードなど) に依存します。 たとえば、マウスの組み込みフィードバックには、通常はカーソルの移動と変化が伴います。一方、タッチとペンの場合は接触の視覚エフェクトが必要です。キーボードによる入力とナビゲーションの場合は、フォーカス用の四角形と強調表示を使います。

プラットフォーム ジェスチャのフィードバック動作を設定するには、[**ShowGestureFeedback**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.showgesturefeedback) を使います。

フィードバック UI をカスタマイズする場合は、すべての入力モードをサポートした適切なフィードバックを提供してください。

Windows には、次のような接触の視覚エフェクトが組み込まれています。

| ![タッチ フィードバック](images/TouchFeedback.png) | ![マウス フィードバック](images/MouseFeedback.png) | ![ペン フィードバック](images/PenFeedback.png) | ![キーボード フィードバック](images/KeyboardFeedback.png) |
| --- | --- | --- | --- |
| タッチの視覚エフェクト | マウス/タッチパッドの視覚エフェクト | ペンの視覚エフェクト | キーボードの視覚エフェクト |

## <a name="high-visibility-focus-visuals"></a>視認性の高いフォーカスの視覚効果

すべての Windows アプリでは、定義済みのさまざまなフォーカスの視覚効果が、アプリケーション内にある対話可能なコントロールの周囲に示されます。 新しいフォーカスの視覚効果は、すべてカスタマイズ可能であり、必要に応じて無効にすることができます。

Xbox とテレビの使用で一般的な **10 フィート エクスペリエンス**では、Windows は**表示フォーカス**をサポートします。これは、ボタンなどのフォーカス可能な要素がゲームパッドやキーボードの入力でフォーカスを取得したときに、その境界線をアニメーション化する照明効果です。 詳細については、「[Xbox およびテレビ向け設計](https://docs.microsoft.com/windows/uwp/design/devices/designing-for-tv#reveal-focus)」を参照してください。

## <a name="color-branding--customizing"></a>色のブランド化とカスタマイズ

**罫線のプロパティ**

視認性の高いフォーカスの視覚効果は、プライマリ境界線とセカンダリ境界線という 2 つの部分で構成されます。 プライマリ境界線は、**2 px** の幅があり、セカンダリ境界線の*外側*に描画されます。 セカンダリ境界線は、**1 px** の幅があり、プライマリ境界線の*内側*に描画されます。
可視性の高いフォーカスを ![ビジュアル赤線](images/FocusRectRedlines.png)

それぞれの境界線 (プライマリおよびセカンダリ) の太さを変更するには、**FocusVisualPrimaryThickness** をプライマリ境界線に対して使用し、 **FocusVisualSecondaryThickness** をセカンダリ境界線に対して使用します。
```XAML
<Slider Width="200" FocusVisualPrimaryThickness="5" FocusVisualSecondaryThickness="2"/>
```
![視認性の高いフォーカスの視覚効果における余白部分の太さ](images/FocusMargin.png)

余白は [**Thickness**](https://docs.microsoft.com/dotnet/api/system.windows.thickness) という種類のプロパティで指定されます。このため、コントロールの特定の側にのみ表示されるように、余白をカスタマイズすることができます。 下の図を参照してください。表示 ![の視覚的な余白の幅の下限のみ](images/FocusThicknessSide.png)

余白は、コントロールの視覚的な境界線と、フォーカスの視覚効果で示される*セカンダリ境界線*の開始点との間にあるスペースです。 既定の余白は、コントロールの境界線から **1 px** の幅で描画されます。 この余白はコントロールごとに変更できます。それには、**FocusVisualMargin** プロパティを変更します。
```XAML
<Slider Width="200" FocusVisualMargin="-5"/>
```
![視認性の高いフォーカスの視覚効果における余白の違い](images/FocusPlusMinusMargin.png)

*負の余白は、コントロールの中央から境界線を離し、正の余白によってコントロールの中心に向かって境界線を近づけます。*

コントロールでフォーカスの視覚効果を完全に無効にするには、**UseSystemFocusVisuals** を無効にするだけです。
```XAML
<Slider Width="200" UseSystemFocusVisuals="False"/>
```

太さ、余白、またはアプリ開発者がフォーカスの視覚効果を必要とするかどうかは、コントロールごとに決める必要があります。

**色のプロパティ**

フォーカスの視覚効果に関する色のプロパティには、プライマリ境界線の色とセカンダリ境界線の色という 2 つのプロパティがあります。 これらのフォーカスの視覚効果における境界線の色は、ページ レベルでコントロールごとに変更したり、アプリ全体を対象としてグローバルに変更したりすることができます。

アプリ全体を対象としてフォーカスの視覚効果をブランド化するには、システム ブラシを上書きします。
```XAML
<SolidColorBrush x:Key="SystemControlFocusVisualPrimaryBrush" Color="DarkRed"/>
<SolidColorBrush x:Key="SystemControlFocusVisualSecondaryBrush" Color="Pink"/>
```
![視認性の高いフォーカスの視覚効果における色の変更](images/FocusRectColorChanges.png)

コントロールごとに色を変更するには、目的のコントロールが持つフォーカスの視覚効果のプロパティを編集するだけです。
```XAML
<Slider Width="200" FocusVisualPrimaryBrush="DarkRed" FocusVisualSecondaryBrush="Pink"/>
```

## <a name="related-articles"></a>関連記事

**デザイナー向け**
* [パンのガイドライン](guidelines-for-panning.md)

**開発者向け**
* [カスタム ユーザー操作](https://docs.microsoft.com/windows/uwp/design/layout/index)

**サンプル**
* [基本的な入力サンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/BasicInput)
* [低待機時間入力サンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/LowLatencyInput)
* [ユーザー操作モードのサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/UserInteractionMode)
* [フォーカスの視覚効果のサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlFocusVisuals)

**サンプルのアーカイブ**
* [入力: XAML ユーザー入力イベントのサンプル](https://code.msdn.microsoft.com/windowsapps/Input-3dff271b)
* [入力: デバイス機能のサンプル](https://code.msdn.microsoft.com/windowsapps/Input-device-capabilities-31b67745)
* [入力: タッチヒットテストのサンプル](https://code.msdn.microsoft.com/windowsapps/Touch-Hit-Testing-sample-5e35c690)
* [XAML のスクロール、パン、ズームのサンプル](https://code.msdn.microsoft.com/windowsapps/xaml-scrollviewer-pan-and-949d29e9)
* [入力: 簡略化されたインクのサンプル](https://code.msdn.microsoft.com/windowsapps/Input-simplified-ink-sample-11614bbf)
* [入力: Windows 8 のジェスチャのサンプル](https://docs.microsoft.com/samples/browse/?redirectedfrom=MSDN-samples)
* [入力: 操作とジェスチャ (C++) のサンプル](https://code.msdn.microsoft.com/windowsapps/Manipulations-and-gestures-362b6b59)
* [DirectX タッチ入力のサンプル](https://code.msdn.microsoft.com/windowsapps/Simple-Direct3D-Touch-f98db97e)
 

 
