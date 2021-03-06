---
title: Windows 10 の音声、音声、および会話
description: このページには、音声認識対応の Windows アプリの開発を開始するための情報が記載されています。
ms.topic: article
ms.date: 09/12/2019
keywords: Windows 10、音声、音声、会話、win32 speech アプリ、UWP speech アプリ、WPF speech apps、WinForms speech apps の音声
ms.author: kbridge
author: Karl-Bridge-Microsoft
ms.openlocfilehash: 7ac8d782591ce8f3716e491714c4cbf241e80b6c
ms.sourcegitcommit: 8a88a05ad89aa180d41a93152632413694f14ef8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 01/24/2020
ms.locfileid: "76726552"
---
# <a name="speech-voice-and-conversation-in-windows-10"></a>Windows 10 の音声、音声、および会話

![音声ヒーローの画像](images/hero-speech-composite-small.png)

音声は、ユーザーが Windows アプリケーションと対話したり、マウス、キーボード、タッチ、コントローラー、またはジェスチャに基づいて従来の対話機能を補完したり、置き換えたりできるようにするための、効果的で自然かつ楽しい方法です。

音声認識、ディクテーション、音声合成 (音声合成または TTS とも呼ばれます)、会話音声アシスタント (Cortana や Alexa など) などの音声ベースの機能は、ユーザーが使用できるようにするためのユーザーエクスペリエンスを提供します。他の入力デバイスが十分でない場合もあります。

このページでは、windows アプリケーションを構築する開発者向けに、さまざまな Windows 開発フレームワークが音声認識、音声合成、およびメッセージ交換のサポートを提供する方法について説明します。

## <a name="platform-specific-documentation"></a>プラットフォームに特化したドキュメント

