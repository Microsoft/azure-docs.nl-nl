---
title: Azure File Sync gelaagde bestanden beheren | Microsoft Docs
description: Tips en Power shell Commandlets om gelaagde bestanden te beheren
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 1/4/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: dc61792da669cd5d2c928eec0fd412d86725fc8c
ms.sourcegitcommit: dda0d51d3d0e34d07faf231033d744ca4f2bbf4a
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102204355"
---
# <a name="how-to-manage-tiered-files"></a>Gelaagde bestanden beheren

Dit artikel bevat richt lijnen voor gebruikers die vragen hebben over het beheren van gelaagde bestanden. Zie [Azure files Veelgestelde vragen](storage-files-faq.md)voor conceptuele vragen over Cloud lagen.

## <a name="how-to-check-if-your-files-are-being-tiered"></a>Controleren of uw bestanden worden getierd

Hiermee wordt één keer per uur geëvalueerd of bestanden moeten worden getierd op basis van de ingestelde beleids regels. U kunt in twee situaties komen wanneer een nieuw server eindpunt wordt gemaakt:

Wanneer u een nieuw eind punt voor de server toevoegt, bestaan er vaak bestanden op die server locatie. Ze moeten worden geüpload voordat cloud lagen kunnen worden gestart. Het volume beschik bare ruimte beleid begint niet met zijn werk totdat de initiële upload van alle bestanden is voltooid. Het optionele datum beleid begint echter met het uitvoeren van een afzonderlijk bestand, zodra een bestand is geüpload. Het interval van één uur is hier ook van toepassing. 

Wanneer u een nieuw server eindpunt toevoegt, is het mogelijk dat u een lege server locatie hebt verbonden met een Azure-bestands share met uw gegevens. Als u ervoor kiest om de naam ruimte te downloaden en inhoud op te halen tijdens de eerste down load naar uw server, worden de bestanden na de naam ruimte op basis van de laatst gewijzigd tijds tempel ingetrokken, waarna het volume beschik bare ruimte beleid en de optionele datum beleids limieten zijn bereikt.

Er zijn verschillende manieren om te controleren of een bestand naar uw Azure-bestands share is getierd:
    
   *  **Controleer de bestands kenmerken van het bestand.**
     Klik met de rechter muisknop op een bestand, ga naar **Details** en schuif omlaag naar de eigenschap **Attributes** . Voor een gelaagd bestand zijn de volgende kenmerken ingesteld:     
        
        | Kenmerk letter | Kenmerk | Definitie |
        |:----------------:|-----------|------------|
        | A | Archiveren | Geeft aan dat er een back-up moet worden gemaakt van het bestand met back-upsoftware. Dit kenmerk is altijd ingesteld, ongeacht of het bestand op de schijf is gelaagd of volledig wordt opgeslagen. |
        | P | Sparse-bestand | Geeft aan dat het bestand een sparse-bestand is. Een sparse-bestand is een speciaal type bestand dat NTFS biedt voor efficiënt gebruik wanneer het bestand op de schijf stroom grotendeels leeg is. Azure File Sync maakt gebruik van verspreide bestanden omdat een bestand volledig is gelaagd of gedeeltelijk wordt ingetrokken. In een volledig gelaagd bestand wordt de bestands stroom opgeslagen in de Cloud. Het deel van het bestand bevindt zich al op schijf in een gedeeltelijk ingetrokken bestand. Dit kan gebeuren wanneer bestanden gedeeltelijk worden gelezen door toepassingen als multimedia spelers of zip-hulpprogram ma's. Als een bestand volledig wordt ingetrokken, wordt Azure File Sync geconverteerd van een sparse-bestand naar een gewoon bestand. Dit kenmerk wordt alleen ingesteld op Windows Server 2016 en ouder.|
        | M | Gegevens toegang intrekken | Geeft aan dat de gegevens van het bestand niet volledig aanwezig zijn in de lokale opslag. Als u het bestand leest, wordt er ten minste een deel van de bestands inhoud opgehaald van een Azure-bestands share waarop het server-eind punt is aangesloten. Dit kenmerk wordt alleen ingesteld op Windows Server 2019. |
        | L | Reparsepunt | Geeft aan dat het bestand een reparsepunt heeft. Een reparsepunt is een speciale verwijzing voor gebruik door een bestandssysteem filter. Azure File Sync maakt gebruik van reparsepunten om te definiëren of het Azure File Sync bestandssysteem filter (StorageSync.sys) de locatie van de Cloud waar het bestand is opgeslagen. Dit biedt ondersteuning voor naadloze toegang. Gebruikers hoeven niet te weten dat Azure File Sync wordt gebruikt of hoe ze toegang krijgen tot het bestand in de Azure-bestands share. Wanneer een bestand volledig wordt ingetrokken, verwijdert Azure File Sync het reparsepunt uit het bestand. |
        | O | Offline | Geeft aan dat sommige of alle inhoud van het bestand niet op de schijf worden opgeslagen. Wanneer een bestand volledig is ingetrokken, Azure File Sync dit kenmerk verwijdert. |

        ![Het dialoog venster Eigenschappen voor een bestand, met het tabblad Details geselecteerd](media/storage-files-faq/azure-file-sync-file-attributes.png)
        
    
        > [!NOTE]
        > U kunt de kenmerken van alle bestanden in een map weer geven door het veld **kenmerken** toe te voegen aan de tabel weergave van bestanden Verkenner. Als u dit wilt doen, klikt u met de rechter muisknop op een bestaande kolom (bijvoorbeeld **grootte**), selecteert u **meer** en selecteert u vervolgens **kenmerken** in de vervolg keuzelijst.
        
        > [!NOTE]
        > Al deze kenmerken worden ook weer gegeven voor gedeeltelijk ingetrokken bestanden.
        
   * **Gebruiken `fsutil` om te controleren op reparsepunten voor een bestand.**
       Zoals beschreven in de voor gaande optie, bevat een gelaagd bestand altijd een reparsepunt set. Met een reparsepunt kan het Azure File Sync stuur programma voor het bestandssysteem filter (StorageSync.sys) inhoud ophalen van Azure-bestands shares die niet lokaal op de server zijn opgeslagen. 

       Voer het hulp programma uit om te controleren of een bestand een reparsepunt heeft, in een opdracht prompt met verhoogde bevoegdheid of een Power shell-venster `fsutil` :

        ```powershell
        fsutil reparsepoint query <your-file-name>
        ```
       Als het bestand een reparsepunt heeft, kunt u de waarde van het **reparse-label zien: 0x8000001e**. Deze hexadecimale waarde is de reparsepunt waarde die het eigendom is van Azure File Sync. De uitvoer bevat ook de reparsegegevens die het pad naar het bestand op uw Azure-bestands share vertegenwoordigt.
        
        > [!WARNING]  
        > De `fsutil reparsepoint` opdracht voor het hulp programma heeft ook de mogelijkheid om een reparsepunt te verwijderen. Voer deze opdracht niet uit, tenzij het Azure File Sync technisch team u vraagt. Als u deze opdracht uitvoert, kan dit leiden tot gegevens verlies. 

