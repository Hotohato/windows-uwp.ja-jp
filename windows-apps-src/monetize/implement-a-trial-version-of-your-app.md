---
ms.assetid: 571697B7-6064-4C50-9A68-1374F2C3F931
description: Windows.Services.Store 名前空間を使って、アプリの試用版の実装する方法について説明します。
title: アプリの試用版の実装
keywords: Windows 10, UWP, 試用版, アプリ内購入, Windows.Services.Store
ms.date: 08/25/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 47affd7e54bcaad21949cb56916de27dd3bf260b
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371061"
---
# <a name="implement-a-trial-version-of-your-app"></a>アプリの試用版の実装

場合する[パートナー センターで無料試用版としてアプリを構成する](../publish/set-app-pricing-and-availability.md#free-trial)顧客を使用できるように、アプリを無料の試用期間中、除外の一部の機能を制限したりすることによって、アプリの完全なバージョンにアップグレードするお客様を引き込むことができます試用期間中。 どのような機能を制限するかをコーディング開始前に決め、完全なライセンスが購入されたときにだけその機能が正しく動作するようにアプリを設定します。 また、ユーザーがアプリを購入する前の試用期間中にだけバナーや透かしなどを表示する機能を有効にすることもできます。

この記事では、[Windows.Services.Store](https://docs.microsoft.com/uwp/api/windows.services.store) 名前空間の [StoreContext](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) クラスのメンバーを使用して、アプリの試用ライセンスがユーザーにあるかどうかを判定したり、アプリの実行中にライセンスが変更されたときに通知を受け取る方法を説明します。 

> [!NOTE]
> **Windows.Services.Store** 名前空間は、Windows 10 バージョン 1607 で導入され、Visual Studio で、**Windows 10 Anniversary Edition (10.0、ビルド 14393)** 以降のリリースをターゲットとするプロジェクトでのみ使用できます。 アプリが Windows 10 の以前のバージョンをターゲットする場合、**Windows.Services.Store** 名前空間の代わりに [Windows.ApplicationModel.Store](https://docs.microsoft.com/uwp/api/windows.applicationmodel.store) 名前空間を使う必要があります。 詳しくは、[こちらの記事](exclude-or-limit-features-in-a-trial-version-of-your-app.md)をご覧ください。

## <a name="guidelines-for-implementing-a-trial-version"></a>試用版を実装するためのガイドライン

アプリの現時点でのライセンスの状態は、[StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense) クラスのプロパティとして保存されています。 通常は、次の手順で説明するように、ライセンスの状態に依存する関数を条件ブロック内に記述します。 このような機能について検討するときには、ライセンスがどの状態であっても動作するように実装できることを確認してください。

また、アプリの実行中にライセンスが変更された場合の処理方法を決めておきます。 試用版のアプリでもすべての機能を使うことができるようにしながら、購入版では表示されない広告バナーを表示することができます。 また、試用版アプリでは一部の機能を無効にしたり、ユーザーに購入を勧めるメッセージを表示したりすることもできます。

アプリの性質を考慮して、それに適した試用や有効期限の戦略を立ててください。 ゲームの試用版の場合は、ユーザーが遊べるゲーム コンテンツの量を制限するのが良い戦略でしょう。 ユーティリティの試用版の場合は、有効期限日の設定や、ユーザーが使いたがるような機能の制限を検討するとよいでしょう。

ゲーム以外の多くのアプリでは、ユーザーにアプリ全体を理解してもらうために、有効期限日を設定するのが適しています。 ここでは、有効期限に関するいくつかの一般的なシナリオと、その処理方法について説明します。

-   **アプリの実行中に試用版ライセンスの有効期限します。**

    アプリの実行中に試用ライセンスが期限切れになった場合は、次の対処方法があります。

    -   何もしない。
    -   ユーザーにメッセージを表示する。
    -    を閉じます。
    -   ユーザーにアプリの購入を促す。

    お勧めするのは、アプリの購入を促すメッセージを表示することです。ユーザーがアプリを購入したら、すべての機能を有効にして、そのまま使うことができるようにします。 購入しなかった場合は、アプリを閉じるか、アプリの購入が必要なことを一定の間隔で通知します。

-   **アプリを起動する前に試用版ライセンスの有効期限します。**

    ユーザーがアプリを起動する前に試用ライセンスが期限切れになった場合、アプリは起動しません。 ユーザーには、ストアからそのアプリを購入できることを伝えるダイアログ ボックスが表示されます。

-   **実行中のアプリを購入した顧客**

    アプリの実行中にユーザーがアプリを購入した場合は、次の対処方法があります。

    -   何もせず、アプリが再起動されるまでは試用モードを続ける。
    -   購入に対するお礼をする、またはメッセージを表示する。
    -   完全なライセンスがある場合に使うことができる機能を、通知なしで有効にする (または、試用版であることを示す表示を消す)。

アプリの動作でユーザーが驚くことがないように、無料試用版のアプリが試用期間中にどのように機能し、期間が過ぎるとどのようになるかを必ず説明してください。 アプリの説明について詳しくは、「[アプリの説明の作成](https://docs.microsoft.com/windows/uwp/publish/create-app-descriptions)」をご覧ください。

## <a name="prerequisites"></a>前提条件

この例には、次の前提条件があります。
* **Windows 10 Anniversary Edition (10.0、ビルド 14393)** 以降のリリースをターゲットとするユニバーサル Windows プラットフォーム (UWP) アプリの Visual Studio プロジェクト。
* として構成されているパートナー センターでアプリを作成した、[無料試用版](https://docs.microsoft.com/windows/uwp/publish/set-app-pricing-and-availability)時間制限なしでこのアプリがストアで公開されています。 必要に応じで、テスト中にストアでアプリを検索できないようにアプリを構成することも可能です。 詳しくは、[テスト ガイダンス](in-app-purchases-and-trials.md#testing)をご覧ください。

この例のコードは、次の点を前提としています。
* コードは、```workingProgressRing``` という名前の [ProgressRing](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.progressring) と ```textBlock``` という名前の [TextBlock](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textblock) を含む [Page](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.page) のコンテキストで実行されます。 これらのオブジェクトは、それぞれ非同期操作が発生していることを示するためと、出力メッセージを表示するために使用されます。
* コード ファイルには、**Windows.Services.Store** 名前空間の **using** ステートメントがあります。
* アプリは、アプリを起動したユーザーのコンテキストでのみ動作するシングル ユーザー アプリです。 詳しくは、「[アプリ内購入と試用版](in-app-purchases-and-trials.md#api_intro)」をご覧ください。

> [!NOTE]
> [デスクトップ ブリッジ](https://developer.microsoft.com/windows/bridges/desktop)を使用するデスクトップ アプリケーションがある場合、この例には示されていないコードを追加して [StoreContext ](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext) オブジェクトを構成することが必要になることがあります。 詳しくは、「[デスクトップ ブリッジを使用するデスクトップ アプリケーションでの StoreContext クラスの使用](in-app-purchases-and-trials.md#desktop)」をご覧ください。

## <a name="code-example"></a>コードの例

アプリを初期化するときに、アプリの [StoreAppLicense](https://docs.microsoft.com/uwp/api/windows.services.store.storeapplicense) オブジェクトを取得し、アプリの実行中にライセンスが変更されたときに通知を受け取る [OfflineLicensesChanged](https://docs.microsoft.com/uwp/api/windows.services.store.storecontext.offlinelicenseschanged) イベントを処理します。 アプリのライセンスが変更されるのは、たとえば、試用期間が終了したときや、ユーザーがストアを通じてアプリを購入したときです。 ライセンスが変更されるときに、新しいライセンスを入手し、必要に応じてアプリの機能を有効または無効にします。

この時点で、ユーザーがアプリを購入したら、ライセンスの状態が変わったことをユーザーに知らせることをお勧めします。 コーディングの方法上、必要であれば、ユーザーにアプリを再起動してもらわなければならないこともあります。 ただし、この移行は可能な限りスムーズで違和感のないようにする必要があります。

> [!div class="tabbedCodeSnippets"]
[!code-csharp[ImplementTrial](./code/InAppPurchasesAndLicenses_RS1/cs/ImplementTrialPage.xaml.cs#ImplementTrial)]

完全なサンプル アプリケーションについては、[ストア サンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)をご覧ください。

## <a name="related-topics"></a>関連トピック

* [アプリ内購入と試用版](in-app-purchases-and-trials.md)
* [アプリケーションとアドオンの製品情報を取得します。](get-product-info-for-apps-and-add-ons.md)
* [アプリケーションとアドオンのライセンス情報を取得します。](get-license-info-for-apps-and-add-ons.md)
* [アプリケーションとアドオンのアプリ内購入を有効にします。](enable-in-app-purchases-of-apps-and-add-ons.md)
* [使用できるアドオンの購入を有効にします。](enable-consumable-add-on-purchases.md)
* [ストアのサンプル](https://github.com/Microsoft/Windows-universal-samples/tree/master/Samples/Store)
