---
title: GET (/handles/{handleId}/session)
assetID: 1f22954c-e77b-69c2-63f4-741fbd965f8f
permalink: en-us/docs/xboxlive/rest/uri-handleshandleidsessionget.html
author: KevinAsgari
description: " GET (/handles/{handleId}/session)"
ms.author: kevinasg
ms.date: 20-12-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: 0ab3214ca9b2cb2ff8ace11706ceda22885598e1
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2018
ms.locfileid: "4088512"
---
# <a name="get-handleshandleidsession"></a>GET (/handles/{handleId}/session)
指定したハンドル識別子のセッション オブジェクトを取得します。

> [!IMPORTANT]
> このメソッドは、2015年マルチプレイヤーで使用し、以降そのマルチプレイヤーのバージョンにのみ適用されます。 テンプレート コントラクト 104/105 以降で使用するものであり、X Xbl コントラクト バージョンのヘッダーの要素が必要です: 104/105 または後ですべての要求します。

  * [注釈](#ID4ET)
  * [URI パラメーター](#ID4EDB)
  * [HTTP ステータス コード](#ID4EOB)
  * [要求本文](#ID4EVB)
  * [応答本文](#ID4E6B)

<a id="ID4ET"></a>


## <a name="remarks"></a>注釈

この HTTP/REST メソッドは、セッション (ハンドル) に指定されたサービス側ポインターを使用して、サーバーからセッション オブジェクトを取得します。 すべての属性を使用して、セッション オブジェクトを返します。 このメソッドは、 **Microsoft.Xbox.Services.Multiplayer.MultiplayerService.GetCurrentSessionByHandleAsync**でラップすることができます。

このメソッドの呼び出し元は、プレイヤーの**MultiplayerActivityDetails**オブジェクトからハンドル ID を取得します。 または、呼び出し元は、ユーザーがゲームへの招待を受け入れた後、プロトコルのアクティブ化から ID を取得します。

<a id="ID4EDB"></a>


## <a name="uri-parameters"></a>URI パラメーター

| パラメーター| 型| 説明|
| --- | --- | --- | --- |
| ハンドル id を使用| GUID| セッション ハンドルの一意の ID。|

<a id="ID4EOB"></a>


## <a name="http-status-codes"></a>HTTP ステータス コード
サービスは、MPSD に適用される HTTP ステータス コードを返します。  
<a id="ID4EVB"></a>


## <a name="request-body"></a>要求本文

この要求の本文には、オブジェクトは送信されません。

<a id="ID4E6B"></a>


## <a name="response-body"></a>応答本文
[MultiplayerSession (JSON)](../../json/json-multiplayersession.md)の応答構造を参照してください。  
<a id="ID4EIC"></a>


## <a name="see-also"></a>関連項目

<a id="ID4EKC"></a>


##### <a name="parent"></a>Parent

[/handles/{handleId}/session](uri-handleshandleidsession.md)