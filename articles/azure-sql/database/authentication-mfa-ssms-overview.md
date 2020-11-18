---
title: Multi-factor Azure Active Directory-verificatie gebruiken
description: Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics ondersteunen verbindingen van SQL Server Management Studio (SSMS) met behulp van Active Directory universele verificatie.
services: sql-database
ms.service: sql-db-mi
ms.subservice: security
titleSuffix: Azure SQL Database & SQL Managed Instance & Azure Synapse Analytics
ms.custom: seoapril2019, has-adal-ref, sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: GithubMirek
ms.author: mireks
ms.reviewer: vanto
ms.date: 09/28/2020
tags: azure-synapse
ms.openlocfilehash: 9afad44bcf67478a81e75c17d0ff8ffc6d8c65aa
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94841125"
---
# <a name="using-multi-factor-azure-active-directory-authentication"></a>Multi-factor Azure Active Directory-verificatie gebruiken
[!INCLUDE[appliesto-sqldb-sqlmi-asa](../includes/appliesto-sqldb-sqlmi-asa.md)]

Azure SQL Database, Azure SQL Managed instance en Azure Synapse Analytics ondersteunen verbindingen van [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) met behulp van *Azure Active Directory-Universal met MFA-* verificatie. In dit artikel worden de verschillen beschreven tussen de verschillende verificatie opties en ook de beperkingen die zijn gekoppeld aan het gebruik van universele verificatie in Azure Active Directory (Azure AD) voor Azure SQL.

**Download de nieuwste versie van SSMS** Download op de clientcomputer de meest recente versie van SSMS. Ga hiervoor naar de pagina [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio (SSMS) downloaden). 

[!INCLUDE[ssms-connect-azure-ad](../includes/ssms-connect-azure-ad.md)]


Voor alle functies die in dit artikel worden besproken, hebt u ten minste versie 17.2 van juli 2017 nodig. Het meest recente dialoogvenster Verbinding maken met server moet er ongeveer uitzien als in de volgende afbeelding:

  ![Scherm afbeelding van het dialoog venster verbinding maken met server in SQL Server Management Studio, met de instellingen voor het server type, de server naam en de verificatie.](./media/authentication-mfa-ssms-overview/1mfa-universal-connect.png)

## <a name="authentication-options"></a>Verificatieopties

Er zijn twee niet-interactieve verificatie modellen voor Azure AD, die kunnen worden gebruikt in veel verschillende toepassingen (ADO.NET, JDCB, ODC, enzovoort). Voor deze twee methoden worden nooit pop-upvensters weergegeven:

- `Azure Active Directory - Password`
- `Azure Active Directory - Integrated`

De interactieve methode die ook Azure AD Multi-Factor Authentication (MFA) ondersteunt, is: 

- `Azure Active Directory - Universal with MFA`

Azure AD MFA helpt de toegang tot gegevens en toepassingen te beschermen terwijl de vraag naar gebruikers wordt gevergaderd voor een eenvoudig aanmeldings proces. Azure MFA biedt krachtige verificatie met een scala aan gebruikersvriendelijke verificatieopties (zoals telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app), zodat gebruikers de gewenste methode kunnen kiezen. Bij interactieve MFA met Azure AD kunnen er pop-upvensters worden weergegeven voor validatie.

Zie [multi-factor Authentication](../../active-directory/authentication/concept-mfa-howitworks.md)voor een beschrijving van Azure AD multi-factor Authentication.
Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domeinnaam of tenant-id als parameter

Vanaf [SSMS versie 17](/sql/ssms/download-sql-server-management-studio-ssms)kunnen gebruikers die in de huidige Azure AD worden geïmporteerd van andere Azure Active Directory-gebruikers, de naam van het Azure AD-domein of de Tenant-id opgeven wanneer ze verbinding maken. Gastgebruikers zijn gebruikers die zijn uitgenodigd bij andere Azure ADs, Microsoft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts zoals gmail.com. Met deze informatie kan `Azure Active Directory - Universal with MFA` verificatie de juiste verificatie-instantie identificeren. Deze optie is ook vereist voor de ondersteuning van Microsoft-accounts (MSA) zoals outlook.com, hotmail.com, live.com, of niet-MSA-accounts. 

