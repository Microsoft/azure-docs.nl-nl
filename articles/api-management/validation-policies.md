---
title: Validatie beleid voor Azure API Management | Microsoft Docs
description: Meer informatie over beleids regels die u kunt gebruiken in azure API Management om aanvragen en antwoorden te valideren.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801890"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>API Management beleid voor het valideren van aanvragen en antwoorden

Dit artikel bevat een overzicht van de volgende API Management-beleids regels. Zie [beleid in API Management](./api-management-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

Validatie beleid gebruiken om API-aanvragen en-antwoorden te valideren voor een OpenAPI-schema en te beschermen tegen beveiligings problemen, zoals injectie van headers of nettolading. Hoewel geen vervanging voor een Web Application firewall is, bieden validatie beleid flexibiliteit om te reageren op een extra klasse bedreigingen die niet worden gedekt door beveiligings producten die afhankelijk zijn van statische, vooraf gedefinieerde regels.

## <a name="validation-policies"></a>Validatie beleid

- [Inhoud valideren](#validate-content) : valideert de grootte of het JSON-schema van een aanvraag of antwoord tekst ten opzichte van het API-schema. 
- [Para meters valideren](#validate-parameters) : valideert de aanvraag header, query of Path-para meters op basis van het API-schema.
- [Headers valideren](#validate-headers) : valideert de antwoord headers op basis van het API-schema.
- [Status code valideren](#validate-status-code) : valideert de HTTP-status codes in antwoorden op het API-schema.

> [!NOTE]
> De maximale grootte van het API-schema dat kan worden gebruikt door een validatie beleid is 4 MB. Als het schema deze limiet overschrijdt, worden er tijdens de uitvoering fouten in het validatie beleid geretourneerd. Neem contact op met de [ondersteuning](https://azure.microsoft.com/support/options/)als u deze wilt verg Roten. 

## <a name="actions"></a>Acties

Elk validatie beleid bevat een kenmerk dat een actie specificeert, die API Management neemt bij het valideren van een entiteit in een API-aanvraag of-antwoord op het API-schema. Er kan een actie worden opgegeven voor elementen die worden weer gegeven in het API-schema en, afhankelijk van het beleid, voor elementen die niet worden weer gegeven in het API-schema. Een actie die is opgegeven in het onderliggende element van een beleid overschrijft een actie die is opgegeven voor de bovenliggende elementen.

Beschik bare acties:

| Bewerking         | Beschrijving          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
|  negeren | De validatie overs Laan. |
| scha | De verwerking van de aanvraag of het antwoord blok keren, de uitgebreide validatie fout vastleggen en een fout retour neren. De verwerking wordt onderbroken wanneer de eerste set fouten wordt gedetecteerd. |
| detect | Validatie fouten vastleggen zonder de verwerking van aanvragen of antwoorden te onderbreken. |

## <a name="logs"></a>Logboeken

Details over de validatie fouten tijdens het uitvoeren van het beleid worden vastgelegd in de variabele in het `context.Variables` `errors-variable-name` kenmerk in het hoofd element van het beleid. Wanneer `prevent` een validatie fout in een actie wordt geconfigureerd, wordt verdere aanvraag-of reactie verwerking geblokkeerd en wordt ook de `context.LastError` eigenschap door gegeven. 

Als u fouten wilt onderzoeken, gebruikt u een [tracerings](api-management-advanced-policies.md#Trace) beleid om de fouten van context variabelen te registreren bij [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Gevolgen voor de prestaties

Het toevoegen van validatie beleid kan van invloed zijn op de API-door voer. De volgende algemene principes zijn van toepassing:
* Hoe groter de grootte van het API-schema, hoe lager de door Voer is. 
* Hoe groter de payload is in een aanvraag of antwoord, hoe lager de door voer. 
* De grootte van het API-schema heeft een grotere invloed op de prestaties dan de grootte van de payload. 
* Validatie op basis van een API-schema dat enkele mega bytes groot is, kan een time-out voor aanvragen of antwoorden veroorzaken onder bepaalde voor waarden. Het effect is meer uitgesp roken in de  **verbruiks** -en **ontwikkelaars** lagen van de service. 

We raden u aan load tests uit te voeren met de verwachte productie workloads om de gevolgen van validatie beleid voor de API-door voer te beoordelen.

## <a name="validate-content"></a>Inhoud valideren

Het `validate-content` beleid valideert de grootte of het JSON-schema van een aanvraag of antwoord tekst ten opzichte van het API-schema. Andere indelingen dan JSON worden niet ondersteund.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Voorbeeld

In het volgende voor beeld wordt de JSON-nettolading in aanvragen en antwoorden gevalideerd in detectie modus. Berichten met nettoladingen groter dan 100 KB worden geblokkeerd. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementen

| Naam         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-content | Hoofd element.                                                                                                                               | Ja      |
| inhoud | Voeg een of meer van deze elementen toe om het inhouds type in de aanvraag of het antwoord te valideren en de opgegeven actie uit te voeren.  | Nee |

### <a name="attributes"></a>Kenmerken

| Naam                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| niet opgegeven-type actie | [Actie](#actions) die moet worden uitgevoerd voor aanvragen of antwoorden met een inhouds type dat niet is opgegeven in het API-schema. |  Ja     | N.v.t.   |
| Max-grootte | Maximum lengte van de hoofd tekst van de aanvraag of het antwoord in bytes, vergeleken met de `Content-Length` koptekst. Als de hoofd tekst van de aanvraag of het antwoord is gecomprimeerd, is deze waarde de gedecomprimeerde lengte. Maxi maal toegestane waarde: 102.400 bytes (100 KB).  | Ja       | N.v.t.   |
| grootte-overschreden-actie | [Actie](#actions) die moet worden uitgevoerd voor aanvragen of antwoorden waarvan de hoofd tekst de grootte overschrijdt die is opgegeven in `max-size` . |  Ja     | N.v.t.   |
| fouten-variabele-name | De naam van de variabele in `context.Variables` om validatie fouten naar te registreren.  |   Ja    | N.v.t.   |
| type | Inhouds type voor het uitvoeren van een validatie van de hoofd tekst voor, vergeleken met de `Content-Type` koptekst. Deze waarde is hoofdletter gevoelig. Als deze leeg is, wordt dit toegepast op elk inhouds type dat is opgegeven in het API-schema. |   Nee    |  N.v.t.  |
| valideren-als | Validatie-engine die moet worden gebruikt voor validatie van de hoofd tekst van een aanvraag of antwoord met een overeenkomend inhouds type. Op dit moment wordt de enige ondersteunde waarde ' JSON ' genoemd.   |  Ja     |  N.v.t.  |
| actie | De [actie](#actions) die moet worden uitgevoerd voor aanvragen of antwoorden waarvan de hoofd tekst niet overeenkomt met het opgegeven inhouds type.  |  Ja      | N.v.t.   |

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** inkomend, uitgaand, op fouten

-   **Beleids bereik:** alle bereiken

## <a name="validate-parameters"></a>Para meters valideren

Het `validate-parameters` beleid valideert de para meters header, query of Path in aanvragen voor het API-schema.

> [!IMPORTANT]
> Als u eerder een API hebt geïmporteerd met behulp van een API-versie van beheer vóór `2021-01-01-preview` , `validate-parameters` werkt het beleid mogelijk niet. Mogelijk moet u [de API opnieuw importeren](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) met behulp van de beheer-API-versie `2021-01-01-preview` of hoger.


### <a name="policy-statement"></a>Beleids verklaring

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Voorbeeld

In dit voor beeld worden alle para meters voor query's en paden gevalideerd in de modus voor preventie en headers in de detectie modus. Validatie wordt overschreven voor verschillende header-para meters:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elementen

| Naam         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-para meters | Hoofd element. Geeft standaard validatie acties voor alle para meters in aanvragen aan.                                                                                                                              | Ja      |
| koppen | Voeg dit element toe om standaard validatie acties voor header-para meters in aanvragen te negeren.   | Nee |
| query | Voeg dit element toe om standaard validatie acties voor query parameters in aanvragen te negeren.  | Nee |
| leertraject | Voeg dit element toe om standaard validatie acties voor URL-path-para meters in aanvragen te negeren.  | Nee |
| parameter | Voeg een of meer elementen voor benoemde para meters toe om de configuratie van de validatie acties op een hoger niveau te overschrijven. | Nee |

### <a name="attributes"></a>Kenmerken

| Naam                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| opgegeven-para meter-Action | [Actie](#actions) die moet worden uitgevoerd voor aanvraag parameters die zijn opgegeven in het API-schema. <br/><br/> Als `headers` de waarde in een, `query` of element wordt gegeven, wordt `path` de waarde van `specified-parameter-action` in het element overschreven `validate-parameters` .  |  Ja     | N.v.t.   |
| niet opgegeven-para meter-Action | [Actie](#actions) die moet worden uitgevoerd voor aanvraag parameters die niet zijn opgegeven in het API-schema. <br/><br/>Als de waarde in een- `headers` of `query` -element wordt gegeven, wordt de waarde van `unspecified-parameter-action` in het element overschreven `validate-parameters` . |  Ja     | N.v.t.   |
| fouten-variabele-name | De naam van de variabele in `context.Variables` om validatie fouten naar te registreren.  |   Ja    | N.v.t.   |
| naam | De naam van de para meter voor het overschrijven van de validatie actie voor. Deze waarde is hoofdletter gevoelig.  | Ja | N.v.t. |
| actie | [Actie](#actions) die moet worden uitgevoerd voor de para meter met de overeenkomende naam. Als de para meter is opgegeven in het API-schema, overschrijft deze waarde de configuratie op een hoger niveau `specified-parameter-action` . Als de para meter niet is opgegeven in het API-schema, overschrijft deze waarde de configuratie op een hoger niveau `unspecified-parameter-action` .| Ja | N.v.t. | 

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** binnenkomend

-   **Beleids bereik:** alle bereiken

## <a name="validate-headers"></a>Kopteksten valideren

Het `validate-headers` beleid valideert de antwoord headers op basis van het API-schema.

> [!IMPORTANT]
> Als u eerder een API hebt geïmporteerd met behulp van een API-versie van beheer vóór `2021-01-01-preview` , `validate-headers` werkt het beleid mogelijk niet. Mogelijk moet u de API opnieuw importeren met behulp van de beheer-API-versie `2021-01-01-preview` of hoger.

### <a name="policy-statement"></a>Beleids verklaring

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Voorbeeld

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementen

| Naam         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-headers | Hoofd element. Hiermee geeft u de standaard validatie acties voor alle headers in antwoorden op.                                                                                                                              | Ja      |
| koptekst | Voeg een of meer elementen voor benoemde kopteksten toe om de standaard validatie acties voor kopteksten in antwoorden te overschrijven. | Nee |

### <a name="attributes"></a>Kenmerken

| Naam                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| opgegeven-header-Action | [Actie](#actions) die moet worden uitgevoerd voor antwoord headers die zijn opgegeven in het API-schema.  |  Ja     | N.v.t.   |
| niet opgegeven-header-Action | [Actie](#actions) die moet worden uitgevoerd voor antwoord headers die niet zijn opgegeven in het API-schema.  |  Ja     | N.v.t.   |
| fouten-variabele-name | De naam van de variabele in `context.Variables` om validatie fouten naar te registreren.  |   Ja    | N.v.t.   |
| naam | De naam van de koptekst waarvoor de validatie actie moet worden overschreven. Deze waarde is hoofdletter gevoelig. | Ja | N.v.t. |
| actie | [Actie](#actions) die moet worden uitgevoerd voor de header met de overeenkomende naam. Als de header is opgegeven in het API-schema, overschrijft deze waarde de waarde van `specified-header-action` in het- `validate-headers` element. Anders wordt de waarde van `unspecified-header-action` in het element validate-headers overschreven. | Ja | N.v.t. | 

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** uitgaand, op fouten

-   **Beleids bereik:** alle bereiken

## <a name="validate-status-code"></a>Status code valideren

Het `validate-status-code` beleid valideert de HTTP-status codes in reacties op het API-schema. Dit beleid kan worden gebruikt om lekkage van backend-fouten te voor komen, die stack-traceringen kunnen bevatten. 

### <a name="policy-statement"></a>Beleids verklaring

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Voorbeeld

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementen

| Naam         | Beschrijving                                                                                                                                   | Vereist |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| validate-status-code | Hoofd element.                                                                                                | Ja      |
| status-code | Voeg een of meer elementen voor HTTP-status codes toe om de standaard validatie actie voor status codes in antwoorden te onderdrukken. | Nee |

### <a name="attributes"></a>Kenmerken

| Naam                       | Beschrijving                                                                                                                                                            | Vereist | Standaard |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| niet opgegeven-status-code-actie | [Actie](#actions) die moet worden uitgevoerd voor HTTP-status codes in antwoorden die niet zijn opgegeven in het API-schema.  |  Ja     | N.v.t.   |
| fouten-variabele-name | De naam van de variabele in `context.Variables` om validatie fouten naar te registreren.  |   Ja    | N.v.t.   |
| code | HTTP-status code voor het overschrijven van de validatie actie voor. | Ja | N.v.t. |
| actie | De [actie](#actions) die moet worden uitgevoerd voor de overeenkomende status code, die niet is opgegeven in het API-schema. Als de status code is opgegeven in het API-schema, wordt deze onderdrukking niet van kracht. | Ja | N.v.t. | 

### <a name="usage"></a>Gebruik

Dit beleid kan worden gebruikt in de volgende beleids [secties](./api-management-howto-policies.md#sections) en [bereiken](./api-management-howto-policies.md#scopes).

-   **Beleids secties:** uitgaand, op fouten

-   **Beleids bereik:** alle bereiken


## <a name="validation-errors"></a>Validatie fouten
De volgende tabel bevat alle mogelijke fouten van de validatie beleidsregels. 

* **Details** : kan worden gebruikt om fouten te onderzoeken. Niet bedoeld om openbaar te worden gedeeld.
* **Openbaar antwoord** -fout geretourneerd naar de client. Lekt geen implementatie details.

| **Naam**                             | **Type**                                                        | **Validatie regel** | **Details**                                                                                                                                       | **Openbaar antwoord**                                                                                                                       | **Actie**           |
|----|----|---|---|---|----|
| **validate-content** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |RequestBody                                                     | SizeLimit           | De hoofd tekst van de aanvraag is {size} bytes lang en overschrijdt de geconfigureerde limiet van {maxSize} bytes.                                                       | De hoofd tekst van de aanvraag is {size} bytes lang en de limiet van {maxSize} bytes wordt overschreden.                                                          | detecteren/voor komen |
||ResponseBody                                                    | SizeLimit           | De hoofd tekst van de reactie is {size} bytes Long en overschrijdt de geconfigureerde limiet van {maxSize} bytes.                                                      | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| {messageContentType}                 | RequestBody                                                     | Niet opgegeven         | Niet-opgegeven inhouds type {messageContentType} is niet toegestaan.                                                                                     | Niet-opgegeven inhouds type {messageContentType} is niet toegestaan.                                                                             | detecteren/voor komen |
| {messageContentType}                 | ResponseBody                                                    | Niet opgegeven         | Niet-opgegeven inhouds type {messageContentType} is niet toegestaan.                                                                                     | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| | ApiSchema                                                       |                     | Het schema van de API bestaat niet of kan niet worden omgezet.                                                                                          | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
|                                      | ApiSchema                                                       |                     | In het API-schema worden geen definities opgegeven.                                                                                                        | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| {messageContentType}                 | RequestBody/ResponseBody                                      | MissingDefinition   | Het schema van de API bevat niet de definitie {eigenschapdefinitie}, die is gekoppeld aan het inhouds type {messageContentType}.                        | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| {messageContentType}                 | RequestBody                                                     | IncorrectMessage    | De hoofd tekst van de aanvraag voldoet niet aan de definitie {eigenschapdefinitie}, die is gekoppeld aan het inhouds type {messageContentType}.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}                  | De hoofd tekst van de aanvraag voldoet niet aan de definitie {eigenschapdefinitie}, die is gekoppeld aan het inhouds type {messageContentType}.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}            | detecteren/voor komen |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | De hoofd tekst van het antwoord voldoet niet aan de definitie {eigenschapdefinitie}, die is gekoppeld aan het inhouds type {messageContentType}.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}                                       | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
|                                      | RequestBody                                                     | ValidationException | De hoofd tekst van de aanvraag kan niet worden gevalideerd voor het inhouds type {messageContentType}.<br/><br/>{Details van uitzonde ring}                                                                | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
|                                      | ResponseBody                                                    | ValidationException | De hoofd tekst van het antwoord kan niet worden gevalideerd voor het inhouds type {messageContentType}.<br/><br/>{Details van uitzonde ring}                                                                | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| **validate-para meter/validate-headers** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {param}/{kopnaam}           | QueryParameter / PathParameter / RequestHeader                  | Niet opgegeven         | Niet opgegeven {Path para meter/query para meter/header} {param} is niet toegestaan.                                                               | Niet opgegeven {Path para meter/query para meter/header} {param} is niet toegestaan.                                                       | detecteren/voor komen |
| Header                         | ResponseHeader                                                  | Niet opgegeven         | Niet-opgegeven header {headernaam} is niet toegestaan.                                                                                                   | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
|                                      |ApiSchema                                                       |                     | Het API-schema bestaat niet of kan niet worden opgelost.                                                                                            | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
|                                       | ApiSchema                                                       |                     | In het API-schema worden geen definities opgegeven.                                                                                                          | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| ParamName                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Het schema van de API bevat niet de definitie {eigenschapdefinitie}, die is gekoppeld aan de {query para meter/Path para meter/header} {param}.  | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | De aanvraag kan niet meerdere waarden bevatten voor de {query para meter/Path para meter/header} {param}.                                           | De aanvraag kan niet meerdere waarden bevatten voor de {query para meter/Path para meter/header} {param}.                                   | detecteren/voor komen |
| Header                         | ResponseHeader                                                  | IncorrectMessage    | Het antwoord kan niet meerdere waarden bevatten voor de header {kopnaam}.                                                                              | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | De waarde van de {query para meter/Path para meter/header} {param} komt niet overeen met de definitie.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}                                          | De waarde van de {query para meter/Path para meter/header} {param} komt niet overeen met de definitie.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}                              | detecteren/voor komen |
| Header                         | ResponseHeader                                                  | IncorrectMessage    | De waarde van de header {kopnaam} komt niet overeen met de definitie.<br/><br/>{valError. Message} regel: {valError. LineNumber}, positie: {valError. LinePosition}                                                                              | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | De waarde van de {query para meter/het pad para meter/header} {param} kan niet worden geparseerd volgens de definitie. <br/><br/>kade. Faxbericht                                | De waarde van de {query para meter/Path para meter/header} {param} kan niet worden geparseerd volgens de definitie. <br/><br/>kade. Faxbericht                      | detecteren/voor komen |
| Header                         | ResponseHeader                                                  | IncorrectMessage    | De waarde van de header {headernaam} kan niet worden geparseerd volgens de definitie.                                                                  | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Query parameter/pad para meter/header} {param} kan niet worden gevalideerd.<br/><br/>{Details van uitzonde ring}                                                                      | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| Header                         | ResponseHeader                                                  | ValidationError     | Header {kopnaam} kan niet worden gevalideerd.<br/><br/>{Details van uitzonde ring}                                                                                                          | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |
| **validate-status-code**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-code}                        | Status code                                                      | Niet opgegeven         | De antwoord status code {status-code} is niet toegestaan.                                                                                                | De aanvraag kan niet worden verwerkt wegens een interne fout. Neem contact op met de eigenaar van de API.                                                       | detecteren/voor komen |


De volgende tabel bevat alle mogelijke reden waarden van een validatie fout, samen met mogelijke bericht waarden:

|  **Reden**         |    **Bericht** |
|---|---|  
| Ongeldige aanvraag       |     {Details} voor context variabele, {open bare reactie} voor client|
| Antwoord niet toegestaan  | {Details} voor context variabele, {open bare reactie} voor client |






## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het werken met beleids regels:

-   [Beleid in API Management](api-management-howto-policies.md)
-   [Api's transformeren](transform-api.md)
-   [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
-   [Voorbeelden van beleid](./policy-reference.md)
-   [Foutafhandeling](./api-management-error-handling-policies.md)
