---
title: azcopy sync | Microsoft Docs
description: Dit artikel bevat naslaginformatie voor de opdracht azcopy sync.
author: normesta
ms.service: storage
ms.topic: reference
ms.date: 07/24/2020
ms.author: normesta
ms.subservice: common
ms.reviewer: zezha-msft
ms.openlocfilehash: 83d10a7a6e9eb14379d32cc88800a2c443feac60
ms.sourcegitcommit: 3b5cb7fb84a427aee5b15fb96b89ec213a6536c2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107503045"
---
# <a name="azcopy-sync"></a>azcopy sync

Repliceert de bronlocatie naar de doellocatie. Dit artikel bevat een gedetailleerde naslag voor de opdracht azcopy sync. Zie Synchroniseren met Azure Blob Storage met behulp van [AzCopy v10](storage-use-azcopy-blobs-synchronize.md)voor meer informatie over het synchroniseren van blobs tussen bron- en doellocaties. Zie Azure Files voor meer [Azure Files.](storage-use-azcopy-files.md#synchronize-files)

## <a name="synopsis"></a>Synopsis

De laatst gewijzigde tijden worden gebruikt voor vergelijking. Het bestand wordt overgeslagen als de laatste wijzigingstijd in de bestemming recenter is.

De ondersteunde paren zijn:

- lokale <-> Azure Blob (SAS- of OAuth-verificatie kan worden gebruikt)
- Azure Blob <-> Azure Blob (bron moet een SAS bevatten of openbaar toegankelijk is; SAS- of OAuth-verificatie kan worden gebruikt voor de bestemming)
- Azure File <-> Azure File (bron moet een SAS bevatten of is openbaar toegankelijk; SAS-verificatie moet worden gebruikt voor de bestemming)

De synchronisatieopdracht verschilt op verschillende manieren van de kopieeropdracht:

1. De recursieve vlag is standaard true en synchronisatie kopieert alle subdirectorieën. Met Synchroniseren worden alleen de bestanden op het hoogste niveau in een map gekopieerd als de recursieve vlag onwaar is.
2. Wanneer u synchroniseert tussen virtuele directories, voegt u een slash toe aan het pad (zie voorbeelden) als er een blob is met dezelfde naam als een van de virtuele directories.
3. Als de vlag is ingesteld op true of prompt, worden bestanden en blobs op de bestemming verwijderd die `deleteDestination` niet aanwezig zijn bij de bron.

## <a name="related-conceptual-articles"></a>Gerelateerde conceptuele artikelen

- [Aan de slag met AzCopy](storage-use-azcopy-v10.md)
- [Zelfstudie: On-premises gegevens naar de cloudopslag migreren met AzCopy](storage-use-azcopy-migrate-on-premises-data.md)
- [Gegevens overdragen met AzCopy en Blob Storage](./storage-use-azcopy-v10.md#transfer-data)
- [Gegevens overdragen met AzCopy en bestandsopslag](storage-use-azcopy-files.md)

### <a name="advanced"></a>Geavanceerd

Als u geen bestandsextensie opgeeft, detecteert AzCopy automatisch het inhoudstype van de bestanden bij het uploaden vanaf de lokale schijf, op basis van de bestandsextensie of inhoud (als er geen extensie is opgegeven).

De ingebouwde opzoektabel is klein, maar in Unix wordt deze uitgebreid met de mime.types-bestanden van het lokale systeem, indien beschikbaar onder een of meer van deze namen:

- /etc/mime.types
- /etc/apache2/mime.types
- /etc/apache/mime.types

In Windows worden MIME-typen uit het register geëxtraheerd.

```azcopy
azcopy sync <source> <destination> [flags]
```

## <a name="examples"></a>Voorbeelden

Eén bestand synchroniseren:

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Hetzelfde als hierboven, maar bereken ook een MD5-hash van de bestandsinhoud en sla die MD5-hash vervolgens op als de eigenschap Content-MD5 van de blob. 

```azcopy
azcopy sync "/path/to/file.txt" "https://[account].blob.core.windows.net/[container]/[path/to/blob]" --put-md5
```

Synchroniseer een volledige map met inbegrip van de subdirectory's (houd er rekening mee dat recursief standaard is ingeschakeld):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]"
```

of

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --put-md5
```

Synchroniseer alleen de bestanden in een map, maar niet de subdirectory's of de bestanden in subdirectory's:

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=false
```

Synchroniseer een subset van bestanden in een map (bijvoorbeeld: alleen JPG- en PDF-bestanden, of als de bestandsnaam `exactName` is):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --include-pattern="*.jpg;*.pdf;exactName"
```

Synchroniseer een volledige map, maar sluit bepaalde bestanden uit van het bereik (bijvoorbeeld: elk bestand dat begint met foo of eindigt met balk):

```azcopy
azcopy sync "/path/to/dir" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --exclude-pattern="foo*;*bar"
```

Eén blob synchroniseren:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/blob]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/blob]"
```

Een virtuele map synchroniseren:

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]" --recursive=true
```

Synchroniseer een virtuele map met dezelfde naam als een blob (voeg een slash toe aan het pad om het ambiguïteit te ontzeggen):

```azcopy
azcopy sync "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/?[SAS]" "https://[account].blob.core.windows.net/[container]/[path/to/virtual/dir]/" --recursive=true
```

Een Azure-bestandsmap synchroniseren (dezelfde syntaxis als Blob):

```azcopy
azcopy sync "https://[account].file.core.windows.net/[share]/[path/to/dir]?[SAS]" "https://[account].file.core.windows.net/[share]/[path/to/dir]" --recursive=true
```

