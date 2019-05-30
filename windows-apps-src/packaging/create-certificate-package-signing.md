---
title: パッケージ署名用証明書を作成する
description: PowerShell ツールを使ってアプリ パッケージ署名用を作成し、エクスポートします。
ms.date: 09/30/2018
ms.topic: article
keywords: windows 10, uwp
ms.assetid: 7bc2006f-fc5a-4ff6-b573-60933882caf8
ms.localizationpriority: medium
ms.openlocfilehash: 1476410c96900eff7ba4b8d0ad34c9d7b5599434
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66372737"
---
# <a name="create-a-certificate-for-package-signing"></a>パッケージ署名用証明書を作成する


この記事では、PowerShell ツールを使用して、アプリ パッケージ署名用の証明書を作成してエクスポートする方法について説明します。 Visual Studio を使用して [UWP アプリをパッケージ化する](https://docs.microsoft.com/windows/uwp/packaging/packaging-uwp-apps)ことをお勧めしますが、Visual Studio を使用してアプリを開発していない場合は、ストア対応アプリを手動でパッケージ化することができます。

> [!IMPORTANT] 
> Visual Studio を使用してアプリを開発する場合は、Visual Studio のウィザードを使って証明書をインポートし、アプリ パッケージに署名することをお勧めします。 詳しくは、「[Visual Studio での UWP アプリのパッケージ化](https://docs.microsoft.com/windows/uwp/packaging/packaging-uwp-apps)」をご覧ください。

## <a name="prerequisites"></a>前提条件

- **パッケージまたはパッケージ化されていないアプリ**  
AppxManifest.xml ファイルを含むアプリ。 マニフェスト ファイルを参照して、最終的なアプリ パッケージの署名に使われる証明書を作成する必要があります。 手動でアプリをパッケージ化する方法について詳しくは、「[MakeAppx.exe ツールを使ってアプリ パッケージを作成する](https://docs.microsoft.com/windows/uwp/packaging/create-app-package-with-makeappx-tool)」をご覧ください。

- **公開キー基盤 (PKI) のコマンドレット**  
署名証明書を作成およびエクスポートするには、PKI コマンドレットが必要です。 詳しくは、「[公開キー基盤コマンドレット](https://docs.microsoft.com/powershell/module/pkiclient/)」をご覧ください。

## <a name="create-a-self-signed-certificate"></a>自己署名証明書を作成する

自己署名証明書は、ストアに発行する準備ができた前に、アプリのテストに便利です。 自己署名証明書を作成するには、このセクションで説明されている手順に従います。

> [!NOTE]
> 自己署名証明書は厳密にテストします。 ストア、またはその他の会場からアプリを発行する準備ができたら、証明書を信頼できるソースに切り替えます。 これに失敗すると、アプリが、顧客にインストールすることができない可能性があります。

### <a name="determine-the-subject-of-your-packaged-app"></a>パッケージ アプリのサブジェクトを決定する  

証明書を使ってアプリ パッケージに署名するには、証明書の「サブジェクト」がアプリのマニフェストの [Publisher] セクションと**一致する必要**があります。

たとえば、アプリの AppxManifest.xml ファイルの [Identity] セクションは、次のようになります。

```xml
  <Identity Name="Contoso.AssetTracker" 
    Version="1.0.0.0" 
    Publisher="CN=Contoso Software, O=Contoso Corporation, C=US"/>
```

この例では [Publisher] は "CN=Contoso Software, O=Contoso Corporation, C=US" で、これを証明書の作成に使用する必要があります。

### <a name="use-new-selfsignedcertificate-to-create-a-certificate"></a>**New-SelfSignedCertificate** を使って証明書を作成する

PowerShell コマンドレット **New-SelfSignedCertificate** を使用して自己署名証明書を作成します。 **New-SelfSignedCertificate** にはカスタマイズのためのいくつかのパラメーターがありますが、この記事では、**SignTool** で動作する簡単な証明書の作成を中心に説明します。 このコマンドレットの使用とその例について詳しくは、「[New-SelfSignedCertificate](https://docs.microsoft.com/powershell/module/pkiclient/New-SelfSignedCertificate)」をご覧ください。

前の例の AppxManifest.xml ファイルに基づいて、次の構文を使用して証明書を作成する必要があります。 管理者特権の PowerShell プロンプトで、次のコマンドを実行します。

```powershell
New-SelfSignedCertificate -Type Custom -Subject "CN=Contoso Software, O=Contoso Corporation, C=US" -KeyUsage DigitalSignature -FriendlyName "Your friendly name goes here" -CertStoreLocation "Cert:\LocalMachine\My" -TextExtension @("2.5.29.37={text}1.3.6.1.5.5.7.3.3", "2.5.29.19={text}")
```

一部のパラメーターの詳細については、次の詳細を確認してください。

- **KeyUsage**:このパラメーターは、証明書が使用目的を定義します。 自己署名証明書の場合にこのパラメーターを設定する必要があります**DigitalSignature**します。

- **TextExtension**:このパラメーターには、次の拡張機能の設定が含まれます。

  - 拡張キー使用法 (EKU)。この拡張機能では、他の目的で、認定公開キーを使えることを示します。 自己署名証明書では、このパラメーターは、拡張子の文字列を含める必要があります **"2.5.29.37={text}1.3.6.1.5.5.7.3.3"** 、証明書がコード署名に使用することを示します。

  - 基本的な制約:この拡張機能は、証明書が証明書機関 (CA) かどうかを示します。 自己署名証明書では、このパラメーターは、拡張子の文字列を含める必要があります **"2.5.29.19={text}"** 、証明書がエンド エンティティ (CA ではない) を示します。

このコマンドを実行すると、"-CertStoreLocation" パラメーターで指定されたローカル証明書ストアに証明書が追加されます。 コマンドの結果には、証明書の拇印も生成されます。  

次のコマンドを使って、PowerShell ウィンドウで証明書を表示できます。

```powershell
Set-Location Cert:\LocalMachine\My
Get-ChildItem | Format-Table Subject, FriendlyName, Thumbprint
```

これにより、ローカル ストア内のすべての証明書が表示されます。

## <a name="export-a-certificate"></a>証明書のエクスポート 

ローカル ストアの証明書を Personal Information Exchange (.pfx) ファイルにエクスポートするには、**Export-PfxCertificate** コマンドレットを使用します。

**Export-PfxCertificate** コマンドレットを使用する場合は、パスワードを作成して使用するか、"-ProtectTo" パラメーターを使用して、パスワードなしでファイルにアクセスできるユーザーまたはグループを指定する必要があります。 "-Password" または "-ProtectTo" パラメーターのいずれかを使用しない場合、エラーが表示されます。

### <a name="password-usage"></a>Password を使用

```powershell
$pwd = ConvertTo-SecureString -String <Your Password> -Force -AsPlainText 
Export-PfxCertificate -cert "Cert:\LocalMachine\My\<Certificate Thumbprint>" -FilePath <FilePath>.pfx -Password $pwd
```

### <a name="protectto-usage"></a>ProtectTo を使用

```powershell
Export-PfxCertificate -cert Cert:\LocalMachine\My\<Certificate Thumbprint> -FilePath <FilePath>.pfx -ProtectTo <Username or group name>
```

証明書を作成してエクスポートしたら、**SignTool** を使ってアプリ パッケージに署名する準備が整いました。 手動によるパッケージ化プロセスの次の手順については、「[SignTool を使ったアプリ パッケージの署名](https://docs.microsoft.com/windows/uwp/packaging/sign-app-package-using-signtool)」をご覧ください。

## <a name="security-considerations"></a>セキュリティの考慮事項

[ローカル コンピューターの証明書ストア](https://docs.microsoft.com/windows-hardware/drivers/install/local-machine-and-current-user-certificate-stores)に証明書を追加することによって、コンピューター上のすべてのユーザーの証明書の信頼に影響します。 システムの信頼性を損なうのを防ぐために、これらの証明書が不要になったときには、削除することをお勧めします。
