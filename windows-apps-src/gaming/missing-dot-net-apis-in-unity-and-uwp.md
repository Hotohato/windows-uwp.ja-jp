---
title: Unity や UWP で不足している .NET API
description: Unity で UWP ゲームを作成する際に不足している .NET API と、一般的な問題に対する回避策について説明します。
ms.assetid: 28A8B061-5AE8-4CDA-B4AB-2EF0151E57C1
ms.date: 02/21/2018
ms.topic: article
keywords: Windows 10、UWP、ゲーム、.NET、Unity
ms.localizationpriority: medium
ms.openlocfilehash: df93fbeb3a879a84873827a5ead926f96b02adcc
ms.sourcegitcommit: 8ee0752099170aaf96c7cb105f7cc039b6e7ff06
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/08/2020
ms.locfileid: "80968058"
---
# <a name="missing-net-apis-in-unity-and-uwp"></a>Unity や UWP で不足している .NET API

.NET を使用して UWP ゲームを作成する場合、Unity エディターで使用できる一部の API やスタンドアロン PC ゲーム用の一部の API が UWP 用に存在しないことがあります。 これは、UWP アプリ用 .NET には、名前空間ごとに、フル バージョンの .NET Framework で提供される型のサブセットが含まれるためです。

さらに、Unity の Mono など一部のゲーム エンジンは、UWP 用の .NET とは完全な互換性のない別の種類の .NET を使用しています。 したがって、ゲームを作成する際に、エディターでは問題なく動作しても、UWP 用にビルドすると、"**型または名前空間の名前 'Formatters' は名前空間 'System.Runtime.Serialization' に存在しません (アセンブリ参照があることを確認してください)** " というエラーが出力される可能性があります。

