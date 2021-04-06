---
title: Beleid in azure API Management | Microsoft Docs
description: Meer informatie over het maken, bewerken en configureren van beleid in API Management. Zie code voorbeelden en Bekijk extra beschik bare resources.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 11/29/2017
ms.author: apimpm
ms.openlocfilehash: 37ac6369790ed526fd923819558863ae84432aed
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94358113"
---
# <a name="policies-in-azure-api-management"></a>Beleidsregels in Azure API Management

In azure API Management (APIM) zijn beleids regels een krachtige mogelijkheid van het systeem waarmee de uitgever het gedrag van de API via configuratie kan wijzigen. Beleids regels zijn een verzameling instructies die opeenvolgend worden uitgevoerd op de aanvraag of het antwoord van een API. Populaire instructies omvatten indelings conversie van XML naar JSON en aanroep frequentie beperken om de hoeveelheid binnenkomende oproepen van een ontwikkelaar te beperken. Er zijn nog veel meer beleids regels beschikbaar.

Beleids regels worden binnen de gateway toegepast die zich tussen de API-consumer en de beheerde API bevindt. De gateway ontvangt alle aanvragen en stuurt deze doorgaans niet-gewijzigd naar de onderliggende API. Een beleid kan echter wijzigingen Toep assen op zowel de inkomende aanvraag als het uitgaande antwoord.

Beleidsexpressies kunnen worden gebruikt als kenmerkwaarden of tekstwaarden in API Management-beleidsregels, tenzij het beleid iets anders aangeeft. Sommige beleidsregels, zoals de beleidsregels [Stroom controleren][Control flow] en [Variabele instellen][Set variable], zijn gebaseerd op beleidsexpressies. Zie [Geavanceerde beleidsregels][Advanced policies] en [Beleidsexpressies][Policy expressions] voor meer informatie.

## <a name="understanding-policy-configuration"></a><a name="sections"> </a>Informatie over beleids configuratie

De beleids definitie is een eenvoudig XML-document dat een reeks van binnenkomende en uitgaande instructies beschrijft. De XML kan rechtstreeks worden bewerkt in het definitie venster. Er wordt een lijst met-instructies aan de rechter kant en de instructies die van toepassing zijn op het huidige bereik, worden ingeschakeld en gemarkeerd.

Als u op een ingeschakelde instructie klikt, wordt de juiste XML toegevoegd op de locatie van de cursor in de definitie weergave. 

> [!NOTE]
> Als het beleid dat u wilt toevoegen niet is ingeschakeld, controleert u of u zich in het juiste bereik voor dat beleid bevindt. Elke beleids instructie is ontworpen voor gebruik in bepaalde bereiken en beleids secties. Als u de beleids secties en-bereiken voor een beleid wilt bekijken, raadpleegt u de sectie **gebruik** van het beleid in de [Naslag informatie over beleid][Policy Reference].
> 
> 

De configuratie is onderverdeeld in `inbound` , `backend` ,, `outbound` en `on-error` . De reeks opgegeven beleids instructies wordt uitgevoerd in de volg orde van een aanvraag en een antwoord.

```xml
<policies>
  <inbound>
    <!-- statements to be applied to the request go here -->
  </inbound>
  <backend>
    <!-- statements to be applied before the request is forwarded to 
         the backend service go here -->
  </backend>
  <outbound>
    <!-- statements to be applied to the response go here -->
  </outbound>
  <on-error>
    <!-- statements to be applied if there is an error condition go here -->
  </on-error>
</policies> 
```

Als er een fout optreedt tijdens de verwerking van een aanvraag, worden alle resterende stappen in `inbound` de `backend` secties, of `outbound` worden overgeslagen en wordt de uitvoering naar de instructies in de `on-error` sectie geleid. Door beleids instructies in de `on-error` sectie in te stellen, kunt u de fout controleren met behulp van de `context.LastError` eigenschap, het fout bericht controleren en aanpassen met behulp van het `set-body` beleid en configureren wat er gebeurt als er een fout optreedt. Er zijn fout codes voor ingebouwde stappen en voor fouten die kunnen optreden tijdens het verwerken van beleids instructies. Zie [fout afhandeling in API management-beleid](./api-management-error-handling-policies.md)voor meer informatie.

## <a name="how-to-configure-policies"></a><a name="scopes"> </a>Beleids regels configureren

Zie [beleid instellen of bewerken](set-edit-policies.md)voor meer informatie over het configureren van beleid.

## <a name="policy-reference"></a>Beleids verwijzing

Zie de [beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en de bijbehorende instellingen.

## <a name="policy-samples"></a>Voorbeelden van beleid

Zie [beleids voorbeelden](./policy-reference.md) voor meer code voorbeelden.

## <a name="examples"></a>Voorbeelden

### <a name="apply-policies-specified-at-different-scopes"></a>Beleids regels Toep assen die zijn opgegeven bij verschillende bereiken

Als u een beleid hebt op het globale niveau en een beleid dat is geconfigureerd voor een API, worden beide beleids regels toegepast wanneer de desbetreffende API wordt gebruikt. API Management maakt deterministische volg orde van gecombineerde beleids instructies mogelijk via het basis element. 

```xml
<policies>
    <inbound>
        <cross-domain />
        <base />
        <find-and-replace from="xyz" to="abc" />
    </inbound>
</policies>
```

In het bovenstaande voor beeld beleid wordt de `cross-domain` instructie uitgevoerd voordat een hoger beleid wordt gevolgd door het `find-and-replace` beleid. 

### <a name="restrict-incoming-requests"></a>Binnenkomende aanvragen beperken

Als u een nieuwe instructie wilt toevoegen om binnenkomende aanvragen te beperken tot opgegeven IP-adressen, plaatst u de cursor net in de inhoud van het `inbound` XML-element en klikt u op de instructie **Caller ip's beperken** .

![Restrictie beleid][policies-restrict]

Hiermee voegt u een XML-fragment toe aan het- `inbound` element dat hulp biedt bij het configureren van de-instructie.

```xml
<ip-filter action="allow | forbid">
    <address>address</address>
    <address-range from="address" to="address"/>
</ip-filter>
```

Als u binnenkomende aanvragen wilt beperken en alleen die van een IP-adres van 1.2.3.4 wilt accepteren, wijzigt u de XML als volgt:

```xml
<ip-filter action="allow">
    <address>1.2.3.4</address>
</ip-filter>
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over het gebruik van beleid:

+ [Api's transformeren](transform-api.md)
+ [Beleids verwijzing](./api-management-policies.md) voor een volledige lijst met beleids instructies en hun instellingen
+ [Voorbeelden van beleid](./policy-reference.md)   

[Policy Reference]: ./api-management-policies.md
[Product]: api-management-howto-add-products.md
[API]: api-management-howto-add-products.md
[Operation]: ./mock-api-responses.md

[Advanced policies]: ./api-management-advanced-policies.md
[Control flow]: ./api-management-advanced-policies.md#choose
[Set variable]: ./api-management-advanced-policies.md#set-variable
[Policy expressions]: ./api-management-policy-expressions.md

[policies-restrict]: ./media/api-management-howto-policies/api-management-policies-restrict.png
