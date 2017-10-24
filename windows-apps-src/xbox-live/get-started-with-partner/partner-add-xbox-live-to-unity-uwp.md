---
title: "UWP 用 Unity と .NET スクリプト"
author: KevinAsgari
description: "ID@Xbox および対象パートナー向けに、.NET スクリプト バックエンドを使用して、Xbox Live サポートを UWP 用 Unity に追加する"
ms.assetid: 790a49ad-eff4-4916-8578-968ca8483211
ms.author: kevinasg
ms.date: 04-04-2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: "Xbox Live, Xbox, ゲーム, UWP, Windows 10, Xbox One, Unity"
ms.openlocfilehash: b71ff7da5e8d4963f860690d34eae0d5a4b602b1
ms.sourcegitcommit: 90fbdc0e25e0dff40c571d6687143dd7e16ab8a8
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 07/06/2017
---
# <a name="add-xbox-live-support-to-unity-for-uwp-with-net-scripting-backend-for-idxbox-and-managed-partners"></a>ID@Xbox および対象パートナー向けに、.NET スクリプト バックエンドを使用して、Xbox Live サポートを UWP 用 Unity に追加する

**1) Unity をインストールします。**

Unity 5.3 以上をインストールします。Unity のインストール プロセスで、[Windows Store .NET Scripting backend] コンポーネントをオンにします。

![](../images/unity/unity1-install.png)

**2) 新規または既存の Unity プロジェクトを開きます。**

このプロジェクトは、3D プロジェクトまたは 2D プロジェクトのいずれかですが、 どちらの種類のプロジェクトも Xbox Live SDK で動作します。

**3) Microsoft.Xbox.Services.winmd ファイルを Unity Assets フォルダーにドラッグ アンド ドロップします。**

![](../images/unity/unity2-winmd.png)

そのためには、次の手順を実行します。

1.  Xbox Live WinMD を [http://aka.ms/xboxlivesdkunity](http://aka.ms/xboxlivesdkunity) からダウンロードし、ハード ディスク上の適当な場所に展開します。

2.  Microsoft.Xbox.Services.winmd を、開いている Unity プロジェクトのアセット パネルにドラッグ アンド ドロップします。

Xbox Live 呼び出しを含むスクリプトを Unity エディター内から実行することはできませんが、この手順を行うことにより、Xbox Live 呼び出しを含むスクリプトを Unity エディター内からコンパイルできるようになります。

**4) 新しい C\# スクリプトを Unity オブジェクトに追加およびアタッチします。**

たとえば、"Main Camera" などの Unity オブジェクトをクリックし、[Add Component]、[New Script]、[C\# Script] の順にクリックして、"XboxLiveScript" という名前を付けます。 ゲーム オブジェクトの種類は問いません。

**5) Unity でプロジェクトを ビルドします。**

1.  [File]、[Build Settings] の順に移動し、[Windows Store] をクリックして、[Switch Platform] をクリックします。

2.  [Add Open Scenes] をクリックして、現在のシーンをビルドに追加します。

3.  [SDK] コンボ ボックスで、[Universal 10] を選択します。

4.  UWP ビルド タイプのコンボ ボックスで [D3D] を選択しますが、必要に応じて [XAML] も選択できます。

5.  [Unity C\# Projects] チェック ボックスをオンにして、Assembly-Csharp.dll プロジェクトを生成します。

6.  Unity の [Build] をクリックして、XDK アプリケーション内に Unity ゲームをラップする UWP Visual Studio プロジェクトを生成します。 新しいファイルが多数作成されるため、場所を指定するときは、混乱を避けるために新しいフォルダーを作成します。 フォルダーの名前を "Build" にすることをお勧めします。フォルダーに名前を付けたら、そのフォルダーを選択します。

![](../images/unity/unity3-buildsettings.png)

  

**6) 生成した UWP プロジェクトを Visual Studio で開きます。**

Unity では、エクスプローラーで出力プロジェクト フォルダーが開きます。  フォルダー内の .sln ファイルは無視してください。  代わりに、Build フォルダーに移動し、生成された .sln を Visual Studio で開きます。  

このソリューションには 3 つのプロジェクトがあります。

1.  Assembly-CSharp。 Xbox Live スクリプトが配置される場所です。

2.  Assembly-Csharp-firstpass。 今回、このプロジェクトは無視してかまいません。

3.  プロジェクトの名前に基づいた UWP アプリ。 これは、Unity エンジンをホストする従来の UWP アプリです。 ここでは、従来の UWP アプリに似たいくつかの Xbox Live 構成をセットアップします。

**7) "Microsoft.Xbox.Live.SDK.WinRT.UWP" NuGet パッケージ参照を追加します。**

「[Xbox Live API のバイナリ パッケージを UWP プロジェクトに追加する](add-xbox-live-apis-binary-to-a-uwp-project.md)」の手順に従います

"Microsoft.Xbox.Live.SDK.WinRT.UWP" NuGet パッケージ参照を UWP アプリ プロジェクトに追加します。

**8) Xbox Live 構成を UWP アプリに追加します。**

