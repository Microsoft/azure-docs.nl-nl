---
title: Migreren van AWS en andere platforms naar Managed Disks in azure
description: Maak Vm's in azure met behulp van Vhd's die zijn geüpload uit andere Clouds, zoals AWS of andere virtualisatieoplossingen en profiteer van Azure Managed Disks.
author: roygara
manager: twooley
ms.service: virtual-machines
ms.subervice: disks
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: conceptual
ms.date: 10/07/2017
ms.author: rogarana
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: c817528ff26dd10112eaf69c7ad20f8fb5813695
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102550396"
---
# <a name="migrate-from-amazon-web-services-aws-and-other-platforms-to-managed-disks-in-azure"></a>Migreren van Amazon Web Services (AWS) en andere platforms naar Managed Disks in azure

U kunt VHD-bestanden van AWS of on-premises virtualisatieoplossingen naar Azure uploaden om virtuele machines te maken die gebruikmaken van Managed Disks. Azure Managed Disks verwijdert de nood zaak om opslag accounts voor Azure IaaS-Vm's te beheren. U hoeft alleen het type (Premium of Standard) en de grootte van de schijf op te geven die u nodig hebt, en Azure maakt en beheert de schijf voor u. 

U kunt zowel gegeneraliseerde als speciale Vhd's uploaden. 
- **Gegeneraliseerde VHD** : er zijn al uw persoonlijke account gegevens verwijderd met behulp van Sysprep. 
- **Gespecialiseerde VHD** : onderhoudt de gebruikers accounts, toepassingen en andere status gegevens van de oorspronkelijke VM. 

> [!IMPORTANT]
> Voordat u een VHD naar Azure uploadt, moet u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md)
>
>


| Scenario                                                                                                                         | Documentatie                                                                                                                       |
|----------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------|
| U hebt bestaande AWS EC2-instanties die u wilt migreren naar Azure-Vm's met Managed disks                              | [Een virtuele machine verplaatsen van Amazon Web Services (AWS) naar Azure](aws-to-azure.md)                           |
| U hebt een virtuele machine van een ander virtualisatieplatform dat u als installatie kopie wilt gebruiken om meerdere virtuele machines van Azure te maken. | [Een gegeneraliseerde VHD uploaden en gebruiken om een nieuwe virtuele machine in azure te maken](upload-generalized-managed.md) |
| U hebt een unieke, aangepaste VM die u opnieuw wilt maken in Azure.                                                      | [Een gespecialiseerde VHD uploaden naar Azure en een nieuwe VM maken](create-vm-specialized.md)         |


## <a name="overview-of-managed-disks"></a>Overzicht van Managed Disks

Azure Managed Disks vereenvoudigt het beheer van VM'S door de nood zaak om opslag accounts te beheren te verwijderen. Managed Disks Profiteer ook van betere betrouw baarheid van Vm's in een Beschikbaarheidsset. Zo zorgt u ervoor dat de schijven van verschillende virtuele machines in een Beschikbaarheidsset voldoende van elkaar zijn geïsoleerd om een Single Point of Failure te voor komen. Er worden automatisch schijven van verschillende virtuele machines in een Beschikbaarheidsset in verschillende opslag schaal eenheden (stempels) geplaatst, waardoor de impact van de enkelvoudige opslag schaal eenheid wordt beperkt als gevolg van hardware-en software fouten.
Op basis van uw behoeften kunt u kiezen uit vier typen opslag opties. Zie voor meer informatie over de beschik bare schijf typen ons artikel [een schijf type selecteren](../disks-types.md).

## <a name="plan-for-the-migration-to-managed-disks"></a>Plannen voor de migratie naar Managed Disks

In deze sectie kunt u de beste beslissing nemen over VM-en schijf typen.

