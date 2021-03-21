---
title: Inleiding tot Azure Service Fabric resource model
description: Meer informatie over het Service Fabric resource model, een vereenvoudigde benadering voor het definiëren van Service Fabric mesh-toepassingen.
author: vturecek
ms.topic: conceptual
ms.date: 10/23/2018
ms.author: vturecek
ms.custom: mvc, devcenter
ms.openlocfilehash: 650755cf2692b613b2998e8ef7b706e09022b178
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99626906"
---
# <a name="introduction-to-service-fabric-resource-model"></a>Inleiding tot het Service Fabric-resourcemodel

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Het Service Fabric resource model beschrijft een eenvoudige benadering voor het definiëren van resources waaruit een Service Fabric mesh-toepassing bestaat. Afzonderlijke bronnen kunnen worden geïmplementeerd in elke Service Fabric omgeving.  Het Service Fabric resource model is ook compatibel met het Azure Resource Manager model. De volgende typen resources worden momenteel ondersteund in dit model:

- Toepassingen en services
- Netwerken
- Gateways
- Geheimen en geheimen/waarden
- Volumes

Elke resource wordt declaratief beschreven in een resource bestand. Dit is een eenvoudig YAML of JSON-document waarin de mesh-toepassing wordt beschreven en wordt ingericht door het Service Fabric-platform.

## <a name="applications-and-services"></a>Toepassingen en services

Een toepassings bron is de implementatie-eenheid, versie beheer en levens duur van een mesh-toepassing. Het bestaat uit een of meer service resources die een micro service vertegenwoordigen. Elke service bron is op zijn beurt samengesteld uit een of meer code pakketten die alles beschrijven wat nodig is voor het uitvoeren van de container installatie kopie die is gekoppeld aan het code pakket.

![Apps en services][Image1]

Een service resource declareert het volgende:

- Container naam, versie en REGI ster
- Benodigde CPU-en geheugen resources voor elke container
- Netwerk eindpunten
- Verwijzingen naar andere resources, zoals netwerken, volumes en geheimen 

Alle code pakketten die als onderdeel van een service resource zijn gedefinieerd, worden als een groep geïmplementeerd en geactiveerd. De service resource beschrijft ook het aantal exemplaren van de service dat moet worden uitgevoerd en ook verwijst naar andere bronnen (bijvoorbeeld netwerk bronnen) waarvan het afhankelijk is.

Als een mesh-toepassing bestaat uit meer dan één service, worden ze niet gegarandeerd samen op hetzelfde knoop punt uitgevoerd. Als er tijdens een upgrade van de toepassing een fout optreedt bij het upgraden van een service, zullen alle services worden teruggezet naar de vorige versie.

Net als alluded eerder kan de levens cyclus van elk toepassings exemplaar onafhankelijk worden beheerd. Zo kan één toepassings exemplaar onafhankelijk van de andere exemplaren van de toepassing worden bijgewerkt. Normaal gesp roken houdt u het aantal services in een toepassing vrij klein, naarmate meer services die u in een toepassing plaatst, het moeilijker wordt om elke service afzonderlijk te beheren.

## <a name="networks"></a>Netwerken

De netwerk resource is een afzonderlijk Implementeer bare resource, onafhankelijk van een toepassings-of service resource die als afhankelijkheid ervan kan verwijzen. Het wordt gebruikt voor het maken van een netwerk voor uw toepassingen. Meerdere services van verschillende toepassingen kunnen deel uitmaken van hetzelfde netwerk.  Meer informatie over netwerken vindt u [in service Fabric-mesh toepassingen](service-fabric-mesh-networks-and-gateways.md).

> [!NOTE]
> De huidige preview ondersteunt alleen een toewijzing tussen toepassingen en netwerken

![Netwerk en gateway][Image2]

## <a name="gateways"></a>Gateways
Een gateway bron verbindt twee netwerken en routeert verkeer.  Met een gateway kunnen uw services communiceren met externe clients en wordt er een ingang naar uw service (s) geboden.  Een gateway kan ook worden gebruikt voor het verbinden van uw nettoepassing met uw eigen bestaande virtuele netwerk. Meer informatie over netwerken vindt u [in service Fabric-mesh toepassingen](service-fabric-mesh-networks-and-gateways.md).

