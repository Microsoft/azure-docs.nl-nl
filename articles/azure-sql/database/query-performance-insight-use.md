---
title: Inzicht in queryprestaties
description: De controle van de query prestaties identificeert de meeste CPU-verbruikte en langlopende query's voor één en gepoolde data bases in Azure SQL Database.
services: sql-database
ms.service: sql-database
ms.subservice: performance
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: how-to
author: danimir
ms.author: danil
ms.reviewer: wiassaf, sstein
ms.date: 03/10/2020
ms.openlocfilehash: a016781e5b05c8f43c043bf1f3368a58064e43ad
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96501257"
---
# <a name="query-performance-insight-for-azure-sql-database"></a>Query Performance Insight voor Azure SQL Database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Query Performance Insight biedt intelligente queryanalyses voor enkelvoudige en pooldatabases. Dit helpt bij het identificeren van de belangrijkste en langlopende query's in uw workload. Zo kunt u de query's vinden die u wilt optimaliseren om de algehele prestaties van de workload te verbeteren en de resource waarvoor u betaalt efficiënt te gebruiken. Query Performance Insight helpt u minder tijd te best Eden aan het oplossen van problemen met database prestaties.

* Dieper inzicht in uw data bases resource (DTU)-verbruik
* Details over de belangrijkste database query's op basis van CPU, duur en aantal uitvoeringen (potentiële afstemmings kandidaten voor prestatie verbeteringen)
* De mogelijkheid om in te zoomen op de details van een query om de query tekst en geschiedenis van het resource gebruik weer te geven
* Aantekeningen die prestatie aanbevelingen van [Data Base Advisor](database-advisor-implement-performance-recommendations.md) weer geven

![Inzicht in queryprestaties](./media/query-performance-insight-use/opening-title.png)

## <a name="prerequisites"></a>Vereisten

Voor Query Performance Insight moet het [query archief](/sql/relational-databases/performance/monitoring-performance-by-using-the-query-store) actief zijn in uw data base. De standaard instelling is automatisch ingeschakeld voor alle data bases in Azure SQL Database. Als query Store niet wordt uitgevoerd, wordt u door de Azure Portal gevraagd dit in te scha kelen.

