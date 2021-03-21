---
title: Exporteren met behulp van Stream Analytics vanuit Azure-toepassing inzichten | Microsoft Docs
description: Stream Analytics kunt de gegevens die u exporteert, continu transformeren, filteren en routeren vanuit Application Insights.
ms.topic: conceptual
ms.date: 01/08/2019
ms.openlocfilehash: a517bddd8981554b7fb5044d33b6c6777df51e36
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101719794"
---
# <a name="use-stream-analytics-to-process-exported-data-from-application-insights"></a>Gebruik Stream Analytics voor het verwerken van geëxporteerde gegevens van Application Insights

[Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/) is het ideale hulp programma voor het verwerken van gegevens die zijn [geëxporteerd vanuit Application Insights](export-telemetry.md). Stream Analytics kunnen gegevens uit verschillende bronnen ophalen. De gegevens kunnen worden getransformeerd en gefilterd en vervolgens worden doorgestuurd naar verschillende Sinks.

In dit voor beeld maken we een adapter die gegevens van Application Insights neemt, de naam van een deel van de velden bijwerkt en verwerkt, en deze in Power BI.

> [!WARNING]
> Er zijn veel betere en eenvoudiger [aanbevolen manieren om Application Insights gegevens in Power bi weer te geven](./export-power-bi.md). Het pad dat hier wordt beschreven, is slechts een voor beeld van het verwerken van geëxporteerde gegevens.

![Blok diagram voor export via SA naar aan pbi](./media/export-stream-analytics/020.png)

## <a name="create-storage-in-azure"></a>Opslag maken in azure
Doorlopend exporteren voert altijd gegevens uit naar een Azure Storage-account, dus u moet eerst de opslag maken.

