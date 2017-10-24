---
title: "ステンシル バッファー"
description: "ステンシル バッファーは、イメージ内のピクセルをマスクし、特殊効果を生成するために使用されます。"
ms.assetid: 544B3B9E-31E3-41DA-8081-CC3477447E94
keywords: "ステンシル バッファー"
author: PeterTurcan
ms.author: pettur
ms.date: 02/08/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
ms.openlocfilehash: 131b573d990db4d24f33b33c38e4534a7932571b
ms.sourcegitcommit: 909d859a0f11981a8d1beac0da35f779786a6889
translationtype: HT
---
# <a name="stencil-buffers"></a>ステンシル バッファー


*ステンシル バッファー*は、イメージ内のピクセルをマスクし、特殊効果を生成するために使用されます。 このマスクは、ピクセルを描画するかどうかを制御します。 このような特殊効果には、合成、デカール、ディゾルブ、フェード、スワイプ、輪郭とシルエット、両面ステンシルなどがあります。 代表的な効果の一部を次に示します。

ステンシル バッファーを使用すると、ピクセル単位でレンダー ターゲット サーフェスへの描画を有効にするか無効にするかを制御できます。 最も基本的なレベルでは、アプリケーションによってレンダリング対象のイメージのセクションをマスクして、表示されないようにすることができます。 アプリケーションでは、しばしば、ディゾルブ、デカール、輪郭などの特殊効果にステンシル バッファーを使用します。

ステンシル バッファー情報は、z バッファー データに埋め込まれます。

## <a name="span-idhowthestencilbufferworksspanspan-idhowthestencilbufferworksspanspan-idhowthestencilbufferworksspanhow-the-stencil-buffer-works"></a><span id="How_the_Stencil_Buffer_Works"></span><span id="how_the_stencil_buffer_works"></span><span id="HOW_THE_STENCIL_BUFFER_WORKS"></span>ステンシル バッファーのしくみ


Direct3D は、ステンシル バッファーのコンテンツをピクセル単位でテストします。 ターゲット サーフェスのピクセルごとに、ステンシル バッファー内の対応する値、ステンシル参照の値、ステンシル マスクの値を使用してテストを実行します。 テストに合格した場合は、Direct3D は操作を実行します。 テストは、次の手順に従って実行されます。

1.  ステンシル マスクを使用してステンシル参照値のビット単位の AND 操作を実行します。
2.  ステンシル マスクを使用して、現在のピクセルに対してステンシル バッファー値のビット単位の AND 操作を実行します。
3.  比較関数を使用して、手順 1 の結果と手順 2 の結果を比較します。

上記の手順は、次のコードで表されます。

```
(StencilRef &amp; StencilMask) CompFunc (StencilBufferValue &amp; StencilMask)
```

-   StencilRef はステンシル参照値を表します。
-   StencilMask はステンシル マスクの値を表します。
-   CompFunc は、比較関数です。
-   StencilBufferValue は、現在のピクセルのステンシル バッファーのコンテンツです。
-   アンパサンド (&) 記号はビット単位の AND 演算を表します。

ステンシルのテストに合格すると、現在のピクセルは、ターゲット サーフェスに書き込まれます。合格しない場合は、無視されます。 比較の既定の動作では、ビット単位の各演算がどのような結果になっても、ピクセルが書き込まれます。 この動作は、列挙型の値を変更して、目的の比較関数を指定することで、変更できます。

アプリケーションによって、ステンシル バッファーの操作をカスタマイズできます。 比較関数、ステンシル マスク、ステンシル参照値を設定できます。 また、ステンシル テストに合格または不合格になったときに、Direct3D が実行する操作を制御することもできます。

## <a name="span-idcompositingspanspan-idcompositingspanspan-idcompositingspancompositing"></a><span id="Compositing"></span><span id="compositing"></span><span id="COMPOSITING"></span>合成


ステンシル バッファーを使えば、アプリケーションは 2D または 3D イメージを 3D シーンに合成できます。 ステンシル バッファーのマスクを使って、レンダー ターゲット サーフェスの領域をオクルードします。 次に、テキストやビットマップなどの格納 2D 情報をオクルードされた領域に書き込むことができます。 別の方法として、アプリケーションでは追加 3D プリミティブをレンダー ターゲット サーフェスのステンシル マスクされた領域にレンダリングできます。 この場合、シーン全体をレンダリングすることもできます。

