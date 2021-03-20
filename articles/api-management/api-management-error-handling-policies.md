---
title: Fout afhandeling in azure API Management-beleid | Microsoft Docs
description: Meer informatie over hoe u kunt reageren op fout condities die zich kunnen voordoen tijdens het verwerken van aanvragen in azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 3c777964-02b2-4f55-8731-8c3bd3c0ae27
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 01/10/2020
ms.author: apimpm
ms.openlocfilehash: a3b6f90d0aa26b478c0f2fcefac55dcd509da437
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92070942"
---
# <a name="error-handling-in-api-management-policies"></a>Fout bij het verwerken van API Management-beleid

Door een object op te geven `ProxyError` , kunnen uitgevers met Azure API Management reageren op fout voorwaarden. Dit kan voor komen tijdens het verwerken van aanvragen. Het `ProxyError` object wordt geopend via de [context. ](api-management-policy-expressions.md#ContextVariables) De eigenschap Last error en kan worden gebruikt door beleid in de `on-error` sectie Policy. In dit artikel vindt u informatie over de mogelijkheden voor het afhandelen van fouten in azure API Management.

## <a name="error-handling-in-api-management"></a>Fout afhandeling in API Management

Beleids regels in azure API management worden onderverdeeld in `inbound` , `backend` ,, en, `outbound` `on-error` zoals in het volgende voor beeld wordt weer gegeven.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is
         forwarded to the backend service go here -->
    </backend>
    <outbound>
      <!-- statements to be applied to the response go here -->
    </outbound>
    <on-error>
        <!-- statements to be applied if there is an error
             condition go here -->
  </on-error>
</policies>
```

Tijdens de verwerking van een aanvraag worden de ingebouwde stappen uitgevoerd, samen met alle beleids regels die binnen het bereik van de aanvraag vallen. Als er een fout optreedt, wordt de verwerking direct naar de `on-error` sectie beleid verwezen.
De `on-error` sectie beleid kan worden gebruikt in elk bereik. API-uitgevers kunnen aangepaste gedragingen configureren, zoals het vastleggen van de fout in Event hubs of het maken van een nieuw antwoord om terug te keren naar de aanroeper.

> [!NOTE]
> De `on-error` sectie is standaard niet in beleids regels aanwezig. Als u de `on-error` sectie wilt toevoegen aan een beleid, bladert u naar het gewenste beleid in de beleids editor en voegt u het toe. Zie [beleid in API Management](./api-management-howto-policies.md)voor meer informatie over het configureren van beleid.
>
> Als er geen `on-error` sectie is, ontvangen aanroepers 400-of 500-HTTP-antwoord berichten als er een fout optreedt.

### <a name="policies-allowed-in-on-error"></a>Beleid dat is toegestaan in on-error

Het volgende beleid kan worden gebruikt in de `on-error` sectie Policy.

-   [desgewenst](api-management-advanced-policies.md#choose)
-   [set-variabele](api-management-advanced-policies.md#set-variable)
-   [zoeken en vervangen](api-management-transformation-policies.md#Findandreplacestringinbody)
-   [retour-antwoord](api-management-advanced-policies.md#ReturnResponse)
-   [set-header](api-management-transformation-policies.md#SetHTTPheader)
-   [set-methode](api-management-advanced-policies.md#SetRequestMethod)
-   [set-status](api-management-advanced-policies.md#SetStatus)
-   [verzenden/aanvragen](api-management-advanced-policies.md#SendRequest)
-   [Send-eenrichtings aanvraag](api-management-advanced-policies.md#SendOneWayRequest)
-   [aanmelden bij eventhub](api-management-advanced-policies.md#log-to-eventhub)
-   [JSON-to-XML](api-management-transformation-policies.md#ConvertJSONtoXML)
-   [XML naar JSON](api-management-transformation-policies.md#ConvertXMLtoJSON)
-   [limiet-gelijktijdigheid](api-management-advanced-policies.md#LimitConcurrency)
-   [model-reactie](api-management-advanced-policies.md#mock-response)
-   [voeren](api-management-advanced-policies.md#Retry)
-   [tracerings](api-management-advanced-policies.md#Trace)

## <a name="lasterror"></a>LastError

Als er een fout optreedt en er wordt verwezen naar de `on-error` beleids sectie, wordt de fout opgeslagen in de [context. ](api-management-policy-expressions.md#ContextVariables) De eigenschap Last error, die kan worden geopend door beleid in de `on-error` sectie. Last error heeft de volgende eigenschappen.

| Naam       | Type   | Beschrijving                                                                                               | Vereist |
| ---------- | ------ | --------------------------------------------------------------------------------------------------------- | -------- |
| `Source`   | tekenreeks | De naam van het element waarop de fout is opgetreden. Dit kan een beleids regel of een ingebouwde pijplijn naam zijn.      | Ja      |
| `Reason`   | tekenreeks | Machine vriendelijke fout code, die kan worden gebruikt voor het afhandelen van fouten.                                       | Nee       |
| `Message`  | tekenreeks | Beschrijving van door de mens lees bare fout.                                                                         | Ja      |
| `Scope`    | tekenreeks | De naam van het bereik waarin de fout is opgetreden en kan een van de volgende zijn: ' Global ', ' product ', ' API ' of ' Operation ' | Nee       |
| `Section`  | tekenreeks | Sectie naam waarin fout is opgetreden. Mogelijke waarden: ' binnenkomend ', ' back-end ', ' outbound ' of ' on-error '.      | Nee       |
| `Path`     | tekenreeks | Hiermee geeft u genest beleid op, bijvoorbeeld ' Kies [3]/when [2] '.                                                 | Nee       |
| `PolicyId` | tekenreeks | Waarde van het `id` kenmerk, indien opgegeven door de klant, op het beleid waar fout is opgetreden             | Nee       |

> [!TIP]
> U kunt de status code openen met behulp van context. Response. status code.

> [!NOTE]
> Alle beleids regels hebben een optioneel `id` kenmerk dat kan worden toegevoegd aan het hoofd element van het beleid. Als dit kenmerk aanwezig is in een beleid als er een fout optreedt, kan de waarde van het kenmerk worden opgehaald met behulp van de `context.LastError.PolicyId` eigenschap.

## <a name="predefined-errors-for-built-in-steps"></a>Vooraf gedefinieerde fouten voor ingebouwde stappen

De volgende fouten zijn vooraf gedefinieerd voor fout situaties die kunnen optreden tijdens de evaluatie van de ingebouwde verwerkings stappen.

| Bron        | Voorwaarde                                 | Reden                  | Bericht                                                                                                                |
| ------------- | ----------------------------------------- | ----------------------- | ---------------------------------------------------------------------------------------------------------------------- |
| configuratie | De URI komt niet overeen met een API of bewerking | OperationNotFound       | Kan de inkomende aanvraag niet overeenkomen met een bewerking.                                                                      |
| autorisatie | De abonnements sleutel is niet opgegeven             | SubscriptionKeyNotFound | De toegang is geweigerd vanwege een ontbrekende abonnements sleutel. Zorg ervoor dat u de abonnements sleutel opneemt bij het maken van aanvragen voor deze API. |
| autorisatie | De waarde van de abonnements sleutel is ongeldig         | SubscriptionKeyInvalid  | De toegang is geweigerd vanwege een ongeldige abonnements sleutel. Zorg ervoor dat u een geldige sleutel opgeeft voor een actief abonnement.            |
| veelvoud | De downstream-verbinding (van een client naar een API Management Gateway) is afgebroken door de client terwijl de aanvraag in behandeling was | ClientConnectionFailure | veelvoud |
| veelvoud | De upstream-verbinding (van een API Management gateway naar een back-end-service) is niet tot stand gebracht of is afgebroken door de back-end | BackendConnectionFailure | veelvoud |
| veelvoud | Er is een runtime-uitzonde ring opgetreden tijdens de evaluatie van een bepaalde expressie | ExpressionValueEvaluationFailure | veelvoud |

## <a name="predefined-errors-for-policies"></a>Vooraf gedefinieerde fouten voor beleids regels

De volgende fouten zijn vooraf gedefinieerd voor fout situaties die kunnen optreden tijdens de beleids evaluatie.

| Bron       | Voorwaarde                                                       | Reden                    | Bericht                                                                                                                              |
| ------------ | --------------------------------------------------------------- | ------------------------- | ------------------------------------------------------------------------------------------------------------------------------------ |
| frequentie limiet   | Frequentie limiet overschreden                                             | RateLimitExceeded         | Frequentie limiet is overschreden                                                                                                               |
| quota        | Quotum overschreden                                                  | QuotaExceeded             | Volumequotum buiten bereik. Het quotum wordt aangevuld in xx: xx: xx. -of-verouderd quotum voor de band breedte. Het quotum wordt aangevuld in xx: xx: xx. |
| Jsonp        | Waarde van call back-para meter is ongeldig (bevat onjuiste tekens) | CallbackParameterInvalid  | De waarde van de call back-para meter {call back-para meter-name} is geen geldige java script-id.                                          |
| IP-filter    | Parseren van beller-IP uit aanvraag mislukt                          | FailedToParseCallerIP     | Kan geen IP-adres voor de oproepende functie instellen. Toegang geweigerd.                                                                        |
| IP-filter    | Het IP-adres van de aanroeper is niet in de lijst met toegestane adressen                                | CallerIpNotAllowed        | Het IP-adres van de oproepende functie {IP-Address} is niet toegestaan. Toegang geweigerd.                                                                        |
| IP-filter    | IP van beller bevindt zich in de lijst met geblokkeerde adressen                                    | CallerIpBlocked           | Het IP-adres van de beller is geblokkeerd. Toegang geweigerd.                                                                                         |
| check-header | De vereiste header is niet weer gegeven of de waarde ontbreekt               | HeaderNotFound            | Header {header-name} is niet gevonden in de aanvraag. Toegang geweigerd.                                                                    |
| check-header | De vereiste header is niet weer gegeven of de waarde ontbreekt               | HeaderValueNotAllowed     | Header {header-name} waarde {header-value} is niet toegestaan. Toegang geweigerd.                                                          |
| validate-JWT | JWT-token ontbreekt in aanvraag                                 | TokenNotFound             | De JWT is niet gevonden in de aanvraag. Toegang geweigerd.                                                                                         |
| validate-JWT | Validatie van hand tekening is mislukt                                     | TokenSignatureInvalid     | <bericht van de JWT-bibliotheek \> . Toegang geweigerd.                                                                                          |
| validate-JWT | Ongeldige doel groep                                                | TokenAudienceNotAllowed   | <bericht van de JWT-bibliotheek \> . Toegang geweigerd.                                                                                          |
| validate-JWT | Ongeldige verlener                                                  | TokenIssuerNotAllowed     | <bericht van de JWT-bibliotheek \> . Toegang geweigerd.                                                                                          |
| validate-JWT | Token is verlopen                                                   | TokenExpired              | <bericht van de JWT-bibliotheek \> . Toegang geweigerd.                                                                                          |
| validate-JWT | De handtekening sleutel is niet omgezet op basis van de ID                            | TokenSignatureKeyNotFound | <bericht van de JWT-bibliotheek \> . Toegang geweigerd.                                                                                          |
| validate-JWT | De vereiste claims ontbreken in het token                          | TokenClaimNotFound        | In het JWT-token ontbreken de volgende claims: <C1 \> , <C2 \> ,... Toegang geweigerd.                                                            |
| validate-JWT | Claim waarden komen niet overeen                                           | TokenClaimValueNotAllowed | Claim {claim naam} waarde {claim-value} is niet toegestaan. Toegang geweigerd.                                                             |
| validate-JWT | Andere validatie fouten                                       | JwtInvalid                | <bericht van de JWT-bibliotheek\>                                                                                                          |
| aanvraag voor door sturen of verzenden | De status code van het HTTP-antwoord is niet ontvangen van de back-end binnen de geconfigureerde time-out | Time-out | veelvoud |

## <a name="example"></a>Voorbeeld

Een API-beleid instellen op:

```xml
<policies>
    <inbound>
        <base />
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
    <on-error>
        <set-header name="ErrorSource" exists-action="override">
            <value>@(context.LastError.Source)</value>
        </set-header>
        <set-header name="ErrorReason" exists-action="override">
            <value>@(context.LastError.Reason)</value>
        </set-header>
        <set-header name="ErrorMessage" exists-action="override">
            <value>@(context.LastError.Message)</value>
        </set-header>
        <set-header name="ErrorScope" exists-action="override">
            <value>@(context.LastError.Scope)</value>
        </set-header>
        <set-header name="ErrorSection" exists-action="override">
            <value>@(context.LastError.Section)</value>
        </set-header>
        <set-header name="ErrorPath" exists-action="override">
            <value>@(context.LastError.Path)</value>
        </set-header>
        <set-header name="ErrorPolicyId" exists-action="override">
            <value>@(context.LastError.PolicyId)</value>
        </set-header>
        <set-header name="ErrorStatusCode" exists-action="override">
            <value>@(context.Response.StatusCode.ToString())</value>
        </set-header>
        <base />
    </on-error>
</policies>
```

en het verzenden van een niet-geautoriseerde aanvraag leidt tot het volgende antwoord:

![Niet-geautoriseerd fout antwoord](media/api-management-error-handling-policies/error-response.png)

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

-   [Beleid in API Management](api-management-howto-policies.md)
-   [Api's transformeren](transform-api.md)
-   [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
-   [Voorbeelden van beleid](./policy-reference.md)