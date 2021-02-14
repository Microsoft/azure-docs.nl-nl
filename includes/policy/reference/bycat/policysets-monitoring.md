---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 02/09/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: 5c1857372eb4aad2bf87e4078bae5e1fc41412a8
ms.sourcegitcommit: 24f30b1e8bb797e1609b1c8300871d2391a59ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100091857"
---
|Naam |Beschrijving |Beleidsregels |Versie |
|---|---|---|---|
|[Vereisten implementeren-configureren om Azure Monitor-en Azure-beveiligings agenten in te scha kelen op virtuele machines](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Configureer machines om automatisch de Azure Monitor-en Azure-beveiligings agenten te installeren. Security Center verzamelt gebeurtenissen van de agents en gebruikt deze om beveiligings waarschuwingen en aangepaste verwerkings taken (aanbevelingen) te bieden. Maak een resource groep en Log Analytics werk ruimte in dezelfde regio als de machine voor het opslaan van audit records. Dit beleid is alleen van toepassing op Vm's in een paar regio's. |5 |1.0.0-preview |
|[Azure Monitor voor virtuele-machineschaalsets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Schakel Azure Monitor in voor de virtuele-machineschaalsets in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. Opmerking: als voor uw schaalset de waarde van upgradePolicy is ingesteld op Handmatig, moet u de extensie op alle VM's in de set toepassen door een aanroep voor het upgraden van de VM's uit te voeren. In CLI doet u dit met az vmss update-instances. |6 |1.0.1 |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de virtuele machines (VM's) in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. |10 |2.0.0 |
