---
title: Regels voor resourcesetpatronen maken
description: Leer hoe u een patroonregel voor een resourceset maakt om te overschrijven hoe assets worden gegroepeerd in resourcesets
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 04/15/2021
ms.openlocfilehash: b9d6ca88d5e9d49d3973193059197a1aa171e3e8
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568692"
---
# <a name="create-resource-set-pattern-rules"></a>Regels voor resourcesetpatronen maken

Systemen voor gegevensverwerking op schaal slaan doorgaans één tabel op een schijf op als meerdere bestanden. Dit concept wordt weergegeven in Azure Purview met behulp van resourcesets. Een resourceset is één object in de gegevenscatalogus dat een groot aantal assets in de opslag vertegenwoordigt. Zie Understanding [resource sets (Informatie over resourcesets) voor meer informatie.](concept-resource-sets.md)

Bij het scannen van een opslagaccount gebruikt Azure Purview een set gedefinieerde patronen om te bepalen of een groep assets een resourceset is. In sommige gevallen weerspiegelt de groepering van resourcesets van Azure Purview mogelijk niet uw gegevensgegevens. Met regels voor resourcesetpatronen kunt u aanpassen of overschrijven hoe Azure Purview detecteert welke assets zijn gegroepeerd als resourcesets en hoe deze worden weergegeven in de catalogus.

Patroonregels worden momenteel ondersteund in de volgende brontypen:
- Azure Data Lake Storage Gen2
- Azure Blob Storage
- Azure Files


## <a name="how-to-create-a-resource-set-pattern-rule"></a>Een patroonregel voor een resourceset maken

Volg de onderstaande stappen om een nieuwe patroonregel voor een resourceset te maken:

1. Ga naar het beheercentrum. Selecteer **Patroonregels** in het menu onder de kop Resourcesets. Selecteer **+ Nieuw om** een nieuwe regelset te maken.

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-rule.png" alt-text="Een nieuwe patroonregel voor een resourceset maken" border="true":::

1. Voer het bereik van uw resourcesetpatroonregel in. Selecteer het type opslagaccount en de naam van het opslagaccount waar u een regelset op wilt maken. Elke set regels wordt toegepast ten opzichte van een mappadbereik dat is opgegeven in het **veld Mappad.**

   :::image type="content" source="media/how-to-resource-set-pattern-rules/create-new-scoped-resource-set-scope.png" alt-text="Regelconfiguraties voor resourcesetpatronen maken" border="true":::

1. Als u een regel voor een configuratiebereik wilt invoeren, selecteert **u + Nieuwe regel**.

