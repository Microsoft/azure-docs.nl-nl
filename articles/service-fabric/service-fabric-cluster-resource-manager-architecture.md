---
title: Resource Manager-architectuur
description: Een overzicht van en architectuur informatie over de Azure Service Fabric cluster resource Manager-service.
author: masnider
ms.topic: conceptual
ms.date: 08/18/2017
ms.author: masnider
ms.openlocfilehash: 0aff55810508fedcf354fba3d9fca9f7a402029b
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94685831"
---
# <a name="cluster-resource-manager-architecture-overview"></a>Overzicht van cluster resource manager-architectuur
De Service Fabric cluster resource manager is een centrale service die in het cluster wordt uitgevoerd. Het beheert de gewenste status van de services in het cluster, met name ten opzichte van het Resource verbruik en eventuele plaatsings regels. 

Als u de resources in uw cluster wilt beheren, moet de Service Fabric cluster resource manager verschillende stukjes informatie hebben:

- Welke services bestaan momenteel
- Het huidige (of standaard) Resource verbruik van elke service 
- De resterende cluster capaciteit 
- De capaciteit van de knoop punten in het cluster 
- De hoeveelheid resources die op elk knoop punt is verbruikt

Het resource gebruik van een bepaalde service kan in de loop van de tijd worden gewijzigd en de services zijn meestal meer dan één type resource. In verschillende services kunnen er zowel echte fysieke als fysieke resources worden gemeten. Services kunnen fysieke metrische gegevens, zoals geheugen en schijf verbruik, bijhouden. Meer informatie over logische metrische gegevens, zoals "WorkQueueDepth" of "TotalRequests". Zowel logische als fysieke waarden kunnen worden gebruikt in hetzelfde cluster. Metrieken kunnen worden gedeeld in veel services of specifiek zijn voor een bepaalde service.

## <a name="other-considerations"></a>Andere overwegingen
De eigen aren en Opera tors van het cluster kunnen verschillen van de service en de makers van de toepassing, of ten minste dezelfde mensen de verschillende hoeden dragen. Bij het ontwikkelen van uw toepassing kent u enkele dingen over wat deze nodig heeft. U hebt een schatting van de resources die worden gebruikt en hoe verschillende services moeten worden geïmplementeerd. De weblaag moet bijvoorbeeld worden uitgevoerd op knoop punten die beschikbaar zijn op het Internet, terwijl de database services dat niet moeten. Een ander voor beeld: de webservices zijn waarschijnlijk beperkt door de CPU en het netwerk, terwijl de gegevenslaag services meer informatie over het geheugen en schijf verbruik betreffen. De persoon die een live-site incident voor die service in productie verwerkt of die een upgrade naar de service beheert, heeft echter een andere taak en vereist verschillende hulpprogram ma's. 

Zowel het cluster als de services zijn dynamisch:

- Het aantal knoop punten in het cluster kan worden verg root of verkleind
- Knoop punten van verschillende groottes en typen kunnen worden geleverd en gestart
- Services kunnen worden gemaakt, verwijderd en de gewenste resource toewijzingen en plaatsings regels worden gewijzigd
- Upgrades of andere beheer bewerkingen kunnen het cluster op infrastructuur niveaus door lopen op de toepassing
- Er kunnen op elk moment storingen optreden.

## <a name="cluster-resource-manager-components-and-data-flow"></a>Cluster resource manager-onderdelen en-gegevens stroom
Cluster resource manager moet de vereisten van elke service en het verbruik van resources voor elk Service object binnen die services bijhouden. Cluster resource manager heeft twee conceptuele onderdelen: agents die worden uitgevoerd op elk knoop punt en een fout tolerante service. Met de agents op elk knoop punt worden laad rapporten van services bijgehouden, geaggregeerd en periodiek gerapporteerd. De cluster resource Manager-service aggregeert alle informatie van de lokale agents en reageert op basis van de huidige configuratie.

Laten we het volgende diagram eens bekijken:

<center>

![Diagram waarin de cluster resource Manager-service wordt weer gegeven, wordt alle informatie van de lokale agents geaggregeerd en reageert op basis van de huidige configuratie.][Image1]
</center>

Tijdens runtime zijn er veel wijzigingen die zich kunnen voordoen. Stel bijvoorbeeld dat de hoeveelheid resources die door bepaalde services worden gebruikt, wordt gewijzigd, dat sommige services mislukken en dat bepaalde knoop punten aan het cluster worden toegevoegd en verlaat. Alle wijzigingen in een knoop punt worden geaggregeerd en regel matig verzonden naar de cluster resource Manager-service (1, 2) waar ze opnieuw worden geaggregeerd, geanalyseerd en opgeslagen. Elke paar seconden dat de service doorzoekt naar de wijzigingen en bepaalt of acties nodig zijn (3). Er kan bijvoorbeeld worden opgemerkt dat er sommige lege knoop punten aan het cluster zijn toegevoegd. Als gevolg hiervan wordt besloten sommige services naar deze knoop punten te verplaatsen. De cluster resource manager kan ook zien dat een bepaald knoop punt overbelast is, of dat bepaalde services zijn mislukt of verwijderd, waardoor er elders bronnen worden vrijgemaakt.

Laten we eens kijken naar het volgende diagram en zien wat er gebeurt. Stel dat cluster resource Manager bepaalt dat wijzigingen nodig zijn. Het coördineert met andere systeem services (met name de Failover Manager) om de benodigde wijzigingen door te voeren. Vervolgens worden de benodigde opdrachten verzonden naar de juiste knoop punten (4). Stel bijvoorbeeld dat de Resource Manager heeft vastgesteld dat Knooppunt5 is overbelast, en dat de service B van Knooppunt5 naar Knooppunt4 moet worden verplaatst. Aan het einde van de herconfiguratie (5) ziet het cluster er als volgt uit:

<center>

![Resource Balancer-architectuur][Image2]
</center>

## <a name="next-steps"></a>Volgende stappen
- Cluster resource manager heeft veel opties voor het beschrijven van het cluster. Lees dit artikel over [het beschrijven van een service Fabric cluster](./service-fabric-cluster-resource-manager-cluster-description.md) voor meer informatie.
- De primaire taken van cluster resource manager worden opnieuw verdeeld over het cluster en het afdwingen van plaatsings regels. Zie [uw service Fabric cluster balanceren](./service-fabric-cluster-resource-manager-balancing.md) voor meer informatie over het configureren van dit gedrag

[Image1]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-1.png
[Image2]:./media/service-fabric-cluster-resource-manager-architecture/Service-Fabric-Resource-Manager-Architecture-Activity-2.png
