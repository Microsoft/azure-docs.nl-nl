---
title: Terminologie voor Azure Service Fabric net
description: In dit artikel vindt u informatie over de terminologie die wordt gebruikt door Azure Service Fabric mesh, zodat u beter inzicht krijgt in de termen die in de documentatie worden gebruikt.
author: georgewallace
ms.author: gwallace
ms.date: 11/28/2018
ms.topic: conceptual
ms.openlocfilehash: 1fda83ec00bed0f0c67d45e1e1af61baa5de5e78
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99626753"
---
# <a name="service-fabric-mesh-terminology"></a>Service Fabric net-terminologie

> [!IMPORTANT]
> De preview-versie van Azure Service Fabric Mesh is buiten gebruik gesteld. Nieuwe implementaties zijn niet langer toegestaan via de API van Service Fabric net. Ondersteuning voor bestaande implementaties gaat door tot 28 april 2021.
> 
> Zie [Azure service Fabric Netpreview buiten](https://azure.microsoft.com/updates/azure-service-fabric-mesh-preview-retirement/)gebruik stellen voor meer informatie.

Azure Service Fabric Mesh is een volledig beheerde service waarmee ontwikkelaars microservices-toepassingen kunnen implementeren zonder virtuele machines, opslag of netwerken hoeven te beheren. In dit artikel vindt u informatie over de terminologie die wordt gebruikt door Azure Service Fabric mesh, zodat u beter inzicht krijgt in de termen die in de documentatie worden gebruikt.

## <a name="service-fabric"></a>Service Fabric

[Service Fabric](../service-fabric/index.yml) is een open-source platform voor gedistribueerde systemen waarmee schaal bare en betrouw bare micro services eenvoudig kunnen worden verpakt, geïmplementeerd en beheerd. Service Fabric is de Orchestrator die Service Fabric net aanstuurt. Service Fabric biedt opties voor hoe u uw micro Services-toepassingen kunt bouwen en uitvoeren. U kunt elk Framework gebruiken om uw services te schrijven en te kiezen waar u de toepassing uitvoert vanuit meerdere omgevings opties.

## <a name="application-and-service-concepts"></a>Toepassings-en service concepten

**Service Fabric mesh-toepassing**: Service Fabric mesh-toepassingen worden beschreven door het [resource model](./service-fabric-mesh-service-fabric-resources.md) (yaml-en JSON-bron bestanden) en kunnen worden geïmplementeerd in elke omgeving waarin Service Fabric wordt uitgevoerd.

**Service Fabric systeem eigen toepassing**: Service Fabric systeem eigen toepassingen worden beschreven door het [systeem eigen toepassings model](../service-fabric/service-fabric-application-model.md) (XML-toepassings-en service manifesten).  Service Fabric systeem eigen toepassingen kunnen niet worden uitgevoerd in Service Fabric net.

**Toepassing**: een service Fabric nettoepassing is de implementatie-eenheid, versie beheer en levens duur van een mesh-toepassing. De levens cyclus van elk toepassings exemplaar kan onafhankelijk worden beheerd.  Toepassingen bestaan uit een of meer service code pakketten en instellingen. Een toepassing wordt gedefinieerd met behulp van het schema van het Azure resource model (RM).  Services worden beschreven als eigenschappen van de toepassings bron in een RM-sjabloon.  Voor netwerken en volumes die worden gebruikt door de toepassing wordt verwezen door de toepassing.  Bij het maken van een toepassing worden de toepassing, de service (s), het netwerk en volume (s) gemodelleerd met behulp van het Service Fabric resource model.

**Service**: een service in een toepassing vertegenwoordigt een micro service en voert een volledige en zelfstandige functie uit. Elke service bestaat uit een of meer code pakketten met een beschrijving van alles wat nodig is voor het uitvoeren van de container installatie kopie die is gekoppeld aan het code pakket.  U kunt het aantal service replica's in een toepassing in-en uitschalen.

**Code pakket**: code pakketten beschrijven alles wat nodig is voor het uitvoeren van de container installatie kopie die is gekoppeld aan het code pakket, met inbegrip van het volgende:

* Container naam, versie en REGI ster
* Benodigde CPU-en geheugen resources voor elke container
* Netwerk eindpunten
* Volumes die moeten worden gekoppeld in de container en verwijzen naar een afzonderlijke volume bron.

## <a name="deployment-and-application-models"></a>Implementatie-en toepassings modellen 

Als u uw services wilt implementeren, moet u beschrijven hoe ze moeten worden uitgevoerd. Service Fabric ondersteunt drie verschillende implementatie modellen:

### <a name="resource-model"></a>Resourcemodel
Service Fabric resources zijn alles wat afzonderlijk kan worden geïmplementeerd in Service Fabric; inclusief toepassingen, services, netwerken en volumes. Resources worden gedefinieerd met behulp van een JSON-bestand dat kan worden geïmplementeerd in een cluster eindpunt.  Voor Service Fabric net wordt het Azure resource model-schema gebruikt. Een YAML-bestands schema kan ook worden gebruikt om eenvoudig definitie bestanden te schrijven. Resources kunnen overal worden geïmplementeerd Service Fabric worden uitgevoerd. Het resource model is de eenvoudigste manier om uw Service Fabric-toepassingen te beschrijven. De belangrijkste focus is het eenvoudig implementeren en beheren van container Services. Lees [Introduction to the service Fabric resource model](./service-fabric-mesh-service-fabric-resources.md)voor meer informatie.

### <a name="native-model"></a>Systeem eigen model
Het systeem eigen toepassings model biedt uw toepassingen volledige toegang tot Service Fabric op laag niveau. Toepassingen en services worden gedefinieerd als geregistreerde typen in XML-manifest bestanden.

Het systeem eigen model ondersteunt het Reliable Services Framework, dat toegang biedt tot de Service Fabric runtime-Api's en Cluster beheer-Api's in C# en Java. Het systeem eigen model biedt ook ondersteuning voor wille keurige containers en uitvoer bare bestanden.

Het systeem eigen model wordt niet ondersteund in de Service Fabric-omgeving.  Zie [programmeer model Overview](../service-fabric/service-fabric-choose-framework.md)(Engelstalig) voor meer informatie.

### <a name="docker-compose"></a>Docker opstellen 
[Docker opstellen](https://docs.docker.com/compose/) maakt deel uit van het docker-project. Service Fabric biedt beperkte ondersteuning voor het implementeren van toepassingen met behulp van het model docker opstellen.

## <a name="environments"></a>Omgevingen

Service Fabric is een open-source platform technologie waarmee verschillende services en producten zijn gebaseerd. Micro soft biedt de volgende opties:

 - **Service Fabric mesh**: een volledig beheerde service voor het uitvoeren van service Fabric toepassingen in Microsoft Azure.
 - **Azure service Fabric**: het gehoste service Fabric cluster van Azure. Het biedt integratie tussen Service Fabric en de Azure-infra structuur, samen met upgrade-en configuratie beheer van Service Fabric clusters.
 - **Service Fabric standalone**: een set hulpprogram ma's voor installatie en configuratie om [service Fabric-clusters overal](../service-fabric/service-fabric-deploy-anywhere.md) (on-premises of op een Cloud provider) te implementeren. Niet beheerd door Azure.
 - **Service Fabric Development cluster**: biedt een lokale ontwikkel ervaring op Windows, Linux of Mac voor het ontwikkelen van service Fabric toepassingen.

## <a name="environment-framework-and-deployment-model-support-matrix"></a>Ondersteunings matrix voor omgeving, Framework en implementatie model
Verschillende omgevingen hebben een ander ondersteunings niveau voor frameworks en implementatie modellen. In de volgende tabel worden de ondersteunde combi Naties van Framework en implementatie modellen beschreven.

| Type toepassing | Beschreven door | Azure Service Fabric Mesh | Azure Service Fabric-clusters (elk besturings systeem)| Lokaal cluster | Zelfstandig cluster |
|---|---|---|---|---|---|
| Service Fabric mesh-toepassingen | Resource model (YAML & JSON) | Ondersteund |Niet ondersteund | Windows: ondersteund, Linux en Mac: niet ondersteund | Windows-niet ondersteund |
|Service Fabric systeem eigen toepassingen | Systeem eigen toepassings model (XML) | Niet ondersteund| Ondersteund|Ondersteund|Windows: ondersteund|

In de volgende tabel worden de verschillende toepassings modellen en de hulp middelen beschreven die voor hen bestaan, vergeleken met Service Fabric.

| Type toepassing | Beschreven door | Visual Studio | Eclipse | SFCTL | AZ CLI | PowerShell|
|---|---|---|---|---|---|---|
| Service Fabric mesh-toepassingen | Resource model (YAML & JSON) | VS 2017 |Niet ondersteund |Niet ondersteund | Alleen ondersteunde mesh-omgeving | Niet ondersteund|
|Service Fabric systeem eigen toepassingen | Systeem eigen toepassings model (XML) | VS 2017 en VS 2015| Ondersteund|Ondersteund|Ondersteund|Ondersteund|

## <a name="next-steps"></a>Volgende stappen

Lees [Wat is Service Fabric Mesh?](service-fabric-mesh-overview.md) voor meer informatie over Service Fabric Mesh.

Zoek antwoorden op [veelgestelde vragen](service-fabric-mesh-faq.md).
