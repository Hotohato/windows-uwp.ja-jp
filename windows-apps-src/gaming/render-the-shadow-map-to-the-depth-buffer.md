---
title: 深度バッファーへのシャドウ マップのレンダリング
description: ライトの視点からレンダリングして、シャドウ ボリュームを表す 2 次元の深度マップを作成します。
ms.assetid: 7f3d0208-c379-8871-cc48-027047c6c2d0
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10、UWP、ゲーム、レンダリング、シャドウ マップ、深度バッファー、Direct3D
ms.localizationpriority: medium
ms.openlocfilehash: a8ae67df457d4abafc8fb689a747139f62ca0e0e
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368072"
---
# <a name="render-the-shadow-map-to-the-depth-buffer"></a>深度バッファーへのシャドウ マップのレンダリング




ライトの視点からレンダリングして、シャドウ ボリュームを表す 2 次元の深度マップを作成します。 深度マップでは、シャドウ内にレンダリングされる空間をマークします。 パート 2 の[チュートリアル。Direct3d11 の深度バッファーを使用してボリュームをシャドウ実装](implementing-depth-buffers-for-shadow-mapping.md)します。

## <a name="clear-the-depth-buffer"></a>深度バッファーの消去


深度バッファーにレンダリングする前に、必ず深度バッファーを消去します。

```cpp
context->ClearRenderTargetView(m_deviceResources->GetBackBufferRenderTargetView(), DirectX::Colors::CornflowerBlue);
context->ClearDepthStencilView(m_shadowDepthView.Get(), D3D11_CLEAR_DEPTH | D3D11_CLEAR_STENCIL, 1.0f, 0);
```

## <a name="render-the-shadow-map-to-the-depth-buffer"></a>深度バッファーへのシャドウ マップのレンダリング


シャドウのレンダリング パスでは、深度バッファーを指定しますが、レンダー ターゲットは指定しません。

ライト ビューポート、頂点シェーダーを指定し、ライト空間の定数バッファーを設定します。 このパスに前面のカリングを使って、シャドウ バッファーに配置された深度値を最適化します。

ほとんどのデバイスでは、ピクセル シェーダーに対して nullptr を指定できます (または、ピクセル シェーダーの指定を完全にスキップできます)。 ただし、ドライバーによっては、Direct3D デバイスで null のピクセル シェーダーを設定して描画を呼び出すと、例外がスローされる場合があります。 この例外を避けるには、シャドウのレンダリング パスに対して最小限のピクセル シェーダーを設定します。 このシェーダーの出力は破棄されるため、各ピクセルで [**discard**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-discard) を呼び出すことができます。

シャドウが生じる可能性があるオブジェクトをレンダリングしますが、シャドウが生じる可能性がないジオメトリ (部屋の床や、最適化のためにシャドウ パスから削除したオブジェクトなど) のレンダリングについては気にする必要はありません。

```cpp
void ShadowSceneRenderer::RenderShadowMap()
{
    auto context = m_deviceResources->GetD3DDeviceContext();

    // Render all the objects in the scene that can cast shadows onto themselves or onto other objects.

    // Only bind the ID3D11DepthStencilView for output.
    context->OMSetRenderTargets(
        0,
        nullptr,
        m_shadowDepthView.Get()
        );

    // Note that starting with the second frame, the previous call will display
    // warnings in VS debug output about forcing an unbind of the pixel shader
    // resource. This warning can be safely ignored when using shadow buffers
    // as demonstrated in this sample.

    // Set rendering state.
    context->RSSetState(m_shadowRenderState.Get());
    context->RSSetViewports(1, &m_shadowViewport);

    // Each vertex is one instance of the VertexPositionTexNormColor struct.
    UINT stride = sizeof(VertexPositionTexNormColor);
    UINT offset = 0;
    context->IASetVertexBuffers(
        0,
        1,
        m_vertexBuffer.GetAddressOf(),
        &stride,
        &offset
        );

    context->IASetIndexBuffer(
        m_indexBuffer.Get(),
        DXGI_FORMAT_R16_UINT, // Each index is one 16-bit unsigned integer (short).
        0
        );

    context->IASetPrimitiveTopology(D3D11_PRIMITIVE_TOPOLOGY_TRIANGLELIST);
    context->IASetInputLayout(m_inputLayout.Get());

    // Attach our vertex shader.
    context->VSSetShader(
        m_simpleVertexShader.Get(),
        nullptr,
        0
        );

    // Send the constant buffers to the Graphics device.
    context->VSSetConstantBuffers(
        0,
        1,
        m_lightViewProjectionBuffer.GetAddressOf()
        );

    context->VSSetConstantBuffers(
        1,
        1,
        m_rotatedModelBuffer.GetAddressOf()
        );

    // In some configurations, it's possible to avoid setting a pixel shader
    // (or set PS to nullptr). Not all drivers are tolerant of this, so to be
    // safe set a minimal shader here.
    //
    // Direct3D will discard output from this shader because the render target
    // view is unbound.
    context->PSSetShader(
        m_textureShader.Get(),
        nullptr,
        0
        );

    // Draw the objects.
    context->DrawIndexed(
        m_indexCountCube,
        0,
        0
        );
}
```

**視錐台を最適化するには。** 実装は、深度バッファーから最も有効桁数を取得するために緊密な錐を計算することを確認します。 シャドウの方法に関するヒントについては、「[シャドウ深度マップを向上させるための一般的な方法](https://docs.microsoft.com/windows/desktop/DxTechArts/common-techniques-to-improve-shadow-depth-maps)」をご覧ください。

## <a name="vertex-shader-for-shadow-pass"></a>シャドウ パスの頂点シェーダー


簡略化したバージョンの頂点シェーダーを使って、ライト空間内の頂点の位置だけをレンダリングします。 照明法線や二次変換などを含めないでください。

```cpp
PixelShaderInput main(VertexShaderInput input)
{
    PixelShaderInput output;
    float4 pos = float4(input.pos, 1.0f);

    // Transform the vertex position into projected space.
    pos = mul(pos, model);
    pos = mul(pos, view);
    pos = mul(pos, projection);
    output.pos = pos;

    return output;
}
```

このチュートリアルの次のパートでは、[深度のテストを使ったレンダリング](render-the-scene-with-depth-testing.md)によってシャドウを追加する方法について説明します。

 

 




