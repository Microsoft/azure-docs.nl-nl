---
title: Voor beelden van Azure Service Fabric-toepassings manifest
description: Meer informatie over het configureren van de toepassings-en service manifest instellingen voor een Service Fabric-toepassing.
author: peterpogorski
ms.topic: conceptual
ms.date: 06/11/2018
ms.author: pepogors
ms.openlocfilehash: 6f44d2ddaac11545a3a3dc52fd20e02263c6ad67
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "75451645"
---
# <a name="service-fabric-application-and-service-manifest-examples"></a>Voor beelden van Service Fabric-toepassingen en-services
Deze sectie bevat voor beelden van toepassings-en service manifesten. Deze voor beelden zijn niet bedoeld voor het weer geven van belang rijke scenario's, maar voor het weer geven van de verschillende instellingen die beschikbaar zijn en hoe u deze kunt gebruiken. 

Hier volgt een index van de weer gegeven functies en de manifesten waarvan ze deel uitmaken.

|Functie|Manifest|
|---|---|
|[Resourcebeheer](service-fabric-resource-governance.md)|[Toepassings manifest reliable Services](service-fabric-manifest-example-reliable-services-app.md#application-manifest), [container toepassings manifest](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Een service uitvoeren als een lokaal beheerders account](service-fabric-application-runas-security.md)|[Reliable Services toepassings manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Een standaard beleid Toep assen op alle service code pakketten](service-fabric-application-runas-security.md#apply-a-default-policy-to-all-service-code-packages)|[Reliable Services toepassings manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Gebruikers-en groeps-principals maken](service-fabric-application-runas-security.md)|[Reliable Services toepassings manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|een gegevens pakket delen tussen service-exemplaren|[Reliable Services toepassings manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Service-eind punten onderdrukken](service-fabric-service-manifest-resources.md#overriding-endpoints-in-servicemanifestxml)|[Reliable Services toepassings manifest](service-fabric-manifest-example-reliable-services-app.md#application-manifest)|
|[Een script uitvoeren bij het starten van de service](service-fabric-run-script-at-service-startup.md)|[VotingWeb-service manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Een HTTPS-eind punt definiëren](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md#define-an-https-endpoint-in-the-service-manifest)|[VotingWeb-service manifest](service-fabric-manifest-example-reliable-services-app.md#votingweb-service-manifest)|
|[Een configuratie pakket declareren](service-fabric-application-and-service-manifests.md)|[VotingData-service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Een gegevens pakket declareren](service-fabric-application-and-service-manifests.md)|[VotingData-service manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|[Omgevings variabelen negeren](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Manifest van container toepassing](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Poort-naar-host-toewijzing van container configureren](service-fabric-get-started-containers.md#configure-container-port-to-host-port-mapping-and-container-to-container-discovery)| [Manifest van container toepassing](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Verificatie containerregister configureren](service-fabric-get-started-containers.md#configure-container-repository-authentication)|[Manifest van container toepassing](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Isolatie modus instellen](service-fabric-get-started-containers.md#configure-isolation-mode)|[Manifest van container toepassing](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Specifieke container installatie kopieën voor het besturings systeem opgeven](service-fabric-get-started-containers.md#specify-os-build-specific-container-images)|[Manifest van container toepassing](service-fabric-manifest-example-container-app.md#application-manifest)|
|[Omgevingsvariabelen instellen](service-fabric-get-started-containers.md#configure-and-set-environment-variables)|[Container FrontEndService service-manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest), [container BackEndService-service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|
|[Een eind punt configureren](service-fabric-get-started-containers.md#configure-communication)|[Container FrontEndService service](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)manifest, [container BackEndService service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest), [VotingData service-manifest](service-fabric-manifest-example-reliable-services-app.md#votingdata-service-manifest)|
|opdrachten door geven aan de container|[Container FrontEndService-service manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Een certificaat importeren in een container](service-fabric-securing-containers.md)|[Container FrontEndService-service manifest](service-fabric-manifest-example-container-app.md#frontendservice-service-manifest)|
|[Volume stuur programma configureren](service-fabric-containers-volume-logging-drivers.md)|[Container BackEndService-service manifest](service-fabric-manifest-example-container-app.md#backendservice-service-manifest)|

