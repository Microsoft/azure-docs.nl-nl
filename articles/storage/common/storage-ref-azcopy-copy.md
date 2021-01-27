---
title: azcopy kopiëren | Microsoft Docs
description: In dit artikel vindt u Naslag informatie voor de azcopy-Kopieer opdracht.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 12/11/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: c4e85195ace0a24aa11d4a03b8f429f2714399b0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98879153"
---
# <a name="azcopy-copy"></a>azcopy copy

Kopieert de bron gegevens naar een doel locatie.

## <a name="synopsis"></a>Samen vatting

Kopieert de bron gegevens naar een doel locatie. De ondersteunde richtingen zijn:

  - lokale <-> Azure-Blob (SAS of OAuth-verificatie)
  - lokale <-> Azure Files (share/Directory SAS-verificatie)
  - lokale <-> Azure Data Lake Storage gen 2 (SAS-, OAuth-of Shared Key-verificatie)
  - Azure-Blob (SAS of openbaar)-> Azure-Blob (SAS of OAuth-verificatie)
  - Azure-Blob (SAS of openbaar)-> Azure Files (SAS)
  - Azure Files (SAS)-> Azure Files (SAS)
  - Azure Files (SAS)-> Azure-Blob (SAS of OAuth-verificatie)
  - Amazon Web Services (AWS) S3 (toegangs sleutel)-> Azure Block BLOB (SAS of OAuth-verificatie)

Zie de sectie voor beelden in dit artikel voor meer informatie.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)
- [Configureren, optimaliseren en problemen oplossen in AzCopy](storage-use-azcopy-configure.md)

## <a name="advanced"></a>Geavanceerd

AzCopy detecteert automatisch het inhouds type van de bestanden wanneer u deze uploadt van de lokale schijf. AzCopy detecteert het inhouds type op basis van de bestands extensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoek tabel is klein, maar op UNIX wordt deze uitgebreid met de bestanden van het lokale systeem `mime.types` als deze beschikbaar zijn onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen geëxtraheerd uit het REGI ster. Deze functie kan worden uitgeschakeld met behulp van een vlag. Zie de sectie markering in dit artikel voor meer informatie.

Als u een omgevings variabele instelt met behulp van de opdracht regel, wordt die variabele leesbaar in uw opdracht regel geschiedenis. U kunt variabelen verwijderen die referenties bevatten van uw opdracht regel geschiedenis. Als u wilt voor komen dat variabelen in uw geschiedenis worden weer gegeven, kunt u een script gebruiken om de gebruiker om referenties te vragen en de omgevings variabele in te stellen.

```
azcopy copy [source] [destination] [flags]
```

## <a name="examples"></a>Voorbeelden

Upload één bestand met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert `azcopy login` u de opdracht uit voordat u de volgende opdracht uitvoert.

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```
Hetzelfde als hierboven, maar deze keer ook de MD5-hash van de bestands inhoud en sla deze op als de eigenschap content-MD5 van de blob:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Een enkel bestand uploaden met behulp van een SAS-token:

```azcopy
azcopy cp "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Eén bestand uploaden met behulp van een SAS-token en pijpleiding (alleen blok-blobs):
  
```azcopy
cat "/path/to/file.txt" | azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]
```

Een volledige directory uploaden met behulp van een SAS-token:
  
```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

of

```azcopy
azcopy cp "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive --put-md5
```

Een set bestanden uploaden met behulp van een SAS-token en Joker tekens (*):
 
```azcopy
azcopy cp "/path/*foo/*bar/*.pdf" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]"
```

Bestanden en mappen uploaden met behulp van een SAS-token en Joker tekens (*):

```azcopy
azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```

Upload bestanden en mappen naar Azure Storage account en stel de tags voor de query teken reeks in op de blob. 

- Voor het instellen van Tags {Key = "verwijderen verwijderen", val = "foo"} en {Key = "verwijderen verwijderen 2", val = "Bar"}, gebruikt u de volgende syntaxis: `azcopy cp "/path/*foo/*bar*" "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
    
- Sleutels en waarden zijn URL-code ring en de sleutel-waardeparen worden gescheiden door een en-teken (' & ')

- Tijdens het instellen van tags op de blobs zijn er in SAS extra machtigingen (niet voor Tags), zonder dat de service een autorisatie fout meer krijgt.

Een enkel bestand downloaden met behulp van OAuth-verificatie. Als u zich nog niet hebt aangemeld bij AzCopy, voert `azcopy login` u de opdracht uit voordat u de volgende opdracht uitvoert.

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]" "/path/to/file.txt"
```

Een enkel bestand downloaden met behulp van een SAS-token:

```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "/path/to/file.txt"
```

Een enkel bestand downloaden met behulp van een SAS-token en vervolgens de uitvoer naar een bestand sluizen (alleen blok-blobs):
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" > "/path/to/file.txt"
``` 

