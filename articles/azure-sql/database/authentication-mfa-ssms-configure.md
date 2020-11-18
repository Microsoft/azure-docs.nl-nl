---
title: Multi-factor Authentication configureren
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
description: Meer informatie over het gebruik van multi-factor Authentication met SSMS voor Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
ms.custom: has-adal-ref, sqldbrb=3
ms.devlang: ''
ms.topic: how-to
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 08/27/2019
ms.openlocfilehash: 094e40fe55e1ba51b0539d740ecb449a8327d6a6
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841237"
---
# <a name="configure-multi-factor-authentication-for-sql-server-management-studio-and-azure-ad"></a>Multi-factor Authentication configureren voor SQL Server Management Studio en Azure AD
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Dit artikel laat u zien hoe u Azure Active Directory (Azure AD) multi-factor Authentication (MFA) kunt gebruiken met SQL Server Management Studio (SSMS). Azure AD MFA kan worden gebruikt bij het verbinden van SSMS of SqlPackage.exe op [Azure SQL database](sql-database-paas-overview.md), [Azure SQL Managed instance](../managed-instance/sql-managed-instance-paas-overview.md) en [azure Synapse Analytics (voorheen SQL Data Warehouse)](../../synapse-analytics/sql-data-warehouse/sql-data-warehouse-overview-what-is.md). Zie voor een overzicht van multi-factor Authentication [universele verificatie met SQL database, SQL Managed instance en Azure Synapse (SSMS-ondersteuning voor MFA)](../database/authentication-mfa-ssms-overview.md).

> [!IMPORTANT]
> Data bases in Azure SQL Database, Azure SQL Managed instance en Azure Synapse (voorheen SQL Data Warehouse) worden gezamenlijk genoemd in de rest van dit artikel als data bases en de server verwijst naar de [Server](logical-servers.md) die als host fungeert voor de data bases voor Azure SQL database en Azure Synapse.

## <a name="configuration-steps"></a>Configuratiestappen

1. **Een Azure Active Directory configureren** : Zie [uw Azure AD-adres lijst beheren](/previous-versions/azure/azure-services/hh967611(v=azure.100)), [uw on-premises identiteiten integreren met Azure Active Directory](../../active-directory/hybrid/whatis-hybrid-identity.md), [uw eigen domein naam toevoegen aan Azure AD](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/), [Microsoft Azure nu ondersteuning biedt voor Federatie met Windows Server Active Directory](https://azure.microsoft.com/blog/20../../windows-azure-now-supports-federation-with-windows-server-active-directory/)en [Azure AD beheren met Windows Power shell](/previous-versions/azure/jj151815(v=azure.100))(Engelstalig) voor meer informatie.
2. **MFA configureren** : Zie [Wat is Azure AD multi-factor Authentication?](../../active-directory/authentication/concept-mfa-howitworks.md), [voorwaardelijke toegang (MFA) met Azure SQL database en Data Warehouse](conditional-access-configure.md)voor stapsgewijze instructies. (Voor volledige voorwaardelijke toegang is een Premium-Azure Active Directory vereist. Beperkte MFA is beschikbaar met een standaard Azure AD.)
3. **Azure AD-verificatie configureren** : Zie [verbinding maken met SQL database, SQL Managed instance of Azure Synapse met behulp van Azure Active Directory-verificatie](authentication-aad-overview.md)voor stapsgewijze instructies.
4. U kunt **SSMS downloaden** : down load de meest recente SSMS vanuit [down load SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms)op de client computer.

## <a name="connecting-by-using-universal-authentication-with-ssms"></a>Verbinding maken met behulp van universele verificatie met SSMS

De volgende stappen laten zien hoe u verbinding maakt met behulp van de nieuwste SSMS.

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]

1. Als u verbinding wilt maken met behulp van universele verificatie, selecteert u in het dialoog venster **verbinding maken met server** in SQL Server Management Studio (SSMS) de optie **Active Directory-Universal met MFA-ondersteuning**. (Als u **Active Directory universele verificatie** ziet, bent u niet de nieuwste versie van SSMS.)

   ![Scherm afbeelding van het tabblad verbindings eigenschappen in het dialoog venster verbinding maken met server in S S M S. "MyDatabase" is geselecteerd in de vervolg keuzelijst verbinding maken met data base.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)  
2. Vul het vak **gebruikers naam** in met de Azure Active Directory referenties in de indeling `user_name@domain.com` .

   ![Scherm afbeelding van de instellingen van het dialoog venster verbinding maken met server voor het server type, de server naam, de verificatie en de gebruikers naam.](./media/authentication-mfa-ssms-configure/1mfa-universal-connect-user.png)
