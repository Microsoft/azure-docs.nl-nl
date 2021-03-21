---
title: Azure API Management-beleid | Microsoft Docs
description: Meer informatie over het beschikbare beleid voor gebruik in Azure API Management. Met beleid kan de uitgever het API-gedrag via de configuratie wijzigen.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/17/2021
ms.author: apimpm
ms.openlocfilehash: e809efa9da32da5fe9ca296608c602e770f78265
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103562345"
---
# <a name="api-management-policies"></a>API Management-beleidsregels
Deze sectie bevat een referentie voor het volgende API Management-beleid. Zie [beleid in API Management](api-management-howto-policies.md)voor meer informatie over het toevoegen en configureren van beleid.

 Beleids regels zijn een krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleids regels zijn een verzameling instructies die opeenvolgend worden uitgevoerd op de aanvraag of het antwoord van een API. Populaire instructies omvatten indelings conversie van XML naar JSON en aanroep frequentie beperken om de hoeveelheid binnenkomende oproepen van een ontwikkelaar te beperken. Er zijn nog veel meer beleids regels beschikbaar.

 Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren](api-management-advanced-policies.md#choose) en [Variabele instellen](api-management-advanced-policies.md#set-variable), zijn gebaseerd op beleidsexpressies. Zie [Geavanceerde beleidsregels](api-management-advanced-policies.md#AdvancedPolicies) en [Beleidsexpressies](api-management-policy-expressions.md) voor meer informatie.

##  <a name="policies"></a><a name="ProxyPolicies"></a> Restrictie

-   [Beleid voor toegangsbeperking](api-management-access-restriction-policies.md#AccessRestrictionPolicies)
    -   [Controleer de http-header](api-management-access-restriction-policies.md#CheckHTTPHeader) : Hiermee wordt het bestaan en/of de waarde van een http-header afgedwongen.
    -   De [aanroepen per abonnement beperken](api-management-access-restriction-policies.md#LimitCallRate) : hiermee voor komt u dat het API-gebruik piekt door de oproep frequentie te beperken, op basis van een abonnement.
    -   [Beperk de aanroepen per sleutel](api-management-access-restriction-policies.md#LimitCallRateByKey) : Hiermee wordt voor komen dat het API-gebruik pieken oploopt door de oproep frequentie per sleutel te beperken.
    -   [Aanroeper Ip's beperken](api-management-access-restriction-policies.md#RestrictCallerIPs) : filters (toestaan/weigeren) aanroepen van specifieke IP-adressen en/of adresbereiken.
    -   [Gebruiks quotum per abonnement instellen](api-management-access-restriction-policies.md#SetUsageQuota) : Hiermee kunt u een Verleng bare of levensduur oproep volume en/of bandbreedte quota afdwingen op basis van elk abonnement.
    -   [Gebruiks quotum instellen op sleutel](api-management-access-restriction-policies.md#SetUsageQuotaByKey) : Hiermee kunt u een Verleng bare of levensduur oproep volume en/of bandbreedte quotum per sleutel afdwingen.
    -   [Valideer JWT](api-management-access-restriction-policies.md#ValidateJWT) : afdwingt aanwezigheid en geldigheid van een JWT die is geëxtraheerd uit een opgegeven HTTP-header of een opgegeven query parameter.
-   [Geavanceerde beleidsregels](api-management-advanced-policies.md#AdvancedPolicies)
    -   [Controle stroom](api-management-advanced-policies.md#choose) : past beleids instructies toe op basis van de evaluatie van Boole-expressies.
    -   [Aanvraag door sturen](api-management-advanced-policies.md#ForwardRequest) : stuurt de aanvraag door naar de back-end-service.
    -   [Gelijktijdigheid beperken](api-management-advanced-policies.md#LimitConcurrency) : hiermee voor komt u dat beleid wordt uitgevoerd door meer dan het opgegeven aantal aanvragen per keer.
    -   [Meld u aan bij Event hub](api-management-advanced-policies.md#log-to-eventhub) : stuurt berichten in de opgegeven indeling naar een bericht doel dat is gedefinieerd door een logboek registratie-entiteit.
    -   Verwaak [antwoord](api-management-advanced-policies.md#mock-response) : Hiermee wordt de uitvoering van de pijp lijn afgebroken en wordt een gesimuleerde respons rechtstreeks naar de oproepende functie geretourneerd.
    -   [Nieuwe](api-management-advanced-policies.md#Retry) pogingen: de uitvoering van de Inge sloten beleids instructies opnieuw proberen, indien en tot aan de voor waarde wordt voldaan. De uitvoering wordt herhaald op de opgegeven tijds intervallen en tot het opgegeven aantal nieuwe pogingen.
    -   [Retour antwoord](api-management-advanced-policies.md#ReturnResponse) : Hiermee wordt de uitvoering van de pijp lijn afgebroken en wordt het opgegeven antwoord rechtstreeks naar de aanroeper geretourneerd.
    -   Eenrichtings [aanvraag verzenden](api-management-advanced-policies.md#SendOneWayRequest) : Hiermee verzendt u een aanvraag naar de opgegeven URL zonder te wachten op een reactie.
    -   [Aanvraag verzenden](api-management-advanced-policies.md#SendRequest) : Hiermee verzendt u een aanvraag naar de opgegeven URL.
    -   [Http-proxy instellen](api-management-advanced-policies.md#SetHttpProxy) : Hiermee kunt u doorgestuurde aanvragen via een http-proxy routeren.
    -   [Variabele instellen](api-management-advanced-policies.md#set-variable) : behoud een waarde in een benoemde context variabele voor later toegang.
    -   [Set-aanvraag methode](api-management-advanced-policies.md#SetRequestMethod) : Hiermee kunt u de HTTP-methode voor een aanvraag wijzigen.
    -   [Status code instellen](api-management-advanced-policies.md#SetStatus) : Hiermee wijzigt u de HTTP-status code in de opgegeven waarde.
    -   [Trace](api-management-advanced-policies.md#Trace) : voegt aangepaste traceringen toe aan de [API-Inspector](./api-management-howto-api-inspector.md) -uitvoer, Application Insights-Telerichtingen en resource Logboeken.
    -   [Wacht](api-management-advanced-policies.md#Wait) op een Inge sloten [verzend aanvraag](api-management-advanced-policies.md#SendRequest), haal de [waarde uit het cache geheugen](api-management-caching-policies.md#GetFromCacheByKey)of Controleer of [controle stroom](api-management-advanced-policies.md#choose) beleid is voltooid voordat u doorgaat.
-   [Verificatiebeleid](api-management-authentication-policies.md#AuthenticationPolicies)
    -   [Authenticatie met Basic](api-management-authentication-policies.md#Basic) : verifiëren met een back-end-service met basis verificatie.
    -   [Verificatie met client certificaat](api-management-authentication-policies.md#ClientCertificate) : Verifieer met een back-end-service met behulp van client certificaten.
    -   [Verificatie met beheerde identiteit](api-management-authentication-policies.md#ManagedIdentity) -verificatie met een back-end-service met een [beheerde identiteit](../active-directory/managed-identities-azure-resources/overview.md).
-   [Cachebeleidsregels](api-management-caching-policies.md#CachingPolicies)
    -   [Ophalen uit cache](api-management-caching-policies.md#GetFromCache) -cache uitvoeren en een geldig antwoord in de cache retour neren als deze beschikbaar zijn.
    -   [Opslaan in cache](api-management-caching-policies.md#StoreToCache) -antwoord in cache geheugen volgens de opgegeven cache beheer configuratie.
    -   [Waarde ophalen uit cache](api-management-caching-policies.md#GetFromCacheByKey) -een item in de cache ophalen met behulp van sleutel.
    -   [Waarde voor opslaan in cache](api-management-caching-policies.md#StoreToCacheByKey) : een item in de cache opslaan per sleutel.
    -   [Waarde uit cache verwijderen](api-management-caching-policies.md#RemoveCacheByKey) : een item in de cache verwijderen per sleutel.
-   [Beleid voor meerdere domeinen](api-management-cross-domain-policies.md#CrossDomainPolicies)
    -   [Interdomein-aanroepen toestaan](api-management-cross-domain-policies.md#AllowCrossDomainCalls) : maakt de API toegankelijk vanuit Adobe Flash en micro soft Silverlight-clients op basis van een browser.
    -   [Cors](api-management-cross-domain-policies.md#CORS) -voegt cross-Origin resource SHARING (CORS)-ondersteuning toe aan een bewerking of een API voor het toestaan van interdomein-aanroepen vanuit clients op basis van de browser.
    -   [Jsonp](api-management-cross-domain-policies.md#JSONP) : voegt JSON met opvulling (Jsonp)-ondersteuning toe aan een bewerking of een API voor het toestaan van cross-domein aanroepen vanuit Java script-clients op basis van een browser.
-   [Transformatiebeleid](api-management-transformation-policies.md#TransformationPolicies)
    -   [JSON converteren naar XML](api-management-transformation-policies.md#ConvertJSONtoXML) : Hiermee wordt de aanvraag of antwoord tekst van JSON naar XML geconverteerd.
    -   [CONVERTEER XML naar JSON](api-management-transformation-policies.md#ConvertXMLtoJSON) -de aanvraag of antwoord tekst van XML naar JSON wordt geconverteerd.
    -   [Teken reeks in hoofd tekst zoeken en vervangen](api-management-transformation-policies.md#Findandreplacestringinbody) : zoekt naar een subtekenreeks voor aanvragen of antwoorden en vervangt deze door een andere subtekenreeks.
    -   [Masker url's in content](api-management-transformation-policies.md#MaskURLSContent) -rewrites (maskers) in de hoofd tekst van het antwoord, zodat ze verwijzen naar de equivalente koppeling via de gateway.
    -   [Backend-service instellen](api-management-transformation-policies.md#SetBackendService) : Hiermee wordt de back-end-service voor een binnenkomende aanvraag gewijzigd.
    -   [Hoofd tekst instellen](api-management-transformation-policies.md#SetBody) : Hiermee stelt u de bericht tekst voor binnenkomende en uitgaande aanvragen.
    -   [Set http header](api-management-transformation-policies.md#SetHTTPheader) : wijst een waarde toe aan een bestaande reactie en/of aanvraag header of voegt een nieuwe reactie en/of een aanvraag header toe.
    -   [Query teken reeks parameter instellen](api-management-transformation-policies.md#SetQueryStringParameter) : toevoegen, vervangt waarde van of verwijdert query teken reeks parameter.
    -   [URL herschrijven](api-management-transformation-policies.md#RewriteURL) : converteert een aanvraag-URL uit de open bare vorm naar het formulier dat wordt verwacht door de webservice.
    -   [XML transformeren met behulp van een XSLT](api-management-transformation-policies.md#XSLTransform) : past een XSL-trans formatie toe op XML in de hoofd tekst van de aanvraag of het antwoord.
- [Integratie beleid voor Dapr](api-management-dapr-policies.md)
    - Een [aanvraag verzenden naar een service](api-management-dapr-policies.md#invoke) : Dapr-runtime wordt gebruikt om te zoeken en te communiceren met een Dapr-micro service.
    -  [Bericht verzenden naar pub/sub-onderwerp](api-management-dapr-policies.md#pubsub) : maakt gebruik van Dapr runtime om een bericht te publiceren naar het onderwerp publiceren/abonneren.
    -  [Trigger uitvoer binding](api-management-dapr-policies.md#bind) : maakt gebruik van Dapr runtime om een extern systeem via een uitvoer binding aan te roepen.
- [Validatie beleid](validation-policies.md)
    - [Inhoud valideren](validation-policies.md#validate-content) : valideert de grootte of het JSON-schema van een aanvraag of antwoord tekst ten opzichte van het API-schema.
. 
    - [Para meters valideren](validation-policies.md#validate-parameters) : valideert de aanvraag header, query of Path-para meters op basis van het API-schema.
    - [Headers valideren](validation-policies.md#validate-headers) : valideert de antwoord headers op basis van het API-schema.
    - [Status code valideren](validation-policies.md#validate-status-code) : valideert de HTTP-status codes in antwoorden op het API-schema.

## <a name="next-steps"></a>Volgende stappen
Zie voor meer informatie over het gebruik van beleid:

+ [Beleid in API Management](api-management-howto-policies.md)
+ [Api's transformeren](transform-api.md)
+ [Voorbeelden van beleid](./policy-reference.md)
