---
ms.assetid: F46D5E18-10A3-4F7B-AD67-76437C77E4BC
title: 変換の概要
description: Windows ランタイムで変換を使用する方法について説明します (& a)\#160;API、UI 内の要素の相対座標システムを変更することで。
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 1fe9805bfd754c050371b41ff091b1b2edf47891
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66364928"
---
# <a name="transforms-overview"></a>変換の概要

UI 要素の相対座標系を変更して、Windows ランタイム API で変換を使う方法について説明します。 変換を使うと、スケーリング、回転、x-y 空間内での平行移動など、個々の XAML 要素の外観を調整できます。

## <a name="span-idwhatisatransformspanspan-idwhatisatransformspanspan-idwhatisatransformspanwhat-is-a-transform"></a><span id="What_is_a_transform_"></span><span id="what_is_a_transform_"></span><span id="WHAT_IS_A_TRANSFORM_"></span>変換とは何ですか。

*変換*とは、座標空間の間で点をマップする、つまり、変換する方法を定義することです。 変換が UI 要素に適用された場合、その UI 要素の外観が UI の一部として画面にレンダリングされる方法が変更されます。

平行移動、回転、スケーリング、スキュー (せん断) という 4 つの種類の変換を考えてみます。 グラフィックス API を使って UI 要素の外観を変更する目的で、最も簡単なのは通常、一度に 1 つの演算のみを定義する変換を作成することです。 そのため、Windows ランタイムでは、これらの変換の種類ごとに個別のクラスが定義されています。

-   [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform): の値の設定で、x と y の領域内の要素を平行移動[ **X** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.x)と[ **Y** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.translatetransform.y).
-   [**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform): の値を設定して、中心点に基づいて変換をスケーリング[ **CenterX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centerx)、 [ **CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centery)、 [ **ScaleX** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.scalex)と[ **ScaleY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.scaleyproperty)します。
-   [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform): の値を設定して、x と y の領域に回転[**角度**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.angle)、 [ **CenterX** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.centerx)と[ **CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.centery)します。
-   [**SkewTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SkewTransform): 傾斜またはの値を設定して、x と y の空間における傾けて[ **AngleX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.anglex)、 [ **AngleY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.angley)、[ **CenterX** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.skewtransform.centerx)と[ **CenterY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.scaletransform.centeryproperty)します。

これらのうちで、UI のシナリオとして最もよく使うと思われるのは、[**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) と [**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform) です。

変換を組み合わせることができ、これをサポートする 2 つの Windows ランタイム クラスがあります。[**CompositeTransform** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform)と[ **TransformGroup**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TransformGroup)します。 **CompositeTransform** では、変換が、回転、スキュー、回転、平行移動の順に適用されます。 変換を別の順序で適用する場合は、**CompositeTransform** ではなく、**TransformGroup** を使います。 詳しくは、「[**CompositeTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform)」をご覧ください。

## <a name="span-idtransformsandlayoutspanspan-idtransformsandlayoutspanspan-idtransformsandlayoutspantransforms-and-layout"></a><span id="Transforms_and_layout"></span><span id="transforms_and_layout"></span><span id="TRANSFORMS_AND_LAYOUT"></span>変換とレイアウト

XAML レイアウトでは、変換はレイアウト パスの完了後に適用されます。利用可能なスペースの計算や他のレイアウトに関する決定は、変換が適用される前に行われています。 レイアウトが最優先されるため、レイアウト時にスペースを割り当てる、[**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid) セルなどのレイアウト コンテナー内にある要素を変換した場合に、予期しない結果になる可能性があります。 変換された要素は、途中で切れたり隠されたりことがあります。親コンテナー内のスペースを分割するとき、変換後のサイズを計算していなかったスペースに描画されるためです。 変換結果を試し、いくつかの設定の調整が必要になる場合があります。 たとえば、アダプティブ レイアウトやスター サイズ指定に頼るのではなく、**Center** プロパティを変更したり、レイアウト スペースの固定ピクセル値を宣言したりすることで、親が十分なスペースを確実に割り当てるようにすることが必要になる場合があります。