ゲームでは、複数の 3D シーンを合成することがよくあります。 たとえば、ドライビング ゲームでは通常バックミラーを表示します。 バックミラーには、運転者の背後の 3D シーンの表示が含まれます。 したがって、バックミラーは、本質的には運転者の前方の風景と合成された 第 2 の 3D シーンと言えます。

## <a name="span-iddecalingspanspan-iddecalingspanspan-iddecalingspandecaling"></a><span id="Decaling"></span><span id="decaling"></span><span id="DECALING"></span>デカール


Direct3D アプリケーションでは、デカールを使用して、レンダー ターゲット サーフェスに描画される特定のプリミティブ イメージのピクセルを制御します。 アプリケーションはプリミティブのイメージにデカールを適用して、同一平面上のポリゴンを適切にレンダリングできるようにします。

たとえば、道路にタイヤの跡と黄色い線を付ける場合、その跡は道路の上に直接表示する必要があります。 ただし、タイヤ跡と道路の z 値は同一です。 したがって、深度バッファーでは、これら 2 つを明確に分離できない場合があります。 背面のプリミティブの一部のピクセルが前面のプリミティブの手前にレンダリングされたり、その逆になったりする場合があります。 出力されるイメージは、フレームが変わるたびにちらついているように見えます。 この現象は、*z ファイティング*または*フリマリング*と呼ばれます。

この問題を解決するには、ステンシルを使用して、デカールが表示される背面プリミティブのセクションをマスクします。 z バッファリングをオフにし、レンダー ターゲット サーフェスのマスク オフされた領域に前面プリミティブのイメージをレンダリングします。

複数のテクスチャのブレンドを使用することでこの問題を解決できますが、その場合、アプリケーションが生成するその他の特殊効果の数が制限されます。 ステンシル バッファーを使用してデカールを適用すると、他の効果のテクスチャ ブレンド ステージが不要になります。

## <a name="span-iddissolvesfadesandswipesspanspan-iddissolvesfadesandswipesspanspan-iddissolvesfadesandswipesspandissolves-fades-and-swipes"></a><span id="Dissolves__fades__and_swipes"></span><span id="dissolves__fades__and_swipes"></span><span id="DISSOLVES__FADES__AND_SWIPES"></span>ディゾルブ、フェード、およびスワイプ


ディゾルブ、スワイプ、フェードなど、映画やビデオでよく使用される特殊効果が、ますますアプリケーションでも使用されるようになっています。

ディゾルブでは、スムーズなフレーム シーケンスで、1 つのイメージが徐々に別のイメージに置き換えられます。 Direct3D では複数のテクスチャのブレンドを使用して同じ効果を生む方法が提供していますが、ディゾルブにステンシル バッファーを使用するアプリケーションでは、ディゾルブを使用しながら、他の効果にテクスチャ ブレンド機能を使用できます。

アプリケーションでディゾルブを行うときは、2 つの異なるイメージをレンダリングする必要があります。 ステンシル バッファーを使用して、レンダー ターゲット サーフェスに描画される各イメージのピクセルを制御します。 一連のステンシル マスクを定義し、後続のフレームのステンシル バッファーにコピーできます。 または、最初のフレームに基本のステンシル マスクを定義して、徐々にそれを変えていくこともできます。

アプリケーションは、ディゾルブを開始するときに、最初のイメージのピクセルのほとんどが、ステンシル テストに合格するように、ステンシル機能とステンシル マスクを設定できます。 最後のイメージのピクセルの大半は、ステンシル テストに不合格になります。 後続のフレームでステンシル マスクは更新されて、テストに合格する最初のイメージのピクセルは徐々に少なくなります。 フレームが進むにつれて、テストに不合格になる最後のイメージのピクセルは徐々に少なくなります。 この方法では、アプリケーションは任意のディゾルブ パターンを使用して、ディゾルブを実行できます。

