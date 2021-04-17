---
title: Informatie over resourcesets
description: In dit artikel wordt uitgelegd wat resourcesets zijn en hoe Azure Purview ze maakt.
author: djpmsft
ms.author: daperlov
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: conceptual
ms.date: 02/03/2021
ms.openlocfilehash: 330a6e54ee88781f71c4a861051aab94f8eef81f
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107587898"
---
# <a name="understanding-resource-sets"></a>Informatie over resourcesets

In dit artikel wordt beschreven hoe Azure Purview gebruikmaakt van resourcesets om gegevensactiva toe te wijsen aan logische resources.
## <a name="background-info"></a>Achtergrondinformatie

Systemen voor gegevensverwerking op schaal slaan doorgaans één tabel op een schijf op als meerdere bestanden. Dit concept wordt weergegeven in Azure Purview met behulp van resourcesets. Een resourceset is één object in de catalogus dat een groot aantal assets in de opslag vertegenwoordigt.

Stel bijvoorbeeld dat uw Spark-cluster een DataFrame heeft persistent gemaakt in een Azure Data Lake Storage (ADLS) Gen2-gegevensbron. Hoewel de tabel in Spark lijkt op één logische resource, zijn er op de schijf waarschijnlijk duizenden Parquet-bestanden, die elk een partitie van de totale inhoud van het DataFrame vertegenwoordigen. IoT-gegevens en weblogboekgegevens hebben dezelfde uitdaging. Stel dat u een sensor hebt die meerdere keren per seconde logboekbestanden uitvoer. Het duurt niet lang voordat u honderdduizenden logboekbestanden van die ene sensor hebt.

Azure Purview maakt gebruik van resourcesets om de uitdaging van het toewijzen van grote aantallen gegevensactiva aan één logische resource aan te pakken.

## <a name="how-azure-purview-detects-resource-sets"></a>Hoe Azure Purview resourcesets detecteert

Azure Purview ondersteunt het detecteren van resourcesets in Azure Blob Storage, ADLS Gen1 en ADLS Gen2.

Azure Purview detecteert automatisch resourcesets bij het scannen. Deze functie bekijkt alle gegevens die worden opgenomen via scannen en vergelijkt deze met een set gedefinieerde patronen.

Stel bijvoorbeeld dat u een gegevensbron scant waarvan de URL `https://myaccount.blob.core.windows.net/mycontainer/machinesets/23/foo.parquet` is. Azure Purview bekijkt de padsegmenten en bepaalt of deze overeenkomen met ingebouwde patronen. Het heeft ingebouwde patronen voor GUID's, getallen, datumindelingen, lokalisatiecodes (bijvoorbeeld en-us), en meer. In dit geval komt het getalpatroon overeen *met 23*. Azure Purview gaat ervan uit dat dit bestand deel uitmaakt van een resourceset met de naam `https://myaccount.blob.core.windows.net/mycontainer/machinesets/{N}/foo.parquet` .

Of voor een URL zoals komt Azure Purview overeen met zowel het lokalisatiepatroon als het getalpatroon, wat een resourceset met de `https://myaccount.blob.core.windows.net/mycontainer/weblogs/en_au/23.json` naam `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` produceert.

Met behulp van deze strategie worden in Azure Purview de volgende resources aan dezelfde resourceset, `https://myaccount.blob.core.windows.net/mycontainer/weblogs/{LOC}/{N}.json` :

- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/1004.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/cy_gb/234.json`
- `https://myaccount.blob.core.windows.net/mycontainer/weblogs/de_Ch/23434.json`

## <a name="file-types-that-azure-purview-will-not-detect-as-resource-sets"></a>Bestandstypen die azure Purview niet detecteert als resourcesets

Met Purview wordt niet geprobeerd de meeste documentbestandstypen, zoals Word, Excel of PDF, te classificeren als resourcesets. De uitzondering hierop is de CSV-indeling, omdat dit een algemene gepartitiesteerde bestandsindeling is.

## <a name="how-azure-purview-scans-resource-sets"></a>Hoe Azure Purview resourcesets scant

Wanneer Azure Purview resources detecteert die deel uitmaken van een resourceset, wordt overschakelt van een volledige scan naar een voorbeeldscan. In een voorbeeldscan wordt alleen een subset geopend van de bestanden die in de resourceset worden denkt te staan. Voor elk bestand dat wordt geopend, wordt het schema gebruikt en worden de classificaties uitgevoerd. Azure Purview zoekt vervolgens de nieuwste resource tussen de geopende resources en gebruikt het schema en de classificaties van die resource in de vermelding voor de hele resourceset in de catalogus.

## <a name="what-azure-purview-stores-about-resource-sets"></a>Wat Azure Purview op slaat over resourcesets

