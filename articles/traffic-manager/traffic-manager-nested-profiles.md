---
title: Geneste Traffic Manager profielen in azure
titleSuffix: Azure Traffic Manager
description: In dit artikel wordt de functie geneste profielen van Azure Traffic Manager uitgelegd
services: traffic-manager
documentationcenter: ''
author: duongau
manager: twooley
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/22/2018
ms.author: duau
ms.openlocfilehash: 5f2aa3d05d349880b5eb2d35a2c58af0741b9855
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98185577"
---
# <a name="nested-traffic-manager-profiles"></a>Geneste Traffic Manager-profielen

Traffic Manager bevat een reeks methoden voor het routeren van verkeer waarmee u kunt bepalen hoe Traffic Manager kiest welk eind punt verkeer van elke eind gebruiker moet ontvangen. Zie [Traffic Manager methoden voor het routeren van verkeer](traffic-manager-routing-methods.md)voor meer informatie.

Elk Traffic Manager profiel bevat één methode voor het routeren van verkeer. Er zijn echter scenario's die meer geavanceerde verkeers routering vereisen dan de route ring van een enkel Traffic Manager profiel. U kunt Traffic Manager profielen nesten om de voor delen van meer dan één Traffic-routerings methode te combi neren. Met geneste profielen kunt u het standaard gedrag van Traffic Manager onderdrukken om grotere en complexere toepassings implementaties te ondersteunen.

De volgende voor beelden laten zien hoe u geneste Traffic Manager profielen kunt gebruiken in verschillende scenario's.

## <a name="example-1-combining-performance-and-weighted-traffic-routing"></a>Voor beeld 1: het combi neren van prestaties en gewogen verkeers routering

Stel dat u een toepassing hebt geïmplementeerd in de volgende Azure-regio's: VS-West, Europa-west en Azië-oost. U gebruikt de verkeers routerings methode van Traffic Manager prestaties om verkeer te distribueren naar de regio die het dichtst bij de gebruiker ligt.

![Profiel voor één Traffic Manager][4]

Stel nu dat u een update naar uw service wilt testen voordat u deze uitgebreid uitbreidt. U wilt de ' gewogen ' Traffic-routerings methode gebruiken om een klein percentage verkeer naar uw test implementatie te sturen. U stelt de test implementatie in naast de bestaande productie-implementatie in Europa-west.

U kunt niet zowel ' gewogen ' als ' prestatie verkeer-route ring combi neren in één profiel. Ter ondersteuning van dit scenario maakt u een Traffic Manager profiel met behulp van de twee Europa-west-eind punten en de ' gewogen ' Traffic-routerings methode. Vervolgens voegt u dit onderliggende profiel als een eind punt toe aan het bovenliggende profiel. Het bovenliggende profiel maakt nog steeds gebruik van de routerings methode voor prestatie verkeer en bevat de andere globale implementaties als eind punten.

In het volgende diagram ziet u dit voor beeld:

![Geneste Traffic Manager-profielen][2]

In deze configuratie wordt verkeer dat via het bovenliggende profiel wordt gestuurd, doorgaans verkeer verdeeld over regio's. Binnen Europa-west, distribueert het geneste profiel het verkeer naar de productie-en test eindpunten op basis van de toegewezen gewichten.

Wanneer het bovenliggende profiel de routerings methode voor prestaties verkeer gebruikt, moet aan elk eind punt een locatie worden toegewezen. De locatie wordt toegewezen wanneer u het eind punt configureert. Kies de Azure-regio die het dichtst bij uw implementatie ligt. De Azure-regio's zijn de locatie waarden die worden ondersteund door de tabel Internet latentie. Zie voor meer informatie [Traffic Manager ' prestaties ' verkeer routerings methode](traffic-manager-routing-methods.md#performance).

## <a name="example-2-endpoint-monitoring-in-nested-profiles"></a>Voor beeld 2: eindpunt bewaking in geneste profielen

