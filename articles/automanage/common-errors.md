---
title: Veelvoorkomende Azure Automanage onboarding-fouten oplossen
description: Veelvoorkomende fouten bij het onboarden van Automanage en hoe u deze kunt oplossen
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 6ee0164dd8243d30cf691350352757f2503e34c8
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107862971"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Veelvoorkomende onboardingfouten met Automanage oplossen
Met Automanage kan de onboarding van een machine op de service mislukken. In dit document wordt uitgelegd hoe u implementatiefouten kunt oplossen, worden enkele veelvoorkomende redenen voor het mislukken van implementaties beschreven en worden mogelijke volgende stappen voor het oplossen van problemen beschreven.

## <a name="troubleshooting-deployment-failures"></a>Implementatiefouten oplossen
Als u een machine onboardt voor Automatisch beheer, wordt er een Azure Resource Manager gemaakt. Zie de implementatie voor meer informatie over waarom de implementatie is mislukt. De flyout met foutdetails bevat koppelingen naar de implementaties, zoals hieronder wordt weergegeven.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Flyout voor foutdetails voor automatischmanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Controleer de implementaties voor de resourcegroep met de mislukte VM
De flyout voor fouten bevat een koppeling naar de implementaties in de resourcegroep die de machine bevat die de onboarding heeft mislukt. De flyout bevat ook een voorvoegselnaam die u kunt gebruiken om implementaties mee te filteren. Als u op de implementatiekoppeling klikt, gaat u naar de blade Implementaties, waar u vervolgens implementaties kunt filteren om Implementaties automatisch op uw computer te kunnen beheeren. Als u in meerdere regio's implementeert, moet u op de implementatie in de juiste regio klikken.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Controleer de implementaties voor het abonnement met de mislukte VM
Als u geen fouten ziet in de implementatie van de resourcegroep, is de volgende stap om te kijken naar de implementaties in uw abonnement met de VM die de onboarding heeft mislukt. Klik op **de koppeling Implementaties voor** abonnement in de flyout met fouten en filter implementaties met behulp van het filter **Automanage-DefaultResourceGroup.** Gebruik de naam van de resourcegroep op de blade Fouten om implementaties te filteren. De naam van de implementatie krijgt het achtervoegsel een regionaam. Als u in meerdere regio's implementeert, moet u op de implementatie in de juiste regio klikken.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Implementaties controleren in een abonnement dat is gekoppeld aan een Log Analytics-werkruimte
Als u geen mislukte implementaties ziet in de resourcegroep of het abonnement met uw mislukte VM en als uw mislukte VM is verbonden met een Log Analytics-werkruimte in een ander abonnement, gaat u naar het abonnement dat is gekoppeld aan uw Log Analytics-werkruimte en controleert u op mislukte implementaties.

## <a name="common-deployment-errors"></a>Veelvoorkomende implementatiefouten

Fout |  Oplossing
:-----|:-------------|
Fout bij onvoldoende machtigingen voor account automatischmanage | Deze fout kan optreden als u onlangs een abonnement met een nieuw Automanage-account hebt verplaatst naar een nieuwe tenant. Stappen voor het oplossen van deze fout bevinden zich [hier.](./repair-automanage-account.md)
Werkruimteregio komt niet overeen met vereisten voor regiotoewijzing | Automanage kan geen onboarding van uw computer maken omdat de Log Analytics-werkruimte waar de machine momenteel aan is gekoppeld, niet is gekoppeld aan een ondersteunde Automation-regio. Zorg ervoor dat uw bestaande Log Analytics-werkruimte en Automation-account zich in een [ondersteunde regiotoewijzing bevinden.](../automation/how-to/region-mappings.md)
'Toegang geweigerd vanwege de toewijzing voor weigeren met de naam 'Systeemte weigeren toewijzing gemaakt door beheerde toepassing'' | Er [is een denyAssignment](https://docs.microsoft.com/azure/role-based-access-control/deny-assignments) gemaakt voor uw resource, waardoor Automanage geen toegang heeft tot uw resource. Deze denyAssignment kan zijn gemaakt door een [blauwdruk](https://docs.microsoft.com/azure/governance/blueprints/concepts/resource-locking) of een [beheerde toepassing](https://docs.microsoft.com/azure/azure-resource-manager/managed-applications/overview).
"Os Information: Name='(null)', ver='(null)', agent status='Not Ready'." | Zorg ervoor dat u een minimaal ondersteunde [agentversie](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)gebruikt, dat de agent wordt uitgevoerd[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) en [Windows)](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)en dat de agent up-to-date is[(Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) en [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
"Kan het besturingssysteem voor de naam van het VM-besturingssysteem niet bepalen:, ver . Controleer of de VM-agent wordt uitgevoerd. De huidige status is Gereed. | Zorg ervoor dat u een minimaal ondersteunde [agentversie](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/support-extensions-agent-version)gebruikt, dat de agent wordt uitgevoerd[(Linux](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) en [Windows)](https://docs.microsoft.com/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)en dat de agent up-to-date is[(Linux](https://docs.microsoft.com/azure/virtual-machines/extensions/update-linux-agent) en [Windows).](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-windows)
'VM heeft een fout gerapporteerd bij het verwerken van extensie 'IaaSAntimalware' | Zorg ervoor dat er geen andere antimalware-/antivirusaanbieding al is geïnstalleerd op uw VM. Als dat mislukt, neem dan contact op met de ondersteuning.
ASC-werkruimte: Automanage biedt momenteel geen ondersteuning voor de Log Analytics-service op _locatie_. | Controleer of uw VM zich in een [ondersteunde regio bevindt.](./automanage-virtual-machines.md#supported-regions)
De sjabloonimplementatie is mislukt vanwege een schending van het beleid. Raadpleeg de details voor meer informatie. | Er is een beleid waardoor automanage de onboarding van uw VM verhindert. Controleer de beleidsregels die worden toegepast op uw abonnement of resourcegroep met de VM die u wilt onboarden naar Automanage.
'De toewijzing is mislukt; er is geen aanvullende informatie beschikbaar' | Open een case met Microsoft Azure ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure Automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Automanage inschakelen voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)