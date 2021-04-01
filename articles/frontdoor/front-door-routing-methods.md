---
title: Methoden voor het routeren van verkeer van Azure voor de voor deur | Microsoft Docs
description: Dit artikel helpt u bij het begrijpen van de verschillende routerings methoden voor verkeer die worden gebruikt door de voor deur
services: front-door
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/28/2020
ms.author: duau
ms.openlocfilehash: 2bc056620ff964747dfd83e7525cb5bfd2eb8e52
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91449146"
---
# <a name="front-door-routing-methods"></a>Routerings methoden voor voor deur

Azure front-deur ondersteunt verschillende soorten verkeers routerings methoden om te bepalen hoe u uw HTTP/HTTPS-verkeer naar verschillende service-eind punten kunt routeren. Wanneer uw client aanvragen de voor deur bereiken, wordt de geconfigureerde routerings methode toegepast om ervoor te zorgen dat de aanvragen worden doorgestuurd naar het beste back-end-exemplaar. 

Er zijn vier methoden voor verkeers routering beschikbaar in de voor deur:

* **[Latentie](#latency):** Met de latentie gebaseerde route ring zorgt u ervoor dat aanvragen worden verzonden naar de laagste latentie back-end die acceptabel is binnen een gevoeligheids bereik. In principe worden uw gebruikers aanvragen verzonden naar de ' dichtstbijgelegen ' set back-ends met betrekking tot netwerk latentie.
* **[Prioriteit](#priority):** U kunt prioriteiten toewijzen aan uw back-ends wanneer u een primaire back-end wilt configureren om al het verkeer te onderhouden. De secundaire back-end kan een back-up zijn voor het geval de primaire back-end niet beschikbaar is.
* **[Gewogen](#weighted):** U kunt gewichten toewijzen aan uw back-ends wanneer u verkeer wilt distribueren over een set back-ends. Of u gelijkmatig wilt verdelen of op basis van de gewichts coëfficiënten.
* **[Sessie affiniteit](#affinity):** U kunt sessie affiniteit configureren voor uw frontend-hosts of-domeinen om ervoor te zorgen dat aanvragen van dezelfde eind gebruiker naar dezelfde back-end worden verzonden.

Alle Front Door-configuraties omvatten bewaking van de back-endstatus en geautomatiseerde, directe failover wereldwijd. Zie voor meer informatie [back-end van front deur controleren](front-door-health-probes.md). De voor deur kan worden gebruikt op basis van één routerings methode. Maar afhankelijk van de behoeften van uw toepassing, kunt u ook meerdere routerings methoden combi neren om een optimale routerings topologie te bouwen.

## <a name="lowest-latencies-based-traffic-routing"></a><a name = "latency"></a>Laagste verkeer op basis van latentie-route ring

Het implementeren van back-ends op twee of meer locaties over de hele wereld kan de reactie snelheid van uw toepassingen verbeteren door verkeer naar de doel locatie te sturen die het dichtst bij uw eind gebruikers ligt. De standaard methode voor het routeren van verkeer voor de configuratie van de voor deur stuurt aanvragen van uw eind gebruikers door naar de dichtstbijzijnde back-end van de front-deur omgeving die de aanvraag heeft ontvangen. In combi natie met de anycast-architectuur van de voor deur van Azure, zorgt deze benadering ervoor dat elk van uw eind gebruikers de maximale prestaties op basis van hun locatie kan verkrijgen.

De ' dichtstbijgelegen ' back-end is niet noodzakelijkerwijs het dichtst bij de geografische afstand gemeten. In plaats daarvan bepaalt de voor deur de dichtstbijzijnde back-end door netwerk latentie te meten. Lees meer over [de routerings architectuur van de front deur](front-door-routing-architecture.md). 

Hieronder ziet u de algehele beslissings stroom:

| Beschik bare back-ends | Prioriteit | Latentie signaal (op basis van de status test) | Gewicht |
|-------------| ----------- | ----------- | ----------- |
| Selecteer eerst alle backends die zijn ingeschakeld en in orde zijn geretourneerd (200 OK) voor de status test. Als er zes back-ends A, B, C, D, E en F zijn, en deze C zijn beschadigd en E is uitgeschakeld. De lijst met beschik bare back-ends is A, B, D en F.  | Vervolgens worden de back-end met de hoogste prioriteit geselecteerd voor de beschik bare items. Als back-end A, B en D prioriteit 1 hebben en backend F de prioriteit 2 heeft. Vervolgens worden de geselecteerde back-ends A, B en D.| Selecteer de back-ends met het latentie bereik (minste latentie & latentie gevoeligheid in MS opgegeven). Als back-end A 15 MS is, is B 30 MS en D 60 MS weg van de front-deur omgeving waar de aangevraagde aanvraag en latentie gevoeligheid 30 MS zijn, en de laagste latentie groep bestaat uit de back-end A en B, omdat D groter is dan 30 MS van de dichtstbijzijnde back-end. | Ten slotte wordt de voor deur round robin het verkeer tussen de uiteindelijke geselecteerde pool met back-ends in de verhouding van de opgegeven gewichten. Als back-end A een gewicht van 5 heeft en backend B een gewicht van 8 heeft, wordt het verkeer gedistribueerd in de verhouding 5:8 tussen back-ends A en B. |

>[!NOTE]
> De eigenschap voor de latentie gevoeligheid wordt standaard ingesteld op 0 MS, dat wil zeggen, de aanvraag altijd door sturen naar de snelste beschik bare back-end.

## <a name="priority-based-traffic-routing"></a><a name = "priority"></a>Op prioriteit gebaseerd verkeer: route ring

Een organisatie wil vaak hoge Beschik baarheid bieden voor hun services door meer dan één back-upservice te implementeren, in het geval de primaire versie één keer wordt uitgeschakeld. In de hele branche wordt deze topologie ook wel actief/stand-by of actieve/passieve implementatie topologie genoemd. Met de routerings methode ' Priority ' kunnen Azure-klanten het failover-patroon eenvoudig implementeren.

De standaard-deur bevat een lijst met gelijke prioriteiten voor de back-end. Standaard verzendt de voor deur alleen verkeer naar de back-ends met de hoogste prioriteit (laagste waarde voor de prioriteit), de primaire set back-ends. Als de primaire back-end niet beschikbaar is, stuurt front deur het verkeer naar de secundaire set back-ends (tweede laagste waarde voor prioriteit). Als zowel de primaire als de secundaire back-end niet beschikbaar zijn, gaat het verkeer naar de derde, enzovoort. De beschik baarheid van de back-end is gebaseerd op de geconfigureerde status (ingeschakeld of uitgeschakeld) en de voortdurende status van de back-end, zoals bepaald door de status controles.

### <a name="configuring-priority-for-backends"></a>De prioriteit voor back-ends configureren

Elke back-end in uw back-end-groep van de front-deur configuratie heeft een eigenschap met de naam priority. Dit kan een getal tussen 1 en 5 zijn. Met de voor deur van Azure kunt u de back-end-prioriteit expliciet configureren met behulp van deze eigenschap voor elke back-end. Deze eigenschap is een waarde tussen 1 en 5. Lagere waarden vertegenwoordigen een hogere prioriteit. Back-ends kunnen prioriteits waarden delen.

## <a name="weighted-traffic-routing-method"></a><a name = "weighted"></a>Gewogen verkeer-routerings methode
Met de methode gewogen verkeers routering kunt u verkeer gelijkmatig distribueren of een vooraf gedefinieerde wegings factor gebruiken.

In de routerings methode gewogen verkeer wijst u een gewicht toe aan elke back-end in de front-deur configuratie van de back-endserver. Het gewicht is een geheel getal tussen 1 en 1000. Voor deze para meter wordt het standaard gewicht van ' 50 ' gebruikt.

Met de lijst met beschik bare back-ends die een acceptabele latentie gevoeligheid hebben, wordt het verkeer gedistribueerd met een Round Robin-mechanisme met de verhouding van de opgegeven gewichten. Als de latentie gevoeligheid wordt ingesteld op 0 milliseconden, wordt deze eigenschap pas van kracht als er twee back-ends zijn met dezelfde netwerk latentie. 

De methode weighted maakt enkele handige scenario's mogelijk:

* **Geleidelijke upgrade** van de toepassing: geeft een percentage van het verkeer dat naar een nieuwe back-end kan worden gerouteerd en verhoogt het verkeer gedurende een periode geleidelijk om dit gemiddeld met andere back-ends te brengen.
* **Toepassings migratie naar Azure**: Maak een back-end-groep met zowel Azure als externe back-endservers. Pas het gewicht van de back-ends aan om de voor keur te geven aan de nieuwe back-end. U kunt dit geleidelijk instellen, te beginnen met het uitschakelen van de nieuwe back-ups en vervolgens de laagste gewichten toewijzen, waardoor deze langzaam toeneemt op het niveau van het verkeer. Vervolgens worden de minder voorkeurs back-ends uitgeschakeld en verwijderd uit de groep.  
* **Cloud-bursting voor extra capaciteit**: Vouw snel een on-premises implementatie uit in de Cloud door deze achter de deur te plaatsen. Wanneer u extra capaciteit nodig hebt in de Cloud, kunt u meer back-endservers toevoegen of inschakelen en opgeven welk deel van het verkeer naar elke back-end gaat.

## <a name="session-affinity"></a><a name = "affinity"></a>Sessieaffiniteit
Standaard, zonder sessie affiniteit, worden aanvragen die afkomstig zijn van dezelfde client, doorgestuurd naar verschillende back-endservers. Sommige stateful toepassingen of in bepaalde scenario's waarbij aanvragen van dezelfde gebruiker worden verzonden, geven de voor keur aan dezelfde back-end die de eerste aanvraag heeft verwerkt. De functie Sessieaffiniteit op basis van cookies is handig als u een gebruikerssessie op dezelfde back-end wilt behouden. Met behulp van beheerde cookies kan Azure front-deur verkeer van een gebruikers sessie naar dezelfde back-end omleiden voor verwerking.

U kunt sessieaffiniteit inschakelen op het hostniveau van de front-end, voor elk van de geconfigureerde domeinen (of subdomeinen). Na het inschakelen voegt Front Door een cookie toe aan de gebruikerssessie. Met op cookies gebaseerde sessieaffiniteit kan Front Door verschillende gebruikers herkennen, zelfs als deze hetzelfde IP-adres hebben. Hierdoor kan er nog meer verkeer worden verdeeld tussen uw verschillende back-ends.

De cookie blijft even lang actief als de gebruikerssessie, omdat Front Door momenteel alleen ondersteuning biedt voor sessiecookies. 

> [!NOTE]
> Open bare proxy's kunnen problemen veroorzaken met sessie affiniteit. Dit komt doordat voor het maken van een sessie aan de voor deur een sessie affiniteits cookie moet worden toegevoegd aan het antwoord. Dit kan niet worden gedaan als het antwoord in de cache kan worden opgeslagen, omdat de cookies van andere clients die dezelfde bron aanvragen, worden verstoord. Als u dit wilt beveiligen, wordt de sessie affiniteit **niet** tot stand gebracht als de back-end een cachebaar antwoord stuurt wanneer dit wordt geprobeerd. Als de sessie al tot stand is gebracht, maakt het niet uit of het antwoord van de back-end in de cache kan worden opgeslagen.
> Sessie affiniteit wordt in de volgende omstandigheden tot stand gebracht, **tenzij** het antwoord een HTTP 304-status code heeft:
> - Het antwoord heeft specifieke waarden die zijn ingesteld voor de ```Cache-Control``` header, waardoor caching niet mogelijk is, zoals ' persoonlijk ' of no-Store '.
> - Het antwoord bevat een ```Authorization``` header die niet is verlopen.
> - Het antwoord heeft een HTTP 302-status code.

## <a name="next-steps"></a>Volgende stappen

- Lees hoe u [een Front Door maakt](quickstart-create-front-door.md).
- Lees [hoe Front Door werkt](front-door-routing-architecture.md).