![Netwerk en gateway][Image2]

## <a name="secrets"></a>Geheimen

Geheimen bronnen kunnen worden geïmplementeerd onafhankelijk van een toepassings-of service resource die als afhankelijkheid ervan worden gebruikt. Dit wordt gebruikt om geheimen veilig te bezorgen voor uw toepassingen. Meerdere services van verschillende toepassingen kunnen verwijzen naar waarden van hetzelfde geheim.

## <a name="volumes"></a>Volumes

Containers maken vaak tijdelijke schijven beschikbaar. Tijdelijke schijven zijn echter tijdelijk een nieuwe tijdelijke schijf en verliezen de informatie wanneer een container vastloopt. Het is ook lastig om informatie over tijdelijke schijven met andere containers te delen. Volumes zijn mappen die in de container instanties worden gekoppeld die u kunt gebruiken om de status te behouden. Volumes bieden u bestands opslag voor algemeen gebruik en bieden de mogelijkheid om bestanden te lezen/schrijven met behulp van normale I/O-bestand-Api's van de schijf. De volume resource is een declaratieve manier om te beschrijven hoe een map is gekoppeld en de back-up van de opslag ruimte (Azure Files volume of Service Fabric betrouw bare volume).  Lees de [opslag status](service-fabric-mesh-storing-state.md#volumes)voor meer informatie.

![Diagram toont een service die naar een schijf volume stroomt, die naar beide Service Fabric betrouw bare volume, vervolgens naar een gerepliceerde lokale schijf, en naar Azure Files volume en vervolgens naar de netwerk opslag.][Image3]

## <a name="programming-models"></a>Programmeermodellen
Voor service resource moet alleen een container installatie kopie worden uitgevoerd, waarnaar wordt verwezen in de code pakketten die zijn gekoppeld aan de resource. U kunt elke wille keurige code uitvoeren, geschreven in elke taal, met behulp van een wille keurig Framework in de container zonder dat hiervoor Service Fabric mesh-specifieke Api's moeten worden gebruikt. 

Uw toepassings code blijft draagbaar, zelfs buiten Service Fabric net, en uw toepassings implementaties blijven consistent, ongeacht de taal of het Framework dat wordt gebruikt voor het implementeren van uw services. Of uw toepassing ASP.NET Core, een go of een set processen en scripts is, het implementatie model voor Service Fabric mesh-resources blijft hetzelfde. 

## <a name="packaging-and-deployment"></a>Verpakking en implementatie

Service Fabric nettoepassingen op basis van het resource model worden verpakt als docker-containers.  Service Fabric Mesh is een gedeelde omgeving met meerdere tenants en containers biedt u een hoog isolatie niveau.  Deze toepassingen worden beschreven met behulp van een JSON-indeling of een YAML-indeling (die vervolgens wordt geconverteerd naar JSON). Wanneer u een mesh-toepassing implementeert in azure Service Fabric mesh, is de JSON die wordt gebruikt om de toepassing te beschrijven een Azure Resource Manager sjabloon. Resources worden toegewezen aan Azure-resources.  Wanneer u een mesh-toepassing implementeert in een Service Fabric-cluster (Zelfstandige of Azure-host), is de JSON die wordt gebruikt om de toepassing te beschrijven een indeling die vergelijkbaar is met een Azure Resource Manager sjabloon.  Zodra de implementatie is geïmplementeerd, kunnen nettoepassingen worden beheerd via HTTP-interfaces of de Azure CLI. 


## <a name="next-steps"></a>Volgende stappen 
Lees het overzicht voor meer informatie over Service Fabric Mesh:
- [Service Fabric Mesh - overzicht](service-fabric-mesh-overview.md)

[Image1]: media/service-fabric-mesh-service-fabric-resources/AppsAndServices.png
[Image2]: media/service-fabric-mesh-service-fabric-resources/NetworkAndGateway.png
[Image3]: media/service-fabric-mesh-service-fabric-resources/volumes.png
