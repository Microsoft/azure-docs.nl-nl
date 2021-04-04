---
title: 'Zelfstudie: Een SQLite-database migreren naar een serverloze Azure SQL-database'
description: Meer informatie over het uitvoeren van een offline migratie vanuit SQLite naar een serverloze Azure SQL-database met behulp van Azure Data Factory.
services: sql-database
author: joplum
ms.author: joplum
ms.service: sql-database
ms.workload: data-services
ms.topic: tutorial
ms.date: 01/08/2020
ms.custom: sqldbrb=1
ms.openlocfilehash: e2f240247cbba0f80254d504792df45be55c6a1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92790403"
---
# <a name="how-to-migrate-your-sqlite-database-to-azure-sql-database-serverless"></a>Een SQLite-database migreren naar een serverloze Azure SQL-database
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Voor veel mensen vormt SQLite hun eerste ervaring met databases en SQL-programmering. Omdat het is opgenomen in veel besturingssystemen en populaire toepassingen is SQLite een van de meest geïmplementeerde en gebruikte database-engines ter wereld. En omdat het vaak de eerste database-engine is die veel mensen gebruiken, vormt het vaak een centraal onderdeel van projecten of toepassingen. In dergelijke gevallen, wanneer het project of de toepassing uit die eerste SQLite-implementatie groeit, moeten ontwikkelaars hun gegevens misschien migreren naar een betrouwbare, gecentraliseerde gegevensopslag.

Een serverloze Azure SQL-database is een compute-laag voor afzonderlijke databases waarmee de compute-resources automatisch worden geschaald op basis van de eisen van de workload en er wordt gefactureerd op basis van de hoeveelheid rekenkracht die per seconde wordt gebruikt. De serverloze compute-laag pauzeert databases ook automatisch tijdens inactieve perioden, zodat er alleen opslag wordt gefactureerd, en hervat databases automatisch wanneer er weer activiteit plaatsvindt.

Als u de onderstaande stappen volgt, wordt uw database naar een serverloze Azure SQL-database gemigreerd, zodat u uw database beschikbaar kunt stellen aan andere gebruikers of toepassingen in de cloud en alleen betaalt voor wat u gebruikt, met minimale wijzigingen in de toepassingscode.

## <a name="prerequisites"></a>Vereisten

- Een Azure-abonnement
- Een SQLite2- of SQLite3-database die u wilt migreren
- Een Windows-omgeving
  - Als u geen lokale Windows-omgeving hebt, kunt u een virtuele Windows-machine in Azure gebruiken voor de migratie. Verplaats en maak uw SQLite-databasebestand beschikbaar op de VM met behulp van Azure Files en Storage Explorer.

## <a name="steps"></a>Stappen

1. Richt een nieuwe Azure SQL-database in op de serverloze compute-laag.

    ![Schermopname van de Azure-portal waarin u een voorbeeld ziet van een serverloze Azure SQL-database](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/provision-serverless.png)

