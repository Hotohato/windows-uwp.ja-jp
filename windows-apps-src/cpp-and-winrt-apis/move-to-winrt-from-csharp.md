---
description: このトピックでは、[C#](/visualstudio/get-started/csharp) プロジェクト内のソース コードを [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) の同等のコードに移植することに関する技術的な詳細を取り上げています。
title: C# から C++/WinRT への移行
ms.date: 07/15/2019
ms.topic: article
keywords: windows 10, uwp, 標準, c++, cpp, winrt, プロジェクション, 移植, 移行, C#
ms.localizationpriority: medium
ms.openlocfilehash: f7cd35dbf211b14dfb886fc9ba4305cd7ce56e5e
ms.sourcegitcommit: f288bcc108f9850671662c7b76c55c8313e88b42
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/26/2020
ms.locfileid: "80290054"
---
# <a name="move-to-cwinrt-from-c"></a>C# から C++/WinRT への移行

このトピックでは、[C#](/visualstudio/get-started/csharp) プロジェクト内のソース コードを [C++/WinRT](/windows/uwp/cpp-and-winrt-apis/intro-to-using-cpp-with-winrt) の同等のコードに移植することに関する技術的な詳細を取り上げています。

## <a name="register-an-event-handler"></a>イベント ハンドラーの登録

XAML マークアップでイベント ハンドラーを登録することができます。

```xaml
<Button x:Name="OpenButton" Click="OpenButton_Click" />
```

C# では、**OpenButton_Click** メソッドはプライベートにすることができ、XAML は引き続きこれを、*OpenButton* によって発生した [**ButtonBase.Click**](/uwp/api/windows.ui.xaml.controls.primitives.buttonbase.click) イベントに接続できます。

C++/WinRT では、**OpenButton_Click** メソッドは[実装型](/windows/uwp/cpp-and-winrt-apis/author-apis)でパブリックである必要があります。

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

または、登録するクラスを実装クラスの friend にし、**OpenButton_Click** をプライベートにすることもできます。

```cppwinrt
namespace winrt::MyProject::implementation
{
    struct MyPage : MyPageT<MyPage>
    {
    private:
        friend MyPageT;
        void OpenButton_Click(
            winrt::Windows:Foundation::IInspectable const& sender,
            winrt::Windows::UI::Xaml::RoutedEventArgs const& args);
    }
};
```

## <a name="making-a-class-available-to-the-binding-markup-extension"></a>クラスを {Binding} マークアップ拡張に使用できるようにする

{Binding} マークアップ拡張を使用してデータ型にデータ バインドする場合は、「[{Binding} を使って宣言されたバインディング オブジェクト](/windows/uwp/data-binding/data-binding-in-depth#binding-object-declared-using-binding)」を参照してください。

## <a name="making-a-data-source-available-to-xaml-markup"></a>データ ソースを XAML マークアップで使用できるようにする

C++/WinRT バージョン 2.0.190530.8 以降では、[**winrt::single_threaded_observable_vector**](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector) により、 **[IObservableVector](/uwp/api/windows.foundation.collections.iobservablevector_t_)\<T\>** と **IObservableVector\<IInspectable\>** の両方をサポートする監視可能なベクターが作成されます。

次のような **Midl ファイル (.idl)** を作成できます (「[ランタイム クラスを Midl ファイル (.idl) にファクタリングする](/windows/uwp/cpp-and-winrt-apis/author-apis#factoring-runtime-classes-into-midl-files-idl)」も参照してください)。

```idl
namespace Bookstore
{
    runtimeclass BookSku { ... }

    runtimeclass BookstoreViewModel
    {
        Windows.Foundation.Collections.IObservableVector<BookSku> BookSkus{ get; };
    }

    runtimeclass MainPage : Windows.UI.Xaml.Controls.Page
    {
        MainPage();
        BookstoreViewModel MainViewModel{ get; };
    }
}
```

さらに、次のように実装できます。

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Bookstore::BookSku>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Bookstore::BookSku> m_bookSkus;
};
...
```

詳細については、「[XAML アイテム コントロール: C++/WinRT コレクションへのバインド](/windows/uwp/cpp-and-winrt-apis/binding-collection)」を参照してください。

## <a name="making-a-data-source-available-to-xaml-markup-prior-to-cwinrt-201905308"></a>データ ソースを XAML マークアップで使用できるようにする (C++/WinRT 2.0.190530.8 より前)

XAML データ バインディングでは、項目ソースが **[IIterable](/uwp/api/windows.foundation.collections.iiterable_t_)\<IInspectable\>** と、次のインターフェイスの組み合わせのいずれかを実装する必要があります。

- **IObservableVector\<IInspectable\>**
- **IBindableVector** および **INotifyCollectionChanged**
- **IBindableVector** および **IBindableObservableVector**
- **IBindableVector** 単独 (変更には対応しません)
- **Ivector\<IInspectable\>**
- **IBindableIterable** (要素を反復処理してプライベート コレクションに保存します)

**IVector\<T\>** などのジェネリック インターフェイスは、実行時に検出できません。 各 **IVector\<\>T** には別のインターフェイス識別子 (IID) があり、これが **T** の関数です。すべての開発者は **T** のセットを自由に拡張できるため、XAML バインディングのコードではクエリを実行する完全なセットを明確に理解することができません。 この制約は C# では問題ではありません。**IEnumerable\<T\>** を実装するすべての CLR オブジェクトは **IEnumerable** を自動的に実装するためです。 これは、ABI レベルでは、**IObservableVector\<T\>** を実装するすべてのオブジェクトが **IObservableVector\<IInspectable\>** を自動的に実装することを意味します。

C++/WinRT ではその保証は提供されていません。 C++/WinRT ランタイム クラスが **IObservableVector\<T\>** を実装する場合は、**IObservableVector\<IInspectable\>** の実装も何らかの形で提供されるとは想定できません。

そのため、前の例では、次のように確認する必要があります。

```idl
...
runtimeclass BookstoreViewModel
{
    // This is really an observable vector of BookSku.
    Windows.Foundation.Collections.IObservableVector<Object> BookSkus{ get; };
}
```

実装は次のとおりです。

```cppwinrt
// BookstoreViewModel.h
...
struct BookstoreViewModel : BookstoreViewModelT<BookstoreViewModel>
{
    BookstoreViewModel()
    {
        m_bookSkus = winrt::single_threaded_observable_vector<Windows::Foundation::IInspectable>();
        m_bookSkus.Append(winrt::make<Bookstore::implementation::BookSku>(L"To Kill A Mockingbird"));
    }
    
    // This is really an observable vector of BookSku.
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> BookSkus();
    {
        return m_bookSkus;
    }

private:
    Windows::Foundation::Collections::IObservableVector<Windows::Foundation::IInspectable> m_bookSkus;
};
...
```

*m_bookSkus* 内のオブジェクトにアクセスする必要がある場合は、再度 **Bookstore::BookSku** に QI する必要があります。

```cppwinrt
Widget MyPage::BookstoreViewModel(winrt::hstring title)
{
    for (auto&& obj : m_bookSkus)
    {
        auto bookSku = obj.as<Bookstore::BookSku>();
        if (bookSku.Title() == title) return bookSku;
    }
    return nullptr;
}
```

## <a name="tostring"></a>ToString()

C# 型には [Object.ToString](/dotnet/api/system.object.tostring) メソッドが用意されています。

```csharp
int i = 2;
var s = i.ToString(); // s is a System.String with value "2".
```

C++/WinRT ではこの機能は直接提供されていませんが、代替機能があります。

```cppwinrt
int i{ 2 };
auto s{ std::to_wstring(i) }; // s is a std::wstring with value L"2".
```

また、C++/WinRT では、限られた数の型のための [**winrt:: to_hstring**](/uwp/cpp-ref-for-winrt/to-hstring) もサポートしています。 stringify が必要なその他の型のオーバーロードを追加する必要があります。

| Language | Stringify int | Stringify enum |
| - | - | - |
| C# | `string result = "hello, " + intValue.ToString();`<br>`string result = $"hello, {intValue}";` | `string result = "status: " + status.ToString();`<br>`string result = $"status: {status}";` |
| C++/WinRT | `hstring result = L"hello, " + to_hstring(intValue);` | `// must define overload (see below)`<br>`hstring result = L"status: " + to_hstring(status);` |

列挙型の stringify の場合は、**winrt::to_hstring** の実装を指定する必要があります。

```cppwinrt
namespace winrt
{
    hstring to_hstring(StatusEnum status)
    {
        switch (status)
        {
        case StatusEnum::Success: return L"Success";
        case StatusEnum::AccessDenied: return L"AccessDenied";
        case StatusEnum::DisabledByPolicy: return L"DisabledByPolicy";
        default: return to_hstring(static_cast<int>(status));
        }
    }
}
```

このような文字列化は、多くの場合、データ バインディングによって暗黙的に使用されます。

```xaml
<TextBlock>
You have <Run Text="{Binding FlowerCount}"/> flowers.
</TextBlock>
<TextBlock>
Most recent status is <Run Text="{x:Bind LatestOperation.Status}"/>.
</TextBlock>
```

これらのバインディングは、バインドされたプロパティの **winrt::to_hstring** を実行します。 2 番目の例 **(statusenum**) の場合は、**winrt::to_hstring** の独自のオーバーロードを指定する必要があります。そうしないと、コンパイラ エラーが発生します。

## <a name="string-building"></a>文字列の作成

C# には、文字列の作成用に組み込みの [**StringBuilder**](/dotnet/api/system.text.stringbuilder) 型があります。

| | C# | C++/WinRT |
|-|-|-|
| ビルダー クラス | `StringBuilder builder;` | `std::wstringstream stream;` |
| 文字列を追加し、null 値を保持する | `builder.Append(s);` | `stream << std::wstring_view{ s };` |
| 結果を抽出する | `s = builder.ToString();` | `ws = stream.str();` |

## <a name="boxing-and-unboxing"></a>ボックス化とボックス化解除

C++ では、スカラーが自動的にオブジェクト内にボックス化されます。 C++/Winrt では、[**winrt::box_value**](/uwp/cpp-ref-for-winrt/box-value) 関数を明示的に呼び出す必要があります。 どちらの言語でも、明示的にボックス化解除する必要があります。 [C++/WinRT を使用したボックス化とボックス化解除](/windows/uwp/cpp-and-winrt-apis/boxing)に関するページを参照してください。

次の表では、これらの定義を使用します。

| C# | C++/WinRT|
|-|-|
| `int i;` | `int i;` |
| `string s;` | `winrt::hstring s;` |
| `object o;` | `IInspectable o;`|

| 操作 | C# | C++/WinRT|
|-|-|-|
| ボックス化 | `o = 1;`<br>`o = "string";` | `o = box_value(1);`<br>`o = box_value(L"string");` |
| ボックス化解除 | `i = (int)o;`<br>`s = (string)o;` | `i = unbox_value<int>(o);`<br>`s = unbox_value<winrt::hstring>(o);` |

C++/CX と C# では、Null ポインターを値型にボックス化解除しようとすると例外が発生します。 C++/WinRT はこれをプログラミング エラーと見なし、クラッシュします。 C++/Winrt では、オブジェクトが想定した型ではないケースに対処したい場合は [**winrt:: unbox_value_or**](/uwp/cpp-ref-for-winrt/unbox-value-or) 関数を使用します。

| シナリオ | C# | C++/WinRT|
|-|-|-|
| 既知の整数をボックス化解除する |`i = (int)o;` | `i = unbox_value<int>(o);` |
| o が null の場合 | `System.NullReferenceException` | クラッシュ |
| o がボックス化された int でない場合 | `System.InvalidCastException` | クラッシュ |
| int をボックス化解除し、null の場合はフォールバックを使用する。それ以外の場合はクラッシュ | `i = o != null ? (int)o : fallback;` | `i = o ? unbox_value<int>(o) : fallback;` |
| 可能であれば int をボックス化解除する。それ以外の場合はフォールバックを使用する | `i = as int? ?? fallback;` | `i = unbox_value_or<int>(o, fallback);` |

### <a name="boxing-and-unboxing-a-string"></a>文字列のボックス化とボックス化解除

文字列は、ある点では値型であり、別の点では参照型です。 C# と C++/WinRT では文字列の扱いが異なります。

ABI 型 [**HSTRING**](/windows/win32/winrt/hstring) は、参照カウント文字列へのポインターです。 しかし、[**IInspectable**](/windows/win32/api/inspectable/nn-inspectable-iinspectable) から派生したものではないため、厳密に言えば*オブジェクト*ではありません。 さらに、null の **HSTRING** は空の文字列を表します。 **IInspectable** から派生したもの以外のボックス化は、[**IReference \<T\>** ](/uwp/api/windows.foundation.ireference_t_) 内にラップすることによって行われ、Windows ランタイムでは標準の実装が [**PropertyValue**](/uwp/api/windows.foundation.propertyvalue) オブジェクトの形式で行われます (カスタム型は [**PropertyType::Othertype**](/uwp/api/windows.foundation.propertytype) として報告されます)。

C# は Windows ランタイム文字列を参照型として表しますが、C++/WinRT は文字列を値型として投影します。 つまり、ボックス化された null 文字列は、どのように表されるかによって異なる表現を持つことができます。

| 動作 | C# | C++/WinRT|
|-|-|-|
| 宣言 | `object o;`<br>`string s;` | `IInspectable o;`<br>`hstring s;` |
| 文字列型のカテゴリ | 参照型 | 値の種類 |
| null **HSTRING** による投影 | `""` | `hstring{}` |
| null と `""` が同一かどうか | いいえ | はい |
| null の有効性 | `s = null;`<br>`s.Length` により NullReferenceException が生成される | `s = hstring{};`<br>`s.size() == 0` (有効) |
| オブジェクトに null 文字列を割り当てる場合 | `o = (string)null;`<br>`o == null` | `o = box_value(hstring{});`<br>`o != nullptr` |
| オブジェクトに `""` を割り当てる場合 | `o = "";`<br>`o != null` | `o = box_value(hstring{L""});`<br>`o != nullptr` |

基本的なボックス化とボックス化解除。

| 操作 | C# | C++/WinRT|
|-|-|-|
| 文字列をボックス化する | `o = s;`<br>空の文字列は非 null オブジェクトになります。 | `o = box_value(s);`<br>空の文字列は非 null オブジェクトになります。 |
| 既知の文字列をボックス化解除する | `s = (string)o;`<br>null オブジェクトは null 文字列になります。<br>文字列でない場合は InvalidCastException。 | `s = unbox_value<hstring>(o);`<br>null オブジェクトはクラッシュします。<br>文字列でない場合はクラッシュします。 |
| 使用可能な文字列をボックス化解除する | `s = o as string;`<br>null オブジェクトまたは文字列以外は null 文字列になります。<br><br>または<br><br>`s = o as string ?? fallback;`<br>null または文字列以外はフォールバックになります。<br>空の文字列は保持されます。 | `s = unbox_value_or<hstring>(o, fallback);`<br>null または文字列以外はフォールバックになります。<br>空の文字列は保持されます。 |

## <a name="derived-classes"></a>派生クラス

ランタイム クラスから派生するためには、基底クラスが*構成可能*である必要があります。 C# ではクラスを構成可能にするために特別な手順を実行する必要はありませんが、C++/WinRT では必要です。 クラスを基底クラスとして使用できるようにすることを示すには、[シールされていないキーワード](/uwp/midl-3/intro#base-classes)を使用します。

```idl
unsealed runtimeclass BasePage : Windows.UI.Xaml.Controls.Page
{
    ...
}
runtimeclass DerivedPage : BasePage
{
    ...
}
```

実装ヘッダー クラスでは、派生クラスの自動生成されたヘッダーをインクルードする前に、基底クラスのヘッダー ファイルをインクルードする必要があります。 そうしないと、"この型は式として不適切に使用されています" などのエラーが表示されます。

```cppwinrt
// DerivedPage.h
#include "BasePage.h"       // This comes first.
#include "DerivedPage.g.h"  // Otherwise this header file will produce an error.

namespace winrt::MyNamespace::implementation
{
    struct DerivedPage : DerivedPageT<DerivedPage>
    {
        ...
    }
}
```

## <a name="consuming-objects-from-xaml-markup"></a>XAML マークアップからのオブジェクトの使用

C# プロジェクトでは、XAML マークアップからプライベート メンバーと名前付き要素を使用できます。 ただし、C++/WinRT では、XAML [ **{x:Bind} マークアップ拡張**](/windows/uwp/xaml-platform/x-bind-markup-extension)の使用によって使用されるすべてのエンティティが、IDL で公開されている必要があります。

また、ブール値へのバインドにより C# では `true` または `false` が表示されますが、C++/WinRT では **Windows.Foundation.IReference`1\<Boolean\>** が表示されます。

詳細とコード例については、[マークアップからのオブジェクトの使用](/windows/uwp/cpp-and-winrt-apis/binding-property#consuming-objects-from-xaml-markup)に関するページを参照してください。

## <a name="important-apis"></a>重要な API
* [winrt::single_threaded_observable_vector 関数テンプレート](/uwp/cpp-ref-for-winrt/single-threaded-observable-vector)
* [winrt 名前空間](/uwp/cpp-ref-for-winrt/winrt)

## <a name="related-topics"></a>関連トピック
* [C# チュートリアル](/visualstudio/get-started/csharp)
* [C++/WinRT で API を作成する](/windows/uwp/cpp-and-winrt-apis/author-apis)
* [データ バインディングの詳細](/windows/uwp/data-binding/data-binding-in-depth)
* [XAML アイテム コントロール: C++/WinRT コレクションへのバインド](/windows/uwp/cpp-and-winrt-apis/binding-collection)