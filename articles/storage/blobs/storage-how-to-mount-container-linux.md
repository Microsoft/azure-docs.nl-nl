---
title: Azure Blob-opslag koppelen als een bestands systeem op Linux | Microsoft Docs
description: Meer informatie over het koppelen van een Azure Blob Storage-container met blobfuse, een virtueel bestandssysteem stuur programma in Linux.
author: rishabpoh
ms.service: storage
ms.subservice: blobs
ms.topic: how-to
ms.date: 2/1/2019
ms.author: ripohane
ms.reviewer: dineshm
ms.openlocfilehash: 331ceb6124463604989116ce84bc73acd8cfb22f
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483246"
---
# <a name="how-to-mount-blob-storage-as-a-file-system-with-blobfuse"></a>Blob-opslag koppelen als een bestands systeem met blobfuse

## <a name="overview"></a>Overzicht
[Blobfuse](https://github.com/Azure/azure-storage-fuse) is een stuurprogramma voor het virtuele bestandssysteem voor Azure Blob Storage. Met Blobfuse krijgt u toegang tot uw bestaande blok-BLOB-gegevens in uw opslag account via het Linux-bestands systeem. Blobfuse maakt gebruik van het schema van de virtuele map met de slash/'/' als scheidings teken.  

In deze hand leiding wordt beschreven hoe u blobfuse gebruikt en hoe u een BLOB storage-container koppelt aan Linux en toegang tot gegevens. Lees de informatie in [de blobfuse-opslag plaats](https://github.com/Azure/azure-storage-fuse)voor meer informatie over blobfuse.

> [!WARNING]
> Blobfuse garandeert niet 100% POSIX-compatibiliteit, omdat de aanvragen eenvoudigweg worden omgezet in [BLOB rest api's](/rest/api/storageservices/blob-service-rest-api). Wijzig de naam van bewerkingen bijvoorbeeld Atomic in POSIX, maar niet in blobfuse.
> Ga naar [de blobfuse-bron code opslagplaats](https://github.com/azure/azure-storage-fuse)voor een volledige lijst met verschillen tussen een systeem eigen bestands systeem en blobfuse.
> 

## <a name="install-blobfuse-on-linux"></a>Blobfuse installeren in Linux
Blobfuse binaire bestanden zijn beschikbaar in [de micro soft-software opslagplaatsen voor Linux](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software) voor Ubuntu-en RHEL-distributies. Als u blobfuse voor deze distributies wilt installeren, configureert u een van de opslag plaatsen in de lijst. U kunt ook de binaire bestanden van de bron code maken na de [Azure Storage installatie stappen](https://github.com/Azure/azure-storage-fuse/wiki/1.-Installation#option-2---build-from-source) als er geen binaire bestanden beschikbaar zijn voor uw distributie.

Blobfuse ondersteunt de installatie op Ubuntu 14,04, 16,04, 18,04 en 20,04. Voer deze opdracht uit om ervoor te zorgen dat er een van de volgende versies is geïmplementeerd:
```
lsb_release -a
```

### <a name="configure-the-microsoft-package-repository"></a>De micro soft-pakket opslagplaats configureren
Configureer de [opslag plaats voor Linux-pakketten voor micro soft-producten](/windows-server/administration/Linux-Package-Repository-for-Microsoft-Software).

Als voor beeld voor een Enter prise Linux 6-distributie:
```bash
sudo rpm -Uvh https://packages.microsoft.com/config/rhel/6/packages-microsoft-prod.rpm
```

Wijzig ook de URL naar `.../rhel/7/...` om naar een Enter prise Linux 7-distributie te verwijzen.

Een ander voor beeld van een Ubuntu 14,04-distributie:
```bash
wget https://packages.microsoft.com/config/ubuntu/14.04/packages-microsoft-prod.deb
sudo dpkg -i packages-microsoft-prod.deb
sudo apt-get update
```

Wijzig ook de URL naar `.../ubuntu/16.04/...` of `.../ubuntu/18.04/...` om te verwijzen naar een andere Ubuntu-versie.

### <a name="install-blobfuse"></a>Blobfuse installeren

Op een Ubuntu/Debian-distributie:
```bash
sudo apt-get install blobfuse
```

Bij een Enter prise Linux-distributie:
```bash    
sudo yum install blobfuse
```

## <a name="prepare-for-mounting"></a>Voorbereiden voor koppelen
Blobfuse biedt systeem eigen-achtige prestaties door een tijdelijk pad in het bestands systeem te vereisen om geopende bestanden te bufferen en in de cache op te slaan. Voor dit tijdelijke pad kiest u de meest geschikte schijf of gebruikt u een Ramdisk voor de beste prestaties. 

> [!NOTE]
> Blobfuse slaat alle geopende bestands inhoud op in het tijdelijke pad. Zorg ervoor dat er voldoende ruimte beschikbaar is voor alle geopende bestanden. 
> 

### <a name="optional-use-a-ramdisk-for-the-temporary-path"></a>Beschrijving Een ramdisk gebruiken voor het tijdelijke pad
In het volgende voor beeld wordt een ramdisk van 16 GB en een map voor blobfuse gemaakt. Kies de grootte op basis van uw behoeften. Met deze ramdisk kan blobfuse bestanden openen die groter zijn dan 16 GB. 
```bash
sudo mkdir /mnt/ramdisk
sudo mount -t tmpfs -o size=16g tmpfs /mnt/ramdisk
sudo mkdir /mnt/ramdisk/blobfusetmp
sudo chown <youruser> /mnt/ramdisk/blobfusetmp
```

### <a name="use-an-ssd-as-a-temporary-path"></a>Een SSD gebruiken als een tijdelijk pad
In azure kunt u gebruikmaken van de tijdelijke schijven (SSD) die beschikbaar zijn op uw virtuele machines om een lage latentie buffer te bieden voor blobfuse. In Ubuntu-distributies wordt deze tijdelijke schijf gekoppeld op '/mnt '. In Red Hat en CentOS-distributies wordt de schijf gekoppeld op '/mnt/resource/'.

Zorg ervoor dat de gebruiker toegang heeft tot het tijdelijke pad:
```bash
sudo mkdir /mnt/resource/blobfusetmp -p
sudo chown <youruser> /mnt/resource/blobfusetmp
```

### <a name="configure-your-storage-account-credentials"></a>De referenties van uw opslag account configureren
Blobfuse vereist dat uw referenties worden opgeslagen in een tekst bestand met de volgende indeling: 

```
accountName myaccount
accountKey storageaccesskey
containerName mycontainer
```
Het `accountName` is het voor voegsel voor uw opslag account, niet de volledige URL.

Dit bestand maken met:

```
touch ~/fuse_connection.cfg
```

Wanneer u dit bestand hebt gemaakt en bewerkt, moet u ervoor zorgen dat u de toegang beperkt zodat andere gebruikers het niet kunnen lezen.
```bash
chmod 600 fuse_connection.cfg
```

> [!NOTE]
> Als u het configuratie bestand in Windows hebt gemaakt, zorg er dan voor dat u `dos2unix` het bestand kunt opschonen en converteren naar de UNIX-indeling. 
>

### <a name="create-an-empty-directory-for-mounting"></a>Een lege directory maken om te koppelen
```bash
mkdir ~/mycontainer
```

## <a name="mount"></a>Koppelen

> [!NOTE]
> Controleer [de blobfuse-opslag plaats](https://github.com/Azure/azure-storage-fuse#mount-options)voor een volledige lijst met koppelings opties.  
> 

Als u blobfuse wilt koppelen, voert u de volgende opdracht uit met uw gebruiker. Met deze opdracht koppelt u de container die is opgegeven in '/Path/to/fuse_connection. cfg ' op de locatie '/mycontainer '.

```bash
sudo blobfuse ~/mycontainer --tmp-path=/mnt/resource/blobfusetmp  --config-file=/path/to/fuse_connection.cfg -o attr_timeout=240 -o entry_timeout=240 -o negative_timeout=120
```

U hebt nu toegang tot uw blok-blobs via de reguliere Api's van het bestands systeem. De gebruiker die de Directory koppelt, is standaard de enige persoon die toegang heeft tot de map, waardoor de toegang wordt beveiligd. Als u toegang tot alle gebruikers wilt toestaan, kunt u koppelen via de optie ```-o allow_other``` . 

```bash
cd ~/mycontainer
mkdir test
echo "hello world" > test/blob.txt
```

## <a name="next-steps"></a>Volgende stappen

* [Start pagina van Blobfuse](https://github.com/Azure/azure-storage-fuse#blobfuse)
* [Blobfuse problemen melden](https://github.com/Azure/azure-storage-fuse/issues)
