---
description: ストア製品向けに Windows.Services.Store 名前空間の拡張 JSON データ スキーマについて説明します。
title: Store 製品のデータ スキーマ
ms.date: 09/26/2017
ms.topic: article
keywords: Windows 10, UWP, ExtendedJsonData, Store 製品, スキーマ
ms.localizationpriority: medium
ms.openlocfilehash: 77f63ce409a576b3c873d95df0d2e8d0f0933808
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372532"
---
# <a name="data-schemas-for-store-products"></a>Store 製品のデータ スキーマ

アプリやアドオンなどの製品をストアに提出すると、製品とそのライセンスの包括的なデータ セットがストアで管理されます。 アプリのコードで [Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 名前空間のプロパティを使用することで、こうしたデータの一部にプログラムによってアクセスできます。 たとえば、[StoreProduct.Description](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.Description) プロパティと [StoreProduct.Price](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.Price) プロパティを使用すると、現在のアプリや、現在のアプリのアドオンに関する説明と価格を取得できます。

ただし、ストア内の製品に関する多くのデータは、[Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 名前空間の定義済みプロパティを使用することでは公開されません。 コードで製品の完全なデータにアクセスするには、代わりに、次の全般的なプロパティを使用できます。

* [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData)
* [StoreSku.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.ExtendedJsonData)
* [StoreAvailability.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability.ExtendedJsonData)
*   [StoreCollectionData.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storecollectiondata.ExtendedJsonData)
*   [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData)
* [StoreLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense.ExtendedJsonData)
*   [StorePurchaseProperties.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storepurchaseproperties.ExtendedJsonData)

これらのプロパティは、対応するオブジェクトの完全なデータを JSON 形式の文字列として返します。 この記事では、これらのプロパティによって返されるデータの完全なスキーマを提供します。

> [!NOTE]
> ストア内の製品は、[製品](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct)オブジェクト、[SKU](https://docs.microsoft.com/uwp/api/windows.services.store.storesku) オブジェクト、および[可用性](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability)オブジェクトの階層で整理されます。 詳細については、「[製品、SKU、および可用性](in-app-purchases-and-trials.md#products-skus)」をご覧ください。

## <a name="schema-for-storeproduct-storesku-storeavailability-and-storecollectiondata"></a>StoreProduct、StoreSku、StoreAvailability、および StoreCollectionData のスキーマ

次のスキーマは、[StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData) によって返される JSON 形式の文字列を示しています。 [StoreSku.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storesku.ExtendedJsonData) プロパティ、[StoreAvailability.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeavailability.ExtendedJsonData) プロパティ、および [StoreCollectionData.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storecollectiondata.ExtendedJsonData) プロパティは、`DisplaySkuAvailabilities\Sku`、`DisplaySkuAvailabilities\Availabilities`、および `DisplaySkuAvailabilities\Sku\CollectionData` のパスの下でそれぞれ定義されているスキーマの部分だけを返します。

[StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData) によって返される JSON 形式の文字列の例については、[このセクション](#product-example)をご覧ください。

[!code-json[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreProduct.ExtendedJsonData.json#L1-L729)]

<span id="product-example" />

### <a name="example"></a>例

次の例は、アプリの [StoreProduct.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeproduct.ExtendedJsonData) プロパティによって返される JSON 形式の文字列を示しています。

[!code-json[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreProduct.ExtendedJsonDataExample.json#L1-L268)]

## <a name="schema-for-storeapplicense-and-storelicense"></a>StoreAppLicense と StoreLicense のスキーマ

次のスキーマは、[StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData) によって返される JSON 形式の文字列を示しています。 [StoreLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storelicense.ExtendedJsonData) プロパティは、`productAddOns` パスの下で定義されているスキーマの部分だけを返します。

[StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData) によって返される JSON 形式の文字列の例については、[このセクション](#license-example)をご覧ください。

[!code-json[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreAppLicense.ExtendedJsonData.json#L1-L80)]

<span id="license-example" />

### <a name="example"></a>例

次の例は、アプリの [StoreAppLicense.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense.ExtendedJsonData) プロパティによって返される JSON 形式の文字列を示しています。

[!code-json[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StoreAppLicense.ExtendedJsonDataExample.json#L1-L28)]

## <a name="schema-for-storepurchaseproperties"></a>StorePurchaseProperties のスキーマ

次のスキーマは、[StorePurchaseProperties.ExtendedJsonData](https://docs.microsoft.com/uwp/api/windows.services.store.storepurchaseproperties.ExtendedJsonData) によって返される JSON 形式の文字列を示しています。

[!code-json[ExtendedJsonDataSchema](./code/InAppPurchasesAndLicenses_RS1/json/StorePurchaseProperties.ExtendedJsonData.json#L1-L12)]

## <a name="related-topics"></a>関連トピック

* [アプリ内購入と試用版](in-app-purchases-and-trials.md)
* [アプリケーションとアドオンの製品情報を取得します。](get-product-info-for-apps-and-add-ons.md)
* [アプリケーションとアドオンのライセンス情報を取得します。](get-license-info-for-apps-and-add-ons.md)
* [アプリケーションとアドオンのアプリ内購入を有効にします。](enable-in-app-purchases-of-apps-and-add-ons.md)
* [使用できるアドオンの購入を有効にします。](enable-consumable-add-on-purchases.md)
* [アプリの試用版を実装します。](implement-a-trial-version-of-your-app.md)
