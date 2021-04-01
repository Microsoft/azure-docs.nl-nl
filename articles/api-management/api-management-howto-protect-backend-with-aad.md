---
title: API-back-end beveiligen in API Management met OAuth 2,0 en Azure AD
titleSuffix: Azure API Management
description: Meer informatie over het beveiligen van de toegang tot een web-API-back-end in azure API Management met OAuth 2,0-gebruikers autorisatie en Azure Active Directory
services: api-management
author: miaojiang
ms.service: api-management
ms.topic: article
ms.date: 09/23/2020
ms.author: apimpm
ms.custom: contperf-fy21q1
ms.openlocfilehash: face4beab450e92be76b2bb90e45625e025de6ee
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97027914"
---
# <a name="protect-a-web-api-backend-in-azure-api-management-by-using-oauth-20-authorization-with-azure-ad"></a>Een web-API-back-end beveiligen in azure API Management met behulp van OAuth 2,0-autorisatie met Azure AD 

In deze hand leiding wordt beschreven hoe u uw [Azure API Management](api-management-key-concepts.md) -exemplaar configureert om een API te beveiligen met behulp van het [OAuth 2,0-protocol met Azure Active Directory (Azure AD)](../active-directory/develop/active-directory-v2-protocols.md). 

> [!NOTE]
> Deze functie is beschikbaar in de lagen **ontwikkelaars**, **Basic**, **Standard** en **Premium** van API management.

## <a name="prerequisites"></a>Vereisten

Als u de stappen in dit artikel wilt volgen, hebt u het volgende nodig:

- Een API Management-exemplaar
- Een API die wordt gepubliceerd en die gebruikmaakt van het API Management-exemplaar
- Een Azure AD-tenant

## <a name="overview"></a>Overzicht

Hier volgt een kort overzicht van de stappen:

1. Registreer een toepassing (back-end-app) in azure AD om de API aan te duiden.
1. Registreer een andere toepassing (client-app) in azure AD om een client toepassing aan te duiden die de API moet aanroepen.
1. Ken in azure AD machtigingen toe om de client-app toe te staan de back-end-app aan te roepen.
1. Configureer de ontwikkelaars console om de API aan te roepen met OAuth 2,0-gebruikers autorisatie.
1. Voeg het beleid **valideren-JWT** toe om het OAuth-token voor elke inkomende aanvraag te valideren.

## <a name="register-an-application-in-azure-ad-to-represent-the-api"></a>Een toepassing registreren in azure AD om de API aan te duiden

Als u een API met Azure AD wilt beveiligen, moet u eerst een toepassing registreren in azure AD die de API vertegenwoordigt. In de volgende stappen wordt gebruikgemaakt van de Azure Portal om de toepassing te registreren. Zie [Quick Start: een toepassing configureren om een web-API beschikbaar te](../active-directory/develop/quickstart-configure-app-expose-web-apis.md)maken voor meer informatie over app-registratie.

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassing te registreren. Zoek en selecteer **app-registraties**.

1. Selecteer **Nieuwe registratie**. 

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:

   - Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app, zoals *back-end-app*. 
   - Selecteer in de sectie **ondersteunde account typen** een optie die aansluit bij uw scenario. 

1. Laat de sectie **URI omleiden** leeg.

1. Selecteer **Registreren** om de toepassing te maken. 

1. Zoek de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** en noteer deze voor later.

1. Selecteer **een API beschikbaar** maken en stel de **URI voor de toepassings-id** in met de standaard waarde. Noteer deze waarde voor later.

1. Selecteer de knop **een bereik toevoegen** om de pagina **een bereik toevoegen** weer te geven. Maak vervolgens een nieuwe scope die wordt ondersteund door de API (bijvoorbeeld `Files.Read` ).

1. Selecteer de knop **bereik toevoegen** om het bereik te maken. Herhaal deze stap om alle scopes toe te voegen die worden ondersteund door uw API.

