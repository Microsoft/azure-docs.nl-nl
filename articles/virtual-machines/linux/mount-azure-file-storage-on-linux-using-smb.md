---
title: Azure File Storage koppelen aan linux-Vm's met behulp van SMB
description: Azure File Storage koppelen op Linux Vm's met behulp van SMB met de Azure CLI
author: cynthn
ms.service: virtual-machines-linux
ms.topic: how-to
ms.workload: infrastructure
ms.date: 06/28/2018
ms.author: cynthn
ms.openlocfilehash: 6a5d4f388d9e5f47a13812687489525590c520c3
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96016094"
---
# <a name="mount-azure-file-storage-on-linux-vms-using-smb"></a>Azure File Storage koppelen aan linux-Vm's met behulp van SMB

In dit artikel wordt beschreven hoe u de Azure File Storage-service op een virtuele Linux-machine gebruikt met een SMB-koppeling met de Azure CLI. Azure File Storage biedt bestands shares in de Cloud met behulp van het standaard SMB-protocol. 

File Storage biedt bestands shares in de cloud die gebruikmaken van het standaard SMB-protocol. U kunt een bestands share koppelen vanuit elk besturings systeem dat SMB 3,0 ondersteunt. Wanneer u een SMB-koppeling in Linux gebruikt, krijgt u eenvoudig back-ups naar een robuuste, duurzame opslag locatie die wordt ondersteund door een SLA.

Het verplaatsen van bestanden van een virtuele machine naar een SMB-koppeling die wordt gehost op File Storage is een uitstekende manier om fouten in Logboeken op te sporen. Dezelfde SMB-share kan lokaal worden gekoppeld aan uw Mac-, Linux-of Windows-werk station. SMB is niet de beste oplossing voor het streamen van Linux-of toepassings Logboeken in realtime, omdat het SMB-protocol niet is gebouwd om dergelijke zware logboek registratie taken af te handelen. Een speciaal hulp programma voor Unified logging-laag, zoals gefluent, zou een betere keuze zijn dan SMB voor het verzamelen van de uitvoer van Linux-en toepassings Logboeken.

Voor deze hand leiding moet u de Azure CLI-versie 2.0.4 of hoger uitvoeren. Voer **AZ--version** uit om de versie te vinden. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli). 


## <a name="create-a-resource-group"></a>Een resourcegroep maken

Maak een resource groep met de naam *myResourceGroup* op de locatie *VS-Oost* .

```azurecli
az group create --name myResourceGroup --location eastus
```

## <a name="create-a-storage-account"></a>Create a storage account

Maak een nieuw opslag account in de resource groep die u hebt gemaakt met [AZ Storage account create](/cli/azure/storage/account). In dit voor beeld wordt een opslag account gemaakt met de naam *mySTORAGEACCT \<random number>* en wordt de naam van dat opslag account in de variabele **STORAGEACCT** geplaatst. Namen van opslag accounts moeten uniek zijn, waarbij `$RANDOM` een getal wordt toegevoegd aan het einde om het uniek te maken.

```azurecli
STORAGEACCT=$(az storage account create \
    --resource-group "myResourceGroup" \
    --name "mystorageacct$RANDOM" \
    --location eastus \
    --sku Standard_LRS \
    --query "name" | tr -d '"')
```

## <a name="get-the-storage-key"></a>De opslag sleutel ophalen

Wanneer u een opslag account maakt, worden de account sleutels in paren gemaakt zodat ze zonder onderbreking van de service kunnen worden gedraaid. Wanneer u overschakelt naar de tweede sleutel in de paar, maakt u een nieuw sleutel paar. Nieuwe sleutels voor opslag accounts worden altijd in paren gemaakt, zodat u altijd ten minste één ongebruikte sleutel voor het opslag account hebt om over te scha kelen naar.

Bekijk de sleutels voor het opslag account met behulp van [AZ Storage account Keys List](/cli/azure/storage/account/keys). In dit voor beeld wordt de waarde van sleutel 1 in de variabele **STORAGEKEY** opgeslagen.

```azurecli
STORAGEKEY=$(az storage account keys list \
    --resource-group "myResourceGroup" \
    --account-name $STORAGEACCT \
    --query "[0].value" | tr -d '"')
```

