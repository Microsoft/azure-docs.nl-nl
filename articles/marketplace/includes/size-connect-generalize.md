---
title: bestand opnemen
description: file
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: include
author: mingshen-ms
ms.author: krsh
ms.date: 10/20/2020
ms.openlocfilehash: addc18a0ebf9e49d3474d3f40cb1e2a6e0f0b272
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97826582"
---
## <a name="generalize-the-image"></a>De installatie kopie generaliseren

Alle installatie kopieën in azure Marketplace moeten op een algemene manier opnieuw worden gebruikt. Om dit te verhelpen, moet de VHD van het besturings systeem worden gegeneraliseerd, een bewerking waarbij alle instantie-specifieke id's en software stuur Programma's van een virtuele machine worden verwijderd.

### <a name="for-windows"></a>Voor Windows

Windows-besturingssysteem schijven worden gegeneraliseerd met het hulp programma [Sysprep](/windows-hardware/manufacture/desktop/sysprep--system-preparation--overview) . Als u het besturings systeem later bijwerkt of opnieuw configureert, moet u Sysprep opnieuw uitvoeren.

> [!WARNING]
> Nadat u Sysprep hebt uitgevoerd, schakelt u de virtuele machine uit totdat deze is geïmplementeerd, omdat updates mogelijk automatisch worden uitgevoerd. Als u dit afsluit, voor komt u dat latere updates exemplaren van specifieke wijzigingen aanbrengen in het besturings systeem of de geïnstalleerde services. Zie [stappen voor het generaliseren van een VHD](../../virtual-machines/windows/capture-image-resource.md#generalize-the-windows-vm-using-sysprep)voor meer informatie over het uitvoeren van Sysprep.

### <a name="for-linux"></a>Voor Linux

Het volgende proces generaliseert een Linux-VM en implementeert deze opnieuw als een afzonderlijke VM. Zie [een installatie kopie van een virtuele machine of VHD maken](../../virtual-machines/linux/capture-image.md)voor meer informatie. U kunt stoppen wanneer u de sectie met de naam ' een virtuele machine maken vanuit de vastgelegde installatie kopie ' bereikt.

1. Verwijder de Azure Linux-agent.
    1. Maak verbinding met uw virtuele Linux-machine met behulp van een SSH-client.
    2. Voer in het SSH-venster de volgende opdracht in: `sudo waagent –deprovision+user` .
    3. Typ j om door te gaan (u kunt de para meter-Force toevoegen aan de vorige opdracht om te voor komen dat de bevestigings stap wordt uitgevoerd).
    4. Nadat de opdracht is voltooid, voert u **Afsluiten** in om de SSH-client te sluiten.
2. Stop de virtuele machine.
    1. Selecteer in de Azure Portal uw resource groep (RG) en de virtuele machine opnieuw toe te wijzen.
    2. Uw VM is nu gegeneraliseerd en u kunt een nieuwe virtuele machine maken met behulp van deze VM-schijf.

### <a name="take-a-snapshot-of-the-vm-disk"></a>Een moment opname van de VM-schijf maken

1. Meld u aan bij de [Azure-portal](https://ms.portal.azure.com/).
2. Selecteer **een resource maken** in de linkerbovenhoek, zoek naar en selecteer **moment opname**.
3. Selecteer  **maken** op de Blade moment opname.
4. Voer een **naam** in voor de moment opname.
5. Selecteer een bestaande resource groep of voer een naam in voor een nieuwe.
6. Voor de **bron schijf** selecteert u de beheerde schijf voor de moment opname.
7. Het **account type** selecteren dat moet worden gebruikt voor het opslaan van de moment opname. Gebruik **Standard-HDD** tenzij u het hebt opgeslagen op een high-upssd.
8. Selecteer **Maken**.

#### <a name="extract-the-vhd"></a>De VHD extra heren

Gebruik het volgende script om de moment opname te exporteren naar een VHD in uw opslag account.

```azurecli-interactive
#Provide the subscription Id where the snapshot is created
$subscriptionId=yourSubscriptionId

#Provide the name of your resource group where the snapshot is created
$resourceGroupName=myResourceGroupName

#Provide the snapshot name
$snapshotName=mySnapshot

#Provide Shared Access Signature (SAS) expiry duration in seconds (such as 3600)
#Know more about SAS here: https://docs.microsoft.com/en-us/azure/storage/storage-dotnet-shared-access-signature-part-1
$sasExpiryDuration=3600

#Provide storage account name where you want to copy the underlying VHD file. 
$storageAccountName=mystorageaccountname

#Name of the storage container where the downloaded VHD will be stored.
$storageContainerName=mystoragecontainername

#Provide the key of the storage account where you want to copy the VHD 
$storageAccountKey=mystorageaccountkey

#Give a name to the destination VHD file to which the VHD will be copied.
$destinationVHDFileName=myvhdfilename.vhd

az account set --subscription $subscriptionId

sas=$(az snapshot grant-access --resource-group $resourceGroupName --name $snapshotName --duration-in-seconds $sasExpiryDuration --query [accessSas] -o tsv)

az storage blob copy start --destination-blob $destinationVHDFileName --destination-container $storageContainerName --account-name $storageAccountName --account-key $storageAccountKey --source-uri $sas
```

#### <a name="script-explanation"></a>Uitleg van het script

In dit script worden de volgende opdrachten gebruikt om de SAS-URI voor een moment opname te genereren en wordt de onderliggende VHD gekopieerd naar een opslag account met behulp van de SAS-URI. Elke opdracht in de tabel is een koppeling naar specifieke documentatie over de opdracht.

| Opdracht | Opmerkingen |
| --- | --- |
| az disk grant-access | Hiermee genereert u een SAS met het kenmerk alleen-lezen die wordt gebruikt om het onderliggende VHD-bestand te kopiëren naar een opslagaccount of om het bestand on-premises te downloaden
| az storage blob copy start | Hiermee wordt een BLOB Asynchroon gekopieerd van het ene opslag account naar het andere. Gebruiken `az storage blob show` om de status van de nieuwe BLOB te controleren. |
|