Een volledige directory downloaden met behulp van een SAS-token:
 
```azcopy
azcopy cp "https://[account].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "/path/to/dir" --recursive
``` 

Een opmerking over het gebruik van een Joker teken (*) in Url's:

Er zijn slechts twee ondersteunde manieren om een Joker teken te gebruiken in een URL. 

- U kunt een van de volgende tekens gebruiken, net na de laatste slash (/) van een URL. Met dit Joker teken worden alle bestanden in een map rechtstreeks naar de bestemming gekopieerd zonder dat ze in een submap worden geplaatst. 

- U kunt ook een Joker teken in de naam van een container opgeven zolang de URL alleen verwijst naar een container en niet naar een blob. U kunt deze methode gebruiken om bestanden van een subset van containers te verkrijgen. 

De inhoud van een map downloaden zonder de bovenliggende map zelf te kopiëren.
 
```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/folder]/*?[SAS]" "/path/to/dir"
```

Down load een hele opslag account.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/" "/path/to/dir" --recursive
```

Down load een subset van containers in een opslag account met behulp van een Joker teken (*) in de container naam.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container*name]" "/path/to/dir" --recursive
```

Kopieer één BLOB naar een andere blob met behulp van een SAS-token.

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer één BLOB naar een andere blob met behulp van een SAS-token en een verificatie token. U moet een SAS-token aan het einde van de URL van het bron account gebruiken, maar het doel account heeft er geen nodig als u zich aanmeldt bij AzCopy met behulp van de `azcopy login` opdracht. 

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]"
```

Eén virtuele BLOB-map kopiëren naar een andere met behulp van een SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive=true
```

Kopieer alle BLOB-containers, directory's en blobs van het opslag account naar een andere met behulp van een SAS-token:

```azcopy
azcopy cp "https://[srcaccount].blob.core.windows.net?[SAS]" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer een enkel object naar Blob Storage van Amazon Web Services (AWS) S3 met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
  
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[object]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/blob]?[SAS]"
```

Kopieer een volledige directory naar Blob Storage van AWS S3 door gebruik te maken van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/[bucket]/[folder]" "https://[destaccount].blob.core.windows.net/[container]/[path/to/directory]?[SAS]" --recursive
```
    
  Raadpleeg https://docs.aws.amazon.com/AmazonS3/latest/user-guide/using-folders.html de tijdelijke aanduiding voor [map] voor meer informatie.

Kopieer alle buckets naar Blob Storage vanuit Amazon Web Services (AWS) met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
azcopy cp "https://s3.amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer alle buckets naar Blob Storage vanuit een Amazon Web Services (AWS) regio met behulp van een toegangs sleutel en een SAS-token. Stel eerst de omgevings variabele `AWS_ACCESS_KEY_ID` en `AWS_SECRET_ACCESS_KEY` voor de AWS S3-bron in.
 
```azcopy
- azcopy cp "https://s3-[region].amazonaws.com/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Kopieer een subset van buckets met behulp van een Joker teken (*) in de Bucket naam. Net als bij de vorige voor beelden hebt u een toegangs sleutel en een SAS-token nodig. Zorg ervoor dat u de omgevings variabele `AWS_ACCESS_KEY_ID` en de `AWS_SECRET_ACCESS_KEY` AWS S3-bron instelt.

```azcopy
- azcopy cp "https://s3.amazonaws.com/[bucket*name]/" "https://[destaccount].blob.core.windows.net?[SAS]" --recursive
```

Bestanden en mappen overzetten naar Azure Storage-account en de opgegeven query-teken reeks gecodeerde Tags op de BLOB instellen. 

- Voor het instellen van Tags {Key = "verwijderen verwijderen", val = "foo"} en {Key = "verwijderen verwijderen 2", val = "Bar"}, gebruikt u de volgende syntaxis: `azcopy cp "https://[account].blob.core.windows.net/[source_container]/[path/to/directory]?[SAS]" "https://[account].blob.core.windows.net/[destination_container]/[path/to/directory]?[SAS]" --blob-tags="bla%20bla=foo&bla%20bla%202=bar"`
        
- Sleutels en waarden zijn URL-code ring en de sleutel-waardeparen worden gescheiden door een en-teken (' & ')
    
- Tijdens het instellen van tags op de blobs zijn er in SAS extra machtigingen (niet voor Tags), zonder dat de service een autorisatie fout meer krijgt.

