---
title: Problemen oplossen bij het verwijderen van klassieke Azure-opslag accounts,-containers of-Vhd's | Microsoft Docs
description: Problemen oplossen bij het verwijderen van opslag resources met gekoppelde Vhd's.
services: storage
author: AngshumanNayakMSFT
tags: top-support-issue,azure-service-management
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/11/2019
ms.author: annayak
ms.openlocfilehash: c74f2ef9eed25719e722970671406c850b6a59b2
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96002592"
---
# <a name="troubleshoot-classic-storage-resource-deletion-errors"></a>Fouten bij het verwijderen van klassieke opslag resources oplossen
Dit artikel bevat richt lijnen voor probleem oplossing wanneer een van de volgende fouten optreedt bij het verwijderen van het klassieke Azure-opslag account, de container of de VHD-pagina blob-bestand. 


Dit artikel behandelt alleen problemen met klassieke opslag resources. Als een gebruiker een klassieke virtuele machine verwijdert met behulp van de Azure Portal, Power shell of CLI, worden de schijven niet automatisch verwijderd. De gebruiker krijgt de mogelijkheid om de schijf bron te verwijderen. Als de optie niet is geselecteerd, wordt voor komen dat het opslag account, de container en de daad werkelijke VHD-pagina blob-bestand worden verwijderd.

Meer informatie over Azure-schijven vindt u [hier](../../virtual-machines/managed-disks-overview.md). Azure voor komt dat een schijf die is gekoppeld aan een virtuele machine wordt verwijderd om beschadiging te voor komen. Er wordt ook voor komen dat containers en opslag accounts worden verwijderd, die een pagina-BLOB hebben die is gekoppeld aan een virtuele machine. 

## <a name="what-is-a-disk"></a>Wat is een ' schijf '?
Een ' schijf ' bron wordt gebruikt voor het koppelen van een VHD-bestand van het type *. vhd's aan een virtuele machine, als een besturingssysteem schijf of gegevens schijf. Een besturingssysteem schijf of gegevens schijf bron, totdat deze is verwijderd, blijft een lease in het VHD-bestand. Een opslag resource in het pad dat wordt weer gegeven in de onderstaande afbeelding, kan niet worden verwijderd als een ' schijf ' bron naar deze resource verwijst.

![Scherm afbeelding van de portal, met het deel venster voor de schijf (klassiek) geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/Disk_Lease_Illustration.jpg) 


## <a name="steps-while-deleting-a-classic-virtual-machine"></a>Stappen voor het verwijderen van een klassieke virtuele machine 

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]


1. Verwijder de klassieke virtuele machine.
2. Als het selectie vakje "schijven" is ingeschakeld, is de lease van de **schijf** (weer gegeven in de bovenstaande afbeelding) die aan de pagina-blob *. VHD is gekoppeld, beschadigd. Het daad werkelijke pagina-blob *. VHD-bestand blijft aanwezig in het opslag account.
![Scherm afbeelding toont een dialoog venster om het verwijderen van een virtuele machine te bevestigen.](./media/storage-classic-cannot-delete-storage-account-container-vhd/steps_while_deleting_classic_vm.jpg) 

3. Zodra de lease van de schijf of schijven is verbroken, kunnen de pagina-BLOB (s) zelf worden verwijderd. Een opslag account of-container kan worden verwijderd zodra alle ' schijf ' in de bron aanwezig is.

