---
title: Multi-Factor Authentication van AAD gebruiken
description: Synapse SQL biedt ondersteuning voor verbindingen vanuit SQL Server Management Studio (SSMS) met behulp van universele verificatie van Active Directory.
services: synapse-analytics
author: vvasic-msft
ms.service: synapse-analytics
ms.subservice: sql
ms.topic: overview
ms.date: 04/15/2020
ms.author: vvasic
ms.reviewer: jrasnick
ms.custom: has-adal-ref
ms.openlocfilehash: 7b3b8aae1345339dc34137550f3fe4c5be915ae5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101672725"
---
# <a name="use-multi-factor-aad-authentication-with-synapse-sql-ssms-support-for-mfa"></a>Multi-Factor Authentication van AAD gebruiken met Synapse SQL (SSMS-ondersteuning voor MFA)

Synapse SQL biedt ondersteuning voor verbindingen vanuit SQL Server Management Studio (SSMS) met behulp van *universele verificatie van Active Directory*. 

In dit artikel worden de verschillen beschreven tussen de verschillende verificatie-opties, evenals de beperkingen van universele verificatie. 

**Download de nieuwste versie van SSMS** Download op de clientcomputer de meest recente versie van SSMS. Ga hiervoor naar de pagina [Download SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true) (SQL Server Management Studio (SSMS) downloaden).

Voor alle functies die in dit artikel worden besproken, hebt u ten minste versie 17.2 van juli 2017 nodig.  Het meest recente dialoogvenster Verbinding maken met server moet er ongeveer uitzien als in de volgende afbeelding:

![Schermopname van het dialoogvenster Verbinding maken met server waar u een servernaam en verificatieoptie kunt selecteren.](./media/mfa-authentication/1mfa-universal-connect.png "Vult het vak Gebruikersnaam in.")  

## <a name="the-five-authentication-options"></a>De vijf verificatieopties  

Universele verificatie met Active Directory ondersteunt de twee niet-interactieve verificatiemethoden:
    - `Active Directory - Password`
    - `Active Directory - Integrated`

Er zijn ook twee niet-interactieve verificatiemodellen, die kunnen worden gebruikt in allerlei verschillende toepassingen (ADO.NET, JDCB, ODC, enzovoort). Voor deze twee methoden worden nooit pop-upvensters weergegeven:

- `Active Directory - Password`
- `Active Directory - Integrated`

De interactieve methode die ook ondersteuning biedt voor Azure AD MFA (Multi-Factor Authentication) is:

- `Active Directory - Universal with MFA`

Azure AD MFA helpt bij het bewaken van de toegang tot uw gegevens en toepassingen en komt tegemoet aan de wensen van gebruikers die een eenvoudige aanmeldprocedure willen. Azure MFA biedt krachtige verificatie met een scala aan gebruikersvriendelijke verificatieopties (zoals telefoonoproepen, sms-berichten, smartcards met pincode of melding in mobiele app), zodat gebruikers de gewenste methode kunnen kiezen. Bij interactieve MFA met Azure AD kunnen er pop-upvensters worden weergegeven voor validatie.

