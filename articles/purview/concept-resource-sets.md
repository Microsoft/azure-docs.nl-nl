---
title: Informatie over resourcesets
description: In dit artikel wordt uitgelegd wat resource sets zijn en hoe Azure controle sfeer liggen ze maakt.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: cbf070dce056795ad8e4a5f3e4d609e7d36d631e
ms.sourcegitcommit: 5f32f03eeb892bf0d023b23bd709e642d1812696
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/12/2021
ms.locfileid: "103200818"
---
# <a name="understanding-resource-sets"></a>Informatie over resourcesets

Dit artikel helpt u te begrijpen hoe Azure controle sfeer liggen gebruikmaakt van resource sets om gegevensassets toe te wijzen aan logische resources.
## <a name="background-info"></a>Achtergrond informatie

Bij schaal bare gegevens verwerkings systemen wordt één tabel op een schijf opgeslagen als meerdere bestanden. Dit concept wordt weer gegeven in azure controle sfeer liggen met behulp van resource sets. Een resourceset is een enkel object in de catalogus dat een groot aantal assets in de opslag vertegenwoordigt.

Stel bijvoorbeeld dat uw Spark-cluster een data frame heeft opgeslagen in Azure Data Lake Storage een Gen2-gegevens bron (ADLS). Hoewel de tabel er in Spark uitziet als een enkele logische resource, is er op de schijf waarschijnlijk duizenden Parquet-bestanden, die elk een partitie van de totale inhoud van de data frame vertegenwoordigen. IoT-gegevens en Web-logboek gegevens hebben dezelfde uitdaging. Stel dat u een sensor hebt die logboek bestanden een aantal malen per seconde uitvoert. Het duurt niet lang voordat u honderd duizenden logboek bestanden van die ene sensor hebt.

Azure controle sfeer liggen maakt gebruik van resource sets om de uitdaging van het toewijzen van een groot aantal gegevensassets aan één logische resource te verhelpen.

## <a name="how-azure-purview-detects-resource-sets"></a>Hoe Azure controle sfeer liggen bron sets detecteert

Azure controle sfeer liggen ondersteunt het detecteren van resource sets in Azure Blob Storage, ADLS Gen1 en ADLS Gen2.

Azure controle sfeer liggen detecteert automatisch resource sets tijdens het scannen. Deze functie bekijkt alle gegevens die worden opgenomen via scannen en vergelijkt deze met een set gedefinieerde patronen.

Stel bijvoorbeeld dat u een gegevens bron scant waarvan de URL is `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` . Azure controle sfeer liggen kijkt naar de padsegmenten en bepaalt of ze overeenkomen met ingebouwde patronen. Het heeft ingebouwde patronen voor GUID'S, cijfers, datum notaties, lokalisatie codes (bijvoorbeeld en-US), enzovoort. In dit geval komt het numerieke patroon overeen met *23*. In azure controle sfeer liggen wordt ervan uitgegaan dat dit bestand deel uitmaakt van een resourceset met de naam `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Of voor een URL zoals `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` Azure controle sfeer liggen overeenkomt met het lokalisatie patroon en het cijfer patroon, waarbij een resourceset wordt geproduceerd met de naam `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` .

