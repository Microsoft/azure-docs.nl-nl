---
title: Overzicht van Service Fabric en containers
description: Een overzicht van Service Fabric en het gebruik van containers voor het implementeren van micro service-toepassingen. Dit artikel bevat een overzicht van de manier waarop containers kunnen worden gebruikt en de beschik bare mogelijkheden in Service Fabric.
ms.topic: conceptual
ms.date: 7/9/2020
ms.openlocfilehash: cd0ec7dd2247fdd791df362fa34542178c17df4d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87091654"
---
# <a name="service-fabric-and-containers"></a>Service Fabric en containers

## <a name="introduction"></a>Introductie

Azure Service Fabric is een platform voor gedistribueerde systemen waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert.

Service Fabric is de [container Orchestrator](service-fabric-cluster-resource-manager-introduction.md) van micro soft voor het implementeren van microservices in een cluster van machines. Service Fabric voor delen van de lessen die zijn geleerd tijdens de jaren waarin Services bij micro soft worden uitgevoerd op grote schaal.

Microservices kunnen op tal van manieren worden ontwikkeld met behulp van de [Service Fabric-programmeermodellen](service-fabric-choose-framework.md), [ASP.NET Core](service-fabric-reliable-services-communication-aspnetcore.md), voor het implementeren van [code naar keuze](service-fabric-guest-executables-introduction.md). Als u alleen [containers wilt implementeren en beheren](service-fabric-containers-overview.md), is service Fabric ook een fantastische keuze.

Standaard worden deze services door Service Fabric geïmplementeerd en geactiveerd als processen. Processen bieden de snelste activering en het hoogste dichtheids gebruik van de resources in een cluster. Service Fabric kunt ook services implementeren in container installatie kopieën. U kunt ook services in processen en services in containers combi neren in dezelfde toepassing.

Als u wilt beginnen met het zoeken naar en uitchecken van containers op Service Fabric, kunt u een Snelstartgids, zelf studie of voor beeld proberen:  