**移行に注意してください。** Windows Presentation Foundation (WPF) が、 **LayoutTransform**レイアウト パスの前に変換を適用するプロパティ。 ただし、Windows ランタイム XAML では **LayoutTransform** のプロパティがサポートされていません。 (Microsoft Silverlight にも、このプロパティはありませんでした。)

## <a name="span-idapplyingatransformtoauielementspanspan-idapplyingatransformtoauielementspanspan-idapplyingatransformtoauielementspanapplying-a-transform-to-a-ui-element"></a><span id="Applying_a_transform_to_a_UI_element"></span><span id="applying_a_transform_to_a_ui_element"></span><span id="APPLYING_A_TRANSFORM_TO_A_UI_ELEMENT"></span>UI 要素への変換の適用

変換をオブジェクトに適用するときは、通常、適用して [**UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) プロパティを設定します。 このプロパティを設定すると、オブジェクトは、文字どおりのピクセル単位では変更されません。 このプロパティにより、そのオブジェクトが存在するローカル座標空間内で変換が適用されます。 その後、レンダリング ロジックとレイアウト後処理により、結合された座標空間がレンダリングされます。これにより、オブジェクトの外観が変わり、そのレイアウト位置も変わる可能性があります ([**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) が適用された場合)。

既定では、レンダリングされる各変換の中心点はターゲット オブジェクトのローカル座標系の原点 (0,0) になります。 その唯一の例外は、中心点を設定するプロパティのない [**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) です。平行移動の結果は、どこを中心点としても同じであるためです。 しかしその他の各変換には、**CenterX** と **CenterY** の値を設定するプロパティがあります。

使用して変換を使用するたびに[ **UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform)に別のプロパティがあることに注意してください[ **UIElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement)です。変換の動作方法に影響します。[**RenderTransformOrigin**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransformorigin)します。 **RenderTransformOrigin** で宣言するのは、変換全体を要素の既定点 (0,0) に適用するか、その要素の相対座標空間内のその他の原点に適用するかです。 一般的な要素の場合、(0,0) では変換が左上隅に適用されます。 求める効果によっては、変換の **CenterX** と **CenterY** の値を調整するのではなく、**RenderTransformOrigin** を変更することを選ぶこともできます。 **RenderTransformOrigin** と **CenterX** / **CenterY** の値の両方を適用する場合、まったく予期しない結果になる可能性があることに注意してください。特に、いずれかの値をアニメーション化している場合です。

