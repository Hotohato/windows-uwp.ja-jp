---
title: 画面の向きのサポート (DirectX と C++)
description: ここでは、Windows 10 デバイスのグラフィックス ハードウェアは効率的かつ効果的に使用されるようには、UWP の DirectX アプリで画面の回転を処理するためのベスト プラクティスについて説明します。
ms.assetid: f23818a6-e372-735d-912b-89cabeddb6d4
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, ゲーム, 画面の向き, DirectX
ms.localizationpriority: medium
ms.openlocfilehash: 84dc81734d945e32d222bdc3e1fe9c7468f078bb
ms.sourcegitcommit: 6f32604876ed480e8238c86101366a8d106c7d4e
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 06/21/2019
ms.locfileid: "67321154"
---
# <a name="supporting-screen-orientation-directx-and-c"></a>画面の向きのサポート (DirectX と C++)



ユニバーサル Windows プラットフォーム (UWP) アプリでは、[**DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) イベントを処理するとき、複数の画面の向きをサポートできます。 ここでは、Windows 10 デバイスのグラフィックス ハードウェアは効率的かつ効果的に使用されるようには、UWP の DirectX アプリで画面の回転を処理するためのベスト プラクティスについて説明します。

始める前に、グラフィックス ハードウェアは、デバイスの向きにかかわらず、常に同じ方法でピクセル データを出力するということを覚えておいてください。 Windows 10 デバイスでは、(なんらかのセンサー、またはソフトウェアの切り替えアイテム付き)、現在の画面の向きを決定でき、表示設定を変更できるようにすることができます。 このため、自体の Windows 10 デバイスの向きに基づいてそれらは「垂直」ことを確認するイメージの回転を処理します。 既定では、アプリは何か (たとえば、ウィンドウ サイズ) の方向が変化したという通知を受け取ります。 この場合、Windows 10 はすぐに最終的な表示の画像を回転します。 3 (後述) 4 つの特定の画面の向きは、Windows 10 は、最終的なイメージを表示するのにその他のグラフィック リソースと計算を使用します。

