---
title: Gelaagde Azure File Sync beheren | Microsoft Docs
description: Tips en PowerShell-commandlets om gelaagde bestanden te beheren
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/13/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: bd740c773998450ef6e8bb95c4df3a1abadaceed
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107796704"
---
# <a name="how-to-manage-tiered-files"></a>Gelaagde bestanden beheren

Dit artikel bevat richtlijnen voor gebruikers die vragen hebben met betrekking tot het beheren van gelaagde bestanden. Zie veelgestelde vragen over Azure Files voor [conceptuele vragen over cloudopslaglagen.](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Controleren of uw bestanden gelaagd zijn

Of bestanden per set beleidsregels gelaagd moeten worden of niet, wordt eenmaal per uur geëvalueerd. Er kunnen zich twee situaties voordoen wanneer een nieuw server-eindpunt wordt gemaakt:

Wanneer u voor het eerst een nieuw server-eindpunt toevoegt, bestaan er vaak bestanden op die serverlocatie. Ze moeten worden geüpload voordat cloudopslaglagen kunnen beginnen. Het beleid voor vrije ruimte op het volume begint pas met het werk als de initiële upload van alle bestanden is voltooid. Het optionele datumbeleid werkt echter op afzonderlijke bestandsbasis zodra een bestand is geüpload. Het interval van één uur is hier ook van toepassing. 

Wanneer u een nieuw server-eindpunt toevoegt, is het mogelijk dat u een lege serverlocatie hebt verbonden met een Azure-bestands share met uw gegevens. Als u ervoor kiest om de naamruimte te downloaden en inhoud terug te halen tijdens de eerste download naar uw server, worden bestanden nadat de naamruimte uitkomt, teruggeroepen op basis van de laatste gewijzigde tijdstempel tot het beleid voor vrije ruimte op het volume en de optionele datumbeleidslimieten zijn bereikt.

Er zijn verschillende manieren om te controleren of een bestand in lagen van uw Azure-bestands share is opgeslagen:
    
   *  **Controleer de bestandskenmerken in het bestand.**
     Klik met de rechtermuisknop op een bestand, ga **naar Details** en schuif omlaag naar de **eigenschap** Kenmerken. Voor een gelaagd bestand zijn de volgende kenmerken ingesteld:     
        
        | Kenmerkletter | Kenmerk | Definitie |
        |:----------------:|-----------|------------|
        | A | Archiveren | Geeft aan dat er een back-up van het bestand moet worden gemaakt door back-upsoftware. Dit kenmerk wordt altijd ingesteld, ongeacht of het bestand is gelaagd of volledig op schijf is opgeslagen. |
        | P | Sparse-bestand | Geeft aan dat het bestand een sparse-bestand is. Een sparse bestand is een speciaal type bestand dat NTFS biedt voor efficiënt gebruik wanneer het bestand op de schijfstroom grotendeels leeg is. Azure File Sync sparse bestanden omdat een bestand volledig gelaagd of gedeeltelijk wordt ingeroepen. In een volledig gelaagd bestand wordt de bestandsstroom opgeslagen in de cloud. In een gedeeltelijk teruggeroepen bestand staat dat deel van het bestand al op schijf. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen zoals multimedia spelers of zip-hulpprogramma's. Als een bestand volledig wordt teruggeroepen naar schijf, Azure File Sync het van een sparse-bestand naar een gewoon bestand. Dit kenmerk is alleen ingesteld op Windows Server 2016 en ouder.|
        | M | Terughalen bij gegevenstoegang | Geeft aan dat de gegevens van het bestand niet volledig aanwezig zijn in de lokale opslag. Als u het bestand leest, wordt ten minste een deel van de bestandsinhoud opgehaald uit een Azure-bestands share waarmee het server-eindpunt is verbonden. Dit kenmerk is alleen ingesteld op Windows Server 2019. |
        | L | Reparsepunt | Geeft aan dat het bestand een reparsepunt heeft. Een reparsepunt is een speciale aanwijzer voor gebruik door een bestandssysteemfilter. Azure File Sync maakt gebruik van reparsepunten voor het definiëren van de Azure File Sync-bestandssysteemfilter (StorageSync.sys) de cloudlocatie waar het bestand wordt opgeslagen. Dit biedt ondersteuning voor naadloze toegang. Gebruikers hoeven niet te weten dat Azure File Sync wordt gebruikt of hoe ze toegang kunnen krijgen tot het bestand in uw Azure-bestands share. Wanneer een bestand volledig wordt ingeroepen, Azure File Sync het reparsepunt uit het bestand verwijderd. |
        | O | Offline | Geeft aan dat sommige of alle inhoud van het bestand niet is opgeslagen op schijf. Wanneer een bestand volledig wordt ingeroepen, Azure File Sync dit kenmerk verwijderd. |

        ![Het dialoogvenster Eigenschappen voor een bestand, met het tabblad Details geselecteerd](../files/media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > U kunt de kenmerken voor alle bestanden in  een map zien door het veld Kenmerken toe te voegen aan de tabelweergave van Verkenner. Klik hiervoor met de rechtermuisknop op een bestaande kolom (bijvoorbeeld **Grootte),** selecteer **Meer** en selecteer **vervolgens** Kenmerken in de vervolgkeuzelijst.
        
        > [!NOTE]
        > Al deze kenmerken zijn ook zichtbaar voor gedeeltelijk teruggeroepen bestanden.
        
   * **Gebruik `fsutil` om te controleren op reparsepunten op een bestand.**
       Zoals beschreven in de voorgaande optie, heeft een gelaagd bestand altijd een reparsepunt ingesteld. Met een reparsepunt kan het Azure File Sync-stuurprogramma voor bestandssysteemfilters (StorageSync.sys) inhoud ophalen uit Azure-bestands shares die niet lokaal op de server zijn opgeslagen. 

       Voer het hulpprogramma uit om te controleren of een bestand een reparsepunt heeft in een opdrachtprompt met verhoogde bevoegdheid of een `fsutil` PowerShell-venster:

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Als het bestand een reparsepunt heeft, kunt u verwachten dat **Reparse Tag Value: 0x8000001e**. Deze hexadecimale waarde is de reparsepuntwaarde die eigendom is van Azure File Sync. De uitvoer bevat ook de reparsegegevens die het pad naar uw bestand op uw Azure-bestands share vertegenwoordigen.
        
        > [!WARNING]  
        > De `fsutil reparsepoint` opdracht van het hulpprogramma heeft ook de mogelijkheid om een reparsepunt te verwijderen. Voer deze opdracht alleen uit als het technische Azure File Sync u om vraagt. Het uitvoeren van deze opdracht kan leiden tot gegevensverlies. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Toepassingen uitsluiten van het bijhouden van de laatste toegangstijd voor opslag in cloudlagen

Met Azure File Sync agentversie 11.1 kunt u toepassingen nu uitsluiten van laatste toegangstracking. Wanneer een toepassing toegang heeft tot een bestand, wordt de laatste toegangstijd voor het bestand bijgewerkt in de database voor cloudopslaglagen. Toepassingen die het bestandssysteem scannen, zoals een antivirusprogramma, zorgen ervoor dat alle bestanden dezelfde laatste toegangstijd hebben, wat van invloed is op het moment dat bestanden in lagen worden opgeslagen.

Als u toepassingen wilt uitsluiten van het bijhouden van de laatste toegangstijd, voegt u de procesnaam toe aan de registerinstelling HeatTrackingProcessNameExclusionList, die zich onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync.

Voorbeeld: reg ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync" /v HeatTrackingProcessNameExclusionList /t REG_MULTI_SZ /d "SampleApp.exe\0AnotherApp.exe" /f

> [!NOTE]
> Processen voor gegevensverdubbeling en Resource Manager (FSRM) worden standaard uitgesloten. Wijzigingen in de uitsluitingslijst voor processen worden elke vijf minuten door het systeem gehonoreerd.

## <a name="how-to-access-the-heat-store"></a>Toegang krijgen tot de heatstore

Cloudopslaglagen gebruiken de laatste toegangstijd en de toegangsfrequentie van een bestand om te bepalen welke bestanden in een laag moeten worden opgeslagen. Het filtertrail voor cloudlagen (storagesync.sys) houdt de laatste toegangstijd bij en registreert de informatie in de heatstore voor opslag in cloudlagen. U kunt de heatstore ophalen en opslaan in een CSV-bestand met behulp van een server-lokale PowerShell-cmdlet.

Er is één heatstore voor alle bestanden op hetzelfde volume. De warmteopslag kan erg groot worden. Als u alleen het 'coolste' aantal items hoeft op te halen, gebruikt u -Limit en een getal en kunt u ook filteren op een subpad versus de hoofdmap van het volume.

- Importeer de PowerShell-module:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- VRIJE VOLUMERUIMTE: om de volgorde op te halen waarin bestanden in lagen worden opgeslagen met behulp van het beleid voor vrije ruimte op het volume:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATUMBELEID: om de volgorde op te halen waarin bestanden in lagen worden opgeslagen met behulp van het datumbeleid:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- Zoek de heatstore-informatie voor een bepaald bestand:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Bekijk alle bestanden in aflopende volgorde op de laatste toegangstijd:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Bekijk de volgorde waarin gelaagde bestanden worden ingeroepen door middel van terughalen op de achtergrond of terughalen op aanvraag via PowerShell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Een bestand of map geforceereerd in lagen opslaan

> [!NOTE]
> Wanneer u een map selecteert die in een laag moet worden opgeslagen, worden alleen de bestanden in de map gelaagd. Bestanden die na die tijd zijn gemaakt, worden niet automatisch gelaagd.

Wanneer de functie voor cloudopslaglagen is ingeschakeld, worden bestanden in cloudlagen automatisch gelaagd op basis van de laatste toegang en wijzigingstijden om het volumepercentage vrije ruimte te bereiken dat is opgegeven op het cloud-eindpunt. Soms wilt u echter handmatig een bestand in lagen forcen. Dit kan handig zijn als u een groot bestand opgeslagen dat u niet van plan bent om opnieuw te gebruiken voor een lange tijd en u wilt de vrije ruimte op uw volume nu gebruiken voor andere bestanden en mappen. U kunt lagen afdwingen met behulp van de volgende PowerShell-opdrachten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Een gelaagd bestand terugroepen naar schijf

De eenvoudigste manier om een bestand terug te roepen naar de schijf is door het bestand te openen. Het Azure File Sync bestandssysteemfilter (StorageSync.sys) downloadt het bestand probleemloos van uw Azure-bestands share zonder werk van uw kant. Voor bestandstypen die gedeeltelijk kunnen worden gelezen of gestreamd, zoals multimedia- of ZIP-bestanden, zorgt het openen van een bestand er niet voor dat het hele bestand wordt gedownload.

Om ervoor te zorgen dat een bestand volledig wordt gedownload naar de lokale schijf, moet u PowerShell gebruiken om af te dwingen dat een bestand volledig wordt ingeroepen. Deze optie kan ook nuttig zijn als u meerdere bestanden tegelijk wilt inroepen, zoals alle bestanden in een map. Open een PowerShell-sessie naar het server-knooppunt Azure File Sync is geïnstalleerd en voer de volgende PowerShell-opdrachten uit:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Optionele parameters:
* `-Order CloudTieringPolicy` roept eerst de meest recent gewijzigde of gebruikt bestanden terug en wordt toegestaan door het huidige beleid voor opslaglagen. 
    * Als het beleid voor vrije ruimte op het volume is geconfigureerd, worden bestanden teruggeroepen totdat de beleidsinstelling voor vrije ruimte op het volume is bereikt. Als de beleidsinstelling voor volumevrij bijvoorbeeld 20% is, wordt de terugroepactie gestopt zodra de vrije ruimte van het volume 20% bereikt.  
    * Als het volume vrije ruimte en datumbeleid is geconfigureerd, bestanden worden teruggeroepen totdat de volume vrije ruimte of datum beleidsinstelling is bereikt. Als de beleidsinstelling voor het gratis volume bijvoorbeeld 20% is en het datumbeleid 7 dagen is, wordt de terugroepactie gestopt zodra de vrije ruimte van het volume 20% bereikt of als alle bestanden die binnen 7 dagen worden gebruikt of gewijzigd lokaal zijn.
* `-ThreadCount` bepaalt hoeveel bestanden parallel kunnen worden ingeroepen.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroeppoging wordt gedaan van een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen het opnieuw proberen terughalen van pogingen en moet altijd worden gebruikt in combinatie met de vorige parameter.

Voorbeeld:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Als het lokale volume dat als host voor de server wordt gehost onvoldoende vrije ruimte heeft om alle gelaagde gegevens terug te halen, mislukt de `Invoke-StorageSyncFileRecall` cmdlet.  

> [!Note]
> Als u bestanden wilt terughalen die zijn gelaagd, moet de netwerkbandbreedte ten minste 1 Mbps zijn. Als de netwerkbandbreedte minder dan 1 Mbps is, kunnen bestanden mogelijk niet worden ingeroepen met een time-outfout.

## <a name="next-steps"></a>Volgende stappen

* [Lees de veelgestelde vragen (FAQ) over Azure Files](../files/storage-files-faq.md?toc=%2fazure%2fstorage%2ffilesync%2ftoc.json)