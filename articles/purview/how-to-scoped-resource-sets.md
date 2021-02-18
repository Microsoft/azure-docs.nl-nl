---
title: 'Procedure: een configuratie met een scoped resource set maken'
description: Meer informatie over het maken van een configuratie regel voor een scoped bron instellen om te overschrijven hoe assets worden gegroepeerd in resource sets
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 02/17/2021
ms.openlocfilehash: 517b07eecdbc63754f46fcf1051bf5b987dbc20e
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100654202"
---
# <a name="create-scoped-resource-set-configuration-rules"></a>Configuratie regels voor het bereik van de bron maken

Bij schaal bare gegevens verwerkings systemen wordt één tabel op een schijf opgeslagen als meerdere bestanden. Dit concept wordt weer gegeven in azure controle sfeer liggen met behulp van resource sets. Een resourceset is een enkel object in de gegevens catalogus dat een groot aantal assets in de opslag vertegenwoordigt. Zie [informatie over resource sets](concept-resource-sets.md)voor meer informatie.

Bij het scannen van een opslag account gebruikt Azure controle sfeer liggen een set gedefinieerde patronen om te bepalen of een groep activa een resourceset is. In sommige gevallen is het mogelijk dat de resourceset-groepering van Azure controle sfeer liggen uw data-erfgoed niet nauw keurig weergeeft. Met scoped resource set-regels kunt u de manier aanpassen of negeren hoe Azure controle sfeer liggen detecteert welke assets worden gegroepeerd als resource sets en hoe ze worden weer gegeven in de catalogus.

## <a name="how-to-create-a-scoped-resource-set-configuration"></a>Een configuratie van een resourceset met een bereik maken

Volg de onderstaande stappen om een nieuwe configuratie voor het configureren van een resource te maken:

1. Ga naar het beheer centrum. Selecteer **scoped resource sets** in het menu. Klik op **+ Nieuw** om een nieuwe set met configuratie regels te maken.
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-rule.png" alt-text="Nieuwe regel voor het instellen van een resourceset maken" border="true":::

1. Geef het bereik van de configuratie van het bereik van de resource groep op. Selecteer uw type opslag account en de naam van het opslag account waarvoor u een regelset wilt maken. Elke set regels wordt toegepast ten opzichte van een pad naar een map die is opgegeven in het veld **pad map** . 
        :::image type="content" source="media/how-to-scoped-resource-sets/create-new-scoped-resource-set-scope.png" alt-text="Nieuwe regel voor het instellen van een resourceset maken" border="true":::

