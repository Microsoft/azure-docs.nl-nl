---
title: Azure Data Box Disk problemen met het kopiëren van gegevens oplossen | Microsoft Docs
description: Hierin wordt beschreven hoe u problemen oplost die zijn opgetreden tijdens het kopiëren van gegevens in Azure Data Box Disk het gebruik van Logboeken.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/13/2019
ms.author: alkohli
ms.openlocfilehash: 5d977fe0b7459af35f678e77681d3b27c31431cc
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85849178"
---
# <a name="troubleshoot-data-copy-issues-in-azure-data-box-disk"></a>Problemen met het kopiëren van gegevens in Azure Data Box Disk oplossen

Dit artikel is van toepassing op Microsoft Azure Data Box Disk en beschrijft het oplossen van problemen die worden weer gegeven wanneer u de gegevens naar de schijven kopieert. Het artikel heeft ook betrekking op problemen bij het gebruik van het hulp programma voor het splitsen van kopiëren.


## <a name="data-copy-issues-when-using-a-linux-system"></a>Problemen bij het kopiëren van gegevens wanneer een Linux-systeem wordt gebruikt

In deze sectie vindt u meer informatie over enkele van de meest voorkomende problemen bij het gebruik van een Linux-client om gegevens naar schijven te kopiëren.

### <a name="issue-drive-getting-mounted-as-read-only"></a>Probleem: station wordt gekoppeld als alleen-lezen
 
**Oorzaak** 

Dit kan worden veroorzaakt door een niet-gereinigd bestands systeem.

Het opnieuw koppelen van een station als lezen/schrijven werkt niet met Data Box schijven. Dit scenario wordt niet ondersteund met stations die zijn ontsleuteld door de delocker. Het apparaat is mogelijk opnieuw gekoppeld met de volgende opdracht:

```
# mount -o remount, rw /mnt/DataBoxDisk/mountVol1
```

Het opnieuw koppelen is geslaagd, maar de gegevens blijven niet behouden.

**Oplossing**

Voer de volgende stappen uit op uw Linux-systeem:

1. Installeer het `ntfsprogs` pakket voor het hulp programma ntfsfix.
2. Ontkoppel de koppel punten die voor het station zijn meegeleverd met het hulp programma voor ontgrendelen. Het aantal koppel punten voor stations is afhankelijk.

    ```
    unmount /mnt/DataBoxDisk/mountVol1
    ```

3. Voer uit `ntfsfix` op het bijbehorende pad. Het gemarkeerde aantal moet gelijk zijn aan stap 2.

    ```
    ntfsfix /mnt/DataBoxDisk/bitlockerVol1/dislocker-file
    ```

4. Voer de volgende opdracht uit om de meta gegevens van de winterslaap stand te verwijderen die het koppelings probleem kunnen veroorzaken.

    ```
    ntfs-3g -o remove_hiberfile /mnt/DataBoxDisk/bitlockerVol1/dislocker-file /mnt/DataBoxDisk/mountVol1
    ```

5. Maak een schone ontkoppeling.

    ```
    ./DataBoxDiskUnlock_x86_64 /unmount
    ```

6. Maak een schone ontgrendeling en koppeling.
7. Test het koppel punt door een bestand te schrijven.
8. Ontkoppelen en opnieuw koppelen om de bestands persistentie te valideren.
9. Ga door met het kopiëren van de gegevens.
 
### <a name="issue-error-with-data-not-persisting-after-copy"></a>Probleem: fout met gegevens die niet worden bewaard na kopiëren
 
**Oorzaak** 

Als u ziet dat uw station geen gegevens bevat nadat het is ontkoppeld (terwijl er gegevens naar zijn gekopieerd), is het mogelijk dat u een station hebt gekoppeld als lezen-schrijven nadat het station als alleen-lezen is gekoppeld.

**Oplossing**
 
