---
title: Veelgestelde vragen over Azure voor vertrouwelijke computing
description: Antwoorden op veelgestelde vragen over Azure vertrouwelijk computing.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: a5ecd3827bbdc12b098684f1feda2df652f11940
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102551909"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Veelgestelde vragen over Azure vertrouwelijk computing

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [werk belastingen op Azure virtual machines](overview.md).

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteunings aanvraag voor Azure indienen. Als u een ondersteunings aanvraag wilt indienen, selecteert u op de [pagina ondersteuning voor Azure](https://azure.microsoft.com/support/options/)de optie ondersteuning ophalen.

## <a name="confidential-computing-virtual-machines"></a>Virtual Machines voor vertrouwelijke computing <a id="vm-faq"></a>

**Hoe kan ik virtuele machines uit de DCsv2-serie implementeren op Azure?**

Hier volgen enkele manieren waarop u een DCsv2-VM kunt implementeren:
   - Een [Azure Resource Manager-sjabloon](../virtual-machines/windows/template-description.md) gebruiken
   - Van de [Azure Portal](https://portal.azure.com/#create/hub)
   - In de sjabloon [Azure vertrouwelijk Computing (Virtual Machine)](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) Marketplace-oplossing. De oplossings sjabloon voor Marketplace helpt een klant te beperken tot de ondersteunde scenario's (regio's, installatie kopieën, Beschik baarheid, schijf versleuteling). 

**Werken alle besturingssysteem installatie kopieën met Azure vertrouwelijk computing?**

Nee. De virtuele machines kunnen alleen worden geïmplementeerd op machines van de tweede generatie met Ubuntu Server 18,04, Ubuntu Server 16,04, Windows Server 2019 Data Center en Windows Server 2016 Data Center. Meer informatie over virtuele machines van generatie 2 in [Linux](../virtual-machines/generation-2.md) en [Windows](../virtual-machines/generation-2.md)

**Virtuele DCsv2-machines zijn niet beschikbaar in de portal en ik kan er geen selecteren**

Op basis van de informatie ballon naast de virtuele machine zijn er verschillende acties die u moet uitvoeren:
   -    **UnsupportedGeneration**: Wijzig de generatie van de installatie kopie van de virtuele machine in ' Gen2 '.
   -    **NotAvailableForSubscription**: de regio is nog niet beschikbaar voor uw abonnement. Selecteer een beschikbare regio.
   -    **InsufficientQuota**: [Maak een ondersteunings aanvraag om uw quotum te verg Roten](../azure-portal/supportability/per-vm-quota-requests.md). Abonnementen voor een gratis proef abonnement hebben geen quota voor het gebruik van de virtuele machines van het werk. 

**Virtuele DCsv2-machines worden niet weer gegeven wanneer ik deze probeer te zoeken in de portal formaat kiezer**

Zorg ervoor dat u een [beschik bare regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines)hebt geselecteerd. Zorg er ook voor dat u ' alle filters wissen ' selecteert in de optie grootte. 

**Kan ik versneld netwerken met Azure vertrouwelijk computing inschakelen?**

 Nee. Versnelde netwerken worden niet ondersteund op DC-Series-of DCsv2-Series virtuele machines. Versnelde netwerken kunnen niet worden ingeschakeld voor de implementatie van de virtuele machine of het Azure Kubernetes-service cluster dat wordt uitgevoerd op vertrouwelijke computing.

**Kan ik een exclusieve Azure-host gebruiken met deze machines?**

Ja. Met Azure exclusieve host ondersteunen virtuele machines uit de DCsv2-serie. De toegewezen Azure-host biedt een fysieke server met één Tenant voor het uitvoeren van uw virtuele machines op. Gebruikers gebruiken meestal een specifieke Azure-host om nalevings vereisten te voldoen rond fysieke beveiliging, gegevens integriteit en bewaking. 

**Er wordt een Azure Resource Manager fout opgetreden bij het implementeren van een sjabloon: ' de bewerking kan niet worden voltooid omdat deze resulteert in een quotum van meer dan goedgekeurd Standard DcsV2-kernen**

[Maak een ondersteunings aanvraag om uw quotum te verg Roten](../azure-portal/supportability/per-vm-quota-requests.md). Abonnementen voor een gratis proef abonnement hebben geen quota voor het gebruik van de virtuele machines van het werk. 

**Wat is het verschil tussen DCsv2-Series en DC-Series Vm's?**

DC-Series Vm's worden uitgevoerd op oudere 6-core Intel-processors met Intel SGX en hebben minder geheugen, minder enclave-cache geheugen (EPC) en zijn alleen beschikbaar in twee regio's (VS-Oost en Europa-West in Standard_DC2s en Standard_DC4s grootten). Er zijn geen plannen om deze Vm's algemeen beschikbaar te maken en ze worden niet aanbevolen voor productie gebruik. Als u deze Vm's wilt implementeren, gebruikt u het Marketplace-exemplaar van de DC-Series voor het uitvoeren van een computer met de naam [vertrouwelijk Compute](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

**Zijn virtuele DCsv2-machines wereld wijd beschikbaar?**

Nee. Op dit moment zijn deze virtuele machines alleen beschikbaar in bepaalde regio's. Controleer de [pagina producten per regio](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) voor de meest recente beschik bare regio's. 

**Is Hyper-Threading uit op deze computers?**

Hyper Threading is uitgeschakeld voor alle Azure vertrouwelijk computing-clusters.

**Hoe kan ik installeert u de open enclave-SDK op virtuele machines van DCsv2?**
   
Volg de instructies in de [Open ENCLAVE SDK github](https://github.com/openenclave/openenclave)voor instructies over het installeren van de OE SDK op een Azure-of on-premises computer.
     
U kunt ook zoeken in de open enclave SDK GitHub voor OS-specifieke installatie-instructies:
   - [De OE SDK installeren in Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [De OE SDK installeren op Ubuntu 18,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)
   - [De OE SDK installeren op Ubuntu 16,04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_16.04.md)