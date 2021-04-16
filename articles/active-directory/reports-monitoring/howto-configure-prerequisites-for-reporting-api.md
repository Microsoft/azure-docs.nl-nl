---
title: Vereisten voor Azure Active Directory rapportage-API-| Microsoft Docs
description: Meer informatie over de vereisten voor toegang tot de rapportage-API van Azure AD
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
ms.openlocfilehash: 00c519ef06637c5193b347f0bbc906c6232a7ca8
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532547"
---
# <a name="prerequisites-to-access-the-azure-active-directory-reporting-api"></a>Vereisten voor toegang tot Azure Active Directory rapportage-API

De [Azure Active Directory (Azure AD) rapportage-API's](./concept-reporting-api.md) bieden toegang tot de gegevens op programmeerniveau via een set op REST-gebaseerde API's. U kunt deze API's aanroepen vanuit programmeertalen en hulpprogramma's.

De rapportage-API maakt gebruik [van OAuth om](../../api-management/api-management-howto-protect-backend-with-aad.md) toegang tot de web-API's te autorisen.

Om uw toegang tot de rapportage-API voor te bereiden, moet u het volgende doen:

1. [Rollen toewijzen](#assign-roles)
2. [Licentievereisten](#license-requirements)
3. [Een toepassing registreren](#register-an-application)
4. [Machtigingen verlenen](#grant-permissions)
5. [Configuratie-instellingen verzamelen](#gather-configuration-settings)

## <a name="assign-roles"></a>Rollen toewijzen

Als u toegang wilt krijgen tot de rapportagegegevens via de API, moet u een van de volgende rollen hebben toegewezen:

- Beveiligingslezer

- Beveiligingsbeheer

- Hoofdbeheerder

## <a name="license-requirements"></a>Licentievereisten

Als u toegang wilt krijgen tot de aanmeldrapporten voor een tenant, moet aan een Azure AD-tenant een licentie Azure AD Premium gekoppeld. Azure AD Premium P1-licentie (of hoger) is vereist voor toegang tot aanmeldingsrapporten voor elke Azure AD-tenant. Als het directorytype is Azure AD B2C, zijn de aanmeldingsrapporten ook toegankelijk via de API zonder aanvullende licentievereiste. 


## <a name="register-an-application"></a>Een toepassing registreren

Registratie is nodig, zelfs als u de rapportage-API gebruikt met behulp van een script. De registratie geeft u een **toepassings-id,** die vereist is voor de autorisatie-aanroepen en waarmee uw code tokens kan ontvangen.

Als u uw directory wilt configureren voor toegang tot de rapportage-API van Azure AD, moet  u zich aanmelden bij de [Azure Portal](https://portal.azure.com) met een Azure-beheerdersaccount dat ook lid is van de directoryrol Globale beheerder in uw Azure AD-tenant.

> [!IMPORTANT]
> Toepassingen die worden uitgevoerd onder referenties met beheerdersbevoegdheden kunnen zeer krachtig zijn, dus zorg ervoor dat u de id en geheime referenties van de toepassing op een veilige locatie houdt.
> 

**Een Azure AD-toepassing registreren:**

1. Selecteer in [Azure Portal](https://portal.azure.com) **het** Azure Active Directory in het linkernavigatievenster.
   
    ![Schermopname toont Azure Active Directory geselecteerd in het Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer **Azure Active Directory** op de **App-registraties**.

    ![Schermopname van App-registraties geselecteerd in het menu Beheren.](./media/howto-configure-prerequisites-for-reporting-api/02.png) 

3. Selecteer op **App-registraties** pagina Nieuwe **registratie.**

    ![Schermopname met Nieuwe registratie geselecteerd.](./media/howto-configure-prerequisites-for-reporting-api/03.png)

4. De **pagina Een toepassing registreren:**

    ![Schermopname van de pagina Een toepassing registreren waar u de waarden in deze stap kunt invoeren.](./media/howto-configure-prerequisites-for-reporting-api/04.png)

    a. Typ in **het** tekstvak `Reporting API application` Naam.

    b. Bij **Ondersteunde accounts typt** u **Alleen accounts in deze organisatie.**

    c. Typ in **het tekstvak** **Omleidings-URL** de optie `https://localhost` Web.

    d. Selecteer **Registreren**. 


## <a name="grant-permissions"></a>Machtigingen verlenen 

Afhankelijk van de API die u wilt openen, moet u uw app de volgende machtigingen verlenen:  

| API | Machtiging |
| --- | --- |
| Windows Azure Active Directory | Mapgegevens lezen |
| Microsoft Graph | Alle auditlogboekgegevens lezen |

![Schermopname die laat zien waar u Een machtiging toevoegen kunt selecteren in het deelvenster A P I-machtigingen.](./media/howto-configure-prerequisites-for-reporting-api/36.png)

De volgende sectie bevat de stappen voor beide API's. Als u geen toegang wilt tot een van de API's, kunt u de gerelateerde stappen overslaan.

**Uw toepassing machtigingen verlenen voor het gebruik van de API's:**


1. Selecteer **API-machtigingen** en vervolgens **Een machtiging toevoegen.** 

    ![Schermopname van de pagina A P I-machtigingen waar u Een machtiging toevoegen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/05.png)

2. Ga op **de pagina API-machtigingen aanvragen** naar Ondersteuning voor verouderde API **Azure Active Directory** **Graph**. 

    ![Schermopname van de pagina A P I-machtigingen aanvragen waar u een grafiek Azure Active Directory selecteren.](./media/howto-configure-prerequisites-for-reporting-api/06.png)

3. Selecteer op **de pagina Vereiste** machtigingen de optie **Toepassingsmachtigingen** en vouw **Map** uit in het selectievakje **Directory.ReadAll.**  Selecteer **Machtigingen toevoegen**.

    ![Schermopname van de pagina A P I-machtigingen aanvragen waar u Toepassingsmachtigingen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/07.png)

4. Selecteer op **de pagina Rapportage-API-toepassing -** API-machtigingen de optie **Beheerders toestemming verlenen.** 

    ![Schermopname van de pagina Reporting A P I Application A P I permissions waar u Beheerdersmachtigingen verlenen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/08.png)

5. Opmerking: **Microsoft Graph** wordt standaard toegevoegd tijdens api-registratie.

    ![Schermopname van de pagina A P I-machtigingen waar u Een machtiging toevoegen kunt selecteren.](./media/howto-configure-prerequisites-for-reporting-api/15.png)

## <a name="gather-configuration-settings"></a>Configuratie-instellingen verzamelen 

In deze sectie ziet u hoe u de volgende instellingen uit uw directory op kunt halen:

- Domeinnaam
- Client-id
- Clientgeheim

U hebt deze waarden nodig bij het configureren van aanroepen naar de rapportage-API. 

### <a name="get-your-domain-name"></a>Uw domeinnaam op te halen

**Ga als volgende te werk om uw domeinnaam op te halen:**

1. Selecteer **Azure Active Directory** in [Azure Portal](https://portal.azure.com) in het navigatiepaneel aan de linkerkant.
   
    ![Schermopname toont Azure Active Directory geselecteerd in het Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer op **Azure Active Directory** pagina Aangepaste **domeinnamen.**

    ![Schermopname toont aangepaste domeinnamen die zijn geselecteerd Azure Active Directory.](./media/howto-configure-prerequisites-for-reporting-api/09.png) 

3. Kopieer uw domeinnaam uit de lijst met domeinen.


### <a name="get-your-applications-client-id"></a>De client-id van uw toepassing op halen

**De client-id van uw toepassing op te halen:**

1. Klik in [Azure Portal](https://portal.azure.com)linkernavigatievenster op **Azure Active Directory.**
   
    ![Schermopname toont Azure Active Directory geselecteerd in het Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2. Selecteer uw toepassing op de **pagina App-registraties.**

3. Navigeer op de toepassingspagina naar **Toepassings-id** en selecteer **Klik om te kopiëren.**

    ![Schermopname van de pagina Reporting A P I Application waar u de toepassings-ID kunt kopiëren.](./media/howto-configure-prerequisites-for-reporting-api/11.png) 


### <a name="get-your-applications-client-secret"></a>Het clientgeheim van uw toepassing op halen
 Vermijd fouten bij het openen van auditlogboeken of aanmelding met behulp van de API.

**Ga als volgende te werk om het clientgeheim van uw toepassing op te halen:**

1. Klik in [Azure Portal](https://portal.azure.com)linkernavigatievenster op **Azure Active Directory**.
   
    ![Schermopname van Azure Active Directory geselecteerd in het Azure Portal menu.](./media/howto-configure-prerequisites-for-reporting-api/01.png) 

2.  Selecteer uw toepassing op de **pagina App-registraties.**

3.  Selecteer **Certificaten en geheimen op de** pagina **API-toepassing** in de sectie **Clientgeheimen** en klik op + **Nieuw clientgeheim.** 

    ![Schermopname van de pagina Certificaten & geheimen waar u een clientgeheim kunt toevoegen.](./media/howto-configure-prerequisites-for-reporting-api/12.png)

5. Voeg op **de pagina Een clientgeheim** toevoegen het volgende toe:

    a. Typ in **het** tekstvak `Reporting API` Beschrijving.

    b. Als **verloopt,** selecteert u **Over 2 jaar.**

    c. Klik op **Opslaan**.

    d. Kopieer de sleutelwaarde.

## <a name="troubleshoot-errors-in-the-reporting-api"></a>Fouten in de rapportage-API oplossen

In deze sectie vindt u de algemene foutberichten die u kunt tegen komen tijdens het openen van activiteitenrapporten met behulp van de Microsoft Graph API en de stappen voor de oplossing.

### <a name="error-failed-to-get-user-roles-from-microsoft-graph"></a>Fout: Kan gebruikersrollen niet op Microsoft Graph

 Meld u aan bij uw account met beide aanmeldingsknoppen in de graph explorer-gebruikersinterface om te voorkomen dat er een fout wordt weergegeven wanneer u zich probeert aan te melden met Graph Explorer. 

![Grafiekverkenner](./media/troubleshoot-graph-api/graph-explorer.png)

### <a name="error-failed-to-do-premium-license-check-from-microsoft-graph"></a>Fout: Controle van Premium-licentie is mislukt vanuit Microsoft Graph 

Als u dit foutbericht tegen komt tijdens het openen van  aanmeldingen met Graph Explorer, kiest u Machtigingen wijzigen onder uw account in het linkernavigatiebalk en selecteert u **Tasks.ReadWrite** en **Directory.Read.All.** 

![Gebruikersinterface voor machtigingen wijzigen](./media/troubleshoot-graph-api/modify-permissions.png)

### <a name="error-tenant-is-not-b2c-or-tenant-doesnt-have-premium-license"></a>Fout: Tenant is niet B2C of tenant heeft geen Premium-licentie

Voor toegang tot aanmeldingsrapporten is een Azure Active Directory Premium 1-licentie (P1) vereist. Als u dit foutbericht ziet tijdens het openen van aanmeldingen, moet u ervoor zorgen dat uw tenant een licentie heeft met een Azure AD P1-licentie.

### <a name="error-the-allowed-roles-does-not-include-user"></a>Fout: De toegestane rollen omvatten geen Gebruiker. 

 Voorkom fouten bij het openen van auditlogboeken of aanmelding met behulp van de API. Zorg ervoor dat uw account deel uitmaakt van de rol **Beveiligingslezer** of **Rapportlezer** in uw Azure Active Directory tenant.

### <a name="error-application-missing-aad-read-directory-data-permission"></a>Fout: AAD-machtiging 'Mapgegevens lezen' ontbreekt voor toepassing 

### <a name="error-application-missing-microsoft-graph-api-read-all-audit-log-data-permission"></a>Fout: Toepassing ontbreekt Microsoft Graph API-machtiging 'Alle auditlogboekgegevens lezen'

Volg de stappen in Vereisten voor toegang tot de [rapportage-API Azure Active Directory](howto-configure-prerequisites-for-reporting-api.md) om ervoor te zorgen dat uw toepassing wordt uitgevoerd met de juiste set machtigingen. 

## <a name="next-steps"></a>Volgende stappen

* [Gegevens ophalen met de Azure Active Directory rapportage-API met certificaten](tutorial-access-api-with-certificates.md)
* [Audit API-verwijzing](/graph/api/resources/directoryaudit) 
* [Verwijzing naar rapport-API voor aanmeldingsactiviteit](/graph/api/resources/signin)