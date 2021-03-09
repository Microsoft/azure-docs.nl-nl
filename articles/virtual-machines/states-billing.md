---
title: Statussen en facturering van Azure Virtual Machines
description: Overzicht van verschillende statussen die een VM kan invoeren en wanneer een gebruiker wordt gefactureerd.
services: virtual-machines
author: mimckitt
ms.service: virtual-machines
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/8/2021
ms.author: mimckitt
ms.reviewer: cynthn
ms.openlocfilehash: 0325dcf16c8e637a58365311a4ebd37a442d6b8c
ms.sourcegitcommit: 956dec4650e551bdede45d96507c95ecd7a01ec9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102522428"
---
# <a name="states-and-billing-of-azure-virtual-machines"></a>Statussen en facturering van Azure Virtual Machines

Azure Virtual Machines (Vm's) passeren verschillende statussen die kunnen worden ingedeeld in *inrichting* en *energie* status. Het doel van dit artikel is om deze statussen te beschrijven en te markeren wanneer klanten worden gefactureerd voor het gebruik van een exemplaar. 

## <a name="get-states-using-instance-view"></a>Statussen ophalen met de instantie weergave

De instantie weergave-API biedt informatie over de status van de virtuele machine. Zie de documentatie over de [virtual machines-instantie weergave-](/rest/api/compute/virtualmachines/instanceview) API voor meer informatie.

Azure resource Explorer biedt een eenvoudige gebruikers interface voor het weer geven van de actieve status van de VM: [resource Explorer](https://resources.azure.com/).

De inrichtings statussen zijn zichtbaar in de VM-eigenschappen en de weer gave van exemplaren. Energie statussen zijn beschikbaar in de instantie weergave van de virtuele machine.

Als u de energiestatus van alle VM's in uw abonnement wilt ophalen, gebruikt u de API [Virtual Machines - List All](/rest/api/compute/virtualmachines/listall), waarbij u de parameter **statusOnly** instelt op *true*.

> [!NOTE]
> [Virtual machines: alle Api's weer geven](/rest/api/compute/virtualmachines/listall) waarbij de para meter **statusOnly** is ingesteld op True, haalt de energie status van alle virtuele machines in een abonnement op. In sommige zeldzame gevallen is de energie status echter mogelijk niet beschikbaar vanwege onregelmatige problemen bij het ophalen van het proces. In dergelijke gevallen kunt u het beste opnieuw proberen met behulp van dezelfde API of door [Azure resource Health](../service-health/resource-health-overview.md) of [Azure resource Graph](..//governance/resource-graph/overview.md) te gebruiken om de energie status van uw vm's te controleren.
 
## <a name="power-states-and-billing"></a>Energie status en facturering

De energie status vertegenwoordigt de laatste bekende status van de virtuele machine.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-power-states.png" alt-text="Afbeelding toont een diagram van de energie statussen die een virtuele machine kan passeren. ":::

De volgende tabel bevat een beschrijving van elke instantie status en geeft aan of deze in rekening wordt gebracht voor het gebruik van exemplaren of niet.

| Energie status | Beschrijving | Billing |  
|---|---|---|
| Starten| De virtuele machine wordt uitgeschakeld. |Niet gefactureerd * | 
| Wordt uitgevoerd | De virtuele machine is volledig actief. Dit is de standaard werk status. | Gefactureerd | 
| Stoppen | Dit is een overgangs status tussen lopend en gestopt. | Gefactureerd| 
|Gestopt | De virtuele machine is afgesloten vanuit het gast besturingssysteem of met behulp van uitgeschakeld-Api's. In deze status is de virtuele machine nog bezig met het vrijgeven van de onderliggende hardware. Deze status wordt ook wel *gestopt (toegewezen)* genoemd. | Gefactureerd | 
| Vrijgeven | Dit is de overgangs status tussen het uitvoeren en het opgeheven van de toewijzing. | Niet gefactureerd * | 
| Toewijzing ongedaan gemaakt | De virtuele machine heeft de lease vrijgegeven op de onderliggende hardware en is volledig uitgeschakeld. Deze status wordt ook wel *gestopt (deallocated)* genoemd. | Niet gefactureerd * | 

&#42; sommige Azure-resources, zoals [schijven](https://azure.microsoft.com/pricing/details/managed-disks) en [netwerken](https://azure.microsoft.com/pricing/details/bandwidth/) , blijven kosten in rekening worden gebracht.


## <a name="provisioning-states"></a>Inrichtings status

Een inrichtings status is de status van een door de gebruiker geïnitieerde, besturings vlak bewerking op de virtuele machine. Deze statussen zijn gescheiden van de energie status van een virtuele machine.

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/vm-provisioning-states.png" alt-text="Afbeelding toont de inrichtings status die een virtuele machine kan passeren.":::

| Inrichtings status | Beschrijving | Energie status | Billing | 
|---|---|---|---|
| Maken | Virtuele machine maken. | Starten | Niet gefactureerd * | 
| Bijwerken | Hiermee werkt u het model voor een bestaande virtuele machine bij. Sommige niet-model wijzigingen voor een virtuele machine, zoals starten en opnieuw opstarten, vallen onder de update status. | Wordt uitgevoerd | Gefactureerd | 
| Verwijderen | De virtuele machine wordt verwijderd. | Vrijgeven | Niet gefactureerd * |
| Toewijzing ongedaan maken | De virtuele machine is volledig gestopt en van de onderliggende host verwijderd. Het ongedaan maken van de toewijzing van een virtuele machine wordt beschouwd als een update en de inrichtings status wordt als bijgewerkt weer gegeven. | Vrijgeven | Niet gefactureerd * | 

&#42; sommige Azure-resources, zoals [schijven](https://azure.microsoft.com/pricing/details/managed-disks) en [netwerken](https://azure.microsoft.com/pricing/details/bandwidth/) , blijven kosten in rekening worden gebracht.

## <a name="os-provisioning-states"></a>Inrichtings status van besturings systeem
De inrichtings status van besturings systemen gelden alleen voor virtuele machines die zijn gemaakt met een installatie kopie van een besturings systeem. Deze statussen worden niet weer gegeven in gespecialiseerde installatie kopieën. 

:::image type="content" source="./media/virtual-machines-common-states-lifecycle/os-provisioning-states.png" alt-text="Afbeelding toont de inrichtings status van het besturings systeem die een virtuele machine kan passeren.":::

| Inrichtings status van besturings systeem | Beschrijving | Energie status | Billing | 
|---|---|---|---|
| OSProvisioningInProgress | De virtuele machine wordt uitgevoerd en de installatie van het gast besturingssysteem wordt uitgevoerd. | Wordt uitgevoerd | Gefactureerd | 
| OSProvisioningComplete | Dit is een korte status. De virtuele machine verandert snel van deze status naar **geslaagd**. Als er nog extensies worden geïnstalleerd, blijft u deze status zien totdat ze zijn voltooid. | Wordt uitgevoerd | Gefactureerd | 
| Geslaagd | De door de gebruiker gestarte acties zijn voltooid. | Wordt uitgevoerd | Gefactureerd | 
| Mislukt | Vertegenwoordigt een mislukte bewerking. Raadpleeg de fout code voor meer informatie en mogelijke oplossingen. | Wordt uitgevoerd  | Gefactureerd | 


## <a name="next-steps"></a>Volgende stappen
- Raadpleeg de [documentatie over Azure Cost Management en facturering](https://docs.microsoft.com/azure/cost-management-billing/)
- Gebruik de [prijs calculator van Azure](https://azure.microsoft.com/pricing/calculator/) om uw implementaties te plannen.
- Zie [virtuele machines in azure controleren](../azure-monitor/insights/monitor-vm-azure.md)voor meer informatie over het bewaken van uw VM.