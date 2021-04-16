---
title: azcopy copy| Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy copy.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 03/08/2021
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 7c1e265f473c1c6fb70fd97416722e7b863c429b
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503555"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopieert brongegevens naar een doellocatie.

## <a name="synopsis"></a>Synopsis

Kopieert brongegevens naar een doellocatie. De ondersteunde aanwijzingen zijn:

  - lokale <-> Azure Blob (SAS- of OAuth-verificatie)
  - lokale <-> Azure Files (SAS-verificatie voor share/directory)
  - lokale <-> Azure Data Lake Storage Gen 2 (SAS-, OAuth- of gedeelde sleutelverificatie)
  - Azure Blob (SAS of openbaar) - > Azure Blob (SAS- of OAuth-verificatie)
  - Azure Blob (SAS of openbaar) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Files (SAS)
  - Azure Files (SAS) -> Azure Blob (SAS- of OAuth-verificatie)
  - Amazon Web Services (AWS) S3 (toegangssleutel) - > Azure Block Blob (SAS- of OAuth-verificatie)
  - Google Cloud Storage (serviceaccountsleutel) -> Azure Block Blob (SAS- of OAuth-verificatie) [preview]

Zie de sectie voorbeelden van dit artikel voor meer informatie.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Zelfstudie: On-premises gegevens naar de cloudopslag migreren met AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

## <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhoudstype van de bestanden wanneer u ze uploadt vanaf de lokale schijf. AzCopy detecteert het inhoudstype op basis van de bestandsextensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoektabel is klein, maar in Unix wordt deze uitgebreid met de bestand(en) van het lokale systeem als deze beschikbaar zijn onder een of meer van deze `mime.types` namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen uit het register geëxtraheerd. Deze functie kan worden uitgeschakeld met behulp van een vlag. Zie het vlaggedeelte van dit artikel voor meer informatie.

Als u een omgevingsvariabele in stelt met behulp van de opdrachtregel, kan die variabele worden gelezen in de opdrachtregelgeschiedenis. U kunt variabelen wissen die referenties uit de opdrachtregelgeschiedenis bevatten. Als u wilt voorkomen dat variabelen in uw geschiedenis worden weergegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevingsvariabele in te stellen.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Voorbeelden

Upload één bestand met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, moet u de opdracht `azcopy login` uitvoeren voordat u de volgende opdracht gaat uitvoeren.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Hetzelfde als hierboven, maar deze keer berekent u ook de MD5-hash van de bestandsinhoud en sla deze op als de eigenschap Content-MD5 van de blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Upload één bestand met behulp van een SAS-token:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Upload één bestand met behulp van een SAS-token en pijpleiding (alleen blok-blobs):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Upload een volledige map met behulp van een SAS-token:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

of

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Upload een set bestanden met behulp van een SAS-token en jokertekens (*) :
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Bestanden en mappen uploaden met behulp van een SAS-token en jokertekens (*) :

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Upload bestanden en mappen naar Azure Storage account en stel de met queryreeks gecodeerde tags in op de blob. 

- Gebruik de volgende syntaxis om tags {key = "bla bla", val = "foo"} en {key = "bla bla 2", val = "bar"} in te stellen: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Sleutels en waarden zijn URL-gecodeerd en de sleutel-waardeparen worden gescheiden door een en-teken ('&')

- Tijdens het instellen van tags op de blobs, zijn er aanvullende machtigingen (niet voor tags) in SAS zonder welke de service een autorisatiefout terug zal geven.

Download één bestand met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, moet u de opdracht `azcopy login` uitvoeren voordat u de volgende opdracht uit te voeren.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Download één bestand met behulp van een SAS-token:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Download één bestand met behulp van een SAS-token en doorspijp de uitvoer vervolgens naar een bestand (alleen blok-blobs):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Download een volledige map met behulp van een SAS-token:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Een opmerking over het gebruik van een jokerteken (*) in URL's:

Er zijn slechts twee ondersteunde manieren om een jokerteken in een URL te gebruiken. 

