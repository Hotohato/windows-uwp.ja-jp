---
author: mcleanbyron
description: アプリの分析データを取得するのに、Microsoft Store 分析 API の以下のメソッドを使用します。
title: 分析データを取得します。
ms.author: mcleans
ms.date: 07/31/2018
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, Store サービス, Microsoft Store 分析 API, insights
ms.localizationpriority: medium
ms.openlocfilehash: 53fbd91437e5dc702f8672c6cbadeea32a8a96bf
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "3847446"
---
# <a name="get-insights-data"></a>分析データを取得します。

特定の日付範囲やその他のオプション フィルターを使って、取得、正常性, アプリの使用状況のメトリックに関連する分析データを取得するのには、Microsoft Store 分析 API の以下のメソッドを使用します。 この情報も[インサイト レポート](../publish/insights-report.md)では、Windows デベロッパー センター ダッシュ ボードで使用できます。

## <a name="prerequisites"></a>前提条件


このメソッドを使うには、最初に次の作業を行う必要があります。

* Microsoft Store 分析 API に関するすべての[前提条件](access-analytics-data-using-windows-store-services.md#prerequisites)を満たします (前提条件がまだ満たされていない場合)。
* このメソッドの要求ヘッダーで使う [Azure AD アクセス トークンを取得](access-analytics-data-using-windows-store-services.md#obtain-an-azure-ad-access-token)します。 アクセス トークンを取得した後、アクセス トークンを使用できるのは、その有効期限が切れるまでの 60 分間です。 トークンの有効期限が切れたら、新しいトークンを取得できます。

## <a name="request"></a>要求


### <a name="request-syntax"></a>要求の構文

| メソッド | 要求 URI       |
|--------|----------------------|
| GET    | ```https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights``` |


### <a name="request-header"></a>要求ヘッダー

| ヘッダー        | 型   | 説明                                                                 |
|---------------|--------|-----------------------------------------------------------------------------|
| Authorization | string | 必須。 **Bearer** &lt;*トークン*&gt; という形式の Azure AD アクセス トークン。 |


### <a name="request-parameters"></a>要求パラメーター

| パラメーター        | 型   |  説明      |  必須かどうか  
|---------------|--------|---------------|------|
| applicationId | string | 分析データを取得するアプリの[ストア ID です](in-app-purchases-and-trials.md#store-ids)。 このパラメーターを指定しない場合、応答本文にはアカウントに登録されているすべてのアプリの分析データが含まれます。  |  必須ではない  |
| startDate | date | 取得する情報のデータの日付範囲の開始日です。 既定値は、現在の日付の 30 日前です。 |  必須ではない  |
| endDate | date | 取得する情報のデータの日付範囲の終了日です。 既定値は現在の日付です。 |  必須ではない  |
| filter | string  | 応答内の行をフィルター処理する 1 つまたは複数のステートメントです。 各ステートメントには、応答本文からのフィールド名、および **eq** 演算子または **ne** 演算子と関連付けられる値が含まれており、**and** や **or** を使用してステートメントを組み合わせることができます。 *filter* パラメーターでは、文字列値を単一引用符で囲む必要があります。 たとえば、*フィルター = dataType eq '入手'* します。 <p/><p/>次のフィルター フィールドを指定することができます。<p/><ul><li><strong>入手</strong></li><li><strong>正常性</strong></li><li><strong>使用状況</strong></li></ul> | いいえ   |

### <a name="request-example"></a>要求の例

次の例では、情報のデータを取得する要求を示します。 *applicationId* 値を、目的のアプリのストア ID に置き換えてください。

```syntax
GET https://manage.devcenter.microsoft.com/v1.0/my/analytics/insights?applicationId=9NBLGGGZ5QDR&startDate=6/1/2018&endDate=6/15/2018&filter=dataType eq 'acquisition' or dataType eq 'health' HTTP/1.1
Authorization: Bearer <your access token>
```

## <a name="response"></a>応答

### <a name="response-body"></a>応答本文

| 値      | 型   | 説明                  |
|------------|--------|-------------------------------------------------------|
| Value      | array  | アプリの分析データを含むオブジェクトの配列です。 各オブジェクトのデータについて詳しくは、以下の「 [Insight 値](#insight-values)」セクションをご覧ください。                                                                                                                      |
| TotalCount | int    | クエリの結果データ内の行の総数です。                 |


### <a name="insight-values"></a>値の詳細を確認できます。

*Value* 配列の要素には、次の値が含まれます。

| 値               | 型   | 説明                           |
|---------------------|--------|-------------------------------------------|
| applicationId       | string | 分析データを取得するアプリのストア ID です。     |
| insightDate                | string | 日付のメトリックが特定の変更を識別します。 この日付またはそれより前に、の週との比較メトリックの短縮を大幅に増加を検出されました週の終わりを表します。 |
| データ型     | string | この情報を記述する一般的な分析領域を指定する次の文字列のいずれか。<p/><ul><li><strong>入手</strong></li><li><strong>正常性</strong></li><li><strong>使用状況</strong></li></ul>   |
| insightDetail          | array | 1 つまたは複数の[InsightDetail 値](#insightdetail-values)を現在の情報の詳細を表します。    |


### <a name="insightdetail-values"></a>InsightDetail 値

| 値               | 型   | 説明                           |
|---------------------|--------|-------------------------------------------|
| FactName           | string | 現在の洞察または現在のディメンションを記述するメトリックを示す次の値のいずれかは、**データ型**の値に基づいています。<ul><li>**正常性**、この値は常に値が**ヒット数**です。</li><li>**入手**、この値は常に値が**AcquisitionQuantity**です。</li><li>**使用状況**、この値は、次の文字列のいずれかを指定ことができます。<ul><li><strong>DailyActiveUsers</strong></li><li><strong>EngagementDurationMinutes</strong></li><li><strong>DailyActiveDevices</strong></li><li><strong>DailyNewUsers</strong></li><li><strong>DailySessionCount</strong></li></ul></ul>  |
| SubDimensions         | array |  情報を得ることの 1 つのメトリックを記述する 1 つまたは複数のオブジェクトです。   |
| PercentChange            | string |  メトリックは、全顧客ベースの間で変更された割合を示します。  |
| DimensionName           | string |  現在の次元で説明されているメトリックの名前です。 例では、**イベントの種類**、**市場**、 **DeviceType**、 **PackageVersion**、 **AcquisitionType**、 **AgeGroup** 、**性別**があります。   |
| DimensionValue              | string | 現在のディメンションに記載されているメトリックの値。 たとえば、 **DimensionName**が**イベントの種類**である場合は、**クラッシュ**や**ハング**が**DimensionValue**することがあります。   |
| FactValue     | string | 情報を得ることが検出された日付、メトリックの絶対座標に基づく値。  |
| Direction | string |  (**正**または**負**) の変更の方向です。   |
| Date              | string |  現在情報を得ることや、現在のサイズに関連する変更わかりました日です。   |

### <a name="response-example"></a>応答の例

この要求の JSON 応答の本文の例を次に示します。

```json
{
  "Value": [
    {
      "applicationId": "9NBLGGGZ5QDR",
      "insightDate": "2018-06-03T00:00:00",
      "dataType": "health",
      "insightDetail": [
        {
          "FactName": "HitCount",
          "SubDimensions": [
            {
              "FactName:": "HitCount",
              "PercentChange": "21",
              "DimensionValue:": "DE",
              "FactValue": "109",
              "Direction": "Positive",
              "Date": "6/3/2018 12:00:00 AM",
              "DimensionName": "Market"
            }
          ],
          "DimensionValue": "crash",
          "Date": "6/3/2018 12:00:00 AM",
          "DimensionName": "EventType"
        },
        {
          "FactName": "HitCount",
          "SubDimensions": [
            {
              "FactName:": "HitCount",
              "PercentChange": "71",
              "DimensionValue:": "JP",
              "FactValue": "112",
              "Direction": "Positive",
              "Date": "6/3/2018 12:00:00 AM",
              "DimensionName": "Market"
            }
          ],
          "DimensionValue": "hang",
          "Date": "6/3/2018 12:00:00 AM",
          "DimensionName": "EventType"
        },
      ],
      "insightId": "9CY0F3VBT1AS942AFQaeyO0k2zUKfyOhrOHc0036Iwc="
    }
  ],
  "@nextLink": null,
  "TotalCount": 2
}
```

## <a name="related-topics"></a>関連トピック

* [インサイト レポート](../publish/insights-report.md)
* [Microsoft Store サービスを使った分析データへのアクセス](access-analytics-data-using-windows-store-services.md)