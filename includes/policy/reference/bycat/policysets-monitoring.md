---
author: DCtheGeek
ms.service: azure-policy
ms.topic: include
ms.date: 04/21/2021
ms.author: dacoulte
ms.custom: generated
ms.openlocfilehash: f8095fc7c9be6ea23a573de8a5e871965d6c2a66
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866023"
---
|Naam |Beschrijving |Beleidsregels |Versie |
|---|---|---|---|
|[\[Preview: \] Implementeren: vereisten configureren om azure-beveiligingsagents en azure-beveiligingsagents in te Azure Monitor virtuele machines](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitoring_Prerequisites.json) |Machines configureren om automatisch de Azure Monitor en Azure Security-agents te installeren. Security Center verzamelt gebeurtenissen van de agents en gebruikt deze om beveiligingswaarschuwingen en op maat gemaakte beveiligingstaken (aanbevelingen) te bieden. Maak een resourcegroep en Log Analytics-werkruimte in dezelfde regio als de computer om controlerecords op te slaan. Dit beleid is alleen van toepassing op VM's in enkele regio's. |5 |1.0.0-preview |
|[Azure Monitor voor virtuele-machineschaalsets inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VMSS.json) |Schakel Azure Monitor in voor de virtuele-machineschaalsets in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. Opmerking: als voor uw schaalset de waarde van upgradePolicy is ingesteld op Handmatig, moet u de extensie op alle VM's in de set toepassen door een aanroep voor het upgraden van de VM's uit te voeren. In CLI doet u dit met az vmss update-instances. |6 |1.0.1 |
|[Azure Monitor voor VM's inschakelen](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policySetDefinitions/Monitoring/AzureMonitor_VM.json) |Schakel Azure Monitor in voor de virtuele machines (VM's) in het opgegeven bereik (beheergroep, abonnement of resourcegroep). De Log Analytics-werkruimte wordt als parameter gebruikt. |10 |2.0.0 |
