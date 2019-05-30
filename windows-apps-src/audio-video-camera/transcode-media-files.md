---
ms.assetid: A1A0D99A-DCBF-4A14-80B9-7106BEF045EC
description: Windows.Media.Transcoding API を使って、ビデオ ファイルをある形式から別の形式にコード変換できます。
title: メディア ファイルのコード変換
ms.date: 02/08/2017
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 17eaa79a65bb19156efd230a3442cfde059e5503
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66360599"
---
# <a name="transcode-media-files"></a>メディア ファイルのコード変換



[  **Windows.Media.Transcoding**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding) API を使って、ビデオ ファイルをある形式から別の形式にコード変換できます。

*コード変換*とは、デジタル メディア ファイル (ビデオ ファイルやオーディオ ファイル) の形式を別の形式に変換することです。 通常は、ファイルをデコードしてエンコードし直すことで行います。 たとえば、MP4 形式をサポートするポータブル デバイスで再生できるように、Windows Media ファイルを MP4 に変換できます。 また、高解像度のビデオ ファイルの解像度を下げることもできます。 この場合、再エンコードしたファイルは元のファイルと同じコーデックを使うことがありますが、エンコード プロファイルは異なります。

## <a name="set-up-your-project-for-transcoding"></a>プロジェクトをコード変換用にセットアップする

この記事のコードを使ってメディア ファイルのコード変換を行うには、既定のプロジェクト テンプレートによって参照される名前空間に加えて、これらの名前空間を参照する必要があります。

[!code-cs[Using](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetUsing)]

## <a name="select-source-and-destination-files"></a>変換元ファイルと変換先ファイルを選択する

