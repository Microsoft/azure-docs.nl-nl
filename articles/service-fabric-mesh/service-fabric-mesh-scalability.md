---
title: Schaal baarheid van Azure Service Fabric mesh-apps
description: Een van de voor delen van het implementeren van toepassingen naar Service Fabric net is de mogelijkheid om uw services eenvoudig te schalen, hetzij hand matig of met beleid voor automatisch schalen.
author: georgewallace
ms.author: gwallace
ms.date: 10/26/2018
ms.topic: conceptual
ms.openlocfilehash: 3f0e115e596925878bf9fdd43b7074cefdba47b2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626855"
---
# <a name="scaling-service-fabric-mesh-applications"></a>Service Fabric-mesh-toepassingen schalen

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Een van de belangrijkste voordelen van het implementeren van toepassingen naar Service Fabric Mesh is de mogelijkheid voor u om services eenvoudig in of uit te schalen. Dit is handig voor het afhandelen van wisselende belastingen van uw services of het verbeteren van de beschikbaarheid. U kunt uw services hand matig in-of uitschalen of het beleid voor automatisch schalen instellen.

## <a name="manual-scaling-instances"></a>Instanties voor hand matig schalen

In de implementatiesjabloon voor de toepassingsresource heeft elke service een eigenschap *replicaCount* die kan worden gebruikt om in te stellen hoe vaak die service wordt geïmplementeerd. Een toepassing kan bestaan uit meerdere services, waarbij elke service een unieke waarde voor *replicaCount* heeft, die samen worden geïmplementeerd en beheerd. Om het aantal service-replica's te schalen, wijzigt u de waarde *replicaCount* voor elke service die u wilt schalen in de implementatiesjabloon of het parametersbestand. Vervolgens voert u een upgrade van de toepassing uit.

Zie [uw services hand matig schalen in of uit](service-fabric-mesh-tutorial-template-scale-services.md)voor voor beelden van het hand matig schalen van services-exemplaren.

## <a name="autoscaling-service-instances"></a>Service-exemplaren automatisch schalen
Automatisch schalen is een extra mogelijkheid van Service Fabric om het aantal service-exemplaren dynamisch te schalen (horizon taal schalen). Automatisch schalen biedt een fantastische elasticiteit en maakt het inrichten of verwijderen van service-exemplaren mogelijk op basis van CPU-of geheugen gebruik.  Automatisch schalen biedt u de mogelijkheid om het juiste aantal service-exemplaren uit te voeren voor uw werk belasting en de kosten te optimaliseren.

Er wordt een beleid voor automatisch schalen gedefinieerd per service in het bron bestand van de service. Elk schaal beleid bestaat uit twee delen:

- Een trigger voor schalen, die beschrijft wanneer het schalen van de service wordt uitgevoerd. Er zijn drie factoren die bepalen wanneer de service wordt geschaald. *Onderste belasting drempel* is een waarde die bepaalt wanneer de service wordt geschaald. Als de gemiddelde belasting van alle exemplaren van de partities lager is dan deze waarde, wordt de service geschaald in. *Drempel* waarde voor laden van boven is een getal dat bepaalt wanneer de service wordt uitgeschaald. Als de gemiddelde belasting van alle exemplaren van de partitie hoger is dan deze waarde, wordt de service uitgeschaald. Het *interval voor schalen* bepaalt hoe vaak (in seconden) de trigger wordt gecontroleerd. Als de trigger is ingeschakeld, wordt het mechanisme toegepast als er wordt geschaald. Als schalen niet nodig is, wordt er geen actie ondernomen. In beide gevallen wordt de trigger niet opnieuw gecontroleerd voordat het schaal interval verloopt.

- Een schaal mechanisme dat beschrijft hoe schalen wordt uitgevoerd wanneer deze wordt geactiveerd. *Schaal verhoging* bepaalt hoeveel instanties worden toegevoegd of verwijderd wanneer het mechanisme wordt geactiveerd. *Maximum aantal exemplaren* definieert de bovengrens voor schalen. Als het aantal exemplaren deze limiet bereikt, wordt de service niet uitgeschaald, ongeacht de belasting. *Minimum aantal exemplaren* definieert de ondergrens voor schalen. Als het aantal exemplaren van de partitie deze limiet bereikt, wordt de service niet geschaald, ongeacht de belasting.

Lees [Services automatisch schalen](service-fabric-mesh-howto-auto-scale-services.md)voor meer informatie over het instellen van een beleid voor automatisch schalen voor uw service.

## <a name="next-steps"></a>Volgende stappen

Zie [service Fabric resources](service-fabric-mesh-service-fabric-resources.md) voor meer informatie over het toepassings model.