Met deze strategie worden de volgende resources in azure controle sfeer liggen toegewezen aan dezelfde resource set `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Bestands typen die door Azure controle sfeer liggen niet worden gedetecteerd als resource sets

Controle sfeer liggen probeert de meeste document bestands typen als Word-, Excel-of PDF-bron sets niet te classificeren. De uitzonde ring is CSV-indeling omdat dit een gemeen schappelijke bestands indeling is.

## <a name="how-azure-purview-scans-resource-sets"></a>Hoe Azure controle sfeer liggen resource sets scant

Wanneer Azure controle sfeer liggen resources detecteert die IT denkt deel uitmaakt van een resourceset, wordt overgeschakeld van een volledige scan naar een voorbeeld scan. In een voorbeeld scan wordt er slechts een subset geopend van de bestanden die worden beschouwd in de resourceset. Voor elk bestand dat wordt geopend, wordt het bijbehorende schema gebruikt en worden de classificaties uitgevoerd. Azure controle sfeer liggen vindt vervolgens de nieuwste resource tussen de geopende resources en maakt gebruik van het schema en de classificaties van die resource in de vermelding voor de gehele resourceset in de catalogus.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Wat Azure controle sfeer liggen opslaat over resource sets

Naast één schema en classificaties bevat Azure controle sfeer liggen de volgende informatie over resource sets:

- Gegevens van de meest recente partitie resource die wordt gecontroleerd.
- Statistische informatie over de partitie resources waaruit de resourceset is samengesteld.
- Een aantal partities dat laat zien hoeveel partitie bronnen er zijn gevonden.
- Een aantal schema's dat laat zien hoeveel unieke schema's er in het voor beeld zijn gevonden. Deze waarde is een getal tussen 1 – 5 of voor waarden groter dan 5, 5 +.
- Een lijst met partitie typen wanneer meer dan één partitie type is opgenomen in de resourceset. Zo kan een IoT-sensor zowel XML-als JSON-bestanden uitvoeren, hoewel beide logisch deel uitmaken van dezelfde resource set.

## <a name="built-in-resource-set-patterns"></a>Ingebouwde patronen voor de vaste resource

Azure controle sfeer liggen ondersteunt de volgende resource set-patronen. Deze patronen kunnen worden weer gegeven als een naam in een map of als onderdeel van een bestands naam.
### <a name="regex-based-patterns"></a>Op regex gebaseerde patronen

| Patroon naam | Weergavenaam | Beschrijving |
|--------------|--------------|-------------|
| Guid         | GPT       | Een Globally Unique Identifier zoals gedefinieerd in [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Aantal       | Nvt          | Een of meer cijfers |
| Datum-/tijdnotatie | Jaareinde Blijft Profieldag Nvt     | Er worden verschillende datum-en tijd notaties ondersteund, maar alle worden weer gegeven met {Year} [scheidings teken] {month} [scheidings teken] {Day} of een reeks van {N} s. |
| 4ByteHex     | BOVENAANZICHT        | Een HEXADECIMAAL getal van vier cijfers. |
| Lokalisatie | Loc        | Een taal code zoals gedefinieerd in [BCP 47](https://tools.ietf.org/html/bcp47), zowel-als _-namen worden ondersteund (bijvoorbeeld en_ca en en en CA) |

### <a name="complex-patterns"></a>Complexe patronen

| Patroon naam | Weergavenaam | Beschrijving |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Bestands-id van Spark-partitie |
| Pad naar datum (jjjj/mm/dd)  | {Year}/{Month}/{Day} | Patroon van jaar/maand/dag dat meerdere mappen omspannen |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Hoe resource sets worden weer gegeven in de Azure controle sfeer liggen-catalogus

Wanneer Azure controle sfeer liggen overeenkomt met een groep assets in een resourceset, wordt geprobeerd de meest nuttige informatie uit te pakken om als weergave naam in de catalogus te gebruiken. Enkele voor beelden van de standaard naamgevings Conventie wordt toegepast: 

### <a name="example-1"></a>Voorbeeld 1

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Weergave naam: ' naam van Spark-uitvoer '

### <a name="example-2"></a>Voorbeeld 2

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Weergave naam: mijn gepartitioneerde gegevens

### <a name="example-3"></a>Voorbeeld 3

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Weergave naam: "gegevens"

## <a name="known-issues-with-resource-sets"></a>Bekende problemen met resource sets

Hoewel resource sets goed werken in de meeste gevallen, kunnen de volgende problemen optreden, waarin Azure controle sfeer liggen:

- Markeert onterecht een activum als een resourceset
- Hiermee wordt een activum in de verkeerde resourceset geplaatst
- Markeert onterecht een activum als niet een resourceset

## <a name="next-steps"></a>Volgende stappen

Zie [Quick Start: een Azure controle sfeer liggen-account maken](create-catalog-portal.md)om aan de slag te gaan met Azure controle sfeer liggen.
