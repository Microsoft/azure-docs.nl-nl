---
title: Platform updates voor de Azure VMware-oplossing
description: Meer informatie over de platform updates voor de Azure VMware-oplossing.
ms.topic: reference
ms.date: 03/24/2021
ms.openlocfilehash: da6317d49edd3f40e1a8f2518f91fe353bbae285
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105045209"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Platform updates voor de Azure VMware-oplossing

De Azure VMware-oplossing is van toepassing op belang rijke updates vanaf maart 2021. U ontvangt een melding via Azure Service Health die de tijd lijn van het onderhoud bevat. Zie [Azure VMware-oplossing persoonlijke Cloud updates en upgrades](concepts-upgrades.md)voor meer informatie.

## <a name="march-24-2021"></a>24 maart 2021
Alle nieuwe persoonlijke Clouds van Azure VMware-oplossingen worden geïmplementeerd met VMware vCenter versie 6,7 U3l en NSX-T versie 3.1.1. Bestaande persoonlijke Clouds worden bijgewerkt en geüpgraded **tot en met 2021 juni** tot de hierboven vermelde releases.

U ontvangt een e-mail met de datum en tijd van de geplande onderhouds werkzaamheden. U kunt een upgrade opnieuw plannen. Het e-mail bericht bevat ook informatie over het bijgewerkte onderdeel, het effect op werk belastingen, toegang tot privécloud en andere Azure-Services.  Een uur voordat de upgrade wordt uitgevoerd, ontvangt u een melding en vervolgens opnieuw wanneer deze is voltooid.

## <a name="march-15-2021"></a>15 maart 2021 

- Het onderhouds service van Azure VMware **-oplossingen werkt tot en met 19 maart 2021** om de vCenter-Server in uw privécloud bij te werken naar VCenter Server 6,7 update 3l-versie.

- VMware vCenter is op dit moment niet beschikbaar.  U kunt uw Vm's dus niet beheren (stoppen, starten, maken, verwijderen) of een persoonlijke Cloud schalen (het toevoegen of verwijderen van servers en clusters). VMware-hoge Beschik baarheid (HA) blijft echter actief om bestaande Vm's te beveiligen. 
 
Zie [VMware vCenter Server 6,7 update 3L release opmerkingen](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)voor meer informatie over deze vCenter-versie.

## <a name="march-4-2021"></a>4 maart 2021

- Met de Azure VMware-oplossing wordt de [VMware ESXi 6,7, patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) toegepast op bestaande privés **tot en met 15 maart 2021**.

- Gedocumenteerde tijdelijke oplossingen voor de vSphere-stack, zoals per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), worden ook toegepast **tot en met 15 maart 2021**.

>[!NOTE]
>Dit is niet storend en heeft geen invloed op Azure VMware-Services of-workloads. Tijdens het onderhoud worden verschillende VMware-waarschuwingen, zoals het verlies van de _netwerk verbinding op DVPorts_ en de _verloren gegane redundantie van de uplink op DVPorts_, weer gegeven in vCenter en worden ze automatisch gewist wanneer het onderhoud wordt uitgevoerd.

## <a name="post-update"></a>Update plaatsen
Zodra het proces is voltooid, worden nieuwere versies van VMware-onderdelen weer gegeven. Als u problemen ondervindt of vragen hebt, neemt u contact op met ons ondersteunings team door een ondersteunings ticket te openen.