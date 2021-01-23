---
title: Een import taak voor Azure import/export herstellen-v1 | Microsoft Docs
description: Meer informatie over het herstellen van een import taak die is gemaakt en uitgevoerd met de Azure import/export-service.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: af6234a81e8f79424cc3b1ec4178a175d9ff0146
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706457"
---
# <a name="repairing-an-import-job"></a>Een importtaak herstellen
De Microsoft Azure Import/Export-service kan een aantal van uw bestanden of delen van een bestand niet kopiëren naar de Windows Azure Blob-service. Enkele mogelijke oorzaken:  
  
-   Beschadigde bestanden  
  
-   Beschadigde stations  
  
-   De sleutel van het opslag account is gewijzigd terwijl het bestand werd overgedragen.  
  
U kunt het Microsoft Azure Import/Export-hulp programma uitvoeren met de Kopieer logboek bestanden van de import taak. Het hulp programma uploadt de ontbrekende bestanden, of delen van een bestand, naar uw Windows Azure-opslag account om de import taak te volt ooien.  
  
## <a name="repairimport-parameters"></a>RepairImport-para meters

De volgende para meters kunnen worden opgegeven met **RepairImport**: 
  
|||  
|-|-|  
|**/r:**<RepairFile\>|**Vereist.** Het pad naar het reparatie bestand, dat de voortgang van de reparatie bijhoudt en u kunt een onderbroken reparatie hervatten. Elk station moet één en slechts één herstel bestand hebben. Wanneer u een herstel start voor een bepaald station, geeft u het pad door aan een herstel bestand dat nog niet bestaat. Als u een onderbroken reparatie wilt hervatten, moet u de naam van een bestaand herstel bestand door geven. Geef altijd het herstel bestand op dat overeenkomt met het doel station.|  
|**/logdir:**<LogDirectory\>|**Optioneel.** De logboekmap. Uitgebreide logboek bestanden worden naar deze map geschreven. Als er geen Logboekmap is opgegeven, wordt de huidige map gebruikt als Logboekmap.|  
|**/d:**<TargetDirectories\>|**Vereist.** Een of meer met punt komma's gescheiden directory's die de oorspronkelijke bestanden bevatten die zijn geïmporteerd. Het import station kan ook worden gebruikt, maar is niet vereist als er alternatieve locaties van de oorspronkelijke bestanden beschikbaar zijn.|  
|**/BK:**<BitLockerKey\>|**Optioneel.** Geef de BitLocker-sleutel op als u wilt dat het hulp programma een versleuteld station ontgrendelt waar de oorspronkelijke bestanden beschikbaar zijn.|  
|**/sn:**<StorageAccountName\>|**Vereist.** De naam van het opslag account voor de import taak.|  
|**/SK:**<StorageAccountKey\>|**Vereist** als en alleen als er geen container-SAS is opgegeven. De account sleutel voor het opslag account voor de import taak.|  
|**/csas:**<ContainerSas\>|**Vereist** als en alleen als de sleutel voor het opslag account niet is opgegeven. De container-SAS voor toegang tot de blobs die zijn gekoppeld aan de import taak.|  
|**/CopyLogFile:**<DriveCopyLogFile\>|**Vereist.** Pad naar het kopie logboek bestand (uitgebreid logboek of fouten logboek). Het bestand wordt gegenereerd door de service Windows Azure import/export en kan worden gedownload van de Blob-opslag die aan de taak is gekoppeld. Het kopie logboek bestand bevat informatie over de mislukte blobs of bestanden, die moeten worden hersteld.|  
|**/PathMapFile:**<DrivePathMapFile\>|**Optioneel.** Pad naar een tekst bestand dat wordt gebruikt om ambiguïteiten op te lossen als u meerdere bestanden hebt met dezelfde naam die u in dezelfde taak hebt geïmporteerd. De eerste keer dat het hulp programma wordt uitgevoerd, kan dit bestand vullen met alle ambigue namen. Als u het hulp programma later uitvoert, gebruikt u dit bestand om de dubbel zinnigheid op te lossen.|  
  
## <a name="using-the-repairimport-command"></a>De opdracht RepairImport gebruiken  
Als u gegevens wilt herstellen door de gegevens over het netwerk te streamen, moet u de mappen opgeven die de oorspronkelijke bestanden bevatten die u hebt geïmporteerd met behulp van de `/d` para meter. Geef ook het kopie logboek bestand op dat u hebt gedownload uit uw opslag account. Een typische opdracht regel voor het herstellen van een import taak met gedeeltelijke fouten ziet er als volgt uit:  
  
```  
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log  
```  
  