1. Als u een regel voor een configuratie bereik wilt invoeren, selecteert u **+ nieuwe regel**.
1. Voer in de volgende velden in om een regel te maken:
    1. **Regel naam:** De naam van de configuratie regel. Dit veld heeft geen invloed op de activa waarop de regel van toepassing is.
    1. **Gekwalificeerde naam:** Een gekwalificeerde pad dat gebruikmaakt van een combi natie van tekst, dynamische vervangingen en statische vervangingen om activa te koppelen aan de configuratie regel. Dit pad is relatief ten opzichte van het bereik van de configuratie regel. Zie de sectie [syntaxis](#syntax) hieronder voor gedetailleerde instructies voor het opgeven van gekwalificeerde namen. 
    1. **Weergave naam:** De weergave naam van de Asset. Dit veld is optioneel. Gebruik onbewerkte tekst en statische vervangingen om te bepalen hoe een activum in de catalogus wordt weer gegeven. Zie de sectie [syntaxis](#syntax) hieronder voor meer gedetailleerde instructies.
    1. **Niet groeperen als resourceset:** Als deze functie is ingeschakeld, wordt overeenkomende resource niet gegroepeerd in een resourceset. 
        :::image type="content" source="media/how-to-scoped-resource-sets/scoped-resource-set-rule-example.png" alt-text="Nieuwe regel voor het instellen van een resourceset maken" border="true"::: 
1. Sla de regel op door op **toevoegen** te klikken. 

## <a name="scoped-resource-set-syntax"></a><a name="syntax"></a> Syntaxis van het bereik van de resourceset

Wanneer u regels voor het instellen van een resourceset maakt, gebruikt u de volgende syntaxis om op te geven welke assets regels van toepassing zijn op.

### <a name="static-replacers-single-brackets"></a>Statische vervangingen (enkele haken)

Enkele haken worden gebruikt als **dynamische vervangingen** in een regel voor het bereik van een resource. Geef een dynamische vervangings functie op in de gekwalificeerde naam met de indeling `{<replacerName:<replacerType>}` . Als er overeenkomsten worden gevonden, worden dynamische vervangingen gebruikt als een groeperings voorwaarde die aangeeft dat activa moeten worden weer gegeven als een resourceset. Als de activa zijn gegroepeerd in een resourceset, bevat het gekwalificeerde pad van de resource `{replacerName}` die de vervangings functie heeft opgegeven.

Als er bijvoorbeeld twee assets `folder1/file-1.csv` zijn en `folder2/file-2.csv` overeenkomen met regel `{folder:string}/file-{NUM:int}.csv` , zou de resourceset één entiteit zijn `{folder}/file-{NUM}.csv` .

#### <a name="special-case-dynamic-replacers-when-not-grouping-into-resource-set"></a>Speciaal geval: dynamische vervangingen wanneer ze niet worden gegroepeerd in een resourceset

Als de *optie niet groeperen als resourceset* is ingeschakeld voor een regel voor een resourceset, is de naam van de vervangings functie een optioneel veld. `{:<replacerType>}` is een geldige syntaxis. Bijvoorbeeld, `file-{:int}.csv` zou overeenkomen met `file-1.csv` en `file-2.csv` en het maken van twee verschillende assets in plaats van een resourceset.

### <a name="static-replacers-double-brackets"></a>Statische vervangingen (dubbele haken)

Dubbele haken worden gebruikt als **statische vervangingen** in de gekwalificeerde naam van een regel voor het instellen van een bereik. Geef een statische vervanger in de gekwalificeerde naam op met behulp van format `{{<replacerName>:<replacerType>}}` . Als er een overeenkomst wordt gevonden, worden met elke set unieke statische-vervangings waarden verschillende groepen voor het instellen van resources gemaakt.

Als er bijvoorbeeld twee assets `folder1/file-1.csv` zijn en `folder2/file-2.csv` overeenkomen met regel `{{folder:string}}/file-{NUM:int}.csv` , worden er twee resource sets gemaakt `folder1/file-{NUM}.csv` en `folder2/file-{NUM}.csv` .

Statische vervangingen kunnen worden gebruikt om de weergave naam op te geven van een Asset die overeenkomt met een regel voor het bereik van een resourceset. Als u `{{<replacerName>}}` in de weergave naam van een regel gebruikt, wordt de overeenkomende waarde gebruikt in de naam van de Asset.

### <a name="available-replacement-types"></a>Beschik bare vervangings typen

Hieronder vindt u de beschik bare typen die kunnen worden gebruikt in statische en dynamische vervangingen:

| Type | Structuur |
| ---- | --------- |
| tekenreeks | Een reeks van 1 of meer Unicode-tekens met scheidings teken zoals spaties. |
| int | Een reeks van 1 of meer 0-9 ASCII-tekens, de waarde kan 0 voor voegsel zijn (bijvoorbeeld 0001). |
| guid | Een reeks van 32 of 8-4-4-4-12 teken reeks representatie van een UUID als defineddefa in https://tools.ietf.org/html/rfc4122 |
| date | Een reeks van 6 of 8 0-9 ASCII-tekens met optioneel gescheiden: jjjmmdd, jjjj-mm-dd, JJMMDD, jj-mm-dd, opgegeven in https://tools.ietf.org/html/rfc3339 |
| tijd | Een reeks van 4 of 6 0-9 ASCII-tekens met optioneel gescheiden: HHmm, uu: mm, HHmmss, uu: mm: SS opgegeven in https://tools.ietf.org/html/rfc3339 |
| tijdstempel | Een reeks van 12 of 14 0-9 ASCII-tekens met optioneel gescheiden: jjjj-mm-ddTuu: mm, yyyymmddhhmm, jjjj-mm-ddTuu: mm: SS, jjjjmmdduummss opgegeven in https://tools.ietf.org/html/rfc3339 |
| booleaans | Kan ' waar ' of ' onwaar ' bevatten, niet hoofdletter gevoelig. |
| getal | Een reeks van 0 of meer 0-9 ASCII-tekens, de waarde mag 0 (bijvoorbeeld 0001) zijn, gevolgd door een punt. en een reeks van 1 of meer 0-9 ASCII-tekens, de waarde kan 0 postfixed zijn (bijvoorbeeld. 100) | 
| hex | Een reeks van 1 of meer ASCII-tekens uit de set 0-1 en A-F, de waarde kan 0 vooraf zijn. |
| landinstelling | Een teken reeks die overeenkomt met de syntaxis die is opgegeven in https://tools.ietf.org/html/rfc5646 |

## <a name="order-of-scoped-resource-set-rules-getting-applied"></a>Volg orde van de regels voor het bereik van de resourceset die worden toegepast.

Hieronder ziet u de volg orde van bewerkingen voor het Toep assen van regels voor het instellen van een resourceset:

1. Meer specifieke bereiken hebben prioriteit als een Asset overeenkomt met twee regels. Regels in een bereik `container/folder` worden bijvoorbeeld toegepast vóór regels in het bereik `container` . 
1. Volg orde van de regels binnen een bepaald bereik. Dit kan in de UX worden bewerkt.
1. Als een activum niet overeenkomt met een opgegeven regel, zijn de standaard heuristiek van de resourceset van toepassing.

## <a name="examples"></a>Voorbeelden

### <a name="example-1"></a>Voorbeeld 1

SAP-gegevens extractie in volledige en Delta belasting

*Invoerwaarden*

Logbestanden
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/13/saptable_customer_20200101_20200102_02.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/delta/2020/01/15/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_01.txt`
-   `https://myazureblob.blob.core.windows.net/bar/customer/full/2020/01/17/saptable_customer_20200101_20200102_02.txt`


*Regel voor het bereik van de Resourceset*

**Bereik:**https://myazureblob.blob.core.windows.net/bar/

**Weergave naam:** Externe klant

**Gekwalificeerde naam:**`customer/{extract:string}/{year:int}/{month:int}/{day:int}/saptable_customer_{date_from:date}_{date_to:time}_{sequence:int}.txt`

**Resource set:** waar

*Uitvoer*

Eén resource set-Asset

**Weergave naam:** Externe klant

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/customer/{extract}/{year}/{month}/{day}/saptable_customer_{date_from}_{date_to}_{sequence}.txt`

### <a name="example-2"></a>Voorbeeld 2

IoT-gegevens in de Avro-indeling

*Invoerwaarden*

Logbestanden
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Regels voor het bereik van bron sets*

**Bereik:**https://myazureblob.blob.core.windows.net/bar/

Regel 1

**Weergave naam:** ' machine-89 '

**Gekwalificeerde naam:**`raw/machinename-89/{date:date}/{time:time}-{id:int}.avro`

**Resource set:** waar

Regel 2

**Weergave naam:** ' machine-90 '

**Gekwalificeerde naam:**`raw/machinename-90/{date:date}/{time:time}-{id:int}.avro`

**Resource set: waar**

*Uitvoerwaarden*

2 resource sets 

Resource set 1

**Weergave naam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resource set 2

**Weergave naam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-3"></a>Voorbeeld 3

IoT-gegevens in de Avro-indeling

*Invoerwaarden*

Logbestanden
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.netbar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Regel voor het bereik van de Resourceset*

**Bereik:**https://myazureblob.blob.core.windows.net/bar/

**Weergave naam:** ' Machine-{{MachineID}} '

**Gekwalificeerde naam:**`raw/machinename-{{machineid:int}}/{date:date}/{time:time}-{id:int}.avro`

**Resource set:** waar

*Uitvoerwaarden*

Resource set 1

**Weergave naam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/{date}/{time}-{id}.avro`

Resource set 2

**Weergave naam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/{date}/{time}-{id}.avro`

### <a name="example-4"></a>Voorbeeld 4

Niet groeperen in resource sets

*Invoerwaarden*

Logbestanden
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`
-   `https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

*Regel voor het bereik van de Resourceset*

**Bereik:**https://myazureblob.blob.core.windows.net/bar/

**Weergave naam:** ' Machine-{{MachineID}} '

**Gekwalificeerde naam:**`raw/machinename-{{machineid:int}}/{{:date}}/{{:time}}-{{:int}}.avro`

**Resource set:** False

*Uitvoerwaarden*

4 afzonderlijke assets

Activum 1

**Weergave naam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-001.avro`

Activum 2

**Weergave naam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/01-01-2020/22:33:22-002.avro`

Activum 3

**Weergave naam:** machine-89

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-89/02-01-2020/22:33:22-001.avro`

Asset 4

**Weergave naam:** machine-90

**Gekwalificeerde naam:**`https://myazureblob.blob.core.windows.net/bar/raw/machinename-90/01-01-2020/22:33:22-001.avro`

## <a name="next-steps"></a>Volgende stappen

Ga aan de slag door [een Azure data Lake Gen2-opslag account te registreren en te scannen](register-scan-adls-gen2.md).