Naast één schema en classificaties slaat Azure Purview de volgende informatie over resourcesets op:

- Gegevens van de meest recente partitieresource zijn diep gescand.
- Aggregatie-informatie over de partitieresources waar de resourceset uit is samengesteld.
- Een aantal partities dat laat zien hoeveel partitiebronnen er zijn gevonden.
- Een aantal schema's dat laat zien hoeveel unieke schema's er zijn gevonden in de voorbeeldset waarop het diepe scans heeft uitgevoerd. Deze waarde is een getal tussen 1 en 5 of voor waarden groter dan 5, 5+.
- Een lijst met partitietypen wanneer meer dan één partitietype is opgenomen in de resourceset. Een IoT-sensor kan bijvoorbeeld zowel XML- als JSON-bestanden als uitvoer geven, hoewel beide logisch deel uitmaken van dezelfde resourceset.

## <a name="built-in-resource-set-patterns"></a>Ingebouwde resourcesetpatronen

Azure Purview ondersteunt de volgende resourcesetpatronen. Deze patronen kunnen worden weergegeven als een naam in een map of als onderdeel van een bestandsnaam.
### <a name="regex-based-patterns"></a>Op regex gebaseerde patronen

| Patroonnaam | Weergavenaam | Beschrijving |
|--------------|--------------|-------------|
| Guid         | {GUID}       | Een wereldwijd unieke id zoals gedefinieerd in [RFC 4122](https://tools.ietf.org/html/rfc4122) |
| Aantal       | {N}          | Een of meer cijfers |
| Datum-/tijdnotaaties | {Jaar} {Maand} {Day} {N}     | We ondersteunen verschillende datum/tijd-indelingen, maar alle indelingen worden weergegeven met {Year}[scheidingsteken]{Maand}[scheidingsteken]{Dag} of reeks {N}s. |
| 4byteHex     | {HEX}        | Een HEX-nummer van 4 cijfers. |
| Lokalisatie | {LOC}        | Een taaltag zoals gedefinieerd in [BCP 47,](https://tools.ietf.org/html/bcp47)worden zowel - als _-namen ondersteund (bijvoorbeeld en_ca en en-ca) |

### <a name="complex-patterns"></a>Complexe patronen

| Patroonnaam | Weergavenaam | Beschrijving |
|--------------|--------------|-------------|
| SparkPath    | {SparkPartitions} | Spark-partitiebestands-id |
| Date(yyyy/mm/dd)InPath  | {Year}/{Month}/{Day} | Patroon jaar/maand/dag dat meerdere mappen beslaat |


## <a name="how-resource-sets-are-displayed-in-the-azure-purview-catalog"></a>Hoe resourcesets worden weergegeven in de Azure Purview-catalogus

Wanneer Azure Purview een groep assets in een resourceset matcht, wordt geprobeerd de nuttigste informatie te extraheren die als weergavenaam in de catalogus moet worden gebruikt. Enkele voorbeelden van de standaardnaamconventie die is toegepast: 

### <a name="example-1"></a>Voorbeeld 1

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/sample-data/name-of-spark-output/{SparkPartitions}`

Weergavenaam: 'naam van spark-uitvoer'

### <a name="example-2"></a>Voorbeeld 2

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/my-partitioned-data/{Year}-{Month}-{Day}/{N}-{N}-{N}-{N}/{GUID}`

Weergavenaam: 'mijn gepart partitioneerde gegevens'

### <a name="example-3"></a>Voorbeeld 3

Gekwalificeerde naam: `https://myblob.blob.core.windows.net/sample-data/data{N}.csv`

Weergavenaam: 'gegevens'

## <a name="customizing-resource-set-grouping-using-pattern-rules"></a>Groeperen van resourcesets aanpassen met behulp van patroonregels

hen die een opslagaccount scannen. Azure Purview gebruikt een set gedefinieerde patronen om te bepalen of een groep assets een resourceset is. In sommige gevallen weerspiegelt de groepering van resourcesets van Azure Purview mogelijk niet nauwkeurig uw gegevens. Deze problemen kunnen zijn:

- Een asset onjuist markeren als een resourceset
- Een asset in de verkeerde resourceset plaatsen
- Een asset ten onrechte markeren als een resourceset

Als u wilt aanpassen of overschrijven hoe Azure Purview detecteert welke assets zijn gegroepeerd als resourcesets en hoe deze worden weergegeven in de catalogus, kunt u patroonregels definiëren in het beheercentrum. Zie Regels voor resourcesetpatronen voor stapsgewijs instructies en [syntaxis.](how-to-resource-set-pattern-rules.md)
## <a name="next-steps"></a>Volgende stappen

Zie [Quickstart: Een Azure Purview-account](create-catalog-portal.md)maken om aan de slag te gaan met Azure Purview.