1. Maak een ' klassiek ' opslag account in uw abonnement in het [Azure Portal](https://portal.azure.com).
   
   ![In Azure Portal kiest u nieuw, gegevens, opslag](./media/export-stream-analytics/030.png)
2. Een container maken
   
    ![In de nieuwe opslag selecteert u containers, klikt u op de tegel containers en voegt u vervolgens](./media/export-stream-analytics/040.png)
3. De toegangs sleutel voor opslag kopiëren
   
    U hebt deze binnenkort nodig om de invoer voor de stream Analytics-service in te stellen.
   
    ![Open in de opslag instellingen, sleutels en maak een kopie van de primaire toegangs sleutel](./media/export-stream-analytics/045.png)

## <a name="start-continuous-export-to-azure-storage"></a>Continue export naar Azure Storage starten

Met [doorlopend exporteren](export-telemetry.md) worden gegevens verplaatst van Application Insights naar Azure Storage.

1. Blader in het Azure Portal naar de Application Insights resource die u hebt gemaakt voor uw toepassing.
   
    ![Kies bladeren, Application Insights, uw toepassing](./media/export-stream-analytics/050.png)
2. Een continue export maken.
   
    ![Kies instellingen, doorlopend exporteren, toevoegen](./media/export-stream-analytics/060.png)

    Selecteer het opslag account dat u eerder hebt gemaakt:

    ![De export bestemming instellen](./media/export-stream-analytics/070.png)

    Stel de gebeurtenis typen in die u wilt weer geven:

    ![Gebeurtenis typen kiezen](./media/export-stream-analytics/080.png)

1. Laat sommige gegevens samen voegen. U kunt de toepassing een tijdje gebruiken. Telemetrie is beschikbaar in en u ziet statistische grafieken in [metrische Explorer](../essentials/metrics-charts.md) en afzonderlijke gebeurtenissen in [Diagnostische Zoek opdrachten](./diagnostic-search.md).
   
    Daarnaast worden de gegevens naar uw opslag geëxporteerd. 
2. Inspecteer de geëxporteerde gegevens. In Visual Studio kiest u **weer gave/Cloud Verkenner** en opent u Azure/Storage. (Als u deze menu optie niet hebt, moet u de Azure SDK installeren: Open het dialoog venster New project en open Visual C#/Cloud/Get Microsoft Azure SDK voor .NET.)
   
    ![Scherm afbeelding die laat zien hoe u de gebeurtenis typen instelt die u wilt zien.](./media/export-stream-analytics/04-data.png)
   
    Noteer het algemene deel van de padnaam, die is afgeleid van de toepassings naam en instrumentatie sleutel.

De gebeurtenissen worden geschreven naar BLOB-bestanden in JSON-indeling. Elk bestand kan een of meer gebeurtenissen bevatten. Daarom willen we de gegevens van de gebeurtenis lezen en de gewenste velden filteren. Er zijn allerlei dingen die we kunnen doen met de gegevens, maar het plan is nu het gebruik van Stream Analytics om de gegevens door te sluizen naar Power BI.

## <a name="create-an-azure-stream-analytics-instance"></a>Een Azure Stream Analytics-exemplaar maken

Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure stream Analytics-service en maak een nieuwe stream Analytics taak:

![Scherm opname van de hoofd pagina voor het maken van Stream Analytics taak in de Azure Portal.](./media/export-stream-analytics/SA001.png)

![Scherm opname van de gegevens die nodig zijn bij het maken van een nieuwe Stream Analytics-taak.](./media/export-stream-analytics/SA002.png)

Wanneer de nieuwe taak is gemaakt, selecteert **u naar resource**.

![Scherm afbeelding met het bericht dat wordt ontvangen wanneer de implementatie van de nieuwe Stream Analytics-taak is voltooid.](./media/export-stream-analytics/SA003.png)

### <a name="add-a-new-input"></a>Een nieuwe invoer toevoegen

![Scherm afbeelding die laat zien hoe u invoer kunt toevoegen aan de Stream Analytics taak.](./media/export-stream-analytics/SA004.png)

Stel deze waarde in om in te voeren op basis van de doorlopende export-blob:

![Scherm opname van de configuratie van de Stream Analytics taak om invoer uit te voeren vanuit een doorlopende export-blob.](./media/export-stream-analytics/SA0005.png)

Nu hebt u de primaire toegangs sleutel van uw opslag account nodig die u eerder hebt genoteerd. Stel dit in als de sleutel voor het opslag account.

### <a name="set-path-prefix-pattern"></a>Patroon voor voegsel van pad instellen

**Zorg ervoor dat u de datum notatie instelt op JJJJ-MM-DD (met streepjes).**

Het voorvoegsel patroon van het pad geeft aan waar Stream Analytics de invoer bestanden in de opslag locatie vindt. U moet deze instellen op overeenkomen met de manier waarop continue export de gegevens opslaat. Stel deze als volgt in:

`webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}`

In dit voorbeeld:

* `webapplication27` is de naam van de Application Insights resource **alle kleine letters**.
* `1234...` is de instrumentatie sleutel van de Application Insights resource, waarbij **streepjes worden wegge laten**. 
* `PageViews` is het type gegevens dat u wilt analyseren. Welke typen beschikbaar zijn, is afhankelijk van het filter dat u in continue export hebt ingesteld. Controleer de geëxporteerde gegevens om de andere beschik bare typen te bekijken en Bekijk het [gegevens model exporteren](export-data-model.md).
* `/{date}/{time}` is een patroon dat letterlijk is geschreven.

> [!NOTE]
> Inspecteer de opslag om er zeker van te zijn dat u het juiste pad krijgt.

## <a name="add-new-output"></a>Nieuwe uitvoer toevoegen

Selecteer nu uw taak > **uitvoer**  >  **toevoegen**.

![Scherm opname van het selecteren van de Stream Analytics taak voor het toevoegen van een nieuwe uitvoer.](./media/export-stream-analytics/SA006.png)


![Selecteer het nieuwe kanaal, klik op uitvoer, toevoegen, Power BI](./media/export-stream-analytics/SA010.png)

Geef uw **werk-of school account** op om stream Analytics te autoriseren voor toegang tot uw Power bi-resource. Vervolgens een naam voor de uitvoer en voor het doel Power BI gegevensset en tabel.

## <a name="set-the-query"></a>De query instellen

De query bepaalt de vertaling van invoer naar uitvoer.

Gebruik de functie test om te controleren of u de juiste uitvoer krijgt. Geef deze voorbeeld gegevens die u hebt gemaakt op de pagina invoer.

### <a name="query-to-display-counts-of-events"></a>Query om het aantal gebeurtenissen weer te geven

Deze query plakken:

```SQL
SELECT
  flat.ArrayValue.name,
  count(*)
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.[event]) as flat
GROUP BY TumblingWindow(minute, 1), flat.ArrayValue.name
```

* export-invoer is de alias die we hebben opgegeven voor de stroom invoer
* aan pbi-uitvoer is de uitvoer alias die we hebben gedefinieerd
* We gebruiken [outer apply GetElements](/stream-analytics-query/apply-azure-stream-analytics) omdat de naam van de gebeurtenis zich in een geneste JSON-matrix bevindt. Vervolgens selecteert de Select de naam van de gebeurtenis, samen met een telling van het aantal exemplaren met die naam in de tijds periode. De [Group By-Component groepeert](/stream-analytics-query/group-by-azure-stream-analytics) de elementen in peri Oden van één minuut.

### <a name="query-to-display-metric-values"></a>Query om meet waarden weer te geven

```SQL
SELECT
  A.context.data.eventtime,
  avg(CASE WHEN flat.arrayvalue.myMetric.value IS NULL THEN 0 ELSE  flat.arrayvalue.myMetric.value END) as myValue
INTO
  [pbi-output]
FROM
  [export-input] A
OUTER APPLY GetElements(A.context.custom.metrics) as flat
GROUP BY TumblingWindow(minute, 1), A.context.data.eventtime
```

* Met deze query wordt ingezoomd op de telemetriegegevens voor de gebeurtenis tijd en de waarde van de metrische gegevens. De metrische waarden bevinden zich in een matrix. Daarom gebruiken we het GetElements-patroon van de BUITENste toepassing om de rijen uit te pakken. ' myMetric ' is de naam van de metrische waarde in dit geval.

### <a name="query-to-include-values-of-dimension-properties"></a>Query voor het toevoegen van waarden van dimensie-eigenschappen

```SQL
WITH flat AS (
SELECT
  MySource.context.data.eventTime as eventTime,
  InstanceId = MyDimension.ArrayValue.InstanceId.value,
  BusinessUnitId = MyDimension.ArrayValue.BusinessUnitId.value
FROM MySource
OUTER APPLY GetArrayElements(MySource.context.custom.dimensions) MyDimension
)
SELECT
  eventTime,
  InstanceId,
  BusinessUnitId
INTO AIOutput
FROM flat
```

* Deze query bevat waarden van de dimensie-eigenschappen, zonder dat dit afhankelijk is van een bepaalde dimensie op een vaste index in de dimensie matrix.

## <a name="run-the-job"></a>De taak uitvoeren

U kunt een datum in het verleden selecteren om de taak te starten.

![Selecteer de taak en klik op query. Plak het voor beeld hieronder.](./media/export-stream-analytics/SA008.png)

Wacht totdat de taak wordt uitgevoerd.

## <a name="see-results-in-power-bi"></a>Resultaten weer geven in Power BI

> [!WARNING]
> Er zijn veel betere en eenvoudiger [aanbevolen manieren om Application Insights gegevens in Power bi weer te geven](./export-power-bi.md). Het pad dat hier wordt beschreven, is slechts een voor beeld van het verwerken van geëxporteerde gegevens.


Open Power BI met uw werk-of school account en selecteer de gegevensset en tabel die u hebt gedefinieerd als de uitvoer van de Stream Analytics taak.

![Selecteer uw gegevensset en velden in Power BI.](./media/export-stream-analytics/200.png)

U kunt deze gegevensset nu gebruiken in rapporten en dash boards in [Power bi](https://powerbi.microsoft.com).

![Scherm afbeelding toont een rapport dat is gemaakt op basis van een gegevensset in Power BI.](./media/export-stream-analytics/210.png)

## <a name="no-data"></a>Zijn er geen gegevens?

* Controleer of u [de datum notatie correct instelt](#set-path-prefix-pattern) op jjjj-mm-dd (met streepjes).

## <a name="next-steps"></a>Volgende stappen
* [Continue export](export-telemetry.md)
* [Gedetailleerde gegevens model verwijzing voor de eigenschaps typen en-waarden.](export-data-model.md)
* [Application Insights](./app-insights-overview.md)