## <a name="how-to-exclude-applications-from-cloud-tiering-last-access-time-tracking"></a>Toepassingen uitsluiten van het bijhouden van de laatste keer toegang tot Cloud lagen

Met Azure File Sync agent versie 11,1 kunt u nu toepassingen uitsluiten van het bijhouden van laatste toegang. Wanneer een toepassing toegang heeft tot een bestand, wordt de tijd van laatste toegang voor het bestand bijgewerkt in de Cloud-Tiering-data base. Toepassingen die het bestands systeem scannen als anti virus, zorgen ervoor dat alle bestanden dezelfde laatste toegangs tijd hebben, wat van invloed is op de lagen van de bestanden.

Als u toepassingen wilt uitsluiten van het bijhouden van de tijd van laatste toegang, voegt u de proces naam toe aan de register instelling HeatTrackingProcessNameExclusionList die zich onder HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync bevindt.

Voor beeld: REG ADD "HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Azure\StorageSync"/v HeatTrackingProcessNameExclusionList/t REG_MULTI_SZ/d "SampleApp.exe\0AnotherApp.exe"/f

> [!NOTE]
> Gegevensontdubbeling en bestands Server bron beheer (FSRM)-processen worden standaard uitgesloten. Wijzigingen in de lijst met uitsluitingen van processen worden elke vijf minuten door het systeem gehonoreerd.

## <a name="how-to-access-the-heat-store"></a>Toegang tot de hitte Store

Cloud lagen maakt gebruik van de tijd van laatste toegang en de toegangs frequentie van een bestand om te bepalen welke bestanden moeten worden gelaagd. Het filter stuur programma voor Cloud lagen (storagesync.sys) traceert de tijd van laatste toegang en registreert de informatie in de opslag van de Cloud-laag. U kunt de warmte opslag ophalen en opslaan in een CSV-bestand met behulp van een server-Local Power shell-cmdlet.

Er is één hitte opslag voor alle bestanden op hetzelfde volume. Het warmte archief kan erg groot worden. Als u alleen het ' coolste ' aantal items, gebruiks limiet en een getal moet ophalen en ook kunt filteren op een subpad versus de hoofdmap van het volume.

- Importeer de Power shell-module:   `Import-Module '<SyncAgentInstallPath>\StorageSync.Management.ServerCmdlets.dll'`

- Beschik bare VOLUME ruimte: om de volg orde te bepalen waarin de bestanden worden getierd met behulp van het volume beschik bare ruimte beleid:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredBySpacePolicy`

- DATUM beleid: om de volg orde te bepalen waarin de bestanden worden getierd met het datum beleid:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName FilesToBeTieredByDatePolicy`

- De informatie over het hitte Archief voor een bepaald bestand zoeken:   `Get-StorageSyncHeatStoreInformation -FilePath '<PathToSpecificFile>'`

