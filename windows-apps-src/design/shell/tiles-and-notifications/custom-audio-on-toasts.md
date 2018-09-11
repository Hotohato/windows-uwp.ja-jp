---
author: andrewleader
Description: Learn how to use custom audio on your toast notifications.
title: トーストでのカスタム オーディオの使用
label: Custom audio on toasts
template: detail.hbs
ms.author: mijacobs
ms.date: 12/15/2017
ms.topic: article
ms.prod: windows
ms.technology: uwp
keywords: windows 10, uwp, トースト, カスタム オーディオ, 通知, オーディオ, サウンド
ms.localizationpriority: medium
ms.openlocfilehash: 24be148340366163fcab00ec75f7f26fac6c2d80
ms.sourcegitcommit: 72710baeee8c898b5ab77ceb66d884eaa9db4cb8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/11/2018
ms.locfileid: "3845646"
---
# <a name="custom-audio-on-toasts"></a>トーストでのカスタム オーディオの使用

トースト通知でカスタム オーディオを使用して、アプリでブランド独自のサウンド エフェクトを表現できます。 たとえば、メッセージング アプリで、トースト通知に汎用の通知サウンドではなく、独自のメッセージング サウンドを使用すると、ユーザーは特定のアプリから通知を受け取ったことが即座にわかります。

## <a name="install-uwp-community-toolkit-nuget-package"></a>UWP コミュニティ ツールキットの NuGet パッケージをインストールする

コードを利用して通知を作成するには、通知 XML コンテンツのオブジェクト モデルを提供する UWP コミュニティ ツールキットの Notifications ライブラリの使用を強くお勧めします。 手動で通知用 XML を構築することもできますが、ミスが発生しやすく煩雑です。 UWP コミュニティ ツールキットに用意された Notifications ライブラリは、Microsoft の通知を担当しているチームが構築して管理しています。

NuGet から [Microsoft.Toolkit.Uwp.Notifications](https://www.nuget.org/packages/Microsoft.Toolkit.Uwp.Notifications/) をインストールします (このページのサンプルでは、バージョン 1.0.0 を使用しています)。


## <a name="add-namespace-declarations"></a>名前空間宣言を追加する

`Windows.UI.Notifications`  には、タイルとトーストの API が含まれています。 `Microsoft.Toolkit.Uwp.Notifications`  には Notifications ライブラリが含まれています。

```csharp
using Microsoft.Toolkit.Uwp.Notifications;
using Windows.UI.Notifications;
```


## <a name="construct-the-notification"></a>通知を作成する

トースト通知のコンテンツには、テキストや画像のほか、ボタンや入力も含まれています。 完全なコード スニペットについては、[ローカル トースト通知の送信](send-local-toast.md) をご覧ください。

```csharp
ToastContent toastContent = new ToastContent()
{
    Visual = new ToastVisual()
    {
        ... (omitted)
    }
};
```


## <a name="add-the-custom-audio"></a>カスタム オーディオを追加する

Windows Mobile は、常にトースト通知でカスタム オーディオがサポートされてきました。 しかし、デスクトップ版では、バージョン 1511 (ビルド 10586) で初めてカスタム オーディオのサポートが追加されました。 バージョン 1511 より前のデスクトップ デバイスに、カスタム オーディオを含んだトーストを送信すると、トーストは無音となります。 そのため、バージョン 1511 より前のバージョンのデスクトップでは、トースト通知にカスタム オーディオを含めないでください。これにより、通知の際に、少なくとも既定の通知サウンドが使用されます。

**既知の問題**: デスクトップ バージョン 1511 を使用している場合、トーストのカスタム オーディオは、アプリが Microsoft Store 経由でインストールされた場合にのみ機能します。 そのため、Microsoft Store への提出前に、ローカル デスクトップでカスタムのオーディオをテストすることはできませんが、Microsoft Store からインストールしたときには、オーディオが正常に機能します。 Anniversary update ではこの問題が修正され、ローカルに展開されたアプリからでもカスタム オーディオが正常に動作します。

```csharp
?
bool supportsCustomAudio = true;
 
// If we're running on Desktop before Version 1511, do NOT include custom audio
// since it was not supported until Version 1511, and would result in a silent toast.
if (AnalyticsInfo.VersionInfo.DeviceFamily.Equals("Windows.Desktop")
    && !ApiInformation.IsApiContractPresent("Windows.Foundation.UniversalApiContract", 2))
{
    supportsCustomAudio = false;
}
 
if (supportsCustomAudio)
{
    toastContent.Audio = new ToastAudio()
    {
        Src = new Uri("ms-appx:///Assets/Audio/CustomToastAudio.m4a")
    };
}
```

サポートされているオーディオ ファイルの種類は次のとおりです。

- .aac
- .flac
- .m4a
- .mp3
- .wav
- .wma


## <a name="send-the-notification"></a>通知を送信する

トースト コンテンツが完了した後は、簡単に通知を送信できます。

```csharp
// Create the toast notification from the previous toast content
ToastNotification notification = new ToastNotification(toastContent.GetXml());
             
// And then send the toast
ToastNotificationManager.CreateToastNotifier().Show(notification);
```


## <a name="related-topics"></a>関連トピック

- [GitHub での完全なコード サンプル](https://github.com/WindowsNotifications/quickstart-toast-with-custom-audio)
- [ローカル トーストの送信](send-local-toast.md)
- [トースト コンテンツのドキュメント](adaptive-interactive-toasts.md)