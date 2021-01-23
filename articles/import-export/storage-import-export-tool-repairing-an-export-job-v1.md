---
title: Een export taak voor Azure import/export herstellen-v1 | Microsoft Docs
description: Meer informatie over het herstellen van een export taak die is gemaakt en uitgevoerd met de Azure import/export-service.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: d84f26b2764a103a9b504c1480e88b58fed3c201
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706468"
---
# <a name="repairing-an-export-job"></a>Een exporttaak herstellen
Nadat een export taak is voltooid, kunt u de Microsoft Azure Import/Export-hulp programma on-premises uitvoeren om:  
  
1.  Down load alle bestanden die niet kunnen worden geëxporteerd met de Azure import/export-service.  
  
2.  Controleer of de bestanden op de schijf correct zijn geëxporteerd.  
  
U moet verbinding hebben met Azure Storage om deze functionaliteit te kunnen gebruiken.  
  
De opdracht voor het herstellen van een import taak is **RepairExport**.

## <a name="repairexport-parameters"></a>RepairExport-para meters

De volgende para meters kunnen worden opgegeven met **RepairExport**:  
  
|Parameter|Beschrijving|  
|---------------|-----------------|  
|**/r: <RepairFile\>**|Vereist. Het pad naar het reparatie bestand, dat de voortgang van de reparatie bijhoudt en u kunt een onderbroken reparatie hervatten. Elk station moet één en slechts één herstel bestand hebben. Wanneer u een herstel start voor een bepaald station, geeft u het pad door naar een herstel bestand dat nog niet bestaat. Als u een onderbroken reparatie wilt hervatten, moet u de naam van een bestaand herstel bestand door geven. Geef altijd het herstel bestand op dat overeenkomt met het doel station.|  
|**/logdir: <LogDirectory\>**|Optioneel. De logboekmap. Uitgebreide logboek bestanden worden naar deze map geschreven. Als er geen Logboekmap is opgegeven, wordt de huidige map gebruikt als de logboekmap.|  
|**/d: <Target Directory\>**|Vereist. De map die moet worden gevalideerd en hersteld. Deze map is doorgaans de hoofdmap van het export station, maar kan ook een netwerk bestands share zijn die een kopie van de geëxporteerde bestanden bevat.|  
|**/BK: <BitLockerKey\>**|Optioneel. Geef de BitLocker-sleutel op als u wilt dat het hulp programma een versleutelde locatie ontgrendelt waar de geëxporteerde bestanden worden opgeslagen.|  
|**/SN: <StorageAccountName\>**|Vereist. De naam van het opslag account voor de export taak.|  
|**/SK: <StorageAccountKey\>**|**Vereist** als en alleen als er geen container-SAS is opgegeven. De account sleutel voor het opslag account voor de export taak.|  
|**/csas: <ContainerSas\>**|**Vereist** als en alleen als de sleutel voor het opslag account niet is opgegeven. De container-SAS voor toegang tot de blobs die aan de export taak zijn gekoppeld.|  
|**/CopyLogFile: <DriveCopyLogFile\>**|Vereist. Het pad naar het kopie logboek bestand van de schijf. Het bestand wordt gegenereerd door de service Windows Azure import/export en kan worden gedownload van de Blob-opslag die aan de taak is gekoppeld. Het kopie logboek bestand bevat informatie over de mislukte blobs of bestanden die moeten worden hersteld.|  
|**/ManifestFile: <DriveManifestFile\>**|Optioneel. Het pad naar het manifest bestand van het export station. Dit bestand wordt gegenereerd door de service Windows Azure import/export en opgeslagen op het export station. Optioneel, in een BLOB in het opslag account dat is gekoppeld aan de taak.<br /><br /> De inhoud van de bestanden op het export station wordt gecontroleerd met de MD5-hashes die in dit bestand zijn opgenomen. Beschadigde bestanden worden gedownload en naar de doel mappen genoteerd.|  
  
## <a name="using-repairexport-mode-to-correct-failed-exports"></a>De RepairExport-modus gebruiken om mislukte export bewerkingen te corrigeren  
U kunt het Azure-hulp programma voor importeren/exporteren gebruiken om bestanden te downloaden die niet kunnen worden geëxporteerd. Het kopie logboek bestand bevat een lijst met bestanden die niet kunnen worden geëxporteerd.  
  
De oorzaken van het exporteren van fouten zijn de volgende mogelijkheden:  
  
-   Beschadigde stations  
  
-   De sleutel van het opslag account is gewijzigd tijdens het overdrachts proces  
  
Als u het hulp programma wilt uitvoeren in de modus **RepairExport** , moet u eerst het station met de geëxporteerde bestanden op uw computer aansluiten. Voer vervolgens het Azure-hulp programma voor importeren/exporteren uit, waarbij u het pad naar dat station opgeeft met de `/d` para meter. U moet ook het pad opgeven naar het kopie logboek bestand dat u hebt gedownload. In het volgende voor beeld van de opdracht regel hieronder wordt het hulp programma uitgevoerd om bestanden te herstellen die niet konden worden geëxporteerd:  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log  
```  
  
Het volgende voor beeld is een kopie logboek bestand waarin wordt weer gegeven dat één blok in de BLOB niet kan worden geëxporteerd:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveLog>  
  <DriveId>9WM35C2V</DriveId>  
  <Blob Status="CompletedWithErrors">  
    <BlobPath>pictures/wild/desert.jpg</BlobPath>  
    <FilePath>\pictures\wild\desert.jpg</FilePath>  
    <LastModified>2012-09-18T23:47:08Z</LastModified>  
    <Length>163840</Length>  
    <BlockList>  
      <Block Offset="65536" Length="65536" Id="AQAAAA==" Status="Failed" />  
    </BlockList>  
  </Blob>  
  <Status>CompletedWithErrors</Status>  
</DriveLog>  
```  
  
