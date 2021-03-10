---
title: Een Windows AWS EC2-exemplaar verplaatsen naar Azure
description: Verplaats een Amazon Web Services (AWS) EC2 Windows-exemplaar naar een virtuele machine van Azure.
author: cynthn
ms.service: virtual-machines
ms.collection: windows
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/01/2018
ms.author: cynthn
ms.openlocfilehash: 86d3e67b121bf7d66651e1e823e5f137e918a171
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102550787"
---
# <a name="move-a-windows-vm-from-amazon-web-services-aws-to-an-azure-virtual-machine"></a>Een Windows-VM verplaatsen van Amazon Web Services (AWS) naar een virtuele machine van Azure

Als u virtuele machines van Azure evalueert om uw workloads te hosten, kunt u een bestaande Amazon Web Services (AWS) EC2 Windows VM-exemplaar exporteren en vervolgens de virtuele harde schijf (VHD) uploaden naar Azure. Zodra de VHD is geüpload, kunt u in azure een nieuwe virtuele machine maken op basis van de VHD. 

In dit artikel wordt beschreven hoe u één VM verplaatst van AWS naar Azure. Als u Vm's op schaal wilt verplaatsen van AWS naar Azure, raadpleegt u [virtual machines in Amazon Web Services (AWS) migreren naar Azure met Azure site Recovery](../../site-recovery/migrate-tutorial-aws-azure.md).

## <a name="prepare-the-vm"></a>De virtuele machine voorbereiden 
 
U kunt zowel gegeneraliseerde als gespecialiseerde Vhd's uploaden naar Azure. Elk type vereist dat u de virtuele machine voorbereidt voordat u exporteert vanuit AWS. 

- **Gegeneraliseerde VHD** : voor een gegeneraliseerde VHD zijn al uw persoonlijke account gegevens verwijderd met behulp van Sysprep. Als u van plan bent om de VHD te gebruiken als een installatie kopie om nieuwe Vm's te maken op basis van, moet u het volgende doen: 
 
    * [Bereid een Windows-VM](prepare-for-upload-vhd-image.md)voor.  
    * Generaliseer de virtuele machine met behulp van Sysprep.  

 
- **Gespecialiseerde VHD** : met een gespecialiseerde VHD worden de gebruikers accounts, toepassingen en andere status gegevens van de oorspronkelijke virtuele machine beheerd. Als u van plan bent om de VHD te gebruiken om een nieuwe virtuele machine te maken, moet u ervoor zorgen dat de volgende stappen zijn uitgevoerd.  
    * [Bereid een Windows VHD voor om te uploaden naar Azure](prepare-for-upload-vhd-image.md). Generaliseer de virtuele machine **niet** met behulp van Sysprep. 
    * Verwijder alle hulpprogram ma's voor gast-virtualisatie en de agents die zijn geïnstalleerd op de VM (bijvoorbeeld VMware-hulpprogram ma's). 
    * Zorg ervoor dat de virtuele machine is geconfigureerd om het IP-adres en de DNS-instellingen via DHCP te halen. Dit zorgt ervoor dat de server een IP-adres binnen het VNet verkrijgt wanneer het wordt gestart.  


## <a name="export-and-download-the-vhd"></a>De VHD exporteren en downloaden 

Exporteer het EC2-exemplaar naar een VHD in een Amazon S3-Bucket. Volg de stappen in het artikel in de Amazon-documentatie om [een exemplaar te exporteren als een virtuele machine met behulp van import/export van de VM](https://docs.aws.amazon.com/vm-import/latest/userguide/vmexport.html) en voer de opdracht [Create-instance-export-Task](https://docs.aws.amazon.com/cli/latest/reference/ec2/create-instance-export-task.html) uit om het EC2-exemplaar te exporteren naar een VHD-bestand. 

Het geëxporteerde VHD-bestand wordt opgeslagen in de Amazon S3-Bucket die u opgeeft. De basis syntaxis voor het exporteren van de VHD vindt u hieronder. Vervang alleen de tijdelijke aanduiding voor tekst in \<brackets> met uw gegevens.

```
aws ec2 create-instance-export-task --instance-id <instanceID> --target-environment Microsoft \
  --export-to-s3-task DiskImageFormat=VHD,ContainerFormat=ova,S3Bucket=<bucket>,S3Prefix=<prefix>
```

Nadat de VHD is geëxporteerd, volgt u de instructies in [Hoe kan ik een object downloaden van een S3-Bucket?](https://docs.aws.amazon.com/AmazonS3/latest/user-guide/download-objects.html) het VHD-bestand downloaden van de S3-Bucket. 

> [!IMPORTANT]
> Kosten voor AWS kosten voor het downloaden van de VHD worden door gegeven. Zie de [prijzen voor Amazon S3](https://aws.amazon.com/s3/pricing/) voor meer informatie.


## <a name="next-steps"></a>Volgende stappen

U kunt nu de VHD uploaden naar Azure en een nieuwe VM maken. 

- Zie [een gegeneraliseerde VHD uploaden en deze gebruiken voor het maken van een nieuwe virtuele machine in azure](upload-generalized-managed.md) als u Sysprep op uw bron hebt uitgevoerd om deze te **generaliseren** voordat u het bestand exporteert.
- Als u Sysprep niet hebt uitgevoerd vóór het exporteren, wordt de VHD als **gespecialiseerd** beschouwd. Zie [een gespecialiseerde VHD uploaden naar Azure en een nieuwe VM maken](create-vm-specialized.md)

 
