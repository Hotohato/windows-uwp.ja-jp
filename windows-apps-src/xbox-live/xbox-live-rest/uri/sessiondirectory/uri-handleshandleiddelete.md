---
title: DELETE (/handles/{handleId})
assetID: 71cceff4-3a74-dc05-12db-cfe3f20a8aea
permalink: en-us/docs/xboxlive/rest/uri-handleshandleiddelete.html
author: KevinAsgari
description: " DELETE (/handles/{handleId})"
ms.author: kevinasg
ms.date: 20-12-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: 15300451495c198a1f15997bae38cb862a9b8186
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2018
ms.locfileid: "4085362"
---
# <a name="delete-handleshandleid"></a>DELETE (/handles/{handleId})
ハンドル ID で指定されたハンドルを削除します。

> [!IMPORTANT]
> このメソッドは、2015年マルチプレイヤーで使用し、以降そのマルチプレイヤーのバージョンにのみ適用されます。 テンプレート コントラクト 104/105 以降で使用するものであり、X Xbl コントラクト バージョンのヘッダーの要素が必要です: 104/105 または後ですべての要求します。

  * [注釈](#ID4ET)
  * [URI パラメーター](#ID4EAB)
  * [HTTP ステータス コード](#ID4ELB)
  * [要求本文](#ID4ESB)
  * [応答本文](#ID4E4B)

<a id="ID4ET"></a>


## <a name="remarks"></a>注釈
この HTTP/REST メソッドは、指定された id のハンドルを削除し、セッションのユーザーの現在のアクティビティをクリアします。 このメソッドは、 **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.ClearActivityAsync**でラップすることができます。  
<a id="ID4EAB"></a>


## <a name="uri-parameters"></a>URI パラメーター

| パラメーター| 型| 説明|
| --- | --- | --- | --- |
| ハンドル id を使用| GUID| セッション ハンドルの一意の ID。|

<a id="ID4ELB"></a>


## <a name="http-status-codes"></a>HTTP ステータス コード
サービスは、MPSD に適用される HTTP ステータス コードを返します。  
<a id="ID4ESB"></a>


## <a name="request-body"></a>要求本文

この要求の本文には、オブジェクトは送信されません。

<a id="ID4E4B"></a>


## <a name="response-body"></a>応答本文

応答の本文には、オブジェクトは送信されません。

<a id="ID4EIC"></a>


## <a name="see-also"></a>関連項目

<a id="ID4EKC"></a>


##### <a name="parent"></a>Parent

[/handles/{handleId}](uri-handleshandleid.md)