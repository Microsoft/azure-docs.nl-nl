---
title: Platform updates voor de Azure VMware-oplossing
description: Meer informatie over de platform updates voor de Azure VMware-oplossing.
ms.topic: reference
ms.date: 03/16/2021
ms.openlocfilehash: 4f4c697f345cca093a83eab2f915aaf9e80ab10f
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563126"
---
# <a name="platform-updates-for-azure-vmware-solution"></a>Platform updates voor de Azure VMware-oplossing

Belang rijke updates voor de Azure VMware-oplossing worden vanaf maart 2021 van kracht. U ontvangt een melding via Azure Service Health die de tijd lijn van het onderhoud bevat. In dit artikel wordt beschreven wat u kunt verwachten tijdens deze onderhouds bewerking en wijzigingen in uw privécloud.

## <a name="march-15-2021"></a>15 maart 2021 

- De Azure VMware-oplossings service zal onderhouds werkzaamheden uitvoeren om de vCenter-Server in uw privécloud bij te werken naar vCenter Server 6,7 update 3l-versie tot en met 19 maart 2021.

- Gedurende deze tijd is VMware vCenter niet beschikbaar en kunt u geen Vm's beheren (stoppen, starten, maken, verwijderen). VMware hoge Beschik baarheid (HA) blijft actief voor het bieden van beveiliging voor bestaande Vm's. Het schalen van de privécloud (het toevoegen of verwijderen van servers en clusters) is ook niet beschikbaar.
 
Zie [VMware vCenter Server 6,7 update 3L release opmerkingen](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/vsphere-vcenter-server-67u3l-release-notes.html)voor meer informatie over deze vCenter-versie.

## <a name="march-4-2021"></a>4 maart 2021

- In azure VMware-oplossingen worden patches toegepast op ESXi in bestaande persoonlijke Clouds tot [VMware ESXi 6,7, patch release ESXi670-202011002](https://docs.vmware.com/en/VMware-vSphere/6.7/rn/esxi670-202011002.html) tot en met 15 maart 2021.

- Gedocumenteerde tijdelijke oplossingen voor de vSphere-stack, zoals per [VMSA-2021-0002](https://www.vmware.com/security/advisories/VMSA-2021-0002.html), worden ook toegepast tot en met 15 maart 2021.

>[!NOTE]
>Dit is niet storend en heeft geen invloed op Azure VMware-Services of-workloads. Tijdens het onderhoud worden verschillende VMware-waarschuwingen, zoals het verlies van de _netwerk verbinding op DVPorts_ en de _verloren gegane redundantie van de uplink op DVPorts_, weer gegeven in vCenter en worden ze automatisch gewist wanneer het onderhoud wordt uitgevoerd.

## <a name="post-update"></a>Update plaatsen
Zodra het proces is voltooid, worden nieuwere versies van VMware-onderdelen weer gegeven. Als u problemen ondervindt of vragen hebt, neemt u contact op met ons ondersteunings team door een ondersteunings ticket te openen.



