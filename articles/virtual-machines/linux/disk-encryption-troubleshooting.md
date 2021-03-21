---
title: Problemen met Azure Disk Encryption voor Linux-Vm's oplossen
description: In dit artikel vindt u tips voor het oplossen van problemen met Microsoft Azure schijf versleuteling voor Linux-Vm's.
author: msmbaldwin
ms.service: virtual-machines
ms.subservice: disks
ms.collection: linux
ms.topic: troubleshooting
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 58ea788d6959b039ff5de8a4a889c531b987d826
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102563367"
---
# <a name="azure-disk-encryption-for-linux-vms-troubleshooting-guide"></a>Probleemoplossings gids voor Azure Disk Encryption voor Linux Vm's

Deze hand leiding is voor IT-professionals, gegevens beveiligings analisten en Cloud beheerders waarvan de organisaties Azure Disk Encryption gebruiken. In dit artikel vindt u informatie over het oplossen van problemen met schijf versleuteling.

Voordat u een van de volgende stappen uitvoert, moet u eerst controleren of de virtuele machines die u probeert te versleutelen, worden ondersteund door de [ondersteunde VM-grootten en-besturings systemen](disk-encryption-overview.md#supported-vms-and-operating-systems)en dat u aan alle vereisten hebt voldaan:

- [Aanvullende vereisten voor Vm's](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Netwerkvereisten](disk-encryption-overview.md#networking-requirements)
- [Opslag vereisten voor de versleutelings sleutel](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Problemen met de versleuteling van Linux-besturingssysteem schijven oplossen

Schijf versleuteling van het Linux-besturings systeem (OS) moet het OS-station ontkoppelen voordat het kan worden uitgevoerd via het proces van de volledige schijf versleuteling. Als het station niet kan worden ontkoppeld, wordt het volgende fout bericht weer gegeven: ' kan niet ontkoppelen na... ' waarschijnlijk zal optreden.

Deze fout kan optreden wanneer versleuteling van de besturingssysteem schijf wordt uitgevoerd op een virtuele machine met een omgeving die is gewijzigd vanuit de ondersteunde installatie kopie van de Stock galerie. Afwijkingen van de ondersteunde afbeelding kunnen de uitbrei ding van het OS-station belemmeren. Voor beelden van afwijkingen kunnen de volgende items bevatten:
- Aangepaste installatie kopieën komen niet meer overeen met een ondersteund bestands systeem of partitie schema.
- Grote toepassingen zoals SAP, MongoDB, Apache Cassandra en docker worden niet ondersteund wanneer ze zijn geïnstalleerd en worden uitgevoerd in het besturings systeem vóór versleuteling. Azure Disk Encryption kan deze processen niet veilig afsluiten zoals vereist in de voor bereiding van het station van het besturings systeem voor schijf versleuteling. Als er nog actieve processen zijn die open bestands ingangen op het station van het besturings systeem houden, kan het station van het besturings systeem niet worden ontkoppeld, waardoor het station van het besturings systeem niet kan worden versleuteld. 
- Aangepaste scripts die worden uitgevoerd in de sluitings tijd nabij de versleuteling die wordt ingeschakeld, of als er tijdens het versleutelings proces andere wijzigingen worden aangebracht op de virtuele machine. Dit conflict kan optreden wanneer een Azure Resource Manager sjabloon meerdere uitbrei dingen definieert om tegelijkertijd uit te voeren, of wanneer een aangepaste script extensie of andere actie gelijktijdig wordt uitgevoerd op schijf versleuteling. Door dergelijke stappen te serialiseren en isoleren, kan het probleem worden opgelost.
- Security Enhanced Linux (SELinux) is niet uitgeschakeld voordat versleuteling is ingeschakeld, waardoor de ontkoppelings stap mislukt. SELinux kan opnieuw worden ingeschakeld nadat de versleuteling is voltooid.
- De besturingssysteem schijf maakt gebruik van een LVM-schema (Logical Volume Manager). Hoewel er beperkte ondersteuning voor LVM-gegevens schijven beschikbaar is, is er geen LVM-besturingssysteem schijf.
- Er wordt niet voldaan aan de minimale geheugen vereisten (7 GB wordt aanbevolen voor de versleuteling van de besturingssysteem schijf).
- Gegevens stations worden recursief gekoppeld in de/mnt/-map of elkaar (bijvoorbeeld/mnt/Data1,/mnt/data2,/DATA3 +/DATA3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>De standaard-kernel voor Ubuntu 14,04 LTS bijwerken

De Ubuntu 14,04 LTS-installatie kopie wordt geleverd met een standaard versie van de kernel van 4,4. Deze kernel-versie heeft een bekend probleem waarbij onvoldoende geheugen-Killer de opdracht DD tijdens het versleutelings proces van het besturings systeem niet correct beëindigt. Deze bug is opgelost in de meest recente Azure-afgestemde Linux-kernel. Als u deze fout wilt voor komen, moet u, voordat u versleuteling inschakelt voor de installatie kopie, bijwerken naar de door [Azure afgestemde kernel 4,15](https://packages.ubuntu.com/trusty/linux-azure) of later met de volgende opdrachten:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Nadat de VM opnieuw is opgestart in de nieuwe kernel, kan de nieuwe versie van de kernel worden bevestigd met:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>De versie van de Azure virtual machine-agent en-extensie bijwerken

Azure Disk Encryption bewerkingen kunnen mislukken op installatie kopieën van virtuele machines met niet-ondersteunde versies van de virtuele machine agent van Azure. Linux-installatie kopieën met eerdere agent versies dan 2.2.38 moeten worden bijgewerkt voordat u versleuteling inschakelt. Zie [How to update the Azure Linux agent](../extensions/update-linux-agent.md) (Engelstalig) voor meer informatie over het bijwerken van een [virtuele machine en de minimale versie van de ondersteuning van](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)de VM-agents in Azure.

De juiste versie van de extensie micro soft. Azure. Security. AzureDiskEncryption of micro soft. Azure. Security. AzureDiskEncryptionForLinux Guest agent is ook vereist. Extensie versies worden automatisch onderhouden en bijgewerkt door het platform wanneer aan de vereisten van de Azure virtual machine-agent wordt voldaan en er een ondersteunde versie van de Virtual Machine agent wordt gebruikt.

De extensie micro soft. OSTCExtensions. AzureDiskEncryptionForLinux is afgeschaft en wordt niet meer ondersteund.  

## <a name="unable-to-encrypt-linux-disks"></a>Kan Linux-schijven niet versleutelen

In sommige gevallen lijkt de schijf versleuteling van Linux te blijven op ' schijf versleuteling van het besturings systeem is gestart ' en is SSH uitgeschakeld. Het versleutelings proces kan tussen 3-16 uur duren om de installatie kopie van de aandelen galerie te volt ooien. Als er gegevens schijven met meerdere terabytes worden toegevoegd, kan het proces dagen duren.

De versleutelings volgorde van de Linux-besturingssysteem schijf ontkoppelt tijdelijk het station van het besturings systeem. Vervolgens wordt er een blok-by-Block-versleuteling uitgevoerd van de volledige besturingssysteem schijf, voordat het opnieuw wordt gekoppeld aan de versleutelde status. Linux-schijf versleuteling staat niet toe dat de virtuele machine gelijktijdig wordt gebruikt terwijl de versleuteling wordt uitgevoerd. De prestatie kenmerken van de virtuele machine kunnen aanzienlijk verschillen in de tijd die nodig is om versleuteling te volt ooien. Deze kenmerken omvatten de grootte van de schijf en of het opslag account een Standard-of Premium-opslag (SSD) is.

Terwijl het station van het besturings systeem wordt versleuteld, wordt door de virtuele machine een onderhouds status geactiveerd en wordt SSH uitgeschakeld om onderbreking van het doorlopende proces te voor komen.  Als u de versleutelings status wilt controleren, gebruikt u de opdracht Azure PowerShell [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) en schakelt u het veld **ProgressMessage** in. **ProgressMessage** rapporteert een reeks statussen wanneer de gegevens en besturingssysteem schijven zijn versleuteld:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings :
ProgressMessage            : Transitioning

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for data volumes

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Provisioning succeeded

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started
```

De **ProgressMessage** blijft aanwezig in de **schijf versleuteling van het besturings systeem, die is gestart** voor het grootste deel van het versleutelings proces.  Wanneer de versleuteling is voltooid, wordt **ProgressMessage** geretourneerd:

```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyResourceGroup" -VMName "myVM"

OsVolumeEncrypted          : Encrypted
DataVolumesEncrypted       : NotMounted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : Encryption succeeded for all volumes
```

Nadat dit bericht beschikbaar is, wordt verwacht dat het versleutelde OS-station gereed is voor gebruik en de VM opnieuw kan worden gebruikt.

Als de opstart informatie, het voortgangs bericht of een fout rapport bevat dat de versleuteling van het besturings systeem is mislukt in het midden van dit proces, herstelt u de virtuele machine naar de moment opname of back-up die direct vóór de versleuteling is gemaakt. Een voor beeld van een bericht is de fout ' kan niet worden ontkoppeld ' die in deze hand leiding wordt beschreven.

Voordat u de versleuteling opnieuw probeert te versleutelen, moet u de kenmerken van de virtuele machine opnieuw evalueren en ervoor zorgen dat aan alle vereisten wordt voldaan.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Problemen met Azure Disk Encryption achter een firewall oplossen

Zie [schijf versleuteling op een geïsoleerd netwerk](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Problemen met de versleutelings status oplossen 

De portal kan een schijf weer geven als versleuteld, zelfs nadat deze is ontsleuteld in de virtuele machine.  Dit kan gebeuren wanneer opdrachten op laag niveau worden gebruikt om de schijf rechtstreeks van de virtuele machine te ontsleutelen, in plaats van de Azure Disk Encryption beheer opdrachten op een hoger niveau te gebruiken.  Met de opdrachten op een hoger niveau wordt de schijf niet alleen ontsleuteld vanuit de virtuele machine, maar buiten de virtuele machine worden ook belang rijke versleutelings instellingen en extensie-instellingen van het platform bijgewerkt die zijn gekoppeld aan de virtuele machine.  Als deze niet in de uitlijning worden bewaard, kan het platform geen versleutelings status rapporteren of de virtuele machine op de juiste wijze inrichten.

Als u Azure Disk Encryption wilt uitschakelen met Power shell, gebruikt u [Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) gevolgd door [Remove-AzVMDiskEncryptionExtension](/powershell/module/az.compute/remove-azvmdiskencryptionextension). Het uitvoeren van Remove-AzVMDiskEncryptionExtension voordat de versleuteling is uitgeschakeld, mislukt.

Gebruik [AZ VM Encryption Disable](/cli/azure/vm/encryption)om Azure Disk Encryption uit te scha KELEN met cli.

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u meer geleerd over enkele veelvoorkomende problemen in Azure Disk Encryption en hoe u deze problemen oplost. Raadpleeg de volgende artikelen voor meer informatie over deze service en de mogelijkheden ervan:

- [Schijf versleuteling Toep assen in Azure Security Center](../../security-center/asset-inventory.md)
- [Versleuteling van inactieve gegevens in Azure](../../security/fundamentals/encryption-atrest.md)