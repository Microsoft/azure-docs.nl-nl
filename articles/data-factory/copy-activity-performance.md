---
title: Handleiding voor prestaties en schaalbaarheid van kopieeractiviteit
description: Meer informatie over de belangrijkste factoren die van invloed zijn op de prestaties van het verplaatsen van gegevens in Azure Data Factory wanneer u de Kopieer activiteit gebruikt.
services: data-factory
documentationcenter: ''
ms.author: jingwang
author: linda33wj
manager: shwang
ms.reviewer: douglasl
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 09/15/2020
ms.openlocfilehash: cba248d3f254c9bb97c66ff7a3d39275b4b912c4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102616074"
---
# <a name="copy-activity-performance-and-scalability-guide"></a>Handleiding voor prestaties en schaalbaarheid van kopieeractiviteit

> [!div class="op_single_selector" title1="Selecteer de versie van Azure Data Factory die u gebruikt:"]
> * [Versie 1:](v1/data-factory-copy-activity-performance.md)
> * [Huidige versie](copy-activity-performance.md)

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Soms wilt u een grootschalige gegevens migratie uitvoeren vanuit data Lake of ENTER prise Data Warehouse (EDW) naar Azure. Andere keren dat u grote hoeveel heden gegevens wilt opnemen van verschillende bronnen in azure, voor big data Analytics. In elk geval is het essentieel om optimale prestaties en schaal baarheid te garanderen.

Azure Data Factory (ADF) biedt een mechanisme voor het opnemen van gegevens. ADF heeft de volgende voor delen:

* Hiermee worden grote hoeveel heden gegevens verwerkt
* Is zeer goed uitgevoerd
* Is rendabel

Deze voor delen maken ADF uitstekend geschikt voor gegevens technici die zeer schaal bare gegevens opname pijplijnen willen bouwen.

Na het lezen van dit artikel kunt u de volgende vragen beantwoorden:

* Welk prestatie-en schaal niveau kan ik gebruiken voor het gebruik van ADF Copy-activiteiten voor gegevens migratie en gegevens opname scenario's?
* Welke stappen moet ik ondernemen om de prestaties van de ADF-Kopieer activiteit te optimaliseren?
* Wat kan ik doen met de methoden voor het optimaliseren van ADF? de prestaties voor een enkele Kopieer activiteit worden uitgevoerd?
* Wat andere factoren buiten ADF zijn om rekening mee te houden wanneer u de Kopieer prestaties optimaliseert?

> [!NOTE]
> Als u niet bekend bent met de Kopieer activiteit in het algemeen, raadpleegt u het overzicht van de [Kopieer activiteit](copy-activity-overview.md) voordat u dit artikel leest.

## <a name="copy-performance-and-scalability-achievable-using-adf"></a>Kopieer prestaties en schaal baarheid die kan worden behaald met behulp van ADF

ADF biedt een serverloze architectuur die parallellisme op verschillende niveaus mogelijk maakt.

Met deze architectuur kunt u pijp lijnen ontwikkelen die de door Voer van gegevens verplaatsing voor uw omgeving maximaliseren. Deze pijp lijnen maken volledig gebruik van de volgende resources:

* Netwerk bandbreedte tussen de bron-en doel gegevens archieven
* Invoer/uitvoer-bewerkingen per seconde (IOPS) en band breedte van bron-of doel gegevens opslag

Dit volledige gebruik houdt in dat u de algehele door Voer kunt schatten door de minimale door Voer die beschikbaar is met de volgende resources te meten:

* Brongegevens archief
* Doelgegevensarchief
* Netwerk bandbreedte tussen de bron-en doel gegevens archieven

De onderstaande tabel berekent de duur van de kopie. De duur is gebaseerd op de gegevens grootte en de bandbreedte limiet voor netwerk/gegevens opslag voor uw omgeving.

&nbsp;

| Gegevens grootte/ <br/> BAP | 50 Mbps    | 100 Mbps  | 500 Mbps  | 1 Gbps   | 5 Gbps   | 10 Gbps  | 50 Gbps   |
| --------------------------- | ---------- | --------- | --------- | -------- | -------- | -------- | --------- |
| **1 GB**                    | 2,7 minuten    | 1,4 minuten   | 0,3 minuten   | 0,1 minuten  | 0,03 minuten | 0,01 minuten | 0,0 minuten   |
| **10 GB**                   | 27,3 minuten   | 13,7 minuten  | 2,7 minuten   | 1,3 minuten  | 0,3 minuten  | 0,1 minuten  | 0,03 minuten  |
| **100 GB**                  | 4,6 uur    | 2,3 uur   | 0,5 uur   | 0,2 uur  | 0,05 uur | 0,02 uur | 0,0 uur   |
| **1 TB**                    | 46,6 uur   | 23,3 uur  | 4,7 uur   | 2,3 uur  | 0,5 uur  | 0,2 uur  | 0,05 uur  |
| **10 TB**                   | 19,4 dagen  | 9,7 dagen  | 1,9 dagen  | 0,9 dagen | 0,2 dagen | 0,1 dagen | 0,02 dagen |
| **100 TB**                  | 194,2 dagen | 97,1 dagen | 19,4 dagen | 9,7 dagen | 1,9 dagen | 1 dag    | 0,2 dagen  |
| **1 PB**                    | 64,7 mo    | 32,4 mo   | 6,5 mo    | 3,2 mo   | 0,6 mo   | 0,3 mo   | 0,06 mo   |
| **10 PB**                   | 647,3 mo   | 323,6 mo  | 64,7 mo   | 31,6 mo  | 6,5 mo   | 3,2 mo   | 0,6 mo    |
| | |  | | |  | | |

