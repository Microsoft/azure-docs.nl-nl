---
title: Acl's, kenmerken en tijds tempels van bestanden behouden met Azure Data Box
description: Acl's, tijds tempels en kenmerken die zijn bewaard tijdens het kopiëren van gegevens via SMB naar Azure Data Box. Meta gegevens kopiëren met Windows-en Linux-hulpprogram ma's voor het kopiëren van gegevens.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: conceptual
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: e8df77356b6b5b1b40e2abd772e13c2e811413ae
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91950309"
---
# <a name="preserving-file-acls-attributes-and-timestamps-with-azure-data-box"></a>Acl's, kenmerken en tijds tempels van bestanden behouden met Azure Data Box

Met Azure Data Box kunt u toegangs beheer lijsten (Acl's), tijds tempels en bestands kenmerken behouden bij het verzenden van gegevens naar Azure. In dit artikel worden de meta gegevens beschreven die u kunt overdragen bij het kopiëren van gegevens naar Data Box via SMB (Server Message Block) om deze te uploaden naar Azure Files. 

Er zijn specifieke stappen voor het kopiëren van meta gegevens met hulpprogram ma's voor het kopiëren van Windows en Linux. Meta gegevens blijven niet behouden bij het overdragen van gegevens naar de Blob-opslag.

In dit artikel worden de Acl's, tijds tempels en bestands kenmerken die worden overgedragen, gezamenlijk aangeduid als *meta gegevens*.

## <a name="transferred-metadata"></a>Overgedragen meta gegevens

De volgende meta gegevens worden overgebracht wanneer gegevens van de Data Box naar Azure Files worden geüpload.

#### <a name="timestamps"></a>Timestamps

De volgende tijds tempels worden overgedragen:
- CreationTime
- LastWriteTime

De volgende tijds tempel wordt niet overgedragen:
- LastAccessTime
  
#### <a name="file-attributes"></a>Bestands kenmerken

Bestands kenmerken voor zowel bestanden als directory's worden overgedragen, tenzij anders vermeld.

De volgende bestands kenmerken worden overgedragen:
- FILE_ATTRIBUTE_READONLY (alleen bestand)
- FILE_ATTRIBUTE_HIDDEN
- FILE_ATTRIBUTE_SYSTEM
- FILE_ATTRIBUTE_DIRECTORY (alleen Directory)
- FILE_ATTRIBUTE_ARCHIVE
- FILE_ATTRIBUTE_TEMPORARY (alleen bestand)
- FILE_ATTRIBUTE_NO_SCRUB_DATA

De volgende bestands kenmerken worden niet overgedragen:
- FILE_ATTRIBUTE_OFFLINE
- FILE_ATTRIBUTE_NOT_CONTENT_INDEXED
  
Alleen-lezen kenmerken voor directory's worden niet overgedragen.

#### <a name="acls"></a>Beheer

Alle Acl's voor mappen en bestanden die u naar de Data Box over SMB kopieert, worden gekopieerd en overgedragen. Overdrachten omvatten zowel discretionaire Acl's (DACL'S) als systeem-Acl's (SACL'S). Voor Linux worden alleen Windows NT-Acl's overgedragen.

Acl's worden niet overgedragen tijdens gegevens kopieën via Network File System (NTS) en wanneer u de Data Copy-service gebruikt om uw gegevens over te dragen. De Data Copy-service leest gegevens rechtstreeks vanuit uw shares en kan geen Acl's lezen.

Zelfs als uw hulp programma voor het kopiëren van gegevens geen Acl's kopieert, worden de standaard-Acl's voor mappen en bestanden overgebracht naar Azure Files. De standaard-Acl's hebben machtigingen voor het ingebouwde Administrator account, het systeem account en de SMB-share gebruikers account die is gebruikt voor het koppelen en kopiëren van gegevens in de Data Box.

De Acl's bevatten Security descriptors met de volgende eigenschappen: Acl's, owner, Group, SACL.

De overdracht van Acl's is standaard ingeschakeld. U kunt deze instelling uitschakelen in de lokale web-UI op uw Data Box. Zie [de lokale web-UI gebruiken voor het beheren van uw data box en data Box Heavy](./data-box-local-web-ui-admin.md)voor meer informatie.

> [!NOTE]
> Bestanden met Acl's met voorwaardelijke ACE-teken reeksen (Access Control Entry) worden niet gekopieerd. Dit is een bekend probleem. U kunt dit probleem omzeilen door deze bestanden hand matig te kopiëren naar de Azure Files-share door de share te koppelen en vervolgens een kopieer programma te gebruiken dat het kopiëren van Acl's ondersteunt.

## <a name="copying-data-and-metadata"></a>Gegevens en meta gegevens kopiëren

Als u de Acl's, tijds tempels en kenmerken voor uw gegevens wilt overdragen, gebruikt u de volgende procedures om gegevens te kopiëren naar de Data Box. 

### <a name="windows-data-copy-tool"></a>Hulp programma Windows Data Copy

Als u gegevens wilt kopiëren naar uw Data Box via SMB, gebruikt u een met SMB compatibel hulp programma voor het kopiëren van bestanden, zoals `robocopy` . Met de volgende voorbeeld opdracht worden alle bestanden en mappen gekopieerd, waarbij meta gegevens worden overgebracht samen met de informatie.

Wanneer u de `/copyall` or `/dcopy:DAT` -optie gebruikt, moet u ervoor zorgen dat de vereiste bevoegdheden voor de back-upoperator niet zijn uitgeschakeld. Zie [de lokale web-UI gebruiken voor het beheren van uw data box en data Box Heavy](./data-box-local-web-ui-admin.md)voor meer informatie. 

```console
robocopy <Source> <Target> * /copyall /e /dcopy:DAT /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /log+:<LogFile>
```

waar

|Optie |Beschrijving |
|------------------- | ----- |
|`/copyall` |Kopieert alle kenmerken.|
|`/e`      |Kopieert submappen, met inbegrip van lege directory's.         |
|`/dcopy:DAT`  |Hiermee kopieert u gegevens, kenmerken en tijds tempels. Opmerking: de optie/dcopy: DAT moet worden gebruikt voor het overdragen `CreationTime` van mappen. |
|`/r:3`    |Hiermee geeft u 3 nieuwe pogingen op mislukte exemplaren.         |
|`/w:60`   |Hiermee geeft u een wacht tijd van 60 seconden tussen nieuwe pogingen.         |
|`/is`     |Neemt dezelfde bestanden op.         |
|`/nfl`    |Maakt geen logboek bestands namen.         |
|`/ndl`    |Maakt geen logboek namen van mappen.        |
|`/np`     |De voortgang van de Kopieer bewerking wordt niet weer gegeven.         |
|`/MT:32 or 64`  |Maakt gebruik van multi-threading, met 32-of 64-threads.           |
|`/fft`    |De nauw keurigheid van de tijds tempel voor elk bestands systeem reduceert.        |
|`/log+:<LogFile>`  |Voegt de uitvoer toe aan het bestaande logboekbestand.|

`robocopy`Zie [zelf studie: gegevens kopiëren naar Azure data box via SMB](./data-box-deploy-copy-data.md) voor meer informatie over deze para meters.

### <a name="linux-data-copy-tool"></a>Hulp programma voor Linux Data Copy

Het overbrengen van meta gegevens in Linux is een proces dat uit twee stappen bestaat. Eerst kopieert u de bron gegevens met een hulp programma zoals `rsync` , waarmee geen meta gegevens worden gekopieerd. Nadat u de gegevens hebt gekopieerd, kunt u de meta gegevens kopiëren met een hulp programma zoals `smbcacls` of `cifsacl` . 

De volgende voorbeeld opdrachten maken de eerste stap en het kopiëren van de gegevens met behulp van `rsync` . 

```console
cp -aR /etc /opt/ 
rsync -avP /etc /opt (-a copies a directory)
```

## <a name="next-steps"></a>Volgende stappen

- [Gegevens naar Azure Data Box Disk kopiëren via SMB](./data-box-deploy-copy-data.md)