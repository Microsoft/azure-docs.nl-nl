---
title: Azure automanage voor Linux
description: Meer informatie over de aanbevolen procedures voor het beheren van Azure voor virtuele machines voor services die automatisch worden uitgevoerd en geconfigureerd voor Linux-machines.
author: deanwe
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 02/22/2021
ms.author: deanwe
ms.openlocfilehash: e36d170e1a7f918ff40c32f3225ce3a0ddbe260f
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101662214"
---
# <a name="azure-automanage-for-virtual-machines-best-practices---linux"></a>Aanbevolen procedures voor Azure automanage voor virtuele machines-Linux

Deze Azure-Services worden automatisch voor u geboardd wanneer u automanage voor virtuele machines (Vm's) op een Linux-VM gebruikt. Ze zijn essentieel voor het technische document van best practices, dat u kunt vinden in ons [Cloud adoptie Framework](/azure/cloud-adoption-framework/manage/azure-server-management).

Voor al deze services worden automatisch de voor bereid, automatisch geconfigureerd, gecontroleerd op drift en hersteld als er een drift wordt gedetecteerd. Zie [Azure automanage voor virtuele machines voor](automanage-virtual-machines.md)meer informatie over dit proces.

## <a name="supported-linux-distributions-and-versions"></a>Ondersteunde Linux-distributies en-versies

Automanage ondersteunt de volgende Linux-distributies en-versies:

- CentOS 7.3 +
- RHEL 7,4-7,8
- Ubuntu 16,04 en 18,04
- SLES 12 (alle service packs)

## <a name="participating-services"></a>Deelnemende Services

>[!NOTE]
> Micro soft antimalware wordt op dit moment niet ondersteund op Linux-Vm's.

|Service    |Beschrijving    |Omgevingen worden ondersteund<sup>1</sup>    |Voor keuren ondersteund<sup>1</sup>    |
|-----------|---------------|----------------------|-------------------------|
|Bewaking van VM Insights    |Azure Monitor voor VM's bewaakt de prestaties en status van uw virtuele machines, met inbegrip van de actieve processen en afhankelijkheden van andere bronnen. [Meer](../azure-monitor/vm/vminsights-overview.md)informatie.    |Productie    |Nee    |
|Backup    |Azure Backup biedt onafhankelijke en geïsoleerde back-ups om bescherming te bieden tegen onbedoelde vernietiging van de gegevens op uw VM's. [Meer](../backup/backup-azure-vms-introduction.md)informatie. De kosten zijn gebaseerd op het aantal en de grootte van virtuele machines die worden beveiligd. [Meer](https://azure.microsoft.com/pricing/details/backup/)informatie.    |Productie    |Ja    |
|Azure Security Center    |Azure Security Center is een systeem voor geïntegreerde infrastructuur beveiliging dat de beveiligings postuur van uw data centers versterkt en geavanceerde bedreigings beveiliging biedt voor uw hybride werk belastingen in de Cloud. [Meer](../security-center/security-center-introduction.md)informatie.  Met automatisch beheer configureert u het abonnement waar uw VM zich bevindt in de gratis laag van Azure Security Center. Als uw abonnement al Azure Security Center is, wordt het niet opnieuw geconfigureerd door automatisch beheren.    |Productie, dev/test    |Nee    |
|Updatebeheer    |U kunt Updatebeheer in Azure Automation gebruiken om updates van het besturings systeem voor uw virtuele machines te beheren. U kunt snel de status van beschik bare updates op alle agent computers beoordelen en het proces voor het installeren van vereiste updates voor servers beheren. [Meer](../automation/update-management/overview.md)informatie.    |Productie, dev/test    |Nee    |
|Wijzigingen bijhouden &-inventarisatie    |Wijzigingen bijhouden en Inventaris combineren functies voor het bijhouden van wijzigingen en inventaris, zodat u wijzigingen in de infrastructuur van virtuele machines en servers kunt bijhouden. De service biedt ondersteuning voor het bijhouden van wijzigingen in Services, daemons software, REGI ster en bestanden in uw omgeving om u te helpen bij het vaststellen van ongewenste wijzigingen en het activeren van waarschuwingen. Met inventarisondersteuning kunt u in-guest resources opvragen voor zichtbaarheid van de geïnstalleerde toepassingen en andere configuratie-items.  [Meer](../automation/change-tracking/overview.md)informatie.    |Productie, dev/test    |Nee    |
|Azure-gast configuratie    | Gast configuratie beleid wordt gebruikt om de configuratie te controleren en te rapporteren over de naleving van de computer. De service voor automatisch beheer installeert de Azure Linux-basis lijn met behulp van de gast configuratie-extensie. Voor Linux-machines installeert de gast configuratie service de basis lijn in de modus alleen controle. U kunt zien waar de virtuele machine niet voldoet aan de basis lijn, maar niet-naleving wordt niet automatisch hersteld. [Meer](../governance/policy/concepts/guest-configuration.md)informatie.    |Productie, dev/test    |Nee    |
|Azure Automation Account    |Azure Automation ondersteunt beheer gedurende de gehele levenscyclus van uw infrastructuur en toepassingen. [Meer](../automation/automation-intro.md)informatie.    |Productie, dev/test    |Nee    |
|Log Analytics-werkruimte    |Azure Monitor worden logboek gegevens opgeslagen in een Log Analytics-werk ruimte, een Azure-resource en een container waarin gegevens worden verzameld, geaggregeerd en fungeert als een administratieve grens. [Meer](../azure-monitor/logs/design-logs-deployment.md)informatie.    |Productie, dev/test    |Nee    |


<sup>1</sup> de omgevings selectie is beschikbaar wanneer u autobeheer inschakelt. [Meer](automanage-virtual-machines.md#environment-configuration)informatie. U kunt ook de standaard instellingen van de omgeving aanpassen en uw eigen voor keuren instellen in de beperkingen van de best practices.


## <a name="next-steps"></a>Volgende stappen

Probeer automanage in te scha kelen voor virtuele machines in de Azure Portal.

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)