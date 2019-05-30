---
title: バックグラウンド タスクの進捗状況と完了の監視
description: バックグラウンド タスクから報告される進行状況と完了をアプリから認識する方法について説明します。
ms.assetid: 17544FD7-A336-4254-97DC-2BF8994FF9B2
ms.date: 07/06/2018
ms.topic: article
keywords: windows 10、uwp、バック グラウンド タスク
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
- cpp
ms.openlocfilehash: d8e4a84b0e927be8e1b89e6189e80acd2d3e4266
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66371317"
---
# <a name="monitor-background-task-progress-and-completion"></a>バックグラウンド タスクの進捗状況と完了の監視

**重要な API**

- [**BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration)
- [**BackgroundTaskProgressEventHandler**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskprogresseventhandler)
- [**BackgroundTaskCompletedEventHandler**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskcompletedeventhandler)

アウトプロセスで実行されるバックグラウンド タスクから報告される進行状況と完了をアプリから認識する方法について説明します  (インプロセス バックグラウンド タスクの場合、共有変数を設定して進行状況と完了を表すことができます)。

バック グラウンド タスクの進捗状況と完了は、アプリ コードによって監視することができます。 これを行うために、アプリでは、システムに登録されたバックグラウンド タスクのイベントを受信登録します。

- このトピックでは、アプリで既にバックグラウンド タスクが登録されていることを前提とします。 バックグラウンド タスクの作成方法の概要については、「[インプロセス バックグラウンド タスクの作成と登録](create-and-register-an-inproc-background-task.md)」または「[アウト プロセス バックグラウンド タスクの作成と登録](create-and-register-a-background-task.md)」をご覧ください。 条件とトリガーについて詳しくは、「[バックグラウンド タスクによるアプリのサポート](support-your-app-with-background-tasks.md)」をご覧ください。

## <a name="create-an-event-handler-to-handle-completed-background-tasks"></a>完了したバックグラウンド タスクを処理するイベント ハンドラーの作成

### <a name="step-1"></a>手順 1
完了したバックグラウンド タスクを処理するイベント ハンドラー関数を作ります。 このコードは、特定フット プリントは、する必要があります、 [ **IBackgroundTaskRegistration** ](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTaskRegistration)オブジェクトと[ **BackgroundTaskCompletedEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskCompletedEventArgs)オブジェクト。

次のフット プリントを使用して、 **OnCompleted**イベント ハンドラー メソッドのタスクをバック グラウンドします。

```csharp
private void OnCompleted(IBackgroundTaskRegistration task, BackgroundTaskCompletedEventArgs args)
{
    // TODO: Add code that deals with background task completion.
}
```

```cppwinrt
auto completed{ [this](
        Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
        Windows::ApplicationModel::Background::BackgroundTaskCompletedEventArgs const& /* args */)
{
    // TODO: Add code that deals with background task completion.
} };
```

```cpp
auto completed = [this](BackgroundTaskRegistration^ task, BackgroundTaskCompletedEventArgs^ args)
{
    // TODO: Add code that deals with background task completion.
};
```

### <a name="step-2"></a>手順 2
バックグラウンド タスクの完了を処理するイベント ハンドラーにコードを追加します。

