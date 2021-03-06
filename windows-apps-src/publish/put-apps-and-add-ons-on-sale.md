---
Description: 期間限定で特売することにより Microsoft Store でアプリやアドオンの販促活動をすることができます。
title: アプリとアドオンの販売
ms.assetid: 71ABA960-0CDC-4E35-A1C8-1D34B6673817
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10, uwp
ms.localizationpriority: medium
ms.openlocfilehash: 3ec71453cd03359dc6e1b72af2f6a43805bcb5ff
ms.sourcegitcommit: fca0132794ec187e90b2ebdad862f22d9f6c0db8
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 04/24/2019
ms.locfileid: "63788357"
---
# <a name="put-apps-and-add-ons-on-sale"></a>アプリとアドオンの販売

期間限定で特売することにより Microsoft Store でアプリやアドオンの販促活動をすることができます。 製品を低い価格帯で提供することも、割引率を適用することもできます。 して、すべてのユーザーに販売の提供、他の製品のいずれかのユーザーの限定の提供を行うかどうかを選択できます。

> [!NOTE]
> 販売価格は、サブスクリプションのアドオンのサポートされていません。

申請の **[価格と使用可能状況]** ページの **[セール価格]** セクションを使ってアプリまたはアドオンの価格を一時的に下げると、顧客が見るストア登録情報には取り消し線が引かれた価格が表示され、値下げされていることをアピールできます (一方、[スケジュールされた価格変更](set-and-schedule-app-pricing.md#schedule-price-changes)では、ストアに変更を表示せずに価格を下げたり上げたりできます)。 

開発者が指定した、製品の特売期間中は、ユーザーは割引価格で製品を購入できるようになります。 価格を **[無料]** にした場合は、特売期間中、無料でダウンロードできます。

> [!IMPORTANT]
> 販売価格は、Xbox One などの Windows 10 デバイスでお客様にのみ表示されます。 のみ、その他の製品のいずれかの所有者に提供する売り上げ高は、Windows 10 バージョン 1607 以降でのお客様にのみ表示されます。
> 
> 他のオペレーティング システムでは、アプリやアドオンの通常価格がユーザーに表示され、セール価格で購入することはできません。 価格は新しい申請で異なる価格帯を選択することでいつでも変更できますが、その場合、期間限定販売としては表示されません。


## <a name="scheduling-a-sale"></a>特売のスケジュールの設定

特売は、アプリまたはアドオンの申請の一環としてスケジュールされます。 既に公開されているアプリやアドオンの特売のスケジュールを設定する場合、それが唯一の変更点であったとしても、新しい申請を作成する必要があります。

**販売をスケジュールするには**

1. 進行中のアプリまたはアドオン申請の **[価格と使用可能状況]** ページで、**[セール価格]** セクションに移動します。
2. **[オプションの表示]** を選択し、**[新しい販売]** を選択します。
3. **[市場の選択]** ポップアップ ウィンドウが表示されます。ここで、特売が提供される市場を指定する *"市場グループ"* を作成できます。 **[すべて選択]** をクリックして、アプリを利用できるすべての市場で特売を提供することも、1 つまたは複数の市場を選択することもできます。 必要に応じて、市場グループの名前を入力できます。 選択が完了したら、**[作成]** をクリックします  (グループ内の市場を後で編集する場合は、グループ名をクリックします)。

   > [!NOTE]
   > [セール価格] セクションで行った市場の選択は、アプリが提供される市場には影響しません。これらの選択は、セール価格を提供するかどうかと、その対象となる市場だけを決定するものです。 アプリを利用できない市場向けにセール価格を設定しても、その市場でアプリが利用可能になることはありません。
4. 割引の種類を指定するには、次のいずれかのオプションを選択します。
   - **価格**:このオプションを使用すると、アプリが提供されます下位の価格レベルを選択します。 通貨のドロップダウンを変更して、任意の通貨の価格を選択できます  (価格は、それぞれの通貨での同等額に変換されます。 詳しくは、「[価格](set-app-pricing-and-availability.md)」をご覧ください)。
   - **割合**:このオプションを使用すると、アプリに適用される割引率を選択します。 すべての通貨で同じ割引率が使われます。
5. **[提供先]** 行で、次の利用可能なオプションの 1 つを選びます。
   - **すべてのユーザー**:販売は、すべてのお客様に提供されます。
   - **所有者**:販売は、アプリのいずれかを既に所持している顧客に提供されます。 表示されるドロップダウンから、公開済みのアプリを選べます。 このオプションは、1 つ以上のアプリが公開されていないと、使用できません。

  > [!IMPORTANT]
  > **[次の所有者]** を選ぶと、Windows 10 バージョン 1607 以降のユーザーにのみ、セールが公開されます。

   - **既知のユーザー グループ**:販売、内のユーザーに提供される、[既知のユーザー グループ](create-known-user-groups.md)を選択します。 このオプションを使うには、既知のユーザー グループが既に作成されている必要があります。
   - **セグメント**:販売は、選択した顧客セグメント内のユーザーに提供されます。 [既に作成したセグメント](create-customer-segments.md)をここで使用できます。 **[初めて支払いを行う顧客]** を選択して、ストアで何も購入したことがないユーザーにのみ特売を提供することもできます。 初めてストアで購入したユーザーは、その後も購入を続ける場合が多いことがわかっています。このようなユーザーのグループはセール価格のアピール先として最適であるため、このセグメントが用意されています。
6. 特売を開始、終了する日時を入力します。 次のいずれかのタイム ゾーン オプションを選択します。
   - **UTC**:すべての場所で同時に、販売が行われるようにを選択するには世界協定時刻 (UTC) の時間になります。
   - **ローカル**:選択するには、市場に関連付けられている各タイム ゾーンの使用になります。 (複数のタイム ゾーンがある市場では、その市場のタイム ゾーンの 1 つだけが使われます。 米国の場合は東部標準時が使われます)。
7. 他の特売をスケジュールするには、**[新しい販売]** を選択します。 それ以外の場合は、**[価格と使用可能状況]** ページの下部にある **[保存]** を選択し、申請の概要から **[ストアに提出]** を選択します。

> [!NOTE]
> アプリの基本価格より高い価格帯を選択することもできます。 ただし、セール価格がユーザーに表示されるのは、価格がその市場でのアプリの通常価格よりも安い場合のみです。
>
> 特定の市場でアプリの基準価格より高いカスタム価格を既に設定していて、その市場での価格を一時的に下げる場合 (それでもセール価格はアプリの基準価格より高い)、アプリの基準価格より高い価格を選択することが特売において適切であることがあります。 選択の結果として特定の市場でアプリの価格が上がる場合、その (高い) 価格はその市場のユーザーには表示されず、引き続き以前の (低い) 価格でアプリが表示されます。 異なる価格で別の重複する特売をスケジュールする場合にも、ユーザーには適用される最低価格が表示されます。

## <a name="changing-or-canceling-a-scheduled-sale"></a>スケジュールされた特売の変更または取り消し

アプリやアドオンに対して以前にスケジュール設定した特売を変更したり取り消したりするには、新しい申請を作成し、ストアに提出する必要があります。

**スケジュールされた販売を編集するには**

1.  進行中のアプリまたはアドオン申請の **[価格と使用可能状況]** ページで、**[セール価格]** セクションに移動します。
2.  更新する特売を見つけて変更します。
3.  **[価格と使用可能状況]** ページの下部にある **[保存]** をクリックし、申請の概要から **[ストアに提出]** をクリックします。

変更は、申請の認定プロセスが完了した後に有効になります。

> [!IMPORTANT]
> 既に特売が始まっている場合、開始日を編集することはできません。 終了日を編集することはできますが、元の終了日より早く終了するように編集することはお勧めできません。 アプリのストア登録情報にはスケジュールされた終了日が表示されるため、最初に公開した日付より早く特売を終了すると、潜在的なユーザーに不満を感じさせる可能性があります。

 **まだ開始されていない販売をキャンセルするには**

1.  進行中のアプリまたはアドオン申請の **[価格と使用可能状況]** ページで、**[セール価格]** セクションに移動します。
2.  取り消す特売を見つけて **[削除]** をクリックします。
3.  **[価格と使用可能状況]** ページの下部にある **[保存]** をクリックし、申請の概要から **[ストアに提出]** をクリックします。 新しい申請の認定プロセスが完了するまでに特売が開始されていなければ、削除された特売は実行されません。