- Alle bestanden weer geven in aflopende volg orde op tijd van laatste toegang:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName DescendingLastAccessTime`

- Zie de volg orde waarin gelaagde bestanden worden ingetrokken door terughalen of on-demand intrekken via Power shell:   `Get-StorageSyncHeatStoreInformation -VolumePath '<DriveLetter>:\' -ReportDirectoryPath '<FolderPathToStoreResultCSV>' -IndexName OrderTieredFilesWillBeRecalled`

## <a name="how-to-force-a-file-or-directory-to-be-tiered"></a>Een gelaagd bestand of een directory afdwingen

> [!NOTE]
> Wanneer u een map selecteert die u wilt gelaagd, worden alleen de bestanden die zich momenteel in de directory bevinden gelaagd gelaagd. Bestanden die zijn gemaakt na die tijd, worden niet automatisch gelaagd.

Wanneer de functie voor Cloud lagen is ingeschakeld, worden bestanden in Cloud lagen automatisch gelaagd op basis van de laatste toegangs-en wijzigings tijden om het percentage beschik bare ruimte op het Cloud eindpunt te verkrijgen. Soms wilt u een bestand op laag hand matig geforceerd afdwingen. Dit kan handig zijn als u een groot bestand opslaat dat u niet langer wilt gebruiken gedurende een lange periode en u de beschik bare ruimte op uw volume nu wilt gebruiken voor andere bestanden en mappen. U kunt de lagen afdwingen met behulp van de volgende Power shell-opdrachten:

```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncCloudTiering -Path <file-or-directory-to-be-tiered>
```

## <a name="how-to-recall-a-tiered-file-to-disk"></a>Een gelaagd bestand terughalen naar schijf

De eenvoudigste manier om een bestand in te trekken op schijf is door het bestand te openen. Het Azure File Sync bestandssysteem filter (StorageSync.sys) downloadt het bestand naadloos van uw Azure-bestands share zonder uw eigen werk. Voor bestands typen die gedeeltelijk kunnen worden gelezen of gestreamd, zoals multi media-of zip-bestanden, hoeft u alleen maar een bestand te openen om het hele bestand te downloaden.

Om ervoor te zorgen dat een bestand volledig wordt gedownload naar een lokale schijf, moet u Power shell gebruiken om te forceren dat een bestand volledig wordt ingetrokken. Deze optie kan ook nuttig zijn als u meerdere bestanden tegelijk wilt intrekken, bijvoorbeeld alle bestanden in een map. Open een Power shell-sessie met het server knooppunt waar Azure File Sync is geïnstalleerd en voer vervolgens de volgende Power shell-opdrachten uit:
    
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint>
```
Optionele para meters:
* `-Order CloudTieringPolicy` roept eerst de meest recent gewijzigde of geopende bestanden aan en is toegestaan door het huidige laag beleid. 
    * Als het volume beschik bare ruimte beleid is geconfigureerd, worden de bestanden ingetrokken totdat de beleids instelling volume beschik bare ruimte is bereikt. Als de waarde voor het gratis volume beleid bijvoorbeeld 20% is, wordt intrekken gestopt zodra de beschik bare ruimte van het volume 20% bedraagt.  
    * Als volume beschik bare ruimte en datum beleid is geconfigureerd, worden de bestanden ingetrokken totdat de instelling volume beschik bare ruimte of datum beleid is bereikt. Als de instelling voor het volume gratis beleid bijvoorbeeld 20% is en het datum beleid 7 dagen is, wordt intrekken gestopt zodra de beschik bare ruimte van het volume 20% bedraagt of alle bestanden die worden geopend of gewijzigd binnen 7 dagen, lokaal zijn.
* `-ThreadCount` Hiermee wordt bepaald hoeveel bestanden parallel kunnen worden ingetrokken.
* `-PerFileRetryCount`bepaalt hoe vaak een terugroep bewerking wordt uitgevoerd voor een bestand dat momenteel is geblokkeerd.
* `-PerFileRetryDelaySeconds`bepaalt de tijd in seconden tussen nieuwe pogingen en moet altijd worden gebruikt in combi natie met de vorige para meter.

Voorbeeld:
```powershell
Import-Module "C:\Program Files\Azure\StorageSyncAgent\StorageSync.Management.ServerCmdlets.dll"
Invoke-StorageSyncFileRecall -Path <path-to-to-your-server-endpoint> -ThreadCount 8 -Order CloudTieringPolicy -PerFileRetryCount 3 -PerFileRetryDelaySeconds 10
``` 

> [!Note]  
>- Als het lokale volume dat als host fungeert voor de server onvoldoende beschik bare ruimte heeft om alle gelaagde gegevens in te trekken, `Invoke-StorageSyncFileRecall` mislukt de cmdlet.  

> [!Note]
> Als u bestanden wilt terughalen die zijn getierd, moet de netwerk bandbreedte ten minste 1 Mbps zijn. Als de netwerk bandbreedte kleiner is dan 1 Mbps, kunnen bestanden niet worden ingetrokken vanwege een time-outfout.

## <a name="next-steps"></a>Volgende stappen
* [Veelgestelde vragen over Azure Files](storage-files-faq.md)
