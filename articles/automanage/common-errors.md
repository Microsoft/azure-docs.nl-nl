---
title: Veelvoorkomende fouten bij het oplossen van problemen met Azure automanage
description: Veelvoorkomende fouten bij onboarding en het oplossen van problemen
author: asinn826
ms.service: virtual-machines
ms.subservice: automanage
ms.workload: infrastructure
ms.topic: conceptual
ms.date: 01/14/2021
ms.author: alsin
ms.openlocfilehash: 18165ce5f39b32fe1c5af28bc88e8e1bd0e9cb62
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104955547"
---
# <a name="troubleshoot-common-automanage-onboarding-errors"></a>Veelvoorkomende fouten bij het onboarden van problemen oplossen
Automatisch beheer kan een machine niet op de service voorbereiden. In dit document wordt uitgelegd hoe u implementatie fouten oplost, delen enkele veelvoorkomende oorzaken van het mislukken van implementaties en een beschrijving van mogelijke volgende stappen bij een oplossing.

## <a name="troubleshooting-deployment-failures"></a>Implementatie fouten oplossen
Als u een machine onboardt op automatisch beheer, wordt er een Azure Resource Manager implementatie gemaakt. Als onboarding mislukt, kan het nuttig zijn om de implementatie te raadplegen voor meer informatie over de oorzaak van de fout. Er zijn koppelingen naar de implementaties in de flyout fout detail, afbeelding hieronder.

:::image type="content" source="media\common-errors\failure-flyout.png" alt-text="Flyout voor fout Details van automanage.":::

### <a name="check-the-deployments-for-the-resource-group-containing-the-failed-vm"></a>Controleer de implementaties voor de resource groep met de mislukte VM
De vervolg-flyout bevat een koppeling naar de implementaties binnen de resource groep die de computer bevat waarop de onboarding is mislukt en een naam voor het voor voegsel dat u kunt gebruiken voor het filteren van implementaties met. Als u op de koppeling klikt, wordt u naar de Blade implementaties geleid, waar u vervolgens de implementaties kunt filteren om automatisch beheerde implementaties op uw computer te zien. Als u in meerdere regio's implementeert, moet u ervoor zorgen dat u op de implementatie in de juiste regio klikt.

### <a name="check-the-deployments-for-the-subscription-containing-the-failed-vm"></a>Controleer de implementaties voor het abonnement met de mislukte VM
Als er geen fouten worden weer geven in de implementatie van de resource groep, is de volgende stap het bekijken van de implementaties in uw abonnement met de virtuele machine waarvoor de onboarding is mislukt. Klik in de flyout fout op de koppeling **implementaties voor abonnement** en filter implementaties met behulp van het filter auto **Manage-DefaultResourceGroup** . Gebruik de naam van de resource groep van de Blade fout om implementaties te filteren. De naam van de implementatie is een achtervoegsel met een regio naam. Als u in meerdere regio's implementeert, moet u ervoor zorgen dat u op de implementatie in de juiste regio klikt.

### <a name="check-deployments-in-a-subscription-linked-to-a-log-analytics-workspace"></a>Implementaties controleren in een abonnement dat is gekoppeld aan een Log Analytics-werk ruimte
Als er geen mislukte implementaties worden weer geven in de resource groep of het abonnement dat uw defecte virtuele machine bevat, en als uw virtuele machine is verbonden met een Log Analytics-werk ruimte in een ander abonnement, gaat u naar het abonnement dat is gekoppeld aan uw Log Analytics-werk ruimte en controleert u op mislukte implementaties.

## <a name="common-deployment-errors"></a>Veelvoorkomende implementatiefouten

Fout |  Oplossing
:-----|:-------------|
Fout met onvoldoende machtigingen voor het account voor automanaged | Dit kan gebeuren als u onlangs een abonnement met een nieuwe account voor automanage hebt verplaatst naar een nieuwe Tenant. Stappen om dit op te lossen, vindt u [hier](./repair-automanage-account.md).
Werkruimte regio komt niet overeen met vereisten voor de toewijzing van regio's | Automatisch beheer kan de computer niet vrijgeven, maar de Log Analytics werk ruimte waaraan de machine momenteel is gekoppeld, is niet toegewezen aan een ondersteunde automatiserings regio. Zorg ervoor dat uw bestaande Log Analytics-werk ruimte en het Automation-account zich in een [ondersteunde regio toewijzing](../automation/how-to/region-mappings.md)bevinden.
De toegang is geweigerd vanwege het weigeren van de toewijzing met de naam die door de beheerde toepassing is gemaakt. | Er is een [denyAssignment](../role-based-access-control/deny-assignments.md) gemaakt in uw resource waardoor automanage geen toegang heeft tot uw resource. Dit kan zijn veroorzaakt door een [blauw druk](../governance/blueprints/concepts/resource-locking.md) of een [beheerde toepassing](../azure-resource-manager/managed-applications/overview.md).
"OS Information: name = ' (null), ver = ' (null), agent status = ' niet gereed '." | Zorg ervoor dat u een [Mini maal ondersteunde agent versie](/troubleshoot/azure/virtual-machines/support-extensions-agent-version)uitvoert, de agent wordt uitgevoerd[(Linux](/troubleshoot/azure/virtual-machines/linux-azure-guest-agent) en [Windows](/troubleshoot/azure/virtual-machines/windows-azure-guest-agent)) en dat de agent up-to-date is ([Linux](../virtual-machines/extensions/update-linux-agent.md) en [Windows](../virtual-machines/extensions/agent-windows.md)).
' Er is een fout in de VM opgetreden bij het verwerken van de extensie ' IaaSAntimalware ' ' | Zorg ervoor dat er al een andere antimalware/antivirus aanbieding op uw virtuele machine is geïnstalleerd. Als dat niet lukt, neemt u contact op met de ondersteuning.
ASC-werk ruimte: de Log Analytics-service wordt momenteel niet ondersteund op _locatie_. | Controleer of uw virtuele machine zich in een [ondersteunde regio](./automanage-virtual-machines.md#supported-regions)bevindt.
De sjabloon implementatie is mislukt vanwege een overtreding van het beleid. Zie de Details voor meer informatie. | Er is een beleid dat het voor komen dat automanage de virtuele machine onboardt. Controleer de beleids regels die worden toegepast op uw abonnement of resource groep met uw virtuele machine die u wilt voorbereiden op automanage.
De toewijzing is mislukt; Er is geen aanvullende informatie beschikbaar. | Open een aanvraag met Microsoft Azure ondersteuning.

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over Azure automanage](./automanage-virtual-machines.md)

> [!div class="nextstepaction"]
> [Schakel automanage in voor virtuele machines in de Azure Portal](quick-create-virtual-machines-portal.md)