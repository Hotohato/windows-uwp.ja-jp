---
Description: 反転リストを使って新しい項目を下部に追加します。
title: 反転リスト
label: Inverted lists
template: detail.hbs
ms.date: 05/19/2017
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 52c1d63d-69c1-48d6-a234-6f39296e4bfd
pm-contact: predavid
design-contact: kimsea
doc-status: Published
ms.localizationpriority: medium
ms.openlocfilehash: c552109b243688c2618425adce797c4d208eac31
ms.sourcegitcommit: aaa4b898da5869c064097739cf3dc74c29474691
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/13/2019
ms.locfileid: "66364775"
---
# <a name="inverted-lists"></a>反転リスト

 

リスト ビューを使って、送信者と受信者を見た目で区別しやすい項目を利用するチャット エクスペリエンスで会話を表示することができます。  異なる色と水平方向の配置を使って送信者と受信者のメッセージを区別すると、ユーザーはすばやく自分の会話の位置を確認できます。

> **重要な API**:[ListView クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.listview)、[ItemsStackPanel クラス](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel)、[ItemsUpdatingScrollMode プロパティ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode)
 
通常では、上から下ではなく、下から上に一覧を表示することが必要になります。  新しいメッセージを受信して末尾に追加するときは、以前のメッセージを上にスライドして空きを作り、ユーザーの注目が新着メッセージに集まるようにします。  ただし、ユーザーが上にスクロールして以前の返信を確認している場合は、ユーザーの集中を妨害することのないように、新しいメッセージの受信で表示位置を変更しないでください。

![反転リストを使ったチャット アプリ](images/listview-inverted.png)

## <a name="create-an-inverted-list"></a>反転リストを作成する

反転リストを作成するには、リスト ビューと共に、項目パネルとして [ItemsStackPanel](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel) を使います。 ItemsStackPanel で、[ItemsUpdatingScrollMode](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsstackpanel.itemsupdatingscrollmode) を [KeepLastItemInView](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemsupdatingscrollmode) に設定します。

> [!IMPORTANT]
> **KeepLastItemInView** 列挙値は、Windows 10 バージョン 1607 以上で利用できます。 この値は、アプリを以前のバージョンの Windows 10 で実行するときには使用できません。

この例は、リスト ビューの項目を下部に配置する方法や、項目に変更があったときに最後の項目をビュー内に維持するように指定する方法を示しています。
 
 **XAML**
 ```xaml
<ListView>
    <ListView.ItemsPanel>
        <ItemsPanelTemplate>
            <ItemsStackPanel VerticalAlignment="Bottom"
                             ItemsUpdatingScrollMode="KeepLastItemInView"/>
        </ItemsPanelTemplate>
    </ListView.ItemsPanel>
</ListView>
```

## <a name="dos-and-donts"></a>推奨と非推奨

- 送信者のメッセージと受信者のメッセージを両側に配置して、ユーザーが会話のフローを簡単に理解できるようにします。
- ユーザーが既に会話の末尾を読んでいて次のメッセージを待っている場合は、既存のメッセージを上に移動して最新のメッセージを表示するアニメーションを表示します。
- ユーザーが会話の末尾を読んでいない場合は、項目を移動することによってユーザーの集中を妨害してはいけません。

## <a name="get-the-sample-code"></a>サンプル コードを入手する

- [XAML ボトムアップ リストのサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/XamlBottomUpList)