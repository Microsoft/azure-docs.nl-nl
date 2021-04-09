---
title: Overzicht van opslag-Azure Time Series Insights Gen2 | Microsoft Docs
description: Meer informatie over gegevens opslag in Azure Time Series Insights Gen2.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 01/21/2021
ms.custom: seodec18
ms.openlocfilehash: 748eaca93eaee5ec858ea43261995111cef8ceda
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98676848"
---
# <a name="data-storage"></a>Gegevensopslag

In dit artikel wordt de gegevens opslag in Azure Time Series Insights Gen2 beschreven. Dit geldt voor warme en koude, Beschik baarheid van gegevens en aanbevolen procedures.

## <a name="provisioning"></a>Inrichten

Wanneer u een Azure Time Series Insights Gen2-omgeving maakt, hebt u de volgende opties:

* Koude gegevens opslag:
  * Maak een nieuwe Azure Storage-Resource in het abonnement en de regio die u hebt gekozen voor uw omgeving.
  * Een bestaand Azure Storage-account koppelen. Deze optie is alleen beschikbaar als u vanuit een Azure Resource Manager- [sjabloon](/azure/templates/microsoft.timeseriesinsights/allversions)implementeert en niet zichtbaar is in de Azure Portal.
* Warme gegevens opslag:
  * Een warme Store is optioneel en kan worden ingeschakeld of uitgeschakeld tijdens of na het inrichten. Als u besluit om warme Store op een later tijdstip in te scha kelen en er al gegevens in uw koel winkel staan, raadpleegt u [deze](concepts-storage.md#warm-store-behavior) sectie hieronder om het verwachte gedrag te begrijpen. De Bewaar periode voor de warme Store-gegevens kan 7 tot 31 dagen worden geconfigureerd. Dit kan ook worden aangepast als dat nodig is.

Wanneer een gebeurtenis wordt opgenomen, wordt deze in zowel de warme Store (indien ingeschakeld) als in het koel archief geïndexeerd.

[![Overzicht van opslag](media/concepts-storage/pipeline-to-storage.png)](media/concepts-storage/pipeline-to-storage.png#lightbox)

> [!WARNING]
> Als eigenaar van het Azure Blob Storage-account waar koud opgeslagen gegevens zich bevinden, hebt u volledige toegang tot alle gegevens in het account. Deze toegang omvat machtigingen voor schrijven en verwijderen. Bewerk of verwijder de gegevens die Azure Time Series Insights Gen2-schrijf bewerkingen, omdat dat gegevens verlies kan veroorzaken.

## <a name="data-availability"></a>Beschikbaarheid van gegevens

Azure Time Series Insights Gen2 partities en index gegevens voor optimale query prestaties. Gegevens worden beschikbaar gesteld voor het uitvoeren van een query vanuit zowel warme (indien ingeschakeld) als koud opgeslagen na de index. De hoeveelheid gegevens die wordt opgenomen en de doorvoer snelheid per partitie kan van invloed zijn op de beschik baarheid. Bekijk de [doorvoer beperkingen](./concepts-streaming-ingress-throughput-limits.md) van de gebeurtenis bron en [Aanbevolen procedures](./concepts-streaming-ingestion-event-sources.md#streaming-ingestion-best-practices) voor de beste prestaties. U kunt ook een vertragings [waarschuwing](./time-series-insights-environment-mitigate-latency.md#monitor-latency-and-throttling-with-alerts) configureren om te worden gewaarschuwd als uw omgeving problemen ondervindt bij het verwerken van gegevens.

> [!IMPORTANT]
> Het kan een periode van Maxi maal 60 seconden duren voordat de gegevens beschikbaar zijn. Als u na 60 seconden een langere latentie ondervindt, kunt u een ondersteunings ticket indienen via de Azure Portal.

## <a name="warm-store"></a>Warme Store

Gegevens in uw warme archief zijn alleen beschikbaar via de [Time Series query-api's](./concepts-query-overview.md), de [Azure time series Insights TSI-verkenner](./concepts-ux-panels.md)of de Power bi- [connector](./how-to-connect-power-bi.md). Query's voor warme Stores zijn gratis en er is geen quotum, maar er is een [limiet van 30](/rest/api/time-series-insights/reference-api-limits#query-apis---limits) gelijktijdige aanvragen.

### <a name="warm-store-behavior"></a>Gedrag voor warme opslag

* Wanneer deze functie is ingeschakeld, worden alle gegevens die in uw omgeving zijn gestreamd, doorgestuurd naar uw warme Store, ongeacht de tijds tempel van de gebeurtenis. Houd er rekening mee dat de pijp lijn voor streaming-opname is gebouwd voor bijna realtime streaming en het opnemen van historische gebeurtenissen wordt [niet ondersteund](./concepts-streaming-ingestion-event-sources.md#historical-data-ingestion).
* De retentie tijd wordt berekend op basis van het moment waarop de gebeurtenis is geïndexeerd in warme opslag, niet de tijds tempel van de gebeurtenis. Dit betekent dat gegevens niet meer beschikbaar zijn in de warme opslag nadat de Bewaar periode is verstreken, zelfs als het tijds tempel van de gebeurtenis voor de toekomst ligt.
  * Voor beeld: een gebeurtenis met weers verwachtingen van 10 dagen wordt opgenomen en geïndexeerd in een warme opslag container die is geconfigureerd met een Bewaar periode van 7 dagen. Na 7 dagen is de voor spelling niet langer toegankelijk in de warme Store, maar kan er wel een query worden uitgevoerd vanuit koude.
* Als u warme Store inschakelt in een bestaande omgeving die al recente gegevens bevat die zijn geïndexeerd in koude opslag, moet u er rekening mee houden dat uw warme archief niet met deze gegevens kan worden gevuld.
* Als u een warme archief zojuist hebt ingeschakeld en problemen ondervindt met het weer geven van uw recente gegevens in de Explorer, kunt u de query's voor warme Store tijdelijk uitschakelen:

   [![Warme query's uitschakelen](media/concepts-storage/toggle-warm.png)](media/concepts-storage/toggle-warm.png#lightbox)

## <a name="cold-store"></a>Koude Store

In deze sectie worden Azure Storage gegevens beschreven die relevant zijn voor Azure Time Series Insights Gen2.

Lees de [Inleiding tot opslag-blobs](../storage/blobs/storage-blobs-introduction.md)voor een uitgebreide beschrijving van Azure Blob-opslag.

### <a name="your-cold-storage-account"></a>Uw account voor koude opslag

Azure Time Series Insights Gen2 bewaren Maxi maal twee exemplaren van elke gebeurtenis in uw Azure Storage-account. Een kopie slaat gebeurtenissen op die zijn besteld door opname tijd, waarbij altijd toegang tot gebeurtenissen in een geordende reeks wordt toegestaan. Azure Time Series Insights Gen2 maakt in de loop van de tijd ook een opnieuw gepartitioneerde kopie van de gegevens voor het optimaliseren van query's die kunnen worden uitgevoerd.

Al uw gegevens worden voor onbepaalde tijd opgeslagen in uw Azure Storage-account.

#### <a name="writing-and-editing-blobs"></a>Blobs schrijven en bewerken

Om de query prestaties en de beschik baarheid van gegevens te garanderen, moet u geen blobs bewerken of verwijderen die Azure Time Series Insights Gen2 maakt.

#### <a name="accessing-cold-store-data"></a>Toegang tot koude Store-gegevens

U hebt niet alleen toegang tot uw gegevens via de Api's van de [Azure time series Insights Explorer](./concepts-ux-panels.md) -en [Time Series-query](./concepts-query-overview.md), maar u kunt ook uw gegevens rechtstreeks openen vanuit de Parquet-bestanden die zijn opgeslagen in het koel huis. U kunt bijvoorbeeld gegevens in een Jupyter-notebook lezen, transformeren en opschonen en deze vervolgens gebruiken om uw Azure Machine Learning model te trainen in dezelfde Spark-werk stroom.

Als u gegevens rechtstreeks vanuit uw Azure Storage-account wilt openen, moet u lees toegang hebben tot het account dat wordt gebruikt om uw Azure Time Series Insights Gen2-gegevens op te slaan. U kunt vervolgens geselecteerde gegevens lezen op basis van de aanmaak tijd van het Parquet-bestand dat zich bevindt in de `PT=Time` map die hieronder wordt beschreven in de sectie [Parquet-bestands indeling](#parquet-file-format-and-folder-structure) .  Zie [toegang tot de resources van uw opslag account beheren](../storage/blobs/anonymous-read-access-configure.md)voor meer informatie over het inschakelen van lees toegang tot uw opslag account.

#### <a name="data-deletion"></a>Gegevens verwijderen

Verwijder niet uw Azure Time Series Insights Gen2-bestanden. Alleen gerelateerde gegevens vanuit Azure Time Series Insights Gen2 beheren.

### <a name="parquet-file-format-and-folder-structure"></a>Parquet bestands indeling en mapstructuur

Parquet is een open-source kolom indeling die is ontworpen voor efficiënte opslag en prestaties. Azure Time Series Insights Gen2 maakt gebruik van Parquet om query prestaties op basis van tijd reeksen op schaal in te scha kelen.  

Lees de [Parquet-documentatie](https://parquet.apache.org/documentation/latest/)voor meer informatie over het bestands type Parquet.

Azure Time Series Insights Gen2 worden kopieën van uw gegevens als volgt opgeslagen:

* De eerste, eerste kopie wordt gepartitioneerd op basis van de opname tijd en slaat ruwweg gegevens op in volg orde van aankomst. Deze gegevens bevinden zich in de `PT=Time` map:

  `V=1/PT=Time/Y=<YYYY>/M=<MM>/<YYYYMMDDHHMMSSfff>_<TSI_INTERNAL_SUFFIX>.parquet`

* De tweede, opnieuw gepartitioneerde kopie wordt gegroepeerd op Time Series-Id's en bevindt zich in de `PT=TsId` map:

  `V=1/PT=TsId/<TSI_INTERNAL_NAME>.parquet`

De tijds tempel in de namen van de blobs in de `PT=Time` map komt overeen met de aankomst tijd van de gegevens in azure time series Insights Gen2 en niet de tijds tempel van de gebeurtenissen.

Gegevens in de `PT=TsId` map worden gedurende een bepaalde periode geoptimaliseerd en zijn niet statisch. Tijdens het opnieuw partitioneren zijn er mogelijk enkele gebeurtenissen aanwezig in meerdere blobs. De naamgeving van de blobs in deze map is niet gegarandeerd dat deze hetzelfde blijft.

In het algemeen kunt u de map gebruiken als u rechtstreeks toegang moet hebben tot gegevens via Parquet-bestanden `PT=Time` .  De toekomstige functionaliteit zorgt ervoor dat de map efficiënt wordt geopend `PT=TsId` .

> [!NOTE]
>
> * `<YYYY>` wordt toegewezen aan een jaar representatie van vier cijfers.
> * `<MM>` wordt toegewezen aan een maand weergave met twee cijfers.
> * `<YYYYMMDDHHMMSSfff>` is gekoppeld aan een tijds tempel weergave met vier cijfers per jaar ( `YYYY` ), een maand van twee cijfers (), een dag van twee cijfers (), een uur van twee cijfers (), een minuut van twee `MM` `DD` `HH` cijfers ( `MM` ), een tweede cijfer seconde ( `SS` ) en een milliseconde van drie cijfers `fff` .

Azure Time Series Insights Gen2-gebeurtenissen worden als volgt toegewezen aan de inhoud van Parquet-bestand:

* Elke gebeurtenis wordt toegewezen aan één rij.
* Elke rij bevat de **Time Stamp** -kolom met een tijds tempel van de gebeurtenis. De eigenschap time stamp is nooit null. De standaard instelling is dat de gebeurtenis in de **wachtrij** wordt geplaatst als de eigenschap time stamp niet is opgegeven in de bron van de gebeurtenis. De opgeslagen tijds tempel is altijd in UTC.
* Elke rij bevat de tijdreeks-ID (TSID) kolom (men) zoals gedefinieerd wanneer de Azure Time Series Insights Gen2-omgeving wordt gemaakt. De TSID-eigenschaps naam bevat het `_string` achtervoegsel.
* Alle andere eigenschappen die als telemetriegegevens worden verzonden, worden toegewezen aan kolom namen die eindigen `_bool` op (Booleaans), `_datetime` (tijds tempel), `_long` (Long), `_double` (dubbele), `_string` (teken reeks) of `dynamic` (dynamisch), afhankelijk van het eigenschaps type.  Meer informatie over [ondersteunde gegevens typen](./concepts-supported-data-types.md)vindt u in.
* Dit toewijzings schema is van toepassing op de eerste versie van de bestands indeling, waarnaar wordt verwezen als **V = 1** en wordt opgeslagen in de map base met dezelfde naam. Als deze functie zich ontwikkelt, kan dit toewijzings schema worden gewijzigd en wordt de referentie naam verhoogd.

## <a name="next-steps"></a>Volgende stappen

* Meer informatie over [gegevens modellering](./concepts-model-overview.md).

* Plan uw [Azure time series Insights Gen2-omgeving](./how-to-plan-your-environment.md).