Alle gast gebruikers die willen worden geverifieerd met universele verificatie, moeten hun Azure AD-domein naam of Tenant-ID invoeren. Deze para meter vertegenwoordigt de huidige naam van het Azure AD-domein of de Tenant-ID waaraan de logische Azure SQL-Server is gekoppeld. Als bijvoorbeeld de logische SQL-Server is gekoppeld aan het Azure AD-domein `contosotest.onmicrosoft.com` , waarbij `joe@contosodev.onmicrosoft.com` de gebruiker wordt gehost als een geïmporteerde gebruiker uit het Azure AD `contosodev.onmicrosoft.com` -domein, is de domein naam die is vereist voor de verificatie van deze gebruiker `contosotest.onmicrosoft.com` . Als de gebruiker een systeem eigen gebruiker is van de Azure AD die is gekoppeld aan een logische SQL-Server en geen MSA-account is, is geen domein naam of Tenant-ID vereist. De para meter invoeren (vanaf SSMS versie 17,2):


1. Open een verbinding in SSMS. Voer de naam van uw server in en selecteer **Azure Active Directory-Universal met MFA-** verificatie. Voeg de **gebruikers naam** toe waarmee u zich wilt aanmelden.
1. Selecteer het vak **Opties** en ga naar het tabblad **verbindings eigenschappen** . In het dialoog venster **verbinding maken met data base vult u** het dialoog venster in voor uw data base. Schakel het selectievakje **AD-domeinnaam of tenant-id** in en geef de gegevens van de verificatie-instantie op, zoals de domeinnaam (**contosotest.onmicrosoft.com**) of de GUID van de tenant-id. 

   ![Scherm afbeelding van het tabblad verbindings eigenschappen met de instellingen voor verbinding maken met data base en AD-domein naam of Tenant-ID.](./media/authentication-mfa-ssms-overview/mfa-tenant-ssms.png)

