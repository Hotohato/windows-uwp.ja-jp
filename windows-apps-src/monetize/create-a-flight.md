---
ms.assetid: 8C1E9E36-13AF-4386-9D0F-F9CB320F02F5
description: Microsoft Store 送信 API でこのメソッドを使用すると、パートナー センター アカウントに登録されているアプリのパッケージのフライトを作成できます。
title: パッケージ フライトの作成
ms.date: 04/16/2018
ms.topic: article
keywords: Windows 10, UWP, Microsoft Store 申請 API, フライトの作成
ms.localizationpriority: medium
ms.openlocfilehash: 0c71dfc05bf2f283652087620848396b731871cd
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371963"
---
# <a name="create-a-package-flight"></a>パッケージ フライトの作成

Microsoft Store 送信 API でこのメソッドを使用すると、パートナー センター アカウントに登録されているアプリのパッケージのフライトを作成できます。

> [!NOTE]
> このメソッドは、申請なしでパッケージ フライトを作成します。 パッケージ フライトの申請を作成するには、「[パッケージ フライト申請の管理](manage-flight-submissions.md)」のメソッドをご覧ください。

## <a name="prerequisites"></a>前提条件

このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 申請 API に関するすべての[前提条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら新しいトークンを取得できます。

## <a name="request"></a>要求

このメソッドの構文は次のとおりです。 ヘッダーと要求本文の使用例と説明については、次のセクションをご覧ください。

| メソッド | 要求 URI                                                      |
|--------|------------------------------------------------------------------|
| POST    | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/flights``` |


### <a name="request-header"></a>要求ヘッダー

| Header        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

| 名前        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | 必須。 パッケージ フライトを作成するアプリのストア ID です。 ストア ID について詳しくは、「[アプリ ID の詳細の表示](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)」をご覧ください。  |


### <a name="request-body"></a>要求本文

要求本文には次のパラメーターがあります。

|  パラメーター  |  種類  |  説明  |  必須  |
|------|------|------|------|
|  friendlyName  |  string  |  開発者によって指定されているパッケージ フライトの名前。  |  X  |
|  groupIds  |  array  |  パッケージ フライトに関連付けられているフライト グループの ID を含む文字列の配列。 フライト グループについて詳しくは、「[パッケージ フライト](https://docs.microsoft.com/windows/uwp/publish/package-flights)」をご覧ください。  |  X  |
|  rankHigherThan  |  string  |  現在のパッケージ フライトの次に低位のパッケージ フライトのフレンドリ名。 このパラメーターを設定しない場合、新しいパッケージ フライトの順位は、すべてのパッケージ フライトで最も高くなります。 フライト グループのランク付けについて詳しくは、「[パッケージ フライト](https://docs.microsoft.com/windows/uwp/publish/package-flights)」をご覧ください。    |  X  |


### <a name="request-example"></a>要求の例

次の例は、ストア ID 9WZDNCRD911W を持つアプリの新しいパッケージ フライトを作成する方法を示しています。

```syntax
POST https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/flights HTTP/1.1
Authorization: Bearer eyJ0eXAiOiJKV1Q...
Content-Type: application/json
{
  "friendlyName": "myflight",
  "groupIds": [
    0
  ],
  "rankHigherThan": null
}

```

## <a name="response"></a>応答

次の例は、このメソッドが正常に呼び出された場合の JSON 応答本文を示しています。 応答本文の値について詳しくは、次のセクションをご覧ください。

```json
{
  "flightId": "43e448df-97c9-4a43-a0bc-2a445e736bcd",
  "friendlyName": "myflight",
  "groupIds": [
    "0"
  ],
  "rankHigherThan": "671c2857-725e-4faf-9e9e-ea1191ef879c"
}
```

### <a name="response-body"></a>応答本文

| Value      | 種類   | 説明                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| flightId            | string  | パッケージ フライトの ID。 この値は、パートナー センターによって提供されます。  |
| friendlyName           | string  | 要求で指定されているパッケージ フライトの名前。   |  
| groupIds           | array  | 要求で指定されている、パッケージ フライトに関連付けられているフライト グループの ID を含む文字列の配列。 フライト グループについて詳しくは、「[パッケージ フライト](https://docs.microsoft.com/windows/uwp/publish/package-flights)」をご覧ください。   |
| rankHigherThan           | string  | 要求で指定されている、現在のパッケージ フライトの次に低位のパッケージ フライトのフレンドリ名。 フライト グループのランク付けについて詳しくは、「[パッケージ フライト](https://docs.microsoft.com/windows/uwp/publish/package-flights)」をご覧ください。  |


## <a name="error-codes"></a>エラー コード

要求を正常に完了できない場合、次の HTTP エラー コードのいずれかが応答に含まれます。

| エラー コード |  説明   |
|--------|------------------|
| 400  | 要求が無効です。 |
| 409  | 現在の状態のため、パッケージの航空券を作成できませんでしたまたはアプリであるパートナー センター機能を使用する[いない現在 Microsoft Store 送信 API ではサポートされて](create-and-manage-submissions-using-windows-store-services.md#not_supported)します。 |   


## <a name="related-topics"></a>関連トピック

* [作成し、Microsoft Store サービスを使用して送信の管理](create-and-manage-submissions-using-windows-store-services.md)
* [パッケージのフライトを取得します。](get-a-flight.md)
* [パッケージのフライトを削除します。](delete-a-flight.md)
