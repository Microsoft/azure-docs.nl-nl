---
title: API-connectors toevoegen aan self-service-aanmeld stromen-Azure AD
description: Een web-API configureren voor gebruik in een gebruikers stroom.
services: active-directory
ms.service: active-directory
ms.subservice: B2B
ms.topic: article
ms.date: 03/02/2021
ms.author: mimart
author: msmimart
manager: celestedg
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 703e3b4c951bc4c3a22f82b9faa31789d1abf868
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103008719"
---
# <a name="add-an-api-connector-to-a-user-flow"></a>Een API-connector toevoegen aan een gebruikers stroom

Als u een [API-connector](api-connectors-overview.md)wilt gebruiken, maakt u eerst de API-connector en schakelt u deze in in een gebruikers stroom.

> [!IMPORTANT]
>**Vanaf 4 januari 2021** wordt [ondersteuning voor WebView-aanmelding afgeschaft](https://developers.googleblog.com/2020/08/guidance-for-our-effort-to-block-less-secure-browser-and-apps.html) in Google. Als u gebruikmaakt van Google-federatie of selfserviceregistratie met Gmail, moet u [de compatibiliteit van uw systeemeigen Line-of-Business-toepassingen testen](google-federation.md#deprecation-of-webview-sign-in-support).

## <a name="create-an-api-connector"></a>Een API-connector maken

1. Meld u aan bij [Azure Portal](https://portal.azure.com/).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **alle API-connectors** en selecteer vervolgens **nieuwe API-connector**.

   ![Een nieuwe API-connector toevoegen](./media/self-service-sign-up-add-api-connector/api-connector-new.png)

5. Geef een weergave naam op voor de aanroep. **Controleer bijvoorbeeld goedkeurings status**.
6. Geef de **eind punt-URL** voor de API-aanroep op.
7. Kies het **verificatie type** en configureer de verificatie gegevens voor het aanroepen van uw API. Zie de sectie hieronder voor opties voor het beveiligen van uw API.

    ![Een API-connector configureren](./media/self-service-sign-up-add-api-connector/api-connector-config.png)

8. Selecteer **Opslaan**.

## <a name="securing-the-api-endpoint"></a>Het API-eind punt beveiligen
U kunt uw API-eind punt beveiligen door HTTP-basis verificatie of HTTPS-verificatie van client certificaten (preview) te gebruiken. In beide gevallen geeft u de referenties op die Azure Active Directory gebruikt voor het aanroepen van uw API-eind punt. Uw API-eind punt controleert vervolgens de referenties en voert autorisatie beslissingen uit.

### <a name="http-basic-authentication"></a>HTTP-basis verificatie
Basis verificatie HTTP is gedefinieerd in [RFC 2617](https://tools.ietf.org/html/rfc2617). Azure Active Directory verzendt een HTTP-aanvraag met de client referenties ( `username` en `password` ) in de `Authorization` header. De referenties zijn ingedeeld als de base64-gecodeerde teken reeks `username:password` . Uw API controleert vervolgens deze waarden om te bepalen of u een API-aanroep wilt afwijzen of niet.

### <a name="https-client-certificate-authentication-preview"></a>HTTPS-verificatie van client certificaten (preview-versie)

> [!IMPORTANT]
> Deze functionaliteit is beschikbaar als preview-versie en wordt uitgevoerd zonder een service overeenkomst. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

Verificatie van client certificaten is een wederzijdse verificatie op basis van certificaten, waarbij de client een client certificaat voor de server biedt om zijn identiteit te bewijzen. In dit geval gebruikt Azure Active Directory het certificaat dat u uploadt als onderdeel van de configuratie van de API-connector. Dit gebeurt als onderdeel van de SSL-handshake. Alleen services die over de juiste certificaten beschikken, hebben toegang tot uw API-service. Het client certificaat is een X. 509-digitaal certificaat. In productie omgevingen moet het worden ondertekend door een certificerings instantie. 

Als u een certificaat wilt maken, kunt u [Azure Key Vault](../../key-vault/certificates/create-certificate.md)gebruiken, dat beschikt over opties voor zelfondertekende certificaten en integraties met leveranciers van uitgevers van certificaten voor ondertekende certificaten. Vervolgens kunt u [het certificaat exporteren](../../key-vault/certificates/how-to-export-certificate.md) en uploaden voor gebruik in de API connectors-configuratie. Het wacht woord is alleen vereist voor certificaat bestanden die worden beveiligd met een wacht woord. U kunt ook de [cmdlet New-SelfSignedCertificate](../../active-directory-b2c/secure-rest-api.md#prepare-a-self-signed-certificate-optional) van Power shell gebruiken om een zelfondertekend certificaat te genereren.

Zie voor Azure App Service en Azure Functions [TLS-wederzijdse verificatie configureren](../../app-service/app-service-web-configure-tls-mutual-auth.md) voor meer informatie over het inschakelen en valideren van het certificaat vanuit uw API-eind punt.

U wordt aangeraden herinnerings waarschuwingen in te stellen voor wanneer uw certificaat verloopt. Als u een nieuw certificaat wilt uploaden naar een bestaande API-connector, selecteert u de API-connector onder **alle API** -connectors en klikt u op **Nieuw certificaat uploaden**. Het laatst geüploade certificaat dat niet is verlopen en na de begin datum valt, wordt automatisch door Azure Active Directory gebruikt.

### <a name="api-key"></a>API-sleutel
Sommige services gebruiken een API-sleutel om de toegang tot uw HTTP-eind punten op te maken tijdens de ontwikkeling. Voor [Azure functions](../../azure-functions/functions-bindings-http-webhook-trigger.md#authorization-keys)kunt u dit doen door de `code` para meter als query op te nemen in de **eind punt-URL**. Bijvoorbeeld `https://contoso.azurewebsites.net/api/endpoint` <b>`?code=0123456789`</b> ). 

Dit is geen mechanisme dat alleen in productie mag worden gebruikt. Daarom is configuratie voor basis verificatie of certificaat authenticatie altijd vereist. Als u een verificatie methode niet wilt implementeren (niet aanbevolen) voor ontwikkelings doeleinden, kunt u basis verificatie kiezen en tijdelijke waarden gebruiken voor `username` en `password` dat uw API kan worden genegeerd tijdens het implementeren van de autorisatie in uw API.

## <a name="the-request-sent-to-your-api"></a>De aanvraag die naar uw API wordt verzonden
Een API-connector resultatenset als een **http post-** aanvraag, waarbij gebruikers kenmerken (' claims ') worden verzonden als sleutel-waardeparen in een JSON-hoofd tekst. Kenmerken worden op dezelfde manier geserialiseerd als [Microsoft Graph](/graph/api/resources/user#properties) gebruikers eigenschappen. 

**Voorbeeldaanvraag**
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```

Alleen gebruikers eigenschappen en aangepaste kenmerken die worden weer gegeven in de **Azure Active Directory**  >  aangepaste gebruikers kenmerken van **externe identiteiten** kunnen  >   in de aanvraag worden verzonden.

Aangepaste kenmerken bestaan in de indeling **extension_ \<extensions-app-id> _AttributeName**  in de map. Uw API moet verwachten dat er claims in dezelfde geserialiseerde indeling worden ontvangen. Zie voor meer informatie over aangepaste kenmerken [aangepaste kenmerken definiëren voor Self-service aanmeldingen](user-flow-add-custom-attributes.md).

Daarnaast wordt de **gebruikers interface land instellingen (' ui_locales ')** standaard in alle aanvragen verzonden. Het biedt de land instellingen van een gebruiker, zoals geconfigureerd op hun apparaat, dat door de API kan worden gebruikt om internationale reacties te retour neren.

> [!IMPORTANT]
> Als een claim geen waarde heeft op het moment dat het API-eind punt wordt aangeroepen, wordt de claim niet verzonden naar de API. Uw API moet zo worden ontworpen dat er expliciet wordt gecontroleerd of de aanvraag wordt verwerkt.

> [!TIP] 
> [**identiteiten (' Identities ')**](/graph/api/resources/objectidentity) en het **e-mail adres (' e-mail ')** kunnen worden gebruikt door uw API om een gebruiker te identificeren voordat ze een account in uw Tenant hebben. De claim ' Identities ' wordt verzonden wanneer een gebruiker zich verifieert met een id-provider zoals Google of Facebook. e-mail bericht wordt altijd verzonden.

## <a name="enable-the-api-connector-in-a-user-flow"></a>De API-connector inschakelen in een gebruikers stroom

Volg deze stappen om een API-connector toe te voegen aan een self-service voor het registreren van een gebruikers stroom.

1. Meld u als een Azure AD-administrator aan bij de [Azure Portal](https://portal.azure.com/).
2. Onder **Azure-Services** selecteert u **Azure Active Directory**.
3. Selecteer in het linkermenu **externe identiteiten**.
4. Selecteer **gebruikers stromen** en selecteer vervolgens de gebruikers stroom waaraan u de API-connector wilt toevoegen.
5. Selecteer **API-connectors** en selecteer vervolgens de API-eind punten die u wilt aanroepen met de volgende stappen in de gebruikers stroom:

   - **Nadat u zich hebt aangemeld met een id-provider**
   - **Voordat u de gebruiker maakt**

   ![Api's toevoegen aan de gebruikers stroom](./media/self-service-sign-up-add-api-connector/api-connectors-user-flow-select.png)

6. Selecteer **Opslaan**.

## <a name="after-signing-in-with-an-identity-provider"></a>Nadat u zich hebt aangemeld met een id-provider

Een API-connector in deze stap van het registratie proces wordt onmiddellijk geactiveerd nadat de gebruiker is geverifieerd met een id-provider (zoals Google, Facebook, & Azure AD). Deze stap gaat vooraf aan de ***pagina kenmerk verzameling***. Dit is het formulier dat wordt weer gegeven aan de gebruiker om gebruikers kenmerken te verzamelen. Deze stap wordt niet aangeroepen als een gebruiker wordt geregistreerd met een lokaal account.

### <a name="example-request-sent-to-the-api-at-this-step"></a>Voorbeeld aanvraag die wordt verzonden naar de API in deze stap
```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "lastName":"Smith",
 "ui_locales":"en-US"
}
```

De exacte claims die worden verzonden naar de API, zijn afhankelijk van welke informatie wordt verstrekt door de ID-provider. e-mail bericht wordt altijd verzonden.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Verwachte antwoord typen van de Web-API bij deze stap

Wanneer de Web-API tijdens een gebruikers stroom een HTTP-aanvraag van Azure AD ontvangt, kan deze de volgende antwoorden retour neren:

- Vervolg reactie
- Antwoord blok keren

#### <a name="continuation-response"></a>Vervolg reactie

Een vervolg antwoord geeft aan dat de gebruikers stroom moet door gaan naar de volgende stap: de pagina kenmerk verzameling.

In een vervolg reactie kan de API claims retour neren. Als een claim wordt geretourneerd door de API, doet de claim het volgende:

- Hiermee wordt het invoer veld vooraf ingevuld op de pagina kenmerk verzameling.

Bekijk een voor beeld van een [vervolg reactie](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Antwoord blok keren

Een blokkerende reactie sluit de gebruikers stroom af. Het kan worden uitgegeven door de API om de voortzetting van de gebruikers stroom te stoppen door een blok pagina voor de gebruiker weer te geven. Op de pagina blok keren wordt de weer gegeven `userMessage` van de API.

Bekijk een voor beeld van een [blokkerend antwoord](#example-of-a-blocking-response).

## <a name="before-creating-the-user"></a>Voordat u de gebruiker maakt

Een API-connector in deze stap van het registratie proces wordt aangeroepen na de pagina kenmerk verzameling, als er een is opgenomen. Deze stap wordt altijd aangeroepen voordat een gebruikers account wordt gemaakt in azure AD. 

### <a name="example-request-sent-to-the-api-at-this-step"></a>Voorbeeld aanvraag die wordt verzonden naar de API in deze stap

```http
POST <API-endpoint>
Content-type: application/json

{
 "email": "johnsmith@fabrikam.onmicrosoft.com",
 "identities": [ // Sent for Google, Facebook, and Email One Time Passcode identity providers 
     {
     "signInType":"federated",
     "issuer":"facebook.com",
     "issuerAssignedId":"0123456789"
     }
 ],
 "displayName": "John Smith",
 "givenName":"John",
 "surname":"Smith",
 "jobTitle":"Supplier",
 "streetAddress":"1000 Microsoft Way",
 "city":"Seattle",
 "postalCode": "12345",
 "state":"Washington",
 "country":"United States",
 "extension_<extensions-app-id>_CustomAttribute1": "custom attribute value",
 "extension_<extensions-app-id>_CustomAttribute2": "custom attribute value",
 "ui_locales":"en-US"
}
```
De exacte claims die worden verzonden naar de API, zijn afhankelijk van welke gegevens van de gebruiker worden verzameld of door de ID-provider worden verstrekt.

### <a name="expected-response-types-from-the-web-api-at-this-step"></a>Verwachte antwoord typen van de Web-API bij deze stap

Wanneer de Web-API tijdens een gebruikers stroom een HTTP-aanvraag van Azure AD ontvangt, kan deze de volgende antwoorden retour neren:

- Vervolg reactie
- Antwoord blok keren
- Validatie antwoord

#### <a name="continuation-response"></a>Vervolg reactie
Een vervolg antwoord geeft aan dat de gebruikers stroom moet door gaan naar de volgende stap: Maak de gebruiker in de Directory.

In een vervolg reactie kan de API claims retour neren. Als een claim wordt geretourneerd door de API, doet de claim het volgende:

- Overschrijft elke waarde die al is toegewezen aan de claim op de pagina kenmerk verzameling.

Bekijk een voor beeld van een [vervolg reactie](#example-of-a-continuation-response).

#### <a name="blocking-response"></a>Antwoord blok keren
Een blokkerende reactie sluit de gebruikers stroom af. Het kan worden uitgegeven door de API om de voortzetting van de gebruikers stroom te stoppen door een blok pagina voor de gebruiker weer te geven. Op de pagina blok keren wordt de weer gegeven `userMessage` van de API.

Bekijk een voor beeld van een [blokkerend antwoord](#example-of-a-blocking-response).

### <a name="validation-error-response"></a>Validatie-fout bericht
 Wanneer de API reageert met een antwoord op validatie-fout, blijft de gebruikers stroom op de pagina kenmerk verzameling en `userMessage` wordt er een weer gegeven voor de gebruiker. De gebruiker kan vervolgens het formulier bewerken en opnieuw verzenden. Dit type antwoord kan worden gebruikt voor invoer validatie.

Bekijk een voor beeld van een [validatie fout](#example-of-a-validation-error-response).

## <a name="example-responses"></a>Voorbeeld reacties

### <a name="example-of-a-continuation-response"></a>Voor beeld van een vervolg reactie

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "Continue",
    "postalCode": "12349", // return claim
    "extension_<extensions-app-id>_CustomAttribute": "value" // return claim
}
```

| Parameter                                          | Type              | Vereist | Beschrijving                                                                                                                                                                                                                                                                            |
| -------------------------------------------------- | ----------------- | -------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| versie                                            | Tekenreeks            | Ja      | De versie van de API.                                                                                                                                                                                                                                                                |
| actie                                             | Tekenreeks            | Ja      | Waarde moet zijn `Continue` .                                                                                                                                                                                                                                                              |
| \<builtInUserAttribute>                            | \<attribute-type> | Nee       | Waarden kunnen worden opgeslagen in de map als ze zijn geselecteerd als een **claim om te ontvangen** in de API-connector configuratie en **gebruikers kenmerken** voor een gebruikers stroom. Waarden kunnen worden geretourneerd in het token als deze zijn geselecteerd als een **toepassings claim**.                                              |
| \<extension\_{extensions-app-id}\_CustomAttribute> | \<attribute-type> | Nee       | De geretourneerde claim hoeft niet te bevatten `_<extensions-app-id>_` . Geretourneerde waarden kunnen waarden overschrijven die zijn verzameld van een gebruiker. Ze kunnen ook worden geretourneerd in het token indien geconfigureerd als onderdeel van de toepassing.  |

### <a name="example-of-a-blocking-response"></a>Voor beeld van een blokkerend antwoord

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "version": "1.0.0",
    "action": "ShowBlockPage",
    "userMessage": "There was a problem with your request. You are not able to sign up at this time.",
}

```

| Parameter   | Type   | Vereist | Beschrijving                                                                |
| ----------- | ------ | -------- | -------------------------------------------------------------------------- |
| versie     | Tekenreeks | Ja      | De versie van de API.                                                    |
| actie      | Tekenreeks | Ja      | Waarde moet `ShowBlockPage`                                              |
| userMessage | Tekenreeks | Ja      | Bericht dat wordt weergegeven aan de gebruiker.                                            |

**Eind gebruikers ervaring met een blokkerend antwoord**

![Voor beeld blok pagina](./media/api-connectors-overview/blocking-page-response.png)

### <a name="example-of-a-validation-error-response"></a>Voor beeld van een validatie-fout bericht

```http
HTTP/1.1 400 Bad Request
Content-type: application/json

{
    "version": "1.0.0",
    "status": 400,
    "action": "ValidationError",
    "userMessage": "Please enter a valid Postal Code.",
}
```

| Parameter   | Type    | Vereist | Beschrijving                                                                |
| ----------- | ------- | -------- | -------------------------------------------------------------------------- |
| versie     | Tekenreeks  | Ja      | De versie van uw API.                                                    |
| actie      | Tekenreeks  | Ja      | Waarde moet zijn `ValidationError` .                                           |
| status      | Geheel getal | Ja      | Dit moet een waarde zijn `400` voor een ValidationError-antwoord.                        |
| userMessage | Tekenreeks  | Ja      | Bericht dat wordt weergegeven aan de gebruiker.                                            |

> [!NOTE]
> De HTTP-status code moet naast de status waarde in de hoofd tekst van het antwoord ' 400 ' zijn.

**Eind gebruikers ervaring met validatie-fout bericht**

![Voorbeeld validatie pagina](./media/api-connectors-overview/validation-error-postal-code.png)


## <a name="best-practices-and-how-to-troubleshoot"></a>Aanbevolen procedures en problemen oplossen

### <a name="using-serverless-cloud-functions"></a>Cloud functies zonder server gebruiken
Serverloze functies, zoals HTTP-triggers in Azure Functions, bieden een eenvoudige manier om API-eind punten te maken voor gebruik met de API-connector. U kunt de Cloud functie zonder server gebruiken om [bijvoorbeeld](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts)validatie logica uit te voeren en aanmeldings pogingen te beperken tot specifieke e-mail domeinen. De Cloud functie zonder server kan ook andere web-Api's, gebruikers archieven en andere Cloud Services aanroepen en aanroepen voor complexere scenario's.

### <a name="best-practices"></a>Aanbevolen procedures
Zorg ervoor dat:
* Uw API volgt de API-aanvraag-en respons contracten zoals hierboven wordt beschreven. 
* De **eind punt-URL** van de API-connector verwijst naar het juiste API-eind punt.
* Uw API controleert expliciet op null-waarden van ontvangen claims.
* Uw API reageert zo snel mogelijk om een onervaren gebruikers ervaring te garanderen.
    * Als u een serverloze functie of schaal bare webservice gebruikt, gebruikt u een hosting abonnement waarmee de API ' actief ' of ' warme ' wordt bewaard. in productie. Voor Azure Functions is de aanbevolen het Premium- [abonnement](../../azure-functions/functions-scale.md) te gebruiken

### <a name="use-logging"></a>Logboek registratie gebruiken
Over het algemeen is het handig om de hulpprogram ma's voor logboek registratie te gebruiken die zijn ingeschakeld door uw web API-service, zoals [Application Insights](../../azure-functions/functions-monitoring.md), om uw API te controleren op onverwachte fout codes, uitzonde ringen en slechte prestaties.
* Monitor voor HTTP-status codes die niet HTTP 200 of 400 zijn.
* Een 401-of 403 HTTP-status code geeft meestal aan dat er een probleem is met uw verificatie. Controleer de Authentication Layer van de API en de bijbehorende configuratie in de API-connector.
* Gebruik zo nodig meer agressieve niveaus van logboek registratie (bijvoorbeeld ' tracering ' of ' fout opsporing ') in ontwikkeling.
* Controleer uw API op lange reactie tijden.

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over het [toevoegen van een aangepaste goedkeurings werk stroom aan self-service registratie](self-service-sign-up-add-approvals.md)
- Ga aan de slag met onze Quick start-voor [beelden](code-samples-self-service-sign-up.md#api-connector-azure-function-quickstarts).