---
description: Windows アプリは、PC、モバイル デバイス、その他の多くの種類のデバイスで同じ外観を共有します。 ユーザー インターフェイス、入力パターン、操作パターンは非常に類似しており、デバイス間を移行するユーザーには使い慣れたエクスペリエンスは歓迎されるはずです。
title: UWP への Windows Phone Silverlight のフォーム ファクターと UX の移植
ms.assetid: 96244516-dd2c-494d-ab5a-14b7dcd2edbd
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 03a994930e956cb3c2e775c32e77c6e62b526a17
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67322312"
---
#  <a name="porting-windowsphone-silverlight-to-uwp-for-form-factor-and-ux"></a>UWP への Windows Phone Silverlight のフォーム ファクターと UX の移植


前のトピックは、「[ビジネス レイヤーとデータ レイヤーの移植](wpsl-to-uwp-business-and-data.md)」でした。

Windows アプリは、PC、モバイル デバイス、その他の多くの種類のデバイスで同じ外観を共有します。 ユーザー インターフェイス、入力パターン、操作パターンは非常に類似しており、デバイス間を移行するユーザーには使い慣れたエクスペリエンスは歓迎されるはずです。 物理サイズ、既定の向き、およびユニバーサル Windows プラットフォーム (UWP) アプリを有効ピクセルの解像度の係数などのデバイス間の相違点は、Windows 10 でレンダリングされます。 さいわいなことに、これらの大変な作業の多くは、有効ピクセルなどのスマートな概念を用いてシステムにより自動的に処理されます。

## <a name="different-form-factors-and-user-experience"></a>フォーム ファクターとユーザー エクスペリエンスの相違

デバイスによってアスペクト比はさまざまであり、縦と横の解像度も複数あります。 UWP アプリのインターフェイス、テキスト、アセットなどの視覚的要素はどのように調整されるのでしょうか。 マウスとキーボード入力に加えて、タッチをサポートするにはどうすればよいでしょうか。 そして、さまざまな視聴距離に対してさまざまなサイズのデバイス上でのタッチをサポートするアプリで、コントロールの適切なサイズとタッチ ターゲットの適切なピクセル密度を共に確保し、*さらに*さまざまな距離でコンテンツを読み取りできるようにするにはどうすればよいでしょうか。 ここでは、このために理解する必要のあることについて説明します。

## <a name="what-is-the-size-of-a-screen-really"></a>実際の画面のサイズ

簡単に言えば、これは主観的です。ディスプレイの客観的なサイズだけでなく、目視する距離にも依存するためです。 ここで主観的とは、ユーザーの側に立つ必要があることを意味します。これは、優れたアプリの開発者が常に行うことです。

客観的には、画面はインチと物理的な (RAW) ピクセル単位で測定されます。 この両方のメトリックがわかれば、1 インチに適合するピクセル数がわかります。 これは、ピクセル密度、DPI (1 インチあたりのドット数)、または PPI (1 インチあたりのピクセル数) と呼ばれています。 また、DPI の逆数は、1 インチを分母とするピクセルの実際のサイズです。 ピクセル密度はまた、*解像度*とも呼ばれます。ただし解像度は、漠然とピクセル数を意味する用語として使われることも少なくありません。