Als dat het geval is, raadpleegt u de oplossing voor stations die worden [gekoppeld als alleen-lezen](#issue-drive-getting-mounted-as-read-only).

Als dat niet het geval is, kopieert u de logboeken van de map met het hulp programma voor het ontgrendelen van Data Box Disk en [neemt u contact op met Microsoft ondersteuning](data-box-disk-contact-microsoft-support.md).


## <a name="data-box-disk-split-copy-tool-errors"></a>Fouten met het splits- en kopieerprogramma voor Data Box Disk

De problemen die zich voordoen bij het gebruik van een splits Copy-hulp programma om de gegevens op meerdere schijven te splitsen, worden in de volgende tabel weer gegeven.

|Foutbericht/waarschuwingen |Aanbevelingen |
|---------|---------|
|Valuta Het BitLocker-wacht woord voor het volume wordt opgehaald: m <br>Optreedt Er is een uitzonde ring opgetreden tijdens het ophalen van de BitLocker-sleutel voor volume m:<br> Reeks bevat geen elementen.|Deze fout wordt gegenereerd als de doel-Data Box Disk offline is. <br> Gebruik hulpprogramma `diskmgmt.msc` voor online schijven.|
|[Fout] Uitzondering opgetreden: WMI-bewerking is mislukt:<br> Method=UnlockWithNumericalPassword, ReturnValue=2150694965, <br>Win32Message=De indeling van het verstrekte herstelwachtwoord is ongeldig. <br>BitLocker-herstelwachtwoorden bestaan uit 48 cijfers. <br>Controleer of het herstelwachtwoord de juiste indeling heeft en probeer het opnieuw.|Gebruik eerst het ontgrendelingsprogramma van Data Box Disk om de schijven te ontgrendelen en voer dan de opdracht opnieuw uit. Voor meer informatie gaat u naar <li> [Data Box Disk ontgrendelen voor Windows-clients](data-box-disk-deploy-set-up.md#unlock-disks-on-windows-client). </li><li> [Data Box Disk ontgrendelen voor Linux-clients](data-box-disk-deploy-set-up.md#unlock-disks-on-linux-client). </li>|
|[Fout] Uitzondering opgetreden: er bestaat een bestand DriveManifest.xml in het doelstation. <br> Dit betekent dat het doelstation mogelijk is voorbereid met een ander logboekbestand. <br>Als u meer gegevens aan hetzelfde station wilt toevoegen, gebruik dan het vorige logboekbestand. Als u bestaande gegevens wilt verwijderen en het doel station opnieuw wilt gebruiken voor een nieuwe import taak, verwijdert u de *DriveManifest.xml* op het station. Voer deze opdracht opnieuw uit met een nieuw logboekbestand.| Deze fout wordt gegenereerd wanneer u dezelfde reeks stations probeert te gebruiken voor meerdere importsessies. <br> Gebruik één set schijven maar voor één splits- en kopieersessie.|
|[Fout] Uitzondering opgetreden: CopySessionId importdata-sept-test-1 verwijst naar een eerdere kopieersessie en kan niet worden hergebruikt voor een nieuwe kopieersessie.|Deze fout wordt gerapporteerd wanneer wordt geprobeerd om dezelfde taaknaam voor een nieuwe taak te gebruiken als een eerder met succes voltooide taak.<br> Geef een unieke naam op voor de nieuwe taak.|
|[Info] De naam van het doelbestand of de doelmap overschrijdt de lengtelimiet van NTFS. |Dit bericht wordt gegenereerd als de naam van het doelbestand is gewijzigd vanwege een lang pad.<br> Wijzig de verwijderingsoptie in bestand `config.json` om dit gedrag te regelen.|
|[Fout] Uitzondering opgetreden: ongeldige JSON-escape-volgorde. |Dit bericht wordt gegenereerd als de config.json een ongeldige opmaak heeft. <br> Valideer uw `config.json` met behulp van [JSONlint](https://jsonlint.com/) voordat u het bestand opslaat.|


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [oplossen van problemen met validatie hulpprogramma's](data-box-disk-troubleshoot.md).