Zie [Multi-Factor Authentication](../../active-directory/authentication//concept-mfa-howitworks.md)voor een beschrijving van Multi-Factor Authentication.

### <a name="azure-ad-domain-name-or-tenant-id-parameter"></a>Azure AD-domeinnaam of tenant-id als parameter

Vanaf [SSMS versie 17](/sql/ssms/download-sql-server-management-studio-ssms?view=azure-sqldw-latest&preserve-view=true) kunnen gebruikers die vanuit andere Azure Active Directory's zijn geïmporteerd als gastgebruiker, de naam van het Azure AD-domein of hun tenant-id opgeven wanneer ze verbinding willen maken. 

Gastgebruikers zijn gebruikers die zijn uitgenodigd bij andere Azure ADs, Microsoft-accounts zoals outlook.com, hotmail.com, live.com of andere accounts zoals gmail.com. Aan de hand van deze gegevens kan de verificatiemethode **Active Directory Universeel met MFA** de juiste verificatie-instantie identificeren. Deze optie is ook vereist voor de ondersteuning van Microsoft-accounts (MSA) zoals outlook.com, hotmail.com, live.com, of niet-MSA-accounts. 

Al deze gebruikers die willen worden geverifieerd met behulp van universele verificatie, moeten hun Azure AD-domeinnaam of tenant-id invoeren. Deze parameter vertegenwoordigt de huidige Azure AD-domeinnaam/tenant-id waaraan de Azure-server is gekoppeld. 

Als de Azure server bijvoorbeeld is gekoppeld aan het Azure AD-domein `contosotest.onmicrosoft.com` waar de gebruiker `joe@contosodev.onmicrosoft.com` wordt gehost als een geïmporteerde gebruiker uit het Azure AD-domein `contosodev.onmicrosoft.com`, is `contosotest.onmicrosoft.com` de domeinnaam die vereist is om deze gebruiker te verifiëren. 

Als de gebruiker een systeemeigen gebruiker is van de Azure AD die is gekoppeld aan de Azure-server, en geen MSA-account is, hoeft er geen domeinnaam of tenant-id te worden ingevoerd. 

Als u de parameter wilt invoeren (vanaf SSMS versie 17.2), vult u het dialoogvenster **Verbinding maken met database** in, waarbij u de verificatiemethode **Active Directory - Universeel met MFA** selecteert. Selecteer **Opties**, vul het vak **Gebruikersnaam** in en selecteer het tabblad **Verbindingseigenschappen**. 

Schakel het selectievakje **AD-domeinnaam of tenant-id** in en geef de gegevens van de verificatie-instantie op, zoals de domeinnaam (**contosotest.onmicrosoft.com**) of de GUID van de tenant-id.  

   ![Schermopname van Verbinding maken met server op het tabblad Verbindingseigenschappen met ingevoerde waarden.](./media/mfa-authentication/mfa-tenant-ssms.png)

Als u SSMS 18.x of hoger gebruikt, is de AD-domeinnaam of tenant-id niet meer nodig voor gastgebruikers omdat 18.x of hoger deze automatisch herkent.

   ![mfa-tenant-ssms](./media/mfa-authentication/mfa-no-tenant-ssms.png)

### <a name="azure-ad-business-to-business-support"></a>Ondersteuning voor Azure AD B2B   
Azure AD-gebruikers die worden ondersteund voor Azure AD B2B-scenario's als gastgebruikers (zie [Wat is Azure B2B-samenwerking](../../active-directory/external-identities/what-is-b2b.md?bc=%2fazure%2fsynapse-analytics%2fbreadcrumb%2ftoc.json&toc=%2fazure%2fsynapse-analytics%2ftoc.json) kunnen alleen verbinding maken met Synapse SQL als leden van een groep die is gemaakt in de huidige Azure AD en die handmatig zijn toegewezen met behulp van de Transact-SQL-instructie `CREATE USER` in een bepaalde database. 

Als `steve@gmail.com` bijvoorbeeld wordt uitgenodigd voor Azure AD `contosotest` (met het Azure AD-domein `contosotest.onmicrosoft.com`), moet er een Azure AD-groep, zoals `usergroup`, worden gemaakt in de Azure AD die het lid `steve@gmail.com` bevat. Vervolgens moet deze groep worden gemaakt voor een specifieke database (MyDatabase) door een Azure AD SQL-beheerder of Azure AD DBO door een Transact-SQL `CREATE USER [usergroup] FROM EXTERNAL PROVIDER`-instructie uit te voeren. 

Nadat de databasegebruiker is gemaakt, kan de gebruiker `steve@gmail.com` zich aanmelden bij `MyDatabase` met behulp van de SSMS-verificatieoptie `Active Directory – Universal with MFA support`. 

De gebruikersgroep heeft standaard alleen de machtiging om verbinding te maken en alle verdere gegevenstoegang moet op de gebruikelijke manier worden verleend. 

Als gastgebruiker moet `steve@gmail.com` het selectievakje inschakelen en de AD-domeinnaam `contosotest.onmicrosoft.com` toevoegen in het dialoogvenster **Verbindingseigenschap** van SSMS. De optie **AD-domeinnaam of tenant-id** wordt alleen ondersteund voor de verbindingsopties van Universeel met MFA; anders wordt deze grijs weergegeven.

## <a name="universal-authentication-limitations-for-synapse-sql"></a>Beperkingen van universele verificatie voor Synapse SQL

- SSMS en SqlPackage.exe zijn de enige hulpprogramma's die momenteel zijn ingeschakeld voor MFA via universele verificatie van Active Directory.
- SSMS versie 17.2 ondersteunt gelijktijdige toegang door meerdere gebruikers met behulp van universele verificatie met MFA. In versies 17.0 en 17.1 wordt de aanmelding voor een exemplaar van SSMS met behulp van universele verificatie beperkt tot één Azure Active Directory-account. Als u zich als een ander Azure AD-account wilt aanmelden, moet u een ander exemplaar van SSMS gebruiken. (Deze beperking geldt alleen voor universele verificatie met Active Directory. U kunt zich aanmelden bij verschillende servers met behulp van Active Directory-wachtwoordverificatie, geïntegreerde Active Directory-verificatie of SQL Server-verificatie).
- SSMS ondersteunt universele verificatie van Active Directory voor Objectverkenner, Query-editor en visualisatie van Query Store.
- SSMS versie 17.2 biedt ondersteuning voor de DacFx-wizard voor het exporteren/extraheren/implementeren van databases. Zodra een specifieke gebruiker is geverifieerd via het dialoogvenster voor initiële verificatie met behulp van universele verificatie, werkt de DacFx-wizard op dezelfde manier als voor alle andere verificatiemethoden.
- De tabelontwerpfunctie van SSMS biedt geen ondersteuning voor universele verificatie.
- Er zijn geen aanvullende softwarevereisten voor universele verificatie van Active Directory, behalve dat u een ondersteunde versie van SSMS moet gebruiken.  
- De versie van Active Directory Authentication Library (ADAL) voor universele verificatie is bijgewerkt naar de nieuwste versie van ADAL.dll, te weten 3.13.9. Zie [Active Directory Authentication Library 3.14.1](https://www.nuget.org/packages/Microsoft.IdentityModel.Clients.ActiveDirectory/) voor meer informatie.  

## <a name="next-steps"></a>Volgende stappen
Zie het artikel [Connect to Azure Synapse Analytics with SQL Server Management Studio (SSMS)](get-started-ssms.md) (Verbinding maken met Azure Synapse Analytics met SQL Server Management Studio (SSMS)) voor meer informatie.