たとえば、[バックグラウンド タスクのサンプル](https://go.microsoft.com/fwlink/p/?LinkId=618666)は UI を更新します。

```csharp
private void OnCompleted(IBackgroundTaskRegistration task, BackgroundTaskCompletedEventArgs args)
{
    UpdateUI();
}
```

```cppwinrt
auto completed{ [this](
        Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
        Windows::ApplicationModel::Background::BackgroundTaskCompletedEventArgs const& /* args */)
{
    UpdateUI();
} };
```

```cpp
auto completed = [this](BackgroundTaskRegistration^ task, BackgroundTaskCompletedEventArgs^ args)
{    
    UpdateUI();
};
```

## <a name="create-an-event-handler-function-to-handle-background-task-progress"></a>バックグラウンド タスクの進行状況を処理するイベント ハンドラー関数の作成

### <a name="step-1"></a>手順 1
完了したバックグラウンド タスクを処理するイベント ハンドラー関数を作ります。 このコードは、[**IBackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.IBackgroundTaskRegistration) オブジェクトと [**BackgroundTaskProgressEventArgs**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskProgressEventArgs) オブジェクトを受け入れる特定のフットプリントに従っている必要があります。

OnProgress バックグラウンド タスク イベント ハンドラー メソッドには次のフットプリントを使います。

```csharp
private void OnProgress(IBackgroundTaskRegistration task, BackgroundTaskProgressEventArgs args)
{
    // TODO: Add code that deals with background task progress.
}
```

```cppwinrt
auto progress{ [this](
    Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
    Windows::ApplicationModel::Background::BackgroundTaskProgressEventArgs const& /* args */)
{
    // TODO: Add code that deals with background task progress.
} };
```

```cpp
auto progress = [this](BackgroundTaskRegistration^ task, BackgroundTaskProgressEventArgs^ args)
{
    // TODO: Add code that deals with background task progress.
};
```

### <a name="step-2"></a>手順 2
バックグラウンド タスクの完了を処理するイベント ハンドラーにコードを追加します。

たとえば、[バックグラウンド タスクのサンプル](https://go.microsoft.com/fwlink/p/?LinkId=618666)では、*args* パラメーターで渡された進行状態により、UI が更新されます。

```csharp
private void OnProgress(IBackgroundTaskRegistration task, BackgroundTaskProgressEventArgs args)
{
    var progress = "Progress: " + args.Progress + "%";
    BackgroundTaskSample.SampleBackgroundTaskProgress = progress;
    UpdateUI();
}
```

```cppwinrt
auto progress{ [this](
    Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
    Windows::ApplicationModel::Background::BackgroundTaskProgressEventArgs const& args)
{
    winrt::hstring progress{ L"Progress: " + winrt::to_hstring(args.Progress()) + L"%" };
    BackgroundTaskSample::SampleBackgroundTaskProgress = progress;
    UpdateUI();
} };
```

```cpp
auto progress = [this](BackgroundTaskRegistration^ task, BackgroundTaskProgressEventArgs^ args)
{
    auto progress = "Progress: " + args->Progress + "%";
    BackgroundTaskSample::SampleBackgroundTaskProgress = progress;
    UpdateUI();
};
```

## <a name="register-the-event-handler-functions-with-new-and-existing-background-tasks"></a>新規および既存のバックグラウンド タスクと共にイベント ハンドラー関数を登録する

### <a name="step-1"></a>手順 1
アプリで初めてバックグラウンド タスクを登録するときは、フォアグラウンドでまだアプリがアクティブになっていてタスクか実行されている場合に、進行状況と完了の更新を受け取ることができるように登録する必要があります。

たとえば、[バックグラウンド タスクのサンプル](https://go.microsoft.com/fwlink/p/?LinkId=618666)では、登録するバックグラウンド タスクごとに次の関数を呼び出します。

```csharp
private void AttachProgressAndCompletedHandlers(IBackgroundTaskRegistration task)
{
    task.Progress += new BackgroundTaskProgressEventHandler(OnProgress);
    task.Completed += new BackgroundTaskCompletedEventHandler(OnCompleted);
}
```

```cppwinrt
void SampleBackgroundTask::AttachProgressAndCompletedHandlers(Windows::ApplicationModel::Background::IBackgroundTaskRegistration const& task)
{
    auto progress{ [this](
        Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
        Windows::ApplicationModel::Background::BackgroundTaskProgressEventArgs const& args)
    {
        winrt::hstring progress{ L"Progress: " + winrt::to_hstring(args.Progress()) + L"%" };
        BackgroundTaskSample::SampleBackgroundTaskProgress = progress;
        UpdateUI();
    } };

    task.Progress(progress);

    auto completed{ [this](
        Windows::ApplicationModel::Background::BackgroundTaskRegistration const& /* sender */,
        Windows::ApplicationModel::Background::BackgroundTaskCompletedEventArgs const& /* args */)
    {
        UpdateUI();
    } };

    task.Completed(completed);
}
```

```cpp
void SampleBackgroundTask::AttachProgressAndCompletedHandlers(IBackgroundTaskRegistration^ task)
{
    auto progress = [this](BackgroundTaskRegistration^ task, BackgroundTaskProgressEventArgs^ args)
    {
        auto progress = "Progress: " + args->Progress + "%";
        BackgroundTaskSample::SampleBackgroundTaskProgress = progress;
        UpdateUI();
    };

    task->Progress += ref new BackgroundTaskProgressEventHandler(progress);

    auto completed = [this](BackgroundTaskRegistration^ task, BackgroundTaskCompletedEventArgs^ args)
    {
        UpdateUI();
    };

    task->Completed += ref new BackgroundTaskCompletedEventHandler(completed);
}
```

### <a name="step-2"></a>手順 2
アプリが起動された時点、またはバックグラウンド タスクの状態が関連する新しいページに移動した時点で、現在登録されているバックグラウンド タスクの一覧を取得し、進行状況と完了に対応するイベント ハンドラー関数に関連付ける必要があります。 アプリケーションで現在登録されているバックグラウンド タスクの一覧は、[**BackgroundTaskRegistration**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Background.BackgroundTaskRegistration).[**AllTasks**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.background.backgroundtaskregistration.alltasks) プロパティで保持されます。

たとえば、[バックグラウンド タスクのサンプル](https://go.microsoft.com/fwlink/p/?LinkId=618666)では、次のコードを使って SampleBackgroundTask ページの移動時にイベント ハンドラーをアタッチします。

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    foreach (var task in BackgroundTaskRegistration.AllTasks)
    {
        if (task.Value.Name == BackgroundTaskSample.SampleBackgroundTaskName)
        {
            AttachProgressAndCompletedHandlers(task.Value);
            BackgroundTaskSample.UpdateBackgroundTaskStatus(BackgroundTaskSample.SampleBackgroundTaskName, true);
        }
    }

    UpdateUI();
}
```

```cppwinrt
void SampleBackgroundTask::OnNavigatedTo(Windows::UI::Xaml::Navigation::NavigationEventArgs const& /* e */)
{
    // A pointer back to the main page. This is needed if you want to call methods in MainPage such
    // as NotifyUser().
    m_rootPage = MainPage::Current;

    // Attach progress and completed handlers to any existing tasks.
    auto allTasks{ Windows::ApplicationModel::Background::BackgroundTaskRegistration::AllTasks() };

    for (auto const& task : allTasks)
    {
        if (task.Value().Name() == SampleBackgroundTaskName)
        {
            AttachProgressAndCompletedHandlers(task.Value());
            break;
        }
    }

    UpdateUI();
}
```

```cpp
void SampleBackgroundTask::OnNavigatedTo(NavigationEventArgs^ e)
{
    // A pointer back to the main page.  This is needed if you want to call methods in MainPage such
    // as NotifyUser().
    rootPage = MainPage::Current;

    // Attach progress and completed handlers to any existing tasks.
    auto iter = BackgroundTaskRegistration::AllTasks->First();
    auto hascur = iter->HasCurrent;
    while (hascur)
    {
        auto cur = iter->Current->Value;

        if (cur->Name == SampleBackgroundTaskName)
        {
            AttachProgressAndCompletedHandlers(cur);
            break;
        }

        hascur = iter->MoveNext();
    }

    UpdateUI();
}
```

## <a name="related-topics"></a>関連トピック

* [インプロセス バックグラウンド タスクの作成と登録](create-and-register-an-inproc-background-task.md)
* [アウトプロセス バックグラウンド タスクの作成と登録](create-and-register-a-background-task.md)
* [アプリケーション マニフェストでのバックグラウンド タスクの宣言](declare-background-tasks-in-the-application-manifest.md)
* [取り消されたバックグラウンド タスクの処理](handle-a-cancelled-background-task.md)
* [バックグラウンド タスクの登録](register-a-background-task.md)
* [バックグラウンド タスクによるシステム イベントへの応答](respond-to-system-events-with-background-tasks.md)
* [バックグラウンド タスクを実行するための条件の設定](set-conditions-for-running-a-background-task.md)
* [バックグラウンド タスクのライブ タイルの更新](update-a-live-tile-from-a-background-task.md)
* [メンテナンス トリガーの使用](use-a-maintenance-trigger.md)
* [タイマーでのバックグラウンド タスクの実行](run-a-background-task-on-a-timer-.md)
* [バックグラウンド タスクのガイドライン](guidelines-for-background-tasks.md)
* [バックグラウンド タスクのデバッグ](debug-a-background-task.md)
* [トリガーする方法を中断、再開、および (デバッグ) 場合は、UWP アプリでイベントをバック グラウンド](https://go.microsoft.com/fwlink/p/?linkid=254345)
