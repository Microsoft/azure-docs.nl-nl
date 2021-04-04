---
title: 'Azure VMware-oplossing door CloudSimple: een virtuele machine maken in azure met VM-sjablonen'
description: Hierin wordt beschreven hoe u virtuele machines in azure maakt met behulp van VM-sjablonen in de VMware-infra structuur voor uw CloudSimple-Privécloud
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: b01afe60a78a746eb0dc5f03cc7b45989f8cf81e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97898756"
---
# <a name="create-a-virtual-machine-in-azure-using-vm-templates-on-the-vmware-infrastructure"></a>Een virtuele machine maken in azure met behulp van VM-sjablonen op de VMware-infra structuur

U kunt een virtuele machine maken in de Azure Portal met behulp van VM-sjablonen op de VMware-infra structuur die uw CloudSimple-beheerder heeft ingeschakeld voor uw abonnement.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de [Azure-portal](https://portal.azure.com).

## <a name="create-cloudsimple-virtual-machine"></a>Een virtuele CloudSimple-machine maken

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple-virtual machines**.

3. Klik op **Add**.

    ![Een virtuele CloudSimple-machine maken](media/create-cloudsimple-virtual-machine.png)

4. Voer basis informatie in Klik op **volgende: grootte**.

    > [!NOTE]
    > Voor het maken van virtuele CloudSimple-machines in Azure is een VM-sjabloon vereist.  Deze VM-sjabloon moet bestaan in uw Privécloud.  Maak een virtuele machine in uw Privécloud vanuit de vCenter-gebruikers interface met de gewenste besturings systemen en configuraties.  Instructies gebruiken [om een virtuele machine te klonen naar een sjabloon in de vSphere-webclient](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-FE6DE4DF-FAD0-4BB0-A1FD-AFE9A40F4BFE_copy.html), een sjabloon maken.

    ![CloudSimple virtual machine maken-basis beginselen](media/create-cloudsimple-virtual-machine-basic-info.png)

    | Veld | Beschrijving |
    | ------------ | ------------- |
    | Abonnement | Het Azure-abonnement dat is gekoppeld aan uw Privécloud.  |
    | Resourcegroep | De resource groep waaraan de virtuele machine wordt toegewezen. U kunt een bestaande groep selecteren of een nieuwe maken. |
    | Name | Naam voor het identificeren van de virtuele machine.  |
    | Locatie | De Azure-regio waarin deze VM wordt gehost.  |
    | Privécloud | CloudSimple de Privécloud waar u de virtuele machine wilt maken. |
    | Resource groep | Toegewezen resource groep voor de virtuele machine. Selecteer een van de beschik bare resource groepen. |
    | vSphere-sjabloon | vSphere-sjabloon voor de virtuele machine.  |
    | Gebruikersnaam | De gebruikers naam van de VM-beheerder (voor Windows-sjablonen)|
    | Wachtwoord <br>Wachtwoord bevestigen | Wacht woord voor de VM-beheerder (voor Windows-sjablonen).  |

5. Selecteer het aantal kernen en geheugen capaciteit voor de virtuele machine en klik op **volgende: configuraties**. Schakel het selectie vakje in als u volledige CPU-virtualisatie wilt weer geven voor het gast besturingssysteem zodat toepassingen waarvoor hardwarevirtualisatie is vereist, kunnen worden uitgevoerd op virtuele machines zonder binaire omzetting of paravirtualisatie. Zie voor meer informatie het VMware-artikel een door [VMware ondersteunde virtualisatie beschikbaar](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-2A98801C-68E8-47AF-99ED-00C63E4857F6.html)maken.

    ![CloudSimple virtuele machine maken-grootte](media/create-cloudsimple-virtual-machine-size.png)

6. Configureer netwerk interfaces en schijven zoals beschreven in de volgende tabellen en klik op **beoordelen + maken**.

    ![CloudSimple virtuele machine maken-configuraties](media/create-cloudsimple-virtual-machine-configurations.png)

    Voor netwerk interfaces klikt u op **netwerk interface toevoegen** en configureert u de volgende instellingen.

    | Beheer | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de interface te identificeren.  |
    | Netwerk | Selecteer in de lijst met geconfigureerde gedistribueerde poort groep in uw Privécloud vSphere.  |
    | Adapter | Selecteer een vSphere-adapter in de lijst met beschik bare typen die voor de virtuele machine zijn geconfigureerd. Zie het artikel over VMware Knowledge Base voor meer informatie. [Kies een netwerk adapter voor de virtuele machine](https://kb.vmware.com/s/article/1001805). |
    | Inschakelen bij opstarten | Kies of u de NIC-hardware wilt inschakelen wanneer de virtuele machine wordt opgestart. De standaard instelling is **ingeschakeld**. |

    Voor schijven, klikt u op **schijf toevoegen** en configureert u de volgende instellingen.

    | Item | Beschrijving |
    | ------------ | ------------- |
    | Name | Voer een naam in om de schijf aan te duiden.  |
    | Grootte | Selecteer een van de beschik bare grootten.  |
    | SCSI-controller | Selecteer een SCSI-controller voor de schijf.  |
    | Modus | Hiermee wordt bepaald hoe de schijf deel uitmaakt van moment opnamen. Kies een van de volgende opties: <br> -Onafhankelijk permanent: alle gegevens die naar de schijf worden geschreven, worden permanent geschreven.<br> -Onafhankelijk niet-persistent: wijzigingen die naar de schijf worden geschreven, worden genegeerd wanneer u de virtuele machine uitschakelt of opnieuw instelt.  Met een onafhankelijke, niet-permanente modus kunt u de virtuele machine in dezelfde staat altijd opnieuw opstarten. Raadpleeg de [VMware-documentatie](https://docs.vmware.com/en/VMware-vSphere/6.5/com.vmware.vsphere.vm_admin.doc/GUID-8B6174E6-36A8-42DA-ACF7-0DA4D8C5B084.html)voor meer informatie.

7. Nadat de validatie is voltooid, controleert u de instellingen en klikt u op **maken**. Als u wijzigingen wilt aanbrengen, klikt u op de tabbladen bovenaan of klikt u op.

    ![CloudSimple virtuele machine maken-controleren](media/create-cloudsimple-virtual-machine-review.png)

## <a name="view-list-of-cloudsimple-virtual-machines"></a>Een lijst met virtuele CloudSimple-machines weer geven

1. Selecteer **Alle services**.

2. Zoek naar **CloudSimple-virtual machines**.

3. Selecteer de waarop uw Privécloud is gemaakt.

    ![Lijst met CloudSimple-Virtual Machines](media/list-cloudsimple-virtual-machines.png)

Een lijst met virtuele CloudSimple-machines bevat virtuele machines die zijn gemaakt op basis van Azure Portal.  Virtuele machines die zijn gemaakt in de Privécloud-vCenter in de toegewezen vCenter-resource groep, worden weer gegeven in de lijst.  
