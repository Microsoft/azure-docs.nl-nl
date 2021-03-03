---
title: Azure-ondersteuning voor virtuele machines van de 2e generatie
description: Overzicht van Azure-ondersteuning voor virtuele machines van de 2e generatie
author: ju-shim
ms.service: virtual-machines
ms.subservice: sizes
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 02/26/2021
ms.author: jushiman
ms.openlocfilehash: 356afea7573b24649f682205d26823b4e24e7fbe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101695189"
---
# <a name="support-for-generation-2-vms-on-azure"></a>Ondersteuning voor Generatie 2-VM’s in Azure

Ondersteuning voor virtuele machines van generatie 2 (Vm's) is nu beschikbaar in Azure. U kunt de generatie van een virtuele machine niet wijzigen nadat u deze hebt gemaakt. Controleer de overwegingen op deze pagina voordat u een generatie kiest.

Vm's van generatie 2 ondersteunen belang rijke functies die niet worden ondersteund in virtuele machines van de eerste generatie. Tot deze functies behoren meer geheugen, Intel-software Guard Extensions (Intel SGX) en gevirtualiseerde permanent geheugen (vPMEM). Virtuele machines van generatie 2 kunnen on-premises worden uitgevoerd, hebben enkele functies die nog niet worden ondersteund in Azure. Zie de sectie [functies en mogelijkheden](#features-and-capabilities) voor meer informatie.

Vm's van generatie 2 gebruiken de nieuwe op UEFI gebaseerde opstart architectuur in plaats van de op BIOS gebaseerde architectuur die wordt gebruikt door virtuele machines van de eerste generatie. Vergeleken met virtuele machines van de eerste generatie, hebben virtuele machines van de tweede generatie mogelijk betere opstart-en installatie tijden. Zie voor een overzicht van de virtuele machines van de 2e generatie en enkele verschillen tussen de generatie 1 en generatie 2, [moet ik een generatie 1 of 2 maken in Hyper-V?](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).

## <a name="generation-2-vm-sizes"></a>VM-grootten van generatie 2

Virtuele machines van de eerste generatie worden ondersteund door alle VM-grootten in azure (met uitzonde ring van Vm's uit de Mv2-serie). Azure biedt nu ondersteuning voor generatie 2 voor de volgende geselecteerde VM-serie:


* [B-serie](sizes-b-series-burstable.md)
* [DCsv2-serie](dcv2-series.md)
* [DSv2-serie](dv2-dsv2-series.md)
* [Dsv3-serie](dv3-dsv3-series.md)
* [Dsv4-serie](dv4-dsv4-series.md)
* [Dasv4-serie](dav4-dasv4-series.md)
* [Ddsv4-serie](ddv4-ddsv4-series.md)
* [Esv3-serie](ev3-esv3-series.md)
* [Esv4-serie](ev4-esv4-series.md)
* [Easv4-serie](eav4-easv4-series.md)
* [Edsv4-serie](edv4-edsv4-series.md)
* [Fsv2-serie](fsv2-series.md)
* [GS-serie](sizes-previous-gen.md#gs-series)
* [HB-serie](hb-series.md)
* [HC-serie](hc-series.md)
* [LS-serie](sizes-previous-gen.md#ls-series) 
* [Lsv2-serie](lsv2-series.md)
* [M-serie](m-series.md)
* [Mv2-serie](mv2-series.md)<sup>1</sup>
* [NCv2-serie](ncv2-series.md) 
* [NCv3-serie](ncv3-series.md)
* [ND-serie](nd-series.md)
* [NVv3-serie](nvv3-series.md)
* [NVv4-serie](nvv4-series.md)
* [NCasT4_v3-series](nct4-v3-series.md)

<sup>1</sup> Mv2-serie biedt geen ondersteuning voor generatie 1 VM-installatie kopieën en biedt alleen ondersteuning voor een subset van installatie kopieën van de 2e generatie. Raadpleeg de [documentatie van de Mv2-serie](mv2-series.md) voor meer informatie.


## <a name="generation-2-vm-images-in-azure-marketplace"></a>VM-installatie kopieën van generatie 2 in azure Marketplace

Vm's van generatie 2 ondersteunen de volgende installatie kopieën van Marketplace:

* Windows Server 2019, 2016, 2012 R2, 2012
* Windows 10 Pro, Windows 10 Enter prise
* SUSE Linux Enterprise Server 15 SP1
* SUSE Linux Enterprise Server 12 SP4
* Ubuntu Server 16,04, 18,04, 19,04, 19,10 
* RHEL 8,1, 8,0, 7,7, 7,6, 7,5, 7,4, 7,0
* Cent OS 8,1, 8,0, 7,7, 7,6, 7,5, 7,4
* Oracle Linux 7.7, 7.7CI

> [!NOTE]
> Bij specifieke grootten van virtuele machines, zoals Mv2-Series, wordt mogelijk alleen een subset van deze installatie kopieën ondersteund. Raadpleeg de documentatie van de betreffende grootte van de virtuele machine voor meer informatie.

## <a name="on-premises-vs-azure-generation-2-vms"></a>On-premises versus Azure Generation 2 Vm's

Azure biedt momenteel geen ondersteuning voor enkele van de functies die on-premises Hyper-V ondersteunt voor virtuele machines van de tweede generatie.

| Functie van de 2e generatie                | On-premises Hyper-V | Azure |
|-------------------------------------|---------------------|-------|
| Beveiligd opstarten                         | :heavy_check_mark:  | Met vertrouwde start (preview-versie)   |
| Afgeschermde VM                         | :heavy_check_mark:  | BxDxH   |
| vTPM                                | :heavy_check_mark:  | Met vertrouwde start (preview-versie)  |
| Beveiliging op basis van virtualisatie (VBS) | :heavy_check_mark:  | Met vertrouwde start (preview-versie)   |
| VHDX-indeling                         | :heavy_check_mark:  | BxDxH   |

Zie [Trusted Launch (preview)](trusted-launch.md)voor meer informatie.

## <a name="features-and-capabilities"></a>Functies en mogelijkheden

### <a name="generation-1-vs-generation-2-features"></a>Generatie 1 vs. generatie 2-functies

| Functie | Eerste generatie | Tweede generatie |
|---------|--------------|--------------|
| Opstarten             | PCAT                      | UEFI                               |
| Schijf controllers | IDE                       | SCSI                               |
| Formaten van virtuele machines         | Alle VM-grootten | [Beschik bare grootten weer geven](#generation-2-vm-sizes) |

### <a name="generation-1-vs-generation-2-capabilities"></a>Mogelijkheden van generatie 1 vs. generatie 2

| Mogelijkheid | Eerste generatie | Tweede generatie |
|------------|--------------|--------------|
| BESTURINGSSYSTEEM schijf > 2 TB                    | BxDxH                | :heavy_check_mark: |
| Besturings systeem voor aangepaste schijf/image/swap         | :heavy_check_mark: | :heavy_check_mark: |
| Ondersteuning voor schaal sets voor virtuele machines | :heavy_check_mark: | :heavy_check_mark: |
| Azure Site Recovery               | :heavy_check_mark: | :heavy_check_mark: |
| Back-up/herstel                    | :heavy_check_mark: | :heavy_check_mark: |
| Galerie met gedeelde afbeeldingen              | :heavy_check_mark: | :heavy_check_mark: |
| [Azure Disk Encryption](../security/fundamentals/azure-disk-encryption-vms-vmss.md)             | :heavy_check_mark: | :heavy_check_mark:                |
| [Versleuteling aan de serverzijde](disk-encryption.md)            | :heavy_check_mark: | :heavy_check_mark: |

## <a name="creating-a-generation-2-vm"></a>Een virtuele machine van de tweede generatie maken

### <a name="marketplace-image"></a>Marketplace-installatiekopie

In de Azure Portal of Azure CLI kunt u virtuele machines van de tweede generatie maken op basis van een Marketplace-installatie kopie die ondersteuning biedt voor UEFI-opstart bewerkingen.

#### <a name="azure-portal"></a>Azure Portal

Hieronder vindt u de stappen voor het maken van een Gen2-VM (Generation 2) in Azure Portal.

1. Meld u aan bij Azure Portal op https://portal.azure.com.
1. Selecteer **Een resource maken**.
1. Klik op **alles weer geven** in azure Marketplace aan de linkerkant.
1. Selecteer een installatie kopie die Gen2 ondersteunt.
1. Klik op **Create**.
1. Selecteer op het tabblad **Geavanceerd** onder de sectie **VM-generatie** de optie **generatie 2** .
1. Ga naar het tabblad **basis** informatie en klik onder **Details van exemplaar** op **grootte** en open de Blade **VM-grootte selecteren** .
1. Selecteer een [ondersteunde virtuele machine van de tweede generatie](#generation-2-vm-sizes).
1. Ga door de rest van de pagina's om het maken van de virtuele machine te volt ooien.

![Selecteer generatie 1 of generatie 2 virtuele machine](./media/generation-2/gen1-gen2-select.png)

#### <a name="powershell"></a>PowerShell

U kunt ook Power shell gebruiken om een virtuele machine te maken door rechtstreeks te verwijzen naar de SKU van de eerste en 2e generatie.

Gebruik bijvoorbeeld de volgende Power shell-cmdlet om een lijst op te halen van de Sku's in de `WindowsServer` aanbieding.

```powershell
Get-AzVMImageSku -Location westus2 -PublisherName MicrosoftWindowsServer -Offer WindowsServer
```

Als u een virtuele machine met Windows Server 2012 als besturings systeem maakt, selecteert u de VM-SKU van generatie 1 (BIOS) of generatie 2 (UEFI), die er als volgt uitziet:

```powershell
2012-Datacenter
2012-datacenter-gensecond
```

Zie de sectie [functies en mogelijkheden](#features-and-capabilities) voor een huidige lijst met ondersteunde Marketplace-installatie kopieën.

#### <a name="azure-cli"></a>Azure CLI

U kunt ook de Azure CLI gebruiken voor een overzicht van alle beschik bare installatie kopieën van de tweede generatie, vermeld door **Publisher**.

```azurecli
az vm image list --publisher Canonical --sku gen2 --output table --all
```

### <a name="managed-image-or-managed-disk"></a>Beheerde installatie kopie of beheerde schijf

U kunt een virtuele machine van de tweede generatie van een beheerde installatie kopie of een beheerde schijf op dezelfde manier maken als een virtuele machine van de eerste generatie.

### <a name="virtual-machine-scale-sets"></a>Virtuele-machineschaalsets

U kunt ook virtuele machines van de tweede generatie maken met behulp van schaal sets voor Virtual Machine. Gebruik in de Azure CLI Azure-schaal sets om virtuele machines van generatie 2 te maken.

## <a name="frequently-asked-questions"></a>Veelgestelde vragen

* **Zijn er Vm's van generatie 2 beschikbaar in alle Azure-regio's?**  
    Ja. Maar niet alle [VM-grootten van generatie 2](#generation-2-vm-sizes) zijn in elke regio beschikbaar. De beschik baarheid van de generatie 2 virtuele machine is afhankelijk van de beschik baarheid van de VM-grootte.

* **Is er een prijs verschil tussen Vm's van generatie 1 en generatie 2?**  
   Nee.

* **Ik heb een VHD-bestand van mijn on-premises generatie 2 VM. Kan ik dat VHD-bestand gebruiken om een virtuele machine van de tweede generatie in azure te maken?**
  Ja, u kunt uw VHD-bestand van de tweede generatie naar Azure brengen en gebruiken om een virtuele machine van de tweede generatie te maken. Gebruik de volgende stappen om dit te doen:
    1. Upload het. VHD-archief naar een opslag account in dezelfde regio waarin u de virtuele machine wilt maken.
    1. Een beheerde schijf maken op basis van het VHD-bestand. Stel de eigenschap Hyper-V Generation in op v2. Met de volgende Power shell-opdrachten wordt de eigenschap Hyper-V Generation ingesteld bij het maken van een beheerde schijf.

        ```powershell
        $sourceUri = 'https://xyzstorage.blob.core.windows.net/vhd/abcd.vhd'. #<Provide location to your uploaded .vhd file>
        $osDiskName = 'gen2Diskfrmgenvhd'  #<Provide a name for your disk>
        $diskconfig = New-AzDiskConfig -Location '<location>' -DiskSizeGB 127 -AccountType Standard_LRS -OsType Windows -HyperVGeneration "V2" -SourceUri $sourceUri -CreateOption 'Import'
        New-AzDisk -DiskName $osDiskName -ResourceGroupName '<Your Resource Group>' -Disk $diskconfig
        ```

    1. Zodra de schijf beschikbaar is, maakt u een virtuele machine door deze schijf te koppelen. De virtuele machine die wordt gemaakt, is een VM van de tweede generatie.
    Bij het maken van de virtuele machine van de tweede generatie kunt u optioneel de installatie kopie van deze VM generaliseren. Door de installatie kopie te generaliseren, kunt u deze gebruiken om meerdere Vm's te maken.

* **De schijf grootte van het besturings systeem Hoe kan ik verg Roten?**  

  BESTURINGSSYSTEEM schijven groter dan 2 TiB zijn nieuw voor virtuele machines van de tweede generatie. BESTURINGSSYSTEEM schijven zijn standaard kleiner dan 2 TiB voor virtuele machines van de tweede generatie. U kunt de schijf grootte verg Roten tot een aanbevolen maximum van 4 TiB. Gebruik de Azure CLI of de Azure Portal om de schijf grootte van het besturings systeem te verg Roten. Zie het **formaat van een schijf wijzigen** voor [Windows](./windows/expand-os-disk.md) of [Linux](./linux/resize-os-disk-gpt-partition.md)voor meer informatie over het programmatisch uitbreiden van schijven.

  De schijf grootte van het besturings systeem verg Roten van de Azure Portal:

  1. Ga in het Azure Portal naar de pagina met VM-eigenschappen.
  1. Als u de virtuele machine wilt afsluiten en de toewijzing ongedaan wilt maken, selecteert u de knop **stoppen** .
  1. Selecteer in de sectie **schijven** de besturingssysteem schijf die u wilt verg Roten.
  1. Selecteer in de sectie **schijven** de optie **configuratie** en werk de **grootte** bij naar de gewenste waarde.
  1. Ga terug naar de pagina met eigenschappen van de virtuele machine en **Start** de virtuele machine.
  
  Mogelijk wordt er een waarschuwing weer gegeven voor besturingssysteem schijven groter dan twee TiB. De waarschuwing is niet van toepassing op virtuele machines van de tweede generatie. BESTURINGSSYSTEEM schijf grootten die groter zijn dan 4 TiB worden echter niet ondersteund.

* **Ondersteunen virtuele machines van generatie 2 versneld netwerken?**  
    Ja. Zie [een virtuele machine maken met versneld netwerken](../virtual-network/create-vm-accelerated-networking-cli.md)voor meer informatie.

* **Ondersteunen virtuele machines van generatie 2 beveiligde opstart-of vTPM in azure?**
    Zowel vTPM als beveiligd opstarten zijn functies van vertrouwde start (preview) voor Vm's van generatie 2. Zie [Trusted Launch](trusted-launch.md)(Engelstalig) voor meer informatie.
    
* **Wordt VHDX ondersteund voor generatie 2?**  
    Nee; Vm's van generatie 2 ondersteunen alleen VHD.

* **Ondersteunen virtuele machines van generatie 2 Azure Ultra Disk Storage?**  
    Ja.

* **Kan ik een virtuele machine van generatie 1 migreren naar generatie 2?**  
    Nee, u kunt de generatie van een virtuele machine niet wijzigen nadat u deze hebt gemaakt. Als u wilt scha kelen tussen VM'S gegenereerd, maakt u een nieuwe VM van een andere generatie.

* **Waarom is mijn VM-grootte niet ingeschakeld in de formaat kiezer wanneer ik een Gen2-VM Maak?**

    Dit kan worden opgelost door de volgende handelingen uit te voeren:

    1. Controleer of de eigenschap voor het genereren van de **virtuele machine** is ingesteld op **gen 2** op het tabblad **Geavanceerd** .
    1. Controleer of u zoekt naar een [VM-grootte die Gen2 vm's ondersteunt](#generation-2-vm-sizes).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het [vertrouwde start (preview)](trusted-launch-portal.md) met virtuele machines van generatie 2.

Meer informatie over [virtuele machines van de 2e generatie in Hyper-V](/windows-server/virtualization/hyper-v/plan/should-i-create-a-generation-1-or-2-virtual-machine-in-hyper-v).
