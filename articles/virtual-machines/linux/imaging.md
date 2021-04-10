---
title: Overzicht van het maken van Linux-installatiekopieën voor Azure
description: Hoe uw eigen Linux VM-installatiekopieën gebruikt of nieuwe installatiekopieën maakt voor gebruik in Azure.
author: danielsollondon
ms.service: virtual-machines
ms.subservice: imaging
ms.collection: linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 06/22/2020
ms.author: danis
ms.reviewer: cynthn
ms.openlocfilehash: a77c2bc69418b821933c0b62674500f7a32e40a5
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102565203"
---
# <a name="bringing-and-creating-linux-images-in-azure"></a>Eigen inux-installatiekopieën gebruiken en maken in Azure

In dit overzicht worden de basisconcepten van installatiekopieën maken beschreven en wordt uitgelegd hoe u Linux-installatiekopieën kunt bouwen en gebruiken in Azure. Voordat u een aangepaste installatiekopie naar Azure gebruikt, moet u rekening houden met de typen en opties die voor u beschikbaar zijn.

In dit artikel bespreken we de beslissingen en vereisten van de installatiekopie en worden belangrijke concepten uitgelegd, zodat u deze kunt volgen en uw eigen aangepaste installatiekopieën kunnen maken die voldoen aan uw specificaties.

## <a name="difference-between-managed-disks-and-images"></a>Verschil tussen beheerde schijven en installatiekopieën


