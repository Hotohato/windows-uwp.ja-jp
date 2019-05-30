---
ms.assetid: E8751EBF-AE0F-4107-80A1-23C186453B1C
description: 既存のアプリの申請を更新するには、Microsoft Store 申請 API の以下のメソッドを使います。
title: アプリの申請の更新
ms.date: 04/17/2018
ms.topic: article
keywords: Windows 10, UWP, Microsoft Store 申請 API, アプリの申請, 更新
ms.localizationpriority: medium
ms.openlocfilehash: 77c033ff09d56448f42d1f8084265ac0aa5d5212
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371430"
---
# <a name="update-an-app-submission"></a>アプリの申請の更新

既存のアプリの申請を更新するには、Microsoft Store 申請 API の以下のメソッドを使います。 このメソッドを使って申請を正常に更新した後は、インジェストと公開のために[申請をコミット](commit-an-app-submission.md)する必要があります。

このメソッドが Microsoft Store 申請 API を使ったアプリの申請の作成プロセスにどのように適合するかについては、「[アプリの申請の管理](manage-app-submissions.md)」をご覧ください。

## <a name="prerequisites"></a>前提条件

このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 申請 API に関するすべての[前提条件](create-and-manage-submissions-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](create-and-manage-submissions-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら新しいトークンを取得できます。
* アプリのいずれかの提出を作成します。 パートナー センターでこれを行うかを使用してこれを行う、[アプリの提出を作成](create-an-app-submission.md)メソッド。

## <a name="request"></a>要求

このメソッドの構文は次のとおりです。 ヘッダーと要求本文の使用例と説明については、次のセクションをご覧ください。

| メソッド | 要求 URI                                                      |
|--------|------------------------------------------------------------------|
| PUT   | ```https://manage.devcenter.microsoft.com/v1.0/my/applications/{applicationId}/submissions/{submissionId}  ``` |


### <a name="request-header"></a>要求ヘッダー

| Header        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

| 名前        | 種類   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| applicationId | string | 必須。 申請を更新するアプリのストア ID です。 ストア ID について詳しくは、「[アプリ ID の詳細の表示](https://docs.microsoft.com/windows/uwp/publish/view-app-identity-details)」をご覧ください。  |
| submissionId | string | 必須。 更新する申請の ID です。 この ID は、[アプリの申請の作成](create-an-app-submission.md)要求に対する応答データで確認できます。 パートナー センターで作成された送信、この ID はパートナー センターでの送信 ページの URL で使用できるも。  |


### <a name="request-body"></a>要求本文

要求本文には次のパラメーターがあります。

| Value      | 種類   | 説明                                                                                                                                                                                                                                                                         |
|------------|--------|----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| applicationCategory           | string  |   アプリの[カテゴリとサブカテゴリ](https://docs.microsoft.com/windows/uwp/publish/category-and-subcategory-table)を指定する文字列です。 カテゴリとサブカテゴリは、アンダースコア "_" で 1 つの文字列に連結します (例: **BooksAndReference_EReader**)。      |  
| pricing           |  オブジェクト  | アプリの価格情報を含むオブジェクトです。 詳しくは、「[価格リソース](manage-app-submissions.md#pricing-object)」セクションをご覧ください。       |   
| visibility           |  string  |  アプリの可視性です。 次のいずれかの値を使用できます。 <ul><li>Hidden</li><li>パブリック</li><li>Private</li><li>NotSet</li></ul>       |   
| targetPublishMode           | string  | 申請の公開モードです。 次のいずれかの値を使用できます。 <ul><li>即時</li><li>Manual</li><li>SpecificDate</li></ul> |
| targetPublishDate           | string  | *targetPublishMode* が SpecificDate に設定されている場合、ISO 8601 形式での申請の公開日です。  |  
| listings           |   オブジェクト  |  キーと値のペアのディクショナリです。各キーは国コード、各値はアプリの登録情報を含む[登録情報リソース](manage-app-submissions.md#listing-object) オブジェクトです。       |   
| hardwarePreferences           |  array  |   アプリの[ハードウェアの基本設定](https://docs.microsoft.com/windows/uwp/publish/enter-app-properties)を定義する文字列の配列です。 次のいずれかの値を使用できます。 <ul><li>タッチ</li><li>キーボード</li><li>マウス</li><li>Camera</li><li>NfcHce</li><li>Nfc</li><li>BluetoothLE</li><li>Telephony</li></ul>     |   
| automaticBackupEnabled           |  boolean  |   OneDrive への自動バックアップにアプリのデータを含めることができるかどうかを示します。 詳しくは、「[アプリの宣言](https://docs.microsoft.com/windows/uwp/publish/app-declarations)」をご覧ください。   |   
| canInstallOnRemovableMedia           |  boolean  |   ユーザーがアプリをリムーバブル記憶域にインストールできるかどうかを示します。 詳しくは、「[アプリの宣言](https://docs.microsoft.com/windows/uwp/publish/app-declarations)」をご覧ください。     |   
| isGameDvrEnabled           |  boolean |   アプリのゲーム録画が有効になっているかどうかを示します。    |   
| gamingOptions           |  オブジェクト |   アプリのゲーム関連の設定を定義する 1 つの[ゲーム オプション リソース](manage-app-submissions.md#gaming-options-object)を格納する配列です。     |   
| hasExternalInAppProducts           |     boolean          |   ユーザーが Microsoft Store コマース システムを使わないで購入することをアプリが許可するかどうかを示します。 詳しくは、「[アプリの宣言](https://docs.microsoft.com/windows/uwp/publish/app-declarations)」をご覧ください。     |   
| meetAccessibilityGuidelines           |    boolean           |  アプリがアクセシビリティ ガイドラインを満たことをテストされているかどうかを示します。 詳しくは、「[アプリの宣言](https://docs.microsoft.com/windows/uwp/publish/app-declarations)」をご覧ください。      |   
| notesForCertification           |  string  |   アプリの[認定の注意書き](https://docs.microsoft.com/windows/uwp/publish/notes-for-certification)が含まれます。    |    
| applicationPackages           |   array  | 申請の各パッケージに関する詳細を提供するオブジェクトが含まれています。 詳しくは、「[アプリ パッケージ](manage-app-submissions.md#application-package-object)」セクションをご覧ください。 このメソッドを呼び出してアプリの申請を更新するとき、要求の本文では、これらのオブジェクトの値 *fileName*、*fileStatus*、*minimumDirectXVersion*、*minimumSystemRam* だけが必須です。 パートナー センターでは、その他の値が設定されます。   |    
| packageDeliveryOptions    | オブジェクト  | 申請の段階的なパッケージのロールアウトと必須の更新の設定が含まれています。 詳しくは、「[パッケージの配信オプション オブジェクト](manage-app-submissions.md#package-delivery-options-object)」をご覧ください。  |
| enterpriseLicensing           |  string  |  アプリのエンタープライズ ライセンス動作を示す[エンタープライズ ライセンス値](manage-app-submissions.md#enterprise-licensing)のいずれかです。  |    
| allowMicrosftDecideAppAvailabilityToFutureDeviceFamilies           |  boolean   |  [アプリを将来の Windows 10 デバイス ファミリで利用できるようにする](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability)ことを Microsoft が許可されているかどうかを示すします。    |    
| allowTargetFutureDeviceFamilies           | boolean   |  [将来の Windows 10 デバイス ファミリをターゲットにする](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability)ことをアプリが許可されているかどうかを示します。     |   
| trailers           |  array |   アプリの登録情報用のビデオ トレーラーを表す[トレーラー リソース](manage-app-submissions.md#trailer-object)を格納する配列です。格納できるトレーラー リソースの数には上限があります。   |   


### <a name="request-example"></a>要求の例

次の例は、アプリの申請を更新する方法を示しています。

```json
PUT https://manage.devcenter.microsoft.com/v1.0/my/applications/9NBLGGH4R315/submissions/1152921504621230023 HTTP/1.1
Authorization: Bearer <your access token>
Content-Type: application/json
{
  "applicationCategory": "BooksAndReference_EReader",
  "pricing": {
    "trialPeriod": "FifteenDays",
    "marketSpecificPricings": {},
    "sales": [],
    "priceId": "Tier2"
  },
  "visibility": "Public",
  "targetPublishMode": "Manual",
  "targetPublishDate": "1601-01-01T00:00:00Z",
  "listings": {
    "en-us": {
      "baseListing": {
        "copyrightAndTrademarkInfo": "",
        "keywords": [
              "epub"
            ],
        "licenseTerms": "",
        "privacyPolicy": "",
        "supportContact": "",
        "websiteUrl": "",
        "description": "Description",
        "features": [
              "Free ebook reader"
            ],
        "releaseNotes": "",
        "images": [
          {
            "fileName": "contoso.png",
            "fileStatus": "Uploaded",
            "id": "1152921504672272757",
            "imageType": "Screenshot"
          }
        ],
        "recommendedHardware": [],
        "title": "Contoso ebook reader"
      },
      "platformOverrides": {
        "Windows81": {
          "description": "Ebook reader for Windows 8.1"
        }
      }
    }
  },
  "hardwarePreferences": [
    "Touch"
  ],
  "automaticBackupEnabled": false,
  "canInstallOnRemovableMedia": true,
  "isGameDvrEnabled": false,
  "gamingOptions": [],
  "hasExternalInAppProducts": false,
  "meetAccessibilityGuidelines": true,
  "notesForCertification": "",
  "applicationPackages": [
    {
      "fileName": "contoso_app.appx",
      "fileStatus": "PendingUpload",
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None"
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "enterpriseLicensing": "Online",
  "allowMicrosoftDecideAppAvailabilityToFutureDeviceFamilies": true,
  "allowTargetFutureDeviceFamilies": {
    "Desktop": false,
    "Mobile": true,
    "Holographic": true,
    "Xbox": false,
    "Team": true
  },
  "trailers": []
}
```

## <a name="response"></a>応答

次の例は、このメソッドが正常に呼び出された場合の JSON 応答本文を示しています。 応答本文には、更新された申請に関する情報が含まれています。 応答本文内の値について詳しくは、[アプリの申請のリソース](manage-app-submissions.md#app-submission-object)をご覧ください。

```json
{
  "id": "1152921504621243540",
  "applicationCategory": "BooksAndReference_EReader",
  "pricing": {
    "trialPeriod": "FifteenDays",
    "marketSpecificPricings": {},
    "sales": [],
    "priceId": "Tier2"
  },
  "visibility": "Public",
  "targetPublishMode": "Manual",
  "targetPublishDate": "1601-01-01T00:00:00Z",
  "listings": {
    "en-us": {
      "baseListing": {
        "copyrightAndTrademarkInfo": "",
        "keywords": [
           "epub"
        ],
        "licenseTerms": "",
        "privacyPolicy": "",
        "supportContact": "",
        "websiteUrl": "",
        "description": "Description",
        "features": [
          "Free ebook reader"
        ],
        "releaseNotes": "",
        "images": [
          {
            "fileName": "contoso.png",
            "fileStatus": "Uploaded",
            "id": "1152921504672272757",
            "imageType": "Screenshot"
          }
        ],
        "recommendedHardware": [],
        "title": "Contoso ebook reader"
      },
      "platformOverrides": {
        "Windows81": {
          "description": "Ebook reader for Windows 8.1",
        }
      }
    }
  },
  "hardwarePreferences": [
    "Touch"
  ],
  "automaticBackupEnabled": false,
  "canInstallOnRemovableMedia": true,
  "isGameDvrEnabled": false,
  "gamingOptions": [],
  "hasExternalInAppProducts": false,
  "meetAccessibilityGuidelines": true,
  "notesForCertification": "",
  "status": "PendingCommit",
  "statusDetails": {
    "errors": [],
    "warnings": [],
    "certificationReports": []
  },
  "fileUploadUrl": "https://productingestionbin1.blob.core.windows.net/ingestion/387a9ea8-a412-43a9-8fb3-a38d03eb483d?sv=2014-02-14&sr=b&sig=sdd12JmoaT6BhvC%2BZUrwRweA%2Fkvj%2BEBCY09C2SZZowg%3D&se=2016-06-17T18:32:26Z&sp=rwl",
  "applicationPackages": [
    {
      "fileName": "contoso_app.appx",
      "fileStatus": "PendingUpload",
      "id": "1152921504620138797",
      "version": "1.0.0.0",
      "architecture": "ARM",
      "languages": [
        "en-US"
      ],
      "capabilities": [
        "ID_RESOLUTION_HD720P",
        "ID_RESOLUTION_WVGA",
        "ID_RESOLUTION_WXGA"
      ],
      "minimumDirectXVersion": "None",
      "minimumSystemRam": "None",
      "targetDeviceFamilies": [
        "Windows.Mobile min version 10.0.10240.0"
      ]
    }
  ],
  "packageDeliveryOptions": {
    "packageRollout": {
        "isPackageRollout": false,
        "packageRolloutPercentage": 0.0,
        "packageRolloutStatus": "PackageRolloutNotStarted",
        "fallbackSubmissionId": "0"
    },
    "isMandatoryUpdate": false,
    "mandatoryUpdateEffectiveDate": "1601-01-01T00:00:00.0000000Z"
  },
  "enterpriseLicensing": "Online",
  "allowMicrosoftDecideAppAvailabilityToFutureDeviceFamilies": true,
  "allowTargetFutureDeviceFamilies": {
    "Desktop": false,
    "Mobile": true,
    "Holographic": true,
    "Xbox": false,
    "Team": true
  },
  "friendlyName": "Submission 2",
  "trailers": []
}
```

## <a name="error-codes"></a>エラー コード

要求を正常に完了できない場合、次の HTTP エラー コードのいずれかが応答に含まれます。

| エラー コード |  説明   |
|--------|------------------|
| 400  | 要求が無効なため、申請を更新できませんでした。 |
| 409  | アプリの現在の状態であるため、送信を更新できませんでしたまたはアプリであるパートナー センター機能を使用する[現在サポートされていません、Microsoft Store 送信 API](create-and-manage-submissions-using-windows-store-services.md#not_supported)。 |   


## <a name="related-topics"></a>関連トピック

* [作成し、Microsoft Store サービスを使用して送信の管理](create-and-manage-submissions-using-windows-store-services.md)
* [アプリの提出を取得します。](get-an-app-submission.md)
* [アプリの提出を作成します。](create-an-app-submission.md)
* [アプリの提出をコミットします。](commit-an-app-submission.md)
* [アプリの提出を削除します。](delete-an-app-submission.md)
* [アプリの送信の状態を取得します。](get-status-for-an-app-submission.md)
