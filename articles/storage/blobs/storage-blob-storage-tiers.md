---
title: Toegangs lagen voor Azure Blob Storage-hot, cool en Archive
description: Meer informatie over de toegangs lagen hot, cool en Archive voor Azure Blob Storage. Bekijk opslag accounts die ondersteuning bieden voor lagen.
author: mhopkins-msft
ms.author: mhopkins
ms.date: 03/18/2021
ms.service: storage
ms.subservice: blobs
ms.topic: conceptual
ms.reviewer: klaasl
ms.openlocfilehash: 1a1cb8e1676405cbfbb3f4f61c86d8136b688b88
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104656835"
---
# <a name="access-tiers-for-azure-blob-storage---hot-cool-and-archive"></a>Toegangs lagen voor Azure Blob Storage-hot, cool en Archive

Azure Storage biedt verschillende toegangs lagen, zodat u gegevens van blob-objecten op de meest rendabele manier kunt opslaan. Beschik bare toegangs lagen zijn onder andere:

- **Dynamisch** geoptimaliseerd voor het opslaan van gegevens die regel matig worden geopend.
- **Koud** geoptimaliseerd voor het opslaan van gegevens die niet regel matig worden geopend en die gedurende ten minste 30 dagen worden opgeslagen.
- **Archief** -geoptimaliseerd voor het opslaan van gegevens die zelden worden gebruikt en die gedurende ten minste 180 dagen worden opgeslagen met flexibele latentie vereisten, op volg orde van uren.

De volgende overwegingen zijn van toepassing op de verschillende toegangslagen:

