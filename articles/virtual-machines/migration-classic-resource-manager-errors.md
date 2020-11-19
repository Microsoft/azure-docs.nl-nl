---
title: Algemene fouten bij de migratie van klassiek naar Azure Resource Manager
description: In dit artikel worden de meest voorkomende fouten en oplossingen voor de migratie van IaaS-resources van Azure Service Management naar Azure Resource Manager catalogussen gecatalogiseerd.
author: tanmaygore
manager: vashan
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: troubleshooting
ms.date: 02/06/2020
ms.author: tagore
ms.openlocfilehash: f196fc1fc88b36538f7427d30ef16d2af158aace
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94904717"
---
# <a name="errors-that-commonly-occur-during-classic-to-azure-resource-manager-migration"></a>Fouten die zich vaak voordoen tijdens de klassieke migratie naar Azure Resource Manager

> [!IMPORTANT]
> Nu gebruiken we op ongeveer 90% IaaS Vm's [Azure Resource Manager](https://azure.microsoft.com/features/resource-manager/). Vanaf 28 februari 2020 zijn klassieke Vm's afgeschaft en worden ze volledig buiten gebruik gesteld op 1 maart 2023. Meer [informatie]( https://aka.ms/classicvmretirement) over deze afschaffing en [hoe dit van invloed is op u](classic-vm-deprecation.md#how-does-this-affect-me).

In dit artikel behandelen we de meest voorkomende fouten en oplossingen tijdens de migratie van IaaS-resources van het klassieke Azure-implementatiemodel naar de Azure Resource Manager-stack.


## <a name="list-of-errors"></a>Lijst met fouten

| Fouttekenreeks | Oplossing |
| --- | --- |
| Interne serverfout |In sommige gevallen is dit een tijdelijke fout die bij een nieuwe poging is verdwenen. Als deze fout zich blijft voordoen, [neem dan contact op met de ondersteuning van Azure](../azure-portal/supportability/how-to-create-azure-support-request.md). De platformlogboeken moeten namelijk worden onderzocht. <br><br> **OPMERKING:** Wanneer het incident eenmaal wordt gevolgd door het ondersteuningsteam, mag u zelf geen oplossingen meer proberen te zoeken voor het probleem. Dit kan ongewenste gevolgen hebben voor uw omgeving. |
| Migratie wordt niet ondersteund voor de implementatie {naam-implementatie} in HostedService {naam-gehoste-service} omdat dit een PaaS-implementatie (web-/werkrol) is. |Dit gebeurt wanneer een implementatie een web-/werkrol bevat. Omdat de migratie alleen wordt ondersteund voor virtuele machines, moet u de web-/werkrol uit de implementatie verwijderen. Probeer hierna het opnieuw. |
| Sjabloonimplementatie {sjabloonnaam} is mislukt. CorrelationId = {guid} |In de back-end van de migratieservice gebruiken we Azure Resource Manager-sjablonen om resources te maken in de Azure Resource Manager-stack. Aangezien sjablonen idempotent zijn, kunt u de migratiebewerking meestal veilig opnieuw proberen om deze fout te verhelpen. Als deze fout zich blijft voordoen, [neemt u contact op met de ondersteuning van Azure](../azure-portal/supportability/how-to-create-azure-support-request.md) en verstrekt u hen de CorrelationId. <br><br> **OPMERKING:** Wanneer het incident eenmaal wordt gevolgd door het ondersteuningsteam, mag u zelf geen oplossingen meer proberen te zoeken voor het probleem. Dit kan ongewenste gevolgen hebben voor uw omgeving. |
| Het virtuele netwerk {naam-virtueel-netwerk} bestaat niet. |Dit kan gebeuren als u het virtuele netwerk hebt gemaakt in de nieuwe Azure portal. De werkelijke naam van het virtuele netwerk heeft de indeling 'Groep * \<VNET name>' |
| VM {vm-naam} in HostedService {naam-gehoste-service} bevat de extensie {naam-extensie}. Deze wordt niet ondersteund in Azure Resource Manager. We raden u aan deze te verwijderen uit de virtuele machine voordat u doorgaat met de migratie. |XML-extensies zoals BGInfo 1. \* worden niet ondersteund in Azure Resource Manager. Daarom kunnen deze extensies niet worden gemigreerd. Als deze uitbreidingen geïnstalleerd blijven op de virtuele machine, worden ze automatisch verwijderd voordat de migratie is voltooid. |
| VM {vm-naam} in HostedService {naam-gehoste-service} bevat de extensie VMSnapshot/VMSnapshotLinux. Deze wordt momenteel niet ondersteund voor migratie. Verwijder deze uit de virtuele machine en voeg de extensie opnieuw toe met Azure Resource Manager nadat de migratie is voltooid |Dit is het scenario waarin de virtuele machine is geconfigureerd voor Azure Backup. Omdat dit momenteel een niet-ondersteund scenario is, volgt u de tijdelijke oplossing op https://aka.ms/vmbackupmigration |
| VM {vm-naam} in HostedService {naam-gehoste-service} bevat de extensie {naam-extensie}. De status van deze extensie wordt niet gerapporteerd door de VM. Daarom kan deze virtuele machine niet worden gemigreerd. Zorg ervoor dat de status van de extensie wordt gerapporteerd of verwijder de extensie uit de virtuele machine en voer de migratie nogmaals uit. <br><br> VM {vm-naam} in HostedService {naam-gehoste-service} bevat de extensie {naam-extensie}. Deze rapporteert de volgende handlerstatus: {handlerstatus}. Hence, the VM cannot be migrated. Zorg ervoor dat de status van de extensiehandler die wordt gerapporteerd {handlerstatus} is of verwijder de extensie uit de virtuele machine en voer de migratie nogmaals uit. <br><br> VM-agent voor de virtuele machine {vm-naam} in HostedService {naam-gehoste-service} rapporteert de algehele agentstatus Niet gereed. Daarom kan de virtuele machine niet worden gemigreerd, als deze een extensie heeft die kan worden gemigreerd. Zorg ervoor dat de VM-agent de algehele agentstatus Gereed rapporteert. Raadpleeg https://aka.ms/classiciaasmigrationfaqs . |De Azure-gastagent en VM-extensies hebben uitgaande internettoegang nodig tot het VM-opslagaccount om hun status te vullen. Algemene oorzaken van de statusfout zijn <li> een netwerkbeveiligingsgroep waarmee uitgaande toegang tot het internet wordt geblokkeerd <li> Als het VNET lokale DNS-servers heeft en de DNS-connectiviteit is verbroken <br><br> Als de melding dat een extensie niet wordt ondersteund nog steeds wordt weergegeven, kunt u de extensies verwijderen om deze controle over te slaan en verder te gaan met de migratie. |
| Migratie wordt niet ondersteund voor de implementatie {naam-implementatie} in HostedService {naam-gehoste-service} omdat deze meerdere beschikbaarheidssets heeft. |Op dit moment kunnen alleen gehoste services die maximaal één beschikbaarheidsset hebben worden gemigreerd. U kunt dit probleem omzeilen door de extra beschikbaarheids sets en virtuele machines in die beschikbaarheids sets te verplaatsen naar een andere gehoste service. |
| Migratie wordt niet ondersteund voor de implementatie {naam-implementatie} in HostedService {naam-gehoste-service} omdat deze VM's bevat die geen deel uitmaken van de beschikbaarheidsset, hoewel de HostedService wel één van de VM's bevat. |De tijdelijke oplossing voor dit scenario is om alle virtuele machines te verplaatsen naar een enkele beschikbaarheidsset. U kunt ook alle virtuele machines verwijderen uit de beschikbaarheidsset in de gehoste service. |
| Opslagaccount/HostedService/virtueel netwerk {naam-virtueel-netwerk} wordt gemigreerd en kan daarom niet worden gewijzigd |Deze fout treedt op wanneer de migratiebewerking 'Voorbereiden' is voltooid op de resource en een bewerking die een wijziging aanbrengt in de resource wordt geactiveerd. Vanwege de vergrendeling op het beheervlak na de bewerking 'Voorbereiden' worden eventuele wijzigingen in de resource geblokkeerd. Als u het beheervlak wilt ontgrendelen, kunt u de migratiebewerking 'Doorvoeren' uitvoeren om de migratie te voltooien. U kunt ook de migratiebewerking 'Afbreken' uitvoeren om de bewerking 'Voorbereiden' terug te draaien. |
| Migratie voor HostedService {naam-gehoste-service} is niet toegestaan omdat de VM {vm-naam} in de service de status: RoleStateUnknown heeft. Migratie is alleen toegestaan wanneer de VM zich in een van de volgende statussen bevindt: Wordt uitgevoerd, Gestopt, Gestopt (toewijzing opgeheven). |De virtuele machine kan een status overgang ondergaan, wat meestal gebeurt wanneer tijdens een update-bewerking op de HostedService, zoals opnieuw opstarten, extensie-installatie, enzovoort. Het is raadzaam om de update bewerking te volt ooien op de HostedService voordat u de migratie uitvoert. |
| Implementatie {naam-implementatie} in HostedService {naam-gehoste-service} bevat een virtuele machine {vm-naam} met een gegevensschijf {naam-gegevens-schijf} waarvan de fysieke blobgrootte van {grootte-van-vhd-blob-achter-gegevensschijf} bytes niet overeenkomt met de logische grootte van {grootte-gegevensschijf-opgegeven-in-vm-api} bytes van de VM. De migratie wordt voortgezet zonder dat er een grootte wordt opgeven voor de gegevensschijf voor de Azure Resource Manager-VM. | Deze fout treedt op als u de grootte van de VHD-blob hebt gewijzigd zonder de grootte in het API-model van de VM te wijzigen. Gedetailleerde stappen worden [hieronder](#vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes) beschreven.|
| Er is een opslaguitzondering opgetreden tijdens het valideren van de gegevensschijf {naam-gegevensschijf} met de medialink {Uri-gegevensschijf} voor de virtuele machine {VM-naam} in de cloudservice {naam-cloudservice}. Zorg ervoor dat de VHD-medialink toegankelijk is voor deze virtuele machine | Deze fout kan optreden als de schijven van de virtuele machine zijn verwijderd of niet meer toegankelijk zijn. Controleer of de schijven voor de virtuele machine bestaan.|
| De VM {vm-naam} in HostedService {naam-cloudservice} bevat een schijf met MediaLink {vhd-uri} met blobnaam {vhd-blobnaam} die niet wordt ondersteund in Azure Resource Manager. | Deze fout treedt op wanneer de naam van de blob een '/' bevat. Dit wordt momenteel niet ondersteund in Compute Resource Provider. |
| Migratie is niet toegestaan voor de implementatie {naam-implementatie} in HostedService {naam-cloudservice} omdat deze zich niet in het regionale bereik bevindt. Raadpleeg https: \/ /aka.MS/regionalscope voor het verplaatsen van deze implementatie naar een regionaal bereik. | In 2014 heeft Azure aangekondigd dat netwerkresources worden verplaatst van een clusterbereik naar een regionaal bereik. Zie [https://aka.ms/regionalscope](https://aka.ms/regionalscope) voor meer informatie. Deze fout treedt op wanneer de implementatie die wordt gemigreerd geen updatebewerking heeft gehad, waarmee de implementatie automatisch naar een regionaal bereik wordt verplaatst. De beste oplossing is om een eind punt toe te voegen aan een virtuele machine of een gegevens schijf aan de virtuele machine en vervolgens de migratie opnieuw uit te voeren. <br> Zie [Eindpunten instellen op een klassieke virtuele Windows-machine in Azure](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#create-an-endpoint) of [Een gegevensschijf koppelen aan een virtuele Windows-machine die is gemaakt met het klassieke implementatiemodel](/previous-versions/azure/virtual-machines/windows/classic//attach-disk.md)|
| Migratie wordt niet ondersteund voor de Virtual Network {vnet-name} omdat deze niet-gateway PaaS-implementaties bevat. | Deze fout treedt op wanneer u PaaS-implementaties zonder gateway hebt, zoals Application Gateway-of API Management-Services die zijn verbonden met de Virtual Network.|


## <a name="detailed-mitigations"></a>Gedetailleerde oplossingen

### <a name="vm-with-data-disk-whose-physical-blob-size-bytes-does-not-match-the-vm-data-disk-logical-size-bytes"></a>Virtuele machine met gegevensschijf waarvan de fysieke blobgrootte in bytes niet overeenkomt met de logische grootte van de VM-gegevensschijf in bytes.

Dit gebeurt wanneer de logische grootte van de gegevensschijf asynchroon kan lopen met de werkelijke grootte van de VHD-blob. Dit u kunt eenvoudig controleren met de volgende opdrachten:

#### <a name="verifying-the-issue"></a>Het probleem verifiëren

```powershell
# Store the VM details in the VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

# Display the data disk properties
# NOTE the data disk LogicalDiskSizeInGB below which is 11GB. Also note the MediaLink Uri of the VHD blob as we'll use this in the next step
$vm.VM.DataVirtualHardDisks


HostCaching         : None
DiskLabel           : 
DiskName            : coreosvm-coreosvm-0-201611230636240687
Lun                 : 0
LogicalDiskSizeInGB : 11
MediaLink           : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
SourceMediaLink     : 
IOType              : Standard
ExtensionData       : 

# Now get the properties of the blob backing the data disk above
# NOTE the size of the blob is about 15 GB which is different from LogicalDiskSizeInGB above
$blob = Get-AzStorageblob -Blob "coreosvm-dd1.vhd" -Container vhds 

$blob

ICloudBlob        : Microsoft.WindowsAzure.Storage.Blob.CloudPageBlob
BlobType          : PageBlob
Length            : 16106127872
ContentType       : application/octet-stream
LastModified      : 11/23/2016 7:16:22 AM +00:00
SnapshotTime      : 
ContinuationToken : 
Context           : Microsoft.WindowsAzure.Commands.Common.Storage.AzureStorageContext
Name              : coreosvm-dd1.vhd
```

#### <a name="mitigating-the-issue"></a>Het probleem oplossen

```powershell
# Convert the blob size in bytes to GB into a variable which we'll use later
$newSize = [int]($blob.Length / 1GB)

# See the calculated size in GB
$newSize

15

# Store the disk name of the data disk as we'll use this to identify the disk to be updated
$diskName = $vm.VM.DataVirtualHardDisks[0].DiskName

# Identify the LUN of the data disk to remove
$lunToRemove = $vm.VM.DataVirtualHardDisks[0].Lun

# Now remove the data disk from the VM so that the disk isn't leased by the VM and it's size can be updated
Remove-AzureDataDisk -LUN $lunToRemove -VM $vm | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       213xx1-b44b-1v6n-23gg-591f2a13cd16   Succeeded  

# Verify we have the right disk that's going to be updated
Get-AzureDisk -DiskName $diskName

AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : 
Location             : East US
DiskSizeInGB         : 11
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 0c56a2b7-a325-123b-7043-74c27d5a61fd
OperationStatus      : Succeeded

# Now update the disk to the new size
Update-AzureDisk -DiskName $diskName -ResizedSizeInGB $newSize -Label $diskName

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureDisk     cv134b65-1b6n-8908-abuo-ce9e395ac3e7 Succeeded 

# Now verify that the "DiskSizeInGB" property of the disk matches the size of the blob 
Get-AzureDisk -DiskName $diskName


AffinityGroup        : 
AttachedTo           : 
IsCorrupted          : False
Label                : coreosvm-coreosvm-0-201611230636240687
Location             : East US
DiskSizeInGB         : 15
MediaLink            : https://contosostorage.blob.core.windows.net/vhds/coreosvm-dd1.vhd
DiskName             : coreosvm-coreosvm-0-201611230636240687
SourceImageName      : 
OS                   : 
IOType               : Standard
OperationDescription : Get-AzureDisk
OperationId          : 1v53bde5-cv56-5621-9078-16b9c8a0bad2
OperationStatus      : Succeeded

# Now we'll add the disk back to the VM as a data disk. First we need to get an updated VM object
$vm = Get-AzureVM -ServiceName $servicename -Name $vmname

Add-AzureDataDisk -Import -DiskName $diskName -LUN 0 -VM $vm -HostCaching ReadWrite | Update-AzureVm -Name $vmname -ServiceName $servicename

OperationDescription OperationId                          OperationStatus
-------------------- -----------                          ---------------
Update-AzureVM       b0ad3d4c-4v68-45vb-xxc1-134fd010d0f8 Succeeded      
```

### <a name="moving-a-vm-to-a-different-subscription-after-completing-migration"></a>Een virtuele machine verplaatsen naar een ander abonnement nadat de migratie is voltooid

Nadat u het migratieproces hebt voltooit, wilt u de virtuele machine mogelijk verplaatsen naar een ander abonnement. De verplaatsing wordt momenteel echter niet ondersteund als u een geheim/certificaat hebt op de virtuele machine die verwijst naar een Key Vault-resource. In de onderstaande instructies kunt u het probleem omzeilen. 

#### <a name="powershell"></a>PowerShell

```powershell
$vm = Get-AzVM -ResourceGroupName "MyRG" -Name "MyVM"
Remove-AzVMSecret -VM $vm
Update-AzVM -ResourceGroupName "MyRG" -VM $vm
```

#### <a name="azure-cli"></a>Azure CLI

```azurecli
az vm update -g "myrg" -n "myvm" --set osProfile.Secrets=[]
```


## <a name="next-steps"></a>Volgende stappen

* [Overzicht van door het platform ondersteunde migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-overview.md)
* [Technische details over door platforms ondersteunde migratie van klassiek naar Azure Resource Manager](migration-classic-resource-manager-deep-dive.md)
* [Planning voor de migratie van IaaS-resources van het klassieke implementatiemodel naar Azure Resource Manager](migration-classic-resource-manager-plan.md)
* [Power shell gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-ps.md)
* [CLI gebruiken voor het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-cli.md)
* [Community tools voor hulp bij de migratie van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-community-tools.md)
* [Bekijk de veelgestelde vragen over het migreren van IaaS-resources van klassiek naar Azure Resource Manager](migration-classic-resource-manager-faq.md)
