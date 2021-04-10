---
title: Een SaaS-toepassing registreren-Azure Marketplace
description: Meer informatie over het gebruik van de Azure Portal voor het registreren van een SaaS-toepassing en het ontvangen van een Azure Active Directory beveiligings token.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 06/10/2020
author: mingshen-ms
ms.author: mingshen
ms.openlocfilehash: e7bcfe1afc063d89bc6a5339bf62521cd644b8ca
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105048337"
---
# <a name="register-a-saas-application"></a>Een SaaS-toepassing registreren

In dit artikel wordt uitgelegd hoe u een SaaS-toepassing registreert met behulp van de micro soft [Azure Portal](https://portal.azure.com/) en hoe u het toegangs token van de uitgever (Azure Active Directory toegangs token) kunt ophalen. De uitgever gebruikt dit token om de SaaS-toepassing te verifiëren door de SaaS-fulfillment-Api's aan te roepen.  De fulfillment-Api's gebruiken de OAuth 2,0-client referenties om flow te verlenen op Azure Active Directory (v 1.0) eind punten om een service-naar-service-toegangs token aanvraag te maken.

Azure Marketplace biedt geen beperkingen voor de verificatie methode die door uw SaaS-service wordt gebruikt voor eind gebruikers. De onderstaande stroom is alleen vereist voor het verifiëren van de SaaS-service in azure Marketplace.

Zie [Wat is verificatie](../../active-directory/develop/authentication-vs-authorization.md)? voor meer informatie over Azure AD (Active Directory).

## <a name="register-an-azure-ad-secured-app"></a>Een Azure AD-beveiligde app registreren

Elke toepassing die de mogelijkheden van Azure Active Directory wil gebruiken, moet eerst in een Azure Active Directory-tenant worden geregistreerd. Bij dit registratie proces moet u Azure AD een aantal details over uw toepassing geven. Voer de volgende stappen uit om een nieuwe toepassing te registreren met behulp van de Azure Portal:

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Als uw account u toegang geeft tot meer dan één, klikt u in de rechter bovenhoek op uw account en stelt u uw portal sessie in op de gewenste Azure AD-Tenant.
3. Klik in het navigatie deel venster aan de linkerkant op de **Azure Active Directory** -service, klik op **app-registraties** en klik vervolgens op **nieuwe toepassing registreren**.

    ![SaaS AD-App-registraties](./media/saas-offer-app-registration-v1.png)

4. Voer op de pagina maken de registratie gegevens voor uw toepassing in \' :
    -   **Naam**: Voer een beschrijvende naam voor de toepassing in
    -   **Toepassings type**:  
        
        Selecteer **Web-app/API** voor [client toepassingen](../../active-directory/develop/developer-glossary.md#client-application)) en [resource/API-toepassingen](../../active-directory/develop/developer-glossary.md#resource-server)die zijn geïnstalleerd op een beveiligde server. Deze instelling wordt gebruikt voor OAuth-vertrouwelijke [webclients](../../active-directory/develop/developer-glossary.md#web-client)en open bare [gebruiker op agent-gebaseerde clients](../../active-directory/develop/developer-glossary.md#user-agent-based-client)).
        Dezelfde toepassing kan zowel een client als resource/API beschikbaar maken.

        Voor specifieke voor beelden van webtoepassingen raadpleegt u de Quick Start-setups die beschikbaar zijn in de sectie [aan de slag](../../active-directory/develop/quickstart-create-new-tenant.md) van de [hand leiding voor ontwikkel AARS van Azure AD](../../active-directory/develop/index.yml).

5. Wanneer u klaar bent, klikt u op **registreren**.  Azure AD wijst een unieke *toepassings-id* toe aan uw nieuwe toepassing. U kunt het beste één app registreren die alleen toegang heeft tot de API, en als één Tenant.

6. Als u client geheim wilt maken, navigeert u naar de **pagina certificaten & geheimen** en klikt u op **+ Nieuw client geheim**.  Zorg ervoor dat u de geheime waarde kopieert om deze in uw code te gebruiken.

De **Azure AD-App-ID** is gekoppeld aan uw uitgevers-id, dus zorg ervoor dat dezelfde *App-ID* wordt gebruikt in al uw aanbiedingen.

>[!Note]
>Als de uitgever twee of meer verschillende accounts in het partner centrum heeft, kunnen de registratie gegevens van de Azure AD-App alleen in één account worden gebruikt. Met dezelfde Tenant-ID wordt het combi neren van de App-ID voor een aanbieding onder een ander uitgevers account niet ondersteund.

## <a name="how-to-get-the-publishers-authorization-token"></a>Het autorisatie token van de uitgever ophalen

Zodra u uw toepassing hebt geregistreerd, kunt u het autorisatie token van de uitgever programmatisch aanvragen (Azure AD-toegangs token, met behulp van Azure AD v1-eind punt). De uitgever moet dit token gebruiken bij het aanroepen van de verschillende SaaS-fulfillment-Api's. Dit token is slechts één uur geldig. 

Zie [Azure Active Directory toegangs tokens](../../active-directory/develop/access-tokens.md)voor meer informatie over deze tokens.  Houd er rekening mee dat in de stroom hieronder v1-eind punt token wordt gebruikt.

### <a name="get-the-token-with-an-http-post"></a>Het token ophalen met een HTTP POST

#### <a name="http-method"></a>HTTP-methode

Plaatsen<br>

##### <a name="request-url"></a>*Aanvraag-URL* 

`https://login.microsoftonline.com/*{tenantId}*/oauth2/token`

##### <a name="uri-parameter"></a>*URI-para meter*

|  Parameternaam    |  Vereist         |  Beschrijving |
|  ---------------   |  ---------------  | ------------ |
|  `tenantId`        |  Waar      |  De Tenant-ID van de geregistreerde AAD-toepassing. |

##### <a name="request-header"></a>*Aanvraag header*

|  Headernaam       |  Vereist         |  Beschrijving |
|  ---------------   |  ---------------  | ------------ |
|  `content-type`    |  Waar      |  Het inhouds type dat is gekoppeld aan de aanvraag. De standaardwaarde is `application/x-www-form-urlencoded`. |

##### <a name="request-body"></a>*Aanvraagbody*

|  Naam van eigenschap     |  Vereist         |  Beschrijving |
|  ---------------   |  ---------------  | ------------ |
|  `grant_type`      |  Waar      |  Toekennings type. Gebruik `"client_credentials"`. |
|  `client_id`       |  Waar      |  Client/App-ID die is gekoppeld aan de Azure AD-app. |
|  `client_secret`   |  Waar      |  Geheim dat is gekoppeld aan de Azure AD-app. |
|  `resource`        |  Waar      |  Doel resource waarvoor het token wordt aangevraagd. Gebruiken `20e940b3-4c77-4b0b-9a53-9e16a1b010a7` omdat de SaaS-API van Marketplace altijd de doel resource in dit geval is. |

##### <a name="response"></a>*Response*

|  Naam     |  Type         |  Description |
|  ------   |  ---------------  | ------------ |
|  200 OK   |  TokenResponse    |  De aanvraag is voltooid. |

##### <a name="tokenresponse"></a>*TokenResponse*

Voorbeeld antwoord:

```json
{
      "token_type": "Bearer",
      "expires_in": "3600",
      "ext_expires_in": "0",
      "expires_on": "15251…",
      "not_before": "15251…",
      "resource": "20e940b3-4c77-4b0b-9a53-9e16a1b010a7",
      "access_token": "eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayIsImtpZCI6ImlCakwxUmNxemhpeTRmcHhJeGRacW9oTTJZayJ9…"
  }
```

De `"access_token"` veld waarde in het antwoord is de `<access_token>` para meter die u opgeeft als autorisatie parameter bij het aanroepen van alle SaaS-uitvoeringen en Marketplace metering-api's.

## <a name="next-steps"></a>Volgende stappen

Uw met Azure AD beveiligde app kan nu gebruikmaken van de [SaaS fulfillment API versie 2](./pc-saas-fulfillment-api-v2.md).