> [!NOTE]
> Zie [de configuratie van het query archief optimaliseren](#optimize-the-query-store-configuration)als het bericht ' query archief is niet juist is geconfigureerd voor deze data base ' wordt weer gegeven in de portal.

## <a name="permissions"></a>Machtigingen

U hebt de volgende machtigingen voor [Azure op rollen gebaseerde toegangs beheer (Azure RBAC)](../../role-based-access-control/overview.md) nodig voor het gebruik van query Performance Insight:

* De machtigingen **lezer**, **eigenaar**, **Inzender**, **SQL DB-Inzender** of **SQL Server Inzender** zijn vereist voor het weer geven van de meest voorkomende query's en grafieken van de resource.
* De machtigingen **eigenaar**, **bijdrager**, **SQL DB-Inzender** of **SQL Server Inzender** zijn vereist om query tekst weer te geven.

## <a name="use-query-performance-insight"></a>Query Performance Insight gebruiken

Query Performance Insight is gemakkelijk te gebruiken:

1. Open de [Azure Portal](https://portal.azure.com/) en zoek een Data Base die u wilt bekijken.
2. Open in het menu aan de linkerkant de optie **intelligent prestaties**  >  **query Performance Insight**.
  
   ![Query Performance Insight in het menu](./media/query-performance-insight-use/tile.png)

3. Bekijk op het eerste tabblad de lijst met belangrijkste query's in de resource.
4. Selecteer een afzonderlijke query om de details ervan weer te geven.
5. Open **intelligente prestatie**  >  **aanbevelingen** en controleer of er aanbevelingen voor prestaties beschikbaar zijn. Zie [Azure SQL database Advisor](database-advisor-implement-performance-recommendations.md)voor meer informatie over ingebouwde prestatie aanbevelingen.
6. Gebruik schuif regelaars of Zoom pictogrammen om het waargenomen interval te wijzigen.

   ![Prestatie dashboard](./media/query-performance-insight-use/performance.png)

> [!NOTE]
> Als Azure SQL Database de gegevens in Query Performance Insight wilt weer geven, moet u in de query Store enkele uren aan gegevens vastleggen. Als de data base geen activiteit heeft of als de query Store niet actief was tijdens een bepaalde periode, zijn de grafieken leeg wanneer Query Performance Insight dat tijds bereik weergeeft. U kunt het query archief op elk gewenst moment inschakelen als het niet wordt uitgevoerd. Zie [Aanbevolen procedures voor query Store](/sql/relational-databases/performance/best-practice-with-the-query-store)voor meer informatie.
>

Voor aanbevelingen voor database prestaties selecteert u [aanbevelingen](database-advisor-implement-performance-recommendations.md) op de blade query Performance Insight navigatie.

![Het tabblad aanbevelingen](./media/query-performance-insight-use/ia.png)

## <a name="review-top-cpu-consuming-queries"></a>Best presterende query's voor CPU-verbruik controleren

Query Performance Insight worden standaard de vijf meest voorkomende query's van CPU-verbruik weer gegeven wanneer u deze voor het eerst opent.

1. Selecteer of wis afzonderlijke query's om deze op te nemen of uit te sluiten van het diagram met behulp van selectie vakjes.

   De bovenste regel bevat het totale DTU-percentage voor de data base. De balken tonen het CPU-percentage dat de geselecteerde query's tijdens het geselecteerde interval hebben verbruikt. Als bijvoorbeeld **afgelopen week** is geselecteerd, vertegenwoordigt elke balk één dag.

   ![Meest voorkomende query's](./media/query-performance-insight-use/top-queries.png)

   > [!IMPORTANT]
   > De weer gegeven DTU-regel wordt geaggregeerd naar een maximum verbruiks waarde in peri Oden van één uur. Het is alleen bedoeld voor een vergelijking op hoog niveau met statistieken voor query-uitvoering. In sommige gevallen lijkt het DTU-gebruik te hoog ten opzichte van uitgevoerde query's, maar dit is mogelijk niet het geval.
   >
   > Als een query bijvoorbeeld gedurende een paar minuten benut uitvalt naar 100%, wordt in de DTU-regel in Query Performance Insight het hele uur verbruik weer gegeven als 100% (het gevolg van de Maxi maal geaggregeerde waarde).
   >
   > Voor een nauw keurigere vergelijking (Maxi maal één minuut) kunt u een aangepast DTU-gebruiks diagram maken:
   >
   > 1. Selecteer **Azure SQL database**  >  **bewaking** in het Azure Portal.
   > 2. Selecteer **Metrische gegevens**.
   > 3. Selecteer **+ grafiek toevoegen**.
   > 4. Selecteer het DTU-percentage in de grafiek.
   > 5. Selecteer bovendien **laatste 24 uur** in het menu linksboven en wijzig deze in één minuut.
   >
   > Gebruik het aangepaste DTU-diagram met een nauw keuriger niveau om te vergelijken met de uitvoerings grafiek van de query.

   In het onderste raster worden geaggregeerde gegevens weer gegeven voor de zicht bare query's:

   * Query-ID, een unieke id voor de query in de data base.
   * CPU per query gedurende een waarneembaar interval dat afhankelijk is van de aggregatie functie.
   * De duur per query, die ook afhankelijk is van de aggregatie functie.
   * Totaal aantal uitvoeringen voor een specifieke query.

2. Als uw gegevens verouderd worden, selecteert u de knop **vernieuwen** .

3. Gebruik schuif regelaars en Zoom knoppen om het observatie interval te wijzigen en pieken in het gebruik te onderzoeken:

   ![Schuif regelaars en Zoom knoppen voor het wijzigen van het interval](./media/query-performance-insight-use/zoom.png)

4. Desgewenst kunt u het **aangepaste** tabblad selecteren om de weer gave aan te passen voor:

   * Metric (CPU, duur, aantal uitvoeringen).
   * Tijds interval (laatste 24 uur, afgelopen week of vorige maand).
   * Aantal query's.
   * Aggregatie functie.
  
   ![Aangepast tabblad](./media/query-performance-insight-use/custom-tab.png)
  
5. Selecteer de knop **ga >** om de aangepaste weer gave te bekijken.

   > [!IMPORTANT]
   > Query Performance Insight is beperkt tot het weer geven van de beste 5-20 verbruiks query's, afhankelijk van uw selectie. Uw data base kan veel meer query's uitvoeren dan de items die worden weer gegeven en deze query's worden niet opgenomen in de grafiek.
   >
   > Het is mogelijk dat er een type werk belasting voor de data base is waarin veel kleinere query's worden weer gegeven. Dit kan vaak worden uitgevoerd en de meeste DTU worden gebruikt. Deze query's worden niet weer gegeven in de prestatie grafiek.
   >
   > Een query kan bijvoorbeeld een aanzienlijke hoeveelheid DTU voor een tijdje hebben verbruikt, hoewel het totale verbruik in de waargenomen periode kleiner is dan de andere best presterende query's. In dat geval wordt het resource gebruik van deze query niet weer gegeven in de grafiek.
   >
   > Als u de meest voorkomende resultaten Query Performance Insight van de uitvoering van de query wilt begrijpen, kunt u [Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) gebruiken voor geavanceerde database prestaties bewaken en probleem oplossing.
   >

## <a name="view-individual-query-details"></a>Details van afzonderlijke query's weer geven

Query details weer geven:

1. Selecteer een query in de lijst met meest voorkomende query's.

    ![Lijst met meest voorkomende query's](./media/query-performance-insight-use/details.png)

   Er wordt een gedetailleerde weer gave geopend. Hier worden het CPU-verbruik, de duur en het aantal uitvoeringen in de loop van de tijd weer gegeven.

2. Selecteer de grafiek functies voor meer informatie.

   * In het bovenste diagram ziet u een regel met het totale data base-DTU-percentage. De staven zijn het CPU-percentage waarmee de geselecteerde query is verbruikt.
   * In de tweede grafiek wordt de totale duur van de geselecteerde query weer gegeven.
   * Het onderste diagram toont het totale aantal uitvoeringen door de geselecteerde query.

   ![Query Details](./media/query-performance-insight-use/query-details.png)

3. U kunt ook schuif regelaars gebruiken, Zoom knoppen gebruiken of **instellingen** selecteren om aan te passen hoe query gegevens worden weer gegeven of om een ander tijds bereik te kiezen.

   > [!IMPORTANT]
   > Met Query Performance Insight worden geen DDL-query's vastgelegd. In sommige gevallen worden niet alle ad hoc-query's vastgelegd.
   >

## <a name="review-top-queries-per-duration"></a>Top-query's per duur controleren

Met twee metrische gegevens in Query Performance Insight kunt u mogelijke knel punten vinden: duur en aantal uitvoeringen.

Langlopende query's hebben de grootste mogelijkheid om bronnen langer te vergren delen, andere gebruikers te blok keren en de schaal baarheid te beperken. Ze zijn ook de beste kandidaten voor Optima Lise ring.

Langlopende query's identificeren:

1. Open het tabblad **aangepast** in query Performance Insight voor de geselecteerde data base.
2. Wijzig de metrische gegevens in **duur**.
3. Het aantal query's en het observatie interval selecteren.
4. Selecteer de aggregatie functie:

   * Met **Sum** wordt alle uitvoerings tijd van de query voor het hele observatie interval opgeteld.
   * **Max** . Hiermee zoekt u query's waarin de uitvoerings tijd het maximum is voor het hele observatie interval.
   * **Gem** vindt de gemiddelde uitvoerings tijd van alle uitvoeringen van query's en toont u de bovenste voor deze gemiddelden.

   ![Query duur](./media/query-performance-insight-use/top-duration.png)

5. Selecteer de knop **ga >** om de aangepaste weer gave te bekijken.

   > [!IMPORTANT]
   > Als de query weergave wordt aangepast, wordt de DTU-regel niet bijgewerkt. De DTU-regel bevat altijd de waarde voor het maximum verbruik voor het interval.
   >
   > Als u meer informatie wilt over het gebruik van data base-DTU met meer details (Maxi maal één minuut), kunt u een aangepaste grafiek maken in de Azure Portal:
   >
   > 1. Selecteer **Azure SQL database**  >  **bewaking**.
   > 2. Selecteer **Metrische gegevens**.
   > 3. Selecteer **+ grafiek toevoegen**.
   > 4. Selecteer het DTU-percentage in de grafiek.
   > 5. Selecteer bovendien **laatste 24 uur** in het menu linksboven en wijzig deze in één minuut.
   >
   > U wordt aangeraden het aangepaste DTU-diagram te gebruiken om te vergelijken met het diagram voor query prestaties.
   >

## <a name="review-top-queries-per-execution-count"></a>Belangrijkste query's per aantal uitvoeringen controleren

Een gebruikers toepassing die de data base gebruikt, kan traag worden, zelfs als een groot aantal uitvoeringen mogelijk niet van invloed is op de data base zelf en het gebruik van resources laag is.

In sommige gevallen kan een hoog aantal uitvoeringen leiden tot meer netwerk round trips. Afrondingen zijn van invloed op de prestaties. Ze zijn onderworpen aan de netwerk latentie en de downstream-server latentie.

Veel gegevensgestuurde websites hebben bijvoorbeeld een zeer grote toegang tot de Data Base voor elke gebruikers aanvraag. Hoewel het groeperen van verbindingen helpt, kan de prestaties van het netwerk verkeer en de verwerkings belasting op de server vertragen. In het algemeen moet u een minimum afronden.

Voor het identificeren van regel matig uitgevoerde query's (' intensieve '):

1. Open het tabblad **aangepast** in query Performance Insight voor de geselecteerde data base.
2. Wijzig de metrische gegevens in het **aantal uitvoeringen**.
3. Het aantal query's en het observatie interval selecteren.
4. Selecteer de knop **ga >** om de aangepaste weer gave te bekijken.

   ![Aantal uitgevoerde query's](./media/query-performance-insight-use/top-execution.png)

## <a name="understand-performance-tuning-annotations"></a>Aantekeningen over prestaties afstemmen

Bij het verkennen van uw werk belasting in Query Performance Insight kunt u pictogrammen met een verticale lijn boven op de grafiek opmerken.

Deze pictogrammen zijn annotaties. Er worden prestatie aanbevelingen van [Azure SQL database Advisor](database-advisor-implement-performance-recommendations.md)weer gegeven. Als u over een aantekening beweegt, kunt u een overzicht krijgen van de aanbevelingen voor prestaties.

   ![Query annotatie](./media/query-performance-insight-use/annotation.png)

Als u meer wilt weten of de aanbeveling van Advisor wilt Toep assen, selecteert u het pictogram om de details van de aanbevolen actie te openen. Als dit een actieve aanbeveling is, kunt u deze direct vanuit de portal Toep assen.

   ![Details van de query annotatie](./media/query-performance-insight-use/annotation-details.png)

In sommige gevallen, vanwege het zoom niveau, is het mogelijk dat annotaties dicht bij elkaar worden samengevouwen tot één aantekening. Query Performance Insight vertegenwoordigt dit pictogram voor een groeps aantekening. Als u het pictogram voor de aantekening groep selecteert, wordt er een nieuwe blade met de aantekeningen weer gegeven.

Het correleren van query's en acties voor het afstemmen van prestaties kunnen u helpen beter inzicht te krijgen in uw werk belasting.

## <a name="optimize-the-query-store-configuration"></a>De configuratie van het query archief optimaliseren

Bij het gebruik van Query Performance Insight ziet u mogelijk de volgende fout berichten in het query archief:

* Query Store is niet juist geconfigureerd voor deze data base. Klik hier voor meer informatie.
* Query Store is niet juist geconfigureerd voor deze data base. Klik hier om instellingen te wijzigen. "

Deze berichten worden meestal weer gegeven wanneer de query Store geen nieuwe gegevens kan verzamelen.

De eerste geval treedt op wanneer query Store de status alleen-lezen heeft en de para meters optimaal zijn ingesteld. U kunt dit probleem verhelpen door de grootte van het gegevens archief te verhogen of door query Store uit te scha kelen. (Als u query Store wist, gaan alle eerder verzamelde telemetrie verloren.)

   ![Details van query Store](./media/query-performance-insight-use/qds-off.png)

Het tweede geval treedt op wanneer query Store niet is ingeschakeld, of para meters zijn niet optimaal ingesteld. U kunt het retentie-en vastleg beleid wijzigen en ook query Store inschakelen door de volgende opdrachten uit [SQL Server Management Studio (SSMS)](/sql/ssms/download-sql-server-management-studio-ssms) of de Azure Portal uit te voeren.

### <a name="recommended-retention-and-capture-policy"></a>Aanbevolen Bewaar-en vastleg beleid

Er zijn twee typen Bewaar beleid:

* **Op basis van grootte**: als dit beleid is ingesteld op **automatisch**, worden de gegevens automatisch opgeschoond wanneer de maximale grootte wordt bereikt.
* **Op basis van tijd**: standaard is dit beleid ingesteld op 30 dagen. Als er geen ruimte meer is op de query opslag, worden de query gegevens ouder dan 30 dagen verwijderd.

U kunt het vastleg beleid instellen op:

* **Alle**: in query Store worden alle query's vastgelegd.
* **Automatisch**: in query Store worden niet-frequente query's en query's met een onbeduidende compilatie-en uitvoerings duur genegeerd. De drempel waarden voor het aantal uitvoeringen, de compilatie duur en de runtime duur worden intern bepaald. Dit is de standaard optie.
* **Geen**: de query Store stopt met het vastleggen van nieuwe query's, maar er worden nog steeds runtime statistieken voor al vastgelegde query's verzameld.

U kunt het beste alle beleids regels instellen op **automatisch** en het opschonings beleid tot 30 dagen door de volgende opdrachten uit [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) of de Azure Portal uit te voeren. (Vervang door `YourDB` de naam van de data base.)

```sql
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (SIZE_BASED_CLEANUP_MODE = AUTO);

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (CLEANUP_POLICY = (STALE_QUERY_THRESHOLD_DAYS = 30));

    ALTER DATABASE [YourDB]
    SET QUERY_STORE (QUERY_CAPTURE_MODE = AUTO);
```

Verg root de grootte van het query Archief door verbinding te maken met een Data Base via [SSMS](/sql/ssms/download-sql-server-management-studio-ssms) of het Azure Portal en de volgende query uit te voeren. (Vervang door `YourDB` de naam van de data base.)

```SQL
    ALTER DATABASE [YourDB]
    SET QUERY_STORE (MAX_STORAGE_SIZE_MB = 1024);
```

Als u deze instellingen toepast, wordt de query Store uiteindelijk telemetrie verzameld voor nieuwe query's. Als u wilt dat het query archief direct operationeel is, kunt u ervoor kiezen om query Store te wissen door de volgende query uit te voeren via SSMS of de Azure Portal. (Vervang door `YourDB` de naam van de data base.)

> [!NOTE]
> Als u de volgende query uitvoert, worden alle eerder verzamelde telemetrie in query Store verwijderd.

```SQL
    ALTER DATABASE [YourDB] SET QUERY_STORE CLEAR;
```

## <a name="next-steps"></a>Volgende stappen

Overweeg het gebruik van [Azure SQL-analyse](../../azure-monitor/insights/azure-sql.md) voor geavanceerde prestatie bewaking van een grote vloot van enkelvoudige en gepoolde data bases, elastische Pools, beheerde exemplaren en data bases van instanties.