- U kunt er een gebruiken net na de laatste slash (/) van een URL. Dit gebruik van het jokerteken kopieert alle bestanden in een map rechtstreeks naar de bestemming zonder ze in een submap te plaatsen. 

- U kunt ook een jokerteken gebruiken in de naam van een container, zolang de URL alleen naar een container verwijst en niet naar een blob. U kunt deze methode gebruiken om bestanden op te halen uit een subset containers. 

Download de inhoud van een map zonder de map zelf te kopiëren.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Download een volledig opslagaccount.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Download een subset containers binnen een opslagaccount met behulp van een jokerteken (*) in de containernaam.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopieer één blob naar een andere blob met behulp van een SAS-token.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer één blob naar een andere blob met behulp van een SAS-token en een Auth-token. U moet een SAS-token aan het einde van de URL van het bronaccount gebruiken, maar het doelaccount heeft er geen nodig als u zich aanmeldt bij AzCopy met behulp van de `azcopy login` opdracht . 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Kopieer de ene virtuele blobmap naar een andere met behulp van een SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopieer alle blobcontainers, -directories en -blobs van het opslagaccount naar een ander met behulp van een SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer één object naar Blob Storage van Amazon Web Services (AWS) S3 met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer een volledige map naar Blob Storage AWS S3 met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Raadpleeg voor meer informatie over de tijdelijke aanduiding https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html [map].

Kopieer alle buckets naar Blob Storage van Amazon Web Services (AWS) met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer alle buckets naar Blob Storage van een Amazon Web Services (AWS)-regio met behulp van een toegangssleutel en een SAS-token. Stel eerst de omgevingsvariabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer een subset van buckets met behulp van een jokerteken (*) in de bucketnaam. Net als in de vorige voorbeelden hebt u een toegangssleutel en een SAS-token nodig. Zorg ervoor dat u de omgevingsvariabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in stelt.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Transfer files and directories to Azure Storage account and set the given query-string encoded tags on the blob. 

- Gebruik de volgende syntaxis om tags {key = "bla bla", val = "foo"} en {key = "bla bla 2", val = "bar"} in te stellen: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Sleutels en waarden zijn url-gecodeerd en de sleutel-waardeparen worden gescheiden door een en-teken ('&')
    
- Tijdens het instellen van tags voor de blobs, zijn er aanvullende machtigingen (niet voor tags) in SAS zonder welke de service een autorisatiefout terug zal geven.

Kopieer één object naar Blob Storage van Google Cloud Storage met behulp van een serviceaccountsleutel en een SAS-token. Stel eerst de omgevingsvariabele in GOOGLE_APPLICATION_CREDENTIALS Google Cloud Storage-bron.
  
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer een volledige map naar Blob Storage van Google Cloud Storage met behulp van een serviceaccountsleutel en een SAS-token. Stel eerst de omgevingsvariabele in GOOGLE_APPLICATION_CREDENTIALS google Cloud Storage-bron.
 
```azcopy
  - azcopy cp "https://storage.cloud.google.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopieer een volledige bucket naar Blob Storage van Google Cloud Storage met behulp van een serviceaccountsleutel en een SAS-token. Stel eerst de omgevingsvariabele in GOOGLE_APPLICATION_CREDENTIALS google Cloud Storage-bron.

```azcopy 
azcopy cp "https://storage.cloud.google.com/[bucket]" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopieer alle buckets naar Blob Storage van Google Cloud Storage met behulp van een serviceaccountsleutel en een SAS-token. Stel eerst de omgevingsvariabelen GOOGLE_APPLICATION_CREDENTIALS en GOOGLE_CLOUD_PROJECT =<project-id> voor GCS-bron

