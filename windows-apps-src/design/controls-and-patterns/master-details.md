---
Description: マスターと詳細パターンでは、マスター リストと、現在選択されている項目の詳細が表示されます。 このパターンは、メールや連絡先リストまたはアドレス帳によく使用されます。
title: マスターと詳細
ms.assetid: 45C9FE8B-ECA6-44BF-8DDE-7D12ED34A7F7
label: Master/details
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 0b7c21e5e6305c7b2941b46db562c93da0fdfe39
ms.sourcegitcommit: 26bb75084b9d2d2b4a76d4aa131066e8da716679
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/06/2020
ms.locfileid: "75684386"
---
# <a name="masterdetails-pattern"></a>マスターと詳細パターン

 

マスターと詳細パターンには、コンテンツのマスター ウィンドウ (通常は[リスト ビュー](lists.md)を含む) と詳細ウィンドウがあります。 マスター リストの項目を選ぶと、詳細ウィンドウが更新されます。 このパターンは、メールやアドレス帳によく使われます。

> **重要な API**:[ListView クラス](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)、[SplitView クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.splitview)

![マスターと詳細パターンの例](images/HIGSecOne_MasterDetail.png)

## <a name="is-this-the-right-pattern"></a>これは適切なパターンですか?

次の場合は、マスターと詳細パターンが適しています。

-   メール アプリ、アドレス帳、またはリストと詳細レイアウトに基づくアプリを構築する。
-   大きいコンテンツのコレクションを検索して優先順位を設定する。
-   コンテキスト間を前後に移動しながら、リストから項目をすばやく追加および削除できるようにする。

## <a name="choose-the-right-style"></a>適切なスタイルの選択

マスターと詳細パターンを実装するとき、利用可能な画面領域の量に基づいて、上下に並べて表示するスタイルまたは左右に並べて表示するスタイルのいずれかを使うことをお勧めします。

| 利用可能なウィンドウの幅 | 推奨スタイル |
|------------------------|-------------------|
| 320 epx から 640 epx        | 上下に並べて表示           |
| 641 epx 以上       | 左右に並べて表示      |

 
## <a name="stacked-style"></a>上下に並べて表示するスタイル

上下に並べて表示するスタイルでは、マスターまたは詳細のうち 1 つのウィンドウだけを一度に表示できます。

![上下に並べて表示モードのマスター詳細](images/patterns-md-stacked.png)

ユーザーがマスター ウィンドウで作業を始め、マスター リストで項目を選んで詳細ウィンドウに "ドリルダウン" します。 ユーザーには、マスター ビューと詳細ビューが別々の 2 つのページに存在するように表示されます。

### <a name="create-a-stacked-masterdetails-pattern"></a>上下に並べて表示のマスターと詳細パターンの作成

上下に並べて表示のマスターと詳細パターンを作る方法の 1 つは、マスター ウィンドウと詳細ウィンドウにそれぞれ別のページを使うことです。 マスター ビューを 1 つのページに配置し、詳細ウィンドウを別のページに配置します。

![上下に並べて表示するスタイルのマスター詳細のパーツ](images/patterns-md-stacked-parts.png)

マスター ビュー ページでは、イメージとテキストが含まれるリストを表示するのに[リスト ビュー](lists.md) コントロールが適しています。 

詳細ビュー ページの場合、最も意味のある[コンテンツ要素](../layout/layout-panels.md)を使います。 多くの個別フィールドがある場合は、**グリッド** レイアウトを使って要素をフォームに配置することを検討します。

ページ間の移動については、「[UWP アプリのナビゲーション履歴と前に戻る移動](../basics/navigation-history-and-backwards-navigation.md)」を参照してください。

## <a name="side-by-side-style"></a>左右に並べて表示するスタイル

左右に並べて表示するスタイルでは、マスター ウィンドウと詳細ウィンドウを同時に表示できます。

![マスターと詳細パターン](images/patterns-masterdetail-400x227.png)

マスター ウィンドウのリストには、現在選択されている項目を示すための選択ビジュアルがあります。 マスター リストで新しい項目を選ぶと、詳細ウィンドウが更新されます。

### <a name="create-a-side-by-side-masterdetails-pattern"></a>左右に並べて表示するマスターと詳細パターンの作成

左右に並べて表示するマスターと詳細パターンを作成する方法の 1 つは、[分割ビュー](split-view.md) コントロールを使用することです。 分割ビューのウィンドウにマスター ビューを配置し、分割ビューのコンテンツに詳細ビューを配置します。

![マスター詳細の分割ビューのパーツ](images/patterns_md_splitview_parts.png)

マスター ウィンドウでは、イメージとテキストが含まれるリストを表示するのに[リスト ビュー](lists.md) コントロールが適しています。

詳細コンテンツの場合、最も意味のある[コンテンツ要素](../layout/layout-panels.md)を使います。 多くの個別フィールドがある場合は、**グリッド** レイアウトを使って要素をフォームに配置することを検討します。

## <a name="adaptive-layout"></a>アダプティブ レイアウト

マスターと詳細パターンを任意の画面サイズに実装するには、[アダプティブ レイアウト](../layout/layouts-with-xaml.md)で応答性の高い UI を作成します。

![アダプティブのマスター詳細レイアウト](images/patterns_masterdetail.png)

### <a name="create-an-adaptive-masterdetails-pattern"></a>アダプティブのマスターと詳細パターンの作成
アダプティブ レイアウトを作成するには、UI にさまざまな [**VisualStates**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.visualstate) を定義し、[**AdaptiveTriggers**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.AdaptiveTrigger) を使用してさまざまな状態のブレークポイントを宣言します。

## <a name="get-the-sample-code"></a>サンプル コードの入手

次のサンプルでは、アダプティブ レイアウトを使用してマスターと詳細パターンを実装し、静的なリソース、データベース リソース、オンライン リソースに対するデータ バインディングを示します。 
- [マスター/詳細のサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlMasterDetail) 
- [マスター/詳細と選択のサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlListView)
- [Windows Template Studio マスター/詳細のサンプル](https://github.com/Microsoft/WindowsTemplateStudio/tree/master/templates/Uwp/Pages/MasterDetail)
- [顧客注文データベースのサンプル](https://github.com/Microsoft/Windows-appsample-customers-orders-database)
- [RSS リーダーのサンプル](https://github.com/Microsoft/Windows-appsample-rssreader)

## <a name="related-articles"></a>関連記事

- [リスト](lists.md)
- [検索](search.md)
- [アプリ バーとコマンド バー](app-bars.md)
- [ListView クラス](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)
- [SplitView クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.splitview)
