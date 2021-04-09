---
title: Voorwaardelijke toegang
description: Meer informatie over het configureren van voorwaardelijke toegang voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics.
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.custom: sqldbrb=1
ms.date: 04/28/2020
tag: azure-synpase
ms.openlocfilehash: c18d235977f1256a10e813fa8e02aa3590366fe1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97936410"
---
# <a name="conditional-access-with-azure-sql-database-and-azure-synapse-analytics"></a>Voorwaardelijke toegang met Azure SQL Database en Azure Synapse Analytics

[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

[Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md)en [Azure Synapse Analytics](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md) bieden ondersteuning voor voorwaardelijke toegang van micro soft.

De volgende stappen laten zien hoe u Azure SQL Database, SQL Managed instance of Azure Synapse configureert om een beleid voor voorwaardelijke toegang af te dwingen.  

## <a name="prerequisites"></a>Vereisten

- U moet Azure SQL Database, een door Azure SQL beheerd exemplaar of een toegewezen SQL-groep in azure Synapse configureren ter ondersteuning van Azure Active Directory-verificatie (Azure AD). Zie voor specifieke stappen [Azure Active Directory verificatie configureren en beheren met SQL database of Azure Synapse](authentication-aad-configure.md).  
- Als Multi-Factor Authentication is ingeschakeld, moet u verbinding maken met een ondersteund hulp programma, zoals de meest recente SQL Server Management Studio (SSMS). Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor meer informatie.  

## <a name="configure-conditional-access"></a>Voorwaardelijke toegang configureren

> [!NOTE]
> In het onderstaande voor beeld wordt Azure SQL Database gebruikt, maar u moet het juiste product selecteren dat u wilt gebruiken voor het configureren van voorwaardelijke toegang.

1. Meld u aan bij de Azure Portal, selecteer **Azure Active Directory** en selecteer vervolgens **voorwaardelijke toegang**. Zie [Azure Active Directory technische Naslag informatie voor voorwaardelijke toegang](../../active-directory/conditional-access/concept-conditional-access-conditions.md).  
   ![Blade voorwaardelijke toegang](./media/conditional-access-configure/conditional-access-blade.png)

2. Klik op de Blade **voorwaardelijke toegang-beleids regels** op **Nieuw beleid**, geef een naam op en klik vervolgens op **regels configureren**.  
3. Onder **toewijzingen** selecteert u **gebruikers en groepen**, **selecteert u gebruikers en groepen selecteren** en selecteert u vervolgens de gebruiker of groep voor voorwaardelijke toegang. Klik op **selecteren** en klik vervolgens op **gereed** om uw selectie te accepteren.  
   ![gebruikers en groepen selecteren](./media/conditional-access-configure/select-users-and-groups.png)  

4. Selecteer **Cloud-apps**, klik op **apps selecteren**. U ziet alle apps die beschikbaar zijn voor voorwaardelijke toegang. Selecteer **Azure SQL database**, klik onderaan op **selecteren** en klik vervolgens op **gereed**.  
   ![SQL Database selecteren](./media/conditional-access-configure/select-sql-database.png)  
   Als **Azure SQL database** niet wordt weer gegeven in de volgende derde scherm afbeelding, voert u de volgende stappen uit:
   - Maak verbinding met uw data base in Azure SQL Database door SSMS te gebruiken met een Azure AD-beheerders account.  
   - Uitvoeren `CREATE USER [user@yourtenant.com] FROM EXTERNAL PROVIDER` .  
   - Meld u aan bij Azure AD en controleer of Azure SQL Database, SQL Managed instance of Azure Synapse worden weer gegeven in de toepassingen in uw Azure AD-exemplaar.  

5. Selecteer **toegangs beheer**, selecteer **toekennen** en controleer vervolgens het beleid dat u wilt Toep assen. Voor dit voor beeld selecteren we **multi-factor Authentication vereisen**.  
   ![Selecteer toegang verlenen](./media/conditional-access-configure/grant-access.png)  

## <a name="summary"></a>Samenvatting

De geselecteerde toepassing (Azure SQL Database) die gebruikmaakt van Azure AD Premium, dwingt nu het geselecteerde beleid voor voorwaardelijke toegang af, **vereiste multi-factor Authentication.**

Voor vragen over Azure SQL Database en Azure Synapse met betrekking tot multi-factor Authentication, neemt u contact op met <MFAforSQLDB@microsoft.com> .  

## <a name="next-steps"></a>Volgende stappen  

Zie [uw data base beveiligen in SQL database](secure-database-tutorial.md)voor een zelf studie.