Als u van plan bent om te migreren van niet-beheerde schijven naar Managed disks, moet u er rekening mee houden dat gebruikers met de rol [Inzender voor virtuele machines](../../role-based-access-control/built-in-roles.md#virtual-machine-contributor) de VM-grootte niet kunnen wijzigen (zoals vóór de conversie). De reden hiervoor is dat de gebruiker van de virtuele machines van het besturings systeem de machtiging micro soft. Compute/disks/write moet hebben voor Vm's met Managed disks.

### <a name="location"></a>Locatie

Kies een locatie waar Azure Managed Disks beschikbaar zijn. Als u migreert naar Premium Managed Disks, moet u er ook voor zorgen dat Premium-opslag beschikbaar is in de regio waar u naar wilt migreren. Zie [Azure-Services per regio](https://azure.microsoft.com/regions/#services) voor actuele informatie over beschik bare locaties.

### <a name="vm-sizes"></a>Formaten van virtuele machines

Als u migreert naar Premium Managed Disks, moet u de grootte van de virtuele machine bijwerken naar Premium Storage geschikte grootte die beschikbaar is in de regio waarin de VM zich bevindt. Controleer de grootte van de virtuele machines die Premium Storage kunnen worden ondersteund. De Azure VM-grootte specificaties worden weer gegeven in [grootten voor virtuele machines](../sizes.md).
Bekijk de prestatie kenmerken van virtuele machines die met Premium Storage werken en kies de meest geschikte VM-grootte die het beste past bij uw werk belasting. Zorg ervoor dat er voldoende band breedte beschikbaar is op uw virtuele machine om het schijf verkeer te verzorgen.

### <a name="disk-sizes"></a>Schijf grootten

**Premium-Managed Disks**

Er zijn zeven soorten Premium Managed disks die kunnen worden gebruikt met uw virtuele machine en elk specifieke IOPs-en doorvoer limieten heeft. Houd rekening met deze beperkingen bij het kiezen van het Premium-schijf type voor uw virtuele machine op basis van de behoeften van uw toepassing in termen van capaciteit, prestaties, schaal baarheid en piek belasting.

| Type Premium-schijven  | P4    | P6    | P10   | P15   | P20   | P30   | P40   | P50   | 
|---------------------|-------|-------|-------|-------|-------|-------|-------|-------|
| Schijfgrootte           | 32 GB| 64 GB| 128 GB| 256 GB|512 GB | 1024 GB (1 TB)    | 2048 GB (2 TB)    | 4095 GB (4 TB)    | 
| IOP's per schijf       | 120   | 240   | 500   | 1100  |2300              | 5000              | 7500              | 7500              | 
| Doorvoer per schijf | 25 MB per seconde  | 50 MB per seconde  | 100 MB per seconde | 125 MB per seconde |150 MB per seconde | 200 MB per seconde | 250 MB per seconde | 250 MB per seconde |

**Standaard Managed Disks**

Er zijn zeven soorten standaard Managed disks die kunnen worden gebruikt met uw VM. Elk daarvan heeft verschillende capaciteit, maar heeft dezelfde IOPS-en doorvoer limieten. Kies het type standaard Managed disks op basis van de capaciteits behoeften van uw toepassing.

| Standard-schijftype  | S4               | S6               | S10              | S15              | S20              | S30              | S40              | S50              | 
|---------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------|------------------| 
| Schijfgrootte           | 30 GB            | 64 GB            | 128 GB           | 256 GB           |512 GB           | 1024 GB (1 TB)   | 2048 GB (2 TB)    | 4095 GB (4 TB)   | 
| IOP's per schijf       | 500              | 500              | 500              | 500              |500              | 500              | 500             | 500              | 
| Doorvoer per schijf | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde |60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 60 MB per seconde | 

### <a name="disk-caching-policy"></a>Beleid voor schijf cache 

**Premium-Managed Disks**

Het cache beleid voor schijven is standaard *alleen-lezen* voor alle Premium-gegevens schijven en *lezen-schrijven* voor de Premium-besturingssysteem schijf die is gekoppeld aan de virtuele machine. Deze configuratie-instelling wordt aanbevolen om de optimale prestaties van de IOs van uw toepassing te garanderen. Voor schrijf bare of alleen-schrijven gegevens schijven (zoals SQL Server-logboek bestanden) schakelt u schijf cache, zodat u betere toepassings prestaties kunt krijgen.

### <a name="pricing"></a>Prijzen

Bekijk de [prijzen voor Managed disks](https://azure.microsoft.com/pricing/details/managed-disks/). De prijzen voor Premium Managed Disks hetzelfde zijn als de Premium unmanaged disks. Prijzen voor standaard Managed Disks verschillen van standaard niet-beheerde schijven.


## <a name="next-steps"></a>Volgende stappen

- Voordat u een VHD naar Azure uploadt, moet u [een Windows-VHD of VHDX voorbereiden om te uploaden naar Azure](prepare-for-upload-vhd-image.md)