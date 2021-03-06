---
title: Direct3D 9 と Direct3D 11 の間の重要な変更点
description: このトピックでは、DirectX 9 と DirectX 11 の大まかな違いについて説明します。
ms.assetid: 35a9e388-b25e-2aac-0534-577b15dae364
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, ゲーム, DirectX, Direct3D 9, Direct3D 11, 変更
ms.localizationpriority: medium
ms.openlocfilehash: e3e3ecfaee8a99522623ee6b021d8e3a2d78ab85
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66367541"
---
# <a name="important-changes-from-direct3d-9-to-direct3d-11"></a>Direct3D 9 と Direct3D 11 の間の重要な変更点



**概要**

-   [DirectX、ポートを計画します。](plan-your-directx-port.md)
-   Direct3D 9 と Direct3D 11 の間の重要な変更点
-   [機能のマッピング](feature-mapping.md)


このトピックでは、DirectX 9 と DirectX 11 の大まかな違いについて説明します。

Direct3D 11 は、グラフィックス ハードウェアに対する下位レベルの仮想化インターフェイスである Direct3D 9 と基本的に同じ種類の API です。 これまでどおり、さまざまなハードウェア実装でグラフィックス描画操作を実行できます。 グラフィックス API のレイアウトは Direct3D 9 から変更されています。具体的には、デバイス コンテキストの概念が拡張され、グラフィックス インフラストラクチャ専用の API が追加されました。 Direct3D デバイスに格納されたリソースには、リソース ビューと呼ばれるデータ ポリモーフィズムの新しいメカニズムが用意されています。

## <a name="core-api-functions"></a>コア API の関数


Direct3D 9 では、使い始める前に、Direct3D API へのインターフェイスを作成する必要がありました。 Direct3D 11 ユニバーサル Windows プラットフォーム (UWP) ゲームでは、[**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) という静的関数を呼び出して、デバイスとデバイス コンテキストを作成します。

## <a name="devices-and-device-context"></a>デバイスとデバイス コンテキスト


Direct3D 11 デバイスは、仮想化グラフィックス アダプターを表します。 これは、GPU へのテクスチャのアップロード、テクスチャ リソースとスワップ チェーン上のビューの作成、テクスチャ サンプラーの作成など、ビデオ メモリ内にリソースを作成するために使います。 Direct3D 11 デバイス インターフェイスの用途の全一覧については、「[**ID3D11Device**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11device)」と「[**ID3D11Device1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11device1)」をご覧ください。

Direct3D 11. のデバイス コンテキストは、パイプラインの状態を設定し、レンダリング コマンドを生成するために使われます。 たとえば、Direct3D 11 のレンダリング チェーンでは、デバイス コンテキストを使って、レンダリング チェーンを設定し、シーンを描画します (下記参照)。 デバイス コンテキストは、Direct3D デバイス リソースで使われるビデオ メモリにアクセス (マップ) するために使われます。また、定数バッファー データなどのサブリソース データを更新するためにも使われます。 Direct3D 11 デバイス コンテキストの用途の全一覧については、「[**ID3D11DeviceContext**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11devicecontext)」と「[**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1)」をご覧ください。 ほとんどのサンプルではイミディエイト コンテキストを使ってデバイスに直接レンダリングしていますが、Direct3D 11 では主にマルチスレッドに使われる遅延デバイス コンテキストもサポートしています。

Direct3D 11 では、[**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) を呼び出して、デバイス ハンドルとデバイス コンテキスト ハンドルの両方を取得します。 また、このメソッドでは、特定のハードウェア機能を要求し、グラフィックス アダプターでサポートされている Direct3D 機能レベルの情報を取得します。 デバイス、デバイス コンテキスト、スレッドの考慮事項について詳しくは、「[Direct3D 11 のデバイスについて](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-intro)」をご覧ください。

## <a name="device-infrastructure-frame-buffers-and-render-target-views"></a>デバイス インフラストラクチャ、フレーム バッファー、レンダー ターゲット ビュー


