---
title: Geneste virtualisatie inschakelen op een sjabloon-VM in Azure Lab Services | Microsoft Docs
description: In dit artikel leert u hoe u een geneste virtualisatie kunt instellen op een sjabloon machine in Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 319695088e967dc3156ecab5c1b9458e77fcc186
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91251471"
---
# <a name="enable-nested-virtualization-on-a-template-virtual-machine-in-azure-lab-services"></a>Geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services

Op dit moment kunt u met Azure Lab Services één sjabloon virtuele machine in een Lab instellen en één exemplaar beschikbaar maken voor elk van uw gebruikers. Als u een docenten netwerk, beveiliging of IT-klassen hebt, moet u mogelijk elk van uw studenten een omgeving bieden waarin meerdere virtuele machines via een netwerk met elkaar kunnen communiceren.

Met geneste virtualisatie kunt u een multi-VM-omgeving maken in de virtuele machine van een lab. Als u de sjabloon publiceert, wordt elke gebruiker in het lab voorzien van een virtuele machine die is ingesteld met meerdere virtuele machines in de test omgeving.  In dit artikel wordt beschreven hoe u een geneste virtualisatie instelt op een sjabloon machine in Azure Lab Services.

## <a name="what-is-nested-virtualization"></a>Wat is geneste virtualisatie?

Met geneste virtualisatie kunt u virtuele machines maken binnen een virtuele machine. Geneste virtualisatie wordt uitgevoerd via Hyper-V en is alleen beschikbaar op Windows-Vm's.

Raadpleeg de volgende artikelen voor meer informatie over geneste virtualisatie:

- [Geneste virtualisatie in azure](https://azure.microsoft.com/blog/nested-virtualization-in-azure/)
- [Geneste virtualisatie inschakelen in een Azure VM](../virtual-machines/windows/nested-virtualization.md)

## <a name="considerations"></a>Overwegingen

Voordat u een Lab met geneste virtualisatie instelt, zijn hier enkele dingen die u in overweging moet nemen.

- Wanneer u een nieuw Lab maakt, selecteert u **medium (geneste virtualisatie)** of **groot (geneste virtualisatie)** grootten voor de grootte van de virtuele machine. Deze virtuele-machine grootten ondersteunen geneste virtualisatie.
- Kies een grootte die goede prestaties biedt voor zowel de host als de virtuele machine van de client.  Houd er rekening mee dat bij gebruik van virtualisatie de grootte die u kiest, voldoende is voor niet meer dan één computer, maar de host en alle Hyper-V-machines die gelijktijdig worden uitgevoerd.
- Virtuele machines van de client hebben geen toegang tot Azure-resources, zoals DNS-servers, in het virtuele Azure-netwerk.
- Voor de virtuele machine van de host is installatie vereist zodat de client computer verbinding met Internet kan hebben.
- Virtuele machines van de client worden als onafhankelijke computers in licentie gegeven. Zie [micro soft-licentie verlening](https://www.microsoft.com/licensing/default) voor informatie over licenties voor micro soft-besturings systemen en-producten. Controleer de licentie overeenkomsten op alle andere software die wordt gebruikt voordat u de sjabloon machine instelt.

## <a name="enable-nested-virtualization-on-a-template-vm"></a>Geneste virtualisatie inschakelen in een sjabloon-VM

In dit artikel wordt ervan uitgegaan dat u een Lab-account en Lab hebt gemaakt.  Zie [zelf studie voor het instellen van een Lab](tutorial-setup-lab-account.md)-account voor meer informatie over het maken van een nieuw Lab-account. Voor meer informatie over het maken van een Lab raadpleegt u [een klassikale-Lab-zelf studie instellen](tutorial-setup-classroom-lab.md).

>[!IMPORTANT]
>Selecteer **groot (geneste virtualisatie)** of **Gemiddeld (geneste virtualisatie)** voor de grootte van de virtuele machine bij het maken van het lab.  Geneste virtualisatie werkt anders niet.  

Zie [een leslokaal sjabloon maken en beheren](how-to-create-manage-template.md)als u verbinding wilt maken met de sjabloon machine.

Voor het inschakelen van geneste virtualisatie zijn er een aantal taken die u kunt uitvoeren.  

- **Schakel de Hyper-V-functie in**. De Hyper-V-rol moet zijn ingeschakeld voor het maken en uitvoeren van virtuele Hyper-V-machines op de virtuele machine van de Lab-Services.
- **Schakel DHCP in**.  Wanneer de virtuele machine van de test services-functie is ingeschakeld, kan aan de virtuele Hyper-V-machines automatisch een IP-adres worden toegewezen.
- **Maak een NAT-netwerk voor virtuele Hyper-V-machines**.  Het NAT-netwerk is zo ingesteld dat de virtuele Hyper-V-machines toegang hebben tot internet.  De Hyper-V virtuele machines kunnen met elkaar communiceren.

>[!NOTE]
>Met het NAT-netwerk dat is gemaakt op de Lab Services-VM kan een Hyper-V-VM toegang krijgen tot het internet en andere Hyper-V-Vm's op dezelfde Lab Services-VM.  De Hyper-V-VM kan geen toegang krijgen tot Azure-resources, zoals DNS-servers, in het virtuele Azure-netwerk.

Het uitvoeren van de hierboven vermelde taken kan worden uitgevoerd met behulp van een script of met behulp van Windows-hulpprogram ma's.  Lees de onderstaande secties voor meer informatie.

### <a name="using-script-to-enable-nested-virtualization"></a>Script gebruiken om geneste virtualisatie in te scha kelen

Zie [geneste virtualisatie inschakelen op een virtuele machine van een sjabloon in Azure Lab Services met behulp van een script](how-to-enable-nested-virtualization-template-vm-using-script.md)om de geautomatiseerde installatie voor geneste virtualisatie met windows server 2016 of windows server 2019 te gebruiken. U gebruikt scripts uit de [hyper-v-scripts van Lab-Services](https://github.com/Azure/azure-devtestlab/tree/master/samples/ClassroomLabs/Scripts/HyperV) om de hyper-v-functie te installeren.  Met de scripts wordt ook netwerken ingesteld, zodat de virtuele Hyper-V-machines toegang hebben tot internet.

### <a name="using-windows-tools-to-enable-nested-virtualization"></a>Windows-hulpprogram ma's gebruiken om geneste virtualisatie in te scha kelen

Met de installatie van geneste virtualisatie voor Windows Server 2016 of Windows Server 2019 met behulp van Windows-rollen en-beheer Programma's raadpleegt u [geneste virtualisatie op een sjabloon virtuele machine in Azure Lab Services hand matig inschakelen](how-to-enable-nested-virtualization-template-vm-ui.md).  Instructies hebben ook betrekking op het instellen van netwerken, zodat de virtuele Hyper-V-machines toegang hebben tot internet.
