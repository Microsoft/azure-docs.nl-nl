---
title: Problemen met het uploaden van gegevens oplossen met behulp van Logboeken
titleSuffix: Azure Data Box Disk
description: Hierin wordt beschreven hoe u de logboeken gebruikt en problemen oplost die worden weer gegeven bij het uploaden van gegevens naar Azure Data Box Disk.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: troubleshooting
ms.date: 06/17/2019
ms.author: alkohli
ms.openlocfilehash: 4b53cf607bdf60c785c7324d9ede526a0983b7e6
ms.sourcegitcommit: e15c0bc8c63ab3b696e9e32999ef0abc694c7c41
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97605272"
---
# <a name="understand-logs-to-troubleshoot-data-upload-issues-in-azure-data-box-disk"></a>Logboeken voor het oplossen van problemen bij het uploaden van gegevens in Azure Data Box Disk

Dit artikel is van toepassing op Microsoft Azure Data Box Disk en beschrijft de problemen die worden weer gegeven wanneer u gegevens uploadt naar Azure.

## <a name="about-upload-logs"></a>Over het uploaden van Logboeken

Wanneer de gegevens worden geüpload naar Azure in het Data Center `_error.xml` en `_verbose.xml` er bestanden worden gegenereerd voor elk opslag account. Deze logboeken worden geüpload naar hetzelfde opslag account dat is gebruikt voor het uploaden van gegevens. 

Beide logboeken hebben dezelfde indeling en bevatten XML-beschrijvingen van de gebeurtenissen die hebben plaatsgevonden tijdens het kopiëren van de gegevens van de schijf naar het Azure Storage-account.

Het uitgebreide logboek bevat volledige informatie over de status van de Kopieer bewerking voor elke BLOB of elk bestand, terwijl het fouten logboek alleen de informatie bevat voor blobs of bestanden die fouten tijdens het uploaden hebben aangetroffen.

Het fouten logboek heeft dezelfde structuur als het uitgebreide logboek, maar Hiermee worden geslaagde bewerkingen gefilterd.

## <a name="download-logs"></a>Logboeken downloaden

Voer de volgende stappen uit om de upload logboeken te vinden.

1. Als er fouten zijn bij het uploaden van de gegevens naar Azure, wordt in de portal een pad naar de map weer gegeven waarin de diagnostische logboeken zich bevinden.

    ![Koppeling naar Logboeken in de portal](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs.png)

2. Ga naar **waies**.

    ![fout en uitgebreide logboeken](./media/data-box-disk-troubleshoot-upload/data-box-disk-portal-logs-1.png)

In elk geval ziet u de fout logboeken en de uitgebreide Logboeken. Selecteer elk logboek en down load een lokale kopie.

## <a name="sample-upload-logs"></a>Voor beeld van upload logboeken

Hieronder ziet u een voor beeld van de `_verbose.xml` . In dit geval is de volg orde zonder fouten voltooid.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Blob Status="Completed">
    <BlobPath>$root/botetapageblob.vhd</BlobPath>
    <FilePath>\PageBlob\botetapageblob.vhd</FilePath>
    <Length>1073742336</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <PageRangeList>
      <PageRange Offset="0" Length="4194304" Status="Completed" />
      <PageRange Offset="4194304" Length="4194304" Status="Completed" />
      <PageRange Offset="8388608" Length="4194304" Status="Completed" />
      --------CUT-------------------------------------------------------
      <PageRange Offset="1061158912" Length="4194304" Status="Completed" />
      <PageRange Offset="1065353216" Length="4194304" Status="Completed" />
      <PageRange Offset="1069547520" Length="4194304" Status="Completed" />
      <PageRange Offset="1073741824" Length="512" Status="Completed" />
    </PageRangeList>
  </Blob>
  <Blob Status="Completed">
    <BlobPath>$root/botetablockblob.txt</BlobPath>
    <FilePath>\BlockBlob\botetablockblob.txt</FilePath>
    <Length>19</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <BlockList>
      <Block Offset="0" Length="19" Status="Completed" />
    </BlockList>
  </Blob>
  <File Status="Completed">
    <FileStoragePath>botetaazurefilesfolder/botetaazurefiles.txt</FileStoragePath>
    <FilePath>\AzureFile\botetaazurefilesfolder\botetaazurefiles.txt</FilePath>
    <Length>20</Length>
    <ImportDisposition Status="Created">rename</ImportDisposition>
    <FileRangeList>
      <FileRange Offset="0" Length="20" Status="Completed" />
    </FileRangeList>
  </File>
  <Status>Completed</Status>
