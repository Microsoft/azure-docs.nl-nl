---
title: Verificatie en gebruikers machtigingen Azure Analysis Services | Microsoft Docs
description: In dit artikel wordt beschreven hoe Azure Analysis Services Azure Active Directory (Azure AD) gebruikt voor identiteits-en gebruikers verificatie.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 551bae56565140da3754e74a23b1cc18087f1171
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96487436"
---
# <a name="authentication-and-user-permissions"></a>Verificatie en gebruikersmachtigingen

Azure Analysis Services maakt gebruik van Azure Active Directory (Azure AD) voor identiteits-en gebruikers verificatie. Elke gebruiker die een Azure Analysis Services server maakt, beheert of er verbinding mee maakt, moet een geldige gebruikers identiteit hebben in een [Azure AD-Tenant](../active-directory/fundamentals/active-directory-whatis.md) in hetzelfde abonnement.

Azure Analysis Services ondersteunt [Azure AD B2B-samen werking](../active-directory/external-identities/what-is-b2b.md). Met B2B kunnen gebruikers van buiten een organisatie worden uitgenodigd als gast gebruikers in een Azure AD-adres lijst. Gasten kunnen afkomstig zijn uit een andere Azure AD-Tenant Directory of een geldig e-mail adres. Zodra de gebruiker is uitgenodigd en de uitnodiging is verzonden via e-mail vanuit Azure, wordt de identiteit van de gebruiker toegevoegd aan de Tenant Directory. Deze identiteiten kunnen worden toegevoegd aan beveiligings groepen of als leden van een server beheerder of databaserol.

![Architectuur van Azure Analysis Services-verificatie](./media/analysis-services-manage-users/aas-manage-users-arch.png)

## <a name="authentication"></a>Verificatie

Alle client toepassingen en hulpprogram ma's gebruiken een of meer van de Analysis Services- [client bibliotheken](/analysis-services/client-libraries?view=azure-analysis-services-current&preserve-view=true) (AMO, MSOLAP, ADOMD) om verbinding te maken met een server. 

Alle drie client bibliotheken ondersteunen zowel interactieve als Azure AD-stroom en niet-interactieve verificatie methoden. De twee niet-interactieve methoden, Active Directory wacht woord en Active Directory geïntegreerde verificatie methoden kunnen worden gebruikt in toepassingen die gebruikmaken van AMOMD en MSOLAP. Deze twee methoden resulteren nooit in pop-updialoogvensters.

Client toepassingen zoals Excel en Power BI Desktop, en hulpprogram ma's zoals SSMS en Analysis Services project extension for Visual Studio installeren de nieuwste versies van de bibliotheken wanneer deze worden bijgewerkt naar de nieuwste versie. De uitbrei ding Power BI Desktop, SSMS en Analysis Services Projects wordt maandelijks bijgewerkt. Excel wordt [bijgewerkt met Microsoft 365](https://support.microsoft.com/office/when-do-i-get-the-newest-features-for-microsoft-365-da36192c-58b9-4bc9-8d51-bb6eed468516). Microsoft 365 updates zijn minder frequent en sommige organisaties gebruiken het uitgestelde kanaal, wat betekent dat updates tot drie maanden worden uitgesteld.

Afhankelijk van de client toepassing of het hulp programma dat u gebruikt, kan het type verificatie en de manier waarop u zich aanmeldt anders zijn. Elke toepassing kan verschillende functies ondersteunen voor het maken van verbinding met Cloud Services, zoals Azure Analysis Services.

Power BI Desktop, Visual Studio en SSMS ondersteunen Active Directory universele verificatie, een interactieve methode die ook ondersteuning biedt voor Azure AD Multi-Factor Authentication (MFA). Azure AD MFA helpt bij het beveiligen van de toegang tot gegevens en toepassingen tijdens een eenvoudig aanmeldings proces. Het biedt krachtige verificatie met verschillende verificatie opties (telefoon oproep, tekst bericht, Smart Cards met pincode of melding van een mobiele app). Bij interactieve MFA met Azure AD kunnen er pop-upvensters worden weergegeven voor validatie. **Universele verificatie wordt aanbevolen**.

Als u zich aanmeldt bij Azure met behulp van een Windows-account en universele verificatie niet is geselecteerd of beschikbaar (Excel), is [Active Directory Federation Services (AD FS)](/windows-server/identity/ad-fs/deployment/how-to-connect-fed-azure-adfs) vereist. Met Federatie worden Azure AD-en Microsoft 365-gebruikers geverifieerd met on-premises referenties en kunnen ze toegang krijgen tot Azure-resources.

### <a name="sql-server-management-studio-ssms"></a>SQL Server Management Studio (SSMS)

Azure Analysis Services-servers ondersteunen verbindingen van [SSMS v-17.1](/sql/ssms/download-sql-server-management-studio-ssms) en hoger met behulp van Windows-verificatie, Active Directory wachtwoord verificatie en Active Directory universele verificatie. Over het algemeen is het raadzaam om Active Directory universele verificatie te gebruiken omdat:

*  Ondersteunt interactieve en niet-interactieve verificatie methoden.

*  Biedt ondersteuning voor Azure B2B-gast gebruikers die zijn uitgenodigd voor Azure als Tenant. Wanneer u verbinding maakt met een server, moeten gast gebruikers Active Directory universele verificatie selecteren wanneer ze verbinding maken met de server.

