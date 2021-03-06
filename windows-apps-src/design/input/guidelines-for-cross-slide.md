---
Description: クロススライドは、スワイプ ジェスチャによる選択や、スライド ジェスチャによるドラッグ (移動) 操作をサポートするために使います。
title: クロススライドのガイドライン
ms.assetid: 897555e2-c567-4bbe-b600-553daeb223d5
ms.date: 10/25/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 833949effd311c707de8dd1823ec6eee06e91e87
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74257974"
---
# <a name="guidelines-for-cross-slide"></a>クロススライドのガイドライン




**重要な API**

-   [**クロススライド**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.crosssliding)
-   [**CrossSlideThresholds**](https://docs.microsoft.com/uwp/api/windows.ui.input.gesturerecognizer.crossslidethresholds)
-   [**Windows. UI. 入力**](https://docs.microsoft.com/uwp/api/Windows.UI.Input)

クロススライドは、スワイプ ジェスチャによる選択や、スライド ジェスチャによるドラッグ (移動) 操作をサポートするために使います。

## <a name="span-iddos_and_don_tsspanspan-iddos_and_don_tsspanspan-iddos_and_don_tsspandos-and-donts"></a><span id="Dos_and_don_ts"></span><span id="dos_and_don_ts"></span><span id="DOS_AND_DON_TS"></span>Dos とすべき


-   クロススライドは、単一の方向にスクロールするリストやコレクションに使います。
-   クロススライドは、タップ操作が別の目的で使われる場合に、項目を選ぶために使います。
-   キューに項目を追加するためにクロススライドを使わないでください。

## <a name="span-idadditional_usage_guidancespanspan-idadditional_usage_guidancespanspan-idadditional_usage_guidancespanadditional-usage-guidance"></a><span id="Additional_usage_guidance"></span><span id="additional_usage_guidance"></span><span id="ADDITIONAL_USAGE_GUIDANCE"></span>使用に関するその他のガイダンス


選択とドラッグは、1 方向 (垂直または水平) にパンできるコンテンツ領域内でだけ行うことができます。 これらの操作が機能するには、1 つのパン方向がロックされていて、ジェスチャがパン方向に対して垂直な方向に行われる必要があります。

ここでは、クロススライドを使ってオブジェクトを選び、ドラッグする方法を示します。 左の図は、スワイプ ジェスチャで距離のしきい値を超える前に指を離してオブジェクトを解放することで、項目が選択された状態を示しています。 右の図は、距離のしきい値を超えてスライド ジェスチャを行うことで、オブジェクトがドラッグされた状態を示しています。

![選択とドラッグ アンド ドロップのプロセスを示す図。](images/crossslide-mechanism.png)

クロススライド操作で使われるしきい値の距離を次の図に示します。

![選択とドラッグ アンド ドロップのプロセスを示すスクリーン ショット。](images/crossslide-threshold.png)

パンの機能を維持するために、選択操作とドラッグ操作は、2.7 mm (ターゲット解像度で約 10 ピクセル) という小さいしきい値を超えないと有効にならないしくみになっています。 この小さいしきい値は、クロススライドとパンの区別に使われるほか、タップ ジェスチャをクロススライドやパンと区別する目的でも使われます。

この図は、ユーザーが UI の要素にタッチしたときに、指の位置がわずかに下に動いてしまった状態を示しています。 しきい値がなければ、最初に垂直方向に移動しているため、この操作はクロススライドと解釈されてしまいます。 このしきい値があるおかげで、水平方向のパンと正しく解釈されます。

![選択かドラッグ アンド ドロップかを明確に区別するためのしきい値を示すスクリーン ショット。](images/crossslide-threshold2.png)

次に、クロススライド機能をアプリに含める際に考慮する必要がある、いくつかのガイドラインを示します。

クロススライドは、単一の方向にスクロールするリストやコレクションに使います。 詳しくは、「[ListView コントロールの追加](https://docs.microsoft.com/previous-versions/windows/apps/hh465382(v=win.10))」をご覧ください。

**注**  web ブラウザーや電子閲覧者などの2方向にコンテンツエリアをパンできる場合は、画像やハイパーリンクなどのオブジェクトのコンテキストメニューを呼び出すために、プレスアンドホールドの時間指定操作を使用する必要があります。

 

|                                                                                         |                                                                                         |
|-----------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------|
| ![水平方向にパンする 2 次元のリスト](images/groupedlistview1.png)                | ![垂直方向にパンする 1 次元のリスト](images/listviewlistlayout.png)                |
| 水平方向にパンする 2 次元のリスト。 項目を選択または移動するには垂直方向にドラッグします。 | 垂直方向にパンする 1 次元のリスト。 項目を選択または移動するには水平方向にドラッグします。 |

 

### <span id="selection"></span><span id="SELECTION"></span>

**する**

選択は、1 つ以上のオブジェクトを起動またはアクティブ化せずにマークする操作です。 これは、マウスを 1 回クリックする操作、または Shift キーを押しながらクリックする操作 (オブジェクトが複数の場合) に相当します。

クロススライド選択を行うには、要素をタッチし、少しドラッグして放します。 この選択方法を使えば、他のタッチ インターフェイスで必要になるような、専用の選択モードや時間制限のある長押し操作は必要ありません。また、アクティブ化のためのタップ操作と競合することもありません。

クロススライド選択には、距離のしきい値のほかにも領域のしきい値があり、次の図に示すように範囲が 90° に制限されます。 この領域の外にドラッグすると、オブジェクトは選択されません。

![選択のしきい値の領域を示す図。](images/crossslide-selection.png)

クロススライド操作を補完する操作に、"自己説明" 操作とも呼ばれる時間制限のある長押し操作があります。 この補助操作でアクティブ化されるアニメーションによって、オブジェクトに対して実行できる操作が示されます。 不明瞭解消 UI について詳しくは、「[視覚的なフィードバックのガイドライン](guidelines-for-visualfeedback.md)」をご覧ください。

次のスクリーン ショットは、自己説明のアニメーションの動作を示しています。

1.  長押しして、自己説明操作のアニメーションを開始します。 項目が選ばれているかどうかによって、アニメーションで説明される内容が変わります。選ばれていない場合はチェック マークが付き、選ばれている場合はチェック マークが付きません。

    ![選択されていない状態を示すスクリーン ショット。](images/crossslide-selfreveal1.png)

2.  スワイプ ジェスチャ (上または下) を使って項目を選びます。

    ![選択のアニメーションを示すスクリーン ショット。](images/crossslide-selfreveal2.png)

3.  この時点で、項目が選ばれています。 スライド ジェスチャを使って選択動作を上書きし、項目を移動します。

    ![ドラッグ アンド ドロップのアニメーションを示すスクリーン ショット。](images/crossslide-selfreveal3.png)

主な操作が選択だけであるアプリケーションでは、選択にシングル タップを使います。 この場合、アクティブ化やナビゲーションのための標準のタップ操作と区別するために、クロススライドの自己説明のアニメーションが表示されます。

**バスケットの選択**

選択バスケットは、アプリの主要なリストやコレクションから選択された項目を視覚的に区別して動的に表す機能です。 これは選択された項目の追跡に役立つ機能で、次のようなアプリで使うと便利です。

-   項目を複数の場所から選択できる。
-   複数の項目を選択できる。
-   選択リストによって操作やコマンドが異なる。

選択バスケットの内容は、操作やコマンドの実行後も保持されます。 たとえば、ギャラリーから一連の写真を選択して各写真に色補正を適用し、それらの写真を何らかの方法で共有した場合、それらの項目は選択されたままになります。

アプリで選択バスケットを使わない場合は、操作やコマンドの実行後に現在の選択がクリアされます。 たとえば、再生リストから曲を選択して評価した後、その選択はクリアされます。

また、選択バスケットを使わない場合は、リストやコレクションで別の項目がアクティブ化されたときにも現在の選択がクリアされます。 たとえば、受信トレイのメッセージを選択すると、プレビュー ウィンドウが更新されます。 その後、受信トレイで別のメッセージを選択すると、前のメッセージの選択が取り消され、プレビュー ウィンドウが更新されます。

**行列**

キューと選択バスケットのリストは異なるものであるため、混同しないように注意してください。 主な違いは次のとおりです。

-   選択バスケットの項目のリストは、視覚的に表すことだけを目的としたものです。キューの項目は、特定の操作を想定してまとめられたものです。
-   選択バスケットでは同じ項目は 1 回しか表示できませんが、キューでは複数回表示できます。
-   選択バスケットの項目の順序は、選択の順序を表します。 キューの項目の順序は、機能に直接関連します。

これらの理由から、キューに項目を追加する目的でクロススライド選択操作を使わないでください。 キューに項目を追加するときは、代わりにドラッグ操作を使います。

### <span id="draganddrop"></span><span id="DRAGANDDROP"></span>

**抗力**

1 つまたは複数のオブジェクトを別の場所に移動するには、ドラッグを使います。

複数のオブジェクトを移動する必要がある場合は、ユーザーが複数の項目を選択してから、すべてを同時にドラッグできるようにします。

## <a name="span-idrelated_topicsspanrelated-articles"></a><span id="related_topics"></span>関連記事


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
 

 