Als u SSMS 18. x of hoger uitvoert, is de AD-domein naam of Tenant-ID niet langer nodig voor gast gebruikers omdat 18. x of hoger deze automatisch herkent.

   ![Scherm afbeelding van het tabblad verbindings eigenschappen in het dialoog venster verbinding maken met server in S S M S. "MyDatabase" is geselecteerd in het veld verbinding maken met data base.](./media/authentication-mfa-ssms-overview/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD B2B

> [!IMPORTANT]
> Ondersteuning voor gast gebruikers om verbinding te maken met Azure SQL Database, SQL Managed instance en Azure Synapse zonder dat een groep deel uitmaakt van een **open bare preview**. Zie voor meer informatie [Azure AD-gast gebruikers maken en instellen als een Azure AD-beheerder](authentication-aad-guest-users.md).

Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gast gebruikers (Zie [Wat is Azure B2B Collaboration](../../active-directory/external-identities/what-is-b2b.md)) kunnen verbinding maken met SQL database en Azure Synapse alleen als onderdeel van de leden van een groep die in de gekoppelde Azure AD is gemaakt en hand matig worden toegewezen met de instructie [Create User (Transact-SQL)](/sql/t-sql/statements/create-user-transact-sql) in een bepaalde data base. Als bijvoorbeeld `steve@gmail.com` wordt uitgenodigd voor Azure AD `contosotest` (met het Azure AD `contosotest.onmicrosoft.com` -domein), wordt een Azure AD-groep, zoals `usergroup` moet worden gemaakt in de Azure AD die het `steve@gmail.com` lid bevat. Vervolgens moet deze groep worden gemaakt voor een specifieke data base (bijvoorbeeld `MyDatabase` ) door een Azure AD SQL-beheerder of Azure AD dbo door de Transact-SQL-instructie uit te voeren `CREATE USER [usergroup] FROM EXTERNAL PROVIDER` . 

Nadat de database gebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden `MyDatabase` met behulp van de verificatie optie SSMS `Azure Active Directory – Universal with MFA` . Standaard `usergroup` heeft alleen de machtiging verbinden. Alle verdere gegevens toegang moet worden [verleend](/sql/t-sql/statements/grant-transact-sql) in de data base door een gebruiker met voldoende bevoegdheden. 

> [!NOTE]
> Voor SSMS 17. x, die `steve@gmail.com` als gast gebruiker wordt gebruikt, moet u het vak **AD-domein naam of Tenant-id** controleren en de naam van het AD-domein toevoegen `contosotest.onmicrosoft.com` in het dialoog venster Eigenschappen van de **verbinding** . De optie **AD-domein naam of Tenant-id** wordt alleen ondersteund voor de **Azure Active Directory-Universal met MFA-** verificatie. Anders wordt het selectie vakje grijs weer gegeven.

## <a name="universal-authentication-limitations"></a>Beperkingen voor universele verificatie

- SSMS en SqlPackage.exe zijn de enige hulpprogramma's die momenteel zijn ingeschakeld voor MFA via universele verificatie van Active Directory.
- SSMS-versie 17,2 ondersteunt gelijktijdige toegang met meerdere gebruikers met behulp van universele verificatie met MFA. Voor SSMS versie 17,0 en 17,1 beperkt het hulp programma een aanmelding voor een exemplaar van SSMS met behulp van universele verificatie voor één Azure Active Directory account. Als u zich wilt aanmelden als een ander Azure AD-account, moet u een ander exemplaar van SSMS gebruiken. Deze beperking is beperkt tot Active Directory universele verificatie. u kunt zich aanmelden bij een andere server met `Azure Active Directory - Password` verificatie, `Azure Active Directory - Integrated` verificatie of `SQL Server Authentication` .
- SSMS ondersteunt universele verificatie van Active Directory voor Objectverkenner, Query-editor en visualisatie van Query Store.
- SSMS versie 17.2 biedt ondersteuning voor de DacFx-wizard voor het exporteren/extraheren/implementeren van databases. Zodra een specifieke gebruiker is geverifieerd via het dialoogvenster voor initiële verificatie met behulp van universele verificatie, werkt de DacFx-wizard op dezelfde manier als voor alle andere verificatiemethoden.
- De tabelontwerpfunctie van SSMS biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende softwarevereisten voor universele verificatie van Active Directory, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- Raadpleeg de volgende koppeling voor de nieuwste versie van Active Directory Authentication Library (ADAL) voor universele verificatie: [micro soft. Identity model. clients. ActiveDirectory](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/).  

## <a name="next-steps"></a>Volgende stappen

- Zie [Azure SQL database multi-factor Authentication configureren voor SQL Server Management Studio](authentication-mfa-ssms-configure.md)voor configuratie stappen.
- Anderen toegang verlenen tot uw Data Base: [SQL database-verificatie en-autorisatie: toegang verlenen](logins-create-manage.md)  
- Zorg ervoor dat anderen verbinding kunnen maken via de firewall: [Configureer een firewall regel op server niveau met behulp van de Azure Portal](firewall-configure.md)  
- [Azure Active Directory-verificatie configureren en beheren met SQL Database of Azure Synapse](authentication-aad-configure.md)
- [Azure AD-gastgebruikers maken en instellen als Azure AD-beheerder](authentication-aad-guest-users.md) 
- [Microsoft SQL Server Data-Tier Application Framework (17.0.0 GA)](https://www.microsoft.com/download/details.aspx?id=55088)  
- [SQLPackage.exe](/sql/tools/sqlpackage)  
- [Een BACPAC-bestand importeren in een nieuwe data base](database-import.md)  
- [Database naar een BACPAC-bestand exporteren](database-export.md)  
- Interface van C# interface [IUniversalAuthProvider](/dotnet/api/microsoft.sqlserver.dac.iuniversalauthprovider)  
- Wanneer u **Azure Active Directory-universele met MFA-** verificatie gebruikt, is ADAL tracering beschikbaar vanaf [SSMS 17,3](/sql/ssms/download-sql-server-management-studio-ssms). Standaard uitgeschakeld, u kunt ADAL tracering inschakelen via het menu **extra**, **Opties** , onder **Azure-Services**, Azure- **Cloud**, **ADAL uitvoervenster traceer niveau**, gevolgd door **uitvoer**  in te scha kelen in het menu **weer gave** . De traceringen zijn beschikbaar in het uitvoer venster wanneer u **Azure Active Directory optie** selecteert.