```azcopy
  - azcopy cp "https://storage.cloud.google.com/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

Kopieer een subset van buckets met behulp van een jokerteken (*) in de bucketnaam uit Google Cloud Storage met behulp van een serviceaccountsleutel en een SAS-token voor de bestemming. Stel eerst de omgevingsvariabelen GOOGLE_APPLICATION_CREDENTIALS en GOOGLE_CLOUD_PROJECT =<project-id> voor de Google Cloud Storage-bron.
 
```azcopy
azcopy cp "https://storage.cloud.google.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net/?[SAS]" --recursive=true
```

## <a name="options"></a>Opties

**--backup** Activeert SeBackupPrivilege van Windows voor uploads, of SeRestorePrivilege voor downloads, zodat AzCopy alle bestanden kan zien en lezen, ongeacht de machtigingen van het bestandssysteem, en om alle machtigingen te herstellen. Vereist dat het account met AzCopy al over deze machtigingen beschikt (bijvoorbeeld beheerdersrechten heeft of lid is van de `Backup Operators` groep). Met deze vlag worden de bevoegdheden geactiveerd die het account al heeft.

**--blob-tags tekenreeks** tags instellen op blobs om gegevens in uw opslagaccount te categoriseren.

**--blob-type** string Definieert het type blob op de bestemming. Dit wordt gebruikt voor het uploaden van blobs en bij het kopiëren tussen accounts (standaard `Detect` ). Geldige waarden zijn `Detect` , `BlockBlob` , en `PageBlob` `AppendBlob` . Bij het kopiëren tussen accounts zorgt een waarde van ervoor dat AzCopy het type bron-blob gebruikt om het `Detect` type van de doel-blob te bepalen. Bij het uploaden van een bestand bepaalt `Detect` of het bestand een VHD- of een VHDX-bestand is op basis van de bestandsextensie. Als het bestand een VHD- of VHDX-bestand is, behandelt AzCopy het bestand als een pagina-blob. (standaard 'Detecteren')

**--blok-blob-tier tekenreeks** Blok-blob uploaden naar Azure Storage deze blob-laag. (standaard 'Geen')

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB) bij het uploaden naar Azure Storage en het downloaden van Azure Storage. De standaardwaarde wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--cache-control string** Set the cache-control header. Geretourneerd bij downloaden.

**--check-length** Controleer de lengte van een bestand op de bestemming na de overdracht. Als de bron en het doel niet overeenkomen, wordt de overdracht gemarkeerd als mislukt. (de standaardwaarde is `true` )

**--check-md5-tekenreeks** Hiermee geeft u op hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Alleen beschikbaar bij het downloaden. Beschikbare opties: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . `FailIfDifferent`(standaard) (standaard 'FailIfDifferent')

**--content-disposition** string Set the content-disposition header. Geretourneerd bij downloaden.

**--content-encoding** string Set the content-encoding header. Geretourneerd bij downloaden.

**--content-language string** Set the content-language header. Geretourneerd bij downloaden.

**--content-type** string Hiermee geeft u het inhoudstype van het bestand op. Impliceert no-guess-mime-type. Geretourneerd bij downloaden.

**--decompress** Bestanden automatisch decomprimeren bij het downloaden, als de inhoudscoderen aangeeft dat ze zijn gecomprimeerd. De ondersteunde waarden voor inhoudscoderen zijn `gzip` en `deflate` . Bestandsextensies `.gz` / `.gzip` van of zijn `.zz` niet nodig, maar worden verwijderd indien aanwezig.

**--exclude-attributes** string (alleen Windows) sluit bestanden waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: A; S; R

**--exclude-blob-type** string Geeft desgewenst het type blob ( ) op dat moet worden uitgesloten bij het kopiëren van blobs uit de container of `BlockBlob` /  `PageBlob` /  `AppendBlob` het account. Het gebruik van deze vlag is niet van toepassing op het kopiëren van gegevens van een niet-Azure-service naar de service. Meer dan één blob moet worden gescheiden door `;` . 

**--exclude-path-tekenreeks** Sluit deze paden uit bij het kopiëren. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee wordt het voorvoegsel van het relatieve pad gecontroleerd (bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf` ). Wanneer paden worden gebruikt in combinatie met account traversal, bevatten paden niet de containernaam.

