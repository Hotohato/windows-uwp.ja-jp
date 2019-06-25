---
ms.assetid: 41E1B4F1-6CAF-4128-A61A-4E400B149011
title: データ バインディングの詳細
description: データ バインディングは、アプリの UI でデータを表示し、必要に応じてそのデータとの同期を保つ方法です。
ms.date: 10/05/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
dev_langs:
- csharp
- cppwinrt
ms.openlocfilehash: f3cdb9cbb1aa3f62fb711be747c44a0df10fb1ee
ms.sourcegitcommit: f7e3782e24d46b2043023835c5b59d12d3b4ed4b
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/24/2019
ms.locfileid: "67345722"
---
# <a name="data-binding-in-depth"></a>データ バインディングの詳細

**重要な API**

-   [ **{X:bind} マークアップ拡張機能**](../xaml-platform/x-bind-markup-extension.md)
-   [**バインディング クラス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding)
-   [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext)
-   [**INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged)

> [!NOTE]
> このトピックでは、データ バインディングの機能について詳しく説明します。 簡潔で実用的な紹介については、「[データ バインディングの概要](data-binding-quickstart.md)」をご覧ください。

このトピックでは、ユニバーサル Windows プラットフォーム (UWP) アプリケーションでのデータ バインディングについて。 ここで説明した Api に存在、 [ **Windows.UI.Xaml.Data**名前空間](/uwp/api/windows.ui.xaml.data)します。

データ バインディングは、アプリの UI でデータを表示し、必要に応じてそのデータとの同期を保つ方法です。 データ バインディングによって、UI の問題からデータの問題を切り離すことができるため、概念的なモデルが簡素化されると共に、アプリの読みやすさ、テストの容易性、保守容易性が向上します。

データ バインディングは、UI が最初に表示されたときにデータ ソースからの値を表示するだけの場合に使うことができ、それらの値の変化に応答するために使うことはできません。 これは、モードというバインドの*1 回限り*、し、実行時に変更されない値に対して適切に機能します。 または、値を「確認」を変更するときに UI を更新するを選択できます。 このモードと呼ばれる*一方向*、および読み取り専用データに対して適切に機能します。 最終的には、ユーザーが UI で値に対して行った変更が自動的にデータ ソースにプッシュバックされるように、監視と更新の両方を行うことを選択できます。 このモードと呼ばれる*双方向*、および読み取り/書き込みデータに対して適切に機能します。 例をいくつか紹介します。

-   1 回限りのモードを使用してバインドすることが、 [**イメージ**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.Image)現在のユーザーの写真にします。
-   一方向のモードを使用してバインドすることが、 [ **ListView** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView)新聞のセクションでグループ化されたリアルタイムのニュース記事のコレクションにします。
-   双方向のモードを使用してバインドすることが、 [ **TextBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)フォームで顧客の名前にします。

モードに依存しない、バインディングの 2 種類がありますし、どちらも通常宣言される UI のマークアップでします。 [{x:Bind} マークアップ拡張](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)と [{Binding} マークアップ拡張](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)のいずれを使うかを選択できます。 また、同じアプリや同じ UI 要素で、この 2 つを組み合わせて使うこともできます。 {x:Bind} は Windows 10 の新機能で、パフォーマンスが向上しています。 このトピックで説明されているすべての詳細は、特に明記していない限り、両方の種類のバインディングに適用されます。

**{X:bind} を示すサンプル アプリ**

