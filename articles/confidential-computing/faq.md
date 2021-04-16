---
title: Veelgestelde vragen over Azure Confidential Computing
description: Antwoorden op veelgestelde vragen over Azure Confidential Computing.
author: JBCook
ms.topic: troubleshooting
ms.workload: infrastructure
ms.service: virtual-machines
ms.subservice: confidential-computing
ms.date: 4/17/2020
ms.author: jencook
ms.openlocfilehash: 05c98102109d1925eb78d4558faf62b366801e77
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107538979"
---
# <a name="frequently-asked-questions-for-azure-confidential-computing"></a>Veelgestelde vragen over Azure Confidential Computing

In dit artikel vindt u antwoorden op enkele van de meest voorkomende vragen over het uitvoeren van [confidential computing-workloads op virtuele Azure-machines.](overview.md)

Als uw Azure-probleem niet in dit artikel wordt behandeld, gaat u naar de Azure-forums op [MSDN en Stack Overflow](https://azure.microsoft.com/support/forums/). U kunt uw probleem in deze forums plaatsen of een bericht sturen naar [@AzureSupport op Twitter](https://twitter.com/AzureSupport). U kunt ook een ondersteuning voor Azure verzenden. Als u een ondersteuningsaanvraag wilt indienen, [selecteert ondersteuning voor Azure de](https://azure.microsoft.com/support/options/)pagina Ondersteuning krijgen.

## <a name="confidential-computing-virtual-machines"></a>Confidential Computing-Virtual Machines <a id="vm-faq"></a>

**Hoe kan ik VM's uit de DCsv2-serie implementeren in Azure?**

Hier zijn enkele manieren waarop u een virtuele DCsv2-VM kunt implementeren:
   - Een sjabloon [voor Azure Resource Manager gebruiken](../virtual-machines/windows/template-description.md)
   - Vanuit de [Azure Portal](https://portal.azure.com/#create/hub)
   - In de [marketplace-oplossingssjabloon Azure Confidential Computing (virtuele](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.acc-virtual-machine-v2?tab=overview) machine). De marketplace-oplossingssjabloon helpt een klant te beperken tot de ondersteunde scenario's (regio's, afbeeldingen, beschikbaarheid, schijfversleuteling). 

**Werken alle besturingssysteemafbeeldingen met Azure Confidential Computing?**

Nee. De virtuele machines kunnen alleen worden geïmplementeerd op computers van de tweede generatie met Ubuntu Server 18.04, Ubuntu Server 16.04, Windows Server 2019 Datacenter en Windows Server 2016 Datacenter. Meer informatie over virtuele Gen 2-VM's in [Linux](../virtual-machines/generation-2.md) en [Windows](../virtual-machines/generation-2.md)

**Virtuele DCsv2-machines worden grijs weergegeven in de portal en ik kan er geen selecteren**

Op basis van de informatieballon naast de VM zijn er verschillende acties die moeten worden ondernomen:
   -    **Niet-ondersteundGeneratie:** wijzig de generatie van de virtuele-machine-afbeelding in Gen2.
   -    **NotAvailableForSubscription:** de regio is nog niet beschikbaar voor uw abonnement. Selecteer een beschikbare regio.
   -    **InsufficientQuota:** maak [een ondersteuningsaanvraag om uw quotum te verhogen.](../azure-portal/supportability/per-vm-quota-requests.md) Gratis proefabonnementen hebben geen quotum voor VM's met Confidential Computing. 

**Virtuele DCsv2-machines worden niet weer geven wanneer ik ze probeer te zoeken in de grootte-selector van de portal**

Zorg ervoor dat u een beschikbare regio [hebt geselecteerd.](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) Zorg er ook voor dat u 'alle filters wissen' selecteert in de grootte-selector. 

**Kan ik versneld netwerken inschakelen met Azure Confidential Computing?**

 Nee. Versneld netwerken wordt niet ondersteund op DC-Series of DCsv2-Series virtuele machines. Versneld netwerken kan niet worden ingeschakeld voor implementatie van virtuele machines met confidential computing of Azure Kubernetes Service clusterimplementatie die wordt uitgevoerd op Confidential Computing.

**Kan ik Azure Dedicated Host gebruiken met deze machines?**

Ja. Azure Dedicated Host virtuele machines uit de DCsv2-serie ondersteunen. Azure Dedicated Host biedt een fysieke server met één tenant om uw virtuele machines op uit te voeren. Gebruikers gebruiken deze Azure Dedicated Host om te voldoen aan nalevingsvereisten op het gebied van fysieke beveiliging, gegevensintegriteit en bewaking. 

**Ik krijg een foutmelding Azure Resource Manager sjabloonimplementatie mislukt: 'De bewerking kan niet worden voltooid omdat dit resulteert in het overschrijden van het goedgekeurde quotum voor dcsV2-familiekernen'**

[Maak een ondersteuningsaanvraag om uw quotum te verhogen.](../azure-portal/supportability/per-vm-quota-requests.md) Gratis proefabonnementen hebben geen quotum voor VM's met Confidential Computing. 

**Wat is het verschil tussen virtuele DCsv2-Series en DC-Series's?**

DC-Series-VM's worden uitgevoerd op oudere Intel-processors met 6 cores met Intel SGX en hebben minder totaal geheugen, minder ENCLAVE Page Cache (EPC) geheugen en zijn beschikbaar in slechts twee regio's (US - oost en Europa - west in Standard_DC2s en Standard_DC4s grootte). Er zijn geen plannen om deze VM's algemeen beschikbaar te maken en ze worden niet aanbevolen voor productiegebruik. Als u deze VM's wilt implementeren, gebruikt u [het Marketplace-exemplaar Confidential Compute DC-Series VM [Preview].](https://azuremarketplace.microsoft.com/marketplace/apps/microsoft-azure-compute.confidentialcompute?tab=Overview)

**Zijn virtuele DCsv2-machines wereldwijd beschikbaar?**

Nee. Op dit moment zijn deze virtuele machines alleen beschikbaar in bepaalde regio's. Controleer de [pagina Producten per regio op](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines) de meest recente beschikbare regio's. 

**Is hyperthreading UIT op deze machines?**

Hyperthreading is uitgeschakeld voor alle Azure Confidential Computing-clusters.

**Hoe kan ik Open Enclave SDK installeren op de virtuele DCsv2-machines?**
   
Volg de instructies in [de Open Enclave SDK GitHub](https://github.com/openenclave/openenclave)voor instructies over het installeren van de OE SDK op een Azure- of on-premises machine.
     
U kunt ook de Open Enclave SDK GitHub bekijken voor installatie-specifieke installatie-instructies voor het besturingssysteem:
   - [De OE SDK installeren in Windows](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Windows.md)
   - [De OE SDK installeren op Ubuntu 18.04](https://github.com/openenclave/openenclave/blob/master/docs/GettingStartedDocs/install_oe_sdk-Ubuntu_18.04.md)