**--exclude-pattern tekenreeks** sluit deze bestanden bij het kopiëren. Deze optie ondersteunt jokertekens (*).

**--follow-symlinks**  Volg symbolische koppelingen bij het uploaden vanuit het lokale bestandssysteem.

**--force-if-read-only** Wanneer u een bestaand bestand in Windows of Azure Files overschrijft, moet u de overschrijving ook laten werken als voor het bestaande bestand een kenmerk is ingesteld dat alleen-lezen is.

**--van naar tekenreeks** Hiermee geeft u desgewenst de combinatie van bronbestemming op. Bijvoorbeeld: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--help**  help voor kopiëren.

**--include-after-tekenreeks** Alleen de bestanden opnemen die zijn gewijzigd op of na de opgegeven datum/tijd. De waarde moet de ISO8601-indeling hebben. Als er geen tijdzone is opgegeven, wordt ervan uitgegaan dat de waarde zich in de lokale tijdzone van de computer met AzCopy. bijvoorbeeld voor `2020-08-19T15:04:00Z` een UTC-tijd of `2020-08-19` voor middernacht (00:00) in de lokale tijdzone. Net als bij AzCopy 10.5 geldt deze vlag alleen voor bestanden, niet voor mappen. Mapeigenschappen worden dus niet gekopieerd wanneer u deze vlag gebruikt met `--preserve-smb-info` of `--preserve-smb-permissions` .

 **--include-before tekenreeks** Alleen de bestanden opnemen die zijn gewijzigd vóór of op de opgegeven datum/tijd. De waarde moet de ISO8601-indeling hebben. Als er geen tijdzone is opgegeven, wordt ervan uitgegaan dat de waarde zich in de lokale tijdzone van de computer met AzCopy. Bijvoorbeeld `2020-08-19T15:04:00Z` voor een UTC-tijd of `2020-08-19` voor middernacht (00:00) in de lokale tijdzone. Vanaf AzCopy 10.7 is deze vlag alleen van toepassing op bestanden, niet op mappen. Mapeigenschappen worden dus niet gekopieerd wanneer u deze vlag gebruikt met `--preserve-smb-info` of `--preserve-smb-permissions` .

**--include-attributes** string (alleen Windows) bevat bestanden waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: A; S; R