</DriveLog>
```

Voor dezelfde bestelling wordt een voor beeld van de `_error.xml` hieronder weer gegeven.

```xml

<?xml version="1.0" encoding="utf-8"?>
<DriveLog Version="2018-10-01">
  <DriveId>184020D95632</DriveId>
  <Summary>
    <ValidationErrors>
      <None Count="3" />
    </ValidationErrors>
    <CopyErrors>
      <None Count="3" Description="No errors encountered" />
    </CopyErrors>
  </Summary>
  <Status>Completed</Status>
</DriveLog>
```

Hieronder ziet u een voor beeld van de `_error.xml` volg orde waarin fouten zijn voltooid. 

Het fout bestand in dit geval bevat een `Summary` sectie en een andere sectie die alle fouten op bestands niveau bevat. 

De `Summary` bevat de `ValidationErrors` en `CopyErrors` . In dit geval zijn er 8 bestanden of mappen geüpload naar Azure en zijn er geen validatie fouten. Wanneer de gegevens zijn gekopieerd naar Azure Storage account, zijn vijf bestanden of mappen geüpload. De naam van de resterende drie bestanden of mappen is gewijzigd volgens de naamgevings conventies van Azure container en vervolgens met succes geüpload naar Azure.

De bestands niveau status bevat `BlobStatus` een beschrijving van alle acties die zijn uitgevoerd voor het uploaden van de blobs. In dit geval worden drie containers gewijzigd, omdat de mappen waarnaar de gegevens zijn gekopieerd niet voldoen aan de Azure-naamgevings conventies voor containers. Voor de blobs die in deze containers worden geüpload, zijn de nieuwe container naam, het pad van de BLOB in azure, het oorspronkelijke ongeldige bestandspad en de grootte van de BLOB opgenomen.
  
```xml
 <?xml version="1.0" encoding="utf-8"?>
  <DriveLog Version="2018-10-01">
    <DriveId>18041C582D7E</DriveId>
    <Summary>
     <!--Summary for validation and data copy to Azure -->
      <ValidationErrors>
        <None Count="8" />
      </ValidationErrors>
      <CopyErrors>
        <Completed Count="5" Description="No errors encountered" />
        <ContainerRenamed Count="3" Description="Renamed the container as the original container name does not follow Azure conventions." />
      </CopyErrors>
    </Summary>
    <!--List of renamed containers with the new names, new file path in Azure, original invalid file path, and size -->
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/private.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\private.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/resource.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\resource.vhd</OriginalFilePath>
      <SizeInBytes>71528448</SizeInBytes>
    </Blob>
    <Blob Status="ContainerRenamed">
      <BlobPath>databox-c2073fd1cc379d83e03d6b7acce23a6cf29d1eef/role.vhd</BlobPath>
      <OriginalFilePath>\PageBlob\pageblob test\role.vhd</OriginalFilePath>
      <SizeInBytes>10490880</SizeInBytes>
    </Blob>
    <Status>CompletedWithErrors</Status>
  </DriveLog>