> [!NOTE]
> Als vlaggen voor opnemen/uitsluiten samen worden gebruikt, worden alleen bestanden die overeenkomen met de insluitpatronen bekeken, maar bestanden die overeenkomen met de uitgesloten patronen, worden altijd genegeerd.

## <a name="options"></a>Opties

**--block-size-mb** float Gebruik deze blokgrootte (opgegeven in MiB) bij het uploaden naar Azure Storage of downloaden van Azure Storage. De standaardwaarde wordt automatisch berekend op basis van de bestandsgrootte. Decimale breuken zijn toegestaan (bijvoorbeeld: `0.25` ).

**--check-md5-tekenreeks** Hiermee geeft u op hoe strikt MD5-hashes moeten worden gevalideerd bij het downloaden. Deze optie is alleen beschikbaar bij het downloaden. Beschikbare waarden zijn: `NoCheck` , `LogOnly` , , `FailIfDifferent` `FailIfDifferentOrMissing` . `FailIfDifferent`(standaard). `FailIfDifferent`(standaard)

**--delete-destination tekenreeks** definieert of extra bestanden verwijderen van de bestemming die niet aanwezig zijn bij de bron. Kan worden ingesteld op `true` `false` , of `prompt` . Als deze is ingesteld op , wordt de gebruiker een vraag gesteld voordat hij bestanden en `prompt` blobs voor verwijdering inplannen. `false`(standaard). `false`(standaard)

**--exclude-attributes** string (alleen Windows) sluit bestanden uit waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: `A;S;R`

**--exclude-path tekenreeks** sluit deze paden bij het vergelijken van de bron met de bestemming. Deze optie biedt geen ondersteuning voor jokertekens (*). Hiermee wordt het voorvoegsel van het relatieve pad gecontroleerd (bijvoorbeeld: `myFolder;myFolder/subDirName/file.pdf` ).

**--exclude-pattern string** Exclude files where the name matches the pattern list. Bijvoorbeeld: `*.jpg;*.pdf;exactName`

**--help**    voor synchronisatie.

**--include-attributes** string (alleen Windows) bevat alleen bestanden waarvan de kenmerken overeenkomen met de kenmerklijst. Bijvoorbeeld: `A;S;R`

**--include-pattern string** Include only files where the name matches the pattern list. Bijvoorbeeld: `*.jpg;*.pdf;exactName`

**--log-level** string Define the log verbosity for the log file, available levels: `INFO` (all requests and responses), `WARNING` (slow responses), (only failed `ERROR` requests) and `NONE` (no output logs). `INFO`(standaard). 

**--preserve-smb-info**   Standaard onwaar. Behoudt SMB-eigenschapsgegevens (laatste schrijftijd, aanmaaktijd, kenmerkbits) tussen SMB-aware resources (Windows en Azure Files). Deze vlag is van toepassing op zowel bestanden als mappen, tenzij er een alleen-bestandsfilter is opgegeven (bijvoorbeeld include-pattern). De informatie die voor mappen wordt overgedragen, is hetzelfde als die voor bestanden, met uitzondering van De laatste schrijftijd die niet behouden blijft voor mappen.

**--preserve-smb-permissions**   Standaard onwaar. Behoudt SMB ACL's tussen aware resources (Windows en Azure Files). Deze vlag is van toepassing op zowel bestanden als mappen, tenzij er een alleen-bestandsfilter is opgegeven (bijvoorbeeld `include-pattern` ).

**--put-md5**     Maak een MD5-hash van elk bestand en sla de hash op als de eigenschap Content-MD5 van de doelblob of het doelbestand. (De hash wordt standaard NIET gemaakt.) Alleen beschikbaar tijdens het uploaden.

**--recursief** `True` kijk standaard recursief naar subdirectorieën bij het synchroniseren tussen de directories.     `True`(standaard). 

**--s2s-preserve-access-tier**  Behoudt de toegangslaag tijdens het kopiëren van de service naar de service. Raadpleeg [Azure Blob Storage: hot-, cool- en archive-toegangslagen](../blobs/storage-blob-storage-tiers.md) om ervoor te zorgen dat het doelopslagaccount ondersteuning biedt voor het instellen van de toegangslaag. In de gevallen waarin het instellen van de toegangslaag niet wordt ondersteund, gebruikt u s2sPreserveAccessTier=false om het kopiëren van de toegangslaag over te slaat. `true`(standaard). 

## <a name="options-inherited-from-parent-commands"></a>Opties die zijn overgenomen van bovenliggende opdrachten

|Optie|Beschrijving|
|---|---|
|--cap-mbps uint32|Beperk de overdrachtssnelheid, in megabits per seconde. De doorvoer per moment kan enigszins afwijken van de limiet. Als deze optie is ingesteld op nul of wordt weggelaten, wordt de doorvoer niet afgekapt.|
|--output-type string|Indeling van de uitvoer van de opdracht. De keuzes zijn onder andere: text, json. De standaardwaarde is 'text'.|
|--trusted-microsoft-suffixes string   |Hiermee geeft u extra domeinachtervoegsels op waar Azure Active Directory aanmeldingstokens kunnen worden verzonden.  De standaardwaarde is *.core.windows.net;*. core.chinacloudapi.cn; *.core.cloudapi.de;*. core.usgovcloudapi.net'. Alle die hier worden vermeld, worden toegevoegd aan de standaardinstelling. Voor de beveiliging moet u alleen Microsoft Azure hier. Scheid meerdere vermeldingen met punt-dubbele punt.|

## <a name="see-also"></a>Zie ook

- [azcopy](storage-ref-azcopy.md)
