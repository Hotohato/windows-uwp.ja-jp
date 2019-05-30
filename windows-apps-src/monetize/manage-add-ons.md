---
ms.assetid: 4F9657E5-1AF8-45E0-9617-45AF64E144FC
description: Microsoft Store 送信 API でこれらのメソッドを使用して、パートナー センター アカウントに登録されているアプリ用のアドオンを管理します。
title: アドオンの管理
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, Microsoft Store 申請 API, アドオン, アプリ内製品, IAP
ms.localizationpriority: medium
ms.openlocfilehash: 8e06f8e915466f116692c63df5c53c2a0f97447f
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372496"
---
# <a name="manage-add-ons"></a>アドオンの管理

アプリのアドオンを管理するには、Microsoft Store 申請 API の以下のメソッドを使います。 Microsoft Store 申請 API の概要については、「[Microsoft Store サービスを使用した申請の作成と管理](create-and-manage-submissions-using-windows-store-services.md)」をご覧ください。この API を使用するための前提条件などの情報があります。

以下のメソッドは、アドオンの取得、作成、または削除にしか使用できません。 アドオンの申請を作成する方法については、「[アドオンの申請の管理](manage-add-on-submissions.md)」のメソッドをご覧ください。

<table>
<colgroup>
<col width="10%" />
<col width="30%" />
<col width="60%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">メソッド</th>
<th align="left">URI</th>
<th align="left">説明</th>
</tr>
</thead>
<tbody>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts</td>
<td align="left"><a href="get-all-add-ons.md">アプリのすべてのアドオンを入手します。</a></td>
</tr>
<tr>
<td align="left">GET</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}</td>
<td align="left"><a href="get-an-add-on.md">特定のアドオンを取得します。</a></td>
</tr>
<tr>
<td align="left">POST</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts</td>
<td align="left"><a href="create-an-add-on.md">アドオンを作成します。</a></td>
</tr>
<tr>
<td align="left">Del</td>
<td align="left">https://manage.devcenter.microsoft.com/v1.0/my/inappproducts/{inAppProductId}</td>
<td align="left"><a href="delete-an-add-on.md">アドオンを削除します。</a></td>
</tr>
</tbody>
</table>

## <a name="prerequisites"></a>前提条件

Microsoft Store 申請 API に関するすべての[前提条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)がまだ満たされていない場合は、ここに記載されているメソッドを使用する前に前提条件を整えてください。

## <a name="data-resources"></a>データ リソース

アドオンを管理するための Microsoft Store 申請 API のメソッドでは、次の JSON データ リソースが使われます。

<span id="add-on-object" />

### <a name="add-on-resource"></a>アドオン リソース

このリソースは、アドオンを記述しています。

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
  "id": "9NBLGGH4TNMP",
  "productId": "TestAddOn",
  "productType": "Durable",
  "pendingInAppProductSubmission": {
    "id": "1152921504621243619",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
  },
  "lastPublishedInAppProductSubmission": {
    "id": "1152921504621243705",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243705"
  }
}
```

このリソースには、次の値があります。

| Value      | 種類   | 説明        |
|------------|--------|--------------|
| applications      | array  | このアドオンが関連付けられるアプリを表す 1 つの[アプリケーション リソース](#application-object)を格納する配列です。 この配列でサポートされる項目は 1 つのみです。  |
| id | string  | アドオンのストア ID です。 この値は、ストアによって提供されます。 ストア ID の例は 9NBLGGH4TNMP です。  |
| productId | string  | アドオンの製品 ID です。 これは、アドオンの作成時に開発者が指定した ID です。 詳しくは、「[IAP の製品の種類と製品 ID を設定する](https://docs.microsoft.com/windows/uwp/publish/set-your-iap-product-id)」をご覧ください。 |
| productType | string  | アドオンの製品の種類です。 次の値がサポートされています。**持続性のある**と**消耗**します。  |
| lastPublishedInAppProductSubmission       | オブジェクト | アドオンの最後に公開された申請に関する情報を提供する[申請のリソース](#submission-object)。         |
| pendingInAppProductSubmission        | オブジェクト  |  アドオンの現在保留中の申請に関する情報を提供する[申請のリソース](#submission-object)。  |   |

<span id="application-object" />

### <a name="application-resource"></a>アプリケーション リソース

このリソースは、アドオンが関連付けられているアプリを説明します。 次の例は、このリソースの書式設定を示しています。

```json
{
  "applications": {
    "value": [
      {
        "id": "9NBLGGH4R315",
        "resourceLocation": "applications/9NBLGGH4R315"
      }
    ],
    "totalCount": 1
  },
}
```

このリソースには、次の値があります。

| Value           | 種類    | 説明        |
|-----------------|---------|-----------|
| value            | オブジェクト  |  次の値を格納するオブジェクトです。 <br/><br/> <ul><li>*id*。アプリケーションのストア ID です。 ストア ID について詳しくは、「[アプリ ID の詳細の表示](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)」をご覧ください。</li><li>*resourceLocation*。 アプリの完全なデータを取得するために基本 ```https://manage.devcenter.microsoft.com/v1.0/my/``` 要求 URI に付加できる相対パス。</li></ul>   |
| totalCount   | int  | 応答本文の *applications* 配列のアプリ オブジェクトの数。                                                                                                                                                 |

<span id="submission-object" />

### <a name="submission-resource"></a>申請のリソース

このリソースは、アドオンの申請に関する情報を提供します。 次の例は、このリソースの書式設定を示しています。

```json
{
  "pendingInAppProductSubmission": {
    "id": "1152921504621243619",
    "resourceLocation": "inappproducts/9NBLGGH4TNMP/submissions/1152921504621243619"
  },
}
```

このリソースには、次の値があります。

| Value           | 種類    | 説明     |
|-----------------|---------|------------------|
| id            | string  | 申請 ID。    |
| resourceLocation   | string  | 申請の完全なデータを取得するために基本 ```https://manage.devcenter.microsoft.com/v1.0/my/``` 要求 URI に付加できる相対パス。     |
 
<span/>

## <a name="related-topics"></a>関連トピック

* [作成し、Microsoft Store サービスを使用して送信の管理](create-and-manage-submissions-using-windows-store-services.md)
* [Microsoft Store 送信 API を使用してアドオン送信を管理します。](manage-add-on-submissions.md)
* [すべてのアドオンを入手します。](get-all-add-ons.md)
* [アドオンを入手します。](get-an-add-on.md)
* [アドオンを作成します。](create-an-add-on.md)
* [アドオンを削除します。](delete-an-add-on.md)