## <a name="options"></a>Opties

**--back-up** Hiermee activeert u de SeBackupPrivilege van Windows voor uploads of SeRestorePrivilege voor down loads, zodat AzCopy alle bestanden, ongeacht hun machtigingen voor het bestands systeem, kan zien en lezen, en alle machtigingen kunt herstellen. Vereist dat het account dat AzCopy gebruikt, al over deze machtigingen beschikt (bijvoorbeeld beheerders rechten heeft of lid is van de `Backup Operators` groep). Met deze vlag worden de bevoegdheden geactiveerd die het account al heeft.

**--BLOB-Tags** reeksen instellen labels op blobs om gegevens in uw opslag account te categoriseren.

**--BLOB-type** teken reeks definieert het type BLOB bij het doel. Dit wordt gebruikt voor het uploaden van blobs en bij het kopiëren tussen accounts (standaard `Detect` ). Geldige waarden zijn `Detect` :,, `BlockBlob` `PageBlob` en `AppendBlob` . Bij het kopiëren tussen accounts, veroorzaakt de waarde `Detect` AzCopy dat het type bron-BLOB wordt gebruikt om het type van de doel-BLOB te bepalen. Bij het uploaden van een bestand wordt `Detect` Hiermee bepaald of het bestand een VHD-of VHDX-bestand is op basis van de bestands extensie. Als het bestand eer een VHD-of VHDX-bestand is, wordt het bestand door AzCopy als een pagina-BLOB beschouwd. (standaard ' detecteren ')

**--blok-** teken reeks-BLOB-Block-BLOB uploaden naar Azure Storage met behulp van deze BLOB-laag. (standaard ' geen ')

**--blok-grootte-MB** float gebruik deze blok grootte (opgegeven in MIB) bij het uploaden naar Azure Storage en down loads van Azure Storage. De standaard waarde wordt automatisch berekend op basis van de bestands grootte. Decimale breuken zijn toegestaan (bijvoorbeeld: 0,25).

**--Cache-Control** string Stel de cache-Control-header in. Geretourneerd bij het downloaden.

**--Check-length** Controleer de lengte van een bestand op de bestemming na de overdracht. Als de bron en bestemming niet overeenkomen, wordt de overdracht als mislukt gemarkeerd. (standaard waarde is `true` )

**--Check-MD5** teken reeks geeft aan hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Alleen beschikbaar bij het downloaden. Beschik bare opties: `NoCheck` , `LogOnly` , `FailIfDifferent` , `FailIfDifferentOrMissing` . (standaard `FailIfDifferent` ) (standaard "FailIfDifferent")

**--** de teken reeks voor de positie van de inhoud is ingesteld op de content-disposition-header. Geretourneerd bij het downloaden.

**--** de teken reeks voor Content-Encoding: Stel de header content-encoding in. Geretourneerd bij het downloaden.

**--Content-Language-** teken reeks Stel de header content-language in. Geretourneerd bij het downloaden.

**--inhouds type** teken reeks Hiermee geeft u het type inhoud van het bestand op. Impliceert geen-schatting-MIME-type. Geretourneerd bij het downloaden.

**--decomprimeren** Bestanden automatisch uitpakken wanneer ze worden gedownload, als hun content-encoding aangeeft dat ze gecomprimeerd zijn. De ondersteunde waarden voor content-encoding zijn `gzip` en `deflate` . Bestands extensies van `.gz` / `.gzip` of `.zz` zijn niet nodig, maar worden verwijderd indien aanwezig.

**--exclude-kenmerken** teken reeks (alleen Windows) Hiermee worden bestanden uitgesloten waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--exclude-BLOB-type** teken reeks geeft optioneel het type BLOB ( `BlockBlob` /  `PageBlob` /  `AppendBlob` ) op dat moet worden uitgesloten bij het kopiëren van blobs uit de container of het account. Het gebruik van deze vlag is niet van toepassing op het kopiëren van gegevens van niet-Azure-service naar de service. Er moeten meerdere blobs worden gescheiden door `;` . 

