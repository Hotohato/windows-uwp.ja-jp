---
Description: パートナーセンターアカウントにユーザー、グループ、および Azure AD アプリケーションを追加できます。
title: パートナーセンターアカウントにユーザー、グループ、および Azure AD アプリケーションを追加する
ms.date: 10/31/2018
ms.topic: article
keywords: windows 10、uwp、azure ad アプリケーション、aad、ユーザー、グループ、複数のユーザー、マルチユーザー
ms.localizationpriority: medium
ms.openlocfilehash: 41467f51e02f3cc700e3759f33d6fd6eea3ac7a6
ms.sourcegitcommit: ca1b5c3ab905ebc6a5b597145a762e2c170a0d1c
ms.translationtype: MT
ms.contentlocale: ja-JP
ms.lasthandoff: 03/13/2020
ms.locfileid: "79210708"
---
# <a name="add-users-groups-and-azure-ad-applications-to-your-partner-center-account"></a>パートナーセンターアカウントにユーザー、グループ、および Azure AD アプリケーションを追加する

[パートナー](https://partner.microsoft.com/dashboard)センターの **[ユーザー]** セクション ( **[アカウントの設定]** の下) では、Azure Active Directory を使用して、パートナーセンターアカウントにユーザーを追加できます。 各ユーザーには、アカウントへのアクセスを定義するロール (またはカスタムのアクセス許可のセット) が割り当てられます。 また、ユーザーと[Azure AD アプリケーション](#azure-ad-applications)[のグループ](#groups)を追加して、パートナーセンターアカウントへのアクセス権を付与することもできます。

ユーザーをアカウントに追加したら、[アカウントの詳細の編集](#edit)、[ロールやアクセス許可](set-custom-permissions-for-account-users.md)の変更、[ユーザーの削除](#remove)を行うことができます。

> [!IMPORTANT]
> ユーザーをアカウントに追加するには、まず、[パートナーセンターアカウントを組織の Azure Active Directory テナントに関連付ける](associate-azure-ad-with-partner-center.md)必要があります。 

ユーザーを追加する場合は、パートナーセンターアカウントへのアクセスを、[ロールまたはカスタムアクセス許可のセット](set-custom-permissions-for-account-users.md)を割り当てることによって指定する必要があります。 

パートナーセンターのユーザー (グループと Azure AD アプリケーションを含む) はすべて、[パートナーセンターアカウントに関連付けら](associate-azure-ad-with-partner-center.md)れている Azure AD テナントにアクティブなアカウントを持っている必要があることに注意してください。 ユーザーの管理は、1 テナントずつ行います。ユーザーを追加または編集するテナントの管理者アカウントでサインインする必要があります。 パートナーセンターで新しいユーザーを作成すると、そのユーザーのアカウントがサインイン先の Azure AD テナントに作成され、パートナーセンターでユーザーの名前を変更すると、組織の Azure AD テナントにも同じ変更が加えられます。

> [!NOTE]
> 組織で[ディレクトリ統合](https://docs.microsoft.com/previous-versions/azure/azure-services/jj573653(v=azure.100)?redirectedfrom=MSDN)を使用してオンプレミスのディレクトリサービスを Azure AD と同期する場合、パートナーセンターで新しいユーザー、グループ、Azure AD アプリケーションを作成することはできません。 自分 (またはオンプレミスのディレクトリ内の他の管理者) は、パートナーセンターでそれらを表示して追加する前に、オンプレミスのディレクトリに直接作成する必要があります。


<span id="users" />

## <a name="add-users-to-your-partner-center-account"></a>パートナーセンターアカウントにユーザーを追加する

パートナーセンターアカウントにユーザーを追加するには、 **[アカウントの設定]** の **[ユーザー]** ページにアクセスし、[ユーザーの追加] を選択し**ます。** このとき、対象の Azure AD テナントに管理者アカウントでサインインしている必要があります。 

### <a name="add-existing-users"></a>既存ユーザーの追加 

組織のテナントに既に存在するユーザーを選択して、パートナーセンターアカウントへのアクセス権を付与することができます。 

<span id="from-directory" />

1.  歯車アイコン (パートナーセンターの右上隅の近く) を選択し、 **[開発者向け設定]** を選択します。 **[設定]** メニューで **[ユーザー]** を選択します。
2.  **[ユーザー]** ページで、 **[ユーザーの追加]** を選択します。 
3.  表示された一覧から 1 人以上のユーザーを選びます。 検索ボックスを使うと、特定のユーザーを検索できます。
    > [!TIP]
    > パートナーセンターアカウントに複数のユーザーを追加する場合は、同じロールまたはカスタムアクセス許可のセットを割り当てる必要があります。 複数のユーザーを追加し、それぞれ異なるロールまはたアクセス許可を割り当てる場合は、ロールまたはカスタムのアクセス許可のセットごとに次の手順を繰り返します。
4.  ユーザーを選んだら、 **[選択内容の追加]** をクリックします。
5.  **[ロール]** セクションで、選んだユーザーに対して[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。
6.  **[保存]** をクリックします。

### <a name="additional-methods-for-adding-users"></a>ユーザーを追加する別の方法

自分が作業している Azure AD テナントの[グローバル管理者](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)のアクセス許可も持つマネージャーアカウントでサインインしている場合は、パートナーセンターアカウントにユーザーを追加するためのオプションが追加されます。 次のいずれかを選択する必要があります。

-   **既存のユーザーの追加**: 組織のディレクトリに既に存在するユーザーを選択し、上記の方法を使用してパートナーセンターアカウントへのアクセス権を付与します。
-   **新しいユーザーの作成**: 組織のディレクトリとパートナーセンターアカウントの両方に追加する新しいユーザーアカウントを作成します。
-   **外部ユーザーの招待**: 組織のディレクトリに現時点で含まれていないユーザーに、電子メールの招待を送信します。 パートナーセンターアカウントにアクセスするように招待され、Azure AD テナントに新しい[ゲストユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b)アカウントが作成されます。

<span id="new-user" />

### <a name="create-new-users"></a>新しいユーザーの作成

> [!IMPORTANT]
> 新しいユーザーを作成するには、全体管理者アカウントを使って Azure AD テナントにサインインする必要があります。

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、 **[ユーザーの追加]** を選択し、 **[新しいユーザーの作成]** を選択します。
2.  新しいユーザーの姓、名、およびユーザー名を入力します。
3.  新しいユーザーを組織のディレクトリの[全体管理者アカウント](https://docs.microsoft.com/azure/active-directory/users-groups-roles/directory-assign-admin-roles)にする場合には、 **[このユーザーを、すべてのディレクトリ リソースに対してフル コントロールを保持する Azure AD の全体管理者として指定します]** チェック ボックスをオンにします。 これにより、ユーザーは会社の Azure AD のすべての管理機能に完全にアクセスできます。 組織のディレクトリにユーザーを追加して管理することができます (ただし、適切な[ロール/アクセス許可](set-custom-permissions-for-account-users.md)をアカウントに付与しない限り、パートナーセンターには含まれません)。 このチェック ボックスがオンの場合、ユーザーの **[パスワードの回復メール]** を入力する必要があります。
4.  **[このユーザーを、すべてのディレクトリ リソースに対してフル コントロールを保持する Azure AD の全体管理者として指定します]** チェック ボックスをオンにした場合は、ユーザーが自分のパスワードを回復するときに使用するメール アドレスを入力します。
5.  **[グループ メンバーシップ]** セクションで、新しいユーザーを追加するグループを選びます。
6.  **[ロール]** セクションで、このユーザーの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。
7.  **[保存]** をクリックします。
8.  確認ページに、一時的なパスワードなどの新しいユーザーのログイン情報が表示されます。 必ずこの情報をメモして、新しいユーザーに提供してください。このページを閉じると、一時的なパスワードにはアクセスできなくなります。


<span id="email" />

### <a name="invite-outside-users"></a>外部ユーザーの招待

> [!IMPORTANT]
> 外部ユーザーを招待するには、全体管理者アカウントを使って Azure AD テナントにサインインする必要があります。

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、 **[ユーザーの追加]** を選択し、 **[電子メールでユーザーを招待]** を選択します。
1.  1 つ以上のメール アドレス (最大 10 個) を、コンマまたはセミコロンで区切って入力します。
2.  **[ロール]** セクションで、このユーザーの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。
3.  **[保存]** をクリックします。

ユーザーにはデベロッパー センター アカウントへの参加を求める招待メールが送られ、ユーザー用に新しい[ゲスト ユーザー](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) アカウントが Azure AD テナント内に作成されます。 各ユーザーは、デベロッパー センター アカウントにアクセスするには招待を承諾する必要があります。

招待を再送信する必要がある場合は、 **[ユーザー]** ページでユーザーを見つけ、メール アドレス (または **[承認待ちの招待]** というテキスト) を選択します。 次に、ページの下部の **[招待状の再送信]** をクリックします。

> [!IMPORTANT]
> パートナーセンターのアカウントに参加するユーザー以外のユーザーには、他のユーザーと同じロールおよびアクセス許可を割り当てることができます。 ただし、外部ユーザーは、アプリを Microsoft Store に関連付けたり、パッケージを作成して Microsoft Store にアップロードするなどの特定のタスクを Visual Studio で実行できなくなります。 ユーザーがこれらのタスクを実行する必要がある場合、 **[外部ユーザーの招待]** ではなく **[新しいユーザーの作成]** を選択します。 (これらのユーザーを既存の Azure AD テナントに追加しない場合、[新しいテナントを作成](../publish/associate-azure-ad-with-partner-center.md#create-a-brand-new-azure-ad-to-associate-with-your-partner-center-account)した後、そのテナントにそれらのユーザーの新しいユーザー アカウントを作成することができます)。 


### <a name="changing-a-users-directory-password"></a>ユーザーのディレクトリ パスワードを変更する

ユーザー アカウントの作成時に **[パスワードの回復メール]** を設定しておくと、そのユーザーは、パスワードを変更する必要がある場合に自分で処理できます。 ユーザーのパスワードは、以下の手順に従って更新することもできます (ユーザーのパスワードを変更するには、Azure AD テナントに対して全体管理者アカウントでサインインしている必要があります)。 これにより、Azure AD テナント内のユーザーのパスワードと、パートナーセンターへのアクセスに使用するパスワードが変更されることに注意してください。 

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、編集するユーザーアカウントの名前を選択します。
2.  ページの下部にある **[パスワードのリセット]** ボタンを選択します。
3.  確認ページが表示され、一時的なパスワードなどのユーザーのログイン情報が通知されます。

    > [!IMPORTANT]
    >この情報を必ず印刷またはコピーし、ユーザーに提供してください。このページから移動した後に一時パスワードにアクセスすることはできないためです。   

<span id="groups" />

## <a name="add-groups-to-your-partner-center-account"></a>パートナーセンターアカウントにグループを追加する

組織のディレクトリからパートナーセンターアカウントにグループを追加できます。 その場合、グループのメンバーであるユーザー全員が、グループに割り当てられた役割に関連付けられているアクセス許可で、デベロッパー センター アカウントにアクセスできるようになります。

### <a name="add-groups-from-your-organizations-directory"></a>組織のディレクトリからグループを追加する

1.  歯車アイコン (パートナーセンターの右上隅の近く) を選択し、 **[開発者向け設定]** を選択します。 **[設定]** メニューで **[ユーザー]** を選択します。
2. **[ユーザー]** ページで、 **[グループの追加]** を選択します。
2.  表示された一覧から 1 つ以上のグループを選びます。 検索ボックスを使うと、特定のグループを検索できます。
    > [!TIP]
    > パートナーセンターアカウントに追加するグループを複数選択した場合は、同じロールまたはカスタムアクセス許可のセットを割り当てる必要があります。 複数のグループを追加し、それぞれ異なるロールまはたアクセス許可を割り当てる場合は、ロールまたはカスタムのアクセス許可のセットごとに次の手順を繰り返します。

3.  グループを選んだら、 **[選択内容の追加]** をクリックします。
4.  **[ロール]** セクションで、選んだグループの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。 グループのすべてのメンバーは、個々のアカウントに関連付けられているロール/アクセス許可に関係なく、グループに適用したアクセス許可を持つパートナーセンターアカウントにアクセスできます。
5.  **[保存]** をクリックします。


### <a name="create-a-new-group-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account"></a>組織のディレクトリに新しいグループアカウントを作成し、パートナーセンターアカウントに追加します。

パートナーセンターに新しいグループへのアクセスを許可する場合は、 **[ユーザー]** セクションに新しいグループを作成できます。 新しいグループは、パートナーセンターアカウントだけではなく、組織のディレクトリに作成されることに注意してください。

1.  **[ユーザー]** ページ ( **[開発者設定]** の下) で、 **[グループの追加]** をクリックします。
2.  次のページで、 **[新しいグループ]** を選択します。
3.  新しいグループの表示名を入力します。
4.  グループの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。 グループのすべてのメンバーは、個々のアカウントに関連付けられているロール/アクセス許可に関係なく、グループに適用したアクセス許可を持つパートナーセンターアカウントにアクセスできます。
5.  表示された一覧から、新しいグループに割り当てるユーザーを選びます。 検索ボックスを使うと、特定のユーザーを検索できます。
6.  ユーザーを選んだら **[選択内容の追加]** をクリックします。選んだユーザーが新しいグループに追加されます。
7.  **[保存]** をクリックします。


<span id="azure-ad-applications" />

## <a name="add-azure-ad-applications-to-your-partner-center-account"></a>パートナーセンターアカウントに Azure AD アプリケーションを追加する

組織の Azure AD の一部であるアプリケーションまたはサービスにパートナーセンターアカウントへのアクセスを許可することができます。 これらの Azure AD アプリケーション ユーザー アカウントを使って、[Microsoft Store のサービス](../monetize/using-windows-store-services.md)によって提供される REST API を呼び出すことができます。


### <a name="add-azure-ad-applications-from-your-organizations-directory"></a>組織のディレクトリから Azure AD アプリケーションを追加する

1.  1.  歯車アイコン (パートナーセンターの右上隅の近く) を選択し、 **[開発者向け設定]** を選択します。 **[設定]** メニューで **[ユーザー]** を選択します。
2. **[ユーザー]** ページで、 **[Azure AD アプリケーションの追加]** を選択します。
3.  表示された一覧から 1 つ以上の Azure AD アプリケーションを選びます。 検索ボックスを使うと、特定の Azure AD アプリケーションを検索できます。
    > [!TIP]
    > パートナーセンターアカウントに追加する複数の Azure AD アプリケーションを選択した場合は、同じロールまたはカスタムアクセス許可のセットを割り当てる必要があります。 複数の Azure AD アプリケーションを追加し、それぞれ異なるロールまはたアクセス許可を割り当てる場合は、ロールまたはカスタムのアクセス許可のセットごとに次の手順を繰り返します。

4.  Azure AD アプリケーションを選んだら、 **[選択内容の追加]** をクリックします。
5.  **[ロール]** セクションで、選んだ Azure AD アプリケーションの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。
6.  **[保存]** をクリックします。


### <a name="create-a-new-azure-ad-application-account-in-your-organizations-directory-and-add-it-to-your-partner-center-account"></a>組織のディレクトリに新しい Azure AD アプリケーションアカウントを作成し、パートナーセンターアカウントに追加します。

新しい Azure AD アプリケーションアカウントにパートナーセンターのアクセス権を付与する場合は、 **[ユーザー]** セクションで作成できます。 これにより、パートナーセンターアカウントだけでなく、組織のディレクトリに新しいアカウントが作成されることに注意してください。

> [!TIP]
> この Azure AD アプリケーションをパートナーセンターの認証に使用し、ユーザーが直接アクセスする必要がない場合は、その値がディレクトリ内の他の Azure AD アプリケーションによって使用されていない限り、**応答 URL**と**アプリ ID URI**の任意の有効なアドレスを入力できます。

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、 **[Azure AD アプリケーションの追加]** を選択します。
2.  次のページで、 **[新しい Azure AD アプリケーション]** を選択します。
3.  新しい Azure AD アプリケーションの **[応答 URL]** を入力します。 これは、ユーザーがサインインして、Azure AD アプリケーションを使うことができるようになる URL です ("アプリ URL" や "サインオン URL" と呼ばれる場合もあります)。 **[応答 URL]** は 256 文字以内にする必要があり、ディレクトリ内で一意でなければなりません。
4.  新しい Azure AD アプリケーションの **[アプリ ID URI]** を入力します。 これは、Azure AD アプリケーションの論理識別子であり、シングル サインオン要求を Azure AD に送るときに提供されます。 **[アプリ ID URI]** は、ディレクトリ内の各 Azure AD アプリケーションで一意の値する必要があります。またこの URI は、256 文字までにする必要があります。 **アプリ ID URI** について詳しくは、「[Azure Active Directory とアプリケーションの統合](https://docs.microsoft.com/azure/active-directory/develop/active-directory-integrating-applications#changing-the-application-registration-to-support-multi-tenant)」をご覧ください。
5.  **[ロール]** セクションで、Azure AD アプリケーションの[ロールまたはカスタムのアクセス許可](set-custom-permissions-for-account-users.md)を指定します。
6.  **[保存]** をクリックします。

Azure AD アプリケーションを追加または作成した後、 **[ユーザー]** セクションに戻ってアプリケーションの名前を選択すると、テナント ID、クライアント ID、応答 URL、アプリ ID URI など、アプリケーションの設定を確認できます。

> [!NOTE]
> [Microsoft Store サービス](../monetize/using-windows-store-services.md)が提供する REST API を使用する場合は、サービスへの呼び出しの認証に使用する Azure AD アクセス トークンを取得するために、このページに表示されているテナント ID とクライアント ID の値が必要になります。   

<span id="manage-keys" />

### <a name="manage-keys-for-an-azure-ad-application"></a>Azure AD アプリケーションのキーを管理する

Azure AD アプリケーションが Microsoft Azure AD でデータの読み取りや書き込みを行う場合は、キーが必要になります。 パートナーセンターで情報を編集することで、Azure AD アプリケーションのキーを作成できます。 また、不要になったキーを削除することもできます。

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、Azure AD アプリケーションの名前を選択します。
    > [!TIP]
    > Azure AD アプリケーションの名前をクリックすると、Azure AD アプリケーションのすべてのアクティブなキーが表示されます。これには、キーが作成された日付と有効期限が含まれます。 不要になったキーを削除するには、 **[削除]** をクリックします。

2.  新しいキーを追加するには、 **[新しいキーの追加]** を選択します。
3.  **[クライアント ID]** と **[キー]** の値を示す画面が表示されます。
    > [!IMPORTANT]
    >この情報を必ず印刷またはコピーしてください。この情報は、このページから移動した後に再度アクセスすることはできないため、 してください。

4.  さらにキーを作成する場合は、 **[別のキーを追加]** を選択します。

<span id="edit" />

## <a name="edit-a-user-group-or-azure-ad-application"></a>ユーザー、グループ、または Azure AD アプリケーションの編集

パートナーセンターアカウントにユーザー、グループ、または Azure AD アプリケーションを追加した後、アカウント情報を変更することができます。 

> [!IMPORTANT]
> [ロールまたはアクセス許可](set-custom-permissions-for-account-users.md)に加えられた変更は、パートナーセンターへのアクセスにのみ影響します。 その他の変更 (ユーザーの名前またはグループメンバーシップの変更、Azure AD アプリケーションの応答 URL、アプリ ID URI の変更など) はすべて、パートナーセンターアカウントに加えて、組織の Azure AD テナントにも反映されます。 

1.  **[ユーザー]** ページ ( **[アカウントの設定]** の下) で、編集するユーザー、グループ、または Azure AD アプリケーションアカウントの名前を選択します。
2.  必要な変更を行います。 編集できる項目は次のとおりです。
    -   **ユーザー**の場合は、ユーザーの姓、名、またはユーザー名を編集できます。 また、 **[グループ メンバーシップ]** セクションで、グループを選ぶまたは選択を解除して、ユーザーのグループ メンバーシップを更新することもできます。
    -   **グループ**の場合は、グループの名前を編集できます。 (グループ メンバーシップを更新するには、グループに追加またはグループから削除するユーザーを編集し、 **[グループ メンバーシップ]** セクションを変更します。)
    -   **Azure AD アプリケーション**の場合は、 **[応答 URL]** または **[アプリケーション ID/URI]** に新しい値を入力できます。
    これらの変更は、組織のディレクトリとパートナーセンターアカウントで行われることに注意してください。
3.  パートナーセンターへのアクセスに関連する変更については、適用するロールを選択または選択解除するか、 **[アクセス許可のカスタマイズ]** を選択して必要な変更を行います。 これらの変更は、パートナーセンターへのアクセスにのみ影響し、組織の Azure AD テナント内のアクセス許可は変更されません。
3.  **[保存]** をクリックします。


## <a name="view-history-for-account-users"></a>アカウント ユーザーの履歴の表示

アカウント所有者は、所有者がアカウントに追加した任意のユーザーに関する詳細な閲覧履歴を参照できます。

**[ユーザー]** ページ ( **[アカウントの設定]** の下) で、閲覧の履歴を確認するユーザーの **[最後のアクティビティ]** の下に表示されるリンクを選択します。 ユーザーが過去 30 日間にアクセスしたすべてのページの URL を参照できます。

<span id="remove" />

## <a name="remove-users-groups-and-azure-ad-applications"></a>ユーザー、グループ、および Azure AD アプリケーションの削除

パートナーセンターアカウントからユーザー、グループ、または Azure AD アプリケーションを削除するには、 **[ユーザー]** ページで、名前によって表示される **[削除]** リンクを選択します。 削除することを確認した後、そのユーザー、グループ、または Azure AD アプリケーションは、パートナーセンターアカウントにアクセスできなくなります (後でもう一度追加しない限り)。

> [!IMPORTANT]
> ユーザー、グループ、または Azure AD アプリケーションを削除すると、パートナーセンターアカウントにアクセスできなくなります。 この操作により、組織のディレクトリからユーザー、グループ、または Azure AD アプリケーションが削除されることは**ありません**。

 