:::row:::
   :::column:::
      ![ユニバーサル Windows プラットフォーム (UWP)](images/platform-uwp.png)

      **ユニバーサル Windows プラットフォーム (UWP)**

      Windows 10 アプリケーションやゲーム向けの最新プラットフォームで、windows デバイス (Pc、携帯電話、Xbox One、HoloLens などを含む) 上で音声対応のアプリを作成し、それらを Microsoft Store に公開します。

      [音声操作](https://docs.microsoft.com/windows/uwp/design/input/speech-interactions)

      [音声認識](https://docs.microsoft.com/windows/uwp/design/input/speech-recognition)

      [継続的なディクテーション](https://docs.microsoft.com/windows/uwp/design/input/enable-continuous-dictation)

      [音声合成](https://docs.microsoft.com/uwp/api/windows.media.speechsynthesis)

      [会話担当者](https://docs.microsoft.com/uwp/api/windows.applicationmodel.conversationalagent)

      [Cortana 音声コマンド](https://docs.microsoft.com/cortana/voice-commands/vcd)
   :::column-end:::
   :::column:::
      ![Win32 プラットフォーム アプリ](images/platform-win32.png)

      **Win32 プラットフォーム**

      ここで提供されているツール、情報、およびサンプルエンジンとアプリケーションを使用して、Windows デスクトップおよび Windows Server 用の音声対応アプリケーションを開発します。

      [Microsoft Speech Platform - ソフトウェア開発キット (SDK) (バージョン 11)](https://www.microsoft.com/download/details.aspx?id=27226)
      
      [Microsoft Speech SDK バージョン 5.1](https://www.microsoft.com/download/details.aspx?id=10121)
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      ![.NET](images/platform-dotnet.png)

      **.NET Framework**

      Windows マネージド アプリケーション用として実績のあるプラットフォームで、XAML UI モデルと .NET Framework を使用して、アクセシビリティに対応したアプリやツールを開発します。

      [.NET Framework 用 System.Speech プログラミング ガイド](https://docs.microsoft.com/previous-versions/office/developer/speech-technologies/hh361625(v=office.14))
   :::column-end:::
   :::column:::
      ![Azure speech services](images/platform-azure-speech.png)

      **Azure speech services**

      Azure speech services を使用して、アクセス可能な web サイトを設計、構築、テストします。

      [音声をテキストに変換](https://azure.microsoft.com/services/cognitive-services/speech-to-text/)

      [テキスト読み上げ](https://azure.microsoft.com/services/cognitive-services/text-to-speech/)
      
      [音声翻訳](https://azure.microsoft.com/services/cognitive-services/speech-translation/)

      [音声優先の仮想アシスタント](https://docs.microsoft.com/azure/cognitive-services/speech-service/voice-first-virtual-assistants)
   :::column-end:::
:::row-end:::
:::row:::
   :::column span="2":::
      **従来の機能**

      Microsoft Speech テクノロジのレガシ、非推奨、またはサポートされていないバージョン。
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [Microsoft エージェント](https://docs.microsoft.com/windows/win32/lwef/microsoft-agent)

      [Microsoft Speech アプリケーションソフトウェア開発キット (SASDK) バージョン1.0](https://www.microsoft.com/download/details.aspx?id=2200)
   :::column-end:::
   :::column:::
      [Microsoft Speech API (SAPI) 5.3](https://docs.microsoft.com/previous-versions/windows/desktop/ms723627(v=vs.85))

      [Microsoft Speech API (SAPI) 5.4](https://docs.microsoft.com/previous-versions/windows/desktop/ee125663(v=vs.85))

      [Bing Speech 認識コントロール](https://docs.microsoft.com/previous-versions/bing/speech/dn434583(v%3dmsdn.10))
   :::column-end:::
:::row-end:::

## <a name="samples"></a>Samples

アクセシビリティのためのさまざまな特徴や機能の例を示す、完全な Windows サンプルをダウンロードして実行します。

:::row:::
   :::column:::
      [コード サンプル ブラウザー](https://docs.microsoft.com/samples/browse/?term=speech)

      新しいサンプルブラウザー (MSDN コードギャラリーを置き換えます)。
   :::column-end:::
   :::column:::
      [GitHub 上の Windows クラシック サンプル](https://github.com/microsoft/Windows-classic-samples/search?q=speech&unscoped_q=speech) (英語)

      これらのサンプルは、Windows および Windows Server の機能とプログラミング モデルの例を示しています。 
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [GitHub 上のユニバーサル Windows プラットフォーム (UWP) サンプル](https://github.com/microsoft/Windows-universal-samples/search?q=speech&unscoped_q=speech) (英語)

      これらのサンプルでは、Windows 10 用の Windows ソフトウェア開発キット (SDK) における、ユニバーサル Windows プラットフォーム (UWP) のための API 使用パターンの例を示しています。
   :::column-end:::
   :::column:::
      [XAML コントロール ギャラリー](https://github.com/microsoft/Xaml-Controls-Gallery)

      このアプリは、Fluent Design System でサポートされているさまざまな Xaml コントロールの例を示しています。
   :::column-end:::
:::row-end:::

## <a name="videos"></a>Videos

音声操作を組み込む Windows アプリケーションを構築する方法を説明するさまざまなビデオです。

:::row:::
   :::column:::
      **Cortana と音声プラットフォームの詳細**
   :::column-end:::
   :::column:::
      **ユニバーサル Windows アプリにおける Cortana の拡張性**
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2015/3-716/player]
   :::column-end:::
   :::column:::
      > [!VIDEO https://channel9.msdn.com/Events/Build/2015/2-691/player]
   :::column-end:::
:::row-end:::

## <a name="other-resources"></a>その他のリソース

:::row:::
   :::column:::
      **ブログとニュース**

      Microsoft speech の世界の最新バージョン。
   :::column-end:::
   :::column:::
      **コミュニティとサポート**

      Windows の開発者とユーザーが一緒に対応し、学習します。
   :::column-end:::
:::row-end:::
:::row:::
   :::column:::
      [関連ニュース](https://news.microsoft.com/?s=speech)

      [音声ブログ](https://blogs.windows.com/windowsdeveloper/?s=speech)
   :::column-end:::
   :::column:::
      [Windows コミュニティ-音声](https://community.windows.com/search?q=speech)

      [Windows 音声開発者フォーラム](https://social.msdn.microsoft.com/Forums/home?filter=alltypes&sort=firstpostdesc&searchTerm=speech)
   :::column-end:::
:::row-end:::