```

## <a name="data-upload-errors"></a>Fouten bij uploaden van gegevens

De fouten die worden gegenereerd bij het uploaden van de gegevens naar Azure, worden in de volgende tabel samenvatten.

| Foutcode | Beschrijving                   |
|-------------|------------------------------|
|`None` |  Is voltooid.           |
|`Renamed` | De naam van de blob is gewijzigd.   |
|`CompletedWithErrors` | Het uploaden is voltooid met fouten. De details van de bestanden die fouten bevatten, zijn opgenomen in het logboek bestand.  |
|`Corrupted`|CRC berekend tijdens de opname van gegevens komt niet overeen met de CRC die tijdens het uploaden is berekend.  |  
|`StorageRequestFailed` | De Azure Storage-aanvraag is mislukt.   |     
|`LeasePresent` | Dit item wordt geleasd en wordt gebruikt door een andere gebruiker. |
|`StorageRequestForbidden` |Kan niet uploaden vanwege verificatie problemen. |
|`ManagedDiskCreationTerminalFailure` | Kan niet uploaden als managed disks. De bestanden zijn beschikbaar in het opslag account voor staging als pagina-blobs. U kunt pagina-blobs hand matig converteren naar Managed disks.  |
|`DiskConversionNotStartedTierInfoMissing` | Omdat het VHD-bestand buiten de mappen van de voorgemaakte laag is gekopieerd, is er geen beheerde schijf gemaakt. Het bestand wordt geüpload als pagina-BLOB naar het staging Storage-account zoals opgegeven tijdens het maken van de order. U kunt deze hand matig converteren naar een beheerde schijf.|
|`InvalidWorkitem` | Kan de gegevens niet uploaden omdat deze niet voldoet aan de conventies voor Azure-naamgeving en-limieten.|
|`InvalidPageBlobUploadAsBlockBlob` | Geüpload als blok-blobs in een container met voor voegsel `databoxdisk-invalid-pb-` .|
|`InvalidAzureFileUploadAsBlockBlob` | Geüpload als blok-blobs in een container met prefix `databoxdisk-invalid-af` -.|
|`InvalidManagedDiskUploadAsBlockBlob` | Geüpload als blok-blobs in een container met prefix `databoxdisk-invalid-md` -.|
|`InvalidManagedDiskUploadAsPageBlob` |Geüpload als pagina-blobs in een container met voor voegsel `databoxdisk-invalid-md-` . |
|`MovedToOverflowShare` |Geüploade bestanden naar een nieuwe share omdat de oorspronkelijke share grootte de maximale grootte voor Azure-limiet overschrijdt. De naam van de nieuwe bestands share bevat de oorspronkelijke naam `-2` .   |
|`MovedToDefaultAzureShare` |Geüploade bestanden die geen deel uitmaken van een map naar een standaard share. De share naam begint met `databox-` . |
|`ContainerRenamed` |De container voor deze bestanden voldoet niet aan Azure-naamgevings conventies en de naam ervan wordt gewijzigd. De nieuwe naam begint met `databox-` en is een achtervoegsel met de SHA1-hash van de oorspronkelijke naam |
|`ShareRenamed` |De share voor deze bestanden voldoet niet aan de naamgevings conventies van Azure en de naam ervan wordt gewijzigd. De nieuwe naam begint met `databox-` en wordt voorafgegaan door de SHA1-hash van de oorspronkelijke naam. |
|`BlobRenamed` |Deze bestanden voldoen niet aan Azure-naamgevings regels en hebben een andere naam gekregen. Controleer het `BlobPath` veld voor de nieuwe naam. |
|`FileRenamed` |Deze bestanden voldoen niet aan Azure-naamgevings regels en hebben een andere naam gekregen. Controleer het `FileStoragePath` veld voor de nieuwe naam. |
|`DiskRenamed` |Deze bestanden voldoen niet aan Azure-naamgevings regels en hebben een andere naam gekregen. Controleer het `BlobPath` veld voor de nieuwe naam. |


## <a name="next-steps"></a>Volgende stappen

- [Open een ondersteunings ticket voor data Box Disk problemen](data-box-disk-contact-microsoft-support.md).
