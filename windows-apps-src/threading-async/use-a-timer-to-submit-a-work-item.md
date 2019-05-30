---
ms.assetid: AAE467F9-B3C7-4366-99A2-8A880E5692BE
title: タイマーを使った作業項目の送信
description: タイマーが終了した後に実行される作業項目の作成方法を説明します。
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, タイマー, スレッド
ms.localizationpriority: medium
ms.openlocfilehash: 7a1a7a161734a142b5f1399798394494d05c6a49
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371509"
---
# <a name="use-a-timer-to-submit-a-work-item"></a>タイマーを使った作業項目の送信


<b>重要な API</b>

-   [**Windows.UI.Core 名前空間**](https://docs.microsoft.com/uwp/api/Windows.UI.Core)
-   [**Windows.System.Threading 名前空間**](https://docs.microsoft.com/uwp/api/Windows.System.Threading)

タイマーが終了した後に実行される作業項目の作成方法を説明します。

## <a name="create-a-single-shot-timer"></a>1 回限りのタイマーの作成

[  **CreateTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createtimer) メソッドを使って、作業項目に対応するタイマーを作成します。 作業を実行するラムダを指定し、*delay* パラメーターを使って、利用可能なスレッドに作業項目を割り当てることができるようになるまでスレッド プールが待機する時間を指定します。 delay パラメーターは [**TimeSpan**](https://docs.microsoft.com/uwp/api/Windows.Foundation.TimeSpan) 構造体を使って指定します。

> **注**  を使用することができます[ **CoreDispatcher.RunAsync** ](https://docs.microsoft.com/uwp/api/windows.ui.core.coredispatcher.windows)を UI にアクセスし、作業項目からの進行状況を表示します。

次の例では、3 分間実行される作業項目を作成します。

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> TimeSpan delay = TimeSpan.FromMinutes(3);
>             
> ThreadPoolTimer DelayTimer = ThreadPoolTimer.CreateTimer(
>     (source) =>
>     {
>         //
>         // TODO: Work
>         //
>         
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>             CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>             });
>
>     }, delay);
> ```
> ``` cpp
> TimeSpan delay;
> delay.Duration = 3 * 60 * 10000000; // 10,000,000 ticks per second
>
> ThreadPoolTimer ^ DelayTimer = ThreadPoolTimer::CreateTimer(
>         ref new TimerElapsedHandler([this](ThreadPoolTimer^ source)
>         {
>             //
>             // TODO: Work
>             //
>             
>             //
>             // Update the UI thread by using the UI core dispatcher.
>             //
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([this]()
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                     ExampleUIUpdateMethod("Timer completed.");
>
>                 }));
>
>         }), delay);
> ```

## <a name="provide-a-completion-handler"></a>完了ハンドラーの指定

必要であれば、[**TimerDestroyedHandler**](https://docs.microsoft.com/uwp/api/windows.system.threading.timerdestroyedhandler) を使って、作業項目の取り消しと完了を処理します。 追加のラムダを指定するには、[**CreateTimer**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.createtimer) オーバーロードを使います。 これは、タイマーが取り消されたとき、または作業項目が完了したときに実行されます。

次の例では、作業項目を送信するタイマーを作成し、作業項目が完了したとき、またはタイマーが取り消されたときにメソッドを呼び出します。

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> TimeSpan delay = TimeSpan.FromMinutes(3);
>             
> bool completed = false;
>
> ThreadPoolTimer DelayTimer = ThreadPoolTimer.CreateTimer(
>     (source) =>
>     {
>         //
>         // TODO: Work
>         //
>
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>                 CoreDispatcherPriority.High,
>                 () =>
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                 });
>
>         completed = true;
>     },
>     delay,
>     (source) =>
>     {
>         //
>         // TODO: Handle work cancellation/completion.
>         //
>
>
>         //
>         // Update the UI thread by using the UI core dispatcher.
>         //
>         Dispatcher.RunAsync(
>             CoreDispatcherPriority.High,
>             () =>
>             {
>                 //
>                 // UI components can be accessed within this scope.
>                 //
>
>                 if (completed)
>                 {
>                     // Timer completed.
>                 }
>                 else
>                 {
>                     // Timer cancelled.
>                 }
>
>             });
>     });
> ```
> ``` cpp
> TimeSpan delay;
> delay.Duration = 3 * 60 * 10000000; // 10,000,000 ticks per second
>
> completed = false;
>
> ThreadPoolTimer ^ DelayTimer = ThreadPoolTimer::CreateTimer(
>         ref new TimerElapsedHandler([&](ThreadPoolTimer ^ source)
>         {
>             //
>             // TODO: Work
>             //
>
>             //
>             // Update the UI thread by using the UI core dispatcher.
>             //
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&]()
>                 {
>                     //
>                     // UI components can be accessed within this scope.
>                     //
>
>                 }));
>
>             completed = true;
>
>         }),
>         delay,
>         ref new TimerDestroyedHandler([&](ThreadPoolTimer ^ source)
>         {
>             //
>             // TODO: Handle work cancellation/completion.
>             //
>
>             Dispatcher->RunAsync(CoreDispatcherPriority::High,
>                 ref new DispatchedHandler([&]()
>                 {
>                     //
>                     // Update the UI thread by using the UI core dispatcher.
>                     //
>
>                     if (completed)
>                     {
>                         // Timer completed.
>                     }
>                     else
>                     {
>                         // Timer cancelled.
>                     }
>
>                 }));
>         }));
> ```

## <a name="cancel-the-timer"></a>タイマーの取り消し

タイマーがカウント ダウンを続けているが、作業項目はもう不要である場合は、[**Cancel**](https://docs.microsoft.com/uwp/api/windows.system.threading.threadpooltimer.cancel) を呼び出します。 タイマーが取り消され、作業項目がスレッド プールに送信されなくなります。

> [!div class="tabbedCodeSnippets"]
> ``` csharp
> DelayTimer.Cancel();
> ```
> ``` cpp
> DelayTimer->Cancel();
> ```

## <a name="remarks"></a>注釈

ユニバーサル Windows プラットフォーム (UWP) アプリでは UI スレッドをブロックできるため、**Thread.Sleep** を使うことができません。 代わりに、[**ThreadPoolTimer**](https://docs.microsoft.com/uwp/api/Windows.System.Threading.ThreadPoolTimer) を使って作業項目を作ります。これによって、UI スレッドをブロックすることなく、作業項目によって実行されたタスクを遅延します。

作業項目、タイマー作業項目、定期的な作業項目の使い方を示すコード サンプル全体については、[スレッド プールのサンプルに関するページ](https://go.microsoft.com/fwlink/p/?linkid=255387)をご覧ください。 コード サンプルは、Windows 8.1 用に記述された最初が、コードは、Windows 10 で再利用できます。

繰り返しタイマーについて詳しくは、「[定期的な作業項目の作成](create-a-periodic-work-item.md)」をご覧ください。

## <a name="related-topics"></a>関連トピック

* [スレッド プールへの作業項目の送信](submit-a-work-item-to-the-thread-pool.md)
* [スレッド プールを使うためのベスト プラクティス](best-practices-for-using-the-thread-pool.md)
* [タイマーを使った作業項目の送信](use-a-timer-to-submit-a-work-item.md)
 

 
