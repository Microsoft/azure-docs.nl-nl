---
title: Geavanceerde aanvraag beperking met Azure API Management
description: Meer informatie over het maken en Toep assen van beleid voor flexibele quota en frequentie beperking met Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: fc813a65-7793-4c17-8bb9-e387838193ae
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/03/2018
ms.author: apimpm
ms.openlocfilehash: 3f91ed2238a7a23b967cd3e3556c8ade88af0044
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106449729"
---
# <a name="advanced-request-throttling-with-azure-api-management"></a>Geavanceerde aanvraag beperking met Azure API Management
Het beperken van binnenkomende aanvragen is een belang rijke rol van Azure API Management. Door de frequentie van aanvragen of het totale aantal overgedragen aanvragen/gegevens te beheren, kunnen met API Management API-providers hun Api's tegen misbruik beveiligen en waarde maken voor verschillende API-product lagen.

## <a name="rate-limits-and-quotas"></a>Frequentie limieten en quota
Frequentie limieten en quota worden gebruikt voor verschillende doel einden.

### <a name="rate-limits"></a>Frequentielimieten
Frequentie limieten worden meestal gebruikt om te beschermen tegen korte en intense volume-bursts. Als u bijvoorbeeld weet dat uw back-end-service een knel punt op de data base heeft met een hoog oproep volume, kunt u een `rate-limit-by-key` beleid instellen om een hoog oproep volume niet toe te staan met behulp van deze instelling.

### <a name="quotas"></a>Quota
Quota worden meestal gebruikt voor het beheren van oproep tarieven gedurende een langere periode. Ze kunnen bijvoorbeeld het totaal aantal aanroepen instellen dat een bepaalde abonnee binnen een bepaalde maand kan maken. Voor inkomsten uw API kunnen quota ook verschillend worden ingesteld voor op lagen gebaseerde abonnementen. Zo kan een abonnement van de Basic-laag bijvoorbeeld een maand niet meer dan 10.000 aanroepen, maar een Premium-laag kan elke maand tot 100.000.000 oproepen lopen.

Binnen Azure API Management worden frequentie limieten doorgaans sneller door gegeven over de knoop punten om te beschermen tegen pieken. Gebruiks quotum gegevens worden daarentegen gebruikt voor een langere periode en daarom is de implementatie anders.

> [!CAUTION]
> Vanwege de gedistribueerde aard van beperkings architectuur is de frequentie beperking nooit volledig nauw keurig. Het verschil tussen het geconfigureerde en reële aantal toegestane aanvragen varieert op basis van het volume en de frequentie van de aanvraag, de back-uplatentie en andere factoren.

## <a name="product-based-throttling"></a>Op product gebaseerde beperking
De mogelijkheden voor het beperken van het bereik van een bepaald abonnement zijn handig voor de API-provider om beperkingen toe te passen op de ontwikkel aars die zich hebben geregistreerd voor het gebruik van hun API. Het biedt echter geen ondersteuning voor het beperken van afzonderlijke eind gebruikers van de API. Het is mogelijk dat één gebruiker van de ontwikkelaar van de toepassing het volledige quotum verbruikt en vervolgens voor komt dat andere klanten van de ontwikkelaar de toepassing kunnen gebruiken. Daarnaast kunnen meerdere klanten die een groot aantal aanvragen kunnen genereren de toegang tot incidentele gebruikers beperken.

## <a name="custom-key-based-throttling"></a>Aangepaste beperking op basis van sleutels

> [!NOTE]
> De `rate-limit-by-key` `quota-by-key` beleids regels en zijn niet beschikbaar in de laag verbruik van Azure API management. 

