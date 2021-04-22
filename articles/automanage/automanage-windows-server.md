---
title: Azure Automanage voor Windows Server
description: Meer informatie over de Azure Automanage voor best practices voor virtuele machines voor services die automatisch worden onboarding uitgevoerd en geconfigureerd voor Windows Server-machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 98d91356b55be015b2c1b73787dad0bb7d8fd424
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863007"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---windows-server"></a>Azure Automanage voor best practices voor virtuele machines - Windows Server

Deze Azure-services worden automatisch voor u onboarding uitgevoerd wanneer u Automatisch beheren gebruikt voor virtuele machines (VM's) op een Windows Server-VM. Ze zijn essentieel voor onze whitepaper met best practices, die u kunt vinden in onze [Cloud Adoption Framework.](/azure/cloud-adoption-framework/manage/azure-server-management)

Voor al deze services gaan we automatisch onboarden, automatisch configureren, controleren op drift en herstellen als er drift wordt gedetecteerd. Zie voor meer informatie over dit proces [Azure Automanage voor virtuele machines.](automanage-virtual-machines.md)

## <a name="supported-windows-server-versions"></a>Ondersteunde Windows Server-versies

Automanage ondersteunt de volgende Windows Server-versies:

- Windows Server 2012/R2
- Windows Server 2016
- Windows Server 2019
- Windows Server 2019 Azure Edition

## <a name="participating-services"></a>Deelnemende services

|Service    |Beschrijving    |Omgevingen ondersteund<sup>1</sup>    |Ondersteunde voorkeuren<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[VM Insights-bewaking](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor voor VM's bewaakt de prestaties en status van uw virtuele machines, inclusief de processen die worden uitgevoerd en afhankelijk zijn van andere resources. [Meer](../azure-monitor/vm/vminsights-overview.md) informatie.    |Productie    |No    |
|[Een back-up maken](https://docs.microsoft.com/azure/backup/backup-overview)    |Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. [Meer](../backup/backup-azure-vms-introduction.md) informatie. De kosten zijn gebaseerd op het aantal en de grootte van de VM's die worden beveiligd. [Meer](https://azure.microsoft.com/pricing/details/backup/) informatie.    |Productie    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center is een geïntegreerd beveiligingsbeheersysteem voor infrastructuur dat de beveiligingsstatus van uw datacenters verbetert en geavanceerde beveiliging tegen bedreigingen biedt voor uw hybride workloads in de cloud. [Meer](../security-center/security-center-introduction.md) informatie.  Met Automanage configureert u het abonnement waarin uw VM zich bevindt in de gratis laag van Azure Security Center. Als uw abonnement al is onboarding voor Azure Security Center, wordt het abonnement niet opnieuw geconfigureerd door Automanage.    |Productie, Dev/Test    |No    |
|[Microsoft Antimalware](https://docs.microsoft.com/azure/security/fundamentals/antimalware)    |Microsoft Antimalware voor Azure is een gratis realtime-beveiliging waarmee virussen, spyware en andere schadelijke software kunnen worden identificeren en verwijderd. Er worden waarschuwingen gegenereerd wanneer bekende schadelijke of ongewenste software zichzelf probeert te installeren of uit te voeren op uw Azure-systemen. [Meer](../security/fundamentals/antimalware.md) informatie. |Productie, Dev/Test    |Yes    |
|[Updatebeheer](https://docs.microsoft.com/azure/automation/update-management/overview)    |U kunt deze Updatebeheer in Azure Automation besturingssysteemupdates voor uw virtuele machines te beheren. U kunt snel de status van beschikbare updates op alle agentmachines beoordelen en het installatieproces van vereiste updates voor servers beheren. [Meer](../automation/update-management/overview.md) informatie.    |Productie, Dev/Test    |No    |
|[Wijzigingen bijhouden & inventaris](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Wijzigingen bijhouden en Inventaris combineren functies voor het bijhouden van wijzigingen en inventaris, zodat u wijzigingen in de infrastructuur van virtuele machines en servers kunt bijhouden. De service ondersteunt het bijhouden van wijzigingen in services, daemons-software, register en bestanden in uw omgeving, om u te helpen bij het diagnosticeren van ongewenste wijzigingen en het geven van waarschuwingen. Met inventarisondersteuning kunt u in-guest resources opvragen voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items.  [Meer](../automation/change-tracking/overview.md) informatie.    |Productie, Dev/Test    |No    |
|[Azure-gastconfiguratie](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration) | Gastconfiguratiebeleid wordt gebruikt om de configuratie te controleren en te rapporteren over de compatibiliteit van de machine. De Automanage-service installeert de [Windows-beveiligingsbasislijnen met](/windows/security/threat-protection/windows-security-baselines) behulp van de extensie Gastconfiguratie. Voor Windows-computers past de gastconfiguratieservice automatisch de basislijninstellingen opnieuw toe als deze niet meer voldoen. [Meer](../governance/policy/concepts/guest-configuration.md) informatie.    |Productie, Dev/Test    |No    |
|[Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)    | Diagnostische gegevens over opstarten is een foutopsporingsfunctie voor virtuele Azure-machines (VM's) waarmee diagnose van opstartfouten van VM's mogelijk is. Met diagnostische gegevens over opstarten kan een gebruiker de status van de VM observeren terwijl deze wordt opgestart door seriële logboekgegevens en schermopnamen te verzamelen. Dit wordt alleen ingeschakeld voor machines die beheerde schijven gebruiken. |Productie, Dev/Test    |No    |
|[Azure Automation Account](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. [Meer](../automation/automation-intro.md) informatie.    |Productie, Dev/Test    |No    |
|[Log Analytics-werkruimte](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor slaat logboekgegevens op in een Log Analytics-werkruimte. Dit is een Azure-resource en een container waarin gegevens worden verzameld, geaggregeerd en als administratieve grens worden gebruikt. [Meer](../azure-monitor/logs/design-logs-deployment.md) informatie.    |Productie, Dev/Test    |No    |


<sup>1</sup> De omgevingsselectie is beschikbaar wanneer u Automatischmanage inschakelen. [Meer](automanage-virtual-machines.md#environment-configuration) informatie. U kunt ook de standaardinstellingen van de omgeving aanpassen en uw eigen voorkeuren instellen binnen de beperkingen voor best practices.


## <a name="next-steps"></a>Volgende stappen

Probeer Automanage in teschakelen voor virtuele machines in Azure Portal.

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)