[Snelstartgids: een Linux-container toepassing implementeren in Service Fabric](service-fabric-quickstart-containers-linux.md)  
[Snelstartgids: een Windows-container toepassing implementeren op Service Fabric](service-fabric-quickstart-containers.md)  
[Een bestaande .NET-app in een container plaatsen](service-fabric-host-app-in-a-container.md)  
[Voorbeelden van Service Fabric-containers](https://azure.microsoft.com/resources/samples/service-fabric-containers/)  

## <a name="what-are-containers"></a>Wat zijn containers

Containers bieden een oplossing voor het probleem van het uitvoeren van toepassingen die op een betrouw bare manier in verschillende computer omgevingen worden uitgevoerd, door een onveranderlijke omgeving voor de toepassing in te stellen. Containers verpakken een toepassing en alle bijbehorende afhankelijkheden, zoals bibliotheken en configuratie bestanden, in een eigen geïsoleerde doos met alles wat u nodig hebt om de software uit te voeren in de container. Overal waar de container wordt uitgevoerd, heeft de toepassing hierin altijd alles wat nodig is om uit te voeren, zoals de juiste versies van de bijbehorende afhankelijke Bibliotheken, eventuele configuratie bestanden en andere items die nodig zijn om uit te voeren.

Containers worden direct op de kernel uitgevoerd en hebben een geïsoleerde weer gave van het bestands systeem en andere bronnen. Een toepassing in een container heeft geen kennis van andere toepassingen of processen buiten de container. Elke toepassing en de runtime, afhankelijkheden en systeem bibliotheken worden uitgevoerd binnen een container met volledige, persoonlijke toegang tot de eigen geïsoleerde weer gave van het besturings systeem van de container. Daarnaast is het eenvoudig om alle afhankelijkheden van uw toepassing te bieden die nodig zijn om te worden uitgevoerd in verschillende omgevingen, beveiliging en bron isolatie zijn belang rijke voor delen van het gebruik van containers met Service Fabric, waardoor Services in een proces op een andere manier worden uitgevoerd.

In vergelijking met virtuele machines hebben containers de volgende voor delen:

* **Klein**: containers gebruiken één opslag ruimte en laag versies en updates om de efficiëntie te verbeteren.
* **Snel**: containers hoeven niet een volledig besturings systeem op te starten, zodat ze veel sneller kunnen beginnen, meestal in seconden.
* **Portabiliteit**: een gecontainerde toepassings installatie kopie kan worden geporteerd om te worden uitgevoerd in de Cloud, on-premises, in virtuele machines of rechtstreeks op fysieke machines.
* **Resource governance**: een container kan de fysieke bronnen beperken die deze kan gebruiken op de host.

### <a name="container-types-and-supported-environments"></a>Container typen en ondersteunde omgevingen

Service Fabric ondersteunt containers in zowel Linux als Windows, en ondersteunt de isolatie modus voor Hyper-V in Windows.

#### <a name="docker-containers-on-linux"></a>Docker-containers in Linux

Docker biedt Api's voor het maken en beheren van containers boven op Linux-kernel-containers. Docker hub biedt een centrale opslag plaats voor het opslaan en ophalen van container installatie kopieën.
Zie [uw eerste service Fabric-container toepassing maken in Linux](service-fabric-get-started-containers-linux.md)voor een zelf studie op basis van Linux.

#### <a name="windows-server-containers"></a>Windows Server-containers

Windows Server 2016 en hoger bieden twee verschillende typen containers die verschillen per isolatie niveau. Windows Server-containers en docker-containers zijn vergelijkbaar omdat beide een naam ruimte en een bestandssysteem isolatie hebben, terwijl de kernel wordt gedeeld met de host waarop ze worden uitgevoerd. In Linux is deze isolatie traditioneel gegeven door cgroups en naam ruimten, en Windows Server-containers gedragen zich op dezelfde manier.

Windows-containers met Hyper-V-ondersteuning bieden meer isolatie en beveiliging omdat geen enkele container de kernel van het besturings systeem deelt met een andere container of met de host. Met dit hogere beveiligings isolatie niveau worden voor Hyper-V ingeschakelde containers mogelijk vijandige scenario's met meerdere tenants bedoeld.
Zie [uw eerste service Fabric-container toepassing maken in Windows](service-fabric-get-started-containers.md)voor een zelf studie op basis van Windows.

In de volgende afbeelding ziet u de verschillende soorten virtualisatie-en isolatie niveaus die beschikbaar zijn.
![Service Fabric-platform][Image1]

## <a name="scenarios-for-using-containers"></a>Scenario's voor het gebruik van containers

Hier volgen enkele typische voor beelden waarin een container een goede keuze is:

* **IIS lift en Shift**: u kunt een bestaande [ASP.NET MVC](https://www.asp.net/mvc) -app in een container plaatsen in plaats van deze te migreren naar ASP.net core. Deze ASP.NET MVC-apps zijn afhankelijk van Internet Information Services (IIS). U kunt deze toepassingen inpakken in container installatie kopieën van de voorgemaakte IIS-installatie kopie en implementeren met Service Fabric. Zie [container installatie kopieën op Windows Server](/virtualization/windowscontainers/quick-start/quick-start-windows-server) voor meer informatie over Windows-containers.

* **Containers en service Fabric micro Services combi neren**: gebruik een bestaande container installatie kopie voor een deel van uw toepassing. U kunt bijvoorbeeld de [NGINX-container](https://hub.docker.com/_/nginx/) voor de web-front-end van uw toepassing en stateful Services gebruiken voor een meer intensieve berekening van de back-end.

* De **impact van ' ruislijke neighbors ' verminderen**: u kunt de resource governance-capaciteit van containers gebruiken om de resources te beperken die door een service op een host worden gebruikt. Als Services veel bronnen kunnen verbruiken en invloed hebben op de prestaties van anderen (zoals een langlopende, query-achtige bewerking), overweeg dan om deze services te plaatsen in containers met resource beheer.

## <a name="service-fabric-support-for-containers"></a>Service Fabric-ondersteuning voor containers

Service Fabric ondersteunt de implementatie van docker-containers op Linux en Windows Server-containers op Windows Server 2016 en hoger, samen met ondersteuning voor de isolatie modus van Hyper-V.

> [!NOTE]
> Containers worden niet ondersteund op een lokaal Service Fabric cluster met één knoop punt (geen Linux-clusters op OneBox of Windows-clusters op lokale Service Fabric-installaties).

Service Fabric biedt een [toepassings model](service-fabric-application-model.md) waarin een container een toepassingshost vertegenwoordigt waarin meerdere service replica's worden geplaatst. Service Fabric biedt ook ondersteuning voor een [uitvoerbaar gast scenario](service-fabric-guest-executables-introduction.md) waarin u geen gebruik maakt van de ingebouwde service Fabric programmeer modellen, maar in plaats daarvan een bestaande toepassing verpakken, geschreven met behulp van een wille keurige taal of elk Framework, binnen een container. Dit scenario is gebruikelijk voor containers.

U kunt ook [service Fabric Services in een container](service-fabric-services-inside-containers.md)uitvoeren. Ondersteuning voor het uitvoeren van Service Fabric services binnen containers is momenteel beperkt.

Service Fabric biedt verschillende container mogelijkheden die u helpen bij het bouwen van toepassingen die zijn samengesteld uit container micro Services, zoals:

* Implementatie en activering van container installatie kopie.
* Resource beheer, inclusief het instellen van resource waarden standaard op Azure-clusters.
* Verificatie van opslag plaats.
* Poort toewijzing van container poort naar host.
* Detectie en communicatie van container-naar-container.
* De mogelijkheid om omgevings variabelen te configureren en in te stellen.
* De mogelijkheid om beveiligings referenties in te stellen op de container.
* Een keuze uit verschillende netwerk modi voor containers.

Voor een uitgebreid overzicht van container ondersteuning op Azure, zoals het maken van een Kubernetes-cluster met de Azure Kubernetes-service, het maken van een persoonlijk docker-REGI ster in Azure Container Registry en meer, Zie [Azure voor containers](../containers/index.yml).

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd over de ondersteunings Service Fabric voor het uitvoeren van containers. We gaan nu de voor beelden van de functies bekijken om u te laten zien hoe u deze kunt gebruiken.

[Uw eerste Service Fabric-containertoepassing maken in Linux](service-fabric-get-started-containers-linux.md)  
[Uw eerste Service Fabric-containertoepassing maken in Windows](service-fabric-get-started-containers.md)  
[Meer informatie over Windows-containers](/virtualization/windowscontainers/about/)

[Image1]: media/service-fabric-containers/Service-Fabric-Types-of-Isolation.png