Het kopie logboek bestand geeft aan dat er een fout is opgetreden tijdens het downloaden van een van de blokken van de BLOB naar het bestand op het export station door de service Windows Azure import/export. De andere onderdelen van het bestand zijn gedownload en de bestands lengte is correct ingesteld. In dit geval wordt het bestand op het station geopend, het blok van het opslag account gedownload en het bestand naar het bestands bereik geschreven vanaf offset 65536 met een lengte van 65536.  
  
## <a name="using-repairexport-to-validate-drive-contents"></a>RepairExport gebruiken om de inhoud van een station te valideren  
U kunt Azure import/export ook gebruiken met de **RepairExport** -optie om de inhoud van het station te valideren. Het manifest bestand op elk export station bevat MD5s voor de inhoud van het station.  
  
De Azure import/export-service kan tijdens het export proces ook de manifest bestanden opslaan in een opslag account. De locatie van de manifest bestanden is beschikbaar via de bewerking [taak ophalen](/rest/api/storageimportexport/jobs) wanneer de taak is voltooid. Zie [import/export service manifest File Format](/previous-versions/azure/storage/common/storage-import-export-file-format-metadata-and-properties)(Engelstalig) voor meer informatie over de indeling van een manifest bestand voor een station.  
  
In het volgende voor beeld ziet u hoe u het Azure import/export-hulp programma uitvoert met de para meters **/ManifestFile** en **/CopyLogFile** :  
  
```  
WAImportExport.exe RepairExport /r:C:\WAImportExport\9WM35C3U.rep /d:G:\ /sn:bobmediaaccount /sk:VkGbrUqBWLYJ6zg1m29VOTrxpBgdNOlp+kp0C9MEdx3GELxmBw4hK94f7KysbbeKLDksg7VoN1W/a5UuM2zNgQ== /CopyLogFile:C:\WAImportExport\9WM35C3U.log /ManifestFile:G:\9WM35C3U.manifest  
```  
  
In het volgende voor beeld ziet u een manifest bestand:  
  
```xml
<?xml version="1.0" encoding="utf-8"?>  
<DriveManifest Version="2011-10-01">  
  <Drive>  
    <DriveId>9WM35C3U</DriveId>  
    <ClientCreator>Windows Azure Import/Export service</ClientCreator>  
    <BlobList>
      <Blob>  
        <BlobPath>pictures/city/redmond.jpg</BlobPath>  
        <FilePath>\pictures\city\redmond.jpg</FilePath>  
        <Length>15360</Length>  
        <PageRangeList>  
          <PageRange Offset="0" Length="3584" Hash="72FC55ED9AFDD40A0C8D5C4193208416" />  
          <PageRange Offset="3584" Length="3584" Hash="68B28A561B73D1DA769D4C24AA427DB8" />  
          <PageRange Offset="7168" Length="512" Hash="F521DF2F50C46BC5F9EA9FB787A23EED" />  
        </PageRangeList>  
        <PropertiesPath Hash="E72A22EA959566066AD89E3B49020C0A">\pictures\city\redmond.jpg.properties</PropertiesPath>  
      </Blob>  
      <Blob>  
        <BlobPath>pictures/wild/canyon.jpg</BlobPath>  
        <FilePath>\pictures\wild\canyon.jpg</FilePath>  
        <Length>10884</Length>  
        <BlockList>  
          <Block Offset="0" Length="2721" Id="AAAAAA==" Hash="263DC9C4B99C2177769C5EBE04787037" />  
          <Block Offset="2721" Length="2721" Id="AQAAAA==" Hash="0C52BAE2CC20EFEC15CC1E3045517AA6" />  
          <Block Offset="5442" Length="2721" Id="AgAAAA==" Hash="73D1CB62CB426230C34C9F57B7148F10" />  
          <Block Offset="8163" Length="2721" Id="AwAAAA==" Hash="11210E665C5F8E7E4F136D053B243E6A" />  
        </BlockList>  
        <PropertiesPath Hash="81D7F81B2C29F10D6E123D386C3A4D5A">\pictures\wild\canyon.jpg.properties</PropertiesPath>  
      </Blob> 
    </BlobList>  
 </Drive>  
</DriveManifest>  
``` 
  
Na het volt ooien van het herstel proces wordt het hulp programma gelezen door elk bestand waarnaar wordt verwezen in het manifest bestand en de integriteit van het bestand te verifiëren met de MD5-hashes. Voor het bovenstaande manifest worden de volgende onderdelen door lopen.  

```  
G:\pictures\city\redmond.jpg, offset 0, length 3584  
  
G:\pictures\city\redmond.jpg, offset 3584, length 3584  
  
G:\pictures\city\redmond.jpg, offset 7168, length 3584  
  
G:\pictures\city\redmond.jpg.properties  
  
G:\pictures\wild\canyon.jpg, offset 0, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 2721, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 5442, length 2721  
  
G:\pictures\wild\canyon.jpg, offset 8163, length 2721  
  
G:\pictures\wild\canyon.jpg.properties  
```

Elk onderdeel dat niet kan worden geverifieerd, wordt door het hulp programma gedownload en naar hetzelfde bestand op het station genoteerd.  
  
## <a name="next-steps"></a>Volgende stappen
 
* [Het Azure-hulp programma voor importeren/exporteren instellen](storage-import-export-tool-setup-v1.md)   
* [Harde schijven voorbereiden voor een importtaak](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)   
* [De taakstatus controleren met kopielogboekbestanden](storage-import-export-tool-reviewing-job-status-v1.md)   
* [Een importtaak herstellen](storage-import-export-tool-repairing-an-import-job-v1.md)