Met Azure kunt u een VHD naar het platform brengen om te gebruiken als [Beheerde schijf](../faq-for-disks.md#managed-disks) of gebruiken als bron voor een installatiekopie. 

Azure Managed Disks zijn enkelvoudige VHD's. U kunt een bestaande VHD nemen en hiervan een beheerde schijf maken, of zelf een lege beheerde schijf maken. U kunt VM's maken op basis van beheerde schijven door de schijf aan de virtuele machine toe te voegen, maar u kunt alleen een VHD met één virtuele machine gebruiken. U kunt geen eigenschappen van besturingssystemen wijzigen. Azure probeert nu de virtuele machine in te schakelen en te starten met die schijf. 

Azure-installatiekopieën kunnen bestaan uit meerdere besturingssysteemschijven en gegevensschijven. Wanneer u een beheerde installatiekopie gebruikt om een virtuele machine te maken, maakt het platform een kopie van de installatiekopie en gebruikt deze om de virtuele machine te maken, zodat de ondersteuning voor beheerde installatiekopieën de installatiekopie voor meerdere VM's opnieuw gebruikt. Azure biedt ook geavanceerde beheermogelijkheden voor installatiekopieën, zoals globale replicatie, en versiebeheer via [Shared Image Gallery](../shared-image-galleries.md). 



## <a name="generalized-and-specialized"></a>Gegeneraliseerd en gespecialiseerd

Azure biedt twee hoofdafbeeldingstypen, gegeneraliseerd en gespecialiseerd. De termen gegeneraliseerd en gespecialiseerd zijn oorspronkelijke Windows-begrippen, die zijn gemigreerd naar Azure. Deze typen bepalen hoe de virtuele machine door het platform wordt verwerkt wanneer deze wordt ingeschakeld. Beide typen hebben voor- en nadelen en vereisten. Voordat u aan de slag gaat, moet u weten welk type installatiekopie u nodig hebt. Hieronder vindt u een overzicht van de scenario's en typen die u moet kiezen:

| Scenario      | Type installatiekopie  | Opslagopties |
| ------------- |:-------------:| :-------------:| 
| Een installatiekopie maken die kan worden geconfigureerd voor gebruik door meerdere VM's, en ik kan de hostnaam instellen, een beheergebruiker toevoegen en andere taken uitvoeren eerste keer dat de computer wordt opgestart. | Gegeneraliseerd | Galerie met gedeelde installatiekopieën of zelfstandig beheerde installatiekopieën |
| Een installatiekopie maken van een VM-momentopname of een back-up | Gespecialiseerd |Gedeelde installatiekopiegalerie of een beheerde schijf |
| Snel een installatiekopie maken waarvoor geen configuratie nodig is voor het maken van meerdere VM's |Gespecialiseerd |Galerie met gedeelde installatiekopieën |


### <a name="generalized-images"></a>Gegeneraliseerde installatiekopieën

Een gegeneraliseerde installatiekopie is een installatiekopie waarvoor de installatie moet worden voltooid bij de eerste keer dat de computer wordt opgestart. Bij de eerste keer dat de computer wordt opgestart, kunt u bijvoorbeeld de hostnaam, beheergebruiker en andere VM-specifieke configuraties instellen. Dit is handig als u wilt dat de installatiekopie meerdere keren opnieuw wordt gebruikt en wanneer u parameters wilt doorgeven tijdens het maken. Als de gegeneraliseerde installatiekopie de Azure-agent bevat, worden de parameters door de agent verwerkt en krijgt platform een seintje dat de eerste configuratie is voltooid. Dit proces wordt [inrichting](./provisioning.md) genoemd. 

Voor het inrichten is vereist dat een inrichtingsprogramma is opgenomen in de installatiekopie. Er zijn twee inrichtingsprogramma’s:
- [Azure Linux Agent](../extensions/agent-linux.md)
- [cloud-init](./using-cloud-init.md)

Dit zijn [vereisten](./create-upload-generic.md) voor het maken van een installatiekopie.


### <a name="specialized-images"></a>Gespecialiseerde installatiekopieën
Dit zijn installatiekopieën die volledig zijn geconfigureerd en waarvoor geen VM- en speciale parameters zijn vereist; het platform zet de virtuele machine gewoon op, u moet de unieke aspecten binnen virtuele machine afhandelen, zoals het instellen van een hostnaam, om DNS-conflicten op hetzelfde VNET te voorkomen. 

Inrichtingsagenten zijn niet vereist voor deze installatiekopieën, maar u wilt mogelijk ook de mogelijkheden voor het afhandelen van extensies hebben. U kunt de Linux-agent installeren, maar de inrichtingsoptie uitschakelen. Hoewel u geen inrichtingsagent nodig hebt, moet de installatiekopie voldoen aan [vereisten](./create-upload-generic.md) voor Azure-installatiekopieën.


## <a name="image-storage-options"></a>Opties voor opslag van installatiekopieën
Wanneer u uw eigen Linux-installatiekopie gebruikt, hebt u twee opties:

- Beheerde installatiekopieën voor het maken van eenvoudige VM'S in een ontwikkelings- en testomgeving.
- [Gedeelde installatiekopiegalerie](../shared-image-galleries.md) voor het maken en delen van installatiekopieën op schaal.


### <a name="managed-images"></a>Beheerde installatiekopieën

Beheerde installatiekopieën kunnen worden gebruikt om meerdere VM's te maken, maar ze hebben veel beperkingen. Beheerde installatiekopieën kunnen alleen worden gemaakt op basis van een gegeneraliseerde bron (VM of VHD). Ze kunnen alleen worden gebruikt om virtuele machines te maken in dezelfde regio en kunnen niet worden gedeeld via abonnementen en tenants.

Beheerde installatiekopieën kunnen worden gebruikt voor ontwikkelings -en testomgevingen, waar u een aantal eenvoudige gegeneraliseerde installatiekopieën nodig hebt voor gebruik binnen één regio en abonnement. 

### <a name="azure-shared-image-gallery-sig"></a>Azure Shared Image Gallery (SIG)

[Gedeelde installatiekopiegalerieën](../shared-image-galleries.md) worden aanbevolen voor het maken, beheren en delen van afbeeldingen op grote schaal. Met de gedeelde installatiekopiegalerieën kunt u een structuur en organisatie rond uw installatiekopieën bouwen.  

- Ondersteuning voor gegeneraliseerde en gespecialiseerde installatiekopieën.
- Ondersteuning voor installatiekopieën van generatie 1 en 2.
- Wereldwijde replicatie van installatiekopieën.
- Versiebeheer en groepering van installatiekopieën voor eenvoudiger beheer.
- Uiterst beschikbare installatiekopieën met zone-redundante opslag (ZRS), in regio’s die beschikbaarheidszones ondersteunen. ZRS biedt betere flexibiliteit tegen zonegebonden fouten.
- U kunt delen tussen meerdere abonnementen en zelfs tussen AD-tenants (Active Directory) met behulp van Azure-RBAC.
- Het schalen van uw implementaties met replica's van installatiekopieën in elke regio.

Op hoog niveau maakt u een SIG en bestaat deze uit:
- Definities van installatiekopie: dit zijn containers die groepen installatiekopieën bevatten.
- Installatiekopieversies: dit zijn de werkelijke installatiekopieën



## <a name="hyper-v-generation"></a>Hyper-V generatie

Azure biedt ondersteuning voor Hyper-V generatie 1 (gen1) en generatie 2 (Gen2), Gen2 is de nieuwste generatie en biedt extra functionaliteit die Gen1 niet heeft. Bijvoorbeeld: groter geheugen, Intel-software Guard Extensions (Intel SGX) en gevirtualiseerd permanent geheugen (vPMEM). Virtuele machines van generatie 2 kunnen on-premises worden uitgevoerd, hebben enkele functies die nog niet worden ondersteund in Azure. Zie de sectie Functies en mogelijkheden voor meer informatie. Raadpleeg [dit artikel](../generation-2.md) voor meer informatie. Maak Gen2-installatiekopieën als u de extra functionaliteit nodig hebt.

Als u nog een eigen installatiekopie moet maken, controleert u of deze voldoet aan de [vereisten voor installatiekopieën](./create-upload-generic.md) en uploadt u deze naar Azure. Distributiespecifieke vereisten:


- [CentOS-distributies](create-upload-centos.md)
- [Debian Linux](debian-create-upload-vhd.md)
- [Flatcar Container Linux](flatcar-create-upload-vhd.md)
- [Oracle Linux](oracle-create-upload-vhd.md)
- [Red Hat Enterprise Linux](redhat-create-upload-vhd.md)
- [SLES en OpenSUSE](suse-create-upload-vhd.md)
- [Ubuntu](create-upload-ubuntu.md)


## <a name="next-steps"></a>Volgende stappen

Meer informatie over hoe u een [Gedeelde installatiekopiegalerie](tutorial-custom-images.md) kunt maken.