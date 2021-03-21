---
title: Overzicht van Azure Service Fabric
description: Service Fabric is een platform bestaande uit gedistribueerde systemen, dat wordt gebruikt voor het bouwen van schaalbare, betrouwbare en eenvoudig te beheren microservices.
ms.topic: overview
ms.date: 09/22/2020
ms.custom: contperf-fy21q1
ms.openlocfilehash: b6a14ba3f905fbef3fc3796b616cd594941a3d60
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97028628"
---
# <a name="overview-of-azure-service-fabric"></a>Overzicht van Azure Service Fabric

Azure Service Fabric is een [platform voor gedistribueerde systemen](#container-orchestration) waarmee u gemakkelijk schaalbare en betrouwbare microservices en containers verpakt, implementeert en beheert. Service Fabric biedt ook een oplossing voor de grote uitdaging van het [ontwikkelen en beheren](#application-lifecycle-management) van systeemeigen cloudtoepassingen.

Een belangrijk onderscheid van Service Fabric is de sterke focus op het bouwen van stateful services. U kunt het Service Fabric-[programmeermodel gebruiken](#stateless-and-stateful-microservices) of in containers geplaatste stateful services in elke taal of code uitvoeren. U kunt naast Azure ook overal [Service Fabric clusters maken](#any-os-any-cloud), inclusief Windows Server en Linux on-premises en andere openbare clouds.

![Het Service Fabric-platform biedt levenscyclusbeheer, beschikbaarheid, indeling, programmeermodellen, status en bewaking, ontwikkel-en OPS-hulpprogramma's en automatisch schalen, in Azure, op locatie, in andere clouds en op uw ontwikkelcomputer][Image1]

Op Service Fabric kunnen tegenwoordig talloze Microsoft-services draaien, waaronder Azure SQL Database, Azure Cosmos DB, Cortana, Microsoft Power BI, Microsoft Intune, Azure Event Hubs, Azure IoT Hub, Dynamics 365, Skype voor Bedrijven en vele Azure-kernservices.

## <a name="container-orchestration"></a>Containerindeling

Service Fabric is de [containerindeler](service-fabric-cluster-resource-manager-introduction.md) van Microsoft voor het implementeren en beheren van microservices op een cluster van machines en haalt voordeel uit lessen die zijn geleerd uit het grootschalig gebruik van Microsoft-diensten. Service Fabric kan toepassingen binnen enkele seconden bij hoge dichtheid implementeren met honderden of duizenden toepassingen of containers per computer. U kunt met Service Fabric beide services onderbrengen in processen en services in containers binnen dezelfde toepassing.

[Meer informatie over Service Fabric](service-fabric-content-roadmap.md) basisconcepten, programmeermodellen, toepassing levenscyclus, testen, clusters en statuscontrole.

## <a name="stateless-and-stateful-microservices"></a>Staatloze en stateful microservices

Service Fabric biedt een geavanceerde, lichtgewicht runtime die ondersteuning biedt voor staatloze en stateful microservices. Een belangrijk aspect van Service Fabric is de sterke focus op het bouwen van stateful apparaten, met de Service Fabric [ingebouwde programmeermodellen](service-fabric-choose-framework.md) of met stateful services in containers.

Lees meer over [toepassingsscenario's](service-fabric-application-scenarios.md) die profiteren van stateful Service Fabric-services.

## <a name="application-lifecycle-management"></a>Beheer van toepassingslevenscyclus

Service Fabric biedt ondersteuning voor de volledige levenscyclus van toepassingen en CI/CD van Cloud-toepassingen, waaronder containers: ontwikkeling via implementatie, dagelijkse bewaking, beheer en onderhoud tot uiteindelijk buiten gebruik stellen. Service Fabric is geïntegreerd met CI/CD-hulpprogramma's als [Azure Pipelines](https://www.visualstudio.com/team-services/), [Jenkins](https://jenkins.io/index.html) en [Octopus Deploy](https://octopus.com/), en kan worden gebruikt met elk ander bekend CI/CD-hulpprogramma.

Zie [Toepassingslevenscyclus](service-fabric-application-lifecycle.md) voor meer informatie. Zie [Een uitvoerbare gast implementeren](service-fabric-deploy-existing-app.md) voor meer informatie over het implementeren van bestaande toepassingen naar Service Fabric.

## <a name="any-os-any-cloud"></a>Elk besturingssysteem, elke cloud

U kunt in veel omgevingen clusters voor Service Fabric maken, waaronder [Azure of op locatie](service-fabric-deploy-anywhere.md), op [Windows Server of Linux](service-fabric-linux-windows-differences.md). U kunt zelfs clusters maken in andere openbare clouds. De ontwikkelomgeving in de Service Fabric SDK is identiek aan de productieomgeving, zonder dat er emulators bij betrokken zijn. Met andere woorden, wat u uitvoert op uw lokale ontwikkelcluster, wordt gebruikt op uw clusters in andere omgevingen.

Voor [ontwikkeling onder Windows](service-fabric-get-started.md) is de SDK van Service Fabric .NET geïntegreerd met Visual Studio en PowerShell. Voor [ontwikkeling onder Linux](service-fabric-get-started-linux.md) is de SDK van Service Fabric Java geïntegreerd met Eclipse, en wordt Yeoman gebruikt voor het genereren van sjablonen voor Java, .NET Core en containertoepassingen.

## <a name="compliance"></a>Naleving

Azure Service Fabric Resource Provider is beschikbaar in alle Azure-regio's en voldoet aan alle Azure-nalevingscertificeringen, waaronder: SOC, ISO, PCI DSS, HIPAA en GDPR. Zie [nalevingsaanbod van Microsoft](https://www.microsoft.com/trustcenter/compliance/complianceofferings) voor een volledige lijst.

## <a name="next-steps"></a>Volgende stappen

Maak en implementeer uw eerste Linux-containertoepassing in Azure Service Fabric:

> [!div class="nextstepaction"]
> [Snelstartgids Service Fabric][sf-quickstart]

[Image1]: media/service-fabric-overview/Service-Fabric-Overview.png
[sf-quickstart]: ./service-fabric-quickstart-dotnet.md