**--exclude-** teken reeks van het pad deze paden uitsluiten bij het kopiëren. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad voor voegsel gecontroleerd (bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf` ). Bij gebruik in combi natie met account-traversal bevatten paden geen container naam.

**--exclude-patroon** teken reeks deze bestanden uitsluiten bij het kopiëren. Deze optie biedt ondersteuning voor joker tekens (*).

**--follow-symlinks**  Volg symbolische koppelingen bij het uploaden van het lokale bestands systeem.

**--Force-alleen-lezen** Wanneer u een bestaand bestand in Windows of Azure Files overschrijft, moet u ervoor zorgen dat de overschrijving werkt, zelfs als voor het bestaande bestand het kenmerk alleen-lezen is ingesteld.

**--van-tot** teken reeks optioneel de combi natie van bron doel opgeven. Bijvoorbeeld: `LocalBlob` , `BlobLocal` , `LocalBlobFS` .

**--Help**  Help bij kopiëren.

**--include-after-** teken reeks bevatten alleen de bestanden die zijn gewijzigd op of na de opgegeven datum/tijd. De waarde moet de indeling ISO8601 hebben. Als er geen tijd zone is opgegeven, wordt ervan uitgegaan dat de waarde zich in de lokale tijd zone bevindt van de machine waarop AzCopy wordt uitgevoerd. bijvoorbeeld `2020-08-19T15:04:00Z` voor een UTC-tijd of `2020-08-19` voor middernacht (00:00) in de lokale tijd zone. Net als bij AzCopy 10,5 is deze vlag alleen van toepassing op bestanden, niet op mappen, zodat de mapeigenschappen niet worden gekopieerd wanneer deze vlag met of wordt gebruikt `--preserve-smb-info` `--preserve-smb-permissions` .

 **--de teken reeks voor insluiting** bevat alleen de bestanden die zijn gewijzigd vóór of op de opgegeven datum/tijd. De waarde moet de indeling ISO8601 hebben. Als er geen tijd zone is opgegeven, wordt ervan uitgegaan dat de waarde zich in de lokale tijd zone bevindt van de machine waarop AzCopy wordt uitgevoerd. Bijvoorbeeld `2020-08-19T15:04:00Z` voor een UTC-tijd of `2020-08-19` voor middernacht (00:00) in de lokale tijd zone. Vanaf AzCopy 10,7 is deze vlag alleen van toepassing op bestanden, niet op mappen, zodat de mapeigenschappen niet worden gekopieerd wanneer deze vlag met `--preserve-smb-info` of wordt gebruikt `--preserve-smb-permissions` .

**--include-** kenmerk reeks (alleen Windows) bevat bestanden waarvan de kenmerken overeenkomen met de kenmerk lijst. Bijvoorbeeld: A; Z N

**--include-** teken reeks bevat alleen deze paden bij het kopiëren. Deze optie biedt geen ondersteuning voor joker tekens (*). Hiermee wordt het relatieve pad voor voegsel gecontroleerd (bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf` ).

**--include-patroon** teken reeks bevatten alleen deze bestanden bij het kopiëren. Deze optie biedt ondersteuning voor joker tekens (*). Scheid bestanden met behulp van een `;` .

**--lijst-van-versie** teken reeks geeft een bestand op waarin elke versie-id wordt weer gegeven op een afzonderlijke regel. Zorg ervoor dat de bron moet verwijzen naar een enkele Blob en dat alle versie-Id's die zijn opgegeven in het bestand met deze vlag, alleen bij de bron-BLOB horen. AzCopy worden de opgegeven versies gedownload in de opgegeven doelmap. Zie [eerdere versies van een BLOB downloaden](./storage-use-azcopy-v10.md#transfer-data)voor meer informatie.

**--** teken reeks op logboek niveau definiëren de logboek uitgebreidheids voor het logboek bestand, beschik bare niveaus: info (alle aanvragen/antwoorden), waarschuwing (trage antwoorden), fout (alleen mislukte aanvragen) en geen (geen uitvoer Logboeken). (standaard `INFO` ). 

**--** de teken reeks voor het uploaden van meta gegevens Azure Storage met deze sleutel-waardeparen als meta gegevens.

**--no-schatting-MIME-type**  Hiermee voor komt u dat AzCopy het inhouds type detecteert op basis van de extensie of inhoud van het bestand.

**--** de teken reeks overschrijven de conflicterende bestanden en blobs op de bestemming overschrijven als deze vlag is ingesteld op True. (standaard `true` ) Mogelijke waarden zijn `true` , `false` , en `prompt` `ifSourceNewer` . Voor doelen die mappen ondersteunen, worden conflicterende eigenschappen op mapniveau overschreven deze vlag is `true` of als er een positief antwoord aan de prompt wordt gegeven. (standaard ingesteld op ' True ')

**--pagina-BLOB-** teken reeks voor het uploaden van de pagina-blob naar Azure Storage met behulp van deze BLOB-laag. (standaard `None` ). (standaard ' geen ')

**--behoud-laatste-gewijzigd-tijd**  Alleen beschikbaar wanneer het doel bestands systeem is.