ADF-kopie is schaalbaar op verschillende niveaus:

![de schaal van ADF kopiëren](media/copy-activity-performance/adf-copy-scalability.png)

* De controle stroom van ADF kan meerdere Kopieer activiteiten parallel starten, bijvoorbeeld [voor elke lus](control-flow-for-each-activity.md).

* Eén Kopieer activiteit kan profiteren van schaal bare reken resources.
  * Wanneer u Azure Integration runtime (IR) gebruikt, kunt u [Maxi maal 256 gegevens integratie-eenheden (DIUs)](#data-integration-units) voor elke Kopieer activiteit opgeven, op serverloze wijze.
  * Wanneer u zelf-hostende IR gebruikt, kunt u een van de volgende benaderingen nemen:
    * De machine hand matig omhoog schalen.
    * Uitschalen naar meerdere machines ([Maxi maal 4 knoop punten](create-self-hosted-integration-runtime.md#high-availability-and-scalability)) en met één Kopieer activiteit wordt de bestands set gepartitioneerd op alle knoop punten.

* Eén Kopieer activiteit leest van en schrijft naar het gegevens archief met behulp van meerdere threads [parallel](#parallel-copy).

## <a name="performance-tuning-steps"></a>Stappen voor het afstemmen van prestaties

Voer de volgende stappen uit om de prestaties van uw Azure Data Factory-service af te stemmen met de Kopieer activiteit:

1. **Haal een test gegevensset op en stel een basis lijn in.**

    Test uw pijp lijn tijdens het ontwikkelen met behulp van de Kopieer activiteit op basis van een representatief gegevens voorbeeld. De gegevensset die u kiest, moet uw typische gegevens patronen vertegenwoordigen naast de volgende kenmerken:

    * Mapstructuur
    * Bestands patroon
    * Gegevens schema

    En uw gegevensset moet groot genoeg zijn om de Kopieer prestaties te kunnen evalueren. Een goede grootte duurt ten minste tien minuten voordat de Kopieer activiteit is voltooid. Verzamel Details van de uitvoerings gegevens en prestatie kenmerken na het controleren van de [activiteit](copy-activity-monitoring.md).

2. **De prestaties van één Kopieer activiteit maximaliseren**:

    U wordt aangeraden eerst de prestaties te maximaliseren met één Kopieer activiteit.

    * **Als de Kopieer activiteit wordt uitgevoerd in een _Azure_ Integration runtime:**

        Begin met standaard waarden voor de instellingen voor [gegevens integratie-eenheden (DIU)](#data-integration-units) en [parallelle kopieën](#parallel-copy) .

    * **Als de Kopieer activiteit wordt uitgevoerd op een _zelf-hostende_ Integration runtime:**

        U wordt aangeraden een specifieke computer te gebruiken om IR te hosten. De machine moet worden gescheiden van de server die als host fungeert voor het gegevens archief. Begin met de standaard waarden voor de instelling voor [parallelle kopieën](#parallel-copy) en gebruik één knoop punt voor de zelf-hostende IR.

    Voer een prestatie test uit. Bekijk de prestaties die zijn behaald. De werkelijke waarden die worden gebruikt, zoals DIUs en parallelle kopieën, bevatten. Raadpleeg de [controle activiteit kopiëren](copy-activity-monitoring.md) voor het verzamelen van resultaten en prestatie-instellingen die worden gebruikt. Meer informatie over het oplossen van problemen met de [Kopieer activiteit](copy-activity-performance-troubleshooting.md) om het knel punt op te sporen en op te lossen.

    Herhaal de instructies voor het uitvoeren van extra prestatie tests die worden uitgevoerd na de richt lijnen voor probleem oplossing en afstemming. Wanneer de uitvoering van de single copy-activiteit geen betere door Voer kan bieden, kunt u overwegen of u de geaggregeerde door Voer gelijktijdig wilt maximaliseren door meerdere exemplaren tegelijk uit te voeren. Deze optie wordt in het volgende genummerde opsommings teken besproken.

3. **De geaggregeerde door Voer maximaliseren door meerdere exemplaren gelijktijdig uit te voeren:**

    Nu hebt u de prestaties van één Kopieer activiteit gemaximaliseerd. Als u de bovengrens voor door Voer van uw omgeving nog niet hebt bereikt, kunt u meerdere Kopieer activiteiten parallel uitvoeren. U kunt parallel uitvoeren met behulp van de schema constructies van de ADF-besturings stroom. Een van deze constructs is de [voor elke lus](control-flow-for-each-activity.md). Raadpleeg de volgende artikelen over oplossings sjablonen voor meer informatie:

    * [Bestanden kopiëren vanuit meerdere containers](solution-template-copy-files-multiple-containers.md)
    * [Gegevens migreren van Amazon S3 naar ADLS Gen2](solution-template-migration-s3-azure.md)
    * [Bulksgewijs kopiëren met een controle tabel](solution-template-bulk-copy-with-control-table.md)

4. **Breid de configuratie uit naar uw volledige gegevensset.**

    Wanneer u tevreden bent met de resultaten en prestaties van de uitvoering, kunt u de definitie en pijp lijn uitvouwen om uw hele gegevensset te bedekken.

## <a name="troubleshoot-copy-activity-performance"></a>Prestaties van de Kopieer activiteit oplossen

Volg de [stappen voor het afstemmen](#performance-tuning-steps) van de prestaties om de prestatie test voor uw scenario te plannen en uit te voeren. Meer informatie over het oplossen van problemen met het prestatie probleem bij het uitvoeren van een Kopieer activiteit in Azure Data Factory van het [oplossen van problemen met de prestaties van Kopieer activiteiten](copy-activity-performance-troubleshooting.md).

## <a name="copy-performance-optimization-features"></a>Functies voor het optimaliseren van prestaties kopiëren

Azure Data Factory biedt de volgende functies voor prestatie optimalisatie:

* [Eenheden voor gegevensintegratie](#data-integration-units)
* [Schaal baarheid van zelf-hostende Integration runtime](#self-hosted-integration-runtime-scalability)
* [Parallelle kopie](#parallel-copy)
* [Gefaseerde kopie](#staged-copy)

### <a name="data-integration-units"></a>Eenheden voor gegevensintegratie

Een DIU (Data Integration Unit) is een meting die de kracht vormt van een enkele eenheid in Azure Data Factory. Kracht is een combi natie van CPU, geheugen en netwerk bron toewijzing. DIU is alleen van toepassing op [Azure Integration runtime](concepts-integration-runtime.md#azure-integration-runtime). DIU is niet van toepassing op [zelf-hostende Integration runtime](concepts-integration-runtime.md#self-hosted-integration-runtime). [Klik hier voor meer informatie](copy-activity-performance-features.md#data-integration-units).

### <a name="self-hosted-integration-runtime-scalability"></a>Schaal baarheid van zelf-hostende Integration runtime

Mogelijk wilt u een groeiende gelijktijdige werk belasting hosten. Het is ook mogelijk dat u de prestaties van uw huidige workload-niveau wilt verhogen. U kunt de schaal van de verwerking verbeteren met behulp van de volgende methoden:

* U kunt de zelf-hostende IR _opschalen door het aantal_ [gelijktijdige taken](create-self-hosted-integration-runtime.md#scale-up) dat kan worden uitgevoerd op een knoop punt te verhogen.  
Omhoog schalen werkt alleen als de processor en het geheugen van het knoop punt minder zijn dan volledig worden gebruikt.
* U _kunt de zelf_ -HOSTende IR uitschalen door meer knoop punten (machines) toe te voegen.

Zie voor meer informatie:

* [Functies voor het optimaliseren van de prestaties van de Kopieer activiteit: zelf-hostende Integration runtime-schaal baarheid](copy-activity-performance-features.md#self-hosted-integration-runtime-scalability)
* [Een zelf-hostende Integration runtime maken en configureren: schaal overwegingen](create-self-hosted-integration-runtime.md#scale-considerations)

### <a name="parallel-copy"></a>Parallelle kopie

U kunt de `parallelCopies` eigenschap instellen om aan te geven in welke parallellisme u de Kopieer activiteit wilt gebruiken. U beschouwt deze eigenschap als het maximum aantal threads in de Kopieer activiteit. De threads worden parallel gebruikt. De threads worden gelezen uit uw bron of geschreven naar uw Sink-gegevens opslag. [Meer informatie](copy-activity-performance-features.md#parallel-copy).

### <a name="staged-copy"></a>Gefaseerde kopie

Een bewerking voor het kopiëren van gegevens kan de gegevens _rechtstreeks_ naar de Sink-gegevens opslag verzenden. U kunt Blob Storage ook gebruiken als een _tijdelijke faserings_ opslag. [Meer informatie](copy-activity-performance-features.md#staged-copy).

## <a name="next-steps"></a>Volgende stappen

Zie de andere artikelen over Kopieer activiteiten:

* [Overzicht van de Kopieer activiteit](copy-activity-overview.md)
* [Prestaties van de Kopieer activiteit oplossen](copy-activity-performance-troubleshooting.md)
* [Functies voor het optimaliseren van de activiteit prestaties](copy-activity-performance-features.md)
* [Azure Data Factory gebruiken om gegevens van uw data Lake of Data Warehouse te migreren naar Azure](data-migration-guidance-overview.md)
* [Gegevens migreren van Amazon S3 naar Azure Storage](data-migration-guidance-s3-azure-storage.md)
