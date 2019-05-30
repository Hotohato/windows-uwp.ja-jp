---
title: チュートリアル -- Direct3D 11 のシャドウ ボリュームの深度バッファー
description: このチュートリアルでは、すべての Direct3D 機能レベルのデバイスで Direct3D 11 を使い、深度マップを利用してシャドウ ボリュームをレンダリングする方法について説明します。
ms.assetid: d15e6501-1a1d-d99c-d1d8-ad79b849db90
ms.date: 02/08/2017
ms.topic: article
keywords: Windows 10, UWP, ゲーム, DirectX, シャドウ ボリューム, 深度バッファー, DirectX 11
ms.localizationpriority: medium
ms.openlocfilehash: 2ce0cbd310ea89c5fa7b5c68033402f559768a24
ms.sourcegitcommit: ac7f3422f8d83618f9b6b5615a37f8e5c115b3c4
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 05/29/2019
ms.locfileid: "66368509"
---
# <a name="walkthrough-implement-shadow-volumes-using-depth-buffers-in-direct3d-11"></a>チュートリアル: Direct3d11 の深度バッファーを使用してボリュームをシャドウの実装します。



このチュートリアルでは、すべての Direct3D 機能レベルのデバイスで Direct3D 11 を使い、深度マップを利用してシャドウ ボリュームをレンダリングする方法について説明します。
## 
<table>
<colgroup>
<col width="50%" />
<col width="50%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">トピック</th>
<th align="left">説明</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left"><p><a href="create-depth-buffer-resource--view--and-sampler-state.md">深度バッファー デバイス リソースを作成します。</a></p></td>
<td align="left"><p>シャドウ ボリュームの深度のテストをサポートするために必要な Direct3D デバイス リソースを作成する方法について説明します。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="render-the-shadow-map-to-the-depth-buffer.md">深度バッファーにシャドウ マップを表示します。</a></p></td>
<td align="left"><p>ライトの視点からレンダリングして、シャドウ ボリュームを表す 2 次元の深度マップを作成します。</p></td>
</tr>
<tr class="odd">
<td align="left"><p><a href="render-the-scene-with-depth-testing.md">深度テストとシーンをレンダリングします。</a></p></td>
<td align="left"><p>シャドウ効果を作成するには、頂点 (またはジオメトリ) シェーダーとピクセル シェーダーに深度のテストを追加します。</p></td>
</tr>
<tr class="even">
<td align="left"><p><a href="target-a-range-of-hardware.md">さまざまなハードウェアでシャドウ マップをサポートします。</a></p></td>
<td align="left"><p>より高速なデバイスでは高品質なシャドウを、性能が低いデバイスではよりすばやいシャドウをレンダリングします。</p></td>
</tr>
</tbody>
</table>

 

## <a name="shadow-mapping-application-to-direct3d-9-desktop-porting"></a>Direct3D 9 デスクトップに対するシャドウ マップの適用の移植


Windows 8 adde 9 の機能レベルを比較機能を d 深さ\_1 から 9\_3。 シャドウ ボリュームを含むレンダリング コードを DirectX 11 に移行できるようになりました。Direct3D 11 レンダラーは機能レベル 9 のデバイスと下位互換性を持ちます。 このチュートリアルでは、Direct3D 11 のアプリやゲームで深度のテストを使って従来のシャドウ ボリュームを実装する方法を説明します。 コードは次のプロセスに対応しています。

1.  シャドウ マッピング用の Direct3D デバイス リソースを作成する。
2.  レンダリング パスを追加して深度マップを作成する。
3.  深度のテストをメイン レンダリング パスに渡す。
4.  必要なシェーダー コードを実行する。
5.  下位レベル ハードウェアでのレンダリングを高速化するためのオプション。

このチュートリアルを完了すると、9 の機能レベルと互換性がある direct3d11 の基本的な互換性のあるシャドウ ボリュームの手法を実装する方法を理解しておく必要がありますあります\_1 以上です。

## <a name="prerequisites"></a>前提条件


[ユニバーサル Windows プラットフォーム (UWP) DirectX ゲームの開発環境を準備する](prepare-your-dev-environment-for-windows-store-directx-game-development.md)必要があります。 テンプレートは、まだ必要はありませんが、このチュートリアルのコード サンプルをビルドする、Microsoft Visual Studio 2015 が必要があります。

## <a name="related-topics"></a>関連トピック


**Direct3D**

* [Direct3D 9 での HLSL シェーダーの記述](https://docs.microsoft.com/windows/desktop/direct3dhlsl/dx-graphics-hlsl-writing-shaders-9)
* [UWP の DirectX 11 の新しいプロジェクトを作成します。](user-interface.md)

**シャドウの技術情報のマッピング**

* [影の深さのマップを向上させるために一般的な手法](https://docs.microsoft.com/windows/desktop/DxTechArts/common-techniques-to-improve-shadow-depth-maps)
* [カスケード シャドウ マップ](https://docs.microsoft.com/windows/desktop/DxTechArts/cascaded-shadow-maps)

 

 




