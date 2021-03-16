---
title: Importeren en exporteren-Azure Database for MySQL
description: In dit artikel worden algemene manieren uitgelegd voor het importeren en exporteren van data bases in Azure Database for MySQL, met behulp van hulpprogram ma's zoals MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 391a38cd3d1e2ffb790587b6c60ed54992d14d2d
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103561988"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Uw MySQL-data base migreren met behulp van importeren en exporteren
[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]
In dit artikel worden twee veelvoorkomende benaderingen beschreven voor het importeren en exporteren van gegevens naar een Azure Database for MySQL-server met behulp van MySQL Workbench.

Zie de [hand leiding voor de migratie van MySQL naar Azure data base](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor een gedetailleerde en uitgebreide migratie handleiding. 

U kunt ook de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) raadplegen voor gedetailleerde informatie en voor beelden over het migreren van data bases naar Azure database for MySQL. Deze hand leiding bevat richt lijnen voor een geslaagde planning en uitvoering van een MySQL-migratie naar Azure.

## <a name="before-you-begin"></a>Voordat u begint
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- Een Azure Database for MySQL server, door [een Azure database for mysql-server te maken met behulp van Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) of een ander mysql-hulp programma van derden om de import/export uit te voeren.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Een Data Base op de Azure Database for MySQL-server maken
Maak een lege data base op de Azure Database for MySQL-server met behulp van MySQL Workbench, Toad of Navicat om de data base te maken. De data base kan dezelfde naam hebben als de data base die de dumped gegevens bevat of u kunt een Data Base maken met een andere naam.

Als u verbinding wilt krijgen, zoekt u de verbindings gegevens in het **overzicht** van uw Azure database for MySQL.

:::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="De verbindings gegevens in de Azure Portal zoeken":::

Voeg de verbindings gegevens toe aan MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="MySQL Workbench connection string":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Bepalen wanneer de technieken voor importeren en exporteren worden gebruikt

> [!TIP]
> Voor scenario's waarbij u de gehele Data Base wilt dumpen en herstellen, gebruikt u in plaats daarvan de methode [dump en Restore](concepts-migrate-dump-restore.md) .