**--pres Erve-eigenaar**    Heeft alleen effect op Down loads en alleen wanneer `--preserve-smb-permissions` wordt gebruikt. Als True (de standaard instelling), worden de bestands eigenaar en-groep bewaard in Down loads. Als deze eigenschap is ingesteld op False, blijven `--preserve-smb-permissions` de acl's behouden, maar de eigenaar en groep worden gebaseerd op de gebruiker die AzCopy uitvoert (standaard waarde waar)

**--behoud-SMB-info**   Standaard onwaar. Hiermee behoudt u SMB-eigenschaps gegevens (laatste schrijf tijd, aanmaak tijd, kenmerk bits) tussen SMB-bewuste resources (Windows en Azure Files). Alleen de kenmerk-bits die door Azure Files worden ondersteund, worden overgedragen; alle andere worden genegeerd. Deze vlag is van toepassing op bestanden en mappen, tenzij een filter alleen-lezen is opgegeven (bijvoorbeeld het patroon include). De gegevens die voor mappen worden overgezet, zijn hetzelfde als voor bestanden, met uitzonde ring van de laatste schrijf tijd die nooit voor mappen is bewaard.

**--behoud-SMB-machtigingen**   Standaard onwaar. Hiermee blijven SMB-Acl's behouden tussen de bewuste resources (Windows en Azure Files). Voor down loads hebt u ook de `--backup` vlag nodig om machtigingen te herstellen, waarbij de nieuwe eigenaar niet de gebruiker is die AzCopy uitvoert. Deze vlag is van toepassing op bestanden en mappen, tenzij een filter voor alleen bestanden is opgegeven (bijvoorbeeld `include-pattern` ).

**--put-MD5**    Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap content-MD5 van de BLOB of het doel bestand. (Standaard wordt de hash niet gemaakt.) Alleen beschikbaar bij het uploaden.

**--recursief**    Zoek in submappen recursief bij het uploaden van het lokale bestands systeem.

**--S2S-detecteren-bron-gewijzigd**   Detecteren of het bron bestand of de BLOB wordt gewijzigd terwijl deze wordt gelezen. (Deze para meter geldt alleen voor service-naar-service-exemplaren, omdat de bijbehorende controle permanent is ingeschakeld voor uploads en down loads.)

**--S2S-handle-ongeldig-meta gegevens** teken reeks geeft aan hoe ongeldige sleutels voor meta gegevens worden verwerkt. Beschik bare opties: ExcludeIfInvalid, FailIfInvalid, RenameIfInvalid. (standaard `ExcludeIfInvalid` ). (standaard "ExcludeIfInvalid")

**--S2S-pres Erve-Access-tier**   De Access-laag behouden tijdens service to service copy. Raadpleeg [Azure Blob-opslag: dynamische, koude en archief toegangs lagen](../blobs/storage-blob-storage-tiers.md) om ervoor te zorgen dat het opslag account van de bestemming ondersteuning biedt voor het instellen van de toegangs laag. In het geval dat het instellen van de toegangs laag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier = False om het kopiëren van de toegangs lagen over te slaan. (standaard `true` ).  (standaard ingesteld op ' True ')

**--pres Erve-eigenschappen**   Volledige eigenschappen behouden tijdens service to service copy. Voor AWS S3 en Azure file-bron niet-enkelvoudige bestands bronnen retourneert de lijst bewerking geen volledige eigenschappen van objecten en bestanden. Voor het behouden van de volledige eigenschappen moet AzCopy één extra aanvraag per object of bestand verzenden. (standaard instelling waar)

## <a name="options-inherited-from-parent-commands"></a>Opties overgenomen van bovenliggende opdrachten

**--Cap-Mbps-float**   De overdrachts frequentie in megabits per seconde. Even door Voer kan enigszins afwijken van het kapje. Als deze optie is ingesteld op nul of wordt wegge laten, wordt de door Voer niet afgetopt.

**--** de teken reeks indeling van het uitvoer type van de uitvoer van de opdracht. De opties zijn onder andere: Text, JSON. De standaardwaarde is `text`. (standaard tekst)

**--vertrouwd-micro soft-achtervoegsels** teken reeks geeft aanvullende domein achtervoegsels aan waar Azure Active Directory aanmeldings tokens kunnen worden verzonden.  De standaardwaarde is `*.core.windows.net;*.core.chinacloudapi.cn;*.core.cloudapi.de;*.core.usgovcloudapi.net`. Alle hier vermelde waarden worden toegevoegd aan de standaard instelling. Voor beveiliging moet u Microsoft Azure domeinen hier alleen plaatsen. Scheid meerdere vermeldingen met een punt komma.

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