>[!NOTE] 
>Als de gebruiker de virtuele machine verwijdert, maar niet de VHD, blijven de opslag kosten toenemen op het VHD-bestand van de pagina-blob. De kosten zijn in overeenstemming met het type opslag account. Controleer de pagina met [prijzen](https://azure.microsoft.com/pricing/details/storage/) voor meer informatie. Als de gebruiker de VHD (s) niet langer wil gebruiken, moet u deze verwijderen om toekomstige kosten te voor komen. 

## <a name="unable-to-delete-storage-account"></a>Kan het opslag account niet verwijderen 

Wanneer een gebruiker probeert een klassiek opslag account te verwijderen dat niet meer nodig is, ziet de gebruiker mogelijk het volgende gedrag.

#### <a name="azure-portal"></a>Azure Portal 
De gebruiker navigeert naar het klassieke opslag account op de [Azure Portal](https://portal.azure.com) en klikt op **verwijderen**. de gebruiker ziet het volgende bericht: 

Met een of meer schijven gekoppeld aan een virtuele machine

![Scherm afbeelding toont een bericht waarin wordt uitgelegd waarom een opslag account niet kan worden verwijderd.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_attached_portal.jpg) 


Met een of meer schijven ' ontkoppeld ' aan een virtuele machine

![Scherm opname van de portal, met het venster voor het verwijderen van de virtuele machine (klassiek) zonder fouten](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_storage_account_disks_unattached_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
De gebruiker probeert een opslag account te verwijderen dat niet meer wordt gebruikt, door gebruik te maken van klassieke Power shell-cmdlets. De gebruiker ziet het volgende bericht:

> <span style="color:cyan">**Remove-AzureStorageAccount-StorageAccountName myclassicaccount**</span>
> 
> <span style="color:red">Remove-AzureStorageAccount: onjuiste aanvraag: het opslag account myclassicaccount heeft een of meer actieve installatie kopieën (n) en/of schijven, bijvoorbeeld  
> myclassicaccount. Zorg ervoor dat deze installatie kopie (n) en/of schijven zijn verwijderd voordat u dit opslag account verwijdert.</span>

## <a name="unable-to-delete-storage-container"></a>Kan de opslag container niet verwijderen

Wanneer een gebruiker probeert een klassieke opslag BLOB-container te verwijderen die niet meer nodig is, ziet de gebruiker mogelijk het volgende gedrag.

#### <a name="azure-portal"></a>Azure Portal 
Azure Portal de gebruiker niet toe te staan om een container te verwijderen als er een lease van een schijf bestaat die verwijst naar een blob-bestand van de VHD-pagina in de container. Het ontwerp om onbedoelde verwijdering te voor komen van een VHD-bestand met een lease (s) op de schijven. 

![Scherm opname van de portal, met het deel venster opslag container "lijst" geopend](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_container_portal.jpg)


#### <a name="azure-powershell"></a>Azure PowerShell
Als de gebruiker kiest om te verwijderen met behulp van Power shell, resulteert dit in de volgende fout. 

> <span style="color:cyan">**Remove-AzureStorageContainer-context $context naam-vhd's**</span>
> 
> <span style="color:red">Remove-AzureStorageContainer: de externe server heeft een fout geretourneerd: (412) er is momenteel een lease in de container en er is geen lease-ID opgegeven in de aanvraag. HTTP-status code: 412-HTTP-fout bericht: er is momenteel een lease in de container en er is geen lease-ID opgegeven in de aanvraag.</span>

## <a name="unable-to-delete-a-vhd"></a>Kan een VHD niet verwijderen 

Na het verwijderen van de virtuele machine van Azure probeert de gebruiker het VHD-bestand (pagina-blob) te verwijderen en wordt het volgende bericht weer gegeven:

#### <a name="azure-portal"></a>Azure Portal 
Op de portal zijn er twee ervaringen, afhankelijk van de lijst met blobs die voor verwijdering zijn geselecteerd.

1. Als alleen ' geleasde ' blobs zijn geselecteerd, wordt de knop verwijderen niet weer gegeven.
![Scherm opname van de portal, met het deel venster container BLOB-lijst geopend en alleen geleasde blobs geselecteerd.](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_portal.jpg)


2. Als er een combi natie van ' geleasde ' en ' beschik bare ' blobs zijn geselecteerd, wordt de knop verwijderen weer gegeven. Maar de bewerking ' verwijderen ' laat achter de pagina-blobs, die een lease voor de schijf bevatten. 
![Scherm opname van de portal, met het deel venster container BLOB-lijst geopend en zowel geleaste als beschik bare blobs geselecteerd. ](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_1.jpg)
 ![ Scherm opname van de portal, met het geselecteerde deel venster voor het verwijderen van de BLOB openen](./media/storage-classic-cannot-delete-storage-account-container-vhd/unable_to_delete_vhd_leased_and_unleased_portal_2.jpg)

#### <a name="azure-powershell"></a>Azure PowerShell 
Als de gebruiker kiest om te verwijderen met behulp van Power shell, resulteert dit in de volgende fout. 

> <span style="color:cyan">**Remove-AzureStorageBlob-context $context-container vhd's-blob "classicvm-OS-8698. VHD"**</span>
> 
> <span style="color:red">Remove-AzureStorageBlob: de externe server heeft een fout geretourneerd: (412) er is momenteel een lease op de BLOB en er is geen lease-ID opgegeven in de aanvraag. HTTP-status code: 412-HTTP-fout bericht: er is momenteel een lease voor de BLOB en er is geen lease-ID opgegeven in de aanvraag.</span>


## <a name="resolution-steps"></a>Stappen om het probleem op te lossen

### <a name="to-remove-classic-disks"></a>Klassieke schijven verwijderen
Volg deze stappen op de Azure Portal:
1.  Navigeer naar [Azure Portal](https://portal.azure.com).
2.  Navigeer naar de schijven (klassiek). 
3.  Klik op het tabblad schijven. ![ Scherm afbeelding toont de Azure Portal met schijven (klassiek) geselecteerd en een klassieke schijf naam en opslag account.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_disks_tab.jpg)
 
4.  Selecteer de gegevensschijf en klik vervolgens op Schijf verwijderen.
 ![Scherm afbeelding toont de Azure Portal met schijven (klassiek) geselecteerd, waarbij een gegevens schijf is geselecteerd en de optie voor verwijderen.](./media/storage-classic-cannot-delete-storage-account-container-vhd/resolution_click_delete_disk.jpg)
 
5.  Voer de Verwijder bewerking opnieuw uit die eerder is mislukt.
6.  Een opslag account of-container kan niet worden verwijderd zolang deze één schijf heeft.

### <a name="to-remove-classic-images"></a>Klassieke installatie kopieën verwijderen   
Volg deze stappen op de Azure Portal:
1.  Navigeer naar [Azure Portal](https://portal.azure.com).
2.  Navigeer naar besturingssysteem installatie kopieën (klassiek).
3.  Verwijder de installatie kopie.
4.  Voer de Verwijder bewerking opnieuw uit die eerder is mislukt.
5.  Een opslag account of-container kan niet worden verwijderd zolang deze één installatie kopie heeft.
