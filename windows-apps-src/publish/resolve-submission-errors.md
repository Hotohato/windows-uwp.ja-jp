---
Description: ストアにアプリを提出した後にエラーが発生した場合、「認定プロセス」を続行するためにそれを解決する必要があります。
title: 申請エラーの解決
ms.assetid: 68199E09-0C66-4EB4-BFE8-D2EEB139C4F3
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: f67474905f4c689153af4dec22cf05f8399db535
ms.sourcegitcommit: b52ddecccb9e68dbb71695af3078005a2eb78af1
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 11/20/2019
ms.locfileid: "74258988"
---
# <a name="resolve-submission-errors"></a>申請エラーの解決

ストアにアプリを提出した後にエラーが発生した場合、「[認定プロセス](the-app-certification-process.md)」を続行するためにそれを解決する必要があります。 エラー メッセージには、どのような問題があるのか、問題を修正するために何をする必要があるのかが示されます。 以下に、これらのエラーの解決に役立つ追加情報をいくつか示します。

## <a name="uwp-apps"></a>UWP アプリ

UWP アプリを送信する場合、パッケージファイルが Visual Studio によってストア用に生成された msixupload または .appxupload ファイルでない場合、前処理中にエラーが表示されることがあります。 「アプリのパッケージファイルを作成するときに[Visual Studio で UWP アプリをパッケージ化](/windows/msix/package/packaging-uwp-apps)する」の手順に従って、msixupload または .appxupload ファイルのみを送信の[パッケージ](upload-app-packages.md)ページでアップロードします。これは、. msix/appx または .msixbundle/.appxbundle ではありません。

コンパイル エラーが表示される場合は、リリース モードでアプリケーションを正常にビルドできることを確認します。 詳しくは、[.NET ネイティブ内部コンパイラ エラーに関するページ](https://github.com/dotnet/core/blob/master/Documentation/ilcRepro.md)をご覧ください。

## <a name="desktop-application"></a>デスクトップアプリケーション

Win32 と UWP バイナリの両方を含むパッケージを送信する場合は、Visual Studio 2017 Update 4 以降のバージョンで使用できる Windows パッケージングプロジェクトを使用して、そのパッケージを作成してください。 UWP プロジェクトテンプレートを使用してパッケージを作成した場合、そのパッケージをストアに送信したり、他の Pc にサイドロードしたりすることはできません。 パッケージが正常に発行された場合でも、ユーザーの PC で予期しない方法で動作する可能性があります。 詳細については、「 [Visual Studio を使用したアプリのパッケージ化 (デスクトップブリッジ)]( https://docs.microsoft.com/windows/uwp/porting/desktop-to-uwp-packaging-dot-net)」を参照してください。

## <a name="windows-phone-8x-and-earlier"></a>Windows Phone 8. x 以前

> [!IMPORTANT]
> 2018年10月31日の時点で、新しく作成された製品には Windows Phone 8. x 以前を対象とするパッケージを含めることはできません。 詳細については、こちらの[ブログ投稿](https://blogs.windows.com/windowsdeveloper/2018/08/20/important-dates-regarding-apps-with-windows-phone-8-x-and-earlier-and-windows-8-8-1-packages-submitted-to-microsoft-store)を参照してください。

前処理中に Windows Phone のパッケージに関する問題が検出されると、**エラー 2001** が表示されることがあります。 ほとんどの場合は、アプリのパッケージをリビルドしてエラーを修正する必要があります。 処理が完了したら、[パッケージ](upload-app-packages.md) ページで古いパッケージを新しいパッケージに置き換えてから、 **[ストアに提出]** をもう一度クリックします。

このエラーの原因になる問題はいくつかあります。 次の一覧を調べて、どの問題が自分のパッケージに当てはまるか判断してください。

-   **パッケージ内の 1 つ以上のアセンブリが不正に暗号化されています:** 別のツールを使用して暗号化を実行するか、暗号化を削除します。 コンパイル プロセスによって暗号化アセンブリが最適化されますが、サポート外の方法で MSIL を変更するツールによって一部のアセンブリが暗号化されているため、エラーが発生する場合があります。
-   **アプリ内の 1 つ以上のメソッドのサイズが、256 KB の IL を超過しています:** 問題のあるメソッドをリファクタリングして、複数の小さい関数にします。 アセンブリ内のメソッドの MSIL のサイズは、ILDASM ツールを使って判別できます。
-   **1 つ以上のアセンブリで、厳密な名前の署名確認に失敗しました:** このエラーは通常、アセンブリ メタデータで必要なキーとは異なるキーを使用して、厳密な名前の署名が実行された場合に発生します。 正しいキーを使用して署名するか、または厳密な名前の署名を削除します。
-   **パッケージに混合モード (マネージ コードとネイティブ コードの) アセンブリが含まれています:** Windows Phone では、混合モード アセンブリはサポートされていません。 パッケージから混合モード アセンブリを削除し、アプリを再申請します。
-   **Windows Phone 8.1 XAP または appx/appxbundle アセンブリが無効です:** .winmd ファイルに公開エントリ ポイントが少なくとも 1 つあることを確認します。 必要に応じて、逆コンパイラ アプリケーションを使ってコードを確認し、公開エントリ ポイントがあるかどうかをチェックすることができます。

アプリの提出後に発生する可能性のある他のエラーは、**エラー 1300** です。 これは、1 つ以上のアセンブリ (またはパッケージ全体) が既にプリコンパイルされているときに発生します。 この問題を解決するには、Microsoft Visual Studio でアプリのパッケージをリビルドし、新たに生成されたパッケージを提出します。

## <a name="nameidentity-errors"></a>名前/ID エラー

「**パッケージ内で見つかった名前が、予約したアプリ名のいずれにも該当しません。アプリ名を予約するか、この言語に対応した正しいアプリ名でパッケージを更新してください**」ということを示すエラーが表示された場合、パッケージに誤った名前を入力した可能性があります。 このエラーは、パートナーセンターで予約していないアプリ名を使用している場合にも発生する可能性があります。 通常、このエラーは次の手順に従って解決できます。

- アプリの [[アプリ ID]](view-app-identity-details.md) ページ ( **[アプリ管理]** の下) に移動して、アプリに ID が割り当てられているかどうかを確認します。 割り当てられていない場合は、ID を作成するオプションが表示されます。 ID を作成するためには、アプリの名前を予約する必要があります。 その名前がパッケージで使用されている名前であることを確認してください。
- アプリに ID が既に割り当てられているときでも、場合によっては、パッケージで使用する名前を予約する必要があります。 **[アプリ管理]** で、[[アプリ名の管理]](manage-app-names.md) をクリックします。 使用する名前を入力し、 **[アプリ名を予約]** をクリックします。

> [!IMPORTANT]
>  必要な名前が利用できない場合、その名前は、他のアプリによって既に予約済みである可能性があります。 アプリがその名前で既に公開されている場合、またはその名前を使用する権利があると考えられる場合は、[サポートにお問い合わせください](https://support.microsoft.com/getsupport/hostpage.aspx?locale=EN-US&supportregion=EN-US&ccfcode=US&ln=EN-US&pesid=14654&oaspworkflow=start_1.0.0.0&tenant=store&supporttopic_L1=31762156&supporttopic_L2=31762179)。  

 

 




