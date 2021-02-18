---
title: Controle van de status test Azure front deur Standard/Premium (preview)
description: Dit artikel helpt u te begrijpen hoe de status van uw back-end door Azure front-deur wordt gecontroleerd.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 02/18/2021
ms.author: duau
ms.openlocfilehash: 30a8208babab2991c9d9e86cc419ac50e1530d7b
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "101099217"
---
# <a name="azure-front-door-standardpremium-preview-health-probe-monitoring"></a>Controle van de status test Azure front deur Standard/Premium (preview)

> [!Note]
> Deze documentatie is voor Azure front deur Standard/Premium (preview). Zoekt u informatie over de voor deur van Azure? [Hier](../front-door-overview.md)weer geven.

Azure front deur verzendt periodiek een HTTP-of HTTPS-aanvraag naar elk van uw back-end. Met deze aanvragen kan Azure front deur de status van elk eind punt in de back-end-groep bepalen. De voor deur gebruikt vervolgens deze antwoorden van de test om de ' beste ' back-upbronnen te bepalen om uw client aanvragen te routeren. 

> [!WARNING]
> Omdat de voor deur veel van de meeste Edge-omgevingen heeft, kan het status controlevolume voor uw back-ends voor elke minuut een hoge mate van 25 aanvragen tot Maxi maal 1200 aanvragen per minuut hebben, afhankelijk van de geconfigureerde Health probe-frequentie. Met de standaard test frequentie van 30 seconden moet het test volume op uw back-end ongeveer 200 aanvragen per minuut bedragen.

## <a name="supported-protocols"></a>Ondersteunde protocollen

De voor deur ondersteunt het verzenden van tests over HTTP-of HTTPS-protocollen. De tests worden via dezelfde TCP-poorten verzonden als de poorten die zijn geconfigureerd voor het routeren van aanvragen van klanten. Deze instelling kan niet worden overschreven.

## <a name="supported-http-methods-for-health-probes"></a>Ondersteunde HTTP-methoden voor status tests

De voor deur ondersteunt de volgende HTTP-methoden voor het verzenden van de status tests:

* **Ophalen:** De GET-methode geeft aan welke gegevens (in de vorm van een entiteit) worden geïdentificeerd door de aanvraag-URI.
* **Kop:** De HEAD-methode is identiek om aan te geven, behalve dat de server geen bericht tekst in het antwoord mag retour neren. Voor nieuwe profielen voor de voor deur wordt standaard de test methode ingesteld als HEAD.

> [!NOTE]
> Voor een lagere belasting en kosten op uw back-end wordt aanbevolen om hoofd aanvragen voor status tests te gebruiken.

## <a name="health-probe-responses"></a>Antwoorden op status testen

| Antwoorden  | Description | 
| ------------- | ------------- |
| Status bepalen  |  Een 200 OK-status code geeft aan dat de back-end in orde is. Alle andere zaken worden beschouwd als een fout. Als er om een of andere reden (inclusief netwerk fout) geen geldig HTTP-antwoord voor een test wordt ontvangen, wordt de test als een fout beschouwd.|
| Meet latentie  | Latentie is de klok tijd gemeten vanaf het moment onmiddellijk voordat de test aanvraag wordt verzonden naar het moment dat de laatste byte van het antwoord wordt ontvangen. We gebruiken een nieuwe TCP-verbinding voor elke aanvraag, zodat deze meting niet wordt afgestemd op back-ends met bestaande warme verbindingen.  |

## <a name="how-front-door-determines-backend-health"></a>De status van de back-end voor de deur bepalen

Azure front deur maakt gebruik van hetzelfde proces van drie stappen hieronder voor alle algoritmen om de status te bepalen.

1. Uitgeschakelde back-ends uitsluiten.

1. Back-end met status controles uitsluiten:

    * Deze selectie wordt uitgevoerd door de laatste _n_ antwoorden op de status test te bekijken. Als ten minste _x_ in orde is, wordt de back-end als gezond beschouwd.

    * _n_ wordt geconfigureerd door de eigenschap SampleSize in instellingen voor taak verdeling te wijzigen.

    * _x_ wordt geconfigureerd door de eigenschap SuccessfulSamplesRequired in instellingen voor taak verdeling te wijzigen.

1. Voor de sets van gezonde back-ends in de back-end-groep meet de voor deur bovendien de latentie (round-trip tijd) voor elke back-end.


## <a name="complete-health-probe-failure"></a>Het volt ooien van de status test is mislukt

Als de status controles voor elke back-end in een back-end-groep mislukken, worden alle back-ends in orde beschouwd en wordt het verkeer gerouteerd in een round robin distributie over alle.

Zodra een back-end een goede status krijgt, wordt het normale algoritme voor taak verdeling door de voor deur hervat.

## <a name="disabling-health-probes"></a>Status controles uitschakelen

Als u één back-end in uw back-end-groep hebt of slechts één back-end actief is in een back-end-groep, kunt u ervoor kiezen om de status controles uit te scha kelen. Door dit te doen, vermindert u de belasting van de back-end van de toepassing.

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [maken van een voor deur standaard/Premium](create-front-door-portal.md).
