---
title: Verzamelen van gebeurtenissen met Linux-Azure Diagnostics
description: Meer informatie over het samen voegen en verzamelen van gebeurtenissen met LAD voor bewaking en diagnose van Azure Service Fabric-clusters.
ms.topic: conceptual
ms.date: 2/25/2019
ms.openlocfilehash: bd5efc6b661bf25ce7946cda0bcfa01be4c351a7
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105628931"
---
# <a name="event-aggregation-and-collection-using-linux-azure-diagnostics"></a>Gebeurtenis aggregatie en verzameling met Linux Azure Diagnostics
> [!div class="op_single_selector"]
> * [Windows](service-fabric-diagnostics-event-aggregation-wad.md)
> * [Linux](service-fabric-diagnostics-event-aggregation-lad.md)
>
>

Wanneer u een Azure Service Fabric-cluster uitvoert, is het een goed idee om de logboeken te verzamelen van alle knoop punten op een centrale locatie. Met de logboeken op een centrale locatie kunt u problemen in uw cluster analyseren en oplossen, of problemen in de toepassingen en services die in dat cluster worden uitgevoerd.

Een manier om logboeken te uploaden en te verzamelen, is door de Linux Azure Diagnostics-extensie (LAD) te gebruiken, waarmee logboeken naar Azure Storage worden geüpload en ook de optie is voor het verzenden van logboeken naar Azure-toepassing inzichten of Event Hubs. U kunt ook een extern proces gebruiken om de gebeurtenissen uit de opslag te lezen en deze te plaatsen in een analyse platform product, zoals [Azure monitor logboeken](./service-fabric-diagnostics-oms-setup.md) of een andere oplossing voor het parseren van Logboeken.

## <a name="log-and-event-sources"></a>Logboek-en gebeurtenis bronnen

### <a name="service-fabric-platform-events"></a>Service Fabric-platform gebeurtenissen
Service Fabric verzendt enkele out-of-the-box-logboeken via [LTTng](https://lttng.org), met inbegrip van operationele gebeurtenissen of runtime-gebeurtenissen. Deze logboeken worden opgeslagen op de locatie die wordt opgegeven in de Resource Manager-sjabloon van het cluster. Om de details van het opslag account op te halen of in te stellen, zoekt u naar de tag **AzureTableWinFabETWQueryable** en zoekt u naar **StoreConnectionString**.

### <a name="application-events"></a>Toepassings gebeurtenissen
 Gebeurtenissen die worden verzonden vanuit de code van uw toepassingen en services zoals opgegeven door u bij het instrumenteren van uw software. U kunt elke registratie oplossing gebruiken waarmee logboek bestanden op basis van tekst worden geschreven, bijvoorbeeld LTTng. Zie de LTTng-documentatie over het traceren van uw toepassing voor meer informatie.

[Services in een installatie van een lokale machine controleren en diagnosticeren](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally-linux.md).

## <a name="deploy-the-diagnostics-extension"></a>De diagnostische uitbrei ding implementeren
De eerste stap bij het verzamelen van Logboeken is het implementeren van de diagnostische uitbrei ding op elke virtuele machine in het Service Fabric cluster. De diagnostische uitbrei ding verzamelt logboeken op elke virtuele machine en uploadt deze naar het opslag account dat u opgeeft. 

Als u de diagnostische uitbrei ding voor de virtuele machines in het cluster wilt implementeren als onderdeel van het maken van een cluster, stelt u **Diagnostische gegevens** in **op aan**. Nadat u het cluster hebt gemaakt, kunt u deze instelling niet wijzigen met behulp van de portal, zodat u de gewenste wijzigingen moet aanbrengen in de Resource Manager-sjabloon.

Hiermee configureert u de LAD-agent om de opgegeven logboek bestanden te bewaken. Wanneer een nieuwe regel aan het bestand wordt toegevoegd, wordt er een syslog-item gemaakt dat wordt verzonden naar de opslag (tabel) die u hebt opgegeven.


## <a name="next-steps"></a>Volgende stappen

1. Zie [LTTng-documentatie](https://lttng.org/docs) en [Lad gebruiken](../virtual-machines/extensions/diagnostics-linux.md)voor meer informatie over de gebeurtenissen die u moet onderzoeken tijdens het oplossen van problemen.
2. [Stel de log Analytics agent](service-fabric-diagnostics-event-analysis-oms.md) in voor het verzamelen van metrische gegevens, het controleren van containers die zijn geïmplementeerd in uw cluster en het visualiseren van uw logboeken 
