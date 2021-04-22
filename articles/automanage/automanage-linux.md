---
title: Azure Automanage voor Linux
description: Meer informatie over de Azure Automanage voor best practices voor virtuele machines voor services die automatisch worden onboarding en geconfigureerd voor Linux-machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.collection: linux
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: 0d35963d96ead68c35ca44467119b3c03d0b16c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107863061"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Azure Automanage voor best practices voor virtuele machines - Linux

Deze Azure-services worden automatisch voor u onboarding uitgevoerd wanneer u Automanage gebruikt voor virtuele machines (VM's) op een Linux-VM. Ze zijn essentieel voor onze whitepaper over best practices, die u kunt vinden in onze [Cloud Adoption Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Voor al deze services gaan we automatisch onboarden, automatisch configureren, controleren op drift en herstellen als er drift wordt gedetecteerd. Zie voor meer informatie over dit proces [Azure Automanage virtuele machines.](automanage-virtual-machines.md)

## <a name="supported-linux-distributions-and-versions"></a>Ondersteunde Linux-distributies en -versies

Automanage ondersteunt de volgende Linux-distributies en -versies:

- CentOS 7.3+
- RHEL 7.4+
- Ubuntu 16.04 en 18.04
- SLES 12 (alleen SP3-SP5)

## <a name="participating-services"></a>Deelnemende services

>[!NOTE]
> Microsoft Antimalware wordt momenteel niet ondersteund op linux-VM's.

|Service    |Beschrijving    |Omgevingen ondersteund<sup>1</sup>    |Ondersteunde voorkeuren<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|[VM Insights Monitoring](https://docs.microsoft.com/azure/azure-monitor/vm/vminsights-overview)    |Azure Monitor voor VM's bewaakt de prestaties en status van uw virtuele machines, inclusief de processen die worden uitgevoerd en afhankelijk zijn van andere resources. [Meer](../azure-monitor/vm/vminsights-overview.md) informatie.    |Productie    |No    |
|[Een back-up maken](https://docs.microsoft.com/azure/backup/backup-overview)   |Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. [Meer](../backup/backup-azure-vms-introduction.md) informatie. De kosten zijn gebaseerd op het aantal en de grootte van de VM's die worden beveiligd. [Meer](https://azure.microsoft.com/pricing/details/backup/) informatie.    |Productie    |Yes    |
|[Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-introduction)    |Azure Security Center is een geïntegreerd beveiligingsbeheersysteem voor infrastructuur dat de beveiligingsstatus van uw datacenters verbetert en geavanceerde beveiliging tegen bedreigingen biedt voor uw hybride workloads in de cloud. [Meer](../security-center/security-center-introduction.md) informatie.  Met Automanage configureert u het abonnement waarin uw VM zich bevindt in de gratis laag van Azure Security Center. Als uw abonnement al is onboarding voor Azure Security Center, wordt het abonnement niet opnieuw geconfigureerd door Automanage.    |Productie, Dev/Test    |No    |
|[Updatebeheer](https://docs.microsoft.com/azure/automation/update-management/overview)    |U kunt deze Updatebeheer in Azure Automation om besturingssysteemupdates voor uw virtuele machines te beheren. U kunt snel de status van beschikbare updates op alle agentmachines beoordelen en het proces voor het installeren van vereiste updates voor servers beheren. [Meer](../automation/update-management/overview.md) informatie.    |Productie, Dev/Test    |No    |
|[Wijzigingen bijhouden & inventaris](https://docs.microsoft.com/azure/automation/change-tracking/overview) |Wijzigingen bijhouden en Inventaris combineren functies voor het bijhouden van wijzigingen en inventaris, zodat u wijzigingen in de infrastructuur van virtuele machines en servers kunt bijhouden. De service ondersteunt het bijhouden van wijzigingen in services, daemons-software, register en bestanden in uw omgeving om u te helpen bij het diagnosticeren van ongewenste wijzigingen en het geven van waarschuwingen. Met inventarisondersteuning kunt u in-guest resources opvragen voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items.  [Meer](../automation/change-tracking/overview.md) informatie.    |Productie, Dev/Test    |No    |
|[Azure-gastconfiguratie](https://docs.microsoft.com/azure/governance/policy/concepts/guest-configuration)  | Gastconfiguratiebeleid wordt gebruikt om de configuratie te bewaken en te rapporteren over de compatibiliteit van de machine. De Automanage-service installeert de Azure Linux-basislijn met behulp van de gastconfiguratie-extensie. Voor Linux-machines installeert de gastconfiguratieservice de basislijn in de modus Alleen controle. U kunt zien waar uw VM niet voldoet aan de basislijn, maar niet-naleving wordt niet automatisch herstelt. [Meer](../governance/policy/concepts/guest-configuration.md) informatie.    |Productie, Dev/Test    |No    |
|[Diagnostische gegevens over opstarten](https://docs.microsoft.com/azure/virtual-machines/boot-diagnostics)  | Diagnostische gegevens over opstarten is een foutopsporingsfunctie voor virtuele Azure-machines (VM' die diagnose van opstartfouten van virtuele machines mogelijk maakt). Met diagnostische gegevens over opstarten kan een gebruiker de status van de VM observeren terwijl deze wordt opgestart door seriële logboekgegevens en schermopnamen te verzamelen. Dit wordt alleen ingeschakeld voor machines die beheerde schijven gebruiken. |Productie, Dev/Test    |No    |
|[Azure Automation Account](https://docs.microsoft.com/azure/automation/automation-create-standalone-account)    |Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. [Meer](../automation/automation-intro.md) informatie.    |Productie, Dev/Test    |No    |
|[Log Analytics-werkruimte](https://docs.microsoft.com/azure/azure-monitor/logs/log-analytics-overview) |Azure Monitor slaat logboekgegevens op in een Log Analytics-werkruimte. Dit is een Azure-resource en een container waarin gegevens worden verzameld, geaggregeerd en fungeert als een administratieve grens. [Meer](../azure-monitor/logs/design-logs-deployment.md) informatie.    |Productie, Dev/Test    |No    |


<sup>1</sup> De omgevingsselectie is beschikbaar wanneer u Automanage inschakelen. [Meer](automanage-virtual-machines.md#environment-configuration) informatie. U kunt ook de standaardinstellingen van de omgeving aanpassen en uw eigen voorkeuren instellen binnen de beperkingen voor best practices.


## <a name="next-steps"></a>Volgende stappen

Probeer Automanage in te stellen voor virtuele machines in Azure Portal.

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)