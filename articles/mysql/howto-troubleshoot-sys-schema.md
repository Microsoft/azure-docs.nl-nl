---
title: Sys_schema-Azure Database for MySQL gebruiken
description: Meer informatie over het gebruik van sys_schema om prestatie problemen op te sporen en data base in Azure Database for MySQL te onderhouden.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: troubleshooting
ms.date: 3/30/2020
ms.openlocfilehash: 939d9f5f4cafc7a08ceb99368e8ede7a81b1eac0
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105110522"
---
# <a name="how-to-use-sys_schema-for-performance-tuning-and-database-maintenance-in-azure-database-for-mysql"></a>Sys_schema gebruiken voor het afstemmen van prestaties en het beheren van data bases in Azure Database for MySQL
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

De MySQL-performance_schema, die voor het eerst beschikbaar is in MySQL 5,5, biedt instrumentatie voor veel cruciale Server bronnen, zoals geheugen toewijzing, opgeslagen Program ma's, vergren deling van meta gegevens, enzovoort. De performance_schema bevat echter meer dan 80 tabellen en het ophalen van de benodigde gegevens vereist vaak join-tabellen in de performance_schema, evenals de tabellen uit de information_schema. De sys_schema biedt op zowel performance_schema als information_schema een krachtige verzameling [gebruiks vriendelijke weer gaven](https://dev.mysql.com/doc/refman/5.7/en/sys-schema-views.html) in een alleen-lezen data base en is volledig ingeschakeld in Azure Database for MySQL versie 5,7.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/sys-schema-views.png" alt-text="weer gaven van sys_schema":::

De sys_schema bevat 52 weer gaven en elke weer gave heeft een van de volgende voor voegsels:

- Host_summary of IO: aan I/O gerelateerde latenties.
- InnoDB: InnoDB buffer status en vergren delingen.
- Geheugen: geheugen gebruik door de host en gebruikers.
- Schema: informatie die betrekking heeft op schema, zoals automatische toename, indexen, enzovoort.
- Instructie: informatie over SQL-instructies; Dit kan een instructie zijn die de volledige tabel scan heeft veroorzaakt, of een lange query tijd.
- Gebruiker: resources die worden verbruikt en gegroepeerd door gebruikers. Voor beelden hiervan zijn bestands-I/O's, verbindingen en geheugen.
- Wachten: wacht gebeurtenissen gegroepeerd op host of gebruiker.

Laten we nu eens kijken naar enkele veelvoorkomende gebruiks patronen van de sys_schema. Om te beginnen, groeperen we de gebruiks patronen in twee categorieën: **prestaties afstemmen** en **database onderhoud**.

## <a name="performance-tuning"></a>Prestaties afstemmen

### <a name="sysuser_summary_by_file_io"></a>*sys.user_summary_by_file_io*

IO is de duurste bewerking in de-data base. We kunnen de gemiddelde IO-latentie ontdekken door de *sys.user_summary_by_file_io* weer gave te doorzoeken. Met de standaard 125 GB aan ingerichte opslag ruimte, is mijn i/o-latentie ongeveer 15 seconden.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-125GB.png" alt-text="io-latentie: 125 GB":::

Omdat Azure Database for MySQL IO inschaalt ten opzichte van opslag, vermindert mijn i/o-wacht tijd tot 571 MS.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/io-latency-1TB.png" alt-text="io-latentie: 1 TB":::

### <a name="sysschema_tables_with_full_table_scans"></a>*sys.schema_tables_with_full_table_scans*

Ondanks een zorgvuldige planning kunnen veel query's nog steeds leiden tot volledige tabel scans. Raadpleeg dit artikel voor meer informatie over de typen indexen en hoe u deze kunt optimaliseren: [problemen met query prestaties oplossen](./howto-troubleshoot-query-performance.md). Volledige tabel scans zijn resource-intensief en verminderen de prestaties van uw data base. De snelste manier om tabellen met volledige tabel scan te vinden, is door de *sys.schema_tables_with_full_table_scans* weer gave te doorzoeken.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/full-table-scans.png" alt-text="volledige tabel scans":::

### <a name="sysuser_summary_by_statement_type"></a>*sys.user_summary_by_statement_type*

Om problemen met de database prestaties op te lossen, kan het nuttig zijn om de gebeurtenissen te identificeren die zich in uw data base voordoen. Als u de *sys.user_summary_by_statement_type* weer gave gebruikt, kan de slag gewoon doen.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/summary-by-statement.png" alt-text="samen vatting per overzicht":::

In dit voor beeld Azure Database for MySQL 53 minuten besteed aan het leegmaken van het slog-query logboek 44579 keer. Dat is een lange tijd en veel IOs. U kunt deze activiteit verminderen door uw logboek voor trage query's uit te scha kelen of de frequentie van langzame query's te verlagen Azure Portal.

## <a name="database-maintenance"></a>Database onderhoud

### <a name="sysinnodb_buffer_stats_by_table"></a>*sys.innodb_buffer_stats_by_table*

[!IMPORTANT]
> Het uitvoeren van query's op deze weer gave kan invloed hebben op de prestaties. Het is raadzaam om deze problemen op te lossen tijdens kantoor uren buiten de piek.

De InnoDB-buffer groep bevindt zich in het geheugen en is het hoofd cache mechanisme tussen het DBMS en de opslag. De grootte van de buffer groep InnoDB is gekoppeld aan de prestatie-laag en kan alleen worden gewijzigd als er een andere product-SKU is gekozen. Net als bij geheugen in uw besturings systeem worden oude pagina's omgewisseld om ruimte te maken voor de gegevens van de nieuwer. Als u wilt weten welke tabellen het meeste geheugen van de InnoDB buffer gebruiken, kunt u een query uitvoeren op de weer gave *sys.innodb_buffer_stats_by_table* .

:::image type="content" source="./media/howto-troubleshoot-sys-schema/innodb-buffer-status.png" alt-text="Status van InnoDB-buffer":::

In de bovenstaande afbeelding ziet u dat naast de systeem tabellen en weer gaven, elke tabel in de mysqldatabase033-data base, die als host fungeert voor een van mijn WordPress-sites, 16 KB of 1 pagina van de gegevens in het geheugen in beslag neemt.

### <a name="sysschema_unused_indexes--sysschema_redundant_indexes"></a>*Sys.schema_unused_indexes* & *sys.schema_redundant_indexes*

Indexen zijn fantastische hulp middelen voor het verbeteren van de Lees prestaties, maar er zijn extra kosten in rekening gebracht voor toevoegingen en opslag. *Sys.schema_unused_indexes* en *sys.schema_redundant_indexes* inzicht geven in ongebruikte of dubbele indexen.

:::image type="content" source="./media/howto-troubleshoot-sys-schema/unused-indexes.png" alt-text="ongebruikte indexen":::

:::image type="content" source="./media/howto-troubleshoot-sys-schema/redundant-indexes.png" alt-text="redundante indexen":::

## <a name="conclusion"></a>Conclusie

In samen vatting is de sys_schema een geweldig hulp middel voor het afstemmen van prestaties en het onderhoud van de data base. Zorg ervoor dat u deze functie in uw Azure Database for MySQL kunt gebruiken. 

## <a name="next-steps"></a>Volgende stappen
- Ga naar [micro soft Q&een vraag pagina](/answers/topics/azure-database-mysql.html) of [stack overflow](https://stackoverflow.com/questions/tagged/azure-database-mysql)als u op zoek bent naar peer antwoorden op uw vragen of als u een nieuwe vraag/antwoord wilt plaatsen.
