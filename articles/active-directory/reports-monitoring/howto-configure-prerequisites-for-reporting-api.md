---
title: Vereisten voor de rapportage-API van Azure Active Directory | Microsoft Docs
description: Meer informatie over de vereisten voor toegang tot de Azure AD Reporting-API
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: ada19f69-665c-452a-8452-701029bf4252
ms.service: active-directory
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 03/04/2020
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ca618dc234ff4383083ff5eb21299fb1e3e7128
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/30/2020
ms.locfileid: "96348735"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Vereisten voor toegang tot de API voor Azure Active Directory rapportage

De [Azure Active Directory (Azure AD) rapportage-API's](./concept-reporting-api.md) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze Api's aanroepen vanuit programmeer talen en hulpprogram ma's.

De rapportage-API maakt gebruik van [OAuth](../../api-management/api-management-howto-protect-backend-with-aad.md) om toegang tot de Web-api's te verlenen.

U moet het volgende doen om de toegang tot de rapportage-API voor te bereiden:

1. [Rollen toewijzen](#assign-roles)
2. [Licentie vereisten](#license-requirements)
3. [Een toepassing registreren](#register-an-application)
4. [Machtigingen verlenen](#grant-permissions)
5. [Configuratie-instellingen verzamelen](#gather-configuration-settings)

## <a name="assign-roles"></a>Rollen toewijzen

Als u toegang wilt krijgen tot de rapportage gegevens via de API, moet een van de volgende rollen zijn toegewezen:

- Beveiligingslezer

- Beveiligingsbeheer

- Hoofdbeheerder

## <a name="license-requirements"></a>Licentie vereisten

Als u toegang wilt krijgen tot de aanmeld rapporten voor een Tenant, moet een Azure AD-Tenant zijn Azure AD Premium licentie hebben. Azure AD Premium P1 (of hoger)-licentie is vereist voor toegang tot aanmeldings rapporten voor een Azure AD-Tenant. Als het Directory type Azure AD B2C is, zijn de aanmeldings rapporten toegankelijk via de API zonder extra licentie vereiste. 


## <a name="register-an-application"></a>Een toepassing registreren

Registratie is vereist, zelfs als u de rapportage-API opent met behulp van een script. De registratie geeft u een **toepassings-id** die is vereist voor de autorisatie aanroepen en de code in staat stelt om tokens te ontvangen.

Als u uw Directory wilt configureren voor toegang tot de Azure AD Reporting-API, moet u zich aanmelden bij de [Azure Portal](https://portal.azure.com) met een Azure-beheerders account dat ook lid is van de rol **globale beheerder** in uw Azure AD-Tenant.

> [!IMPORTANT]
> Toepassingen die worden uitgevoerd onder referenties met beheerders bevoegdheden, kunnen zeer krachtig zijn. Zorg er dus voor dat u de ID en geheime referenties van de toepassing op een veilige locatie behoudt.
> 

**Een Azure AD-toepassing registreren:**

1. Selecteer **Azure Active Directory** in het navigatie deel venster links In het [Azure Portal](https://portal.azure.com).
   
    ![Scherm afbeelding toont Azure Active Directory geselecteerd in het menu Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer op de pagina **Azure Active Directory** **app-registraties**.

    ![Scherm afbeelding toont App-registraties geselecteerd in het menu beheren.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Selecteer op de pagina **app-registraties** **nieuwe registratie**.

    ![Scherm opname toont nieuwe registratie geselecteerd.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. De pagina voor het **registreren van een toepassing** :

    ![Scherm afbeelding toont de pagina een toepassing registreren waar u de waarden in deze stap kunt invoeren.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Typ in het tekstvak **naam** `Reporting API application` .

    b. Voor het **type ondersteunde accounts** selecteert u **alleen accounts in deze organisatie**.

    c. Typ in de **omleidings-URL** **webtekstvak selecteren** `https://localhost` .

    d. Selecteer **Registreren**. 


## <a name="grant-permissions"></a>Machtigingen verlenen 

Afhankelijk van de API die u wilt gebruiken, moet u uw app de volgende machtigingen verlenen:  

| API | Machtiging |
| --- | --- |
| Windows Azure Active Directory | Mapgegevens lezen |
| Microsoft Graph | Alle audit logboek gegevens lezen |

![Scherm afbeelding laat zien waar u een machtiging toevoegen kunt selecteren in het deel venster met een P I-machtigingen.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

In de volgende sectie worden de stappen voor beide Api's vermeld. Als u geen toegang tot een van de Api's wilt, kunt u de gerelateerde stappen overs Laan.

**Uw toepassings machtigingen verlenen voor het gebruik van de Api's:**


1. Selecteer **API-machtigingen** en **Voeg vervolgens een machtiging toe**. 

    ![Scherm afbeelding toont de pagina P I permissions waar u een machtiging toevoegen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Zoek op de **pagina API-machtigingen voor aanvragen** naar **verouderde API** **Azure Active Directory Graph**. 

    ![Scherm afbeelding toont de pagina een P I-machtiging aanvragen waar u Azure Active Directory Graph kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Selecteer op de pagina **vereiste machtigingen** de optie **toepassings machtigingen**, vouw **Directory** -selectie vakje Directory **. ReadAll**.  Selecteer **Machtigingen toevoegen**.

    ![Scherm afbeelding toont de pagina een P I-machtiging aanvragen waar u toepassings machtigingen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Selecteer op de pagina **rapportage-API Application-API permissions** de optie **beheerder toestemming verlenen**. 

    ![In de scherm afbeelding ziet u de pagina een P i-toepassing rapporteren waarop u toestemming van de beheerder verlenen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Opmerking: de **Microsoft Graph** wordt standaard toegevoegd tijdens de API-registratie.

    ![Scherm afbeelding toont de pagina P I permissions waar u een machtiging toevoegen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Configuratie-instellingen verzamelen 

In deze sectie wordt beschreven hoe u de volgende instellingen uit uw Directory kunt ophalen:

- Domeinnaam
- Client-id
- Clientgeheim

U hebt deze waarden nodig bij het configureren van aanroepen naar de rapportage-API. 

### <a name="get-your-domain-name"></a>Uw domein naam ophalen

**Uw domein naam ophalen:**

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.
   
    ![Scherm afbeelding toont Azure Active Directory geselecteerd in het menu Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer op de pagina **Azure Active Directory** **aangepaste domein namen**.

    ![Scherm opname toont aangepaste domein namen die zijn geselecteerd uit Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopieer uw domein naam uit de lijst met domeinen.


### <a name="get-your-applications-client-id"></a>De client-ID van uw toepassing ophalen

**De client-ID van uw toepassing ophalen:**

1. Klik in het [Azure Portal](https://portal.azure.com)op **Azure Active Directory** in het navigatie deel venster aan de linkerkant.
   
    ![Scherm afbeelding toont Azure Active Directory geselecteerd in het menu Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer uw toepassing op de pagina **app-registraties** .

3. Ga op de pagina toepassing naar **toepassings-id** en selecteer **klikken om te kopiëren**.

    ![Scherm afbeelding toont de pagina een P I-toepassing melden waar u de toepassing van de D kunt kopiëren.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Het client geheim van uw toepassing ophalen
 Voorkom fouten bij het openen van de controle Logboeken of het aanmelden met de API.

**Het client geheim van uw toepassing ophalen:**

1. Klik in het [Azure Portal](https://portal.azure.com)op **Azure Active Directory** in het navigatie deel venster aan de linkerkant.
   
    ![Scherm afbeelding toont Azure Active Directory geselecteerd in het menu Azure Portal.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecteer uw toepassing op de pagina **app-registraties** .

3.  Selecteer **certificaten en geheimen** op de pagina **API-toepassing** , in de sectie **client geheimen** op **+ Nieuw client geheim**. 

    ![Scherm afbeelding toont de certificaten & geheimen pagina waar u een client geheim kunt toevoegen.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Voeg op de pagina **een geheim van client toevoegen** toe:

    a. Typ in het tekstvak **Beschrijving** `Reporting API` .

    b. Als **verlopen**, selecteert u **in 2 jaar**.

    c. Klik op **Opslaan**.

    d. Kopieer de sleutelwaarde.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Fouten in de rapportage-API oplossen

In deze sectie vindt u de algemene fout berichten die u kunt uitvoeren bij het openen van activiteiten rapporten met behulp van de Microsoft Graph-API en de stappen voor de oplossing.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fout: kan geen gebruikers rollen ophalen uit Microsoft Graph

 Meld u aan bij uw account met behulp van de aanmeldings knoppen in de interface van Graph Explorer om te voor komen dat er een fout optreedt wanneer u zich aanmeldt met Graph Explorer. 

![Grafiekverkenner](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fout: kan geen Premium-licentie controle uitvoeren vanuit Microsoft Graph 

Als u in dit fout bericht wordt weer gegeven terwijl u probeert toegang te krijgen tot aanmeldingen met behulp van Graph Explorer, kiest u **machtigingen voor wijzigen** onder uw account in het navigatie venster links en selecteert u **taken. readwrite** en **Directory. Read. all**. 

![Machtigingen voor gebruikers interface wijzigen](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: de Tenant is niet B2C of de Tenant heeft geen Premium-licentie

Voor toegang tot aanmeldings rapporten is een licentie voor Azure Active Directory Premium 1 (P1) vereist. Als dit fout bericht wordt weer gegeven tijdens het openen van de aanmeldingen, moet u ervoor zorgen dat uw Tenant is gelicentieerd met een Azure AD P1-licentie.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fout: de toegestane rollen bevatten geen gebruiker. 

 Voorkom fouten bij het openen van de controle Logboeken of het aanmelden met de API. Zorg ervoor dat uw account deel uitmaakt van de rol van **beveiligings lezer** of **rapport lezer** in uw Azure Active Directory-Tenant.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: de machtiging voor het lezen van de Directory gegevens van de toepassing voor AAD ontbreekt 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: er ontbreekt een machtiging voor de toepassing Microsoft Graph-API voor het lezen van alle audit logboek gegevens

Volg de stappen in de [vereisten voor toegang tot de API voor Azure Active Directory rapportage](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Audit API-verwijzing](/graph/api/resources/directoryaudit?view=graph-rest-beta) 
* [Verwijzing naar rapport-API voor aanmeldingsactiviteit](/graph/api/resources/signin?view=graph-rest-beta)