ヒット テストの目的で、変換が適用されたオブジェクトは、x-y スペースでのその外観と一貫した、予期された方法で入力に応答し続けます。 たとえば、[**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) を使って、UI で [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) を横方向に 400 ピクセル移動した場合は、**Rectangle** が視覚的に表示される位置をユーザーが押すと、その [**Rectangle** が **PointerPressed**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.pointerpressed) イベントに応答します。 平行移動前に **Rectangle** があった位置をユーザーが押した場合、false イベントが発生することはありません。 ヒット テストに影響を与える z インデックスについて考えた場合、変換の適用による違いはありません。x-y スペース内のある位置の入力イベントをどの要素が処理するかを制御する z インデックスは、コンテナー内での子の宣言順に従って評価されます。 その順序は、通常、XAML で要素を宣言する順序と同じです。ただし、[**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas) オブジェクトの子要素については、[**Canvas.ZIndex**](https://docs.microsoft.com/previous-versions/windows/silverlight/dotnet-windows-silverlight/cc190397(v%3Dvs.95)) 添付プロパティを子要素に適用して、順序を調整できます。

## <a name="span-idothertransformpropertiesspanspan-idothertransformpropertiesspanspan-idothertransformpropertiesspanother-transform-properties"></a><span id="Other_transform_properties"></span><span id="other_transform_properties"></span><span id="OTHER_TRANSFORM_PROPERTIES"></span>その他の変換プロパティ

-   [**Brush.Transform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.transform)、 [ **Brush.RelativeTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.brush.relativetransform):これらの影響を与える方法、 [**ブラシ**](/uwp/api/Windows.UI.Xaml.Media.Brush)座標領域内の空間を使用する、**ブラシ**foregrounds や背景などのビジュアルのプロパティの設定に適用されます。 これらの変換は、ほとんどの一般的なブラシ (通常は [**SolidColorBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SolidColorBrush) で単色を設定する) には関連していませんが、[**ImageBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ImageBrush) または [**LinearGradientBrush**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.LinearGradientBrush) で領域に描画するときには有用な場合があります。
-   [**Geometry.Transform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.geometry.transform):このプロパティを使用してそのジオメトリを使用する前にジオメトリへの変換を適用する場合があります、 [ **Path.Data** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.shapes.path.data)プロパティの値。

## <a name="span-idanimatingatransformspanspan-idanimatingatransformspanspan-idanimatingatransformspananimating-a-transform"></a><span id="Animating_a_transform"></span><span id="animating_a_transform"></span><span id="ANIMATING_A_TRANSFORM"></span>変換をアニメーション化

[**変換**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform)オブジェクトをアニメーション化することができます。 **Transform** をアニメーション化するには、アニメーション化するプロパティに互換性のある種類のアニメーションを適用します。 これは、通常、[**DoubleAnimation**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Animation.DoubleAnimation) オブジェクトまたは [**DoubleAnimationUsingKeyFrames**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.animation.doubleanimationusingkeyframes) オブジェクトを使ってアニメーションを定義していることを意味します。なぜなら、すべての変換プロパティは、型 [**Double**](https://docs.microsoft.com/dotnet/api/system.double?redirectedfrom=MSDN) であるためです。 [  **UIElement.RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) 値に使われる変換に影響を与えるアニメーションは、再生時間が 0 以外であっても、依存型アニメーションとは見なされません。 依存型アニメーションについて詳しくは、「[ストーリーボードに設定されたアニメーション](/windows/uwp/design/motion/storyboarded-animations)」をご覧ください。

プロパティをアニメーション化して、最終的な外観という点で同様の効果を生み出す場合、たとえば、[**TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform) を適用する代わりに、[**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) の [**Width**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Width) と [**Height**](/uwp/api/Windows.UI.Xaml.FrameworkElement.Height) をアニメーション化する場合、このようなアニメーションは、ほとんど常に依存型アニメーションとして扱われます。 アニメーションを有効にする必要があるため、パフォーマンスに大きな問題が発生する可能性があります。特に、ユーザーの操作をサポートしながら、そのオブジェクトをアニメーション化する場合です。 そのため、望ましいのは、変換を使ってアニメーション化することです。この場合は、他のどのプロパティもアニメーション化されず、アニメーションが依存型アニメーションとして扱われることはありません。

変換をターゲット設定するには、既にある [**Transform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform) を [**RenderTransform**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.rendertransform) の値にする必要があります。 通常は、適切な種類の変換の要素を、場合によってはその変換にプロパティを設定せずに、最初の XAML に配置します。

通常は、間接的なターゲット設定の手法を使って変換のプロパティにアニメーションを適用します。 間接的なターゲット設定の構文について詳しくは、「[ストーリーボードに設定されたアニメーション](/windows/uwp/design/motion/storyboarded-animations)」と「[プロパティ パス構文](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax)」をご覧ください。

コントロールの既定のスタイルで、表示状態の動作の一部として変換のアニメーションを定義する場合があります。 たとえば、[**ProgressRing**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ProgressRing) の表示状態で、アニメーション化した [**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) 値を使って、リングのドットを "回転" させます。

ここでは、変換をアニメーション化する方法の簡単な例を示します。 この例では、[**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform) の [**Angle**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.rotatetransform.angle) をアニメーション化して、その視覚的な中心点の周りで [**Rectangle**](/uwp/api/Windows.UI.Xaml.Shapes.Rectangle) を回転させます。 また、この例では、**RotateTransform** に名前を付けません。間接的なアニメーションのターゲット設定には不要なためです。しかしその代わりに、変換には名前を付けませんが、変換が適用される要素に名前を付けることで、`(UIElement.RenderTransform).(RotateTransform.Angle)` などの間接的なターゲット設定を使うことができます。

```xml
<StackPanel Margin="15">
  <StackPanel.Resources>
    <Storyboard x:Name="myStoryboard">
      <DoubleAnimation
       Storyboard.TargetName="myTransform"
       Storyboard.TargetProperty="Angle"
       From="0" To="360" Duration="0:0:5" 
       RepeatBehavior="Forever" />
    </Storyboard>
  </StackPanel.Resources>
  <Rectangle Width="50" Height="50" Fill="RoyalBlue"
   PointerPressed="StartAnimation">
    <Rectangle.RenderTransform>
      <RotateTransform x:Name="myTransform" Angle="45" CenterX="25" CenterY="25" />
    </Rectangle.RenderTransform>
  </Rectangle>
</StackPanel>
```

```xml
void StartAnimation (object sender, RoutedEventArgs e) {
    myStoryboard.Begin();
}
```

## <a name="span-idaccountingforcoordinateframesofreferenceatruntimespanspan-idaccountingforcoordinateframesofreferenceatruntimespanspan-idaccountingforcoordinateframesofreferenceatruntimespanaccounting-for-coordinate-frames-of-reference-at-run-time"></a><span id="Accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="accounting_for_coordinate_frames_of_reference_at_run_time"></span><span id="ACCOUNTING_FOR_COORDINATE_FRAMES_OF_REFERENCE_AT_RUN_TIME"></span>実行時に、座標の基準のアカウンティング

[**UIElement** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.UIElement)という名前のメソッドを持つ[ **TransformToVisual**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.)、生成することができます、 [**変換**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform)2 つの UI 要素の座標のフレームの参照を相互に関連付けます。 このメソッドを使って要素をアプリの既定の参照座標系に比較できます。この場合は最初のパラメーターとしてルート ビジュアルを渡します。 この方法が役立つことがあるのは、別の要素から入力イベントをキャプチャする場合や、実際にレイアウト パスを要求せずにレイアウト動作を予測しようとする場合です。

ポインター イベントから取得したイベント データを使うと、[**GetCurrentPoint**](https://docs.microsoft.com/uwp/api/windows.ui.input.pointerpoint.getcurrentpoint) メソッドにアクセスできます。このメソッドで、参照座標系をアプリの既定のものではなく特定の要素のものに変更するように、*relativeTo* パラメーターを指定できます。 これにより、平行移動変換が内部で適用され、戻り値となる [**PointerPoint**](https://docs.microsoft.com/uwp/api/Windows.UI.Input.PointerPoint) オブジェクトの作成時に x-y 座標データが変換されます。

## <a name="span-iddescribingatransformmathematicallyspanspan-iddescribingatransformmathematicallyspanspan-iddescribingatransformmathematicallyspandescribing-a-transform-mathematically"></a><span id="Describing_a_transform_mathematically"></span><span id="describing_a_transform_mathematically"></span><span id="DESCRIBING_A_TRANSFORM_MATHEMATICALLY"></span>数学的変換を記述します。

変換は、変換マトリックスの観点から説明できます。 3×3 マトリックスは、2 次元の x-y 平面での変換を記述するために使われます。 アフィン変換マトリックスでは、平行移動前の任意の数の線形変換 (回転やスキュー (せん断) など) を 1 つのマトリックスの乗算にまとめることができます。 アフィン変換マトリックスの最後の列は (0, 0, 1) に等しいため、最初の 2 列のメンバーのみを数学的記述で指定する必要があります。

変換の数学的記述が役立つことがあるのは、数学的な背景知識がある場合や、やはりマトリックスを使って座標空間の変換を記述するグラフィックス プログラミング手法に精通している場合です。 [**変換**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform)-派生クラスでは、express 3 × 3 のマトリックスの観点から直接変換することができます。[**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform)します。 **MatrixTransform**が、 [**マトリックス**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrixtransform.matrix)プロパティで、6 つのプロパティを持つ構造を保持します。[**M11**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m11)、 [ **M12**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m12)、 [ **M21**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m21)、 [ **m22...** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m22)、 [ **OffsetX** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsetx)と[ **OffsetY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsety)します。 [  **Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix) プロパティには **Double** 値が使われ、各プロパティはアフィン変換マトリックスの 6 つの該当する値 (列 1 および 2) に対応します。

|                                             |                                             |     |
|---------------------------------------------|---------------------------------------------|-----|
| [**M11**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m11)         | [**M21**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m21)         | 0   |
| [**M12**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m12)         | [**M22**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.m22)         | 0   |
| [**OffsetX**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsetx) | [**offsetY**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.matrix.offsety) | 1   |

 

[  **TranslateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.TranslateTransform)、[**ScaleTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.ScaleTransform)、[**RotateTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.RotateTransform)、または [**SkewTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.SkewTransform) の各オブジェクトで記述できる変換は、[**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform) と [**Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix) 値で同様に記述できます。 ただし、一般的に使われるのは、**TranslateTransform** などです。それらの変換クラスのプロパティは、**Matrix** でベクター成分を設定するよりも、概念化が簡単なためです。 また、変換の個別のプロパティをアニメーション化することも簡単です。**Matrix** は実際には構造体であり、[**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) ではありません。したがって、アニメーション化された個々の値をサポートすることはできません。

変換演算の適用を可能にする XAML 設計ツールには、結果を [**MatrixTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.MatrixTransform) としてシリアル化するものもあります。 この場合に最善と思われる方法は、[**Matrix**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Matrix) の値を XAML で直接操作しようとすることではなく、もう一度同じ設計ツールを使って変換結果を変更し、もう一度 XAML でシリアル化することです。

## <a name="span-id3-dtransformsspanspan-id3-dtransformsspanspan-id3-dtransformsspan3-d-transforms"></a><span id="3-D_transforms"></span><span id="3-d_transforms"></span><span id="3-D_TRANSFORMS"></span>3-D 変換

Windows 10 では、XAML に新しいプロパティである [**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d) が導入されました。このプロパティを使用すると、UI で使用する 3D 効果を作成できます。 そのためには、[**PerspectiveTransform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.media3d.perspectivetransform3d) を使用して共有 3D 視点、つまり "カメラ" をシーンに追加し、[**CompositeTransform**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.CompositeTransform) を使用する場合のように、[**CompositeTransform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.media.media3d.compositetransform3d) を使用して 3D 空間の要素を変換します。 3D 変換を実装する方法の説明については、[**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d) のページをご覧ください。

 1 つのオブジェクトにのみ適用されるもっと簡単な 3D 効果については、[**UIElement.Projection**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.projection) プロパティを使用できます。 このプロパティの値として [**PlaneProjection**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.PlaneProjection) を使用することは、固定された視点の変換と 1 つ以上の 3D 変換を要素に適用することに相当します。 この種類の変換については、「[XAML UI 用の 3-D 遠近効果](3-d-perspective-effects.md)」でより詳しく説明しています。

## <a name="span-idrelatedtopicsspanrelated-topics"></a><span id="related_topics"></span>関連トピック

* [**UIElement.Transform3D**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.uielement.transform3d)
* [XAML UI の 3-d 効果](3-d-perspective-effects.md)
* [**変換**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Media.Transform)
 

 





