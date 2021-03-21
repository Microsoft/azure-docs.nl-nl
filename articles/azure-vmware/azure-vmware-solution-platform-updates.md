---
title: Platform updates voor de Azure VMware-oplossing
description: Meer informatie over de platform updates voor de Azure VMware-oplossing.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 73bd1d088f9055ebd80a28c6247ea9dfa6229093
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104586230"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Platform updates voor de Azure VMware-oplossing

Belang rijke updates voor de Azure VMware-oplossing worden vanaf maart 2021 van kracht. U ontvangt een melding via Azure Service Health die de tijd lijn van het onderhoud bevat. Zie [Azure VMware-oplossing persoonlijke Cloud updates en upgrades](concepts-upgrades.md)voor meer informatie over de belangrijkste upgrade processen en functies in de Azure VMware-oplossing.

## <a name="march-15-2021"></a>15 maart 2021 

- Met de Azure VMware-oplossings service wordt het onderhouds werk tot en met 19 maart 20201 uitgevoerd om de vCenter-Server in uw privécloud bij te werken naar de 3l-versie van vCenter Server 6,7-update.

- Gedurende deze tijd is VMware vCenter niet beschikbaar en kunt u geen Vm's beheren (stoppen, starten, maken, verwijderen). Het schalen van de privécloud (het toevoegen of verwijderen van servers en clusters) is ook niet beschikbaar. VMware hoge Beschik baarheid (HA) blijft actief voor het bieden van beveiliging voor bestaande Vm's. 
 
Zie [VMware vCenter Server 6,7 update 3L release opmerkingen](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)voor meer informatie over deze vCenter-versie.

## <a name="march-4-2021"></a>4 maart 2021

- Met Azure VMware-oplossingen worden patches toegepast tot en met 15 maart 2021 tot ESXi in bestaande persoonlijke Clouds tot [VMware ESXi 6,7, patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html).

- Gedocumenteerde tijdelijke oplossingen voor de vSphere-stack, zoals per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), worden ook toegepast tot en met 15 maart 2021.

>[!NOTE]
>Dit is niet storend en heeft geen invloed op Azure VMware-Services of-workloads. Tijdens het onderhoud worden verschillende VMware-waarschuwingen, zoals het verlies van de _netwerk verbinding op DVPorts_ en de _verloren gegane redundantie van de uplink op DVPorts_, weer gegeven in vCenter en worden ze automatisch gewist wanneer het onderhoud wordt uitgevoerd.

## <a name="post-update"></a>Update plaatsen
Zodra het proces is voltooid, worden nieuwere versies van VMware-onderdelen weer gegeven. Als u problemen ondervindt of vragen hebt, neemt u contact op met ons ondersteunings team door een ondersteunings ticket te openen.





