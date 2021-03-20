---
title: Aanbevolen procedures voor Azure Service Fabric-bewaking
description: Aanbevolen procedures en ontwerp overwegingen voor het bewaken van clusters en toepassingen met Azure Service Fabric.
author: peterpogorski
ms.topic: conceptual
ms.date: 01/23/2019
ms.author: pepogors
ms.openlocfilehash: a7b1c1b3fc3196557b862c488ee01af8b8e1f04f
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86529247"
---
# <a name="monitoring-and-diagnostic-best-practices-for-azure-service-fabric"></a>Aanbevolen procedures voor het bewaken en diagnosticeren voor Azure Service Fabric

[Bewaking en diagnose](./service-fabric-diagnostics-overview.md) zijn essentieel voor het ontwikkelen, testen en implementeren van werk belastingen in elke cloud omgeving. U kunt bijvoorbeeld bijhouden hoe uw toepassingen worden gebruikt, de acties die worden uitgevoerd door het Service Fabric platform, het resource gebruik met prestatie meter items en de algehele status van uw cluster. U kunt deze informatie gebruiken om problemen vast te stellen en te verhelpen en te voor komen dat ze in de toekomst optreden.

## <a name="application-monitoring"></a>Toepassingsbewaking

Met toepassings bewaking wordt bijgehouden hoe onderdelen en onderdelen van uw toepassing worden gebruikt. Bewaak uw toepassingen om te controleren of er problemen zijn die van invloed zijn op uw gebruikers. Toepassings bewaking is de verantwoordelijkheid van het ontwikkelen van de toepassing en de bijbehorende services, omdat deze uniek is voor de bedrijfs logica van uw toepassing. Het is raadzaam toepassings bewaking in te stellen met [Application Insights](./service-fabric-tutorial-monitoring-aspnet.md), het toepassings bewakings programma van Azure.

## <a name="cluster-monitoring"></a>Clusterbewaking

Een van de doelen van Service Fabric is om toepassingen te maken voor hardwarefouten. Dit doel wordt bereikt door de mogelijkheid van de systeem services van het platform om infrastructuur problemen en snelle failover-workloads te detecteren naar andere knoop punten in het cluster. Maar wat gebeurt er als de systeem services zelf problemen ondervinden? Of als u een werk belasting probeert te implementeren of verplaatsen, worden de regels voor het plaatsen van services geschonden? Service Fabric voorziet in diagnostische gegevens voor deze en andere problemen om ervoor te zorgen dat u op de hoogte bent van de manier waarop het Service Fabric platform communiceert met uw toepassingen, services, containers en knoop punten.

Voor Windows-clusters is het raadzaam om cluster bewaking in te stellen met [Diagnostische agent](./service-fabric-diagnostics-event-aggregation-wad.md) en [Azure monitor logboeken](./service-fabric-diagnostics-oms-setup.md).

Voor Linux-clusters is Azure Monitor-logboeken ook het aanbevolen hulp programma voor het controleren van het Azure-platform en de infra structuur. De diagnostische gegevens van Linux-platform vereisen een andere configuratie, zoals vermeld in [service Fabric Linux-cluster gebeurtenissen in syslog](./service-fabric-diagnostics-oms-syslog.md).

## <a name="infrastructure-monitoring"></a>Infrastructuur bewaken

[Azure monitor logboeken](./service-fabric-diagnostics-oms-agent.md) worden aanbevolen voor het bewaken van gebeurtenissen op cluster niveau. Zodra u de Log Analytics-agent met uw werk ruimte hebt geconfigureerd zoals beschreven in de vorige koppeling, kunt u prestatie gegevens verzamelen, zoals CPU-gebruik, .NET-prestatie meter items, zoals CPU-gebruik op proces niveau, Service Fabric prestatie meter items, zoals het aantal uitzonde ringen van een betrouw bare service, en metrische gegevens over containers, zoals CPU-gebruik.  U moet container logboeken schrijven naar stdout of stderr, zodat deze beschikbaar zijn in Azure Monitor Logboeken.

## <a name="watchdogs"></a>Watchdog

Over het algemeen is een watchdog een afzonderlijke service die de status controleert en laadt tussen services, eind punten pingt en onverwachte status gebeurtenissen in het cluster rapporteert. Dit kan helpen om fouten te voor komen die niet worden gedetecteerd op basis van de prestaties van één service. Watchdog zijn ook een goede plaats voor het hosten van code voor het uitvoeren van een herstel bewerking waarvoor geen interactie van de gebruiker is vereist, zoals het opschonen van logboek bestanden in de opslag op bepaalde tijds intervallen. Bekijk een voor beeld van een watchdog-service-implementatie in  [service Fabric Linux-cluster gebeurtenissen in syslog](https://github.com/Azure-Samples/service-fabric-watchdog-service).

## <a name="next-steps"></a>Volgende stappen

* Ga aan de slag met het instrumenteren van uw toepassingen: [toepassings niveau gebeurtenis en logboek generatie](service-fabric-diagnostics-event-generation-app.md).
* Door loop de stappen om Application Insights voor uw toepassing in te stellen met behulp van [een ASP.net core-toepassing in service Fabric te controleren en te diagnosticeren](service-fabric-tutorial-monitoring-aspnet.md).
* Meer informatie over het bewaken van het platform en de gebeurtenissen Service Fabric biedt u de volgende mogelijkheden: [gebeurtenis op platform niveau en logboek generatie](service-fabric-diagnostics-event-generation-infra.md).
* Integratie van Azure Monitor logboeken configureren met Service Fabric: [Azure monitor logboeken instellen voor een cluster](service-fabric-diagnostics-oms-setup.md)
* Meer informatie over het instellen van Azure Monitor-logboeken voor controle containers: [bewaking en diagnose voor Windows-containers in Azure service Fabric](service-fabric-tutorial-monitoring-wincontainers.md).
* Bekijk voor beelden van diagnostische problemen en oplossingen met Service Fabric: [algemene scenario's diagnosticeren](service-fabric-diagnostics-common-scenarios.md)
* Meer informatie over algemene controle aanbevelingen voor Azure-resources: [Aanbevolen procedures-bewaking en diagnose](/azure/architecture/best-practices/monitoring).