UWP DirectX アプリでは、アプリが照会できる基本的な表示の向きのデータを [**DisplayInformation**](https://docs.microsoft.com/uwp/api/Windows.Graphics.Display.DisplayInformation) オブジェクトが提供します。 既定の向きは*横*で、表示のピクセルの幅が高さよりも大きくなります。もう 1 つの向きは*縦*で、表示はどちらかの方向に 90° 回転され、幅は高さより小さくなります。

Windows 10 では、特定のディスプレイの向きの 4 つのモードを定義します。

-   ランドス ケープ-既定値は、Windows 10 での方向を表示して、ベースまたは id (0 度) の回転角度と見なされます。
-   縦: 表示は時計回りに 90° (または反時計回りに 270°) 回転します。
-   横、反転: 表示は 180° 回転されます (上下が逆になります)。
-   縦、反転: 表示は時計回りに 270° (または反時計回りに 90°) 回転します。

表示は、別の 1 つの向きから回転と、新しい方向に描画するイメージを配置する回転の操作を内部で実行する Windows 10 ユーザーには、画面に垂直のイメージが表示されます。

また、Windows 10 では、1 つの方向からの脱却ときに、スムーズなユーザー エクスペリエンスを作成する自動遷移のアニメーションが表示されます。 表示の向きが変わる際に、ユーザーにはその変化が、表示されている画面の画像の固定拡大と回転アニメーションとして表示されます。 時間は、新しい方向にレイアウトするようにアプリケーションを Windows 10 によって割り当てられます。

画面の向きの変更を処理する一般的なプロセスは、ほぼ次のようになります。

1.  ウィンドウの境界値と表示の向きのデータの組み合わせを使って、デバイスの本来の表示の向きに合わせたスワップ チェーンを維持します。
2.  向きを使用してスワップ チェーンの Windows 10 への通知[ **IDXGISwapChain1::SetRotation**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-setrotation)します。
3.  レンダリング コードを変更して、デバイスのユーザーによる向きに合わせた画像を生成します。

## <a name="resizing-the-swap-chain-and-pre-rotating-its-contents"></a>スワップ チェーンのサイズ変更とその内容の事前回転


UWP DirectX アプリで基本的な表示サイズ変更とその内容の事前回転を行うには、次の手順を実装します。

1.  [  **DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) イベントを処理します。
2.  ウィンドウの新しいサイズにスワップ チェーンをサイズ変更します。
3.  [  **IDXGISwapChain1::SetRotation**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-setrotation) を呼び出して、スワップ チェーンの向きを設定します。
4.  ウィンドウ サイズに依存するすべてのリソース (レンダー ターゲット、その他のピクセル データ バッファーなど) を再作成します。

それでは、これらの手順をもう少し詳しく見てみましょう。

最初の手順は、[**DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) イベントのハンドラーを登録することです。 このイベントは、アプリで画面の向きが変更されるたびに発生します (表示が回転されたときなど)。

[  **DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) イベントを処理するには、必須の [**SetWindow**](https://docs.microsoft.com/uwp/api/windows.applicationmodel.core.iframeworkview.setwindow) メソッドで **DisplayInformation::OrientationChanged** のハンドラーを接続します。このメソッドは、ビュー プロバイダーが実装しなければならない [**IFrameworkView**](https://docs.microsoft.com/uwp/api/Windows.ApplicationModel.Core.IFrameworkView) インターフェイスのメソッドのうちの 1 つです。

このコード例では、[**DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) のイベント ハンドラーは、**OnOrientationChanged** というメソッドです。 **DisplayInformation::OrientationChanged** が発生すると、**SetCurrentOrientation** というメソッドを呼び出し、このメソッドが **CreateWindowSizeDependentResources** を呼び出します。

```cpp
void App::SetWindow(CoreWindow^ window)
{
  // ... Other UI event handlers assigned here ...
  
    currentDisplayInformation->OrientationChanged +=
        ref new TypedEventHandler<DisplayInformation^, Object^>(this, &App::OnOrientationChanged);

  // ...
}
}
```

```cpp
void App::OnOrientationChanged(DisplayInformation^ sender, Object^ args)
{
    m_deviceResources->SetCurrentOrientation(sender->CurrentOrientation);
    m_main->CreateWindowSizeDependentResources();
}

// This method is called in the event handler for the OrientationChanged event.
void DX::DeviceResources::SetCurrentOrientation(DisplayOrientations currentOrientation)
{
    if (m_currentOrientation != currentOrientation)
    {
        m_currentOrientation = currentOrientation;
        CreateWindowSizeDependentResources();
    }
}
```

次に、スワップ チェーンを新しい画面の向きにサイズ変更し、レンダリングが行われるときにグラフィックス パイプラインの内容を回転させる準備をします。 この例では、**DirectXBase::CreateWindowSizeDependentResources** は、IDXGISwapChain::ResizeBuffers の呼び出し、3D および 2D 回転マトリックスの設定、SetRotation の呼び出し、リソースの再作成を処理するメソッドです。

```cpp
void DX::DeviceResources::CreateWindowSizeDependentResources() 
{
    // Clear the previous window size specific context.
    ID3D11RenderTargetView* nullViews[] = {nullptr};
    m_d3dContext->OMSetRenderTargets(ARRAYSIZE(nullViews), nullViews, nullptr);
    m_d3dRenderTargetView = nullptr;
    m_d2dContext->SetTarget(nullptr);
    m_d2dTargetBitmap = nullptr;
    m_d3dDepthStencilView = nullptr;
    m_d3dContext->Flush();

    // Calculate the necessary render target size in pixels.
    m_outputSize.Width = DX::ConvertDipsToPixels(m_logicalSize.Width, m_dpi);
    m_outputSize.Height = DX::ConvertDipsToPixels(m_logicalSize.Height, m_dpi);
    
    // Prevent zero size DirectX content from being created.
    m_outputSize.Width = max(m_outputSize.Width, 1);
    m_outputSize.Height = max(m_outputSize.Height, 1);

    // The width and height of the swap chain must be based on the window's
    // natively-oriented width and height. If the window is not in the native
    // orientation, the dimensions must be reversed.
    DXGI_MODE_ROTATION displayRotation = ComputeDisplayRotation();

    bool swapDimensions = displayRotation == DXGI_MODE_ROTATION_ROTATE90 || displayRotation == DXGI_MODE_ROTATION_ROTATE270;
    m_d3dRenderTargetSize.Width = swapDimensions ? m_outputSize.Height : m_outputSize.Width;
    m_d3dRenderTargetSize.Height = swapDimensions ? m_outputSize.Width : m_outputSize.Height;

    if (m_swapChain != nullptr)
    {
        // If the swap chain already exists, resize it.
        HRESULT hr = m_swapChain->ResizeBuffers(
            2, // Double-buffered swap chain.
            lround(m_d3dRenderTargetSize.Width),
            lround(m_d3dRenderTargetSize.Height),
            DXGI_FORMAT_B8G8R8A8_UNORM,
            0
            );

        if (hr == DXGI_ERROR_DEVICE_REMOVED || hr == DXGI_ERROR_DEVICE_RESET)
        {
            // If the device was removed for any reason, a new device and swap chain will need to be created.
            HandleDeviceLost();

            // Everything is set up now. Do not continue execution of this method. HandleDeviceLost will reenter this method 
            // and correctly set up the new device.
            return;
        }
        else
        {
            DX::ThrowIfFailed(hr);
        }
    }
    else
    {
        // Otherwise, create a new one using the same adapter as the existing Direct3D device.
        DXGI_SWAP_CHAIN_DESC1 swapChainDesc = {0};

        swapChainDesc.Width = lround(m_d3dRenderTargetSize.Width); // Match the size of the window.
        swapChainDesc.Height = lround(m_d3dRenderTargetSize.Height);
        swapChainDesc.Format = DXGI_FORMAT_B8G8R8A8_UNORM; // This is the most common swap chain format.
        swapChainDesc.Stereo = false;
        swapChainDesc.SampleDesc.Count = 1; // Don't use multi-sampling.
        swapChainDesc.SampleDesc.Quality = 0;
        swapChainDesc.BufferUsage = DXGI_USAGE_RENDER_TARGET_OUTPUT;
        swapChainDesc.BufferCount = 2; // Use double-buffering to minimize latency.
        swapChainDesc.SwapEffect = DXGI_SWAP_EFFECT_FLIP_SEQUENTIAL; // All UWP apps must use this SwapEffect.
        swapChainDesc.Flags = 0;    
        swapChainDesc.Scaling = DXGI_SCALING_NONE;
        swapChainDesc.AlphaMode = DXGI_ALPHA_MODE_IGNORE;

        // This sequence obtains the DXGI factory that was used to create the Direct3D device above.
        ComPtr<IDXGIDevice3> dxgiDevice;
        DX::ThrowIfFailed(
            m_d3dDevice.As(&dxgiDevice)
            );

        ComPtr<IDXGIAdapter> dxgiAdapter;
        DX::ThrowIfFailed(
            dxgiDevice->GetAdapter(&dxgiAdapter)
            );

        ComPtr<IDXGIFactory2> dxgiFactory;
        DX::ThrowIfFailed(
            dxgiAdapter->GetParent(IID_PPV_ARGS(&dxgiFactory))
            );

        DX::ThrowIfFailed(
            dxgiFactory->CreateSwapChainForCoreWindow(
                m_d3dDevice.Get(),
                reinterpret_cast<IUnknown*>(m_window.Get()),
                &swapChainDesc,
                nullptr,
                &m_swapChain
                )
            );

        // Ensure that DXGI does not queue more than one frame at a time. This both reduces latency and
        // ensures that the application will only render after each VSync, minimizing power consumption.
        DX::ThrowIfFailed(
            dxgiDevice->SetMaximumFrameLatency(1)
            );
    }

    // Set the proper orientation for the swap chain, and generate 2D and
    // 3D matrix transformations for rendering to the rotated swap chain.
    // Note the rotation angle for the 2D and 3D transforms are different.
    // This is due to the difference in coordinate spaces.  Additionally,
    // the 3D matrix is specified explicitly to avoid rounding errors.

    switch (displayRotation)
    {
    case DXGI_MODE_ROTATION_IDENTITY:
        m_orientationTransform2D = Matrix3x2F::Identity();
        m_orientationTransform3D = ScreenRotation::Rotation0;
        break;

    case DXGI_MODE_ROTATION_ROTATE90:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(90.0f) *
            Matrix3x2F::Translation(m_logicalSize.Height, 0.0f);
        m_orientationTransform3D = ScreenRotation::Rotation270;
        break;

    case DXGI_MODE_ROTATION_ROTATE180:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(180.0f) *
            Matrix3x2F::Translation(m_logicalSize.Width, m_logicalSize.Height);
        m_orientationTransform3D = ScreenRotation::Rotation180;
        break;

    case DXGI_MODE_ROTATION_ROTATE270:
        m_orientationTransform2D = 
            Matrix3x2F::Rotation(270.0f) *
            Matrix3x2F::Translation(0.0f, m_logicalSize.Width);
        m_orientationTransform3D = ScreenRotation::Rotation90;
        break;

    default:
        throw ref new FailureException();
    }


    //SDM: only instance of SetRotation
    DX::ThrowIfFailed(
        m_swapChain->SetRotation(displayRotation)
        );

    // Create a render target view of the swap chain back buffer.
    ComPtr<ID3D11Texture2D> backBuffer;
    DX::ThrowIfFailed(
        m_swapChain->GetBuffer(0, IID_PPV_ARGS(&backBuffer))
        );

    DX::ThrowIfFailed(
        m_d3dDevice->CreateRenderTargetView(
            backBuffer.Get(),
            nullptr,
            &m_d3dRenderTargetView
            )
        );

    // Create a depth stencil view for use with 3D rendering if needed.
    CD3D11_TEXTURE2D_DESC depthStencilDesc(
        DXGI_FORMAT_D24_UNORM_S8_UINT, 
        lround(m_d3dRenderTargetSize.Width),
        lround(m_d3dRenderTargetSize.Height),
        1, // This depth stencil view has only one texture.
        1, // Use a single mipmap level.
        D3D11_BIND_DEPTH_STENCIL
        );

    ComPtr<ID3D11Texture2D> depthStencil;
    DX::ThrowIfFailed(
        m_d3dDevice->CreateTexture2D(
            &depthStencilDesc,
            nullptr,
            &depthStencil
            )
        );

    CD3D11_DEPTH_STENCIL_VIEW_DESC depthStencilViewDesc(D3D11_DSV_DIMENSION_TEXTURE2D);
    DX::ThrowIfFailed(
        m_d3dDevice->CreateDepthStencilView(
            depthStencil.Get(),
            &depthStencilViewDesc,
            &m_d3dDepthStencilView
            )
        );
    
    // Set the 3D rendering viewport to target the entire window.
    m_screenViewport = CD3D11_VIEWPORT(
        0.0f,
        0.0f,
        m_d3dRenderTargetSize.Width,
        m_d3dRenderTargetSize.Height
        );

    m_d3dContext->RSSetViewports(1, &m_screenViewport);

    // Create a Direct2D target bitmap associated with the
    // swap chain back buffer and set it as the current target.
    D2D1_BITMAP_PROPERTIES1 bitmapProperties = 
        D2D1::BitmapProperties1(
            D2D1_BITMAP_OPTIONS_TARGET | D2D1_BITMAP_OPTIONS_CANNOT_DRAW,
            D2D1::PixelFormat(DXGI_FORMAT_B8G8R8A8_UNORM, D2D1_ALPHA_MODE_PREMULTIPLIED),
            m_dpi,
            m_dpi
            );

    ComPtr<IDXGISurface2> dxgiBackBuffer;
    DX::ThrowIfFailed(
        m_swapChain->GetBuffer(0, IID_PPV_ARGS(&dxgiBackBuffer))
        );

    DX::ThrowIfFailed(
        m_d2dContext->CreateBitmapFromDxgiSurface(
            dxgiBackBuffer.Get(),
            &bitmapProperties,
            &m_d2dTargetBitmap
            )
        );

    m_d2dContext->SetTarget(m_d2dTargetBitmap.Get());

    // Grayscale text anti-aliasing is recommended for all UWP apps.
    m_d2dContext->SetTextAntialiasMode(D2D1_TEXT_ANTIALIAS_MODE_GRAYSCALE);

}

```

このメソッドを次回に呼び出すときのためにウィンドウの現在の高さと幅の値を保存した後で、表示の境界の DIP (デバイスに依存しないピクセル) 値をピクセルに変換します。 例では、**ConvertDipsToPixels** を呼び出しています。これは、次のコードを実行する単純な関数です。

` floor((dips * dpi / 96.0f) + 0.5f);`

0\.5f を追加しているのは、最も近い整数に丸めるためです。

なお、[**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) の座標は、常に DIP で定義されます。 1/96 インチでの OS の定義に整列として Windows 10 と Windows の以前のバージョンでは、DIP が定義されている*を*します。 表示の向きが縦モードに回転されると、アプリは **CoreWindow** の幅と高さを反転するので、レンダー ターゲットのサイズ (境界) もそれに応じて変更する必要があります。 Direct3D の座標は常に物理ピクセルであるため、スワップ チェーンをセットアップするために Direct3D に渡す **CoreWindow** の DIP 値は、事前に整数ピクセル値に変換する必要があります。

プロセスに関しては、単にスワップ チェーンをサイズ変更する場合よりも少し多くの作業を行っています。実際には、画像の Direct2D と Direct3D のコンポーネントを提示用に合成する前に回転させ、スワップ チェーンに結果を新しい向きでレンダリングしたことを伝えます。 ここでは、**DX::DeviceResources::CreateWindowSizeDependentResources** のコード例で示されているように、このプロセスについてさらに詳しく説明します。

-   表示の新しい向きを判断します。 表示が横から縦に (またはその逆に) 反転された場合は、表示境界の高さと幅の値を交換します (当然、DIP 値をピクセルに変換します)。

-   次に、スワップ チェーンが作成されたかどうかを確認します。 作成されていない場合は、[**IDXGIFactory2::CreateSwapChainForCoreWindow**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgifactory2-createswapchainforcorewindow) を呼び出して作成します。 または、[**IDXGISwapchain:ResizeBuffers**](https://docs.microsoft.com/windows/desktop/api/dxgi/nf-dxgi-idxgiswapchain-resizebuffers) を呼び出して、既にあるスワップ チェーンのバッファーのサイズを新しい表示サイズに変更します。 回転イベントのためにスワップ チェーンをサイズ変更する必要はありませんが (レンダリング パイプラインによって既に回転された内容を出力するだけであるため)、スナップ イベントやページ横幅に合わせるイベントのように、サイズ変更が必要なその他のイベントもあります。

-   その後、グラフィックス パイプラインのピクセルまたは頂点をスワップ チェーンにレンダリングするときに適用する、適切な 2-D または 3-D マトリックス変換を設定します。 可能性のある回転マトリックスは、次の 4 つです。

    -   横 (DXGI\_モード\_回転\_IDENTITY)
    -   縦 (DXGI\_モード\_回転\_[rotate270])
    -   反転横 (DXGI\_モード\_回転\_ROTATE180)
    -   反転、縦向き (DXGI\_モード\_回転\_ROTATE90)

    Windows 10 で提供されるデータに基づいて適切なマトリックスを選択 (の結果など[ **DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged)) の表示を決定するための向き、およびそれになります各 (Direct2D) ピクセルまたは頂点 (Direct3D)、シーン内の座標を効果的に画面の向きに合わせて回転を乗算します。 Direct2D では画面の原点が左上隅として定義されていますが、Direct3D では原点がウィンドウの論理的中央として定義されていることに注意してください。

> **注**  回転し、それらを定義する方法を使用する 2 D 変換に関する詳細については、次を参照してください。[画面の回転 (2-d) のマトリックスを定義する](#appendix-a-applying-matrices-for-screen-rotation-2-d)します。 回転で使われる 3-D 変換について詳しくは、「[画面の回転のためのマトリックスの適用 (3-D)](#appendix-b-applying-matrices-for-screen-rotation-3-d)」をご覧ください。

 

ここからが重要な部分です。次のように、[**IDXGISwapChain1::SetRotation**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-setrotation) を呼び出して、更新した回転マトリックスを渡します。

`m_swapChain->SetRotation(rotation);`

また、選択された回転マトリックスを、レンダリング メソッドが新しいプロジェクションを計算するときに取得できる場所に格納します。 最終的な 3-D プロジェクションをレンダリングするとき、または最終的な 2-D レイアウトを合成するときに、このマトリックスを使います。 自動的に適用されることはありません。

その後、回転された 3-D ビューのための新しいレンダー ターゲットと、ビューのための新しい深度ステンシル バッファーを作成します。 [  **ID3D11DeviceContext:RSSetViewports**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-rssetviewports) を呼び出して、回転されたシーンのための 3-D レンダリング ビューポートを設定します。

最後に、回転またはレイアウトする 2-D 画像がある場合は、[**ID2D1DeviceContext::CreateBitmapFromDxgiSurface**](https://docs.microsoft.com/windows/desktop/api/d2d1_1/nf-d2d1_1-id2d1devicecontext-createbitmapfromdxgisurface(idxgisurface_constd2d1_bitmap_properties1__id2d1bitmap1)) を使って、サイズ変更されたスワップ チェーンのための書き込み可能なビットマップとして 2-D レンダー ターゲットを作成し、更新された向きのための新しいレイアウトを合成します。 レンダー ターゲットで、必要に応じて任意のプロパティ (コード例でのアンチエイリアシング モードなど) を設定します。

ここで、スワップ チェーンを表示します。

## <a name="reduce-the-rotation-delay-by-using-corewindowresizemanager"></a>CoreWindowResizeManager の使用による回転の遅延の短縮


既定では、Windows 10 は、アプリ モデルやイメージの回転を完了する、言語に関係なく、すべてのアプリの時間の短いも顕著なウィンドウを提供します。 ただし、アプリがここで説明したいずれかの方法で回転の計算を行った場合は、この時間枠が閉じる前に処理を完了できる可能性があります。 残った時間は返して、回転アニメーションを完了するために使いたいと思うでしょう。 ここで、[**CoreWindowResizeManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindowResizeManager) が登場します。

[  **CoreWindowResizeManager**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindowResizeManager) の使い方は、次のようになります。[**DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) イベントが発生したら、イベントのハンドラー内で [**CoreWindowResizeManager::GetForCurrentView**](https://docs.microsoft.com/previous-versions/hh404170(v=vs.85)) を呼び出して、**CoreWindowResizeManager** のインスタンスを取得します。新しい向きのレイアウトが完了して提示されたら、[**NotifyLayoutCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindowresizemanager.notifylayoutcompleted) を呼び出し、Windows に対して、回転アニメーションを完了してアプリ画面を表示できることを知らせます。

[  **DisplayInformation::OrientationChanged**](https://docs.microsoft.com/uwp/api/windows.graphics.display.displayinformation.orientationchanged) のイベント ハンドラー内のコードは、次のようになります。

```cpp
CoreWindowResizeManager^ resizeManager = Windows::UI::Core::CoreWindowResizeManager::GetForCurrentView();

// ... build the layout for the new display orientation ...

resizeManager->NotifyLayoutCompleted();
```

ユーザーが画面の向きを回転するとき Windows 10、アニメーション、アプリの独立したフィードバックとしてをユーザーに表示します。 アニメーションは 3 つの部分から成り、次の順序で実行されます。

-   Windows 10 では、元のイメージを縮小します。
-   Windows 10 では、新しいレイアウトを再構築にかかる時間のイメージが保持されます。 これが、短縮したい時間枠です。アプリはおそらく、この時間のすべては必要としません。
-   レイアウトの時間枠が終了するか、レイアウトの完了通知を受け取ると、Windows は画像を回転させ、クロスフェードが新しい向きにズームします。

アプリを呼び出すときの 3 番目の項目で、推奨される[ **NotifyLayoutCompleted**](https://docs.microsoft.com/uwp/api/windows.ui.core.corewindowresizemanager.notifylayoutcompleted)、Windows 10 タイムアウトまでの時間を停止、回転アニメーションが完了して、アプリでは、描画は今すぐに制御を返します新しい方向を表示します。 全体的な効果は、アプリの動作が少し滑らかで機敏になり、効率もいくらか向上することです。

## <a name="appendix-a-applying-matrices-for-screen-rotation-2-d"></a>付録 A:画面の回転 (2-d) の行列を適用します。


[スワップ チェーンのサイズ変更とその内容の事前回転](#resizing-the-swap-chain-and-pre-rotating-its-contents)のサンプル　コード (および [DXGI スワップ チェーンの回転のサンプル](https://go.microsoft.com/fwlink/p/?linkid=257600)) で既にお気付きかもしれませんが、回転マトリックスは Direct2D 出力用と Direct3D 出力用とに分けてきました。 まずは、2-D マトリックスを見てみましょう。

Direct2D コンテンツと Direct3D コンテンツに同じ回転マトリックスを適用できない理由は、次の 2 つです。

-   1 つ目の理由は、異なるデカルト座標モデルが使われていることです。 Direct2D では右手の法則が使われており、Y 座標は原点から上へ移動すると正の値が増加します。 一方、Direct3D では左手の法則が使われており、Y 座標は原点から右に移動すると正の値が増加します。 そのため、Direct2D では画面座標の原点が左上にあり、Direct3D では画面 (プロジェクション平面) の原点が左下にあります。 詳しくは、「[3-D 座標系](https://docs.microsoft.com/previous-versions/windows/desktop/bb324490(v=vs.85))」をご覧ください。

    ![Direct3D 座標系。](images/direct3d-origin.png)![Direct2D 座標系。](images/direct2d-origin.png)

-   2 つ目の理由は、3-D 回転マトリックスは、丸めエラーを回避するために、明示的に指定する必要があることです。

スワップ チェーンは原点が左下にあると想定するため、右手による Direct2D 座標系を回転させて、スワップ チェーンで使われる左手による座標系に揃える必要があります。 具体的には、回転した座標系原点の変換マトリックスで回転マトリックスを乗算して、左手による新しい向きに画像を位置変更します。次に、画像を [**CoreWindow**](https://docs.microsoft.com/uwp/api/Windows.UI.Core.CoreWindow) の座標空間からスワップ チェーンの座標空間に変換します。 Direct2D レンダー ターゲットがスワップ チェーンに接続されている場合は、アプリも一貫してこの変換を適用する必要があります。 ただし、スワップ チェーンに直接関連付けられていない中間サーフェスにアプリが描画している場合は、この座標空間変換を適用しないでください。

4 つの回転の中から適切なマトリックスを選ぶコードは、次のようになります (新しい座標系原点への変換に注意が必要です)。

```cpp
   
// Set the proper orientation for the swap chain, and generate 2D and
// 3D matrix transformations for rendering to the rotated swap chain.
// Note the rotation angle for the 2D and 3D transforms are different.
// This is due to the difference in coordinate spaces.  Additionally,
// the 3D matrix is specified explicitly to avoid rounding errors.

switch (displayRotation)
{
case DXGI_MODE_ROTATION_IDENTITY:
    m_orientationTransform2D = Matrix3x2F::Identity();
    m_orientationTransform3D = ScreenRotation::Rotation0;
    break;

case DXGI_MODE_ROTATION_ROTATE90:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(90.0f) *
        Matrix3x2F::Translation(m_logicalSize.Height, 0.0f);
    m_orientationTransform3D = ScreenRotation::Rotation270;
    break;

case DXGI_MODE_ROTATION_ROTATE180:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(180.0f) *
        Matrix3x2F::Translation(m_logicalSize.Width, m_logicalSize.Height);
    m_orientationTransform3D = ScreenRotation::Rotation180;
    break;

case DXGI_MODE_ROTATION_ROTATE270:
    m_orientationTransform2D = 
        Matrix3x2F::Rotation(270.0f) *
        Matrix3x2F::Translation(0.0f, m_logicalSize.Width);
    m_orientationTransform3D = ScreenRotation::Rotation90;
    break;

default:
    throw ref new FailureException();
}
    
```

2-D 画像の正しい変換マトリックスと原点を取得したら、[**ID2D1DeviceContext::BeginDraw**](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-begindraw) と [**ID2D1DeviceContext::EndDraw**](https://docs.microsoft.com/windows/desktop/api/d2d1/nf-d2d1-id2d1rendertarget-enddraw) の呼び出しの間で、[**ID2D1DeviceContext::SetTransform**](https://docs.microsoft.com/windows/desktop/Direct2D/id2d1rendertarget-settransform) を呼び出して設定します。

**警告**   Direct2D 変換履歴はありません。 アプリが [**ID2D1DeviceContext::SetTransform**](https://docs.microsoft.com/windows/desktop/Direct2D/id2d1rendertarget-settransform) を描画コードの一部としても使っている場合、このマトリックスは、適用した他のすべての変換で、右から乗算する必要があります。

 

```cpp
    ID2D1DeviceContext* context = m_deviceResources->GetD2DDeviceContext();
    Windows::Foundation::Size logicalSize = m_deviceResources->GetLogicalSize();

    context->SaveDrawingState(m_stateBlock.Get());
    context->BeginDraw();

    // Position on the bottom right corner.
    D2D1::Matrix3x2F screenTranslation = D2D1::Matrix3x2F::Translation(
        logicalSize.Width - m_textMetrics.layoutWidth,
        logicalSize.Height - m_textMetrics.height
        );

    context->SetTransform(screenTranslation * m_deviceResources->GetOrientationTransform2D());

    DX::ThrowIfFailed(
        m_textFormat->SetTextAlignment(DWRITE_TEXT_ALIGNMENT_TRAILING)
        );

    context->DrawTextLayout(
        D2D1::Point2F(0.f, 0.f),
        m_textLayout.Get(),
        m_whiteBrush.Get()
        );

    // Ignore D2DERR_RECREATE_TARGET here. This error indicates that the device
    // is lost. It will be handled during the next call to Present.
    HRESULT hr = context->EndDraw();
```

次にスワップ チェーンを表示するときは、2-D 画像が新しい表示の向きに合わせて回転されています。

## <a name="appendix-b-applying-matrices-for-screen-rotation-3-d"></a>付録 B:画面の回転 (3 D) の行列を適用します。


[スワップ チェーンのサイズ変更とその内容の事前回転](#resizing-the-swap-chain-and-pre-rotating-its-contents)のコード例 (および [DXGI スワップ チェーンの回転のサンプル](https://go.microsoft.com/fwlink/p/?linkid=257600)) では、画面の向きごとに特定の変換マトリックスを定義しました。 ここでは、3-D シーンを回転させるためのマトリックスを見てみましょう。 前と同じように、4 つのそれぞれの向きのために、マトリックスのセットを作成します。 丸めエラー、つまりわずかな視覚的アーティファクトを避けるために、コード内でマトリックスを明示的に宣言します。

これらの 3-D 回転マトリックスは、次のようにセットアップします。 次のコード例で示されているマトリックスは、カメラの 3-D シーン空間内の点を定義する頂点を 0°、90°、180°、または 270° 回転させる、標準的な回転マトリックスです。 各頂点の\[x、y、z\]シーンの 2 D 投影が計算されたときに、この回転行列を使用して、シーンの座標の値が乗算されます。

```cpp
   
// 0-degree Z-rotation
static const XMFLOAT4X4 Rotation0( 
    1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 1.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 90-degree Z-rotation
static const XMFLOAT4X4 Rotation90(
    0.0f, 1.0f, 0.0f, 0.0f,
    -1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 180-degree Z-rotation
static const XMFLOAT4X4 Rotation180(
    -1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, -1.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );

// 270-degree Z-rotation
static const XMFLOAT4X4 Rotation270( 
    0.0f, -1.0f, 0.0f, 0.0f,
    1.0f, 0.0f, 0.0f, 0.0f,
    0.0f, 0.0f, 1.0f, 0.0f,
    0.0f, 0.0f, 0.0f, 1.0f
    );            
    }
```

スワップ チェーンの回転の種類は、次のように、[**IDXGISwapChain1::SetRotation**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-setrotation) を呼び出して設定します。

`   m_swapChain->SetRotation(rotation);`

ここで、レンダリング メソッド内に、次のようなコードを実装します。

``` syntax
struct ConstantBuffer // This struct is provided for illustration.
{
    // Other constant buffer matrices and data are defined here.

    float4x4 projection; // Current matrix for projection
} ;
ConstantBuffer  m_constantBufferData;          // Constant buffer resource data

// ...

// Rotate the projection matrix as it will be used to render to the rotated swap chain.
m_constantBufferData.projection = mul(m_constantBufferData.projection, m_rotationTransform3D);
```

ここで、render メソッドを呼び出すときに、現在回転行列乗算 (クラス変数で指定された**m\_orientationTransform3D**) 現在の投影マトリックスを使用し、その結果を割り当てますレンダラーに新しい射影行列として操作します。 スワップ チェーンを表示すると、更新された表示の向きでシーンを見ることができます。

 

 




