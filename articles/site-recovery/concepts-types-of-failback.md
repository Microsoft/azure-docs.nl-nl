---
title: Failback tijdens nood herstel met Azure Site Recovery | Microsoft Docs
description: In dit artikel vindt u een overzicht van de verschillende soorten failback en voor behoud die moeten worden overwogen tijdens het uitvoeren van een failover naar on-premises tijdens nood herstel met de Azure Site Recovery-service.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: conceptual
ms.date: 08/07/2019
ms.author: raynew
ms.openlocfilehash: c0eaf28f9aeb4050fd35a6036a53e3e91d00f3eb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "84691081"
---
# <a name="failback-of-vmware-vms-after-disaster-recovery-to-azure"></a>Failback van virtuele VMware-machines na herstel na noodgeval naar Azure

Nadat u een failover hebt uitgevoerd naar Azure als onderdeel van uw nood herstel proces, kunt u een failback uitvoeren naar uw on-premises site. Er zijn twee verschillende soorten failback die mogelijk zijn met Azure Site Recovery: 

- Een failback uitvoeren naar de oorspronkelijke locatie 
- Failback naar een alternatieve locatie

Als u een failover van een virtuele VMware-machine hebt uitgevoerd, kunt u een failback uitvoeren naar dezelfde bron on-premises virtuele machine als deze nog bestaat. In dit scenario worden alleen de wijzigingen teruggerepliceerd. Dit scenario staat bekend als **herstel van de oorspronkelijke locatie**. Als de on-premises virtuele machine niet bestaat, is het scenario een **alternatieve locatie herstel**.

> [!NOTE]
> U kunt alleen een failback uitvoeren naar de oorspronkelijke vCenter-en configuratie server. U kunt geen nieuwe configuratie server implementeren en failback gebruiken. U kunt ook geen nieuwe vCenter toevoegen aan de bestaande configuratie server en failback in de nieuwe vCenter.

## <a name="original-location-recovery-olr"></a>Herstel van de oorspronkelijke locatie (herstellen)
Als u een failback naar de oorspronkelijke virtuele machine wilt uitvoeren, moet aan de volgende voor waarden worden voldaan:

* Als de virtuele machine wordt beheerd door een vCenter-Server, moet de ESX-host van het hoofd doel toegang hebben tot de gegevens opslag van de virtuele machine.
* Als de virtuele machine zich op een ESX-host bevindt, maar niet wordt beheerd door vCenter, moet de vaste schijf van de virtuele machine zich in een gegevens archief bekomen waartoe de host van het hoofd doel toegang heeft.
* Als uw virtuele machine zich op een ESX-host bevindt en geen vCenter gebruikt, moet u de detectie van de ESX-host van het hoofd doel volt ooien voordat u de beveiliging opnieuw beveiligt. Dit geldt ook als u geen fysieke back-upservers meer hebt.
* Als de schijven al bestaan en zijn verbonden met de on-premises virtuele machine, kunt u een failback uitvoeren naar een virtuele Storage Area Network (vSAN) of een schijf die is gebaseerd op RDM (raw device mapping).

> [!IMPORTANT]
> Het is belang rijk om schijf. enableUUID = TRUE in te scha kelen, zodat tijdens de failback de Azure Site Recovery-service de oorspronkelijke VMDK kan identificeren op de virtuele machine waarnaar de in behandeling zijnde wijzigingen worden geschreven. Als deze waarde niet is ingesteld op TRUE, probeert de service de bijbehorende on-premises VMDK te identificeren op basis van de beste poging. Als de juiste VMDK niet wordt gevonden, wordt er een extra schijf gemaakt en worden er gegevens naar worden geschreven.

## <a name="alternate-location-recovery-alr"></a>Herstel naar een andere locatie (ALR)
Als de on-premises virtuele machine niet bestaat voordat u de virtuele machine opnieuw beveiligt, wordt het scenario een alternatieve locatie herstel genoemd. De werk stroom opnieuw beveiligen maakt de on-premises virtuele machine opnieuw. Dit zorgt er ook voor dat de volledige gegevens worden gedownload.

* Wanneer u een failback naar een andere locatie gebruikt, wordt de virtuele machine hersteld naar dezelfde ESX-host waarop de hoofddoel server is geïmplementeerd. De gegevens opslag die wordt gebruikt voor het maken van de schijf, is dezelfde gegevens opslag die is geselecteerd bij het opnieuw beveiligen van de virtuele machine.
* U kunt alleen een failback uitvoeren naar een VMFS (Virtual Machine File System) of vSAN Data Store. Als u een RDM hebt, is het opnieuw beveiligen en failback niet mogelijk.
* Opnieuw beveiligen houdt in dat er één grote initiële gegevens overdracht wordt uitgevoerd, gevolgd door de wijzigingen. Dit proces bestaat omdat de virtuele machine niet on-premises bestaat. De volledige gegevens moeten worden gerepliceerd. Deze herbeveiliging neemt ook meer tijd in beslag dan het herstel van de oorspronkelijke locatie.
* U kunt geen failback uitvoeren op schijven op basis van RDM. Alleen nieuwe virtuele-machine schijven (Vmdk's) kunnen worden gemaakt op een VMFS/vSAN-gegevens opslag.

> [!NOTE]
> Een fysieke machine, wanneer failover naar Azure wordt uitgevoerd, kan alleen als een virtuele VMware-machine worden hersteld. Dit volgt dezelfde werk stroom als het herstel van de alternatieve locatie. Zorg ervoor dat u ten minste één hoofddoel server detecteert en de benodigde ESX/ESXi-hosts waarvoor u een failback moet uitvoeren.

## <a name="next-steps"></a>Volgende stappen

Volg de stappen om de [failback-bewerking](vmware-azure-failback.md)uit te voeren.

