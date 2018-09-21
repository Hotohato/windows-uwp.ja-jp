---
title: DeviceEndpoint (JSON)
assetID: bd6c4af8-e491-8885-970e-e53d1d60642b
permalink: en-us/docs/xboxlive/rest/json-deviceendpoint.html
author: KevinAsgari
description: " DeviceEndpoint (JSON)"
ms.author: kevinasg
ms.date: 20-12-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One
ms.localizationpriority: medium
ms.openlocfilehash: bfae4eac9ecf0177026183cc25bac5526bbba62f
ms.sourcegitcommit: 4f6dc806229a8226894c55ceb6d6eab391ec8ab6
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/20/2018
ms.locfileid: "4086690"
---
# <a name="deviceendpoint-json"></a>DeviceEndpoint (JSON)
 
<a id="ID4EO"></a>

 
## <a name="deviceendpoint"></a>DeviceEndpoint
 
DeviceEndpoint オブジェクトでは、次の仕様があります。
 
| メンバー| 種類| 説明| 
| --- | --- | --- | 
| deviceName| string| 省略可能。 該当する場合は、デバイスのフレンドリ名。 現在、この値は使用しません。| 
| endpointUri| string| 必須。 クライアント プラットフォーム (Windows または Windows Phone) が、プッシュ通知サービス (WNS または MPNS) から入手した URL です。| 
| locale| string| 必須。 このエンドポイントに送信される通知の目的の言語です。 優先順位の値をコンマ区切りのリストであることができます。 例:「DE-DE, EN-US, en」します。| 
| プラットフォーム| string| 省略可能。 現在サポートされている値は、"WindowsPhone"および"Windows"です。 指定しない場合は、デバイス トークンから導出されます。| 
| platformVersion| string| 省略可能。 この文字列の形式は、各プラットフォームに固有です。 現在、この値は使用しません。| 
| systemId| GUID| 必須。 「アプリ インスタンス」の一意の識別子 (デバイス/ユーザーの組み合わせ)。 ベスト プラクティスの導入がインストール/最初の実行時にランダムな GUID を生成するアプリの引き続き、アプリの後続の実行でその値を使用します。| 
| titleId| 32 ビット符号なし整数| 必須。 サービスへの呼び出しを発行するゲームのタイトル ID です。| 
  
<a id="ID4EGD"></a>

 
## <a name="sample-json-syntax"></a>JSON 構文の例
 

```json
{
  "systemId": "e9af05b4-70de-4920-880f-026c6fb67d1b",
  "userId" : 1234567890
  "endpointUri": "http://cloud.notify.windows.com/.../",
  "platform": "Windows",
  "platformVersion": "1.0",
  "deviceName": "Test Endpoint",
  "locale": "en-US",
  "titleId": 1297290219
}
    
```

  
<a id="ID4EPD"></a>

 
## <a name="see-also"></a>関連項目
 
<a id="ID4ERD"></a>

 
##### <a name="parent"></a>Parent 

[JavaScript Object Notation (JSON) オブジェクト リファレンス](atoc-xboxlivews-reference-json.md)

  
<a id="ID4E4D"></a>

 
##### <a name="reference"></a>リファレンス   