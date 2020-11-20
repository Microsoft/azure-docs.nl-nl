---
title: Back-up en herstel van het besturings systeem van SAP HANA op Azure (grote exemplaren) type II Sku's | Microsoft Docs
description: Back-up en herstel van het besturings systeem uitvoeren voor SAP HANA op Azure (grote exemplaren) type II Sku's
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/12/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: e690f6abc9c597cc1facb7b3e59604bb711cf274
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94955664"
---
# <a name="os-backup-and-restore-for-type-ii-skus-of-revision-3-stamps"></a>Back-up en herstel van het besturings systeem voor de type II Sku's van Revision 3-stem pels

In dit document worden de stappen beschreven voor het uitvoeren van een back-up en herstel van het bestands niveau van het besturings systeem voor de **type II sku's** van de Hana grote instanties van Revision 3. 

>[!Important]
> **Dit artikel is niet van toepassing op implementaties van type II SKU in revisie 4 HANA grote instantie stempels.** Opstart-LUN'S van het type II HANA grote instantie-eenheden die zijn geïmplementeerd in revisie 4 HANA grote instantie-stem pels, kunnen back-ups maken met moment opnamen van de opslag


>[!NOTE]
>De back-upscripten van het besturings systeem gebruiken de achterzijde-software, die vooraf is geïnstalleerd op de server.  

Nadat het inrichten is voltooid door het micro soft- `Service Management` team, is de server standaard geconfigureerd met twee back-upschemas om een back-up te maken van het bestandssysteem niveau van het besturings systeem. U kunt de planningen van de back-uptaken controleren met behulp van de volgende opdracht:
```
#crontab –l
```
U kunt het back-upschema op elk gewenst moment wijzigen met de volgende opdracht:
```
#crontab -e
```
## <a name="how-to-take-a-manual-backup"></a>Hoe kunt u een hand matige back-up maken?

De bestandssysteem back-up van het besturings systeem wordt al gepland met behulp van een **cron-taak** . U kunt echter ook de back-up op bestands niveau van het besturings systeem hand matig uitvoeren. Voer de volgende opdracht uit om een hand matige back-up uit te voeren:

```
#rear -v mkbackup
```
In het volgende scherm wordt de voor beeld-hand matige back-up weer gegeven:

![werking](media/HowToHLI/OSBackupTypeIISKUs/HowtoTakeManualBackup.PNG)


## <a name="how-to-restore-a-backup"></a>Hoe kan ik een back-up herstellen?

U kunt een volledige back-up of een afzonderlijk bestand herstellen vanuit de back-up. Als u wilt herstellen, gebruikt u de volgende opdracht:

```
#tar  -xvf  <backup file>  [Optional <file to restore>]
```
Na het herstellen wordt het bestand in de huidige werkmap hersteld.

Met de volgende opdracht wordt het herstellen van een bestand met back-up van het back-upbestand */etc/fstabfrom* *. tar. gz* weer gegeven.
```
#tar  -xvf  /osbackups/hostname/backup.tar.gz  etc/fstab 
```
>[!NOTE] 
>U moet het bestand naar de gewenste locatie kopiëren nadat het is hersteld vanuit de back-up.

De volgende scherm afbeelding toont het herstellen van een volledige back-up:

![Scherm afbeelding toont een opdracht prompt venster met de herstel bewerking.](media/HowToHLI/OSBackupTypeIISKUs/HowtoRestoreaBackup.PNG)

## <a name="how-to-install-the-rear-tool-and-change-the-configuration"></a>Het hulp programma achteraan installeren en de configuratie wijzigen? 

De pakketten voor het versoepelen en herstellen (achteraan) worden **vooraf geïnstalleerd** in de **type II-Sku's** van Hana grote instanties en er is geen actie nodig van u. U kunt direct beginnen met het gebruik van de back-up voor de reserve kopie van het besturings systeem.
In de omstandigheden waarin u de pakketten zelf moet installeren, kunt u echter de vermelde stappen volgen om het hulp programma achteraan te installeren en configureren.

Gebruik de volgende opdrachten om de back-uppakketen **achter** te installeren:

Voor het besturings systeem **SLES** gebruikt u de volgende opdracht:
```
#zypper install <rear rpm package>
```
Voor het besturings systeem **RHEL** gebruikt u de volgende opdracht: 
```
#yum install rear -y
```
Als u het hulp programma achteraan wilt configureren, moet u de para meters **OUTPUT_URL**  en **BACKUP_URL**  bijwerken in het *bestand/etc/Rear/local.conf*.
```
OUTPUT=ISO
ISO_MKISOFS_BIN=/usr/bin/ebiso
BACKUP=NETFS
OUTPUT_URL="nfs://nfsip/nfspath/"
BACKUP_URL="nfs://nfsip/nfspath/"
BACKUP_OPTIONS="nfsvers=4,nolock"
NETFS_KEEP_OLD_BACKUP_COPY=
EXCLUDE_VG=( vgHANA-data-HC2 vgHANA-data-HC3 vgHANA-log-HC2 vgHANA-log-HC3 vgHANA-shared-HC2 vgHANA-shared-HC3 )
BACKUP_PROG_EXCLUDE=("${BACKUP_PROG_EXCLUDE[@]}" '/media' '/var/tmp/*' '/var/crash' '/hana' '/usr/sap'  ‘/proc’)
```

De volgende scherm afbeelding toont het herstellen van een volledige back-up: ![ scherm afbeelding toont een opdracht prompt venster met het hulp programma achteraan.](media/HowToHLI/OSBackupTypeIISKUs/RearToolConfiguration.PNG)