Direct3D 11 では、[**IDXGIAdapter**](https://docs.microsoft.com/windows/desktop/api/dxgi/nn-dxgi-idxgiadapter) と [**IDXGIDevice1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgidevice2) COM インターフェイスを使って、DirectX Graphic Infrastructure (DXGI) API でデバイス アダプターとハードウェア構成を設定します。 特定の DXGI インターフェイスでバッファーなどのウィンドウ リソース (表示またはオフ スクリーン) を作成し、構成します。[**IDXGIFactory2**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2) ファクトリ パターンの実装では、フレーム バッファーなどの DXGI リソースを取得します。 DXGI がスワップ チェーンを所有するため、画面にフレームを表示するために DXGI インターフェイスが使われます (「[**IDXGISwapChain1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgiswapchain1)」をご覧ください)。

[  **IDXGIFactory2**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nn-dxgi1_2-idxgifactory2) を使って、ゲームと互換性のあるスワップ チェーンを作成します。 HWND のスワップ チェーンを作成する代わりに、コア ウィンドウ (コンポジション (XAML の相互運用機能)) のスワップ チェーンを作成する必要があります。

## <a name="device-resources-and-resource-views"></a>デバイス リソースとリソース ビュー


Direct3D 11 では、ビューと呼ばれるビデオ メモリ リソースに対する追加のポリモーフィズム レベルをサポートしています。 基本的には、テクスチャに対する Direct3D 9 オブジェクトは以前は 1 つでしたが、それが 2 つになりました。1 つはデータを保持するテクスチャ リソースで、もう 1 つはレンダリングにビューを使う方法を示すリソース ビューです。 リソースに基づくビューでは、特定の目的にそのリソースを使うことができます。 たとえば、2D テクスチャ リソースを [**ID3D11Texture2D**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11texture2d) として作成し、それをシェーダーのテクスチャとして使うことができるように、2D テクスチャ リソースに対してシェーダー リソース ビュー ([**ID3D11ShaderResourceView**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11shaderresourceview)) を作成します。 また、描画面として使うことができるように、同じ 2D テクスチャ リソースに対してレンダー ターゲット ビュー ([**ID3D11RenderTargetView**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview)) を作成することもできます。 別の例として、1 つのテクスチャ リソースに対して 2 つの異なるビューを使うと、同じピクセル データが 2 つの異なるピクセル形式で表されます。

基になるリソースは、そのリソースから作成されるビューの種類と互換性のあるプロパティを使って作成する必要があります。 たとえば場合、 [ **ID3D11RenderTargetView** ](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11rendertargetview)が適用されるでその画面を作成、画面に、 [ **D3D11\_バインド\_レンダー\_ターゲット**](https://docs.microsoft.com/windows/desktop/api/d3d11/ne-d3d11-d3d11_bind_flag)フラグ。 レンダリングと互換性のある DXGI サーフェス形式にも、画面があります (を参照してください[ **DXGI\_形式**](https://docs.microsoft.com/windows/desktop/api/dxgiformat/ne-dxgiformat-dxgi_format))。

レンダリングに使うリソースのほとんどは、[**ID3D11Resource**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11resource) インターフェイスから継承します。このインターフェイスは、[**ID3D11DeviceChild**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11devicechild) から継承します。 頂点バッファー、インデックス バッファー、定数バッファー、シェーダーはすべて Direct3D 11 リソースです。 入力レイアウトとサンプラーの状態は [**ID3D11DeviceChild**](https://docs.microsoft.com/windows/desktop/api/d3d11/nn-d3d11-id3d11devicechild) から直接継承します。

リソース ビューの使用、DXGI\_ピクセル形式を示す列挙値を書式設定します。 常にではなく D3DFMT は、DXGI としてサポートされている\_形式。 たとえば、形式はありません 24 bpp RGB D3DFMT に相当する DXGI で\_R8G8B8 します。 いないすべての RGB 形式に対応する BGR も (DXGI\_形式\_R10G10B10A2\_UNORM は D3DFMT\_A2B10G10R10、D3DFMT に直接相当するものはありませんが、\_A2R10G10B10)。 これらの従来の形式のコンテンツをビルド時にサポートされている形式に変換する必要があります。 DXGI の完全な一覧形式を参照してください、 [ **DXGI\_形式**](https://docs.microsoft.com/windows/desktop/api/dxgiformat/ne-dxgiformat-dxgi_format)列挙体。

Direct3D デバイス リソース (とリソース ビュー) は、シーンをレンダリングする前に作成します。 デバイス コンテキストは、後で説明するように、レンダリング チェーンを設定するために使われます。

## <a name="device-context-and-the-rendering-chain"></a>デバイス コンテキストとレンダリング チェーン


Direct3D 9 と Direct3D 10.x には、リソースの作成、状態、描画を管理する単一の Direct3D デバイス オブジェクトがありました。 Direct3D 11 では、これまでどおり Direct3D デバイス インターフェイスでリソースの作成を管理しますが、すべての状態と描画操作は Direct3D デバイス コンテキストを使って処理します。 デバイス コンテキスト ([**ID3D11DeviceContext1**](https://docs.microsoft.com/windows/desktop/api/d3d11_1/nn-d3d11_1-id3d11devicecontext1) インターフェイス) を使って、レンダリング チェーンを設定する例を次に示します。

-   レンダー ターゲット ビュー (と深度ステンシル ビュー) の設定と消去
-   入力アセンブラー ステージ (IA ステージ) の頂点バッファー、インデックス バッファー、入力レイアウトの設定
-   パイプラインへの頂点シェーダーとピクセル シェーダーのバインド
-   シェーダーへの定数バッファーのバインド
-   ピクセル シェーダーへのテクスチャ ビューとサンプラーのバインド
-   シーンの描画

[  **ID3D11DeviceContext::Draw**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-id3d11devicecontext-draw) メソッドの 1 つを呼び出すと、レンダー ターゲット ビューにシーンが描画されます。 すべての描画が完了したら、[**IDXGISwapChain1::Present1**](https://docs.microsoft.com/windows/desktop/api/dxgi1_2/nf-dxgi1_2-idxgiswapchain1-present1) を呼び出し、DXGI アダプターを使って、完成したフレームを表示します。

## <a name="state-management"></a>状態の管理


Direct3D 9 では、SetRenderState、SetSamplerState、SetTextureStageState メソッドで設定された多数の個々のトグルを使って状態の設定を管理しました。 Direct3D 11 では、従来の固定関数パイプラインがサポートされないため、SetTextureStageState の代わりに、ピクセル シェーダー (PS) を記述します。 Direct3D 9 の状態ブロックに相当するものはありません。 代わりに、Direct3D 11 では、レンダリングの状態をグループ化する合理化的な方法を提供する 4 種類の状態オブジェクトを使って状態を管理します。

D3DRS をサニティを使用する代わりに、たとえば、\_ZENABLE、この DepthStencilState オブジェクトを作成して、他の関連設定の状態し、レンダリング時に、状態を変更します。

Direct3D 9 アプリケーションを状態オブジェクトに移植する際は、さまざまな状態の組み合わせが、変更できない状態オブジェクトとして表されることに注意してください。 それらは一度作成し、有効な限り再利用する必要があります。

## <a name="direct3d-feature-levels"></a>Direct3D の機能レベル


Direct3D には、機能レベルと呼ばれるハードウェア サポートを確認するための新しいメカニズムが用意されています。 機能レベルにより、明確に定義された一連の GPU 機能を要求できるようになるため、グラフィックス アダプターが実行できる操作を簡単に判断できます。 9 など\_機能は、シェーダーを含む、Direct3D 9 グラフィックス アダプターによって提供される機能の 1 レベルの実装が 2.x をモデル化します。 9 以降\_1 は最も低い機能レベル、9 の Direct3D のプログラミング可能なシェーダー モデルでサポートされている同じステージされた頂点シェーダーとピクセル シェーダーをサポートするすべてのデバイスを期待できます。

ゲームでは [**D3D11CreateDevice**](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) を使って、Direct3D デバイスとデバイス コンテキストを作成します。 この関数を呼び出す際に、ゲームでサポートできる機能レベルの一覧を指定します。 この関数は、その一覧からサポートされる最高の機能レベルを返します。 たとえば、ゲームが 9 以上を含めるように対する BC4/BC5 テクスチャ (DirectX 10 ハードウェアの機能) を使用できる場合\_1 から 10\_サポートされている機能レベルの一覧では 0。 ゲームが DirectX 9 ハードウェア上で実行し、に対する BC4/BC5 テクスチャを使用できない、 [ **D3D11CreateDevice** ](https://docs.microsoft.com/windows/desktop/api/d3d11/nf-d3d11-d3d11createdevice) 9 が返されます\_1。 その場合は、別のテクスチャ形式 (とより小さいテクスチャ) にフォールバックできます。

Direct3D 9 ゲームを拡張して、より高い Direct3D 機能レベルをサポートする場合は、まず、既にある Direct3D 9 グラフィックス コードの移行を完了させることをお勧めします。 ゲームが Direct3D 11 で動作するようにすると、拡張グラフィックスを使った追加のレンダリング パスの追加が簡単になります。

機能レベルのサポートについて詳しくは、「[Direct3D 機能レベル](https://docs.microsoft.com/windows/desktop/direct3d11/overviews-direct3d-11-devices-downlevel-intro)」をご覧ください。 Direct3D 11 機能の一覧については、「[Direct3D 11 の機能](https://docs.microsoft.com/windows/desktop/direct3d11/direct3d-11-features)」と「[Direct3D 11.1 の機能](https://docs.microsoft.com/windows/desktop/direct3d11/direct3d-11-1-features)」をご覧ください。

## <a name="feature-levels-and-the-programmable-pipeline"></a>機能レベルとプログラム可能なパイプライン


ハードウェアは Direct3D 9 から進化を続けており、いくつかの新しいオプション ステージがプログラム可能なグラフィックス パイプラインに追加されています。 グラフィックス パイプラインの一連のオプションは、Direct3D 機能レベルによって異なります。 機能レベル 10.0 には、GPU でのマルチパス レンダリング用のオプションのストリーム アウトがあるジオメトリ シェーダー ステージが含まれています。 機能レベル 11\_ハードウェア テセレーションで使用するためのドメイン シェーダー、ハル シェーダー、0 が含まれます。 機能レベル 11\_10.x のみを含める機能レベルが DirectCompute の制限付き形式のサポートに 0 が DirectCompute のシェーダーの完全なサポートにも含まれます。

すべてのシェーダーは、Direct3D 機能レベルに対応するシェーダー プロファイルを使って HLSL で記述します。 シェーダー プロファイルは、vs を使用してをコンパイルする HLSL シェーダーのため、互換性のある上向き、\_4\_0\_レベル\_9\_1 または ps\_4\_0\_レベル\_9\_1 はすべてのデバイスで動作します。 シェーダーのプロファイルはない下位互換性があるため vs を使用して、シェーダーのコンパイル\_4\_1 は 10 の機能レベルでのみ動作\_1, 11\_0、または 11\_1 デバイス。

Direct3D 9 では、共有配列と一緒に SetVertexShaderConstant と SetPixelShaderConstant を使ってシェーダーの定数を管理していました。 Direct3D 11 では、頂点バッファーやインデックス バッファーと同じようなリソースである定数バッファーを使います。 定数バッファーは、効率的に更新できるように設計されています。 すべてのシェーダー定数を 1 つのグローバル配列にまとめる代わりに、定数を論理グループに整理し、1 つ以上の定数バッファーを使って管理します。 Direct3D 9 ゲームを Direct3D 11 に移植する場合は、定数バッファーを適切に更新できるように整理してください。 たとえば、フレームごとに更新されないシェーダーの定数は別の定数バッファーにグループ化します。そうすると、より動的なシェーダーの定数と一緒にそのデータをグラフィックス アダプターに絶えずアップロードする必要がなくなります。

> **注**  最も Direct3D 9 アプリケーション、シェーダーの広範な使用が従来の固定機能の動作の使用に混在場合があります。 Direct3D 11 では、プログラム可能なシェーダー モデルしか使わないことに注意してください。 Direct3D 9 の従来の固定関数機能は推奨されなくなりました。

 

 

 




