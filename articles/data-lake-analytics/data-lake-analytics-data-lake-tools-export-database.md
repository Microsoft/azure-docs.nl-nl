---
title: Export U-SQL database-Azure Data Lake-Hulpprogram Ma's voor Visual Studio
description: Meer informatie over het gebruik van Azure Data Lake-Hulpprogram Ma's voor Visual Studio voor het exporteren van een U-SQL database en om deze automatisch te importeren in een lokaal account.
ms.reviewer: jasonh
ms.service: data-lake-analytics
ms.topic: how-to
ms.date: 11/27/2017
ms.openlocfilehash: 0a768990a33f0d60a3222bd3ad323edcc53c9ec0
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92219085"
---
# <a name="export-a-u-sql-database"></a>Een U-SQL database exporteren

In dit artikel leert u hoe u [Azure data Lake-Hulpprogram ma's voor Visual Studio](https://aka.ms/adltoolsvs) kunt gebruiken om een u-SQL database te exporteren als één u-SQL-script en gedownloade resources. U kunt de geëxporteerde data base in hetzelfde proces importeren naar een lokaal account.

Klanten onderhouden doorgaans meerdere omgevingen voor ontwikkeling, testen en productie. Deze omgevingen worden gehost op een lokaal account, op de lokale computer van een ontwikkelaar en in een Azure Data Lake Analytics-account in Azure. 

Wanneer u u-SQL-query's ontwikkelt en afstemt in ontwikkel-en test omgevingen, moeten ontwikkel aars hun werk vaak opnieuw in een productie database maken. De wizard Data Base exporteren helpt dit proces te versnellen. Met behulp van de wizard kunnen ontwikkel aars de bestaande database omgeving en voorbeeld gegevens klonen naar andere Data Lake Analytics accounts.

## <a name="export-steps"></a>Stappen exporteren

### <a name="step-1-export-the-database-in-server-explorer"></a>Stap 1: de data base in Server Explorer exporteren

Alle Data Lake Analytics accounts waarvoor u machtigingen hebt, worden weer gegeven in Server Explorer. De data base exporteren:

1. Vouw in Server Explorer het account uit dat de Data Base bevat die u wilt exporteren.
2. Klik met de rechter muisknop op de data base en selecteer vervolgens **exporteren**. 
   
    ![Server Explorer: een Data Base exporteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database.png)

     Als de menu optie **exporteren** niet beschikbaar is, moet u [het hulp programma bijwerken naar de laatste release](https://aka.ms/adltoolsvs).

### <a name="step-2-configure-the-objects-that-you-want-to-export"></a>Stap 2: Configureer de objecten die u wilt exporteren

Als u slechts een klein deel van een grote data base nodig hebt, kunt u een subset van objecten configureren die u wilt exporteren in de wizard exporteren. 

De export actie wordt voltooid door een U-SQL-taak uit te voeren. Daarom is het exporteren van een Azure-account kosten in rekening gebracht.

![Wizard Data Base exporteren-Selecteer objecten exporteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard.png)

### <a name="step-3-check-the-objects-list-and-other-configurations"></a>Stap 3: de lijst met objecten en andere configuraties controleren

In deze stap kunt u de geselecteerde objecten in de **keuze lijst export object** controleren. Als er fouten zijn, selecteert u **vorige** om terug te gaan en de objecten die u wilt exporteren correct te configureren.

U kunt ook andere instellingen voor het export doel configureren. Configuratie beschrijvingen worden weer gegeven in de volgende tabel:

|Configuratie|Beschrijving|
|-------------|-----------|
|Doel naam|Met deze naam wordt aangegeven waar u de geëxporteerde database resources wilt opslaan. Voor beelden hiervan zijn assembly's, extra bestanden en voorbeeld gegevens. Er wordt een map met deze naam gemaakt in de hoofdmap van uw lokale data root.|
|Projectmap|Dit pad definieert waar u het geëxporteerde U-SQL-script wilt opslaan. Alle database object definities worden op deze locatie opgeslagen.|
|Alleen schema|Als u deze optie selecteert, worden alleen database definities en resources (zoals assembly's en aanvullende bestanden) geëxporteerd.|
|Schema en gegevens|Als u deze optie selecteert, worden database definities, resources en gegevens geëxporteerd. De bovenste N rijen met tabellen worden geëxporteerd.|
|Automatisch importeren in lokale data base|Als u deze optie selecteert, wordt de geëxporteerde data base automatisch geïmporteerd in uw lokale data base wanneer het exporteren is voltooid.|

![Wizard Data Base exporteren-lijst met objecten exporteren en andere configuraties](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-configuration.png)

### <a name="step-4-check-the-export-results"></a>Stap 4: de export resultaten controleren

Wanneer het exporteren is voltooid, kunt u de geëxporteerde resultaten weer geven in het logboek venster in de wizard. In het volgende voor beeld ziet u hoe u een geëxporteerde-SQL-script en database bronnen kunt vinden, inclusief assembly's, aanvullende bestanden en voorbeeld gegevens:

![Wizard Data Base exporteren-resultaten exporteren](./media/data-lake-analytics-data-lake-tools-export-database/export-database-wizard-completed.png)

## <a name="import-the-exported-database-to-a-local-account"></a>De geëxporteerde data base importeren naar een lokaal account

De meest geschikte manier om de geëxporteerde data base te importeren is door het selectie vakje **naar lokale data base automatisch importeren** tijdens het export proces in stap 3 te selecteren. Als u dit selectie vakje niet hebt ingeschakeld, gaat u eerst naar het geëxporteerde U-SQL-script in het export logboek. Voer vervolgens het U-SQL-script lokaal uit om de data base te importeren in uw lokale account.

## <a name="import-the-exported-database-to-a-data-lake-analytics-account"></a>De geëxporteerde data base importeren in een Data Lake Analytics-account

De data base importeren in een andere Data Lake Analytics account:

1. Upload de geëxporteerde resources, inclusief de assembly's, aanvullende bestanden en voorbeeld gegevens, naar de standaard Azure Data Lake Store account van het Data Lake Analytics account waarnaar u wilt importeren. De map met de geëxporteerde resources is te vinden in de hoofdmap van lokale gegevens. Upload de volledige map naar de hoofdmap van het standaard account Data Lake Store.
2. Wanneer het uploaden is voltooid, verzendt u het geëxporteerde U-SQL-script naar het Data Lake Analytics-account waarnaar u de Data Base wilt importeren.

## <a name="known-limitations"></a>Bekende beperkingen

Als u op dit moment de optie **schema en gegevens** in stap 3 selecteert, wordt met het hulp programma een U-SQL-taak uitgevoerd voor het exporteren van de gegevens die zijn opgeslagen in tabellen. Als gevolg hiervan kan het proces voor het exporteren van gegevens traag zijn en kunnen er kosten in rekening worden gebracht. 

## <a name="next-steps"></a>Volgende stappen

* [Meer informatie over U-SQL-data bases](/u-sql/data-definition-language-ddl-statements) 
* [U-SQL-taken testen en controleren op fouten met behulp van lokale uitvoering en de Azure Data Lake U-SQL-SDK](data-lake-analytics-data-lake-tools-local-run.md)


