---
title: Onderbrekingen van de Azure-service
description: Meer informatie over wat u moet doen in het geval dat een Azure-service een onderbreking heeft op Azure virtual machines.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.date: 05/31/2017
ms.author: cynthn
ms.reviewer: ''
ms.openlocfilehash: 5c6036a65fa449986f5c085202fefa752e3a414a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98701030"
---
# <a name="what-if-an-azure-service-disruption-impacts-azure-vms"></a>Wat gebeurt er als de onderbreking van een Azure-service gevolgen heeft voor Azure Vm's

Bij micro soft werken we hard om ervoor te zorgen dat onze services altijd beschikbaar zijn wanneer u ze nodig hebt. Forceren meer dan ons besturings element is soms van invloed op de manier waarop ongeplande service onderbrekingen ontstaan.

Micro soft biedt een Service Level Agreement (SLA) voor zijn services als een toezeg ging voor de uptime en connectiviteit. De SLA voor afzonderlijke Azure-Services vindt u op [Azure Service Level Agreements](https://azure.microsoft.com/support/legal/sla/).

Azure biedt al veel ingebouwde platform functies die ondersteuning bieden voor Maxi maal beschik bare toepassingen. Lees [nood herstel en hoge Beschik baarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)voor meer informatie over deze services.

In dit artikel wordt een scenario voor herstel na nood gevallen beschreven, wanneer een hele regio een storing veroorzaakt door een grote natuur ramp of een uitgebreide service onderbreking. Dit zijn zeldzame gevallen, maar u moet er wel voor bereid zijn dat er een storing optreedt in een hele regio. Als een hele regio een onderbreking van de service ondervindt, zijn de lokaal redundante kopieën van uw gegevens tijdelijk niet beschikbaar. Als u geo-replicatie hebt ingeschakeld, worden er drie extra kopieën van uw Azure Storage-blobs en-tabellen in een andere regio opgeslagen. In het geval van een volledige regionale onderbreking of nood gevallen waarin de primaire regio niet kan worden hersteld, wijst Azure alle DNS-vermeldingen toe aan de regio met geo-replicatie.

Om u te helpen deze zeldzame gevallen te verwerken, bieden we de volgende richt lijnen voor virtuele Azure-machines in het geval van een service onderbreking van de hele regio waar uw Azure virtual machine-toepassing wordt geïmplementeerd.

## <a name="option-1-initiate-a-failover-by-using-azure-site-recovery"></a>Optie 1: een failover initiëren met behulp van Azure Site Recovery
U kunt Azure Site Recovery voor uw virtuele machines configureren, zodat u uw toepassing met één klik in een paar minuten kunt herstellen. U kunt repliceren naar de Azure-regio van uw keuze en niet beperkt tot gekoppelde regio's. U kunt aan de slag door [uw virtuele machines te repliceren](../site-recovery/azure-to-azure-quickstart.md). U kunt [een herstel plan maken](../site-recovery/site-recovery-create-recovery-plans.md) zodat u het hele failoverproces voor uw toepassing kunt automatiseren. U kunt [de failovers](../site-recovery/site-recovery-test-failover-to-azure.md) van tevoren testen zonder dat dit van invloed is op de productie toepassing of de continue replicatie. In het geval van een onderbreking van de primaire regio [initieert u zojuist een failover](../site-recovery/site-recovery-failover.md) en brengt u uw toepassing in de doel regio.


## <a name="option-2-wait-for-recovery"></a>Optie 2: wachten op herstel
In dit geval is er geen actie voor uw onderdeel vereist. U weet dat we hard werken om de beschik baarheid van de service te herstellen. U kunt de huidige status van de service zien op het [Azure service Health-dash board](https://azure.microsoft.com/status/).

Dit is de beste optie als u geen Azure Site Recovery, geografisch redundante opslag met lees toegang of geografisch redundante opslag hebt ingesteld voorafgaand aan de onderbreking. Als u geografisch redundante opslag of geografisch redundante opslag met lees toegang hebt ingesteld voor het opslag account waarin de virtuele harde schijven van uw virtuele machines zijn opgeslagen, kunt u de VHD met basis installatie kopie herstellen en proberen een nieuwe VM in te richten. Dit is geen voorkeurs optie omdat er geen garanties zijn voor het synchroniseren van gegevens. Daarom is deze optie niet gegarandeerd werk.


> [!NOTE]
> Houd er rekening mee dat u geen controle hebt over dit proces. dit gebeurt alleen voor onderbrekingen van de hele regio. Daarom moet u ook vertrouwen op andere toepassingsspecifieke back-upstrategieen om het hoogste niveau van Beschik baarheid te krijgen. Zie de sectie over [gegevens strategieën voor herstel na nood gevallen](/azure/architecture/reliability/disaster-recovery#disaster-recovery-plan)voor meer informatie.
>
>

## <a name="next-steps"></a>Volgende stappen

- [Het beveiligen van uw toepassingen die worden uitgevoerd op virtuele machines van Azure](../site-recovery/azure-to-azure-quickstart.md) met Azure site Recovery starten

- Zie [herstel na nood gevallen en hoge Beschik baarheid voor Azure-toepassingen](/azure/architecture/framework/resiliency/backup-and-recovery)voor meer informatie over het implementeren van een strategie voor herstel na nood gevallen en hoge Beschik baarheid.

- Zie [technische richt lijnen voor Azure](../data-lake-store/data-lake-store-disaster-recovery-guidance.md)voor meer informatie over het ontwikkelen van een gedetailleerd technisch inzicht in de mogelijkheden van een Cloud platform.


- Neem contact op met de [klant ondersteuning](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade)als de instructies niet duidelijk zijn of als u wilt dat micro soft de bewerkingen namens u uitvoert.