1. Voer in de volgende velden in om een regel te maken:

   1. **Regelnaam:** De naam van de configuratieregel. Dit veld heeft geen invloed op de assets op basis van de regel.

   1. **Gekwalificeerde naam:** Een gekwalificeerd pad dat gebruikmaakt van een combinatie van tekst, dynamische vervangingen en statische vervangingen om assets te matchen met de configuratieregel. Dit pad is relatief ten opzichte van het bereik van de configuratieregel. Zie de [syntaxissectie](#syntax) hieronder voor gedetailleerde instructies over het opgeven van gekwalificeerde namen.

   1. **Weergavenaam:** De weergavenaam van de asset. Dit veld is optioneel. Gebruik tekst zonder tekst en statische vervangingen om aan te passen hoe een asset wordt weergegeven in de catalogus. Zie de sectie [syntaxis](#syntax) hieronder voor meer gedetailleerde instructies.

   1. **Groeper niet als resourceset:** Als deze functie is ingeschakeld, wordt de overeenkomende resource niet gegroepeerd in een resourceset.

      :::image type="content" source="media/how-to-resource-set-pattern-rules/scoped-resource-set-rule-example.png" alt-text="Maak een nieuwe configuratieregel." border="true":::

1. Sla de regel op door op Toevoegen **te klikken.**

## <a name="pattern-rule-syntax"></a><a name="syntax"></a> Syntaxis van patroonregel

Wanneer u regels voor resourcesetpatronen maakt, gebruikt u de volgende syntaxis om op te geven op welke assets regels van toepassing zijn.

### <a name="dynamic-replacers-single-brackets"></a>Dynamische vervangingen (enkele haken)

Enkele haakjes worden gebruikt als **dynamische vervangingen** in een patroonregels. Geef een dynamische vervanging op in de gekwalificeerde naam met behulp van de indeling `{<replacerName:<replacerType>}` . Indien overeenkomend, worden dynamische vervangingen gebruikt als een groeperingsvoorwaarde die aangeeft dat assets moeten worden weergegeven als een resourceset. Als de assets zijn gegroepeerd in een resourceset, bevat het gekwalificeerde pad van de resourceset waar de `{replacerName}` vervanging is opgegeven.

Als er bijvoorbeeld twee assets zijn en overeenkomen met regel , is de `folder1/file-1.csv` `folder2/file-2.csv` `{folder:string}/file-{NUM:int}.csv` resourceset één `{folder}/file-{NUM}.csv` entiteit.

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Speciaal geval: Dynamische vervangingen wanneer u niet groepeert in een resourceset

Als *Niet groep als resourceset* is ingeschakeld voor een patroonregel, is de naam van de vervanging een optioneel veld. `{:<replacerType>}` is een geldige syntaxis. Zou bijvoorbeeld overeenkomen met en en twee verschillende assets maken in `file-{:int}.csv` `file-1.csv` plaats van een `file-2.csv` resourceset.

### <a name="static-replacers-double-brackets"></a>Statische vervangingen (dubbele haken)

Dubbele haken worden gebruikt als **statische vervangingen** in de gekwalificeerde naam van een patroonregel. Geef een statische vervanging op in de gekwalificeerde naam met behulp van de indeling `{{<replacerName>:<replacerType>}}` . Als deze overeenkomen, maakt elke set unieke statische vervangingswaarden verschillende resourcesetgroeperingen.

Als er bijvoorbeeld twee assets zijn en overeenkomen met regel , worden er `folder1/file-1.csv` `folder2/file-2.csv` twee `{{folder:string}}/file-{NUM:int}.csv` resourcesets gemaakt `folder1/file-{NUM}.csv` en `folder2/file-{NUM}.csv` .

Statische vervangingen kunnen worden gebruikt om de weergavenaam op te geven van een asset die overeenkomt met een patroonregel. Als `{{<replacerName>}}` u in de weergavenaam van een regel gebruikt, wordt de overeenkomende waarde in de assetnaam gebruikt.

### <a name="available-replacement-types"></a>Beschikbare vervangingstypen

Hieronder vindt u de beschikbare typen die kunnen worden gebruikt in statische en dynamische vervangingen:

| Type | Structuur |
| ---- | --------- |
| tekenreeks | Een reeks van 1 of meer Unicode-tekens, inclusief scheidingstekens zoals spaties. |
| int | Een reeks van 1 of meer 0-9 ASCII-tekens, kan het voorvoegsel 0 hebben (bijvoorbeeld 0001). |
| guid | Een reeks van 32 of 8-4-4-4-12 tekenreeksweergave van een UUID zoals gedefinieerddefa in [RFC 4122.](https://tools.ietf.org/html/rfc4122) |
| date | Een reeks van 6 of 8 0-9 ASCII-tekens met optioneel scheidingstekens: yyyymmdd, yyyy-mm-dd, yymmdd, yy-mm-dd, opgegeven in [RFC 3339.](https://tools.ietf.org/html/rfc3339) |
| tijd | Een reeks van 4 of 6 0-9 ASCII-tekens met optioneel scheidingstekens: HHmm, HH:mm, HHmmss, HH:mm:ss opgegeven in [RFC 3339.](https://tools.ietf.org/html/rfc3339) |
| tijdstempel | Een reeks van 12 of 14 0-9 ASCII-tekens met optioneel scheidingstekens: yyyy-mm-ddTHH:mm, yyyymmddhhmm, yyyy-mm-ddTHH:mm:ss, yyyymmddHHmmss opgegeven in [RFC 3339](https://tools.ietf.org/html/rfc3339). |
| booleaans | Kan 'true' of 'false' bevatten, niet-casegevoelig. |
| getal | Een reeks van 0 of meer 0-9 ASCII-tekens. Het kan 0 voorvoegsels (bijvoorbeeld 0001) zijn, gevolgd door optioneel een punt '.' en een reeks van 1 of meer 0-9 ASCII-tekens. Het kan 0 navoegen (bijvoorbeeld .100) |
| hex | Een reeks van 1 of meer ASCII-tekens uit de set 0-1 en A-F, kan de waarde worden vooraf 0 |
| landinstelling | Een tekenreeks die overeenkomt met de syntaxis die is opgegeven in [RFC 5646.](https://tools.ietf.org/html/rfc5646) |

## <a name="order-of-resource-set-pattern-rules-getting-applied"></a>Volgorde van regels voor resourcesetpatronen die worden toegepast

Hieronder vindt u de volgorde van de bewerkingen voor het toepassen van patroonregels:

1. Specifiekere scopes hebben prioriteit als een asset overeenkomt met twee regels. Regels in een bereik zijn bijvoorbeeld `container/folder` van toepassing vóór regels in het bereik `container` .

1. Volgorde van regels binnen een specifiek bereik. Dit kan worden bewerkt in de UX.

1. Als een asset niet overeen komt met een opgegeven regel, zijn de standaard heuristieken van de resourceset van toepassing.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

SAP-gegevensextractie in volledige en deltabelastingen

#### <a name="inputs"></a>Invoerwaarden

Bestanden:

- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
- `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`

#### <a name="pattern-rule"></a>Patroonregel

**Bereik:**`https://myazureblob.blob.core.windows.net/bar/`

**Weergavenaam:** Externe klant

**Gekwalificeerde naam:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Resourceset:** true

#### <a name="output"></a>Uitvoer

Eén resourceset-asset

**Weergavenaam:** Externe klant

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Voorbeeld 2

IoT-gegevens in AVRO-indeling

#### <a name="inputs"></a>Invoerwaarden

Bestanden:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rules"></a>Patroonregels

**Bereik:**`https://myazureblob.blob.core.windows.net/bar/`

Regel 1

**Weergavenaam:** 'machine-89'

**Gekwalificeerde naam:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Resourceset:** true

Regel 2

**Weergavenaam:** 'machine-90'

**Gekwalificeerde naam:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

#### <a name="resource-set-true"></a>*Resourceset: true*

#### <a name="outputs"></a>Uitvoerwaarden

2 resourcesets

Resourceset 1

**Weergavenaam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resourceset 2

**Weergavenaam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Voorbeeld 3

IoT-gegevens in AVRO-indeling

#### <a name="inputs"></a>Invoerwaarden

Bestanden:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Patroonregel

**Bereik:**`https://myazureblob.blob.core.windows.net/bar/`

**Weergavenaam:** 'Machine-{{machineid}}'

**Gekwalificeerde naam:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Resourceset:** true

#### <a name="outputs"></a>Uitvoerwaarden

Resourceset 1

**Weergavenaam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resourceset 2

**Weergavenaam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Voorbeeld 4

Groep u niet in resourcesets

#### <a name="inputs"></a>Invoerwaarden

Bestanden:

- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
- `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

#### <a name="pattern-rule"></a>Patroonregel

**Bereik:**`https://myazureblob.blob.core.windows.net/bar/`

**Weergavenaam:**`Machine-{{machineid}}`

**Gekwalificeerde naam:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Resourceset:** false

#### <a name="outputs"></a>Uitvoerwaarden

4 afzonderlijke assets

Asset 1

**Weergavenaam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Asset 2

**Weergavenaam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Asset 3

**Weergavenaam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Asset 4

**Weergavenaam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag [door een Azure Data Lake Gen2-opslagaccount te](register-scan-adls-gen2.md)registreren en te scannen.