幸いなことに、Unity では、これらの不足している API の一部を拡張メソッドや置換型として提供しています。詳しくは、[ユニバーサル Windows プラットフォームの .NET Scripting Backend で不足している .NET 型に関するページ](https://docs.unity3d.com/Manual/windowsstore-missingtypes.html)をご覧ください。 ただし、必要な機能がここにない場合は、「[Windows ストア アプリ用 .NET の概要](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140))」で説明している方法に従って、WinRT または UWP 用 .NET の API を使用するようにコードを変換できます (このページでは、Windows 8 について説明していますが、Windows 10 の UWP アプリにも適用できます)。

## <a name="net-standard"></a>.NET Standard

一部の API が動作しない理由を理解するには、.NET のさまざまな種類と、UWP での .NET の実装方法を理解しておく必要があります。 [.NET Standard](https://docs.microsoft.com/dotnet/standard/net-standard) は、クロスプラットフォームに対応し、さまざまな種類の .NET を統一することを目的とした、.NET API の正式な仕様です。 .NET の各実装では、特定のバージョンの .NET Standard をサポートしています。 標準や実装の一覧表については、「[.NET 実装のサポート](https://docs.microsoft.com/dotnet/standard/net-standard#net-implementation-support)」をご覧ください。

UWP SDK の各バージョンは、.NET Standard のさまざまなレベルに準拠しています。 たとえば、16299 SDK (Fall Creators Update) では、.NET Standard 2.0 をサポートしています。

ターゲットにしている UWP バージョンで特定の .NET API がサポートされるかどうかを確認する場合は、[.NET Standard API リファレンス](https://docs.microsoft.com/dotnet/api/index?view=netstandard-2.0)を参照して、そのバージョンの UWP でサポートされている .NET Standard のバージョンを選択します。

## <a name="scripting-backend-configuration"></a>スクリプト バックエンドの構成

UWP のビルドで問題が発生した場合に最初に行うことは、 **[Player Settings] (プレーヤーの設定)** ( **[File] (ファイル) > [Build Settings] (ビルド設定)** 、 **[Universal Windows Platform] (ユニバーサル Windows プラットフォーム)** 、 **[Player Settings] (プレーヤーの設定)** の順に選択) を確認することです。 **[Other Settings] (その他の設定) > [Configuration] (構成)** にある、最初の 3 つのドロップダウン リスト ( **[Scripting Runtime Version] (スクリプト ランタイム バージョン)** 、 **[Scripting Backend] (スクリプト バックエンド)** 、 **[Api Compatibility Level] (API 互換性レベル)** ) はいずれも考慮する必要がある重要な設定です。

**[Scripting Runtime Version] (スクリプト ランタイム バージョン)** は、Unity スクリプト バックエンドが使用するもので、選択した .NET Framework サポートと (ほぼ) 同等のバージョンを取得できます。 ただし、そのバージョンの .NET Framework のすべての API がサポートされるわけではない点に注意してください。UWP がターゲットにしている .NET Standard のバージョンでサポートされている API のみがサポートされます。

新しい .NET リリースでは、通常、より多くの API が .NET Standard に追加され、スタンドアロンと UWP で同じコードを使用できるようになっている場合があります。 たとえば、[System.Runtime.Serialization.Json](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.json) 名前空間は .NET Standard 2.0 で導入されました。 **[Scripting Runtime Version] (スクリプト ランタイム バージョン)** を **[.NET 3.5 Equivalent] (.NET 3.5 相当)** (以前のバージョンの .NET Standard をターゲットにする) に設定した場合、API を使用しようとしたときにエラーが表示されます。 **[.NET 4.6 Equivalent] (.NET 4.6 相当)** (.NET Standard 2.0 をサポートする) に切り替えると、API は動作します。

**[Scripting Backend] (スクリプト バックエンド)** は、 **[.NET]** または **[IL2CPP]** に設定できます。 このトピックでは、.NET で発生する問題について説明しているため、 **[.NET]** を選択していることを想定しています。 詳細については、[スクリプト バックエンドに関するページ](https://docs.unity3d.com/Manual/windowsstore-scriptingbackends.html)をご覧ください。

最後に、 **[Api Compatibility Level] (API 互換性レベル)** を、ゲームを実行する .NET のバージョンに設定します。 これは、 **[Scripting Runtime Version] (スクリプト ランタイム バージョン)** と一致している必要があります

一般的に、 **[Scripting Runtime Version] (スクリプト ランタイム バージョン)** と **[Api Compatibility Level] (API 互換性レベル)** については、.NET Framework との互換性を高め、より多くの .NET API を使用できるようにするために、利用可能な最新バージョンを選択してください。

![構成: スクリプト ランタイム バージョン、スクリプト バックエンド、API 互換性レベル](images/missing-dot-net-apis-in-unity-1.png)

## <a name="platform-dependent-compilation"></a>プラットフォーム依存のコンパイル

UWP を含む複数のプラットフォーム用に Unity ゲームを作成する場合、ゲームが UWP として作成されたときにのみ、UWP 向けのコードが実行されるようにするために、プラットフォーム依存のコンパイルを使用できます。 これにより、スタンドアロンのデスクトップやその他のプラットフォーム用にフル バージョンの .NET Framework を、UWP 用に WinRT API を使用でき、ビルド エラーが発生することはありません。

UWP アプリとして実行する場合にのみコードをコンパイルするには、次のディレクティブを使用します。

```csharp
#if NETFX_CORE
    // Your UWP code here
#else
    // Your standard code here
#endif
```

> [!NOTE]
> `NETFX_CORE` は、.NET スクリプトバックエンドに対してC#コードをコンパイルしているかどうかを確認することだけを目的としています。 別のスクリプトバックエンド (IL2CPP など) を使用している場合は、代わりに[`ENABLE_WINMD_SUPPORT`](https://docs.unity3d.com/Manual/windowsstore-code-snippets.html)を使用します。

プラットフォーム依存のコンパイル ディレクティブの一覧については、[プラットフォーム依存のコンパイルに関するページ](https://docs.unity3d.com/Manual/PlatformDependentCompilation.html)をご覧ください。

## <a name="common-issues-and-workarounds"></a>一般的な問題と回避方法

次のシナリオでは、UWP のサブセットに .NET API がない場合に発生する可能性がある一般的な問題と、それらを回避する方法について説明します。

### <a name="data-serialization-using-binaryformatter"></a>BinaryFormatter を使用したデータのシリアル化

ゲームでは、プレイヤーが簡単に操作ができないように、セーブ データをシリアル化するのが一般的です。 ただし、オブジェクトをバイナリにシリアル化する [BinaryFormatter](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.formatters.binary.binaryformatter) は、.NET Standard の以前のバージョン (2.0 よりも前) では使用できません。 代わりに、[XmlSerializer](https://docs.microsoft.com/dotnet/api/system.xml.serialization.xmlserializer) または [DataContractJsonSerializer](https://docs.microsoft.com/dotnet/api/system.runtime.serialization.json.datacontractjsonserializer) を使用することを検討してください。

```csharp
private void Save()
{
    SaveData data = new SaveData(); // User-defined object to serialize

    DataContractJsonSerializer serializer = 
      new DataContractJsonSerializer(typeof(SaveData));

    FileStream stream = 
      new FileStream(Application.persistentDataPath, FileMode.CreateNew);

    serializer.WriteObject(stream, data);
    stream.Dispose();
}
```

### <a name="io-operations"></a>I/O 操作

[System.IO](https://docs.microsoft.com/dotnet/api/system.io) 名前空間の一部の型 ([FileStream](https://docs.microsoft.com/dotnet/api/system.io.filestream) など) は、以前のバージョンの .NET Standard では使用できません。 ただし、Unity では、[Directory](https://docs.microsoft.com/dotnet/api/system.io.directory)、[File](https://docs.microsoft.com/dotnet/api/system.io.file)、および **FileStream** 型を提供しているため、ゲームでこれらを使用できます。

また、UWP アプリでのみ利用できる [Windows.Storage](https://docs.microsoft.com/uwp/api/Windows.Storage) API を使用することもできます。 ただし、これらの API では、アプリの書き込みは特定の記憶域に制限され、ファイル システム全体に自由にアクセスすることはできません。 詳しくは、「[ファイル、フォルダー、およびライブラリ](https://docs.microsoft.com/windows/uwp/files/)」をご覧ください。

重要な注意事項は、[Close](https://docs.microsoft.com/dotnet/api/system.io.stream.close) メソッドは、.NET Standard 2.0 以降でのみ利用できることです (ただし、Unity は拡張メソッドを提供しています)。 代わりに、[Dispose](https://docs.microsoft.com/dotnet/api/system.io.stream.dispose) を使用します。

### <a name="threading"></a>スレッド化

[System.Threading](https://docs.microsoft.com/dotnet/api/system.threading) 名前空間の一部の型 ([ThreadPool](https://docs.microsoft.com/dotnet/api/system.threading.threadpool) など) は、以前のバージョンの .NET Standard では使用できません。 このような場合は、代わりに [Windows.System.Threading](https://docs.microsoft.com/uwp/api/windows.system.threading) 名前空間を使用できます。

以下に、プラットフォーム依存のコンパイルを使用し、UWP と UWP 以外の両方のプラットフォーム用に準備して、Unity ゲームでスレッド化を処理する方法を示します。

```csharp
private void UsingThreads()
{
#if NETFX_CORE
    Windows.System.Threading.ThreadPool.RunAsync(workItem => SomeMethod());
#else
    System.Threading.ThreadPool.QueueUserWorkItem(workItem => SomeMethod());
#endif
}
```

### <a name="security"></a>セキュリティ

**System.Security.** * 名前空間の一部 ([System.Security.Cryptography.X509Certificates](https://docs.microsoft.com/dotnet/api/system.security.cryptography.x509certificates?view=netstandard-2.0) など) は、UWP 用に Unity ゲームを構築する場合は利用できません。 このような場合は、同じ機能の多くをカバーしている **Windows.Security.** * API を使用します。

次の例では、指定した名前の証明書ストアからの証明書だけを取得します。

```cs
private async void GetCertificatesAsync(string certStoreName)
    {
#if NETFX_CORE
        IReadOnlyList<Certificate> certs = await CertificateStores.FindAllAsync();
        IEnumerable<Certificate> myCerts = 
            certs.Where((certificate) => certificate.StoreName == certStoreName);
#else
        X509Store store = new X509Store(certStoreName, StoreLocation.CurrentUser);
        store.Open(OpenFlags.OpenExistingOnly);
        X509Certificate2Collection certs = store.Certificates;
#endif
    }
```

WinRT セキュリティ API の使用方法の詳細については、「[セキュリティ](https://docs.microsoft.com/windows/uwp/security/)」を参照してください。

### <a name="networking"></a>ネットワーク

**System&period;Net.** * 名前空間の一部 ([System.Net.Mail](https://docs.microsoft.com/dotnet/api/system.net.mail?view=netstandard-2.0) など) も、UWP 用に Unity ゲームを構築する場合は利用できません。 これらの API のほとんどについては、対応する **Windows.Networking.** * と **Windows.Web.** * WinRT API を使用して同様の機能を実現できます。 詳細については、「[ネットワークと Web サービス](https://docs.microsoft.com/windows/uwp/networking/)」を参照してください。

**System.Net.Mail** の場合は、[Windows.ApplicationModel.Email](https://docs.microsoft.com/uwp/api/windows.applicationmodel.email) 名前空間を使用します。 詳細については、「[メールの送信](https://docs.microsoft.com/windows/uwp/contacts-and-calendar/sending-email)」を参照してください。

## <a name="see-also"></a>参照

* [ユニバーサル Windows プラットフォーム: .NET Scripting バックエンドに .NET 型がない](https://docs.unity3d.com/Manual/windowsstore-missingtypes.html)
* [UWP アプリ用 .NET の概要](https://docs.microsoft.com/previous-versions/windows/apps/br230302(v=vs.140))
* [Unity UWP 移植ガイド](https://unity3d.com/partners/microsoft/porting-guides)