「[新規または既存の UWP プロジェクトに Xbox Live を追加する](get-started-with-visual-studio-and-uwp.md)」というドキュメント ページの手順に従います。

**9) Xbox Live コードをスクリプトに追加します。**

以下のサンプルの Xbox Live コードをコピーし、ゲーム オブジェクトにアタッチしたスクリプトに貼り付けます。 このスクリプトは "Assembly-CSharp" プロジェクトに表示されます。 コードは自由に変更できます。

```cpp
#if NETFX_CORE

using UnityEngine;
using System;
using Microsoft.Xbox.Services.System;

public class XboxLiveScript : MonoBehaviour
{
    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new Microsoft.Xbox.Services.System.XboxLiveUser();
    Microsoft.Xbox.Services.XboxLiveContext m_xboxLiveContext = null;
    Windows.UI.Core.CoreDispatcher UIDispatcher = null;
    string debugText = "";

    void Start()
    {
        Windows.ApplicationModel.Core.CoreApplicationView mainView = Windows.ApplicationModel.Core.CoreApplication.MainView;
        Windows.UI.Core.CoreWindow cw = mainView.CoreWindow;

        UIDispatcher = cw.Dispatcher;
        SignIn();
    }

    void Update()
    {
    }

    void OnGUI()
    {
        GUI.Label(new UnityEngine.Rect(10, 10, 300, 50), debugText);
    }

    async void SignIn()
    {
        SignInResult result = await m_user.SignInAsync(UIDispatcher);

        if (result.Status == SignInStatus.Success)
        {
            m_xboxLiveContext = new Microsoft.Xbox.Services.XboxLiveContext(m_user);
            debugText += "\n User signed in: " + m_xboxLiveContext.User.Gamertag;
        }
    }
}

#endif
```

**10) Visual Studio から UWP アプリをコンパイルして実行します。**

これにより、通常の UWP アプリのようにアプリが起動し、動作のために UWP アプリ コンテナーが必要なときに Xbox Live 呼び出しが可能になります。

**11) Unity で変更を加えた場合はリビルドします。**
  
Unity で変更を加えた場合、UWP プロジェクトをリビルドする必要があります。

再コンパイル時に Unity が pfx ファイルを置き換えることによって Xbox Live へのサインインが失敗することに注意してください。この問題を避けるために Unity プロジェクト内でファイルを更新する必要があります。

これを行うには、[File]、[Build Settings] の順に移動し、Windows Store プレイヤー上で [Build Settings] をクリックしてから、[PFX] ボタンをクリックして、PFX ファイルを前の手順で取得したファイルに置き換えます。 別の方法として、Unity 内でプロジェクトをリビルドするたびに PFX ファイルを削除することもできます。

## <a name="troubleshooting-common-issues"></a>一般的な問題のトラブルシューティング

**1)** Unity において、関連付けられたスクリプトを読み込めない場合、手順 3 を実行して WinMD を Unity のプロジェクト アセット パネルにドラッグしたことを確認してください。

**2)** 起動後すぐ、または次のコード行を実行しようとしたときにアプリがクラッシュする場合:

    Microsoft.Xbox.Services.System.XboxLiveUser m_user = new Microsoft.Xbox.Services.System.XboxLiveUser();

xboxservices.config テキスト ファイルをプロジェクトに追加して、そのプロパティで "Build Action" を "Content" に、"Copy to Output Directory" を "Copy Always" に設定したことを確認します。
また、次のような正しい JSON フォーマット (10 進形式の TitleId) がテキスト ファイルに含まれていることを確認します。

```json
{
    "TitleId" : 928643728,
    "PrimaryServiceConfigId" : "3ebd0100-ace5-4aa4-ab9c-5b733759fa90"
}
```

**3)** アプリが起動するがサインインに失敗する場合は、以下を確認してください。

a) コンピューターがデベロッパー サンドボックスに設定されていること。  これを行うには、Xbox Live SDK の \Tools フォルダーにある SwitchSandbox.cmd スクリプトを使用します。

b) デベロッパー サンドボックスにアクセスできる Xbox Live アカウントでサインインしていること。  通常のリテール Xbox Live アカウントにはそのようなアクセス権がありません。  XDP またはデベロッパー センターを使用してテスト アカウントを作成できます。

c) UWP アプリの package.appxmanfiest で正しい Identity が設定されていること。  これは手動で編集できますが、Visual Studio でプロジェクトを右クリックし、[ストア] の [アプリケーションをストアと関連付ける] を選択するのが最も簡単な修正方法です。

d) Unity によって提供されるストック .pfx ファイルは Identity が正しくないため、そのファイルをディスクから削除し、そのファイルを参照する行を .csproj から削除します。または、Visual Studio でプロジェクトを右クリックし、[ストア] の [アプリケーションをストアと関連付ける] を選択すれば、適切な .pfx ファイルが配置されます。  その後、Unity に戻って Windows Store プレイヤー上で [Build Settings] をクリックし、[PFX] ボタンをクリックして、.pfx ファイルを Visual Studio での [アプリケーションをストアと関連付ける] 操作により取得したファイルに置き換えます。