---
title: Texture2D と Texture2DArray のサブリソースのタイル表示
description: 以下の表に、Texture2D および Texture2DArray サブリソースがどのようにタイル表示されるかを示します。
ms.assetid: 2DC14DFC-5299-44D9-895F-5A223D3FD530
keywords:
- Texture2D と Texture2DArray のサブリソースのタイル表示
ms.date: 02/08/2017
ms.topic: article
ms.localizationpriority: medium
ms.openlocfilehash: 2e193ab7bce31c1f13cb40f04902922c6ff21056
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66370913"
---
# <a name="texture2d-and-texture2darray-subresource-tiling"></a>Texture2D と Texture2DArray のサブリソースのタイル表示


次の表に、[**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d) および [**Texture2DArray**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2darray) サブリソースがどのようにタイル表示されるかを示します。 これらの表の値は、テール ミップ パッキングをカウントしていません。

## <a name="span-idsubresources-with-multisample-counts-of-1spanspan-idsubresources-with-multisample-counts-of-1spanspan-idsubresources-with-multisample-counts-of-1spansubresources-with-multisample-counts-of-1"></a><span id="Subresources-with-multisample-counts-of-1"></span><span id="subresources-with-multisample-counts-of-1"></span><span id="SUBRESOURCES-WITH-MULTISAMPLE-COUNTS-OF-1"></span>1 のマルチ サンプリングのカウントを使用したサブリソース


次の表に、マルチサンプル数が 1 の [**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d) および [**Texture2DArray**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2darray) サブリソースがどのようにタイル表示されるかを示します。

| ビット/ピクセル (1 サンプル/ピクセル) | タイルの寸法 (ピクセル、W x H) |
|-----------------------------|-------------------------------|
| 8                           | 256 x 256                       |
| 16                          | 256 x 128                       |
| 32                          | 128 x 128                       |
| 64                          | 128 x 64                        |
| 128                         | 64 x 64                         |
| BC1、4                       | 512 x 256                       |
| BC2、3、5、6、7                 | 256 x 256                       |

 

ストリームのリソースではサポートされていません形式のビット数は 96 の bpp 形式、ビデオ形式は、DXGI\_形式\_R1\_UNORM、DXGI\_形式\_R8G8\_B8G8\_UNORM、DXGI\_形式\_R8R8\_G8B8\_UNORM します。

## <a name="span-idsubresources-with-various-multisample-countsspanspan-idsubresources-with-various-multisample-countsspanspan-idsubresources-with-various-multisample-countsspansubresources-with-various-multisample-counts"></a><span id="Subresources-with-various-multisample-counts"></span><span id="subresources-with-various-multisample-counts"></span><span id="SUBRESOURCES-WITH-VARIOUS-MULTISAMPLE-COUNTS"></span>サブリソースのさまざまなマルチ サンプル数


次の表に、さまざまなマルチサンプル数の [**Texture2D**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2d) および [**Texture2DArray**](https://docs.microsoft.com/windows/desktop/direct3dhlsl/sm5-object-texture2darray) サブリソースがどのようにタイル表示されるかを示します。

| ビット/ピクセル (1 サンプル/ピクセル) | タイルの寸法 (ピクセル、W x H) |
|-----------------------------|-------------------------------|
| 1                           | 1 x 1                           |
| 2                           | 2 x 1                           |
| 4                           | 2 x 2                           |
| 8                           | 4 x 2                           |
| 16                          | 4 x 4                           |

 

サンプル数 1 および 4 だけが、ストリーミング リソースにサポート (および許可) される必要があります。 ストリーミング リソースは現在、2、8、および 16 をサポートしていません (表示されていますが)。

実装では、ストリーミング リソースでサポートされていない場合でも、非ストリーミング リソースで 2、8、または 16 サンプルのマルチサンプル アンチエイリアシング (MSAA) モードをサポートすることを選択できます。

サンプル数が 1 より大きいストリーミング リソースは、128 bpp 形式を使うことはできません。

サポートされているサンプルの数と形式に関する制約は、仕様とハードウェアの不一致が原因です。

## <a name="span-idrelated-topicsspanrelated-topics"></a><span id="related-topics"></span>関連トピック


[ストリーミングのリソースの領域は並べて表示する方法](how-a-streaming-resource-s-area-is-tiled.md)

 

 