Traffic Manager bewaakt de status van elk service-eind punt actief. Als een eind punt is beschadigd, stuurt Traffic Manager gebruikers naar alternatieve eind punten om de beschik baarheid van uw service te behouden. Dit endpoint-bewakings-en failover-gedrag is van toepassing op alle methoden voor het routeren van verkeer. Zie [Traffic Manager endpoint monitoring](traffic-manager-monitoring.md)voor meer informatie. Eindpunt controle werkt anders voor geneste profielen. Bij geneste profielen voert het bovenliggende profiel niet rechtstreeks status controles uit op het kind. In plaats daarvan wordt de status van de eind punten van het onderliggende profiel gebruikt voor het berekenen van de algemene status van het onderliggende profiel. Deze status informatie wordt door gegeven aan de hiërarchie van geneste profielen. Het bovenliggende profiel gebruikt deze geaggregeerde status om te bepalen of het verkeer naar het onderliggende profiel moet worden omgeleid. Raadpleeg de [Veelgestelde vragen](traffic-manager-FAQs.md#traffic-manager-nested-profiles) voor volledige informatie over de status controle van geneste profielen.

Als u terugkeert naar het vorige voor beeld, mislukt de productie-implementatie in Europa-west. Standaard stuurt het onderliggende profiel alle verkeer naar de test implementatie. Als de test implementatie ook mislukt, bepaalt het bovenliggende profiel dat het onderliggende profiel geen verkeer mag ontvangen omdat alle onderliggende eind punten niet in orde zijn. Vervolgens distribueert het bovenliggende profiel het verkeer naar de andere regio's.

![Failover genest Profiel (standaard gedrag)][3]

Misschien bent u tevreden over deze rang schikking. U kunt er ook voor zorgen dat al het verkeer voor Europa-west nu naar de test implementatie gaat in plaats van een beperkt deel verkeer. Ongeacht de status van de test implementatie, wilt u een failover uitvoeren naar de andere regio's wanneer de productie-implementatie in Europa-west mislukt. Als u deze failover wilt inschakelen, kunt u de para meter ' MinChildEndpoints ' opgeven bij het configureren van het onderliggende profiel als een eind punt in het bovenliggende profiel. De para meter bepaalt het minimale aantal beschik bare eind punten in het onderliggende profiel. De standaard waarde is 1. Voor dit scenario stelt u de waarde voor MinChildEndpoints in op 2. Onder deze drempel waarde houdt het bovenliggende profiel in dat het hele onderliggende profiel niet beschikbaar is en stuurt het verkeer naar de andere eind punten.

In de volgende afbeelding ziet u deze configuratie:

![Failover van geneste profielen met ' MinChildEndpoints ' = 2][4]

> [!NOTE]
> Met de methode voor het routerings verkeer met prioriteit wordt al het verkeer gedistribueerd naar een enkel eind punt. Er is dus weinig doel in een MinChildEndpoints-instelling dan ' 1 ' voor een onderliggend profiel.

## <a name="example-3-prioritized-failover-regions-in-performance-traffic-routing"></a>Voor beeld 3: failover-regio's met prioriteit in prestaties verkeer routeren

Het standaard gedrag voor de routerings methode ' prestaties ' is wanneer u eind punten in verschillende geografische locaties hebt, worden de eind gebruikers doorgestuurd naar het dichtstbijzijnde eind punt in termen van de laagste netwerk latentie.

Stel dat u de voor keur geeft aan de failover van Europa-west verkeer naar VS-West en alleen directe verkeer naar andere regio's wanneer beide eind punten niet beschikbaar zijn. U kunt deze oplossing maken met behulp van een onderliggend profiel met de methode voor het routeren van verkeer.

![Verkeers routering voor prestaties met voorkeurs failover][6]

Omdat het Europa-west-eind punt een hogere prioriteit heeft dan het eind punt van de VS West, wordt al het verkeer naar het Europa-west eind punt verzonden wanneer beide eind punten online zijn. Als Europa-west mislukt, wordt het verkeer omgeleid naar de VS-West. Met het geneste profiel wordt verkeer alleen omgeleid naar Azië-oost als zowel Europa-west als de VS-West mislukt.

U kunt dit patroon herhalen voor alle regio's. Vervang alle drie de eind punten in het bovenliggende profiel door drie onderliggende profielen, elk met een failover-volg orde met prioriteit.

## <a name="example-4-controlling-performance-traffic-routing-between-multiple-endpoints-in-the-same-region"></a>Voor beeld 4: de route ring van verkeer van de prestaties tussen meerdere eind punten in dezelfde regio beheren

Stel dat de methode voor verkeers routering voor prestaties wordt gebruikt in een profiel met meer dan één eind punt in een bepaalde regio. Verkeer dat naar die regio wordt gestuurd, wordt standaard gelijkmatig verdeeld over alle beschik bare eind punten in die regio.

![' Prestaties ' verkeers routering in-Region-verkeer distributie (standaard gedrag)][7]

In plaats van meerdere eind punten toe te voegen aan Europa-west, worden deze eind punten Inge sloten in een afzonderlijk onderliggend profiel. Het onderliggende profiel wordt toegevoegd aan de bovenliggende sleutel als het enige eind punt in Europa-west. De instellingen op het onderliggende profiel kunnen de verkeers distributie met Europa-west bepalen door de route ring op basis van prioriteit of gewogen verkeer in die regio in te scha kelen.

![' Prestaties ' verkeers routering met aangepaste verkeer distributie in regio's][8]

## <a name="example-5-per-endpoint-monitoring-settings"></a>Voor beeld 5: instellingen voor bewaking per eind punt

Stel dat u gebruikmaakt van Traffic Manager om het verkeer van een oude on-premises website soepel te migreren naar een nieuwe Cloud versie die wordt gehost in Azure. Voor de oude site wilt u de URL van de start pagina gebruiken om de status van de site te controleren. Voor de nieuwe Cloud versie implementeert u echter een aangepaste controle pagina (Path '/monitor.aspx ') met aanvullende controles.

![Eindpunt bewaking Traffic Manager (standaard gedrag)][9]

De controle-instellingen in een Traffic Manager profiel zijn van toepassing op alle eind punten binnen één profiel. Met geneste profielen gebruikt u een ander onderliggend profiel per site om verschillende bewakings instellingen te definiëren.

![Endpoint-bewaking Traffic Manager met instellingen per eind punt][10]

## <a name="faqs"></a>Veelgestelde vragen

* [Hoe kan ik geneste profielen configureren?](./traffic-manager-faqs.md#traffic-manager-nested-profiles)

* [Hoeveel lagen nesten ondersteunt Traffic Manager?](./traffic-manager-faqs.md#how-many-layers-of-nesting-does-traffic-manger-support)

* [Kan ik andere eindpunt typen met geneste onderliggende profielen combi neren in hetzelfde Traffic Manager profiel?](./traffic-manager-faqs.md#can-i-mix-other-endpoint-types-with-nested-child-profiles-in-the-same-traffic-manager-profile)

* [Hoe is het facturerings model van toepassing op geneste profielen?](./traffic-manager-faqs.md#how-does-the-billing-model-apply-for-nested-profiles)

* [Is er sprake van invloed op de prestaties van geneste profielen?](./traffic-manager-faqs.md#is-there-a-performance-impact-for-nested-profiles)

* [Hoe berekent Traffic Manager de status van een genest eind punt in een bovenliggend profiel?](./traffic-manager-faqs.md#how-does-traffic-manager-compute-the-health-of-a-nested-endpoint-in-a-parent-profile)

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [Traffic Manager profielen](traffic-manager-overview.md)

Meer informatie over het [maken van een Traffic Manager profiel](./quickstart-create-traffic-manager-profile.md)

<!--Image references-->
[1]: ./media/traffic-manager-nested-profiles/figure-1.png
[2]: ./media/traffic-manager-nested-profiles/figure-2.png
[3]: ./media/traffic-manager-nested-profiles/figure-3.png
[4]: ./media/traffic-manager-nested-profiles/figure-4.png
[5]: ./media/traffic-manager-nested-profiles/figure-5.png
[6]: ./media/traffic-manager-nested-profiles/figure-6.png
[7]: ./media/traffic-manager-nested-profiles/figure-7.png
[8]: ./media/traffic-manager-nested-profiles/figure-8.png
[9]: ./media/traffic-manager-nested-profiles/figure-9.png
[10]: ./media/traffic-manager-nested-profiles/figure-10.png