- De toegangs laag kan tijdens of na het uploaden worden ingesteld op een blob.
- Alleen de statische- en dynamische-toegangslagen kunnen op accountniveau worden ingesteld. De toegangs laag voor het archief kan alleen worden ingesteld op het niveau van de blob.
- Gegevens in de laag voor een coolbar hebben een iets lagere Beschik baarheid, maar hebben nog steeds een hoge duurzaamheid, ophaal latentie en doorvoer kenmerken die vergelijkbaar zijn met dynamische gegevens. Voor coole gegevens zijn iets lagere Beschik baarheid en hogere toegangs kosten acceptabel voor de verhoudingen van de totale opslag kosten in vergelijking met de dynamische gegevens. Zie [Dienstovereenkomst voor Storage](https://azure.microsoft.com/support/legal/sla/storage/v1_5/) voor meer informatie.
- De gegevens in de Access-laag voor het archief worden offline opgeslagen. De archief laag biedt de laagste opslag kosten, maar ook de hoogste toegangs kosten en-latentie.
- De warme en cool-laag ondersteunen alle redundantie opties. De archief laag ondersteunt alleen LRS, GRS en RA-GRS.
- Limieten voor gegevens opslag worden ingesteld op account niveau en niet per toegangs laag. U kunt ervoor kiezen om al uw limieten in één laag of in alle drie de lagen te gebruiken.

Gegevens die zijn opgeslagen in de Cloud, groeien in een exponentieel tempo. Voor een effectief beheer van de kosten voor uw groeiende opslagbehoeften is het vanwege kostenoptimalisatie een goed idee om de gegevens te ordenen op basis van kenmerken als toegangsfrequentie en geplande bewaarperiode. Gegevens die in de cloud zijn opgeslagen, kunnen verschillen vertonen naar gelang de manier waarop ze gedurende hun levensduur worden gegenereerd, verwerkt en geopend. Sommige gegevens worden tijdens hun hele levensduur actief geopend en gewijzigd. Andere gegevens worden in het begin van hun levensduur regelmatig geopend, terwijl dit naarmate de tijd verstrijkt, aanzienlijk minder vaak gebeurt. Sommige gegevens verblijven inactief in de cloud en worden zelden of nooit geopend nadat ze zijn opgeslagen.

Elk van deze scenario's voor gegevens toegang is voor deel van een andere toegangs laag die is geoptimaliseerd voor een specifiek toegangs patroon. Met de toegangs lagen hot, cool en Archive kunnen Azure Blob Storage deze behoefte voor gedifferentieerde toegangs lagen met afzonderlijke prijs modellen.

De volgende hulpprogram ma's en client bibliotheken ondersteunen alle lagen op BLOB-niveau en archief opslag.

- Azure Portal
- PowerShell
- Azure CLI-hulpprogram ma's
- .NET-clientbibliotheek
- Java-clientbibliotheek
- Python-clientbibliotheek
- Client bibliotheek Node.js

[!INCLUDE [storage-multi-protocol-access-preview](../../../includes/storage-multi-protocol-access-preview.md)]

## <a name="storage-accounts-that-support-tiering"></a>Storage-accounts die ondersteuning bieden voor opslaglagen

De gegevenslaag van object opslag tussen hot, cool en Archive wordt ondersteund in Blob Storage-en Algemeen v2-accounts (GPv2). Algemeen v1 (GPv1)-accounts bieden geen ondersteuning voor lagen. U kunt uw bestaande GPv1-of Blob Storage-accounts eenvoudig converteren naar GPv2-accounts via de Azure Portal. GPv2 biedt nieuwe prijzen en functies voor blobs, bestanden en wacht rijen. Sommige functies en prijs delen worden alleen aangeboden in GPv2-accounts. Sommige werk belastingen kunnen duurder zijn voor GPv2 dan GPv1. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

Blob Storage-en GPv2-accounts bieden het kenmerk **toegangs** niveau op account niveau. Met dit kenmerk kunt u de standaardlaag voor toegang opgeven voor een blob die niet expliciet is ingesteld op object niveau. Voor objecten waarvoor de laag expliciet is ingesteld, is de laag van de account niet van toepassing. De archief laag kan alleen op object niveau worden toegepast. U kunt op elk gewenst moment scha kelen tussen toegangs lagen.

Gebruik GPv2 in plaats van Blob Storage accounts voor lagen. GPv2 biedt ondersteuning voor alle functies die Blob Storage accounts ondersteunen, plus nog veel meer. De prijzen tussen Blob Storage en GPv2 zijn bijna identiek, maar sommige nieuwe functies en prijs verlagingen zijn alleen beschikbaar voor GPv2-accounts.

De prijsstructuur tussen GPv1- en GPv2-accounts is verschillend en klanten moeten beide zorgvuldig evalueren alvorens te besluiten GPv2-accounts te gebruiken. U kunt een bestaand Blob Storage- of GPv1-account eenvoudig met één muisklik omzetten naar GPv2. Zie [Overzicht van Azure-opslagaccount](../common/storage-account-overview.md) voor meer informatie.

## <a name="hot-access-tier"></a>Dynamische-toegangslaag

De dynamische-toegangslaag heeft hogere opslagkosten dan de statische-toegangslaag en de archieftoegangslaag, maar de laagste toegangskosten. Voorbeelden van gebruiksscenario's voor de dynamische-toegangslaag:

- Gegevens die worden gebruikt in het actieve gebruik of waarvan wordt verwacht dat ze worden gelezen uit en geschreven naar regel matig
- Gegevens die worden voor bereid voor verwerking en uiteindelijk de migratie naar de laag met coole toegang

## <a name="cool-access-tier"></a>Statische-toegangslaag

De statische-toegangslaag heeft lagere opslagkosten en hogere toegangskosten in vergelijking met dynamische opslag. Deze laag is bedoeld voor gegevens die ten minste dertig dagen in de Cold Storage verblijven. Voorbeelden van gebruiksscenario's voor de statische-toegangslaag:

- Back-ups op korte termijn en nood herstel
- Oudere gegevens worden niet vaak gebruikt, maar moeten naar verwachting onmiddellijk beschikbaar zijn wanneer ze worden geopend
- Grote gegevens sets die kostenbesparend moeten worden opgeslagen, terwijl er meer gegevens worden verzameld voor toekomstige verwerking

## <a name="archive-access-tier"></a>Archieftoegangslaag

De toegangs laag voor het archief heeft de laagste opslag kosten, maar hogere kosten voor het ophalen van gegevens vergeleken met warme en coole lagen. Gegevens moeten gedurende ten minste 180 dagen in de archief laag blijven of onderhevig zijn aan de kosten voor een vroege verwijdering. Het ophalen van gegevens in de archief laag kan enkele uren duren, afhankelijk van de opgegeven rehydratatie prioriteit. Voor kleine objecten kan een met een hoge prioriteit gehydrateerd object binnen een uur worden opgehaald uit het archief. Zie [BLOB-gegevens opnieuw inbreken van de archief laag](storage-blob-rehydration.md) voor meer informatie.

Hoewel een BLOB zich in archief opslag bevindt, zijn de BLOB-gegevens offline en kunnen ze niet worden gelezen of gewijzigd. Als u een BLOB in Archive wilt lezen of downloaden, moet u deze eerst opnieuw laten worden gehydrateerd tot een online-laag. U kunt geen moment opnamen maken van een BLOB in archief opslag. De BLOB-meta gegevens blijven echter online en beschikbaar, zodat u de blob, de eigenschappen, de meta gegevens en de index Tags van de BLOB kunt weer geven. Het is niet toegestaan om de BLOB-meta gegevens in te stellen of te wijzigen in het archief. U kunt echter de index Tags van de BLOB instellen en wijzigen. Voor blobs in archief worden de enige geldige bewerkingen [weer geven BLOB-eigenschappen](/rest/api/storageservices/get-blob-properties), BLOB- [meta gegevens ophalen](/rest/api/storageservices/get-blob-metadata), [BLOB-Tags instellen](/rest/api/storageservices/set-blob-tags), [BLOB-Tags ophalen](/rest/api/storageservices/get-blob-tags), blobs [op labels zoeken](/rest/api/storageservices/find-blobs-by-tags) [, blobs](/rest/api/storageservices/list-blobs)- [laag instellen](/rest/api/storageservices/set-blob-tier), [BLOB kopiëren](/rest/api/storageservices/copy-blob)en BLOB [verwijderen](/rest/api/storageservices/delete-blob).

Voor beelden van gebruiks scenario's voor de Access-laag voor archiveren zijn:

- Langetermijnback-up, secundaire back-up en gegevenssets voor archivering
- Oorspronkelijke (onbewerkte) gegevens die moeten worden bewaard, zelfs nadat deze in de uiteindelijke bruikbare vorm zijn verwerkt
- Compatibiliteits-en archiverings gegevens die gedurende lange tijd moeten worden opgeslagen en die nauwelijks worden gebruikt

> [!NOTE]
> De opslaglaag wordt niet ondersteund voor ZRS-, GZRS-of RA-GZRS-accounts. Het migreren van LRS naar GRS wordt niet ondersteund als het opslag account blobs in de laag Archive bevat.

## <a name="account-level-tiering"></a>Lagen op account niveau

Blobs in alle drie de toegangs lagen kunnen naast elkaar worden gebruikt in hetzelfde account. Een blob die geen expliciet toegewezen laag heeft, leidt de laag af van de instelling van de toegangs laag voor het account. Als de toegangs laag afkomstig is van het account, ziet u de eigenschap voor de **uitstelde** blob van de toegangs laag die is ingesteld op ' True ', en de BLOB-eigenschap van de **Access-laag** overeenkomt met de account-laag. In de Azure Portal wordt de eigenschap voor de _toegang tot de laag_ weer gegeven met de BLOB-toegangs laag als **Hot (uitgesteld)** of **koud (uitgesteld)**.

Het wijzigen van de toegangs laag voor het account is van toepassing op alle objecten in de _toegangs laag_ , die zijn opgeslagen in het account waarvoor geen expliciete laag is ingesteld. Als u de laag van dynamisch naar koud wisselt, wordt u in rekening gebracht voor schrijf bewerkingen (per 10.000) voor alle blobs zonder een set-laag in GPv2-accounts. Er worden geen kosten in rekening gebracht voor deze wijziging in Blob Storage accounts. Er worden kosten in rekening gebracht voor zowel lees bewerkingen (per 10.000) als voor het ophalen van gegevens (per GB) als u van koud naar warm schakelt Blob Storage-of GPv2-accounts.

Alleen de lagen hot en cool kunnen worden ingesteld als het standaard account voor toegang tot de laag. Archive kan alleen op objectniveau worden ingesteld. Bij het uploaden van blobs kunt u de toegangs laag opgeven die u wilt gebruiken als dynamisch, koud of archief, ongeacht de standaardlaag. Met deze functie kunt u gegevens rechtstreeks naar de archief laag schrijven om de kosten besparingen te bedenken vanaf het moment dat u gegevens in Blob Storage maakt.

## <a name="blob-level-tiering"></a>Laaginstelling op blobniveau

Met lagen op blobniveau kunt u gegevens uploaden naar de toegangs laag van uw keuze met behulp van de bewerkingen [put-BLOB](/rest/api/storageservices/put-blob) of [put list](/rest/api/storageservices/put-block-list) en wijzigt u de laag van uw gegevens op object niveau met behulp van de functie voor het instellen van een [BLOB-laag](/rest/api/storageservices/set-blob-tier) of [levenscyclus beheer](#blob-lifecycle-management) . U kunt gegevens uploaden naar uw vereiste Access-laag en de BLOB Access-laag eenvoudig wijzigen onder de dynamische, koude of archief lagen als gebruiks patronen veranderen, zonder dat u gegevens tussen accounts hoeft te verplaatsen. Alle laag wijzigings aanvragen gebeuren onmiddellijk en laag wijzigingen tussen warme en koelen zijn direct. Het kan enkele uren duren om een BLOB te reactiveren uit de archief laag.

Het tijdstip waarop de laatste wijziging aan de blob-laag heeft plaatsgevonden, wordt weergegeven via de blob-eigenschap **Access Tier Change Time**. De tijd voor het wijzigen van de **toegangs lagen** is een eigenschap op BLOB-niveau en wordt niet bijgewerkt wanneer de standaardlaag van het account wordt gewijzigd. De eigenschappen van het account en de BLOB-eigenschappen zijn gescheiden. Het is prohibitively kostbaar om de **wijzigings tijd van de toegangs tier** bij te werken op elke Blob in een opslag account wanneer de standaard Access-laag van het account wordt gewijzigd.

Bij het overschrijven van een BLOB in de warme of koude laag, neemt de zojuist gemaakte BLOB de laag over van de blob die is overschreven, tenzij de nieuwe BLOB-toegangs laag expliciet is ingesteld bij het maken. Als een BLOB zich in de laag Archive bevindt, kan deze niet worden overschreven, dus het uploaden van dezelfde blob is niet toegestaan in dit scenario.

> [!NOTE]
> Archiefopslag en laaginstelling op blobniveau ondersteunen alleen blok-blobs.

### <a name="blob-lifecycle-management"></a>Beheer van de BLOB levenscyclus

Beheer van levenscyclus opslag biedt een rijk, op regels gebaseerd beleid dat u kunt gebruiken om uw gegevens over te zetten naar de beste Access-laag en om gegevens aan het einde van de levens cyclus te laten verlopen. Zie [kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md) voor meer informatie.

> [!NOTE]
> Gegevens die zijn opgeslagen in een blok-Blob-opslag account (Premium-prestaties), kunnen momenteel niet worden getierd naar warme, koude of archief met behulp van [set BLOB](/rest/api/storageservices/set-blob-tier) of Azure Blob Storage levenscyclus beheer.
> Als u gegevens wilt verplaatsen, moet u blobs synchroon kopiëren van het blok-Blob-opslag account naar de hete Access-laag in een ander account met behulp [van de put-block from URL API](/rest/api/storageservices/put-block-from-url) of een versie van AzCopy die deze API ondersteunt.
> Met de put-functie **van URL** -API worden gegevens synchroon gekopieerd op de server, wat betekent dat de aanroep alleen wordt voltooid wanneer alle gegevens van de oorspronkelijke server locatie naar de doel locatie worden verplaatst.

### <a name="blob-level-tiering-billing"></a>Facturering van laaginstelling op blobniveau

Wanneer een BLOB wordt geüpload of verplaatst tussen lagen, wordt het bijbehorende tarief direct bij het uploaden of de laag wijziging in rekening gebracht.

Wanneer een BLOB wordt verplaatst naar een koele laag (Hot->cool, Hot->Archive of koele->Archive), wordt de bewerking gefactureerd als een schrijf bewerking naar de doellaag, waar de kosten voor de schrijf bewerking (per 10.000) en het schrijven van gegevens (per GB) van de doellaag van toepassing zijn.

Wanneer een BLOB wordt verplaatst naar een warmere laag (archief->cool, Archive->Hot of cool->Hot), wordt de bewerking gefactureerd als een lees bewerking vanuit de bronlaag, waarbij de kosten voor lees bewerkingen (per 10.000) en het ophalen van gegevens (per GB) van de bronlaag van toepassing zijn. De kosten voor [vroegtijdig verwijderen](#cool-and-archive-early-deletion) voor elke blob die uit de cool-of Archive-laag worden verplaatst, kunnen ook worden toegepast. [Reactiveren-gegevens van de archiefmap](storage-blob-rehydration.md) worden tijd en gegevens worden in rekening gebracht totdat de gegevens online worden teruggezet en de BLOB-laag wordt gewijzigd in warme of koud.

De volgende tabel bevat een overzicht van de manier waarop laag wijzigingen worden gefactureerd.

| | **Schrijf kosten (bewerking + toegang)** | **Lees kosten (bewerking + toegang)** |
| ---- | ----- | ----- |
| **Set Blob Tier** (Blob-laag instellen) | warm > cool<br> Archief met Hot ><br> Archief met koele > | Archief-> cool<br> Archief-> Hot<br> koud > warm

### <a name="cool-and-archive-early-deletion"></a>Vroege verwijdering voor Koud en Archief

Elke blob die wordt verplaatst naar de cool-laag (alleen GPv2-accounts), is onderhevig aan een leuke verwijderings periode van 30 dagen. Elke blob die wordt verplaatst naar de laag van het archief, is onderhevig aan een vroegtijdige verwijderings periode van 180 dagen van een archief. Deze kosten zijn evenredig verdeeld. Als een BLOB bijvoorbeeld wordt verplaatst naar Archive en vervolgens na 45 dagen wordt verwijderd of verplaatst naar de warme laag, worden de kosten voor vroegtijdige verwijdering in rekening gebracht die gelijk zijn aan 135 (180 min 45) dagen waarin de blob is opgeslagen in archief.

Er zijn enkele details bij het verplaatsen tussen de coole en archief lagen:

1. Als een BLOB wordt afgeleid van de standaard Access-laag van het opslag account en de BLOB wordt verplaatst naar Archive, worden er geen kosten voor vroegtijdige verwijdering in rekening gebracht.
1. Als een BLOB expliciet wordt verplaatst naar de cool-laag en vervolgens wordt verplaatst naar Archive, worden de kosten voor vroegtijdig verwijderen van toepassing.

Bereken de tijd voor vroegtijdige verwijdering met behulp van de **laatste gewijzigde** BLOB-eigenschap, als er geen wijzigingen in de toegangs categorie zijn. Gebruik anders wanneer de Access-laag voor het laatst is gewijzigd in Cool of Archive door de BLOB-eigenschap weer te geven: **Access-tier-time-** out. Zie [Eigenschappen van BLOB ophalen](/rest/api/storageservices/get-blob-properties)voor meer informatie over blob-eigenschappen.

## <a name="comparing-block-blob-storage-options"></a>Vergelijkings opties voor blok-Blob-opslag

In de volgende tabel ziet u een vergelijking van de toegangs lagen voor het blok keren van Premium-prestaties en de dynamische, koude en archief opslag.

|                                           | **Premium-prestaties**   | **Warme laag** | **Cool-laag**       | **Laag van archief**  |
| ----------------------------------------- | ------------------------- | ------------ | ------------------- | ----------------- |
| **Beschikbaarheid**                          | 99,9%                     | 99,9%        | 99%                 | Offline           |
| **Beschikbaarheid** <br> **(RA-GRS-leesbewerkingen)**  | N.v.t.                       | 99,99%       | 99,9%               | Offline           |
| **Gebruiks kosten**                         | Hogere opslag kosten, lagere toegang en transactie kosten | Hogere opslag kosten, lagere toegang en transactie kosten | Lagere opslag kosten, hogere toegang en transactie kosten | Laagste opslag kosten, hoogste toegang en transactie kosten |
| **Minimale opslagduur**              | N.v.t.                       | N.v.t.          | 30 dagen<sup>1</sup> | 180 dagen
| **Latentie** <br> **(Tijd tot eerste byte)** | Milliseconden met één cijfer | milliseconden | milliseconden        | uur<sup>2</sup> |

<sup>1</sup> objecten in de cool-laag op GPv2-accounts hebben een minimale Bewaar periode van 30 dagen. Blob Storage accounts hebben geen minimale Bewaar duur voor de cool-laag.

<sup>2</sup> Archive Storage biedt momenteel ondersteuning voor twee rehydratatie-prioriteiten, hoog en standaard, die verschillende latenties en kosten voor het ophalen bieden. Zie BLOB-gegevens opnieuw [inbreken vanuit de laag archief](storage-blob-rehydration.md)voor meer informatie.

> [!NOTE]
> Blob Storage-accounts ondersteunen dezelfde prestatie-en schaalbaarheids doelen als voor algemeen gebruik v2-opslag accounts. Zie [schaalbaarheids-en prestatie doelen voor Blob Storage](scalability-targets.md)voor meer informatie.

## <a name="pricing-and-billing"></a>Prijzen en facturering

Alle opslag accounts gebruiken een prijs model voor blok-Blob-opslag op basis van de laag van elke blob. Houd rekening met de volgende overwegingen met betrekking tot facturering:

- **Opslagkosten**: de kosten voor het opslaan van gegevens hangen niet alleen af van de hoeveelheid opgeslagen gegevens, maar ook van de gebruikte toegangslaag. De kosten per GB nemen af als de laag minder dynamisch ('cooler') wordt.
- **Kosten van gegevenstoegang**: de kosten voor gegevenstoegang nemen toe als de laag minder dynamisch ('cooler') wordt. Voor gegevens in de coole en archief-toegangs laag, worden er kosten in rekening gebracht voor gegevens toegang per gigabyte voor lees bewerkingen.
- **Transactie kosten**: er zijn kosten per trans actie voor alle lagen die toenemen naarmate de laag koeler wordt.
- **Kosten voor gegevens overdracht met geo-replicatie**: deze kosten zijn alleen van toepassing op accounts waarvoor geo-replicatie is geconfigureerd, inclusief GRS en RA-GRS. Kosten voor gegevensoverdracht met geo-replicatie worden in rekening gebracht per GB.
- **Kosten voor uitgaande gegevensoverdracht**: uitgaande gegevensoverdracht (gegevens die buiten een Azure-regio worden overgedragen) worden gefactureerd voor bandbreedtegebruik per GB, net zoals bij opslagaccounts voor algemeen gebruik.
- **De toegangs laag wijzigen**: als u de toegangs laag van het account wijzigt, worden de kosten voor het wijzigen van lagen in rekening gebracht voor alle blobs waarvoor geen expliciete laag is ingesteld. Zie voor meer informatie over het wijzigen van de toegangs laag voor één BLOB de [facturering op BLOB-niveau](#blob-level-tiering-billing).

    Het wijzigen van de toegangs laag voor een BLOB wanneer versie beheer is ingeschakeld, of als de BLOB moment opnamen bevat, kunnen er extra kosten in rekening worden gebracht. Zie [prijzen en facturering](versioning-overview.md#pricing-and-billing) in de documentatie van de BLOB-versie voor meer informatie over blobs waarvoor versie beheer is ingeschakeld. Zie [prijzen en facturering](snapshots-overview.md#pricing-and-billing) in de documentatie van de BLOB-moment opnamen voor meer informatie over blobs met moment opnamen.

> [!NOTE]
> Zie de [prijzen](https://azure.microsoft.com/pricing/details/storage/blobs/)voor blok-blobs voor meer informatie over de prijzen voor blok-blobs. Zie pagina met [prijs informatie voor band breedte](https://azure.microsoft.com/pricing/details/bandwidth/) voor meer informatie over de kosten voor uitgaande gegevens overdracht.

## <a name="availability"></a>Beschikbaarheid

Verschillende toegangs lagen, samen met lagen op BLOB-niveau, zijn beschikbaar in geselecteerde regio's. Zie voor een volledige lijst [Azure-producten beschikbaar per regio](https://azure.microsoft.com/global-infrastructure/services/?products=storage).

## <a name="next-steps"></a>Volgende stappen

Meer informatie over het beheren van blobs en accounts in toegangs lagen.

- [De laag van een BLOB in een Azure Storage account beheren](manage-access-tier.md)
- [De toegangs laag van het standaard account van een Azure Storage account beheren](../common/manage-account-default-access-tier.md)
- [Kosten optimaliseren door Azure Blob Storage Access-lagen te automatiseren](storage-lifecycle-management-concepts.md)