## <a name="create-a-file-share"></a>Een bestandsshare maken

Maak de bestands opslag share met [AZ Storage share Create](/cli/azure/storage/share). 

Share namen moeten bestaan uit kleine letters, cijfers en enkele afbreek streepjes, maar mogen niet beginnen met een koppel teken. Zie [Shares, mappen, bestanden en metagegevens een naam geven en hiernaar verwijzen](/rest/api/storageservices/naming-and-referencing-shares--directories--files--and-metadata) voor meer informatie over de naamgeving van bestandsshares en bestanden.

In dit voor beeld wordt een share gemaakt met de naam *MyShare* met een GiB-quotum van 10. 

```azurecli
az storage share create --name myshare \
    --quota 10 \
    --account-name $STORAGEACCT \
    --account-key $STORAGEKEY
```

## <a name="create-a-mount-point"></a>Een koppel punt maken

Als u de Azure-bestands share op uw Linux-computer wilt koppelen, moet u ervoor zorgen dat het **CIFS-hulppr-** pakket is geïnstalleerd. Zie voor installatie-instructies [het CIFS-hulppre-pakket installeren voor uw Linux-distributie](../../storage/files/storage-how-to-use-files-linux.md#install-cifs-utils).

Azure Files gebruikt het SMB-protocol, dat communiceert via TCP-poort 445.  Als u problemen ondervindt met het koppelen van uw Azure-bestands share, moet u ervoor zorgen dat de firewall TCP-poort 445 niet blokkeert.


```bash
mkdir -p /mnt/MyAzureFileShare
```

## <a name="mount-the-share"></a>De share koppelen

Koppel de Azure-bestands share aan de lokale map. 

```bash
sudo mount -t cifs //$STORAGEACCT.file.core.windows.net/myshare /mnt/MyAzureFileShare -o vers=3.0,username=$STORAGEACCT,password=$STORAGEKEY,dir_mode=0777,file_mode=0777,serverino
```

De bovenstaande opdracht gebruikt de [koppelings](https://linux.die.net/man/8/mount) opdracht voor het koppelen van de Azure-bestands share en opties die specifiek zijn voor [CIFS](https://linux.die.net/man/8/mount.cifs). Met name de opties file_mode en dir_mode stellen bestanden en mappen in op machtigingen `0777` . De `0777` machtiging biedt Lees-, schrijf-en uitvoer machtigingen voor alle gebruikers. U kunt deze machtigingen wijzigen door de waarden te vervangen door andere [CHMOD-machtigingen](https://en.wikipedia.org/wiki/Chmod). U kunt ook andere [CIFS](https://linux.die.net/man/8/mount.cifs) -opties gebruiken, zoals GID of UID. 


## <a name="persist-the-mount"></a>De koppeling persistent maken

Wanneer u de virtuele Linux-machine opnieuw opstart, wordt de gekoppelde SMB-share tijdens het afsluiten ontkoppeld. Als u de SMB-share opnieuw wilt koppelen bij het opstarten, voegt u een regel toe aan de Linux-/etc/fstab. Linux maakt gebruik van het fstab-bestand om de bestands systemen weer te geven die moeten worden gekoppeld tijdens het opstart proces. Door de SMB-share toe te voegen, zorgt u ervoor dat de bestands opslag share permanent is gekoppeld aan het bestands systeem voor de virtuele Linux-machine. Het toevoegen van SMB-share File Storage aan een nieuwe VM is mogelijk wanneer u Cloud-init gebruikt.

```bash
//myaccountname.file.core.windows.net/mystorageshare /mnt/mymountpoint cifs vers=3.0,username=mystorageaccount,password=myStorageAccountKeyEndingIn==,dir_mode=0777,file_mode=0777
```

Voor een betere beveiliging in productie omgevingen moet u uw referenties buiten fstab opslaan.

## <a name="next-steps"></a>Volgende stappen

- [Cloud-init gebruiken voor het aanpassen van een virtuele Linux-machine tijdens het maken](using-cloud-init.md)
- [Een schijf toevoegen aan een virtuele Linux-machine](add-disk.md)
- [Azure Disk Encryption voor Linux-VM's](disk-encryption-overview.md)
