---
title: Azure Service Fabric-toepassings model
description: Toepassingen en services in azure Service Fabric model leren en beschrijven met toepassings-en service manifest bestanden.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: 84e6b2309fdb206771d4ea01aa03c7f355d6ff19
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85963730"
---
# <a name="model-an-application-in-service-fabric"></a>Een toepassing model leren in Service Fabric
Dit artikel bevat een overzicht van het Azure Service Fabric-toepassings model en hoe u een toepassing en service kunt definiëren via manifest bestanden.

## <a name="understand-the-application-model"></a>Inzicht in het toepassings model
Een toepassing is een verzameling van onderdeel Services die een bepaalde functie of functies uitvoeren. Een service voert een volledige en zelfstandige functie uit en kan onafhankelijk van andere services worden gestart en uitgevoerd.  Een service bestaat uit code, configuratie en gegevens. Voor elke service bestaat code uit de binaire uitvoer bare bestanden, configuratie bestaat uit service-instellingen die tijdens runtime kunnen worden geladen en gegevens bestaan uit wille keurige statische gegevens die door de service worden gebruikt. Elk onderdeel in dit hiërarchische toepassings model kan worden geversied en onafhankelijk van elkaar worden bijgewerkt.

![Het Service Fabric toepassings model][appmodel-diagram]

Een toepassings type is een categorisatie van een toepassing en bestaat uit een bundel van service typen. Een service type is een categorisatie van een service. De categorisatie kan verschillende instellingen en configuraties hebben, maar de kern functionaliteit blijft hetzelfde. De exemplaren van een service zijn de verschillende service configuratie variaties van hetzelfde service type.  

Klassen (of "typen") van toepassingen en services worden beschreven met XML-bestanden (toepassings manifesten en service manifesten).  In de manifesten worden toepassingen en services beschreven en zijn de sjablonen waarmee toepassingen kunnen worden geïnstantieerd uit de installatie kopie opslag van het cluster.  Manifesten worden gedetailleerd besproken in [toepassings-en service manifesten](service-fabric-application-and-service-manifests.md). De schema definitie voor de ServiceManifest.xml en het ApplicationManifest.xml-bestand wordt geïnstalleerd met de SDK en hulpprogram ma's van Service Fabric naar *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.XSD*. Het XML-schema wordt beschreven in de [documentatie bij ServiceFabricServiceModel. XSD-schema](service-fabric-service-model-schema.md).

De code voor verschillende toepassings exemplaren wordt uitgevoerd als afzonderlijke processen, zelfs als deze worden gehost door hetzelfde Service Fabric knoop punt. Bovendien kan de levens cyclus van elke toepassings instantie afzonderlijk worden beheerd (bijvoorbeeld geüpgraded). In het volgende diagram ziet u hoe toepassings typen bestaan uit service typen, die op zijn beurt bestaan uit code, configuratie en gegevens pakketten. Om het diagram te vereenvoudigen, worden alleen de code/config/gegevens pakketten voor `ServiceType4` weer gegeven, hoewel elk Service type een of meer van de pakket typen zou bevatten.

![Service Fabric toepassings typen en-service typen][cluster-imagestore-apptypes]

Er kunnen een of meer exemplaren van een service type actief zijn in het cluster. Bijvoorbeeld stateful service instanties of replica's, behaalt u hoge betrouw baarheid door de status te repliceren tussen replica's die zich op verschillende knoop punten in het cluster bevinden. Replicatie heeft in wezen de redundantie voor de service beschikbaar, zelfs als één knoop punt in een cluster uitvalt. Een [gepartitioneerde service](service-fabric-concepts-partitioning.md) heeft de status van de knoop punten in het cluster verder onderverdeeld (en toegang tot patronen met die status).

In het volgende diagram ziet u de relatie tussen toepassingen en service-exemplaren, partities en replica's.

![Partities en replica's in een service][cluster-application-instances]

> [!TIP]
> U kunt de indeling van toepassingen in een cluster weer geven met behulp van het hulp programma Service Fabric Explorer beschikbaar op http:// &lt; yourclusteraddress &gt; : 19080/Explorer. Zie [uw cluster visualiseren met Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)voor meer informatie.
> 
> 


## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [schaal baarheid van toepassingen](service-fabric-concepts-scalability.md).
- Meer informatie over service [status](service-fabric-concepts-state.md), [partitionering](service-fabric-concepts-partitioning.md)en [Beschik baarheid](service-fabric-availability-services.md).
- Meer informatie over hoe toepassingen en services worden gedefinieerd in [toepassings-en service manifesten](service-fabric-application-and-service-manifests.md).
- Met [Application Hosting-modellen](service-fabric-hosting-model.md) wordt de relatie tussen replica's (of exemplaren) van een geïmplementeerd service-en service-hostproces beschreven.

<!--Image references-->
[appmodel-diagram]: ./media/service-fabric-application-model/application-model.png
[cluster-imagestore-apptypes]: ./media/service-fabric-application-model/cluster-imagestore-apptypes.png
[cluster-application-instances]: media/service-fabric-application-model/cluster-application-instances.png