-   [{x:Bind} のサンプル](https://go.microsoft.com/fwlink/p/?linkid=619989)。
-   [QuizGame](https://github.com/microsoft/Windows-appsample-networkhelper)。
-   [XAML UI の基本のサンプル](https://go.microsoft.com/fwlink/p/?linkid=619992)。

**{バインディング} を示すサンプル アプリ**

-   [Bookstore1](https://go.microsoft.com/fwlink/?linkid=532950) アプリのダウンロード。
-   [Bookstore2](https://go.microsoft.com/fwlink/?linkid=532952) アプリのダウンロード。

## <a name="every-binding-involves-these-pieces"></a>すべてのバインディングに関連する要素

-   *バインディング ソース*。 これは、バインディング用のデータのソースで、UI に表示する値を持つメンバーが含まれる、任意のクラスのインスタンスを使うことができます。
-   *バインディング ターゲット*。 これは、データを表示する UI の [**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) の [**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) です。
-   *バインディング オブジェクト*。 これは、ソースからターゲットに、および必要に応じてターゲットからソースに、データ値を転送する要素です。 バインディング オブジェクトは、XAML の読み込み時に [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) または [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) マークアップ拡張から作成されます。

次のセクションでは、バインディング ソース、バインディング ターゲット、バインド オブジェクトについて詳しく見てみます。 また、以下のセクションでは、**HostViewModel** という名前のクラスに属する **NextButtonText** という名前の文字列プロパティに、ボタンのコンテンツをバインドする例へのリンクも示します。

### <a name="binding-source"></a>バインディング ソース

バインディング ソースとして使用できるクラスの非常に基本的な実装を次に示します。

使用している場合[C +/cli WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)、新規に追加し、 **Midl ファイル (.idl)** という名前の c++ で示すように、プロジェクトに項目/cli WinRT コードの例の一覧が下です。 それらの新しいファイルの内容を置き換えます、 [MIDL 3.0](/uwp/midl-3/intro)の一覧で示すようにコードを生成するプロジェクトをビルド`HostViewModel.h`と`.cpp`、し、一覧に一致するように、生成されたファイルにコードを追加します。 生成されたファイルに関する詳細情報と、プロジェクトにコピーする方法は、次を参照してください。 [XAML 制御; バインド c++/cli WinRT プロパティ](/windows/uwp/cpp-and-winrt-apis/binding-property)。

```csharp
public class HostViewModel
{
    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText { get; set; }
}
```

```cppwinrt
// HostViewModel.idl
namespace DataBindingInDepth
{
    runtimeclass HostViewModel
    {
        HostViewModel();
        String NextButtonText;
    }
}

// HostViewModel.h
// Implement the constructor like this, and add this field:
...
HostViewModel() : m_nextButtonText{ L"Next" } {}
...
private:
    std::wstring m_nextButtonText;
...

// HostViewModel.cpp
// Implement like this:
...
hstring HostViewModel::NextButtonText()
{
    return hstring{ m_nextButtonText };
}

void HostViewModel::NextButtonText(hstring const& value)
{
    m_nextButtonText = value;
}
...
```

**HostViewModel** の実装とその **NextButtonText** プロパティは、1 回限りのバインディングにのみ適しています。 ただし、一方向バインディングと双方向バインディングは非常に一般的であり、これらの種類のバインディングでは、UI はバインディング ソースのデータ値の変化に対応して自動的に更新されます。 これらの種類のバインディングが正常に動作するには、バインディング ソースをバインディング オブジェクトから "監視可能" にする必要があります。 この例では、**NextButtonText** プロパティに対して一方向または双方向バインディングを設定する場合、実行時にこのプロパティの値に対して発生するすべての変更を、バインディング オブジェクトから監視可能にする必要があります。

そのための方法の 1 つは、[**DependencyObject**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyObject) からバインディング ソースを表すクラスを派生させ、[**DependencyProperty**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DependencyProperty) を通じてデータ値を公開することです。 これが、[**FrameworkElement**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) を監視可能にする方法です。 **FrameworkElements** は、そのまま利用できる優れたバインディング ソースです。

より簡単にクラスを監視可能にする方法 (および既に基底クラスがあるクラスで必要な方法) は、[**System.ComponentModel.INotifyPropertyChanged**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?redirectedfrom=MSDN) を実装することです。 この方法は、**PropertyChanged** という名前の単一のイベントを実装するだけです。 **HostViewModel** を使った例を次に示します。

```csharp
...
using System.ComponentModel;
using System.Runtime.CompilerServices;
...
public class HostViewModel : INotifyPropertyChanged
{
    private string nextButtonText;

    public event PropertyChangedEventHandler PropertyChanged = delegate { };

    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText
    {
        get { return this.nextButtonText; }
        set
        {
            this.nextButtonText = value;
            this.OnPropertyChanged();
        }
    }

    public void OnPropertyChanged([CallerMemberName] string propertyName = null)
    {
        // Raise the PropertyChanged event, passing the name of the property whose value has changed.
        this.PropertyChanged(this, new PropertyChangedEventArgs(propertyName));
    }
}
```

```cppwinrt
// HostViewModel.idl
namespace DataBindingInDepth
{
    runtimeclass HostViewModel : Windows.UI.Xaml.Data.INotifyPropertyChanged
    {
        HostViewModel();
        String NextButtonText;
    }
}

// HostViewModel.h
// Add this field:
...
    winrt::event_token PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler);
    void PropertyChanged(winrt::event_token const& token) noexcept;

private:
    winrt::event<Windows::UI::Xaml::Data::PropertyChangedEventHandler> m_propertyChanged;
...

// HostViewModel.cpp
// Implement like this:
...
void HostViewModel::NextButtonText(hstring const& value)
{
    if (m_nextButtonText != value)
    {
        m_nextButtonText = value;
        m_propertyChanged(*this, Windows::UI::Xaml::Data::PropertyChangedEventArgs{ L"NextButtonText" });
    }
}

winrt::event_token HostViewModel::PropertyChanged(Windows::UI::Xaml::Data::PropertyChangedEventHandler const& handler)
{
    return m_propertyChanged.add(handler);
}

void HostViewModel::PropertyChanged(winrt::event_token const& token) noexcept
{
    m_propertyChanged.remove(token);
}
...
```

これで、**NextButtonText** プロパティは監視可能です。 そのプロパティへの一方向または双方向のバインディングを作成する場合 (方法については後述)、作成したバインディング オブジェクトは **PropertyChanged** イベントを受信登録します。 そのイベントが発生すると、バインディング オブジェクトのハンドラーは、変更されたプロパティの名前を含む引数を受け取ります。 このようにして、バインディング オブジェクトはどのプロパティの値が残っており、再び読み取る必要があるかを識別します。

使用している場合は、複数回、上記のパターンを実装する必要があるないようにC#だけから派生することができ、 **BindableBase**で低音のクラス、 [QuizGame](https://github.com/microsoft/Windows-appsample-networkhelper) (サンプル"Common"フォルダー)。 どのようになるかを示す例を以下に示します。

```csharp
public class HostViewModel : BindableBase
{
    private string nextButtonText;

    public HostViewModel()
    {
        this.NextButtonText = "Next";
    }

    public string NextButtonText
    {
        get { return this.nextButtonText; }
        set { this.SetProperty(ref this.nextButtonText, value); }
    }
}
```

```cppwinrt
// Your BindableBase base class should itself derive from Windows::UI::Xaml::DependencyObject. Then, in HostViewModel.idl, derive from BindableBase instead of implementing INotifyPropertyChanged.
```

> [!NOTE]
> C++/cli WinRT、基底クラスから派生した、アプリケーションで宣言された任意のランタイム クラスと呼ばれる、*コンポーザブル*クラス。 構成可能なクラスに関する制約があります。 アプリケーションに渡す、 [Windows アプリ認定キット](../debug-test-perf/windows-app-certification-kit.md)Visual Studio では、Microsoft Store での送信を検証するために使用するテスト (ため Microsoft Store に正常に取り込まれるアプリケーションの)、コンポーザブル クラスは、Windows の基本クラスから最終的に派生する必要があります。 つまり、継承階層の非常にルートにあるクラスは、Windows.* 名前空間で生成された型である必要があります。 ランタイム クラスを基底クラスから派生する必要は場合&mdash;を実装する例を**BindableBase**クラスから派生するは、ビュー モデルのすべての&mdash;から派生できます[ **Windows.UI.Xaml.DependencyObject**](/uwp/api/windows.ui.xaml.dependencyobject)します。

[  **String.Empty**](https://docs.microsoft.com/dotnet/api/system.string.empty?redirectedfrom=MSDN) または **null** の引数を使って **PropertyChanged** イベントを発生させることは、オブジェクトのすべての非インデクサー プロパティを再び読み取る必要があることを示します。 引数を使用してオブジェクトのインデクサー プロパティを変更したことを示すイベントを発生させることができます"項目\[*インデクサー*\]"特定のインデクサー (場所*インデクサー*はインデックス値にある) かの値を"項目\[\]"のすべてのインデクサー。

バインディング ソースは、プロパティにデータが含まれる単一のオブジェクト、またはオブジェクトのコレクションとして処理できます。 C# Visual Basic のコードを実装するオブジェクトを 1 回限りのバインドをできますと[ **List (Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1?redirectedfrom=MSDN)実行時に変更することはコレクションを表示します。 監視可能なコレクション (コレクションの項目の追加と削除を監視する) の場合、代わりに [**ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1?redirectedfrom=MSDN) への一方向バインディングを設定します。 C++ コードでは、監視可能なコレクションと監視可能ではないコレクションの両方について、[**Vector&lt;T&gt;** ](https://docs.microsoft.com/dotnet/api/system.numerics.vector-1?redirectedfrom=MSDN) にバインドできます。 独自のコレクション クラスにバインドするには、次の表をご覧ください。

|シナリオ|C# と VB (CLR)|C++/WinRT|C++/CX|
|-|-|-|-|
|オブジェクトにバインドする。|どのオブジェクトでもかまいません。|どのオブジェクトでもかまいません。|オブジェクトで [**BindableAttribute**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute) を指定するか、[**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider) を実装する必要があります。|
|バインドされたオブジェクトからプロパティの変更通知を取得します。|オブジェクトを実装する必要があります[ **INotifyPropertyChanged**](https://docs.microsoft.com/dotnet/api/system.componentmodel.inotifypropertychanged?redirectedfrom=MSDN)します。| オブジェクトを実装する必要があります[ **INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged)します。|オブジェクトを実装する必要があります[ **INotifyPropertyChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.INotifyPropertyChanged)します。|
|コレクションにバインドする。| [**List(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1?redirectedfrom=MSDN)|[**IVector** ](/uwp/api/windows.foundation.collections.ivector_t_)の[ **IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)、または[ **IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector)します。 参照してください[XAML コントロールの項目。 c++ にバインド/cli WinRT コレクション](../cpp-and-winrt-apis/binding-collection.md)と[コレクション c++/cli WinRT](../cpp-and-winrt-apis/collections.md)。| [**ベクター&lt;T&gt;** ](https://docs.microsoft.com/cpp/cppcx/platform-collections-vector-class)|
|バインドのコレクションからコレクションの変更通知を取得します。|[**ObservableCollection (Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1?redirectedfrom=MSDN)|[**IObservableVector** ](/uwp/api/windows.foundation.collections.iobservablevector_t_)の[ **IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)します。|[**IObservableVector&lt;T&gt;** ](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IObservableVector_T_)|
|バインドをサポートするコレクションを実装する。|[  **List(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.generic.list-1?redirectedfrom=MSDN) を拡張するか、[**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist?redirectedfrom=MSDN)、[**IList**](https://docs.microsoft.com/dotnet/api/system.collections.generic.ilist-1?redirectedfrom=MSDN)(Of [**Object**](https://docs.microsoft.com/dotnet/api/system.object?redirectedfrom=MSDN))、[**IEnumerable**](https://docs.microsoft.com/dotnet/api/system.collections.ienumerable?redirectedfrom=MSDN)、または [**IEnumerable**](https://docs.microsoft.com/dotnet/api/system.collections.generic.ienumerable-1?redirectedfrom=MSDN)(Of **Object**) を実装します。 汎用の **IList(Of T)** と **IEnumerable(Of T)** へのバインドはサポートされていません。|実装[ **IVector** ](/uwp/api/windows.foundation.collections.ivector_t_)の[ **IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)します。 参照してください[XAML コントロールの項目。 c++ にバインド/cli WinRT コレクション](../cpp-and-winrt-apis/binding-collection.md)と[コレクション c++/cli WinRT](../cpp-and-winrt-apis/collections.md)。|[  **IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector)、[**IBindableIterable**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableIterable)、[**IVector**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IVector_T_)&lt;[**Object**](https://docs.microsoft.com/dotnet/api/system.object?redirectedfrom=MSDN)^&gt;、[**IIterable**](https://docs.microsoft.com/uwp/api/Windows.Foundation.Collections.IIterable_T_)&lt;**Object**^&gt;、**IVector**&lt;[**IInspectable**](https://docs.microsoft.com/windows/desktop/api/inspectable/nn-inspectable-iinspectable)\*&gt;、または **IIterable**&lt;**IInspectable**\*&gt; を実装します。 汎用の **IVector&lt;T&gt;** と **IIterable&lt;T&gt;** へのバインドはサポートされていません。|
| コレクションの変更通知をサポートするコレクションを実装します。 | [  **ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1?redirectedfrom=MSDN) を拡張するか、(汎用ではない) [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist?redirectedfrom=MSDN) と [**INotifyCollectionChanged**](https://docs.microsoft.com/dotnet/api/system.collections.specialized.inotifycollectionchanged?redirectedfrom=MSDN) を実装します。|実装[ **IObservableVector** ](/uwp/api/windows.foundation.collections.iobservablevector_t_)の[ **IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)、または[ **IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector).|[  **IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector) と [**IBindableObservableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableObservableVector) を実装します。|
|段階的読み込みをサポートするコレクションを実装する。|[  **ObservableCollection(Of T)** ](https://docs.microsoft.com/dotnet/api/system.collections.objectmodel.observablecollection-1?redirectedfrom=MSDN) を拡張するか、(汎用ではない) [**IList**](https://docs.microsoft.com/dotnet/api/system.collections.ilist?redirectedfrom=MSDN) と [**INotifyCollectionChanged**](https://docs.microsoft.com/dotnet/api/system.collections.specialized.inotifycollectionchanged?redirectedfrom=MSDN) を実装します。 さらに、[**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading) を実装します。|実装[ **IObservableVector** ](/uwp/api/windows.foundation.collections.iobservablevector_t_)の[ **IInspectable**](/windows/desktop/api/inspectable/nn-inspectable-iinspectable)、または[ **IBindableObservableVector**](/uwp/api/windows.ui.xaml.interop.ibindableobservablevector). さらに、[**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading) を実装します|[  **IBindableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableVector)、[**IBindableObservableVector**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Interop.IBindableObservableVector)、[**ISupportIncrementalLoading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading) を実装します。|

段階的読み込みを使うと、任意の大きさのデータ ソースにリスト コントロールをバインドすると同時に、高パフォーマンスを実現できます。 たとえば、一度にすべての結果を読む込むことなく、Bing の画像クエリ結果にリスト コントロールをバインドすることができます。 この場合、すぐに読み込むのは一部の結果だけで、他の結果は必要に応じて読み込みます。 増分読み込みをサポートするために実装する必要があります[ **ISupportIncrementalLoading** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ISupportIncrementalLoading)コレクションをサポートするデータ ソースに通知を変更します。 データ バインディング エンジンがより多くのデータを要求する場合は、UI を更新するためにデータ ソースで適切な要求を行い、結果を統合して、適切な通知を送信する必要があります。

### <a name="binding-target"></a>バインディング ターゲット

以下の 2 つの例で、 **Button.Content**プロパティはバインディング ターゲット、およびバインディング オブジェクトを宣言するマークアップ拡張機能にその値を設定します。 最初に [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) を示し、次に [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) を示します。 マークアップでバインディングを宣言する方法は一般的です (便利で、読みやすく、ツールで処理できます)。 ただし、必要な場合は、マークアップを使わずに、命令を使って (プログラムで) [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) クラスのインスタンスを作成できます。

```xaml
<Button Content="{x:Bind ...}" ... />
```

```xaml
<Button Content="{Binding ...}" ... />
```

C + を使用している場合/cli WinRT または Visual C コンポーネント拡張 (C +/cli CX) を追加する必要があります、 [ **BindableAttribute** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute)属性を使用する任意のランタイム クラス、 [{binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)でマークアップ拡張機能。

> [!IMPORTANT]
> 使用している場合[C +/cli WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)、 [ **BindableAttribute** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute)属性は、Windows SDK バージョン (Windows 10、バージョンは 1809 10.0.17763.0 をインストールした場合に使用)、またはそれ以降。 その属性がない場合は、実装する必要があります、 [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider)と[ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty)インターフェイスを使用するには、 [{binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)マークアップ拡張機能。

### <a name="binding-object-declared-using-xbind"></a>{x:Bind} を使って宣言されたバインディング オブジェクト

[{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) マークアップを作成する前に必要な手順が 1 つあります。 マークアップのページを表すクラスからバインディング ソース クラスを公開する必要があります。 プロパティを追加することで行っている (型の**HostViewModel**ここでは) を**MainPage**クラスのページします。

```csharp
namespace DataBindingInDepth
{
    public sealed partial class MainPage : Page
    {
        public MainPage()
        {
            this.InitializeComponent();
            this.ViewModel = new HostViewModel();
        }
    
        public HostViewModel ViewModel { get; set; }
    }
}
```

```cppwinrt
// MainPage.idl
import "HostViewModel.idl";

namespace DataBindingInDepth
{
    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        HostViewModel ViewModel{ get; };
    }
}

// MainPage.h
// Include a header, and add this field:
...
#include "HostViewModel.h"
...
    DataBindingInDepth::HostViewModel ViewModel();

private:
    DataBindingInDepth::HostViewModel m_viewModel{ nullptr };
...

// MainPage.cpp
// Implement like this:
...
MainPage::MainPage()
{
    InitializeComponent();

}

DataBindingInDepth::HostViewModel MainPage::ViewModel()
{
    return m_viewModel;
}
...
```

これが完了したら、バインディング オブジェクトを宣言するマークアップを詳しく見ていくことができます。 次の例では、前の「バインディング ターゲット」セクションで使用したものと同じ **Button.Content** バインディング ターゲットを使って、**HostViewModel.NextButtonText** プロパティにバインドされたバインディング ターゲットを示します。

```xaml
<!-- MainPage.xaml -->
<Page x:Class="DataBindingInDepth.Mainpage" ... >
    <Button Content="{x:Bind Path=ViewModel.NextButtonText, Mode=OneWay}" ... />
</Page>
```

**Path** として指定している値に注意してください。 この値は、ページ自体のコンテキストで解釈されるパスは、ここを参照して、 **ViewModel**だけに追加したプロパティ、 **MainPage**ページ。 このプロパティは **HostViewModel** インスタンスを返すため、そのオブジェクトにドットを付けて **HostViewModel.NextButtonText** プロパティにアクセスできます。 さらに、**Mode** を指定して、[{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) の既定値である 1 回限りのバインディングをオーバーライドします。

[  **Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) プロパティは、入れ子になったプロパティ、添付プロパティ、整数と文字列のインデクサーにバインドするためのさまざまな構文オプションをサポートしています。 詳しくは、「[Property-path 構文](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax)」をご覧ください。 文字列のインデクサーにバインドすると、[**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider) を実装しなくても動的プロパティにバインドする効果を得られます。 その他の設定については、「[{x:Bind} マークアップ拡張](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)」をご覧ください。

説明するため、 **HostViewModel.NextButtonText**プロパティが実際に監視可能な場合、追加、**クリックして**、ボタンのイベント ハンドラーの値を更新**HostViewModel.NextButtonText**. ビルド、実行、およびボタンの値を表示するボタンをクリックします。**コンテンツ**を更新します。

```csharp
// MainPage.xaml.cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    this.ViewModel.NextButtonText = "Updated Next button text";
}
```

```cppwinrt
// MainPage.cpp
void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
{
    ViewModel().NextButtonText(L"Updated Next button text");
}
```

> [!NOTE]
> 変更[ **TextBox.Text** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text)双方向のバインドに送信されるソース、 [ **TextBox** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)がフォーカスを失うとすべてのユーザーのキー入力の後。

**DataTemplate および x: データ型**

[  **DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate) 内で (項目テンプレート、コンテンツ テンプレート、ヘッダー テンプレートのいずれとして使用される場合でも)、**Path** の値はページのコンテキストではなく、テンプレート化されたデータ オブジェクトのコンテキストで解釈されています。 {x:Bind} をデータ テンプレートで使用する場合、コンパイル時にバインディングを検証できるように (バインディング用に効率的なコードを生成できるように) するために、**DataTemplate** では、**x:DataType** を使って、データ オブジェクトの型を宣言する必要があります。 次に示す例は、**SampleDataGroup** オブジェクトのコレクションにバインドされている、項目コントロールの **ItemTemplate** として使うことができます。

```xaml
<DataTemplate x:Key="SimpleItemTemplate" x:DataType="data:SampleDataGroup">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{x:Bind Title}"/>
      <TextBlock Text="{x:Bind Description}"/>
    </StackPanel>
  </DataTemplate>
```

**パス内のオブジェクトの弱い型指定**

たとえば、SampleDataGroup という名前の型があり、Title という名前の文字列プロパティを実装しているとします。 使用してプロパティ MainPage.SampleDataGroupAsObject、オブジェクトの型ですが、実際に SampleDataGroup のインスタンスを返します。 バインディング `<TextBlock Text="{x:Bind SampleDataGroupAsObject.Title}"/>` は、型オブジェクトで Title プロパティが見つからないため、コンパイル エラーとなります。 これを解決するには、Path 構文に `<TextBlock Text="{x:Bind ((data:SampleDataGroup)SampleDataGroupAsObject).Title}"/>` などのキャストを追加します。 Element がオブジェクトとして宣言されているが、実際には TextBlock である、`<TextBlock Text="{x:Bind Element.Text}"/>` という例を考えてみます。 この場合も、`<TextBlock Text="{x:Bind ((TextBlock)Element).Text}"/>` のようにキャストによって問題が解決されます。

**場合は、データが非同期的に読み込みます**

ページの部分クラスに、 **{x:Bind}** をサポートするコードがコンパイル時に生成されます。 これらのファイルは `obj` フォルダー内にあり、`<view name>.g.cs` (C# の場合) などの名前が付けられています。 生成されたコードには、ページの [**Loading**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.FrameworkElement) イベントのハンドラーが含まれており、このハンドラーが、ページのバインディングを表す生成されたクラスで **Initialize** メソッドを呼び出します。 次に、**Initialize** が **Update** を呼び出して、バインディング ソースとターゲットの間のデータの移動を開始します。 **Loading** は、ページまたはユーザー コントロールの最初の測定パスの直前に発生します。 したがって、データが非同期的に読み込まれる場合、**Initialize** が呼び出された時点で準備ができていない可能性があります。 そのため、データを読み込んだ後、`this.Bindings.Update();` を呼び出すことによって、1 回限りのバインディングを強制的に実行できます。 非同期的に読み込まれたデータのバインドを 1 回限りのみ必要な場合は、一方向のバインドがあると、変更をリッスンするよりも、それらがこのように初期化するためにかなり安価です。 データがきめ細かく変更されない場合や、特定のアクションの一部として更新される可能性が高い場合は、バインディングを 1 回限りにし、いつでも **Update** を呼び出すことによって、強制的に手動更新を実行できます。

> [!NOTE]
> **{0} バインド: x}** 各部への JSON オブジェクトもアヒル型定義のディクショナリなど、遅延バインディング シナリオには適していません。 「ダック タイピング」脆弱な形式でプロパティ名の構文の一致に基づく入力するは、(、「場合について説明します、水中、アヒルのように鳴いたらはアヒル」)。 アヒルへのバインドと、**年齢**プロパティで均等に満たされるように、**人**または**ワイン**オブジェクト (各型があることを前提、**年齢**プロパティ)。 これらのシナリオを使用して、 **{binding}** マークアップ拡張機能。

### <a name="binding-object-declared-using-binding"></a>{Binding} を使って宣言されたバインディング オブジェクト

C + を使用している場合/cli WinRT または Visual C コンポーネント拡張 (C +/cli CX) し、使用する、 [{binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)マークアップ拡張機能を追加する必要があります、 [ **BindableAttribute** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute)属性にバインドする任意のランタイム クラスをします。 使用する[{X:bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)、その属性は必要はありません。

```cppwinrt
// HostViewModel.idl
// Add this attribute:
[Windows.UI.Xaml.Data.Bindable]
runtimeclass HostViewModel : Windows.UI.Xaml.Data.INotifyPropertyChanged
...
```

> [!IMPORTANT]
> 使用している場合[C +/cli WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt)、 [ **BindableAttribute** ](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.BindableAttribute)属性は、Windows SDK バージョン (Windows 10、バージョンは 1809 10.0.17763.0 をインストールした場合に使用)、またはそれ以降。 その属性がない場合は、実装する必要があります、 [ICustomPropertyProvider](/uwp/api/windows.ui.xaml.data.icustompropertyprovider)と[ICustomProperty](/uwp/api/windows.ui.xaml.data.icustomproperty)インターフェイスを使用するには、 [{binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)マークアップ拡張機能。

[{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) は、既定で、マークアップ ページの [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) にバインドしていることを前提としています。 したがって、ページの **DataContext** を、バインディング ソース クラス (ここでは **HostViewModel** 型) のインスタンスに設定します。 次の例は、バインディング オブジェクトを宣言するマークアップを示しています。 前の「バインディング ターゲット」セクションで使用したものと同じ **Button.Content** バインディング ターゲットを使っており、**HostViewModel.NextButtonText** プロパティにバインドします。

```xaml
<Page xmlns:viewmodel="using:DataBindingInDepth" ... >
    <Page.DataContext>
        <viewmodel:HostViewModel x:Name="viewModelInDataContext"/>
    </Page.DataContext>
    ...
    <Button Content="{Binding Path=NextButtonText}" ... />
</Page>
```

```csharp
// MainPage.xaml.cs
private void Button_Click(object sender, RoutedEventArgs e)
{
    this.viewModelInDataContext.NextButtonText = "Updated Next button text";
}
```

```cppwinrt
// MainPage.cpp
void MainPage::ClickHandler(IInspectable const&, RoutedEventArgs const&)
{
    viewModelInDataContext().NextButtonText(L"Updated Next button text");
}
```

**Path** として指定している値に注意してください。 この値は、ページの [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) で解釈されます。この例では、**HostViewModel** のインスタンスに設定されます。 パスは **HostViewModel.NextButtonText** プロパティを参照します。 [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) の既定値である一方向のバインディングが適切であるため、**Mode** は省略できます。

UI 要素の [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) の既定値は、その親から継承された値です。 もちろん **DataContext** を明示的に設定することによってこの既定値 (さらに既定で子に継承される) をオーバーライドすることができます。 要素で明示的に **DataContext** を設定することは、同じソースを使う複数のバインディングが必要な場合に便利です。

バインディング オブジェクトには **Source** プロパティがあり、その既定値はバインディングが宣言されている UI 要素の [**DataContext**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) です。 この既定値をオーバーライドするには、バインディングで **Source**、**RelativeSource**、または **ElementName** を明示的に設定します (詳しくは、「[{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)」をご覧ください)。

内で、 [ **DataTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.DataTemplate)、 [ **DataContext** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext)テンプレート化されているデータ オブジェクトが自動的に設定します。 次の例は、**Title** と **Description** という名前の文字列プロパティを持つ任意の型のコレクションにバインドされている、項目コントロールの **ItemTemplate** として使うことができます

```xaml
<DataTemplate x:Key="SimpleItemTemplate">
    <StackPanel Orientation="Vertical" Height="50">
      <TextBlock Text="{Binding Title}"/>
      <TextBlock Text="{Binding Description"/>
    </StackPanel>
  </DataTemplate>
```

> [!NOTE]
> 既定では、変更[ **TextBox.Text** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.textbox.text)双方向のバインドに送信されるときにソース、 [**テキスト ボックス**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)がフォーカスを失った。 変更をユーザーの各キーストロークの後に送信するには、マークアップのバインディングで **UpdateSourceTrigger** を **PropertyChanged** に設定します。 **UpdateSourceTrigger** を **Explicit** に設定することによって、変更が送信されるタイミングを完全に制御することもできます。 次に、テキスト ボックスでのイベント (通常 [**TextBox.TextChanged**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.TextBox)) を処理し、ターゲットで [**GetBindingExpression**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.getbindingexpression) を呼び出して [**BindingExpression**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingexpression) オブジェクトを取得します。最後に、[**BindingExpression.UpdateSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingexpression.updatesource) を呼び出して、データ ソースをプログラムで更新します。

[  **Path**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.path) プロパティは、入れ子になったプロパティ、添付プロパティ、整数と文字列のインデクサーにバインドするためのさまざまな構文オプションをサポートしています。 詳しくは、「[Property-path 構文](https://docs.microsoft.com/windows/uwp/xaml-platform/property-path-syntax)」をご覧ください。 文字列のインデクサーにバインドすると、[**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider) を実装しなくても動的プロパティにバインドする効果を得られます。 [  **ElementName**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.elementname) プロパティは要素間のバインディングに便利です。 [  **RelativeSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.relativesource) プロパティにはいくつかの用途があり、そのうちの 1 つが、[**ControlTemplate**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ControlTemplate) 内でバインディングをテンプレート化するためのより強力な方法としての用途です。 その他の設定については、「[{Binding} マークアップ拡張](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)」と [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) クラスの説明をご覧ください。

## <a name="what-if-the-source-and-the-target-are-not-the-same-type"></a>ソースとターゲットが同じ型ではない場合

ブール型プロパティの値に基づいて UI 要素の表示を制御する場合、数値の範囲や傾向に応じた色で UI 要素をレンダリングする場合、または文字列を想定している UI 要素のプロパティに日付や時刻の値を表示する場合は、値の型を別の型に変換する必要があります。 バインディング ソース クラスから適切な型の別のプロパティを公開し、変換ロジックをカプセル化し、その中でテストできるようにしておくことが、適切なソリューションである場合があります。 ただし、この方法はソースとターゲットのプロパティの数が多くなり、組み合わせが多くなると、柔軟性も拡張性もありません。 その場合は、いくつかのオプションがあります。

* {X:Bind} を使用している場合、関数に直接バインドして変換を行うことができます
* または、変換を実行するためのオブジェクトである、値コンバーターを指定することができます 

## <a name="value-converters"></a>値コンバーター

[  **DateTime**](https://docs.microsoft.com/dotnet/api/system.datetime?redirectedfrom=MSDN) 値を月を含む文字列値に変換する、1 回限りまたは一方向のバインディングに適した値コンバーターを以下に示します。 このクラスは、[**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter) を実装します。

```csharp
public class DateToStringConverter : IValueConverter
{
    // Define the Convert method to convert a DateTime value to 
    // a month string.
    public object Convert(object value, Type targetType, 
        object parameter, string language)
    {
        // value is the data from the source object.
        DateTime thisdate = (DateTime)value;
        int monthnum = thisdate.Month;
        string month;
        switch (monthnum)
        {
            case 1:
                month = "January";
                break;
            case 2:
                month = "February";
                break;
            default:
                month = "Month not found";
                break;
        }
        // Return the value to pass to the target.
        return month;
    }

    // ConvertBack is not implemented for a OneWay binding.
    public object ConvertBack(object value, Type targetType, 
        object parameter, string language)
    {
        throw new NotImplementedException();
    }
}
```

```cppwinrt
// See the "Formatting or converting data values for display" section in the "Data binding overview" topic.
```

次に、バインディング オブジェクトのマークアップでその値コンバーターを利用する方法を示します。

```xaml
<UserControl.Resources>
  <local:DateToStringConverter x:Key="Converter1"/>
</UserControl.Resources>
...
<TextBlock Grid.Column="0" 
  Text="{x:Bind ViewModel.Month, Converter={StaticResource Converter1}}"/>
<TextBlock Grid.Column="0" 
  Text="{Binding Month, Converter={StaticResource Converter1}}"/>
```

バインドの [**Converter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converter) パラメーターを定義した場合、[**Convert**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.ivalueconverter.convert) メソッドと [**ConvertBack**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.ivalueconverter.convertback) メソッドがバインド エンジンによって呼び出されます。 ソースからデータが渡されると、バインド エンジンは、**Convert** を呼び出し、返すデータをターゲットに渡します。 ターゲットからデータが渡されると (双方向バインディングの場合)、バインド エンジンは、**ConvertBack** を呼び出し、返すデータをソースに渡します。

コンバーターは、省略可能なパラメーターもあります。[**ConverterLanguage**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterlanguage)、変換処理で使用する言語を指定することができますと[ **ConverterParameter**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.binding.converterparameter)パラメーターを渡すことができますが、変換ロジック。 コンバーター パラメーターの使用例については、「[**IValueConverter**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.IValueConverter)」をご覧ください。

> [!NOTE]
> 変換でエラーがある場合は、例外をスローしません。 代わりに [**DependencyProperty.UnsetValue**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyproperty.unsetvalue) を返します。これにより、データ転送が中止されます。

バインディング ソースを解決できない場合に使用する既定値を表示するには、マークアップのバインディング オブジェクトで **FallbackValue** プロパティを設定します。 これは、変換エラーや書式エラーを処理する場合に便利です。 また、バインド時にソースのプロパティが、型が混在するバインド先のコレクションのどのオブジェクトにも見つからないときにも便利です。

テキスト コントロールを文字列でない値にバインドした場合、データ バインディング エンジンは値を文字列に変換します。 値が参照型である場合、データ バインディング エンジンは [**ICustomPropertyProvider.GetStringRepresentation**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.icustompropertyprovider.getstringrepresentation) または [**IStringable.ToString**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nf-windows-foundation-istringable-tostring) を呼び出すことで文字列の値を取得します。取得できない場合は、[**Object.ToString**](https://docs.microsoft.com/dotnet/api/system.object.tostring?redirectedfrom=MSDN#System_Object_ToString) を呼び出します。 ただし、データ バインディング エンジンは基底クラスの実装を隠ぺいする **ToString** の実装を無視することに注意してください。 代わりに、サブクラスの実装で基底クラスの **ToString** メソッドをオーバーライドする必要があります。 同様に、ネイティブ言語では、すべてのマネージ オブジェクトが [**ICustomPropertyProvider**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.ICustomPropertyProvider) と [**IStringable**](https://docs.microsoft.com/windows/desktop/api/windows.foundation/nn-windows-foundation-istringable) を実装しているように見えます。 ただし、**GetStringRepresentation** と **IStringable.ToString** のすべての呼び出しは、**Object.ToString** またはそのオーバーライドされたメソッドに割り振られます。基底クラスの実装を隠ぺいする新しい **ToString** メソッドの実装に割り振られることはありません。

> [!NOTE]
> Windows 10、バージョン1607 以降では、XAML フレームワークにブール値と Visibility 値のコンバーターが組み込まれています。 コンバーターは、**Visible** 列挙値に対して **true** を、**Collapsed** に対して **false** をマッピングします。これにより、コンバーターを作成せずに Visibility プロパティをブール値にバインドできます。 組み込みのコンバーターを使用するには、アプリの最小のターゲット SDK バージョンが 14393 以降である必要があります。 アプリがそれよりも前のバージョンの Windows 10 をターゲットとしている場合は使うことができません。 ターゲット バージョンについて詳しくは、「[バージョン アダプティブ コード](https://docs.microsoft.com/windows/uwp/debug-test-perf/version-adaptive-code)」をご覧ください。

## <a name="function-binding-in-xbind"></a>{X:Bind} の関数バインド

{x:Bind} は関数となるバインディング パスの最終ステップを有効化します。 これを使って変換を実行できます。また 1 つ以上のプロパティに依存するバインディングを実行できます。 参照してください[ **X:bind 関数**](function-bindings.md)

<span id="resource-dictionaries-with-x-bind"/>

## <a name="element-to-element-binding"></a>要素の要素へのバインド

1 つの XAML 要素のプロパティは、別の XAML 要素のプロパティにバインドできます。 例のマークアップで表示する方法。

```xaml
<TextBox x:Name="myTextBox" />
<TextBlock Text="{x:Bind myTextBox.Text, Mode=OneWay}" />
```

> [!IMPORTANT]
> 使用して要素から要素のバインディングのために必要なワークフローのC++/WinRT を参照してください[要素から要素のバインディング](/windows/uwp/cpp-and-winrt-apis/binding-property#element-to-element-binding)します。

## <a name="resource-dictionaries-with-xbind"></a>リソース ディクショナリと {x:Bind}

[{x:Bind} マークアップ拡張](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)はコードの生成に依存するため、**InitializeComponent** (生成されたコードを初期化する) を呼び出すコンストラクターを含むコード ビハインド ファイルが必要です。 ファイル名を参照する代わりに、その型をインスタンス化する (**InitializeComponent** が呼び出される) ことによって、リソース ファイルを再利用します。 次の例では、既存のリソース ディクショナリがあり、その中で {x:Bind} を使う場合の対処方法を示します。

TemplatesResourceDictionary.xaml

```xaml
<ResourceDictionary
    x:Class="ExampleNamespace.TemplatesResourceDictionary"
    .....
    xmlns:examplenamespace="using:ExampleNamespace">
    
    <DataTemplate x:Key="EmployeeTemplate" x:DataType="examplenamespace:IEmployee">
        <Grid>
            <TextBlock Text="{x:Bind Name}"/>
        </Grid>
    </DataTemplate>
</ResourceDictionary>
```

TemplatesResourceDictionary.xaml.cs

```csharp
using Windows.UI.Xaml.Data;
 
namespace ExampleNamespace
{
    public partial class TemplatesResourceDictionary
    {
        public TemplatesResourceDictionary()
        {
            InitializeComponent();
        }
    }
}
```

MainPage.xaml

```xaml
<Page x:Class="ExampleNamespace.MainPage"
    ....
    xmlns:examplenamespace="using:ExampleNamespace">

    <Page.Resources>
        <ResourceDictionary>
            .... 
            <ResourceDictionary.MergedDictionaries>
                <examplenamespace:TemplatesResourceDictionary/>
            </ResourceDictionary.MergedDictionaries>
        </ResourceDictionary>
    </Page.Resources>
</Page>
```

## <a name="event-binding-and-icommand"></a>イベント バインディングと ICommand

[{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) はイベント バインディングと呼ばれる機能をサポートしています。 この機能によって、バインディングを使用するイベントのハンドラーを指定できます。これは、コード ビハインド ファイルのメソッドによるイベント処理に対する追加のオプションです。 たとえば、**MainPage** クラスに **RootFrame** プロパティがあるとします。

```csharp
public sealed partial class MainPage : Page
{
    ...
    public Frame RootFrame { get { return Window.Current.Content as Frame; } }
}
```

次のように、**RootFrame** プロパティによって返される **Frame** オブジェクトのメソッドに、ボタンの **Click** イベントをバインドできます。 また、ボタンの **IsEnabled** プロパティを、同じ **Frame** の別のメンバーにもバインドします。

```xaml
<AppBarButton Icon="Forward" IsCompact="True"
IsEnabled="{x:Bind RootFrame.CanGoForward, Mode=OneWay}"
Click="{x:Bind RootFrame.GoForward}"/>
```

この方法では、オーバーロードされたメソッドを使ってイベントを処理することはできません。 また、イベントを処理するメソッドにパラメーターがある場合、すべてのパラメーターがそれぞれ、イベントのすべての型から代入できる必要があります。 この場合、[**Frame.GoForward**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goforward) はオーバーロードされておらず、パラメーターはありません (ただし、2 つの **object** パラメーターを取る場合でも有効です)。 [**Frame.GoBack** ](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.frame.goback)がオーバー ロードされて、そのメソッドを使用してこの方法ではできないようにします。

イベント バインディングの手法は、コマンドの実装と使用に似ています (コマンドは [**ICommand**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.input.icommand) インターフェイスを実装するオブジェクトを返すプロパティです)。 [{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) と [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) はいずれもコマンドで動作します。 コマンド パターンを何度も実装する必要はありません。[QuizGame](https://github.com/microsoft/Windows-appsample-networkhelper) サンプル (Common フォルダー) に含まれている **DelegateCommand** ヘルパー クラスを使うことができます。

## <a name="binding-to-a-collection-of-folders-or-files"></a>フォルダーやファイルのコレクションへのバインド

[  **Windows.Storage**](https://docs.microsoft.com/uwp/api/Windows.Storage) 名前空間の API を使って、フォルダーとファイルのデータを取得できます。 ただし、**GetFilesAsync** メソッド、**GetFoldersAsync** メソッド、**GetItemsAsync** メソッドは、リスト コントロールへのバインドに適した値を返さないことがあります。 代わりに、[**FileInformationFactory**](https://docs.microsoft.com/uwp/api/Windows.Storage.BulkAccess.FileInformationFactory) クラスの [**GetVirtualizedFilesVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizedfilesvector) メソッド、[**GetVirtualizedFoldersVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizedfoldersvector) メソッド、[**GetVirtualizedItemsVector**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.fileinformationfactory.getvirtualizeditemsvector) メソッドの戻り値にバインドする必要があります。 [StorageDataSource と GetVirtualizedFilesVector のサンプルに関するページ](https://go.microsoft.com/fwlink/p/?linkid=228621)から抜粋した次のコード例は、一般的な使用パターンを示しています。 アプリ パッケージ マニフェストで **picturesLibrary** 機能を宣言し、ピクチャ ライブラリ フォルダーにピクチャがあることを確認します。

```csharp
protected override void OnNavigatedTo(NavigationEventArgs e)
{
    var library = Windows.Storage.KnownFolders.PicturesLibrary;
    var queryOptions = new Windows.Storage.Search.QueryOptions();
    queryOptions.FolderDepth = Windows.Storage.Search.FolderDepth.Deep;
    queryOptions.IndexerOption = Windows.Storage.Search.IndexerOption.UseIndexerWhenAvailable;

    var fileQuery = library.CreateFileQueryWithOptions(queryOptions);

    var fif = new Windows.Storage.BulkAccess.FileInformationFactory(
        fileQuery,
        Windows.Storage.FileProperties.ThumbnailMode.PicturesView,
        190,
        Windows.Storage.FileProperties.ThumbnailOptions.UseCurrentScale,
        false
        );

    var dataSource = fif.GetVirtualizedFilesVector();
    this.PicturesListView.ItemsSource = dataSource;
}
```

通常はこの方法で、ファイルとフォルダーの情報の読み取り専用ビューを作成します。 たとえば、ユーザーが音楽ビューで曲を評価できるように、ファイルとフォルダーのプロパティへの双方向のバインドを作成できます。 ただし、適切な **SavePropertiesAsync** メソッド ([**MusicProperties.SavePropertiesAsync**](https://docs.microsoft.com/uwp/api/windows.storage.fileproperties.musicproperties.savepropertiesasync) など) を呼び出すまで、変更は永続化されません。 項目からフォーカスが移動したときに選択のリセットがトリガーされるため、変更をコミットする必要があります。

この方法による双方向のバインドは、ミュージックなど、インデックス化された場所でのみ機能します。 ある場所がインデックス化されているかどうかを確認するには、[**FolderInformation.GetIndexedStateAsync**](https://docs.microsoft.com/uwp/api/windows.storage.bulkaccess.folderinformation.getindexedstateasync) メソッドを呼び出します。

仮想化されたベクターは、一部の項目に対して、値の設定の前に **null** を返す場合があることにも注意してください。 たとえば、仮想化されたベクターにバインドされたリスト コントロールの [**SelectedItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selecteditem) 値を使う前に、**null** をチェックする必要があります。または、代わりに [**SelectedIndex**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.primitives.selector.selectedindex) を使います。

## <a name="binding-to-data-grouped-by-a-key"></a>キーでグループ化されたデータへのバインド

項目のフラット コレクションを作成した場合 (によって表されますたとえば、ブック、 **BookSku**クラス) をキーとして共通のプロパティを使用して、項目をグループ化すると (、 **BookSku.AuthorName**プロパティなど)、。結果をグループ化されたデータと呼びます。 データをグループ化すると、フラットなコレクションではなくなります。 グループ化されたデータは、各グループ オブジェクトが、グループ オブジェクトのコレクション

- キーと
- プロパティがそのキーに一致する項目のコレクション。

結果、作成者の名前で、ブックをグループ化の結果の各グループには、作成者名グループのコレクションでブックの「例をもう一度実行するには

- である作成者の名前、キーと
- コレクション、 **BookSku**s が**AuthorName**プロパティ グループのキーに一致します。

一般的に、コレクションを表示するには、項目コントロール [**ItemsSource**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.controls.itemscontrol.itemssource) ([**ListView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.ListView) や [**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) など) を、コレクションを返すプロパティに直接バインドします。 項目のフラットなコレクションの場合は、何も特別なことをする必要はありません。 一方、グループ オブジェクトのコレクションの場合 (グループ化されたデータにバインドしている場合など) は、項目コントロールとバインディング ソースの間に存在する、[**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) と呼ばれる中間オブジェクトのサービスが必要です。 グループ化されたデータを返すプロパティに **CollectionViewSource** をバインドし、項目コントロールを **CollectionViewSource** にバンドします。 **CollectionViewSource** の追加の付加価値として現在の項目を追跡できるため、複数の項目コントロールをすべて同じ **CollectionViewSource** にバインドすることによって同期させることができます。 [  **CollectionViewSource.View**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.view) プロパティによって返されるオブジェクトの [**ICollectionView.CurrentItem**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.icollectionview.currentitem) プロパティによって、現在の項目にプログラムでアクセスすることもできます。

[  **CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) のグループ化機能をアクティブにするには、[**IsSourceGrouped**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.issourcegrouped) を **true** に設定します。 [  **ItemsPath**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.collectionviewsource.itemspath) プロパティも設定する必要があるかどうかは、グループ オブジェクトを作成する方法に依存します。 グループ オブジェクトを作成するには、"グループである" パターンと "グループを保持する" パターンの 2 つの方法があります。 "グループである" パターンでは、グループ オブジェクトはコレクション型から派生 (たとえば、**List&lt;T&gt;** ) から派生するため、グループ オブジェクトは実際にそれ自体が項目のグループです。 このパターンでは、**ItemsPath** を設定する必要はありません。 "グループを保持する" パターンでは、グループ オブジェクトはコレクション型 (**List&lt;T&gt;** など) の 1 つまたは複数のプロパティを持つため、グループは、プロパティの形式で項目のグループ (または複数のプロパティの形式で項目の複数のグループ) を "保持" します。 このパターンでは、**ItemsPath** を、項目のグループを含むプロパティの名前に設定する必要があります。

次の例は、"グループを保持する" パターンを示しています。 ページ クラスには [**ViewModel**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.datacontext) という名前のプロパティがあります。このプロパティはビュー モデルのインスタンスを返します。 [  **CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) はビュー モデルの **Authors** プロパティにバインドされ (**Authors** はグループ オブジェクトのコレクション)、それがグループ化された項目を格納する **Author.BookSkus** プロパティであることも指定します。 最後に、[**GridView**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.GridView) は **CollectionViewSource** にバインドされ、グループ内の項目をレンダリングできるようにグループのスタイルが定義されています。

```csharp
<Page.Resources>
    <CollectionViewSource
    x:Name="AuthorHasACollectionOfBookSku"
    Source="{x:Bind ViewModel.Authors}"
    IsSourceGrouped="true"
    ItemsPath="BookSkus"/>
</Page.Resources>
...
<GridView
ItemsSource="{x:Bind AuthorHasACollectionOfBookSku}" ...>
    <GridView.GroupStyle>
        <GroupStyle
            HeaderTemplate="{StaticResource AuthorGroupHeaderTemplateWide}" ... />
    </GridView.GroupStyle>
</GridView>
```

"グループである" パターンは、2 つの方法のいずれかで実装できます。 1 つは、独自のグループ クラスを作成する方法です。 **List&lt;T&gt;** からクラスを派生させます (ここで *T* は項目の型です)。 たとえば、`public class Author : List<BookSku>` と記述します。 もう 1 つは、**BookSku** 項目の同様のプロパティ値から、動的にグループ オブジェクト (とグループ クラス) を動的に作成する [LINQ](https://docs.microsoft.com/previous-versions/bb397926(v=vs.140)) 式を使う方法です。 このアプローチ (項目のフラットな一覧のみを保持し、必要に応じてグループ化する) は、クラウド サービスのデータにアクセスするアプリで一般的です。 著者やジャンルなどに基づいて書籍を柔軟にグループ化することができます。**Author** や **Genre** などの特別なグループ クラスは必要ありません。

次の例は、[LINQ](https://docs.microsoft.com/previous-versions/bb397926(v=vs.140)) を使用した "グループである" パターンを示しています。 今回は、書籍をジャンルでグループ化し、グループ ヘッダーにジャンル名と共に表示します。 これは、グループの [**Key**](https://docs.microsoft.com/dotnet/api/system.linq.igrouping-2.key?redirectedfrom=MSDN#System_Linq_IGrouping_2_Key) の値に関連する "Key" プロパティ パスによって示されます。

```csharp
using System.Linq;
...
private IOrderedEnumerable<IGrouping<string, BookSku>> genres;

public IOrderedEnumerable<IGrouping<string, BookSku>> Genres
{
    get
    {
        if (this.genres == null)
        {
            this.genres = from book in this.bookSkus
                          group book by book.genre into grp
                          orderby grp.Key
                          select grp;
        }
        return this.genres;
    }
}
```

[{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) をデータ テンプレートと共に使う場合、**x:DataType** 値を設定することによって、バインド先の型を指定する必要があることに注意してください。 型がジェネリックである場合、マークアップでは表現できないため、グループ スタイル ヘッダー テンプレート内で代わりに [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) を使う必要があります。

```xaml
    <Grid.Resources>
        <CollectionViewSource x:Name="GenreIsACollectionOfBookSku"
        Source="{x:Bind Genres}"
        IsSourceGrouped="true"/>
    </Grid.Resources>
    <GridView ItemsSource="{x:Bind GenreIsACollectionOfBookSku}">
        <GridView.ItemTemplate x:DataType="local:BookTemplate">
            <DataTemplate>
                <TextBlock Text="{x:Bind Title}"/>
            </DataTemplate>
        </GridView.ItemTemplate>
        <GridView.GroupStyle>
            <GroupStyle>
                <GroupStyle.HeaderTemplate>
                    <DataTemplate>
                        <TextBlock Text="{Binding Key}"/>
                    </DataTemplate>
                </GroupStyle.HeaderTemplate>
            </GroupStyle>
        </GridView.GroupStyle>
    </GridView>
```

[  **SemanticZoom**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Controls.SemanticZoom) コントロールは、ユーザーがグループ化されたデータを表示したり、データ間を移動したりするための優れた方法です。 [Bookstore2](https://go.microsoft.com/fwlink/?linkid=532952) サンプル アプリは、**SemanticZoom** の使い方を示しています。 このアプリでは、著者別にグループ化された書籍の一覧を表示する (拡大表示ビュー) ことも、縮小して著者のジャンプ リストを表示する (縮小表示ビュー) こともできます。 ジャンプ リストを使うと、書籍の一覧をスクロールするよりもすばやく移動することができます。 拡大表示ビューと縮小表示ビューは、実際には、同じ **CollectionViewSource** にバインドされた **ListView** または **GridView** コントロールです。

![SemanticZoom の図](images/sezo.png)

階層データ (カテゴリ内のサブカテゴリなど) にバインドする場合、一連の項目コントロールを使って、UI に階層レベルを表示できます。 1 つの項目コントロールで項目を選ぶと、後続する項目コントロールの内容に反映されます。 各リストをそれぞれの [**CollectionViewSource**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.CollectionViewSource) インスタンスにバインドし、**CollectionViewSource** インスタンスをチェーン形式でバインドすることで、これらのリストの同期を保つことができます。 これは、マスター/詳細 (またはリスト/詳細) ビューと呼ばれます。 詳しくは、「[階層データにバインドし、マスター/詳細ビューを作る方法](how-to-bind-to-hierarchical-data-and-create-a-master-details-view.md)」をご覧ください。

<span id="debugging"/>

## <a name="diagnosing-and-debugging-data-binding-problems"></a>データ バインディングに関する問題の診断とデバッグ

バインド マークアップには、プロパティ (と、C# では場合によってフィールドとメソッド) の名前が含まれています。 したがって、プロパティの名前を変更するときに、それを参照するすべてのバインディングを変更する必要があります。 この処理を忘れることは、データ バインディングのバグの一般的な例であり、アプリは正しくコンパイルまたは実行されません。

[{x:Bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension) と [{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) によって作成されたバインディング オブジェクトは、ほとんど機能的に同等です。 ただし、{x:Bind} にはバインディング ソースの型情報が含まれ、コンパイル時にソース コードが生成されます。 {x:Bind} を使うと、コードの残りの部分で取得できるものと同じ種類の問題の検出を行うことができます。 これには、コンパイル時のバインド式の検証や、ページの部分クラスとして生成されたソース コード内でのブレークポイント設定によるデバッグが含まれます。 これらのクラスは `obj` フォルダー内のファイルにあり、`<view name>.g.cs` (C# の場合) などの名前が付けられています。 バインディングに問題がある場合は、Microsoft Visual Studio デバッガーで、 **[処理されない例外で中断]** をオンにします。 デバッガーはその時点での実行を中断し、問題のある点をデバッグすることができます。 {x:Bind} によって生成されたコードは、バインディング ソース ノードのグラフの各部分で同じパターンに従うため、この情報を **[コール スタック]** ウィンドウで使って、問題の原因となった呼び出しのシーケンスを特定できます。

[{Binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension) には、バインディング ソースの型情報はありません。 ただし、デバッガーがアタッチされたアプリを実行すると、バインド エラーがある場合は Visual Studio の **[出力]** ウィンドウにそのエラーが表示されます。

## <a name="creating-bindings-in-code"></a>コードでのバインドの作成

**注**  このセクションにのみ該当[{binding}](https://docs.microsoft.com/windows/uwp/xaml-platform/binding-markup-extension)作成できないため、 [{X:bind}](https://docs.microsoft.com/windows/uwp/xaml-platform/x-bind-markup-extension)コードでバインドします。 ただし、{x:Bind} と同じメリットを、[**DependencyObject.RegisterPropertyChangedCallback**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.dependencyobject.registerpropertychangedcallback) によって実現できます。これにより、すべての依存関係プロパティについての変更通知を登録できます。

XAML の代わりに手続き型コードを使っても UI 要素をデータに接続できます。 そのためには、新しい [**Binding**](https://docs.microsoft.com/uwp/api/Windows.UI.Xaml.Data.Binding) オブジェクトを作成し、適切なプロパティを設定してから、[**FrameworkElement.SetBinding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.frameworkelement.setbinding) または [**BindingOperations.SetBinding**](https://docs.microsoft.com/uwp/api/windows.ui.xaml.data.bindingoperations.setbinding) を呼び出します。 バインドをプログラムで作成すると、Binding プロパティの値を実行時に選択する場合や、複数のコントロール間で 1 つのバインドを共有する場合に便利です。 ただし、**SetBinding** の呼び出し後は Binding プロパティの値を変更できないことに注意してください。

次の例では、バインドをコードで実装する方法を示しています。

```xaml
<TextBox x:Name="MyTextBox" Text="Text"/>
```

```csharp
// Create an instance of the MyColors class 
// that implements INotifyPropertyChanged.
MyColors textcolor = new MyColors();

// Brush1 is set to be a SolidColorBrush with the value Red.
textcolor.Brush1 = new SolidColorBrush(Colors.Red);

// Set the DataContext of the TextBox MyTextBox.
MyTextBox.DataContext = textcolor;

// Create the binding and associate it with the text box.
Binding binding = new Binding() { Path = new PropertyPath("Brush1") };
MyTextBox.SetBinding(TextBox.ForegroundProperty, binding);
```

```vbnet
' Create an instance of the MyColors class 
' that implements INotifyPropertyChanged. 
Dim textcolor As New MyColors()

' Brush1 is set to be a SolidColorBrush with the value Red. 
textcolor.Brush1 = New SolidColorBrush(Colors.Red)

' Set the DataContext of the TextBox MyTextBox. 
MyTextBox.DataContext = textcolor

' Create the binding and associate it with the text box.
Dim binding As New Binding() With {.Path = New PropertyPath("Brush1")}
MyTextBox.SetBinding(TextBox.ForegroundProperty, binding)
```

## <a name="xbind-and-binding-feature-comparison"></a>{x:Bind} と {Binding} の機能の比較

| 機能 | {x:Bind} | {Binding} | メモ |
|---------|----------|-----------|-------|
| Path が既定のプロパティである | `{x:Bind a.b.c}` | `{Binding a.b.c}` | | 
| Path プロパティ | `{x:Bind Path=a.b.c}` | `{Binding Path=a.b.c}` | x:Bind では、Path は既定で DataContext ではなく、Page をルートにします。 | 
| Indexer | `{x:Bind Groups[2].Title}` | `{Binding Groups[2].Title}` | コレクション内で指定した項目にバインドします。 整数ベースのインデックスのみがサポートされます。 | 
| 添付プロパティ | `{x:Bind Button22.(Grid.Row)}` | `{Binding Button22.(Grid.Row)}` | 添付プロパティは、かっこを使って指定します。 プロパティが XAML 名前空間で宣言されていない場合は、そのプロパティの前に xml 名前空間を付けます。これはドキュメントの先頭でコード名前空間にマップする必要があります。 | 
| キャスト | `{x:Bind groups[0].(data:SampleDataGroup.Title)}` | 必要ありません。 | キャストはかっこを使って指定します。 プロパティが XAML 名前空間で宣言されていない場合は、そのプロパティの前に xml 名前空間を付けます。これはドキュメントの先頭でコード名前空間にマップする必要があります。 | 
| Converter | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}}` | コンバーターは、Page/ResourceDictionary のルートまたは App.xaml で宣言する必要があります。 | 
| ConverterParameter、ConverterLanguage | `{x:Bind IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | `{Binding IsShown, Converter={StaticResource BoolToVisibility}, ConverterParameter=One, ConverterLanguage=fr-fr}` | コンバーターは、Page/ResourceDictionary のルートまたは App.xaml で宣言する必要があります。 | 
| TargetNullValue | `{x:Bind Name, TargetNullValue=0}` | `{Binding Name, TargetNullValue=0}` | バインド式のリーフが null の場合に使用されます。 文字列値の場合は単一引用符を使用します。 | 
| FallbackValue | `{x:Bind Name, FallbackValue='empty'}` | `{Binding Name, FallbackValue='empty'}` | バインディングのパスの一部 (リーフを除く) が null の場合に使用されます。 | 
| ElementName | `{x:Bind slider1.Value}` | `{Binding Value, ElementName=slider1}` | {x:Bind} によって、フィールドにバインドしています。Path は既定で Page をルートとするため、名前付きの要素はそのフィールドを使ってアクセスできます。 | 
| RelativeSource:Self (自己) | `<Rectangle x:Name="rect1" Width="200" Height="{x:Bind rect1.Width}" ... />` | `<Rectangle Width="200" Height="{Binding Width, RelativeSource={RelativeSource Self}}" ... />` | {x:Bind} では、要素に名前を付けて、Path でその名前を使います。 | 
| RelativeSource:TemplatedParent | 不要 | `{Binding <path>, RelativeSource={RelativeSource TemplatedParent}}` | {X:bind} TargetType ControlTemplate にした場合は、親テンプレートにバインドすることを示します。 {バインド} たいていの通常のテンプレート バインディング コントロール テンプレートで使用できます。 ただし、コンバーターまたは双方向バインディングを使用する必要がある場合は TemplatedParent を使います。< | 
| Source | 不要 | `<ListView ItemsSource="{Binding Orders, Source={StaticResource MyData}}"/>` | {X:bind} の名前付きの要素を直接に使用できる、プロパティまたは静的のパスを使用します。 | 
| Mode | `{x:Bind Name, Mode=OneWay}` | `{Binding Name, Mode=TwoWay}` | Mode には、OneTime、OneWay、TwoWay を指定できます。 {x:Bind} の既定値は OneTime で、{Binding} の既定値は OneWay です。 | 
| UpdateSourceTrigger | `{x:Bind Name, Mode=TwoWay, UpdateSourceTrigger=PropertyChanged}` | `{Binding UpdateSourceTrigger=PropertyChanged}` | UpdateSourceTrigger には、Default、LostFocus、PropertyChanged を指定できます。 {x:Bind} では、UpdateSourceTrigger=Explicit はサポートされません。 {x:Bind} では、TextBox.Text を除くすべての場合に PropertyChanged 動作を使います。TextBox.Text の場合は LostFocus 動作を使います。 | 


