---
title: Problemen in Azure Data Box Disk oplossen | Microsoft Docs
description: Informatie over het gebruik van Logboeken voor het oplossen van problemen met validatie problemen die kunnen optreden wanneer u Azure Data Box Disk implementeert.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/14/2019
ms.author: alkohli
ms.openlocfilehash: fa8a6643f1b7bd60fbf6e5950234e0381666177e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97605221"
---
# <a name="use-logs-to-troubleshoot-validation-issues-in-azure-data-box-disk"></a>Logboeken gebruiken om problemen met validatie in Azure Data Box Disk op te lossen

Dit artikel is van toepassing op Microsoft Azure Data Box Disk. In dit artikel wordt beschreven hoe u de logboeken gebruikt voor het oplossen van de validatie problemen die u kunt zien wanneer u deze oplossing implementeert.

## <a name="validation-tool-log-files"></a>Validatie hulpprogramma logboek bestanden

Wanneer u de gegevens op de schijven valideert met het [validatie hulpprogramma](data-box-disk-deploy-copy-data.md#validate-data), wordt er een *error.xml* gegenereerd om eventuele fouten te registreren. Het logboek bestand bevindt zich in de  `Drive:\DataBoxDiskImport\logs` map van uw station. Er wordt een koppeling naar het fouten logboek weer gegeven wanneer u validatie uitvoert.

<!--![Validation tool with link to error log](media/data-box-disk-troubleshoot/validation-tool-link-error-log.png)-->

Als u meerdere sessies voor validatie uitvoert, wordt er per sessie één fouten logboek gegenereerd.

- Hier volgt een voor beeld van het fouten logboek wanneer de gegevens die in de `PageBlob` map worden geladen, niet 512 zijn uitgelijnd. Alle gegevens die zijn geüpload naar PageBlob moeten 512-bytes uitgelijnd zijn, bijvoorbeeld een VHD of VHDX. De fouten in dit bestand bevinden zich in de `<Errors>` waarschuwingen en in `<Warnings>` .

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
        <ErrorLog Version="2018-10-01">
            <SessionId>session#1</SessionId>
            <ItemType>PageBlob</ItemType>
            <SourceDirectory>D:\Dataset\TestDirectory</SourceDirectory>
            <Errors>
                <Error Code="Not512Aligned">
                    <Description>The file is not 512 bytes aligned.</Description>
                    <List>
                        <File Path="\Practice\myScript.ps1" />
                    </List>
                    <Count>1</Count>
                </Error>
            </Errors>
            <Warnings />
        </ErrorLog>
    ```

- Hier volgt een voor beeld van het fouten logboek wanneer de container naam ongeldig is. De map die u maakt onder `BlockBlob` , `PageBlob` of `AzureFile` mappen op de schijf wordt een container in uw Azure Storage-account. De naam van de container moet voldoen aan de [Azure-naamgevings conventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions).

    ```xml
    <?xml version="1.0" encoding="utf-8"?>
    <ErrorLog Version="2018-10-01">
        <SessionId>bbsession</SessionId>
        <ItemType>BlockBlob</ItemType>
        <SourceDirectory>E:\BlockBlob</SourceDirectory>
        <Errors>
        <Error Code="InvalidShareContainerFormat">
            <List>
            <Container Name="Azu-reFile" />
            <Container Name="bbcont ainer1" />
            </List>
            <Count>2</Count>
        </Error>
        </Errors>
        <Warnings />
    </ErrorLog>
    ```

## <a name="validation-tool-errors"></a>Validatie hulpprogramma fouten

De fouten die zijn opgetreden in de *error.xml* met de bijbehorende aanbevolen acties, worden in de volgende tabel samenvatten.

| Foutcode| Beschrijving                       | Aanbevolen acties               |
|------------|--------------------------|-----------------------------------|
| `None` | De gegevens zijn gevalideerd. | Geen actie vereist. |
| `InvalidXmlCharsInPath` |Kan geen manifest bestand maken omdat het bestandspad ongeldige tekens bevat. | Verwijder deze tekens om door te gaan.  |
| `OpenFileForReadFailed`| Kan het bestand niet verwerken. Dit kan worden veroorzaakt door een toegangs probleem of beschadiging van het bestands systeem.|Kan het bestand niet lezen wegens een fout. De fout Details zijn in de uitzonde ring. |
| `Not512Aligned` | Dit bestand heeft geen geldige indeling voor de map PageBlob.| Upload alleen gegevens die 512 bytes zijn uitgelijnd op de `PageBlob` map. Verwijder het bestand uit de map PageBlob of verplaats het naar de map BlockBlob. Voer de validatie opnieuw uit.|
| `InvalidBlobPath` | Het bestandspad wordt niet toegewezen aan een geldig BLOB-pad in de Cloud volgens de naamgevings conventies van Azure Blob.|Volg de naamgevings richtlijnen van Azure om de naam van het bestandspad te wijzigen. |
| `EnumerationError` | Kan het bestand niet opsommen voor validatie. |Er kunnen meerdere oorzaken voor deze fout zijn. De meest waarschijnlijke reden hiervoor is toegang tot het bestand. |
| `ShareSizeExceeded` | Dit bestand veroorzaakt dat de grootte van de Azure-bestands share de Azure-limiet van 5 TB overschrijdt.|Verklein de grootte van de gegevens in de share, zodat deze voldoet aan de [limieten](data-box-disk-limits.md#azure-object-size-limits)voor de grootte van Azure-objecten. Voer de validatie opnieuw uit. |
| `AzureFileSizeExceeded` | De bestands grootte overschrijdt de limieten voor de grootte van Azure-bestanden.| Verklein de grootte van het bestand of de gegevens, zodat deze voldoet aan de [limieten](data-box-disk-limits.md#azure-object-size-limits)voor de grootte van Azure-objecten. Voer de validatie opnieuw uit.|
| `BlockBlobSizeExceeded` | De bestands grootte overschrijdt de limieten voor de grootte van Azure Block blob. | Verklein de grootte van het bestand of de gegevens, zodat deze voldoet aan de [limieten](data-box-disk-limits.md#azure-object-size-limits)voor de grootte van Azure-objecten. Voer de validatie opnieuw uit. |
| `ManagedDiskSizeExceeded` | De bestands grootte overschrijdt de grootte limieten voor Azure Managed disks. | Verklein de grootte van het bestand of de gegevens, zodat deze voldoet aan de [limieten](data-box-disk-limits.md#azure-object-size-limits)voor de grootte van Azure-objecten. Voer de validatie opnieuw uit. |
| `PageBlobSizeExceeded` | De bestands grootte overschrijdt de grootte limieten voor Azure Managed disks. | Verklein de grootte van het bestand of de gegevens, zodat deze voldoet aan de [limieten](data-box-disk-limits.md#azure-object-size-limits)voor de grootte van Azure-objecten. Voer de validatie opnieuw uit. |
| `InvalidShareContainerFormat`  |De mapnamen voldoen niet aan de naamgevings conventies van Azure voor containers of shares.         |De eerste map die in de bestaande mappen op de schijf wordt gemaakt, wordt een container in uw opslag account. Deze share-of container naam voldoet niet aan de naamgevings conventies van Azure. Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevings conventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Voer de validatie opnieuw uit.   |
| `InvalidBlobNameFormat` | Het bestandspad wordt niet toegewezen aan een geldig BLOB-pad in de Cloud volgens de naamgevings conventies van Azure Blob.|Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevings conventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Voer de validatie opnieuw uit. |
| `InvalidFileNameFormat` | Het bestandspad wordt niet toegewezen aan een geldig bestandspad in de Cloud volgens de naamgevings conventies voor Azure-bestanden. |Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevings conventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Voer de validatie opnieuw uit. |
| `InvalidDiskNameFormat` | Het bestandspad wordt niet toegewezen aan een geldige schijf naam in de Cloud volgens de naamgevings conventies voor Azure Managed disks. |Wijzig de naam van het bestand zodat het voldoet aan [Azure-naamgevings conventies](data-box-disk-limits.md#azure-block-blob-page-blob-and-file-naming-conventions). Voer de validatie opnieuw uit.       |
| `NotPartOfFileShare` | Het pad voor het uploaden van bestanden is niet geldig. Upload de bestanden naar een map in Azure Files.   | Verwijder de bestanden met de fout en upload deze bestanden naar een map die u hebt gemaakt. Voer de validatie opnieuw uit. |
| `NonVhdFileNotSupportedForManagedDisk` | Een niet-VHD-bestand kan niet worden geüpload als een beheerde schijf. |Verwijder de niet-VHD-bestanden uit `ManagedDisk` de map omdat deze niet worden ondersteund of verplaats deze bestanden naar een `PageBlob` map. Voer de validatie opnieuw uit. |


## <a name="next-steps"></a>Volgende stappen

- Fouten bij het [uploaden van gegevens](data-box-disk-troubleshoot-upload.md)oplossen.
