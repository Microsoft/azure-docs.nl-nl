---
title: Een bestaand uitvoerbaar bestand verpakken naar Azure Service Fabric
description: Meer informatie over het verpakken van een bestaande toepassing als een uitvoerbaar gast bestand, zodat het kan worden geïmplementeerd in een Service Fabric cluster.
ms.topic: conceptual
ms.date: 03/15/2018
ms.openlocfilehash: 8b808d092001196a4d2150e44d508e031db95554
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96017743"
---
# <a name="deploy-an-existing-executable-to-service-fabric"></a>Een bestaand uitvoerbaar bestand implementeren naar Service Fabric
U kunt elk type code, zoals Node.js, Java of C++, uitvoeren in azure Service Fabric als een service. Service Fabric verwijst naar deze typen services als uitvoer bare gast bestanden.

Uitvoer bare gast bestanden worden behandeld door Service Fabric zoals stateless Services. Als gevolg hiervan worden ze op knoop punten in een cluster geplaatst, op basis van Beschik baarheid en andere metrische gegevens. In dit artikel wordt beschreven hoe u een uitvoerbaar gast bestand verpakken en implementeert in een Service Fabric cluster met behulp van Visual Studio of een opdracht regel programma.

## <a name="benefits-of-running-a-guest-executable-in-service-fabric"></a>Voor delen van het uitvoeren van een gast bestand in Service Fabric
Er zijn verschillende voor delen voor het uitvoeren van een uitvoerbaar gast bestand in een Service Fabric cluster:

* Hoge beschikbaarheid. Toepassingen die in Service Fabric worden uitgevoerd, worden Maxi maal beschikbaar gemaakt. Service Fabric zorgt ervoor dat exemplaren van een toepassing worden uitgevoerd.
* Statuscontrole. Service Fabric status bewaking detecteert of een toepassing wordt uitgevoerd en bevat diagnostische gegevens als er een fout optreedt.   
* Beheer van toepassings levenscyclus. Naast het bieden van upgrades zonder downtime, biedt Service Fabric automatisch terugdraaien naar de vorige versie als er een slechte status gebeurtenis is gerapporteerd tijdens een upgrade.    
* Veebezetting. U kunt meerdere toepassingen uitvoeren in een cluster, waardoor de nood zaak van elke toepassing niet op eigen hardware hoeft te worden uitgevoerd.
* Vind baarheid: met behulp van REST kunt u de Service Fabric naamgevings service aanroepen om andere services in het cluster te vinden. 

## <a name="samples"></a>Voorbeelden
* [Voor beeld voor het verpakken en implementeren van een uitvoerbaar gast bestand](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started)
* [Voor beeld van twee gast uitvoer bare bestanden (C# en nodejs) die communiceren via de naamgevings service met REST](https://github.com/Azure-Samples/service-fabric-dotnet-containers)

## <a name="overview-of-application-and-service-manifest-files"></a>Overzicht van toepassings-en service manifest bestanden
Als onderdeel van het implementeren van een uitvoerbaar gast bestand, is het handig om inzicht te krijgen in het model van Service Fabric-verpakking en-implementatie zoals beschreven in het [toepassings model](service-fabric-application-model.md). Het Service Fabric-verpakkende model is afhankelijk van twee XML-bestanden: de toepassings-en service manifesten. De schema definitie voor de ApplicationManifest.xml-en ServiceManifest.xml-bestanden wordt geïnstalleerd met de SDK van Service Fabric in *C:\Program Files\Microsoft SDKs\Service Fabric\schemas\ServiceFabricServiceModel.XSD*.

* **Toepassings manifest** Het toepassings manifest wordt gebruikt om de toepassing te beschrijven. Hierin worden de services weer gegeven die het samen stellen en andere para meters die worden gebruikt om te definiëren hoe een of meer services moeten worden geïmplementeerd, zoals het aantal exemplaren.

  In Service Fabric is een toepassing een eenheid van implementatie en upgrade. Een toepassing kan worden bijgewerkt als één eenheid waar mogelijke fouten en mogelijke terugdraai bewerkingen worden beheerd. Service Fabric garandeert dat het upgrade proces is geslaagd of dat als de upgrade mislukt, de toepassing niet in een onbekende of onstabiele staat blijft.
* **Service manifest** In het service manifest worden de onderdelen van een service beschreven. Het bevat gegevens, zoals de naam en het type van de service en de code en configuratie. Het service manifest bevat ook enkele aanvullende para meters die kunnen worden gebruikt om de service te configureren zodra deze is geïmplementeerd.

## <a name="application-package-file-structure"></a>Bestands structuur toepassings pakket
Als u een toepassing wilt implementeren op Service Fabric, moet de toepassing een vooraf gedefinieerde mapstructuur volgen. Hier volgt een voor beeld van deze structuur.

```
|-- ApplicationPackageRoot
    |-- GuestService1Pkg
        |-- Code
            |-- existingapp.exe
        |-- Config
            |-- Settings.xml
        |-- Data
        |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```

De Application Package root bevat het ApplicationManifest.xml bestand dat de toepassing definieert. Een submap voor elke service die in de toepassing wordt opgenomen, wordt gebruikt voor alle artefacten die de service nodig heeft. Deze submappen zijn de ServiceManifest.xml en meestal het volgende:

* *Code*. Deze map bevat de service code.
* *Configuratie*. Deze map bevat een Settings.xml bestand (en andere bestanden indien nodig) die de service tijdens runtime kan openen om specifieke configuratie-instellingen op te halen.
* *Gegevens*. Dit is een extra Directory voor het opslaan van aanvullende lokale gegevens die de service mogelijk nodig heeft. Gegevens moeten worden gebruikt om alleen tijdelijke gegevens op te slaan. Service Fabric kopieert of repliceert geen wijzigingen naar de gegevensdirectory als de service opnieuw moet worden gevonden (bijvoorbeeld tijdens failover).

> [!NOTE]
> `config` `data` Als u deze niet nodig hebt, hoeft u de-en-mappen niet te maken.
>
>

## <a name="next-steps"></a>Volgende stappen
Raadpleeg de volgende artikelen voor gerelateerde informatie en taken.
* [Een toepassing implementeren die door een gast kan worden uitgevoerd](service-fabric-deploy-existing-app.md)
* [Meerdere toepassingen implementeren die door gasten kunnen worden uitgevoerd](./service-fabric-deploy-existing-app.md)
* [Uw eerste uitvoer bare gast toepassing maken met Visual Studio](quickstart-guest-app.md)
* [Voor beeld voor het verpakken en implementeren van een uitvoerbaar gast bestand](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started), met inbegrip van een koppeling naar de Prerelease van het verpakkings programma
* [Voor beeld van twee gast uitvoer bare bestanden (C# en nodejs) die communiceren via de naamgevings service met REST](https://github.com/Azure-Samples/service-fabric-containers)