1. Wanneer de bereiken zijn gemaakt, noteert u deze voor gebruik in een volgende stap. 

## <a name="register-another-application-in-azure-ad-to-represent-a-client-application"></a>Een andere toepassing registreren in azure AD om een client toepassing aan te duiden

Elke client toepassing die de API aanroept, moet worden geregistreerd als een toepassing in azure AD. In dit voor beeld is de client toepassing de **ontwikkelaars console** in het API Management ontwikkelaars Portal. 

Een andere toepassing registreren in azure AD om de ontwikkelaars console te vertegenwoordigen:

1. Ga naar de [Azure Portal](https://portal.azure.com) om uw toepassing te registreren.

1. Zoek en selecteer **app-registraties**.

1. Selecteer **Nieuwe registratie**.

1. Wanneer de pagina **Een toepassing registreren** verschijnt, voert u de registratiegegevens van de toepassing in:

   - Voer in de sectie **naam** een zinvolle toepassings naam in die wordt weer gegeven voor gebruikers van de app, zoals *client-app*. 
   - Selecteer in de sectie **ondersteunde account typen** de optie **accounts in elke organisatie Directory (een Azure AD-Directory-multi tenant)**. 

1. Selecteer in de sectie **omleidings-URI** `Web` het veld URL voor nu leeg.

1. Selecteer **Registreren** om de toepassing te maken. 

1. Zoek de waarde **Toepassings-id (client)** op de app-pagina **Overzicht** en noteer deze voor later.

1. Maak een client geheim voor deze toepassing voor gebruik in een volgende stap.

   1. Selecteer in de lijst met pagina's voor uw client-app **certificaten & geheimen** en selecteer **Nieuw client geheim**.

   1. Geef onder **een client geheim toevoegen** een **Beschrijving** op. Kies wanneer de sleutel moet verlopen en selecteer **toevoegen**.

Wanneer het geheim is gemaakt, noteert u de sleutel waarde voor gebruik in een volgende stap. 

## <a name="grant-permissions-in-azure-ad"></a>Machtigingen verlenen in azure AD

Nu u twee toepassingen hebt geregistreerd die de API en de ontwikkelaars console vertegenwoordigen, moet u machtigingen verlenen om de client-app toe te staan de back-end-app aan te roepen.  

1. Ga naar de [Azure Portal](https://portal.azure.com) om machtigingen toe te kennen aan uw client toepassing. Zoek en selecteer **app-registraties**.

1. Kies uw client-app. Selecteer vervolgens in de lijst met pagina's voor de app **API-machtigingen**.

1. Selecteer **een machtiging toevoegen**.

1. Selecteer onder **een API selecteren** **de optie mijn api's**, zoek en selecteer vervolgens uw back-end-app.

1. Selecteer onder **gedelegeerde machtigingen** de juiste machtigingen voor uw back-end-app en selecteer vervolgens **machtigingen toevoegen**.

1. Selecteer eventueel op de pagina **API-machtigingen** de optie **\<your-tenant-name> toestemming van beheerder geven** om toestemming uit te geven namens alle gebruikers in deze map. 

## <a name="enable-oauth-20-user-authorization-in-the-developer-console"></a>OAuth 2,0-gebruikers autorisatie inschakelen in de ontwikkelaars console

Op dit moment hebt u uw toepassingen gemaakt in azure AD en hebt u de juiste machtigingen verleend om de client-app toe te staan de back-end-app aan te roepen. 

In dit voor beeld is de ontwikkelaars console de client-app. In de volgende stappen wordt beschreven hoe u OAuth 2,0-gebruikers autorisatie inschakelt in de ontwikkelaars console. 

1. Ga in Azure Portal naar uw API Management-exemplaar.

1. Selecteer **OAuth 2,0**  >  **toevoegen**.

1. Geef een **weergave naam** en een **Beschrijving** op.

1. Voer voor de URL voor de **registratie pagina** van de client een tijdelijke aanduiding in, bijvoorbeeld `http://localhost` . De **URL voor de registratie pagina** van de client verwijst naar een pagina die gebruikers kunnen gebruiken om hun eigen accounts te maken en te configureren voor OAuth 2,0-providers die dit ondersteunen. In dit voor beeld maken gebruikers geen eigen accounts en configureren ze daarom een tijdelijke aanduiding.

1. Selecteer **autorisatie code** voor het **type autorisatie verlening**.

1. Geef de URL van het **autorisatie-eind punt** en de URL van het **token eind punt** op. Deze waarden worden opgehaald van de pagina **eind punten** in uw Azure AD-Tenant. Blader opnieuw naar de pagina **app-registraties** en selecteer **eind punten**.


1. Kopieer het **OAuth 2,0-autorisatie-eind punt** en plak dit in het tekstvak **URL van autorisatie-eind punt** . Selecteer **bericht** onder Autorisatie aanvraag methode.

1. Kopieer het **OAuth 2,0 token-eind punt** en plak het in het tekstvak **URL voor token-eind punt** . 

   >[!IMPORTANT]
   > Gebruik **v1** -of **v2** -eind punten. Afhankelijk van de versie die u kiest, is de onderstaande stap echter anders. U kunt het beste v2-eind punten gebruiken. 

1. Als u **v1** -eind punten gebruikt, voegt u een body-para meter met de naam **resource** toe. Gebruik de **toepassings-id** van de back-end-app voor de waarde van deze para meter. 

1. Als u **v2** -eind punten gebruikt, gebruikt u het bereik dat u hebt gemaakt voor de back-end-app in het **standaard bereik** veld. Zorg er ook voor dat u de waarde voor de [`accessTokenAcceptedVersion`](../active-directory/develop/reference-app-manifest.md#accesstokenacceptedversion-attribute) eigenschap instelt op `2` in het manifest van de [toepassing](../active-directory/develop/reference-app-manifest.md).

1. Geef vervolgens de client referenties op. Dit zijn de referenties voor de client-app.

1. Gebruik voor **client-id** de **toepassings-id** van de client-app.

1. Gebruik voor **client geheim** de sleutel die u eerder hebt gemaakt voor de client-app. 

1. Direct na het client geheim bevindt zich het **redirect_url** voor het toekennings type voor autorisatie code. Noteer deze URL.

1. Selecteer **Maken**.

1. Ga terug naar de registratie van uw client-app in Azure Active Directory en selecteer **verificatie**.

1. Klik onder **platform configuraties** op **een platform toevoegen** en selecteer het type als **Web**, plak de **redirect_url** onder **omleidings-URI** en klik vervolgens op de knop **configureren** om op te slaan.

Nu u een OAuth 2,0-autorisatie server hebt geconfigureerd, kan de ontwikkelaars console toegangs tokens van Azure AD verkrijgen. 

De volgende stap is om OAuth 2,0-gebruikers autorisatie in te scha kelen voor uw API. Op deze manier kan de ontwikkelaars console weten dat het nodig is om een toegangs token te verkrijgen namens de gebruiker voordat u de API aanroept.

1. Blader naar uw API Management-exemplaar en ga naar **api's**.

1. Selecteer de API die u wilt beveiligen. Bijvoorbeeld `Echo API`.

1. Ga naar **Settings**.

1. Kies onder **beveiliging** **OAuth 2,0** en selecteer de OAuth 2,0-server die u eerder hebt geconfigureerd. 

1. Selecteer **Opslaan**.

## <a name="successfully-call-the-api-from-the-developer-portal"></a>De API kan worden aangeroepen vanuit de ontwikkelaars Portal

> [!NOTE]
> Deze sectie is niet van toepassing op de laag **verbruik** , die geen ondersteuning biedt voor de ontwikkelaars Portal.

Nu de OAuth 2,0-gebruikers autorisatie is ingeschakeld op uw API, zal de ontwikkelaars console een toegangs token namens de gebruiker verkrijgen voordat de API wordt aangeroepen.

1. Blader naar een wille keurige bewerking onder de API in de ontwikkelaars Portal en selecteer **proberen**. Hiermee wordt de ontwikkelaars console geopend.

1. Noteer een nieuw item in het gedeelte **autorisatie** , dat overeenkomt met de autorisatie server die u zojuist hebt toegevoegd.

1. Selecteer **autorisatie code** in de vervolg keuzelijst autorisatie en u wordt gevraagd u aan te melden bij de Azure AD-Tenant. Als u al bent aangemeld met het account, wordt u mogelijk niet gevraagd.

1. Na een geslaagde aanmelding wordt een `Authorization` header toegevoegd aan de aanvraag, met een toegangs token van Azure AD. Hier volgt een voor beeld van een token (base64-gecodeerd):

   ```
   Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGciOiJSUzI1NiIsIng1dCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCIsImtpZCI6IlNTUWRoSTFjS3ZoUUVEU0p4RTJnR1lzNDBRMCJ9.eyJhdWQiOiIxYzg2ZWVmNC1jMjZkLTRiNGUtODEzNy0wYjBiZTEyM2NhMGMiLCJpc3MiOiJodHRwczovL3N0cy53aW5kb3dzLm5ldC80NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgvIiwiaWF0IjoxNTIxMTUyNjMzLCJuYmYiOjE1MjExNTI2MzMsImV4cCI6MTUyMTE1NjUzMywiYWNyIjoiMSIsImFpbyI6IkFWUUFxLzhHQUFBQUptVzkzTFd6dVArcGF4ZzJPeGE1cGp2V1NXV1ZSVnd1ZXZ5QU5yMlNkc0tkQmFWNnNjcHZsbUpmT1dDOThscUJJMDhXdlB6cDdlenpJdzJLai9MdWdXWWdydHhkM1lmaDlYSGpXeFVaWk9JPSIsImFtciI6WyJyc2EiXSwiYXBwaWQiOiJhYTY5ODM1OC0yMWEzLTRhYTQtYjI3OC1mMzI2NTMzMDUzZTkiLCJhcHBpZGFjciI6IjEiLCJlbWFpbCI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsImZhbWlseV9uYW1lIjoiSmlhbmciLCJnaXZlbl9uYW1lIjoiTWlhbyIsImlkcCI6Imh0dHBzOi8vc3RzLndpbmRvd3MubmV0LzcyZjk4OGJmLTg2ZjEtNDFhZi05MWFiLTJkN2NkMDExZGI0Ny8iLCJpcGFkZHIiOiIxMzEuMTA3LjE3NC4xNDAiLCJuYW1lIjoiTWlhbyBKaWFuZyIsIm9pZCI6IjhiMTU4ZDEwLWVmZGItNDUxMS1iOTQzLTczOWZkYjMxNzAyZSIsInNjcCI6InVzZXJfaW1wZXJzb25hdGlvbiIsInN1YiI6IkFGaWtvWFk1TEV1LTNkbk1pa3Z3MUJzQUx4SGIybV9IaVJjaHVfSEM1aGciLCJ0aWQiOiI0NDc4ODkyMC05Yjk3LTRmOGItODIwYS0yMTFiMTMzZDk1MzgiLCJ1bmlxdWVfbmFtZSI6Im1pamlhbmdAbWljcm9zb2Z0LmNvbSIsInV0aSI6ImFQaTJxOVZ6ODBXdHNsYjRBMzBCQUEiLCJ2ZXIiOiIxLjAifQ.agGfaegYRnGj6DM_-N_eYulnQdXHhrsus45QDuApirETDR2P2aMRxRioOCR2YVwn8pmpQ1LoAhddcYMWisrw_qhaQr0AYsDPWRtJ6x0hDk5teUgbix3gazb7F-TVcC1gXpc9y7j77Ujxcq9z0r5lF65Y9bpNSefn9Te6GZYG7BgKEixqC4W6LqjtcjuOuW-ouy6LSSox71Fj4Ni3zkGfxX1T_jiOvQTd6BBltSrShDm0bTMefoyX8oqfMEA2ziKjwvBFrOjO0uK4rJLgLYH4qvkR0bdF9etdstqKMo5gecarWHNzWi_tghQu9aE3Z3EZdYNI_ZGM-Bbe3pkCfvEOyA
   ```

1. Selecteer **verzenden** om de API met succes aan te roepen.

## <a name="configure-a-jwt-validation-policy-to-pre-authorize-requests"></a>Een JWT-validatie beleid configureren om aanvragen vooraf te autoriseren

Wanneer een gebruiker op dit moment probeert een aanroep te doen vanuit de ontwikkelaars console, wordt de gebruiker gevraagd zich aan te melden. De ontwikkelaars console verkrijgt een toegangs token namens de gebruiker en bevat het token in de aanvraag voor de API.

Wat gebeurt er echter als iemand uw API aanroept zonder een token of met een ongeldig token? U kunt bijvoorbeeld proberen om de API aan te roepen zonder de `Authorization` header, maar de aanroep gaat door. De reden hiervoor is dat API Management het toegangs token op dit moment niet valideert. De header wordt gewoon door gegeven `Authorization` aan de back-end-API.

Gebruik de [validatie JWT](./api-management-access-restriction-policies.md#ValidateJWT) -beleid om aanvragen vooraf te autoriseren in API Management door de toegangs tokens van elke binnenkomende aanvraag te valideren. Als een aanvraag geen geldig token heeft, wordt deze door API Management geblokkeerd. Voeg bijvoorbeeld het volgende beleid toe aan de `<inbound>` sectie beleid van de `Echo API` . Hiermee wordt de claim van een doel groep in een toegangs token gecontroleerd en wordt een fout bericht geretourneerd als het token ongeldig is. Zie [beleid instellen of bewerken](./set-edit-policies.md)voor meer informatie over het configureren van beleid.


```xml
<validate-jwt header-name="Authorization" failed-validation-httpcode="401" failed-validation-error-message="Unauthorized. Access token is missing or invalid.">
    <openid-config url="https://login.microsoftonline.com/{aad-tenant}/.well-known/openid-configuration" />
    <required-claims>
        <claim name="aud">
            <value>{Application ID of backend-app}</value>
        </claim>
    </required-claims>
</validate-jwt>
```

> [!NOTE]
> Deze `openid-config` URL komt overeen met het v1-eind punt. Voor het v2 `openid-config` -eind punt gebruikt u de volgende URL:
>
> `https://login.microsoftonline.com/common/v2.0/.well-known/openid-configuration`.

## <a name="build-an-application-to-call-the-api"></a>Een toepassing bouwen om de API aan te roepen

In deze hand leiding hebt u de ontwikkelaars console in API Management gebruikt als de voor beeld-client toepassing om het `Echo API` beveiligde door OAuth 2,0 aan te roepen. Zie [Azure Active Directory code voorbeelden](../active-directory/develop/sample-v2-code.md)voor meer informatie over het bouwen van een toepassing en het implementeren van OAuth 2,0.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Azure Active Directory en OAuth 2.0](../active-directory/develop/authentication-vs-authorization.md).
- Bekijk meer [Video's](https://azure.microsoft.com/documentation/videos/index/?services=api-management) over API management.
- Zie [wederzijdse verificatie van certificaten](./api-management-howto-mutual-certificates.md)voor andere manieren om uw back-end-service te beveiligen.
- [Een API Management service-exemplaar maken](./get-started-create-service-instance.md).
- [Uw eerste API beheren](./import-and-publish.md).
