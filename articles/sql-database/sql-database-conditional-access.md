---
title: 条件付きアクセス - Azure SQL Database および Data Warehouse | Microsoft Doc
description: Azure SQL Database と Data Warehouse 用に条件付きアクセスを構成する方法を説明します。
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: sql-data-warehouse
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
manager: craigg
ms.date: 04/01/2018
ms.openlocfilehash: e7386e2f926132f5657bdb17011dafe1e1c7b101
ms.sourcegitcommit: cc4fdd6f0f12b44c244abc7f6bc4b181a2d05302
ms.translationtype: HT
ms.contentlocale: ja-JP
ms.lasthandoff: 09/25/2018
ms.locfileid: "47063083"
---
# <a name="conditional-access-mfa-with-azure-sql-database-and-data-warehouse"></a>Azure SQL Database と Data Warehouse での条件付きアクセス (MFA)  

Azure [SQL Database](sql-database-technical-overview.md) と [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) の両方で Microsoft の条件付きアクセスがサポートされています。 

> [!NOTE]
> このトピックは Azure SQL サーバーのほか、その Azure SQL サーバーに作成される SQL Database と SQL Data Warehouse の両方に当てはまります。 わかりやすいように、SQL Database という言葉で SQL Database と SQL Data Warehouse の両方を言い表します。

次の手順では、条件付きアクセス ポリシーを適用するように SQL Database を構成する方法を示します。  

## <a name="prerequisites"></a>前提条件  
- Azure Active Directory 認証をサポートするように SQL Database または SQL Data Warehouse を構成する必要があります。 具体的な手順については、「[SQL Database または SQL Data Warehouse で Azure Active Directory 認証を構成して管理する](sql-database-aad-authentication-configure.md)」を参照してください。  
- 多要素認証が有効になっている場合は、最新の SSMS などのサポートされているツールに接続する必要があります。 詳細については、「[SQL Server Management Studio 用に Azure SQL Database の多要素認証を構成する](sql-database-ssms-mfa-authentication-configure.md)」を参照してください。  

## <a name="configure-ca-for-azure-sql-dbdw"></a>Azure SQL DB/DW 用に CA を構成する  
1.  Portal にサインインし、**[Azure Active Directory]** を選択してから **[条件付きアクセス]** を選択します。 詳細については、「[Azure Active Directory の条件付きアクセスに関するテクニカル リファレンス](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-technical-reference)」を参照してください。  
  ![[条件付きアクセス] ブレード](./media/sql-database-conditional-access/conditional-access-blade.png) 
     
2.  **[Conditional Access-Policies]\(条件付きアクセス ポリシー\)** ブレードで、**[新しいポリシー]** をクリックし、名前を指定してから **[Configure rules]\(規則の構成\)** をクリックします。  
3.  **[割り当て]** で、**[ユーザーとグループ]** を選択し、**[ユーザーとグループの選択]** をオンにしてから条件付きアクセス対象のユーザーまたはグループを選択します。 **[選択]** をクリックし、**[完了]** をクリックして選択を確定します。  
  ![[ユーザーとグループの選択]](./media/sql-database-conditional-access/select-users-and-groups.png)  

4.  **[クラウド アプリ]** を選択し、**[アプリを選択]** をクリックします。 条件付きアクセスに使用可能なすべてのアプリが表示されます。 **[Azure SQL Database]** を選択し、下部にある **[選択]** をクリックしてから **[完了]** をクリックします。  
  ![SQL Database を選択する](./media/sql-database-conditional-access/select-sql-database.png)  
  3 番目のスクリーン ショットに示されているように **[Azure SQL Database]** が表示されない場合は、以下の手順を実行します。   
  - AAD 管理者アカウントで SSMS を使用して、Azure SQL DB/DW インスタンスにサインインします。  
  - `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` を実行します。  
  - AAD にサインインし、AAD のアプリケーションに Azure SQL Database と Data Warehouse がリストされていることを確認します。  

5.  **[アクセス制御]** を選択し、**[許可]** を選択してから適用するポリシーを確認します。 この例では、**[多要素認証が必要です]** を選択します。  
  ![アクセス権の付与を選択する](./media/sql-database-conditional-access/grant-access.png)  

## <a name="summary"></a>まとめ  
これで、Azure AD Premium を使用して Azure SQL DB/DW に接続できるようにする選択されたアプリケーション (Azure SQL Database) で、選択された条件付きアクセス ポリシー **[多要素認証が必要です]** が適用されるようになりました。  
Azure SQL Database と Database and Data Warehouse での多要素認証に関するご質問は、MFAforSQLDB@microsoft.com にお問い合わせください。  

## <a name="next-steps"></a>次の手順  

チュートリアルについては、「[Azure SQL データベースのセキュリティ保護](sql-database-security-tutorial.md)」を参照してください。