2. Zorg ervoor dat uw SQLite-databasebestand beschikbaar is in uw Windows-omgeving. Installeer een SQLite ODBC-stuurprogramma als u er nog geen hebt (er zijn er veel open source beschikbaar, zoals http://www.ch-werner.de/sqliteodbc/).

3. Maak een systeem-DSN voor de database. Zorg ervoor dat u versie van de toepassing Data Source Administrator gebruikt die overeenkomt met uw systeemarchitectuur (32-bits of 64-bits). U kunt zien welke versie u gebruikt in de systeeminstellingen.

    - Open ODBC Data Source Administrator in uw omgeving.
    - Klik op het tabblad voor systeem-DSN en klik op 'Add' (Toevoegen)
    - Selecteer de SQLite ODBC-connector die u hebt geïnstalleerd en geef de verbinding een beschrijvende naam, zoals sqlitemigrationsource
    - Stel als databasenaam het .db-bestand in
    - Opslaan en afsluiten

4. Download en installeer de zelf-hostende integratieruntime. De eenvoudigste manier om dit te doen is met de optie voor snelle installatie, zoals beschreven in de documentatie. Als u kiest voor een handmatige installatie, moet u een verificatiesleutel voor de toepassing opgeven, die u in uw Data Factory-exemplaar kunt vinden:

    - Start ADF (Ontwerpen en bewaken vanuit de service in de Azure-portal)
    - Klik aan de linkerkant op het tabblad 'Ontwerpen' (blauw potlood)
    - Klik op 'Verbindingen' (linksonder) en vervolgens op Integratieruntime
    - Voeg een nieuwe zelf-hostende integratieruntime toe, geef deze een naam en selecteer *optie 2*.

5. Maak een nieuwe gekoppelde service voor de SQLite-brondatabase in uw Data Factory.

    ![Schermopname van een lege blade voor gekoppelde services in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create.png)

6. Klik bij **Verbindingen**, onder **Gekoppelde service**, op **Nieuw**.

7. Zoek en selecteer de connector 'ODBC'

   ![Schermopname met het logo van de ODBC-connector in de blade voor gekoppelde services in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-odbc.png)

8. Geef de gekoppelde service een duidelijke naam, zoals 'sqlite_odbc'. Selecteer de integratieruntime in de vervolgkeuzelijst 'Verbinding maken via integratieruntime'. Voer het volgende in voor de verbindingsreeks en vervang de eerste catalogusvariabele door het bestandspad voor het .db-bestand en de DSN met de naam van de systeem-DSN-verbinding:

   ```
   Connection string: Provider=MSDASQL.1;Persist Security Info=False;Mode=ReadWrite;Initial Catalog=C:\sqlitemigrationsource.db;DSN=sqlitemigrationsource
    ```

9. Stel het verificatietype in op anoniem

10. De verbinding testen

    ![Schermopname met een geslaagde verbinding in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-test-successful.png)

11. Maak nog een gekoppelde service voor uw serverloze SQL-doel. Selecteer de database met behulp van de wizard voor gekoppelde service en geef de referenties voor de SQL-verificatie op.

    ![Schermopname met Azure SQL Database geselecteerd in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/linked-services-create-target.png)

12. Haal de CREATE TABLE-instructies op uit uw SQLite-database. U kunt dit doen door het onderstaande python-script uit te voeren voor uw databasebestand.

    ```
    #!/usr/bin/python
    import sqlite3
    conn = sqlite3.connect("sqlitemigrationsource.db")
    c = conn.cursor()

    print("Starting extract job..")
    with open('CreateTables.sql', 'w') as f:
        for tabledetails in c.execute("SELECT * FROM sqlite_master WHERE type='table'"):
            print("Extracting CREATE statement for " + (str(tabledetails[1])))
            print(tabledetails)
            f.write(str(tabledetails[4].replace('\n','') + ';\n'))
    c.close()
    ```

13. Maak de doeltabellen in uw serverloze SQL-doelomgeving door de CREATE TABLE-instructies te kopiëren uit het bestand CreateTables.sql en de SQL-instructies uit te voeren in de query-editor in de Azure-portal.

14. Ga terug naar het startscherm van uw Data Factory en klik op 'Gegevens kopiëren' om de wizard voor het maken van de taak uit te voeren.

    ![Schermopname van het logo van de wizard 'Gegevens kopiëren' in Azure Data Factory](./media/migrate-sqlite-db-to-azure-sql-serverless-offline-tutorial/copy-data.png)

15. Selecteer alle tabellen uit de SQLite-brondatabase met behulp van de selectievakjes en wijs deze toe aan de doeltabellen in Azure SQL. Zodra de taak is uitgevoerd, hebt u uw gegevens van SQLite naar Azure SQL gemigreerd.

## <a name="next-steps"></a>Volgende stappen

- Als u aan de slag wilt gaan, raadpleegt u [Quickstart: Een individuele database maken in Azure SQL Database met de Azure-portal](single-database-create-quickstart.md).
- Zie voor resourcelimieten [Resourcelimieten voor serverloze compute-lagen](./resource-limits-vcore-single-databases.md#general-purpose---serverless-compute---gen5).