In het volgende voor beeld van een kopie logboek bestand is 1 64-K-gedeelte van een bestand beschadigd op het station dat voor de import taak werd verzonden. Omdat deze fout de enige is die wordt aangegeven, zijn de rest van de blobs in de taak met succes geïmporteerd.  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
 <DriveId>9WM35C2V</DriveId>  
 <Blob Status="CompletedWithErrors">  
 <BlobPath>pictures/animals/koala.jpg</BlobPath>  
 <FilePath>\animals\koala.jpg</FilePath>  
 <Length>163840</Length>  
 <ImportDisposition Status="Overwritten">overwrite</ImportDisposition>  
 <PageRangeList>  
  <PageRange Offset="65536" Length="65536" Hash="AA2585F6F6FD01C4AD4256E018240CD4" Status="Corrupted" />  
 </PageRangeList>  
 </Blob>  
 <Status>CompletedWithErrors</Status>  
</DriveLog>  
```
  
Wanneer dit Kopieer logboek wordt door gegeven aan het Azure-hulp programma voor importeren/exporteren, probeert het hulp programma de import bewerking voor dit bestand te volt ooien door de ontbrekende inhoud over het netwerk te kopiëren. Na het bovenstaande voor beeld zoekt het hulp programma naar het oorspronkelijke bestand `\animals\koala.jpg` binnen de twee directory's `C:\Users\bob\Pictures` en `X:\BobBackup\photos` . Als het bestand `C:\Users\bob\Pictures\animals\koala.jpg` bestaat, kopieert het Azure import/export-hulp programma het ontbrekende gegevens bereik naar de bijbehorende BLOB `http://bobmediaaccount.blob.core.windows.net/pictures/animals/koala.jpg` .  
  
## <a name="resolving-conflicts-when-using-repairimport"></a>Conflicten oplossen bij het gebruik van RepairImport  
In sommige gevallen is het mogelijk dat het hulp programma het vereiste bestand niet kan vinden of openen om een van de volgende redenen: het bestand is niet gevonden, het bestand is niet toegankelijk, de bestands naam is onduidelijk of de inhoud van het bestand is niet meer correct.  
  
Een onduidelijke fout kan optreden als het hulp programma probeert te vinden `\animals\koala.jpg` en er een bestand is met die naam onder zowel `C:\Users\bob\pictures` en `X:\BobBackup\photos` . Dat wil zeggen, beide `C:\Users\bob\pictures\animals\koala.jpg` en `X:\BobBackup\photos\animals\koala.jpg` bestaan op de stations voor het importeren van taken.  
  
`/PathMapFile`Met deze optie kunt u deze fouten oplossen. U kunt de naam opgeven van het bestand dat de lijst met bestanden bevat die het hulp programma niet juist kan identificeren. Het volgende opdracht regel voorbeeld vult `9WM35C2V_pathmap.txt` :  
  
```
WAImportExport.exe RepairImport /r:C:\WAImportExport\9WM35C2V.rep /d:C:\Users\bob\Pictures;X:\BobBackup\photos /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C2V.log /PathMapFile:C:\WAImportExport\9WM35C2V_pathmap.txt  
```
  
Het hulp programma schrijft vervolgens de problematische bestands paden naar `9WM35C2V_pathmap.txt` , één op elke regel. Het bestand kan bijvoorbeeld de volgende vermeldingen bevatten nadat de opdracht is uitgevoerd:  
 
```
\animals\koala.jpg  
\animals\kangaroo.jpg  
```
  
 Voor elk bestand in de lijst moet u proberen het bestand te zoeken en te openen om ervoor te zorgen dat het beschikbaar is voor het hulp programma. Als u het hulp programma expliciet wilt laten weten waar een bestand moet worden gevonden, wijzigt u het pad naar de map en voegt u het pad toe aan elk bestand op dezelfde regel, gescheiden door een tabteken:  
  
```
\animals\koala.jpg           C:\Users\bob\Pictures\animals\koala.jpg  
\animals\kangaroo.jpg        X:\BobBackup\photos\animals\kangaroo.jpg  
```
  
Nadat u de benodigde bestanden hebt gemaakt voor het hulp programma of het pad naar de map hebt bijgewerkt, kunt u het hulp programma opnieuw uitvoeren om het import proces te volt ooien.  
  
## <a name="next-steps"></a>Volgende stappen
 
* [Het Azure-hulp programma voor importeren/exporteren instellen](storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [De taakstatus controleren met kopielogboekbestanden](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een exporttaak herstellen](./storage-import-export-tool-repairing-an-export-job-v1.md)