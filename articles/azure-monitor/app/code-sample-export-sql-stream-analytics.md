---
title: Exporteren naar SQL vanuit Azure-toepassing Insights | Microsoft Docs
description: Application Insights gegevens doorlopend naar SQL exporteren met behulp van Stream Analytics.
ms.topic: conceptual
ms.date: 09/11/2017
ms.openlocfilehash: 25d28bb0fc35ef76231c085dc1d9d8a1234a264c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100587756"
---
# <a name="walkthrough-export-to-sql-from-application-insights-using-stream-analytics"></a>Walkthrough: exporteren naar SQL vanuit Application Insights met behulp van Stream Analytics
In dit artikel wordt beschreven hoe u uw telemetriegegevens van [Azure-toepassing inzichten][start] kunt verplaatsen naar Azure SQL database met behulp van [continue export][export] en [Azure stream Analytics](https://azure.microsoft.com/services/stream-analytics/). 

Doorlopend exporteren verplaatst uw telemetriegegevens naar Azure Storage in JSON-indeling. We parseren de JSON-objecten met Azure Stream Analytics en maken rijen in een database tabel.

(In het algemeen is doorlopend exporteren de manier om uw eigen analyse uit te voeren van de telemetrie die uw apps naar Application Insights verzenden. U kunt dit code voorbeeld aanpassen om andere dingen te doen met de geëxporteerde telemetrie, zoals het samen voegen van gegevens.)

We beginnen met de veronderstelling dat u de app die u wilt bewaken al hebt.

In dit voor beeld gebruiken we de pagina weergave gegevens, maar hetzelfde patroon kan eenvoudig worden uitgebreid naar andere gegevens typen, zoals aangepaste gebeurtenissen en uitzonde ringen. 

## <a name="add-application-insights-to-your-application"></a>Application Insights toevoegen aan uw toepassing
Aan de slag:

1. [Application Insights instellen voor uw webpagina's](./javascript.md). 
   
    (In dit voor beeld richten we zich op het verwerken van pagina weergave gegevens van de client browsers, maar u kunt ook Application Insights instellen voor de server zijde van uw [Java](./java-get-started.md) -of [ASP.net](./asp-net.md) -app, en proces aanvraag, afhankelijkheid en andere server-telemetrie.)
2. Uw app publiceren en telemetrie-gegevens bekijken die worden weer gegeven in uw Application Insights-resource.

## <a name="create-storage-in-azure"></a>Opslag maken in azure
Doorlopend exporteren voert altijd gegevens uit naar een Azure Storage-account, dus u moet eerst de opslag maken.

1. Maak een opslag account in uw abonnement in de [Azure Portal][portal].
   
    ![Kies in Azure Portal nieuwe, gegevens, opslag. Selecteer klassiek en kies maken. Geef een opslag naam op.](./media/code-sample-export-sql-stream-analytics/040-store.png)
2. Een container maken
   
    ![In de nieuwe opslag selecteert u containers, klikt u op de tegel containers en voegt u vervolgens](./media/code-sample-export-sql-stream-analytics/050-container.png)
3. De toegangs sleutel voor opslag kopiëren
   
    U hebt deze binnenkort nodig om de invoer voor de stream Analytics-service in te stellen.
   
    ![Open in de opslag instellingen, sleutels en maak een kopie van de primaire toegangs sleutel](./media/code-sample-export-sql-stream-analytics/21-storage-key.png)

## <a name="start-continuous-export-to-azure-storage"></a>Continue export naar Azure Storage starten
1. Blader in het Azure Portal naar de Application Insights resource die u hebt gemaakt voor uw toepassing.
   
    ![Kies bladeren, Application Insights, uw toepassing](./media/code-sample-export-sql-stream-analytics/060-browse.png)
2. Een continue export maken.
   
    ![Kies instellingen, doorlopend exporteren, toevoegen](./media/code-sample-export-sql-stream-analytics/070-export.png)

    Selecteer het opslag account dat u eerder hebt gemaakt:

    ![De export bestemming instellen](./media/code-sample-export-sql-stream-analytics/080-add.png)

    Stel de gebeurtenis typen in die u wilt weer geven:

    ![Gebeurtenis typen kiezen](./media/code-sample-export-sql-stream-analytics/085-types.png)


1. Laat sommige gegevens samen voegen. U kunt de toepassing een tijdje gebruiken. Telemetrie is beschikbaar in en u ziet statistische grafieken in [metrische Explorer](../essentials/metrics-charts.md) en afzonderlijke gebeurtenissen in [Diagnostische Zoek opdrachten](./diagnostic-search.md). 
   
    Daarnaast worden de gegevens naar uw opslag geëxporteerd. 
2. Inspecteer de geëxporteerde gegevens in de portal: Kies **Bladeren**, selecteer uw opslag account en vervolgens **containers** of in Visual Studio. In Visual Studio kiest u **weer gave/Cloud Verkenner** en opent u Azure/Storage. (Als u deze menu optie niet hebt, moet u de Azure SDK installeren: Open het dialoog venster New project en open Visual C#/Cloud/Get Microsoft Azure SDK voor .NET.)
   
    ![Open in Visual Studio Server browser, azure, opslag](./media/code-sample-export-sql-stream-analytics/087-explorer.png)
   
    Noteer het algemene deel van de padnaam, die is afgeleid van de toepassings naam en instrumentatie sleutel. 

De gebeurtenissen worden geschreven naar BLOB-bestanden in JSON-indeling. Elk bestand kan een of meer gebeurtenissen bevatten. Daarom willen we de gegevens van de gebeurtenis lezen en de gewenste velden filteren. Er zijn allerlei dingen die we kunnen doen met de gegevens, maar het plan is nu het gebruik van Stream Analytics om de gegevens naar SQL Database te verplaatsen. Zo kunt u heel eenvoudig veel interessante query's uitvoeren.

## <a name="create-an-azure-sql-database"></a>Een Azure SQL-database maken
Nadat u het abonnement in [Azure Portal][portal]hebt gemaakt, maakt u de data base (en een nieuwe server, tenzij u er al een hebt) waarnaar u de gegevens gaat schrijven.

![Nieuwe, gegevens, SQL](./media/code-sample-export-sql-stream-analytics/090-sql.png)

Zorg ervoor dat de server toegang verleent tot Azure-Services:

![Bladeren, servers, uw server, instellingen, firewall, toegang tot Azure toestaan](./media/code-sample-export-sql-stream-analytics/100-sqlaccess.png)

## <a name="create-a-table-in-azure-sql-database"></a>Een tabel maken in Azure SQL Database
Maak verbinding met de data base die in de vorige sectie is gemaakt met uw favoriete beheer programma. In deze walkthrough wordt [SQL Server Management tools](/sql/ssms/sql-server-management-studio-ssms) (SSMS) gebruikt.

![Verbinding maken met Azure SQL Database](./media/code-sample-export-sql-stream-analytics/31-sql-table.png)

Maak een nieuwe query en voer de volgende T-SQL-opdracht uit:

```SQL

CREATE TABLE [dbo].[PageViewsTable](
    [pageName] [nvarchar](max) NOT NULL,
    [viewCount] [int] NOT NULL,
    [url] [nvarchar](max) NULL,
    [urlDataPort] [int] NULL,
    [urlDataprotocol] [nvarchar](50) NULL,
    [urlDataHost] [nvarchar](50) NULL,
    [urlDataBase] [nvarchar](50) NULL,
    [urlDataHashTag] [nvarchar](max) NULL,
    [eventTime] [datetime] NOT NULL,
    [isSynthetic] [nvarchar](50) NULL,
    [deviceId] [nvarchar](50) NULL,
    [deviceType] [nvarchar](50) NULL,
    [os] [nvarchar](50) NULL,
    [osVersion] [nvarchar](50) NULL,
    [locale] [nvarchar](50) NULL,
    [userAgent] [nvarchar](max) NULL,
    [browser] [nvarchar](50) NULL,
    [browserVersion] [nvarchar](50) NULL,
    [screenResolution] [nvarchar](50) NULL,
    [sessionId] [nvarchar](max) NULL,
    [sessionIsFirst] [nvarchar](50) NULL,
    [clientIp] [nvarchar](50) NULL,
    [continent] [nvarchar](50) NULL,
    [country] [nvarchar](50) NULL,
    [province] [nvarchar](50) NULL,
    [city] [nvarchar](50) NULL
)

CREATE CLUSTERED INDEX [pvTblIdx] ON [dbo].[PageViewsTable]
(
    [eventTime] ASC
)WITH (PAD_INDEX = OFF, STATISTICS_NORECOMPUTE = OFF, SORT_IN_TEMPDB = OFF, DROP_EXISTING = OFF, ONLINE = OFF, ALLOW_ROW_LOCKS = ON, ALLOW_PAGE_LOCKS = ON)

```

![PageViewsTable maken](./media/code-sample-export-sql-stream-analytics/34-create-table.png)

In dit voor beeld gebruiken we gegevens uit pagina weergaven. Als u de overige beschik bare gegevens wilt bekijken, inspecteert u de JSON-uitvoer en bekijkt u het [gegevens model exporteren](./export-data-model.md).

## <a name="create-an-azure-stream-analytics-instance"></a>Een Azure Stream Analytics-exemplaar maken
Selecteer in de [Azure Portal](https://portal.azure.com/)de Azure stream Analytics-service en maak een nieuwe stream Analytics taak:

![In de scherm afbeelding wordt de pagina stream Analytics-taak weer gegeven met de knop maken gemarkeerd.](./media/code-sample-export-sql-stream-analytics/SA001.png)

![Nieuwe stream Analytics-taak](./media/code-sample-export-sql-stream-analytics/SA002.png)

Wanneer de nieuwe taak is gemaakt, selecteert **u naar resource**.

![Scherm afbeelding ziet u het bericht implementatie voltooid en gaat u naar de knop resource.](./media/code-sample-export-sql-stream-analytics/SA003.png)

#### <a name="add-a-new-input"></a>Een nieuwe invoer toevoegen

![Scherm afbeelding toont de pagina invoer met de knop toevoegen geselecteerd.](./media/code-sample-export-sql-stream-analytics/SA004.png)

Stel deze waarde in om in te voeren op basis van de doorlopende export-blob:

![Scherm afbeelding toont het nieuwe invoer venster met opties voor de vervolg keuzelijst invoer alias, bron en opslag account geselecteerd.](./media/code-sample-export-sql-stream-analytics/SA0005.png)

Nu hebt u de primaire toegangs sleutel van uw opslag account nodig die u eerder hebt genoteerd. Stel dit in als de sleutel voor het opslag account.

#### <a name="set-path-prefix-pattern"></a>Patroon voor voegsel van pad instellen

**Zorg ervoor dat u de datum notatie instelt op JJJJ-MM-DD (met streepjes).**

Het voor voegsel van het pad geeft aan hoe Stream Analytics de invoer bestanden in de opslag locatie vindt. U moet deze instellen op overeenkomen met de manier waarop continue export de gegevens opslaat. Stel deze als volgt in:

```sql
webapplication27_12345678123412341234123456789abcdef0/PageViews/{date}/{time}
```

In dit voorbeeld:

* `webapplication27` is de naam van de Application Insights resource, **in kleine letters**. 
* `1234...` is de instrumentatie sleutel van de Application Insights resource **waarvoor streepjes zijn verwijderd**. 
* `PageViews` is het type gegevens dat we willen analyseren. Welke typen beschikbaar zijn, is afhankelijk van het filter dat u in continue export hebt ingesteld. Controleer de geëxporteerde gegevens om de andere beschik bare typen te bekijken en Bekijk het [gegevens model exporteren](./export-data-model.md).
* `/{date}/{time}` is een patroon dat letterlijk is geschreven.

Als u de naam en iKey van uw Application Insights resource wilt ophalen, opent u essentiële elementen op de pagina overzicht of opent u de instellingen.

> [!TIP]
> Gebruik de voorbeeld functie om te controleren of u het pad naar de invoer juist hebt ingesteld. Als dit mislukt: Controleer of er gegevens in de opslag ruimte zijn voor het geselecteerde tijds bereik van de steek proef. Bewerk de invoer definitie en controleer of u het opslag account, het voor voegsel en de datum notatie juist hebt ingesteld.

 
## <a name="set-query"></a>Query instellen
Open de sectie query:

Vervang de standaard query door:

```SQL

    SELECT flat.ArrayValue.name as pageName
    , flat.ArrayValue.count as viewCount
    , flat.ArrayValue.url as url
    , flat.ArrayValue.urlData.port as urlDataPort
    , flat.ArrayValue.urlData.protocol as urlDataprotocol
    , flat.ArrayValue.urlData.host as urlDataHost
    , flat.ArrayValue.urlData.base as urlDataBase
    , flat.ArrayValue.urlData.hashTag as urlDataHashTag
      ,A.context.data.eventTime as eventTime
      ,A.context.data.isSynthetic as isSynthetic
      ,A.context.device.id as deviceId
      ,A.context.device.type as deviceType
      ,A.context.device.os as os
      ,A.context.device.osVersion as osVersion
      ,A.context.device.locale as locale
      ,A.context.device.userAgent as userAgent
      ,A.context.device.browser as browser
      ,A.context.device.browserVersion as browserVersion
      ,A.context.device.screenResolution.value as screenResolution
      ,A.context.session.id as sessionId
      ,A.context.session.isFirst as sessionIsFirst
      ,A.context.location.clientip as clientIp
      ,A.context.location.continent as continent
      ,A.context.location.country as country
      ,A.context.location.province as province
      ,A.context.location.city as city
    INTO
      AIOutput
    FROM AIinput A
    CROSS APPLY GetElements(A.[view]) as flat


```

U ziet dat de eerste paar eigenschappen specifiek zijn voor pagina weergave gegevens. De export van andere telemetrie-typen heeft andere eigenschappen. Zie de [Naslag informatie over het gedetailleerde gegevens model voor de eigenschaps typen en-waarden.](./export-data-model.md)

## <a name="set-up-output-to-database"></a>Uitvoer instellen op Data Base
Selecteer SQL als uitvoer.

![Selecteer in stream Analytics uitvoer](./media/code-sample-export-sql-stream-analytics/SA006.png)

Geef de Data Base op.

![Vul de details van de data base in](./media/code-sample-export-sql-stream-analytics/SA007.png)

Sluit de wizard en wacht op een melding dat de uitvoer is ingesteld.

## <a name="start-processing"></a>Verwerking starten
De taak starten vanuit de actie balk:

![Klik in stream Analytics op Start](./media/code-sample-export-sql-stream-analytics/SA008.png)

U kunt kiezen of u wilt beginnen met het verwerken van de gegevens vanaf nu, of om te beginnen met eerdere gegevens. De laatste is handig als u voortdurend een continue export hebt uitgevoerd.

Na een paar minuten gaat u terug naar SQL Server Management Tools en bekijkt u de gegevens stromen in. Gebruik bijvoorbeeld een query als volgt:

```sql
SELECT TOP 100 *
FROM [dbo].[PageViewsTable]
```

## <a name="related-articles"></a>Verwante artikelen:
* [Exporteren naar Power BI met behulp van Stream Analytics](./export-power-bi.md)
* [gedetailleerde gegevens model verwijzing voor de eigenschaps typen en-waarden.](./export-data-model.md)
* [Continue export in Application Insights](./export-telemetry.md)
* [Application Insights](https://azure.microsoft.com/services/application-insights/)

<!--Link references-->

[diagnostic]: ./diagnostic-search.md
[export]: ./export-telemetry.md
[metrics]: ../essentials/metrics-charts.md
[portal]: https://portal.azure.com/
[start]: ./app-insights-overview.md

