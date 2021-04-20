---
title: Overzicht van Storage - Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over gegevensopslag in Azure Time Series Insights Gen2.
author: deepakpalled
ms.author: dpalled
manager: diviso
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: cd26df1de86ee4bdb33050d0bc4769663707733e
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725022"
---
# <a name="data-storage"></a>Gegevensopslag

In dit artikel wordt gegevensopslag in Azure Time Series Insights Gen2 beschreven. Het bevat informatie over warm en koud, beschikbaarheid van gegevens en best practices.

## <a name="provisioning"></a>Inrichten

Wanneer u een Azure Time Series Insights Gen2-omgeving maakt, hebt u de volgende opties:

* Koude gegevensopslag:
  * Maak een nieuwe Azure Storage resource in het abonnement en de regio die u hebt gekozen voor uw omgeving.
  * Voeg een bestaand Azure Storage toe. Deze optie is alleen beschikbaar door te implementeren vanuit een Azure Resource Manager [sjabloon](/azure/templates/microsoft.timeseriesinsights/allversions)en is niet zichtbaar in de Azure Portal.
* Warme gegevensopslag:
  * Een warme opslag is optioneel en kan tijdens of na het inrichten worden ingeschakeld of uitgeschakeld. Als u besluit om warme opslag op een later tijdstip in [](concepts-storage.md#warm-store-behavior) teschakelen en er al gegevens in uw koude opslag staan, bekijkt u deze sectie hieronder om inzicht te krijgen in het verwachte gedrag. De gegevensretentietijd van de warme opslag kan worden geconfigureerd voor 7 tot 31 dagen en dit kan ook naar behoefte worden aangepast.

Wanneer een gebeurtenis wordt opgenomen, wordt deze geïndexeerd in zowel warme opslag (indien ingeschakeld) als koude opslag.

[![Overzicht van Opslag](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Als eigenaar van het Azure Blob Storage-account waarin koude opslaggegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat schrijf- en verwijdermachtigingen. Bewerk of verwijder de gegevens die niet worden Azure Time Series Insights Gen2-schrijfbewerkingen, omdat dit gegevensverlies kan veroorzaken.

## <a name="data-availability"></a>Beschikbaarheid van gegevens

Azure Time Series Insights Gen2 partities en indexeert gegevens voor optimale queryprestaties. Gegevens worden beschikbaar voor het uitvoeren van query's vanuit zowel warm (indien ingeschakeld) als koude opslag nadat deze zijn geïndexeerd. De hoeveelheid gegevens die wordt opgenomen en de doorvoersnelheid per partitie kan van invloed zijn op de beschikbaarheid. Bekijk de beperkingen en best [practices](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) [voor doorvoer van gebeurtenisbron](./concepts-streaming-ingress-throughput-limits.md) voor de beste prestaties. U kunt ook een vertragingswaarschuwing [configureren om](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) een melding te ontvangen als uw omgeving problemen ondervindt met het verwerken van gegevens.

> [!IMPORTANT]
> U kunt een periode van maximaal 60 seconden ervaren voordat gegevens beschikbaar komen. Als u een aanzienlijke latentie van meer dan 60 seconden ervaart, dient u een ondersteuningsticket in via de Azure Portal.

## <a name="warm-store"></a>Warme winkel

Gegevens in uw warme opslag zijn alleen beschikbaar via de [Time Series Query-API's,](./concepts-query-overview.md)de [Azure Time Series Insights TSI Explorer](./concepts-ux-panels.md)of de Power BI [Connector](./how-to-connect-power-bi.md). Warme-winkelquery's zijn gratis en er is geen quotum, maar er is een limiet [van 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) gelijktijdige aanvragen.

### <a name="warm-store-behavior"></a>Warm winkelgedrag

* Wanneer deze functie is ingeschakeld, worden alle gegevens die naar uw omgeving worden gestreamd, gerouteerd naar uw warme opslag, ongeacht de tijdstempel van de gebeurtenis. Houd er rekening mee dat de pijplijn voor streaming-opname is gebouwd voor bijna realtime streaming en het opnemen van historische gebeurtenissen [niet wordt ondersteund.](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion)
* De bewaarperiode wordt berekend op basis van het moment waarop de gebeurtenis is geïndexeerd in een warme opslag, niet de tijdstempel van de gebeurtenis. Dit betekent dat gegevens niet meer beschikbaar zijn in warme opslag nadat de bewaarperiode is verstreken, zelfs niet als de tijdstempel van de gebeurtenis voor de toekomst is.
  * Voorbeeld: een gebeurtenis met 10-daagse weersvoorspellingen wordt opgenomen en geïndexeerd in een warme-opslagcontainer die is geconfigureerd met een bewaarperiode van 7 dagen. Na zeven dagen is de voorspelling niet meer toegankelijk in een warme opslag, maar kan er een query worden gedaan bij koude gegevens.
* Als u warme opslag in een bestaande omgeving inschakelen waarin al recente gegevens zijn geïndexeerd in koude opslag, moet u er rekening mee houden dat uw warme opslag niet wordt gevuld met deze gegevens.
* Als u warme opslag zojuist hebt ingeschakeld en problemen ondervindt met het weergeven van uw recente gegevens in de Verkenner, kunt u warm store-query's tijdelijk uitschakelen:

   [![Warme query's uitschakelen](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Koude opslag

In deze sectie worden Azure Storage informatie beschreven die relevant is Azure Time Series Insights Gen2.

Lees de inleiding tot [Storage-blobs](../storage/blobs/storage-blobs-introduction.md)voor een gedetailleerde beschrijving van Azure Blob Storage.

### <a name="your-cold-storage-account"></a>Uw koude opslagaccount

Azure Time Series Insights Gen2 behoudt maximaal twee kopieën van elke gebeurtenis in uw Azure Storage account. In één kopie worden gebeurtenissen op volgorde van opnametijd op volgorde opgevolgd, waarbij altijd toegang tot gebeurtenissen wordt gegeven in een op tijd geordende volgorde. Na een periode maakt Azure Time Series Insights Gen2 ook een gepartitioneerde kopie van de gegevens om te optimaliseren voor het uitvoeren van query's.

Al uw gegevens worden voor onbepaalde tijd opgeslagen in uw Azure Storage account.

> [!WARNING]
> Beperk de openbare internettoegang niet tot een hub of gebeurtenisbron die wordt gebruikt door Time Series Insights anders wordt de benodigde verbinding verbroken.

#### <a name="writing-and-editing-blobs"></a>Blobs schrijven en bewerken

Om ervoor te zorgen dat queryprestaties en beschikbaarheid van gegevens beschikbaar zijn, moet u geen blobs bewerken of verwijderen die Azure Time Series Insights Gen2 maakt.

#### <a name="accessing-cold-store-data"></a>Toegang tot gegevens in koude opslag

Naast toegang tot uw gegevens vanuit [de Azure Time Series Insights Explorer-](./concepts-ux-panels.md) en Time Series Query-API's, kunt u ook rechtstreeks toegang krijgen tot uw gegevens vanuit de Parquet-bestanden die zijn opgeslagen in de koude opslag. [](./concepts-query-overview.md) U kunt bijvoorbeeld gegevens lezen, transformeren en ops schonen in een Jupyter-notebook en deze vervolgens gebruiken om uw Azure Machine Learning-model in dezelfde Spark-werkstroom te trainen.

Als u rechtstreeks vanuit uw Azure Storage-account toegang wilt krijgen tot gegevens, hebt u leestoegang nodig tot het account dat wordt gebruikt om uw Azure Time Series Insights Gen2-gegevens op te slaan. Vervolgens kunt u geselecteerde gegevens lezen op basis van de aanmaaktijd van het Parquet-bestand in de map die hieronder wordt beschreven in de `PT=Time` [sectie Parquet-bestandsindeling.](#parquet-file-format-and-folder-structure)  Zie Toegang tot uw opslagaccountresources beheren voor meer informatie over het inschakelen van leestoegang tot [uw opslagaccount.](../storage/blobs/anonymous-read-access-configure.md)

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder uw Azure Time Series Insights Gen2-bestanden niet. Gerelateerde gegevens alleen beheren vanuit Azure Time Series Insights Gen2.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet-bestandsindeling en mapstructuur

Parquet is een opensource-bestandsindeling voor kolommen die is ontworpen voor efficiënte opslag en prestaties. Azure Time Series Insights Gen2 maakt gebruik van Parquet om queryprestaties op basis van time series-id's op schaal mogelijk te maken.

Lees de Parquet-documentatie voor meer informatie over het [Parquet-bestandstype.](https://parquet.apache.org/documentation/latest/)

Azure Time Series Insights Gen2 slaat kopieën van uw gegevens als volgt op:

* De eerste, eerste kopie wordt gepartitief op basis van de opnametijd en slaat gegevens ongeveer op volgorde van aankomst op. Deze gegevens bevinden zich in de `PT=Time` map :

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gegroepeerd op tijdreeks-ID's en bevindt zich in de `PT=TsId` map:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

De tijdstempel in de blobnamen in de map komt overeen met de aankomsttijd van de gegevens met Azure Time Series Insights Gen2 en niet met de `PT=Time` tijdstempel van de gebeurtenissen.

Gegevens in de `PT=TsId` map worden geoptimaliseerd voor query's gedurende een periode en zijn niet statisch. Tijdens het opnieuw indelen zijn sommige gebeurtenissen mogelijk aanwezig in meerdere blobs. De naamgeving van de blobs in deze map blijft niet gegarandeerd hetzelfde.

Als u rechtstreeks toegang nodig hebt tot gegevens via Parquet-bestanden, gebruikt u over het algemeen de `PT=Time` map .  Toekomstige functionaliteit maakt efficiënte toegang tot de map `PT=TsId` mogelijk.

> [!NOTE]
>
> * `<YYYY>` wordt aan een viercijferige jaarweergave toebetalen.
> * `<MM>` wordt aan een maandweergave van twee cijfers toebetalen.
> * `<YYYYMMDDHHMMSSfff>` wordt een tijdstempel weergegeven met viercijferige jaar ( ), maand (), dag met twee cijfers ( ), uur met twee cijfers ( ), minuut met twee cijfers ( ), seconde van twee cijfers ( ) en milliseconden van drie `YYYY` `MM` cijfers ( `DD` `HH` `MM` `SS` `fff` ).

Azure Time Series Insights Gen2-gebeurtenissen worden als volgt aan de inhoud van het Parquet-bestand toegevoegd:

* Elke gebeurtenis wordt aan één rij toe te kennen.
* Elke rij bevat de **tijdstempelkolom** met een tijdstempel van de gebeurtenis. De eigenschap tijdstempel is nooit null. De standaardwaarde is de **gebeurtenis in de enqueu-tijd** als de eigenschap tijdstempel niet is opgegeven in de gebeurtenisbron. De opgeslagen tijdstempel is altijd in UTC.
* Elke rij bevat de kolom(s) tijdreeks-id(s) zoals gedefinieerd wanneer de Azure Time Series Insights Gen2-omgeving wordt gemaakt. De TSID-eigenschapsnaam bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden, afhankelijk van het eigenschapstype, aan kolomnamen die eindigen op `_bool` (Booleaanse), `_datetime` (tijdstempel), `_long` (lang), (dubbel), (tekenreeks) of `_double` `_string` `dynamic` (dynamisch) toebeland.  Lees meer over Ondersteunde [gegevenstypen voor meer informatie.](./concepts-supported-data-types.md)
* Dit toewijzingsschema is van toepassing op de eerste versie van de bestandsindeling waarnaar wordt verwezen **als V=1** en wordt opgeslagen in de basismap met dezelfde naam. Naarmate deze functie zich verder ontwikkelt, kan dit toewijzingsschema worden gewijzigd en wordt de referentienaam verhoogd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie [over gegevensmodelleren.](./concepts-model-overview.md)

* Plan uw [Azure Time Series Insights Gen2-omgeving.](./how-to-plan-your-environment.md)