フェード インやフェード アウトは、ディゾルブの特殊なケースです。 フェード インでは、ステンシル バッファーを使用して黒または白のイメージから 3D シーンのレンダリングへとディゾルブします。 フェード アウトは反対に、3D シーンのレンダリングから始めて、黒または白のイメージにディゾルブします。 フェードは、利用したい任意のパターンを使用して実現できます。

Direct3D アプリケーションでは、スワイプに同様の手法を使用します。 たとえば、アプリケーションが左から右へのスワイプを実行する場合、最初のイメージの上に最後のイメージが左から右にスライドする形で表示されます。 ディゾルブと同様に、後続のフレームでステンシル バッファーに読み込まれる一連のステンシル マスクを定義するか、最初のステンシル マスクを順次変更します。 ステンシル マスクは、最初のイメージのピクセルの書き込みを無効にし、最後のイメージのピクセルの書き込みを有効にするために使用されます。

スワイプは、ディゾルブよりもやや複雑で、アプリケーションがスワイプの逆の順序で最後のイメージのピクセルを読み取る必要があります。 つまり、スワイプが左から右に移動する場合、アプリケーションは最初のイメージのピクセルを右から左に読み取る必要があります。

## <a name="span-idoutlinesandsilhouettesspanspan-idoutlinesandsilhouettesspanspan-idoutlinesandsilhouettesspanoutlines-and-silhouettes"></a><span id="Outlines_and_silhouettes"></span><span id="outlines_and_silhouettes"></span><span id="OUTLINES_AND_SILHOUETTES"></span>輪郭とシルエット


輪郭処理やシルエット処理など、より抽象的なエフェクトにステンシル バッファーを使用することができます。

アプリケーションがステンシル マスクを形状は同じでもやや小さいプリミティブのイメージに適用する場合、結果のイメージにはプリミティブの輪郭しか含まれません。 その場合、ステンシルでマスクされたイメージの領域を単色で塗りつぶすことで、プリミティブが浮き上がって見えるようにすることができます。

ステンシル マスクが、レンダリングするプリミティブと同じサイズおよび形状の場合、出力されるイメージではプリミティブの位置が穴になります。 その場合、穴を黒で塗りつぶすことで、プリミティブのシルエットを作成できます。

## <a name="span-idtwo-sidedstencilspanspan-idtwo-sidedstencilspanspan-idtwo-sidedstencilspantwo-sided-stencil"></a><span id="Two-sided_stencil"></span><span id="two-sided_stencil"></span><span id="TWO-SIDED_STENCIL"></span>2 面ステンシル


シャドウ ボリュームは、ステンシル バッファーで影を描画するために使用します。 オクルーディング ジオメトリによってキャストされたシャドウ ボリュームは、シルエットの縁を計算し、ライトと反対側の 3D ボリューム セットに押し出すことによって計算されます。 これらのボリュームはその後、ステンシル バッファーに 2 回レンダリングされます。

最初のレンダリングでは、前向きのポリゴンが描画され、ステンシル バッファーの値が増加します。 2 回目のレンダリングでは、シャドウ ボリュームの後ろ向きのポリゴンが描画され、ステンシル バッファーの値が減少します。 通常、増加したり減少したりする値はすべて、他方がない場合はキャンセルされます。 ただし、通常のジオメトリでシーンが既にレンダリングされているため、シャドウ ボリュームがレンダリングされるときに、ピクセルのいくつかが Z バッファー テストで不合格になります。 ステンシル バッファーに残された値は、シャドウのピクセルに対応します。 ステンシル バッファーの残りの内容は、すべてを覆う大きな黒のクワッドをシーンにアルファ ブレンディングするために、マスクとして使用されます。 マスクとして機能するステンシル バッファーを使用すると、シャドウ内のピクセルが暗くなります。

これは、シャドウ ジオメトリが光源ごとに 2 回描画されることを意味するため、GPU の頂点処理のスループットに影響します。 2 面ステンシル機能は、この状況を軽減することを目的として設計されています。 この方法には、(次の) 2 組のステンシル ステートがあります。一方は前向きの三角形のそれぞれに設定され、もう一方は後ろ向きの三角形に設定されます。 このようにして、光源ごとにシャドウ ボリューム単位で 1 つのパスのみが描画されます。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>関連トピック


[深度バッファーとステンシル バッファー](depth-and-stencil-buffers.md)

 

 