アプリでコード変換の変換元ファイルと変換先ファイルを判別する方法は、実装によって異なります。 この例では、[**FileOpenPicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileOpenPicker) と [**FileSavePicker**](https://docs.microsoft.com/uwp/api/Windows.Storage.Pickers.FileSavePicker) を使って、ユーザーが変換元ファイルと変換先ファイルを選べるようにします。

[!code-cs[TranscodeGetFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeGetFile)]

## <a name="create-a-media-encoding-profile"></a>メディア エンコード プロファイルを作成する

エンコード プロファイルには、変換先ファイルのエンコード方法を決めるための設定が含まれています。 ファイルをコード変換するときのオプションが最も多いのは、このエンコード プロファイルです。

[  **MediaEncodingProfile**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.MediaEncodingProfile) クラスには、あらかじめ定義されたエンコード プロファイルを作るための静的メソッドが用意されています。

### <a name="methods-for-creating-audio-only-encoding-profiles"></a>オーディオ専用のエンコード プロファイルを作成するメソッド

メソッド  |Profile  |
---------|---------|
[**CreateAlac**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createalac)     |Apple Lossless Audio Codec (ALAC) オーディオ         |
[**CreateFlac**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createflac)     |Free Lossless Audio Codec (FLAC) オーディオ         |
[**CreateM4a**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createm4a)     |AAC オーディオ (M4A)         |
[**CreateMp3**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp3)     |MP3 オーディオ         |
[**CreateWav**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwav)     |WAV オーディオ         |
[**CreateWmv**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwmv)     |Windows Media Audio (WMA)         |

### <a name="methods-for-creating-audio--video-encoding-profiles"></a>オーディオ/ビデオのエンコード プロファイルを作成するメソッド

メソッド  |Profile  |
---------|---------|
[**CreateAvi**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createavi) |AVI |
[**CreateHevc**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createhevc) |High Efficiency Video Coding (HEVC) ビデオ、H.265 ビデオとも呼ばれます |
[**CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4) |MP4 ビデオ (H.264 ビデオと AAC オーディオ) |
[**CreateWmv**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createwmv) |Windows Media Video (WMV) |


次のコードでは、MP4 ビデオ用のプロファイルが作成されます。

[!code-cs[TranscodeMediaProfile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeMediaProfile)]

静的 [**CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4) メソッドは、MP4 エンコード プロファイルを作ります。 このメソッドのパラメーターで、ビデオのターゲット解像度を指定します。 この場合の [**VideoEncodingQuality.hd720p**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.VideoEncodingQuality) は、1280 x 720 ピクセルで 1 秒あたり 30 フレームであることを意味します  ("720 p"の略フレームあたりの 720 プログレッシブ行です。)すべての定義済みプロファイルを作成するための他のメソッドでは、このパターンに従います。

別の方法として、[**MediaEncodingProfile.CreateFromFileAsync**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createfromfileasync) メソッドを使って現在のメディア ファイルに一致するプロファイルを作成することもできます。 または、必要なエンコード設定が正確にわかれば、新しい [**MediaEncodingProfile**](https://docs.microsoft.com/uwp/api/Windows.Media.MediaProperties.MediaEncodingProfile) オブジェクトを作成してプロファイルの詳細を入力できます。

## <a name="transcode-the-file"></a>ファイルのコード変換を行う

ファイルのコード変換を行うには、新しい [**MediaTranscoder**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding.MediaTranscoder) オブジェクトを作成し、[**MediaTranscoder.PrepareFileTranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.mediatranscoder.preparefiletranscodeasync) メソッドを呼び出します。 ソース ファイル、出力先ファイル、エンコード プロファイルを渡します。 次に、非同期コード変換操作から返された [**PrepareTranscodeResult**](https://docs.microsoft.com/uwp/api/Windows.Media.Transcoding.PrepareTranscodeResult) オブジェクト上の [**TranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.preparetranscoderesult.transcodeasync) メソッドを呼び出します。

[!code-cs[TranscodeTranscodeFile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeTranscodeFile)]

## <a name="respond-to-transcoding-progress"></a>コード変換の進行状況に対して応答する

非同期の [**TranscodeAsync**](https://docs.microsoft.com/uwp/api/windows.media.transcoding.preparetranscoderesult.transcodeasync) の進行状況が変化したときに応答するイベントを登録できます。 これらのイベントは、ユニバーサル Windows プラットフォーム (UWP) アプリの非同期プログラミング フレームワークの一部であり、コード変換 API に固有のものではありません。

[!code-cs[TranscodeCallbacks](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetTranscodeCallbacks)]


## <a name="encode-a-metadata-stream"></a>メタデータ ストリームをエンコードする
Windows 10、バージョン 1803、以降の時間指定メタデータを含めることができますとメディア ファイルをトランス コードします。 ビデオのトランス コードとは異なり上記の例、組み込みのメディアのエンコードを使用するプロファイルの作成方法、ような[ **MediaEncodingProfile.CreateMp4**](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.mediaencodingprofile.createmp4)メタデータのエンコードを手動で作成する必要がありますプロファイルがメタデータにエンコードの種類をサポートします。

メタデータ incoding プロファイルを作成するこの最初の手順が作成するには、 **[TimedMetadataEncodingProperties]** トランス コードするメタデータのエンコードを記述するオブジェクト。 サブタイプ プロパティは、メタデータの種類を指定する GUID です。 メタデータの種類ごとのエンコードの詳細については、専用 Windows によって提供されていません。 この例では、GoPro メタデータ (gprs) の GUID が使用されます。 次に、 [ **SetFormatUserData** ](https://docs.microsoft.com/uwp/api/windows.media.mediaproperties.timedmetadataencodingproperties.setformatuserdata)バイナリの blob のメタデータの形式に固有のストリームの形式を記述するデータを設定します。 次に、 **TimedMetadataStreamDescriptor**(https://docs.microsoft.com/uwp/api/windows.media.core.timedmetadatastreamdescriptor)エンコードの対象のプロパティから作成され追跡ラベルと名前がメタデータのストリームを識別するために、エンコード形式のストリームを読み取るアプリケーションを許可して、必要に応じてUI には、ストリーム名を表示します。 
 
[!code-cs[GetStreamDescriptor](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetGetStreamDescriptor)]

作成した後、 **TimedMetadataStreamDescriptor**、作成することができます、 **MediaEncodingProfile**ビデオ、オーディオ、およびファイルにエンコードするメタデータを記述します。 **TimedMetadataStreamDescriptor**作成された最後の例はこの例のヘルパー関数に渡され、に追加されます、 **MediaEncodingProfile**呼び出して[ **SetTimedMetadataTracks**](https://docs.microsoft.com/en-us/uwp/api/windows.media.mediaproperties.mediaencodingprofile.settimedmetadatatracks)します。

[!code-cs[GetMediaEncodingProfile](./code/TranscodeWin10/cs/MainPage.xaml.cs#SnippetGetMediaEncodingProfile)]
 

 