*  Ondersteunt Multi-Factor Authentication (MFA). Azure AD MFA helpt de toegang tot gegevens en toepassingen te beschermen met een reeks verificatie opties: telefoon oproep, SMS-bericht, Smart Cards met pincode of mobiele app-melding. Bij interactieve MFA met Azure AD kunnen er pop-upvensters worden weergegeven voor validatie.

### <a name="visual-studio"></a>Visual Studio

Visual Studio maakt verbinding met Azure Analysis Services met behulp van Active Directory universele verificatie met MFA-ondersteuning. Gebruikers wordt gevraagd zich aan te melden bij Azure bij de eerste implementatie. Gebruikers moeten zich aanmelden bij Azure met een account met beheerders machtigingen voor de server op de server waarop ze zijn geïmplementeerd. Bij de eerste keer dat u zich bij Azure aanmeldt, wordt er een token toegewezen. Het token wordt in het geheugen opgeslagen in de cache voor toekomstige reconnecties.

### <a name="power-bi-desktop"></a>Power BI Desktop

Power BI Desktop maakt verbinding met Azure Analysis Services met behulp van Active Directory universele verificatie met MFA-ondersteuning. Gebruikers wordt gevraagd zich aan te melden bij Azure bij de eerste verbinding. Gebruikers moeten zich aanmelden bij Azure met een account dat is opgenomen in een server beheerder of databaserol.

### <a name="excel"></a>Excel

Excel-gebruikers kunnen verbinding maken met een server met behulp van een Windows-account, een organisatie-ID (e-mail adres) of een extern e-mail adres. Externe e-mail-identiteiten moeten aanwezig zijn in azure AD als gast gebruiker.

## <a name="user-permissions"></a>Gebruikersmachtigingen

**Server beheerders** zijn specifiek voor een Azure Analysis Services Server-exemplaar. Ze maken verbinding met hulp middelen als Azure Portal, SSMS en Visual Studio voor het uitvoeren van taken zoals het toevoegen van data bases en het beheren van gebruikers rollen. De gebruiker die de server maakt, wordt standaard automatisch toegevoegd als Analysis Services server beheerder. Andere beheerders kunnen worden toegevoegd met behulp van Azure Portal of SSMS. Server beheerders moeten een account hebben in de Azure AD-Tenant in hetzelfde abonnement. Zie [Server beheerders beheren](analysis-services-server-admins.md)voor meer informatie. 

**Database gebruikers** maken verbinding met model databases met behulp van client toepassingen zoals Excel of Power bi. Gebruikers moeten worden toegevoegd aan database rollen. Database rollen definiëren beheerders-, proces-of lees machtigingen voor een Data Base. Het is belang rijk om te begrijpen dat database gebruikers in een rol met beheerders bevoegdheden een andere zijn dan Server beheerders. Server beheerders zijn echter standaard ook database beheerders. Zie [Data Base-rollen en-gebruikers beheren](analysis-services-database-users.md)voor meer informatie.

**Azure-resource-eigen aren**. Resource-eigen aren beheren resources voor een Azure-abonnement. Resource-eigen aren kunnen Azure AD-gebruikers identiteiten toevoegen aan eigenaar-of Inzender rollen binnen een abonnement met behulp van **toegangs beheer** in azure portal of met Azure Resource Manager sjablonen. 

![Toegangs beheer in Azure Portal](./media/analysis-services-manage-users/aas-manage-users-rbac.png)

Rollen op dit niveau zijn van toepassing op gebruikers of accounts die taken moeten uitvoeren die kunnen worden uitgevoerd in de portal of met behulp van Azure Resource Manager sjablonen. Zie op [rollen gebaseerd toegangs beheer voor Azure (Azure RBAC)](../role-based-access-control/overview.md)voor meer informatie. 

## <a name="database-roles"></a>Database rollen

 Rollen die zijn gedefinieerd voor een tabellair model zijn database rollen. Dat wil zeggen dat de rollen leden bevatten die bestaan uit Azure AD-gebruikers en-beveiligings groepen die specifieke machtigingen hebben die de actie definiëren die leden kunnen uitvoeren op een model database. Een databaserol wordt gemaakt als een afzonderlijk object in de database en is alleen van toepassing op de database waarin die rol is gemaakt.   
  
 Wanneer u een nieuw tabellaire model project maakt, heeft het model project standaard geen rollen. Rollen kunnen worden gedefinieerd met behulp van het dialoog venster rollen beheer in Visual Studio. Wanneer rollen worden gedefinieerd tijdens het ontwerp van een model project, worden ze alleen toegepast op de model werkruimte database. Wanneer het model wordt geïmplementeerd, worden dezelfde rollen toegepast op het geïmplementeerde model. Nadat een model is geïmplementeerd, kunnen server-en database beheerders rollen en leden beheren met behulp van SSMS. Zie [Data Base-rollen en-gebruikers beheren](analysis-services-database-users.md)voor meer informatie.
  
## <a name="next-steps"></a>Volgende stappen

[Toegang tot resources beheren met Azure Active Directory groepen](../active-directory/fundamentals/active-directory-manage-groups.md)   
[Database rollen en-gebruikers beheren](analysis-services-database-users.md)  
[Serverbeheerders beheren](analysis-services-server-admins.md)  
[Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../role-based-access-control/overview.md)