Het beleid voor [frequentie limieten per sleutel](./api-management-access-restriction-policies.md#LimitCallRateByKey) en [quotum per sleutel](./api-management-access-restriction-policies.md#SetUsageQuotaByKey) biedt een flexibele oplossing voor verkeers beheer. Met deze beleids regels kunt u expressies definiëren om de sleutels te identificeren die worden gebruikt voor het bijhouden van het gebruik van verkeer. De manier waarop dit werkt, is eenvoudig te demonstreren met een voor beeld. 

## <a name="ip-address-throttling"></a>Beperking van IP-adressen
De volgende beleids regels beperken één IP-adres van een client tot slechts 10 aanroepen per minuut, met een totaal van 1.000.000-aanroepen en 10.000 kilo byte band breedte van de maand. 

```xml
<rate-limit-by-key  calls="10"
          renewal-period="60"
          counter-key="@(context.Request.IpAddress)" />

<quota-by-key calls="1000000"
          bandwidth="10000"
          renewal-period="2629800"
          counter-key="@(context.Request.IpAddress)" />
```

Als alle clients op internet een uniek IP-adres gebruiken, kan dit een efficiënte manier zijn om het gebruik door de gebruiker te beperken. Het is echter waarschijnlijk dat meerdere gebruikers een enkel openbaar IP-adres delen als gevolg van de toegang tot internet via een NAT-apparaat. Als dit niet mogelijk is, is de beste optie voor Api's die niet-geverifieerde toegang toestaan `IpAddress` .

## <a name="user-identity-throttling"></a>Beperking van gebruikers identiteit
Als een eind gebruiker is geverifieerd, kan een beperkings sleutel worden gegenereerd op basis van gegevens die een unieke identificatie van die gebruiker zijn.

```xml
<rate-limit-by-key calls="10"
    renewal-period="60"
    counter-key="@(context.Request.Headers.GetValueOrDefault("Authorization","").AsJwt()?.Subject)" />
```

In dit voor beeld ziet u hoe u de autorisatie-header uitpakt, converteert naar een `JWT` object en het onderwerp van het token gebruikt om de gebruiker te identificeren en die als de frequentie beperkings sleutel te gebruiken. Als de identiteit van de gebruiker wordt opgeslagen in de `JWT` als een van de andere claims, zou die waarde kunnen worden gebruikt in plaats daarvan.

## <a name="combined-policies"></a>Gecombineerd beleid
Hoewel het op gebruikers gebaseerde beperkings beleid meer controle biedt dan het op abonnementen gebaseerde beperkings beleid, is er nog steeds waarden die beide mogelijkheden combi neren. Beperking per product abonnements sleutel ([oproep snelheid per abonnement beperken](./api-management-access-restriction-policies.md#LimitCallRate) en [gebruiks quotum per abonnement instellen](./api-management-access-restriction-policies.md#SetUsageQuota)) is een uitstekende manier om inkomsten van een API in te stellen op basis van gebruiks niveaus. De nauw keurige controle over het beperken van de beperking door de gebruiker is complementair en voor komt dat het gedrag van een gebruiker de ervaring van een ander kan verlagen. 

## <a name="client-driven-throttling"></a>Door client gestuurde beperking
Wanneer de beperkings sleutel is gedefinieerd met behulp van een [beleids expressie](./api-management-policy-expressions.md), is het de API-provider die het bereik van de beperking kan bepalen. Het is echter mogelijk dat een ontwikkelaar wil bepalen hoe ze hun eigen klanten beoordelen. Dit kan worden ingeschakeld door de API-provider door een aangepaste header te introduceren, zodat de client toepassing van de ontwikkelaar de sleutel kan communiceren naar de API.

```xml
<rate-limit-by-key calls="100"
          renewal-period="60"
          counter-key="@(request.Headers.GetValueOrDefault("Rate-Key",""))"/>
```

Hierdoor kan de client toepassing van de ontwikkelaar kiezen hoe ze de frequentie limiet sleutel willen maken. De client ontwikkelaars kunnen hun eigen tarieven lagen maken door sets sleutels aan gebruikers toe te wijzen en het sleutel gebruik te roteren.

## <a name="summary"></a>Samenvatting
Azure API Management biedt limieten voor snelheid en quota voor het beveiligen en toevoegen van waarde aan uw API-service. Met het nieuwe beperkings beleid met aangepaste bereik regels kunt u een nauw keurigere controle over het beleid bieden zodat uw klanten zelfs betere toepassingen kunnen bouwen. In de voor beelden in dit artikel wordt het gebruik van deze nieuwe beleids regels gedemonstreerd door de productie frequentie beperkende sleutels met client-IP-adressen, gebruikers-id en door client gegenereerde waarden. Er zijn echter veel andere delen van het bericht die kunnen worden gebruikt, zoals gebruikers agent, URL-pad fragmenten, bericht grootte.

## <a name="next-steps"></a>Volgende stappen
Geef ons uw feedback als een GitHub-probleem voor dit onderwerp. Het is handig om te horen over andere mogelijke sleutel waarden die een logische keuze zijn in uw scenario's.