Gebruik MySQL-hulpprogram ma's voor het importeren en exporteren van data bases in de Azure MySQL-data base in de volgende scenario's. Zie voor andere hulp middelen pagina 22 van de [MySQL-naar-Azure-database migratie handleiding](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Wanneer u selectief moet kiezen uit een aantal tabellen die u wilt importeren uit een bestaande MySQL-data base in azure MySQL-data base, kunt u het beste de import-en export techniek gebruiken.  Door dit te doen, kunt u onnodige tabellen uit de migratie weglaten om tijd en resources te besparen. Gebruik bijvoorbeeld de `--include-tables` of `--exclude-tables` Schakel over met [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables) en de `--tables` Switch met [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wanneer u de andere database objecten dan tabellen verplaatst, moet u deze objecten expliciet maken. Neem beperkingen op (primaire sleutel, refererende sleutel, indexen), weer gaven, functies, procedures, triggers en andere database objecten die u wilt migreren.
- Wanneer u gegevens migreert uit externe gegevens bronnen, met uitzonde ring van een MySQL-data base, maakt u platte bestanden en importeert u deze met behulp van [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Zowel één server als flexibele server bieden **alleen ondersteuning voor de InnoDB-opslag engine**. Zorg ervoor dat alle tabellen in de data base de InnoDB-opslag engine gebruiken wanneer u gegevens laadt in Azure Database for MySQL.
> Als uw bron database een andere opslag engine gebruikt, moet u deze converteren naar de InnoDB-engine voordat u de data base migreert. Als u bijvoorbeeld een WordPress-of web-app hebt die gebruikmaakt van de MyISAM-engine, moet u de tabellen eerst converteren door de gegevens te migreren naar InnoDB-tabellen. Gebruik de-component `ENGINE=INNODB` om de engine in te stellen voor het maken van een tabel en vervolgens de gegevens over te dragen naar de compatibele tabel vóór de migratie.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestatie aanbevelingen voor importeren en exporteren
-   Maak geclusterde indexen en primaire sleutels vóór het laden van gegevens. Laad gegevens in de volg orde van de primaire sleutel.
-   Het maken van secundaire indexen vertragen totdat de gegevens zijn geladen. Alle secundaire indexen na het laden maken.
-   Externe-sleutel beperkingen uitschakelen vóór het laden. Het uitschakelen van externe-sleutel controles biedt aanzienlijke prestatie verbeteringen. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te waarborgen.
-   Gegevens parallel laden. Vermijd te veel parallellisme, waardoor u een resource limiet kunt bereiken en resources bewaken met behulp van de metrische gegevens die beschikbaar zijn in de Azure Portal.
-   Gebruik gepartitioneerde tabellen wanneer dat nodig is.

## <a name="import-and-export-by-using-mysql-workbench"></a>Importeren en exporteren met behulp van MySQL Workbench
Er zijn twee manieren om gegevens te exporteren en te importeren in MySQL Workbench. Elk is een ander doel.

> [!NOTE]
> Als u een verbinding toevoegt aan MySQL single of Flexible server (preview) in MySQL Workbench, moet u het volgende controleren:
> - Voor MySQL één server moet de gebruikers naam de volgende indeling hebben: ' username@servername '
> - Voor een flexibele MySQL-server kunt u ' gebruikers naam ' alleen gebruiken als u ' username@servername ' gebruikt om verbinding te maken, mislukt de verbinding.

### <a name="table-data-export-and-import-wizards-from-the-object-browsers-context-menu"></a>Wizards voor het exporteren en importeren van tabel gegevens in het context menu van het object browser
:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="MySQL Workbench-wizards in het context menu van het object browser":::

De wizards voor tabel gegevens ondersteunen import-en export bewerkingen door gebruik te maken van CSV-en JSON-bestanden. Ze bevatten verschillende configuratie opties, zoals scheidings tekens, kolom selectie en coderings selectie. U kunt elke wizard uitvoeren op een lokaal of extern aangesloten MySQL-servers. De import actie bevat tabel-, kolom-en type toewijzing.

U kunt deze wizards openen vanuit het context menu van het object browser door met de rechter muisknop te klikken op een tabel. Kies vervolgens de **wizard tabel gegevens exporteren** of de **wizard tabel gegevens importeren**.

#### <a name="table-data-export-wizard"></a>Wizard tabel gegevens exporteren
In het volgende voor beeld wordt de tabel geëxporteerd naar een CSV-bestand:
1. Klik met de rechter muisknop op de tabel van de data base die u wilt exporteren.
2. Selecteer de **wizard tabel gegevens exporteren**. Selecteer de kolommen die u wilt exporteren, verschuiving van rij (indien aanwezig) en aantal (indien van toepassing).
3. Klik op de pagina **gegevens selecteren voor exporteren** op **volgende**. Selecteer het bestandspad, CSV of JSON-bestands type. Selecteer ook het regel scheidings teken, de methode van insluitende teken reeksen en het scheidings teken voor velden.
4. Klik op de pagina **locatie van uitvoer bestand selecteren** op **volgende**.
5. Klik op de pagina **gegevens exporteren** op **volgende**.

#### <a name="table-data-import-wizard"></a>Wizard tabel gegevens importeren
In het volgende voor beeld wordt de tabel uit een CSV-bestand geïmporteerd:
1. Klik met de rechter muisknop op de tabel van de data base die u wilt importeren.
2. Blader naar en selecteer het CSV-bestand dat u wilt importeren en klik vervolgens op **volgende**.
3. Selecteer de doel tabel (nieuw of bestaand) en schakel het selectie vakje **tabel afkappen vóór importeren** in of uit. Klik op **Volgende**.
4. Selecteer code ring en de kolommen die u wilt importeren en klik vervolgens op **volgende**.
5. Klik op de pagina **gegevens importeren** op **volgende**. De wizard importeert de gegevens dienovereenkomstig.

### <a name="sql-data-export-and-import-wizards-from-the-navigator-pane"></a>Wizards voor het exporteren en importeren van SQL-gegevens vanuit het deel venster navigator
Gebruik een wizard voor het exporteren of importeren van SQL die is gegenereerd vanuit MySQL Workbench of wordt gegenereerd met de opdracht mysqldump. Open deze wizards vanuit het deel venster **Navigator** of door **Server** te selecteren in het hoofd menu. Selecteer vervolgens **gegevens export** of **gegevens import**.

#### <a name="data-export"></a>Gegevensexport
:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Gegevens export van MySQL Workbench met behulp van het deel venster navigator":::

U kunt het tabblad **gegevens export** gebruiken om uw MySQL-gegevens te exporteren.
1. Selecteer elk schema dat u wilt exporteren, eventueel specifieke schema objecten/tabellen uit elk schema kiezen en de export genereren. Configuratie opties zijn onder andere exporteren naar een projectmap of een zelf opgenomen SQL-bestand, opgeslagen routines en gebeurtenissen dumpen of tabel gegevens overs Laan.

   U kunt ook **een resultatenset exporteren** gebruiken om een specifieke resultatenset in de SQL-Editor te exporteren naar een andere indeling, zoals CSV, JSON, HTML en XML.
3. Selecteer de database objecten die u wilt exporteren en configureer de gerelateerde opties.
4. Klik op **vernieuwen** om de huidige objecten te laden.
5. U kunt ook het tabblad **Geavanceerde opties** openen om de export bewerking te verfijnen. Voeg bijvoorbeeld tabel vergrendelingen toe, gebruik vervangen in plaats van INSERT-instructies en Quote-id's met apostroffen-tekens.
6. Klik op **exporteren starten** om het export proces te starten.


#### <a name="data-import"></a>Gegevens importeren
:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Gegevens import van MySQL Workbench met behulp van beheer Navigator":::

U kunt het tabblad **gegevens importeren** gebruiken om geëxporteerde gegevens te importeren of te herstellen uit de bewerking voor het exporteren van gegevens of vanuit de mysqldump-opdracht.
1. Kies de projectmap of het zelf opgenomen SQL-bestand, kies het schema dat u wilt importeren of kies **Nieuw** om een nieuw schema te definiëren.
2. Klik op **importeren starten** om het import proces te starten.

## <a name="next-steps"></a>Volgende stappen
- Lees voor een andere migratie methode [de migratie van uw MySQL-data base met dump en herstel in azure database for MySQL](concepts-migrate-dump-restore.md).
- Zie de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor meer informatie over het migreren van data bases naar Azure database for MySQL.