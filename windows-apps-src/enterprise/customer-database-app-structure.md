---
title: 顧客データベース アプリケーション構造
description: 顧客データベース チュートリアルでは、構造を確認およびその理由は方法が構築されます。
keywords: enterprise、チュートリアル、顧客、データ、crud
ms.date: 05/07/2018
ms.topic: article
ms.localizationpriority: med
ms.openlocfilehash: b1f8f8c8a2fd1522d8c304a45514d5257543f222
ms.sourcegitcommit: b034650b684a767274d5d88746faeea373c8e34f
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/06/2019
ms.locfileid: "57656617"
---
# <a name="customer-database-app-structure"></a>顧客データベース アプリケーション構造

複雑な基幹業務アプリ多くの場合がある多くのページと機能、優れたコードの行数。 このため、これが重要予測可能な構造を軸のアプリを設計することです。 エンタープライズのアプリに適したアプリケーション設計パターンをいくつかありますが、すべて大規模なアプリケーションを簡単に理解し、操作の目的に構築されます。

中に、[顧客データベース チュートリアル](customer-database-tutorial.md)シングル ページ アプリを表示します。 わかりやすくするために、アクションでは、この考え方を紹介するモデル-ビュー-ビューモデル (MVVM) アプリの設計パターンを実装します。 名前が示すように、MVVM デザイン パターンは、3 つのカテゴリにコア アプリケーション ロジックを分離します。

* モデルは、アプリケーションのデータを含むクラスです。
* ビューは、指定されたページの UI です。
* Viewmodel は、アプリケーション ロジックを提供します。 これは、ビューからユーザーのアクションの処理やモデルとの対話の管理に含めることができます。

このアプリは MVVM の完全なと architypical の例ではありませんは、主な懸念事項の分離の原則をアクションに表示します。 [ここで、アプリを確認します。](https://github.com/Microsoft/windows-tutorials-customer-database)

## <a name="application-structure"></a>アプリケーションの構造

アプリを開いた後の起動を調べることで、**ソリューション エクスプ ローラー。** 表示する前に、UWP アプリで作業したことがある場合に使い慣れたありますが、フォルダーのコレクションを確認しますの一部を保持するアプリのコンポーネント部分。

![アプリのソリューション エクスプ ローラーでの開始点](images/customer-database-tutorial/solution-explorer.png)

### <a name="views"></a>ビュー

すべてのアプリの UI は、ビュー フォルダーで定義されます。 このチュートリアルでは、シングル ページ アプリを今すぐはであるために、- 1 つのビューがあるつまり**CustomerListPage**します。 XAML UI のマークアップと xaml.cs コード分離の両方があります: これら 2 つのファイルが 1 つのビューを構成します。 UI 要素を追加する**CustomerListPage.xaml**します。

> [!NOTE]
> このアプリで、MainPage がないことに注意してください可能性があります。 指定しますだ**App.xaml.cs**アプリを起動する必要があります**CustomerListPage**を起動するとします。

### <a name="viewmodels"></a>ビューモデル

このアプリには、1 つのビューはのみが 2 つのビューモデルが。 それはなぜか。

**CustomerListPageViewModel.cs** MVVM パターンで標準的な ViewModel が。 アプリのページの基本的なロジックがあるあり、ページ操作する、チュートリアルでは、ほとんどをお勧めします。 ユーザーが行うすべての UI アクションは、表示を使用して処理するためには、このビューモデルに渡されます。

**CustomerViewModel.cs**、ただし、特定の任意のビューに関連付けられていません。 代わりに、個々 の顧客のモデルに含まれるデータ (プロパティを編集した) プログラムの概念を関連付けます。

### <a name="models"></a>モデル

このアプリには、アプリのデータを格納し、リポジトリと対話するためのインターフェイスを提供する 3 つのモデルが含まれています。 これらは、アプリの重要な部分が、このチュートリアルで直接編集することはありません。

最も重要なは**Customer.cs**チュートリアルで使用する顧客のデータ構造をについて説明します。

> [!NOTE]
> このチュートリアルでは無視されます、*電子メール*と*Phone* Customer オブジェクトのプロパティ。 超える場合提示された内容ここでは、アプリの UI にこれら 2 つのプロパティを追加することが優れた第一歩です。

### <a name="repository"></a>リポジトリ

リポジトリ フォルダーには、構築し、ローカルの SQLite データベースと対話するクラスが含まれています。 チュートリアルでは、SQLite データベースとして表示されます。-です。 コードに追加するときに**CustomerListPageViewModel.cs**これらのクラスによって定義されたメソッドを呼び出すには、それらを設定するために変更を加える必要はありません。

詳細については、UWP での SQLite[この記事を参照してください](../data-access/sqlite-databases.md)します。

チュートリアルの「進む」セクションしようとすると、これは、リモートの残りのデータベースに接続するためのクラスを作成します。 実装も、 **ICustomerRepository**インターフェイスは、モデルのセクションで定義されているが、SQLite の対応するよりも大きく異なることになります。

### <a name="other-elements"></a>その他の要素

アプリケーションの起動の動作が定義されている UWP アプリの通常は、 **App.xaml.cs**クラス。 次のコードのほとんどは、すべての UWP アプリの既定のコードです。 ただし、いくつかの小さな変更を既に行いました。

* アプリを表示することを指定しました**CustomerListPage**で起動します。
* 使用してデータ ソースを保持するリポジトリ オブジェクトを作成しました。
* 追加されました、 **SQLiteDatabase**メソッドでは、ローカル データベースを初期化し、指定されたリポジトリとして設定します。

「進む」セクションしようとすると、残りの部分のリポジトリ オブジェクトを初期化するために同様のメソッドを追加します。 当社の懸念事項を分離した SQLite と REST の両方の操作に対して定義されているのと同じインターフェイスを使用しているため、SQLite の代わりに、アプリで REST を使用して変更する必要がありますのみの既存のコードになります。

## <a name="next-steps"></a>次のステップ

チュートリアルでは、既に完了しているかどうかは、チェック アウトすることができます、[完全なサンプル アプリ](https://github.com/Microsoft/Windows-appsample-customers-orders-database)を大きな規模でこれらの機能を実装する方法を確認します。

それがなぜすべてはことがわかったら、する必要があります[のチュートリアルに戻り](customer-database-tutorial.md)だけを取り上げました構造を処理します。