**--include-path string** Include only these paths when copying. Deze optie biedt geen ondersteuning voor jokertekens (*). Controleert het relatieve pad voorvoegsel (bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-pattern** string Include only these files when copying. Deze optie ondersteunt jokertekens (*). Scheid bestanden met behulp van een `;` .

**--list-of-versions-tekenreeks** Hiermee geeft u een bestand op waarin elke versie-id op een afzonderlijke regel wordt vermeld. Zorg ervoor dat de bron moet wijzen op één blob en dat alle versie-ID's die in het bestand zijn opgegeven met deze vlag alleen tot de bron-blob behoren. AzCopy downloadt de opgegeven versies in de opgegeven doelmap. Zie Vorige versies van [een blob downloaden voor meer informatie.](./storage-use-azcopy-v10.md#transfer-data)

**--log-level** string Define the log verbosity for the log file, available levels: INFO(all requests/responses), WARNING (slow responses), ERROR (only failed requests) and NONE (no output logs). `INFO`(standaard). 

**--metadata** string Upload to Azure Storage with these key-value pairs as metadata.

**--no-guess-mime-type**  Hiermee voorkomt u dat AzCopy het inhoudstype detecteert op basis van de extensie of inhoud van het bestand.

**--overschrijf tekenreeks** overschrijf de conflicterende bestanden en blobs op de bestemming als deze vlag is ingesteld op true. `true`(standaard) Mogelijke waarden `true` zijn , , en `false` `prompt` `ifSourceNewer` . Voor bestemmingen die mappen ondersteunen, worden conflicterende eigenschappen op mapniveau overschreven. Deze vlag wordt overschreven of als er een positief antwoord `true` wordt gegeven op de prompt. (standaard 'true')

**--page-blob-tier string** Upload page blob to Azure Storage using this blob tier (Pagina-blob-laag met behulp van deze bloblaag). `None`(standaard). (standaard 'Geen')

**--preserve-last-modified-time**  Alleen beschikbaar wanneer het doel bestandssysteem is.

**--preserve-owner**    Heeft alleen een effect op downloads en alleen wanneer `--preserve-smb-permissions` wordt gebruikt. Indien waar (de standaardinstelling), blijven de bestandseigenaar en groep behouden in downloads. Als deze is ingesteld op onwaar, blijven ACL's behouden, maar worden Eigenaar en Groep gebaseerd op de gebruiker `--preserve-smb-permissions` die AzCopy gebruikt (standaard true)

**--preserve-smb-info**   Standaard onwaar. Behoudt SMB-eigenschapsgegevens (laatste schrijftijd, aanmaaktijd, kenmerkbits) tussen SMB-aware resources (Windows en Azure Files). Alleen de kenmerkbits die door Azure Files worden overgedragen; alle andere worden genegeerd. Deze vlag is van toepassing op zowel bestanden als mappen, tenzij er een alleen-bestandsfilter is opgegeven (bijvoorbeeld include-pattern). De informatie die voor mappen wordt overgedragen, is hetzelfde als die voor bestanden, met uitzondering van De laatste schrijftijd die nooit behouden blijft voor mappen.

**--preserve-smb-permissions**   Standaard onwaar. Behoudt SMB ACL's tussen welbewuste resources (Windows en Azure Files). Voor downloads hebt u ook de vlag nodig om machtigingen te herstellen waarbij de nieuwe eigenaar niet de gebruiker is die `--backup` AzCopy gebruikt. Deze vlag is van toepassing op zowel bestanden als mappen, tenzij er een alleen-bestandsfilter is opgegeven (bijvoorbeeld `include-pattern` ).

**--put-md5**    Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doel-blob of het doelbestand. (De hash wordt standaard NIET gemaakt.) Alleen beschikbaar tijdens het uploaden.

**--recursief**    Bekijk subdirectorieën recursief bij het uploaden vanuit het lokale bestandssysteem.

**--s2s-detect-source-changed**   Detecteer of het bronbestand/de bronblob verandert terwijl het wordt gelezen. (Deze parameter is alleen van toepassing op exemplaren van de service naar de service, omdat de bijbehorende controle permanent is ingeschakeld voor uploads en downloads.)

**--s2s-handle-invalid-metadata** string Hiermee geeft u op hoe ongeldige metagegevenssleutels worden verwerkt. Beschikbare opties: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. `ExcludeIfInvalid`(standaard). (standaard 'ExcludeIfInvalid')

**--s2s-preserve-access-tier**   Behoudt de toegangslaag tijdens het kopiëren van de service naar de service. Raadpleeg [Azure Blob-opslag: hot-, cool- en archieftoegangslagen](../blobs/storage-blob-storage-tiers.md) om ervoor te zorgen dat het doelopslagaccount ondersteuning biedt voor het instellen van de toegangslaag. In het geval dat het instellen van de toegangslaag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier=false om het kopiëren van de toegangslaag over te slaat. `true`(standaard).  (standaard 'true')

**--s2s-preserve-properties**   Behoudt volledige eigenschappen tijdens het kopiëren van de service naar de service. Voor AWS S3 en Azure File niet-één bestandsbron retourneren de lijstbewerking geen volledige eigenschappen van objecten en bestanden. Om volledige eigenschappen te behouden, moet AzCopy één extra aanvraag per object of bestand verzenden. (standaard true)

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

**--cap-mbps float**   Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.

**--output-type** string Format of the command's output. De keuzes zijn onder andere: text, json. De standaardwaarde is `text`. (standaard 'tekst')

**--trusted-microsoft-suffixes** string Hiermee geeft u extra domeinachtervoegsels op Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor beveiliging moet u hier alleen Microsoft Azure zetten. Scheid meerdere vermeldingen met punt-dubbele punts.

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