3. Als u verbinding maakt als een gast gebruiker, hoeft u niet langer het veld AD-domein naam of Tenant-ID voor gast gebruikers te volt ooien omdat SSMS 18. x of hoger deze automatisch herkent. Zie voor meer informatie [Universal Authentication with SQL database, SQL Managed instance en Azure Synapse (SSMS-ondersteuning voor MFA)](../database/authentication-mfa-ssms-overview.md).

   ![Scherm afbeelding van het tabblad verbindings eigenschappen in het dialoog venster verbinding maken met server in S S M S. "MyDatabase" is geselecteerd in de vervolg keuzelijst verbinding maken met data base.](./media/authentication-mfa-ssms-configure/mfa-no-tenant-ssms.png)

   Als u echter verbinding maakt als een gast gebruiker met behulp van SSMS 17. x of ouder, moet u op **Opties** klikken, in het dialoog venster **verbindings eigenschap** en het vak **AD-domein naam of Tenant-id** invullen.

   ![Scherm opname van het tabblad verbindings eigenschappen in het dialoog venster verbinding maken met server in S S M S. de optie AD-domein naam of Tenant-ID-eigenschap is ingevuld.](./media/authentication-mfa-ssms-configure/mfa-tenant-ssms.png)

4. Selecteer **Opties** en geef de Data Base op in het dialoog venster **Opties** . (Als de verbonden gebruiker een gast gebruiker is (dat wil zeggen joe@outlook.com ), moet u het selectie vakje inschakelt en de huidige AD-domein naam of Tenant-id toevoegen als onderdeel van de opties. Zie [universele verificatie met SQL database en Azure Synapse Analytics (SSMS-ondersteuning voor MFA)](../database/authentication-mfa-ssms-overview.md). Klik vervolgens op **Verbinden**.  
5. Wanneer het dialoog venster **Aanmelden bij uw account** wordt weer gegeven, geeft u het account en het wacht woord op van uw Azure Active Directory identiteit. Er is geen wacht woord vereist als een gebruiker deel uitmaakt van een domein dat is federatief met Azure AD.

   ![Scherm afbeelding van het dialoog venster Aanmelden bij uw account voor Azure SQL Database en het Data Warehouse. Het account en het wacht woord zijn ingevuld.](./media/authentication-mfa-ssms-configure/2mfa-sign-in.png)  

   > [!NOTE]
   > Voor universele verificatie met een account waarvoor MFA niet is vereist, kunt u op dit moment verbinding maken. Ga door met de volgende stappen voor gebruikers die MFA vereisen:
   >  

6. Er kunnen twee dialoog vensters voor het instellen van MFA worden weer gegeven. Deze eenmalige bewerking is afhankelijk van de instelling van de MFA-beheerder en kan daarom optioneel zijn. Voor een domein waarvoor MFA is ingeschakeld, is deze stap soms vooraf gedefinieerd (het domein vereist bijvoorbeeld dat gebruikers een Smart Card en pincode gebruiken).

   ![Scherm afbeelding van het dialoog venster Aanmelden bij uw account voor Azure SQL Database en data warehouse met een prompt voor het instellen van aanvullende beveiligings verificatie.](./media/authentication-mfa-ssms-configure/3mfa-setup.png)
  
7. In de tweede mogelijke tijd in het dialoog venster kunt u de details van uw verificatie methode selecteren. De mogelijke opties worden geconfigureerd door de beheerder.

   ![Scherm afbeelding van het dialoog venster aanvullende beveiligings verificatie met opties voor het selecteren en configureren van een verificatie methode.](./media/authentication-mfa-ssms-configure/4mfa-verify-1.png)  
8. De Azure Active Directory stuurt de bevestigings gegevens naar u. Wanneer u de verificatie code ontvangt, voert u deze in het vak **verificatie code invoeren** in en klikt u op **Aanmelden**.

   ![Scherm afbeelding van het dialoog venster Aanmelden bij uw account voor Azure SQL Database en data warehouse met de vraag een verificatie code in te voeren.](./media/authentication-mfa-ssms-configure/5mfa-verify-2.png)  

Wanneer de verificatie is voltooid, maakt SSMS verbinding met het normaal gezien van geldige referenties en toegang tot de firewall.

## <a name="next-steps"></a>Volgende stappen

- Zie voor een overzicht van multi-factor Authentication [universele verificatie met SQL database, SQL Managed instance en Azure Synapse (SSMS-ondersteuning voor MFA)](../database/authentication-mfa-ssms-overview.md).  
- Anderen toegang verlenen tot uw Data Base: [SQL database-verificatie en-autorisatie: toegang verlenen](logins-create-manage.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [Configureer een firewall regel op server niveau met behulp van de Azure Portal](./firewall-configure.md)  
- Wanneer u **Active Directory-universele met MFA-** verificatie gebruikt, is ADAL tracering beschikbaar vanaf [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Standaard uitgeschakeld, u kunt ADAL tracering inschakelen via het menu **extra**, **Opties** , onder **Azure-Services**, Azure- **Cloud**, **ADAL uitvoervenster traceer niveau**, gevolgd door **uitvoer**  in te scha kelen in het menu **weer gave** . De traceringen zijn beschikbaar in het uitvoer venster wanneer u **Azure Active Directory optie** selecteert.