視聴距離が増加すると、それに伴ってこうしたすべての客観的なメトリックが小さく*見え*、また画面の*有効サイズ*と*有効解像度*に帰着します。 電話は通常、最も近くで目視されます。次にタブレット、PC モニター、そして最も遠くで見られるのが [Surface Hub](https://www.microsoft.com/surface/devices/surface-hub) デバイスとテレビです。 補正のために、デバイスは視聴距離に対して客観的に大きくなる傾向があります。 UI 要素のサイズを設定する場合、有効ピクセル (epx) と呼ばれる単位でそのサイズを設定します。 Windows 10 が最適なエクスペリエンスを表示するための物理ピクセルに UI 要素の最適なサイズを計算するにアカウント DPI、およびデバイスからの一般的な表示距離になります。 詳しくは、「[表示/有効ピクセル、視聴距離、スケール ファクター](wpsl-to-uwp-porting-xaml-and-ui.md)」をご覧ください。

ただし、多くの異なるデバイスでアプリをテストし、各エクスペリエンスを自分で確認することをお勧めします。

## <a name="touch-resolution-and-viewing-resolution"></a>タッチ解像度と表示解像度

アフォーダンス (UI ウィジェット) はタッチに対して適切なサイズであることが必要です。 したがってタッチ ターゲットでは、さまざまなピクセル密度のさまざまなデバイスにわたって、実際のサイズを多かれ少なかれ保持する必要があります。 ここでも有効ピクセルが役立ちます。有効ピクセルは、タッチ ターゲットにとって理想的になるように、ほとんど一定の実サイズを実現するために、ピクセル密度を考慮して異なるデバイス上でスケーリングします。

テキストは、読み取るための適切なサイズであることが必要です (経験則では、50 cm の視聴距離で 12 ポイントのテキストが適切です)。また、画像は視聴距離に対して適切なサイズと有効な解像度であることが必要です。 さまざまなデバイスで、該当する同じ有効ピクセルのスケーリングにより、UI 要素が適切なサイズで、読みやすく維持されます。 テキストやその他のベクター ベースのグラフィックスは、自動的に非常に適切にスケーリングします。 開発者が単一の大きなサイズでアセットを提供する場合、ラスター (ビットマップ) ベースのグラフィックも自動的にスケーリングします。 ただし、ターゲット デバイスの倍率に対して適切なサイズを自動的に読み込むことができるように、開発者は一連のサイズで各アセットを提供することをお勧めします。 詳しくは、「[表示/有効ピクセル、視聴距離、スケール ファクター](wpsl-to-uwp-porting-xaml-and-ui.md)」をご覧ください。

## <a name="layout-and-adaptive-visual-state-manager"></a>レイアウトとアダプティブな Visual State Manager

ここまでで、重要な画面サイズに関連する要素について説明しました。 次に、アプリのレイアウトと、可能な場合に画面を幅広く利用する方法について考えましょう。 画面の狭いモバイル デバイスで実行するように設計された非常に単純なアプリの次のようなページについて考えてみます。 より大きな画面では、このページはどのように見えるでしょうか。

![移植された Windows Phone ストア アプリ](images/wpsl-to-uwp-case-studies/c01-04-uni-phone-app-ported.png)

モバイル バージョンでは、書籍の一覧に最適な縦横比である縦向きのみに制限されます。また、モバイル デバイスで 1 列に最も適切に維持されるテキスト ページでも同様です。 しかし、PC とタブレットの画面はどの向きにも大きいため、このようなモバイル デバイスの制限は大型のデバイスでは不要であると考えられます。

光学的にアプリを拡大表示してモバイル バージョンを大きくするだけでは、デバイスとその追加領域を活用できず、ユーザーに対して適切な機能を提供しません。 同じコンテンツをより大きく表示するのではなく、より多くのコンテンツを表示することを検討する必要があります。 タブレットであっても、コンテンツの表示行数を増やすことができます。 広告など、さまざまなコンテンツを表示するために追加領域を使うことができます。また、リスト ボックスをリスト ビューに変更することや、領域で可能であれば複数の列に項目を折り返すことができます。 「[リスト ビュー コントロールとグリッド ビュー コントロールのガイドライン](https://docs.microsoft.com/windows/uwp/controls-and-patterns/lists)」をご覧ください。

リスト ビューおよびグリッド ビューなどの新しいコントロールのほかの Windows Phone Silverlight から確立されたレイアウト型のほとんどはユニバーサル Windows プラットフォーム (UWP) 対応をあります。 たとえば、[**Canvas**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Canvas)、[**Grid**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Grid)、[**StackPanel**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.StackPanel) などです。 こうしたレイアウトを使う UI の多くは、簡単に移植できるはずですが、さまざまなサイズのデバイスでサイズ変更と再レイアウトを自動的に行うために、こうしたレイアウト パネルの動的レイアウト機能を活用する方法を常に模索してください。

システム コントロールやレイアウト パネルに組み込まれている動的レイアウトを凌駕場合は、このと呼ばれる新しい Windows 10 機能使用できる[アダプティブ Visual State Manager](wpsl-to-uwp-porting-xaml-and-ui.md)します。

## <a name="input-modalities"></a>入力モダリティ

Windows Phone Silverlight インターフェイスは、タッチに固有です。 また、移植するアプリのインターフェイスでももちろんタッチをサポートしますが、マウスやキーボードなど他の入力モダリティをさらにサポートすることもできます。 UWP では、マウス、ペン、タッチ入力は*ポインター入力*として統合されています。 詳しくは、「[ポインター入力の処理](https://docs.microsoft.com/windows/uwp/input-and-devices/handle-pointer-input)」と「[キーボード操作](https://docs.microsoft.com/windows/uwp/input-and-devices/keyboard-interactions)」をご覧ください。

## <a name="maximizing-markup-and-code-re-use"></a>マークアップとコード再利用の最大化

さまざまなフォーム ファクターのデバイスをターゲットとする UI の共有に関する手法については、「[マークアップとコード再利用の最大化](wpsl-to-uwp-porting-to-a-uwp-project.md)」のリストをご覧ください。

## <a name="more-info-and-design-guidelines"></a>詳しい情報と設計のガイドライン

-   [UWP アプリをデザインします。](https://developer.microsoft.com/en-us/windows/apps/design)
-   [フォントのガイドライン](https://docs.microsoft.com/windows/uwp/controls-and-patterns/fonts)
-   [さまざまなフォーム ファクターの計画](https://docs.microsoft.com/windows/uwp/layout/screen-sizes-and-breakpoints-for-responsive-design)

## <a name="related-topics"></a>関連トピック

* [Namespace およびクラスのマッピング](wpsl-to-uwp-namespace-and-class-mappings.md)

