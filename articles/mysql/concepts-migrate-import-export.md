---
title: Importeren en exporteren-Azure Database for MySQL
description: In dit artikel worden algemene manieren uitgelegd voor het importeren en exporteren van data bases in Azure Database for MySQL, met behulp van hulpprogram ma's zoals MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: 4d553f6c87d1044f8bde7460a0ea7bf123dd1851
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106450069"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Uw MySQL-database migreren met behulp van importeren en exporteren

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In dit artikel worden twee veelvoorkomende benaderingen beschreven voor het importeren en exporteren van gegevens naar een Azure Database for MySQL-server met behulp van MySQL Workbench.

Zie de bronnen voor de [migratie handleiding](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor gedetailleerde en uitgebreide richt lijnen voor migratie. 

Zie de [hand leiding voor database migratie](https://datamigration.microsoft.com/)voor andere migratie scenario's. 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met de migratie van uw MySQL-data base, moet u het volgende doen:
- Een Azure Database for MySQL-server maken met [behulp van de Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Down load en Installeer [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) of een ander mysql-hulp programma van derden voor het importeren en exporteren.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Een Data Base op de Azure Database for MySQL-server maken
Maak een lege data base op de Azure Database for MySQL-server met behulp van MySQL Workbench, Toad of Navicat. De data base kan dezelfde naam hebben als de data base die de dumped gegevens bevat of u kunt een Data Base maken met een andere naam.

Ga als volgt te werk om verbinding te maken:

1. Zoek in het Azure Portal naar de verbindings gegevens in het deel venster **overzicht** van uw Azure database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Scherm afbeelding van de verbindings gegevens van de Azure Database for MySQL server in de Azure Portal.":::

1. Voeg de verbindings gegevens toe aan MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Scherm afbeelding van de MySQL Workbench-connection string.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Bepalen wanneer de technieken voor importeren en exporteren worden gebruikt

> [!TIP]
> Voor scenario's waarbij u de gehele Data Base wilt dumpen en herstellen, gebruikt u de methode voor [dump en herstel](concepts-migrate-dump-restore.md) .

In de volgende scenario's kunt u MySQL-hulpprogram ma's gebruiken om data bases in uw MySQL-data base te importeren en exporteren. Voor andere hulpprogram ma's gaat u naar de sectie ' migratie methoden ' (pagina 22) van de [MySQL naar Azure data base Migration Guide](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf). 

- Wanneer u selectief moet kiezen uit een aantal tabellen die u wilt importeren uit een bestaande MySQL-data base in uw Azure MySQL-data base, kunt u het beste de import-en export techniek gebruiken. Door dit te doen, kunt u onnodige tabellen uit de migratie weglaten om tijd en resources te besparen. Gebruik bijvoorbeeld de of- `--include-tables` `--exclude-tables` Switch met [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)en de `--tables` Switch met [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wanneer u andere database objecten dan tabellen verplaatst, moet u deze objecten expliciet maken. Neem beperkingen op (primaire sleutel, refererende sleutel en indexen), weer gaven, functies, procedures, triggers en andere database objecten die u wilt migreren.
- Wanneer u gegevens migreert uit externe gegevens bronnen, met uitzonde ring van een MySQL-data base, maakt u platte bestanden en importeert u deze met behulp van [mysqlimport](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html).

> [!Important]
> Zowel één server als flexibele server bieden alleen ondersteuning voor de InnoDB-opslag engine. Zorg ervoor dat alle tabellen in de data base de InnoDB-opslag engine gebruiken wanneer u gegevens laadt in uw Azure Data Base voor MySQL.
>
> Als uw bron database een andere opslag engine gebruikt, moet u deze converteren naar de InnoDB-engine voordat u de data base migreert. Als u bijvoorbeeld een WordPress-of web-app hebt die gebruikmaakt van de MyISAM-engine, moet u de tabellen eerst converteren door de gegevens te migreren naar InnoDB-tabellen. Gebruik de-component `ENGINE=INNODB` om de engine in te stellen voor het maken van een tabel en vervolgens de gegevens over te dragen naar de compatibele tabel vóór de migratie.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestatie aanbevelingen voor importeren en exporteren

Voor optimale prestaties bij het importeren en exporteren van gegevens kunt u het volgende doen:
-   Maak geclusterde indexen en primaire sleutels voordat u gegevens laadt. Laad de gegevens in de volg orde van de primaire sleutel.
-   Het maken van secundaire indexen vertragen totdat de gegevens zijn geladen.
-   Schakel beperkingen van refererende sleutels uit voordat u de gegevens laadt. Het uitschakelen van externe-sleutel controles biedt aanzienlijke prestatie verbeteringen. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te waarborgen.
-   Gegevens parallel laden. Vermijd te veel parallellisme, waardoor u een resource limiet kunt bereiken en resources bewaken met behulp van de metrische gegevens die beschikbaar zijn in de Azure Portal.
-   Gebruik gepartitioneerde tabellen wanneer dat nodig is.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Gegevens importeren en exporteren met behulp van MySQL Workbench
Er zijn twee manieren om gegevens te exporteren en te importeren in MySQL Workbench: vanuit het context menu object browser of vanuit het deel venster navigator. Elke methode fungeert voor een ander doel.

> [!NOTE]
> Ga als volgt te werk als u een verbinding toevoegt aan een enkele server met MySQL of een flexibele server (preview) in MySQL Workbench:
> - Zorg ervoor dat de gebruikers naam in de indeling is voor MySQL-één server *\<username@servername>* .
> - Gebruik voor MySQL flexibele server *\<username>* alleen. Als u gebruikt *\<username@servername>* om verbinding te maken, mislukt de verbinding.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>Voer de wizards tabel gegevens exporteren en importeren uit in het context menu object browser

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Scherm afbeelding van de wizard exporteren en importeren van MySQL Workbench in het context menu van het object browser.":::

De tabel gegevens wizards ondersteunen import-en export bewerkingen door gebruik te maken van CSV-en JSON-bestanden. De wizards bevatten verschillende configuratie opties, zoals scheidings tekens, kolom selectie en coderings selectie. U kunt elke wizard uitvoeren op een lokaal of extern aangesloten MySQL-servers. De import actie bevat tabel-, kolom-en type toewijzing.

Als u deze wizards wilt openen vanuit het context menu van de object browser, klikt u met de rechter muisknop op een tabel en selecteert u vervolgens de **wizard tabel gegevens exporteren** of de **wizard tabel gegevens importeren**.

#### <a name="the-table-data-export-wizard"></a>De wizard tabel gegevens exporteren

Een tabel exporteren naar een CSV-bestand:

1. Klik met de rechter muisknop op de tabel van de data base die u wilt exporteren.
1. Selecteer de **wizard tabel gegevens exporteren**. Selecteer de kolommen die u wilt exporteren, verschuiving van rij (indien aanwezig) en aantal (indien van toepassing).
1. Selecteer **volgende** in het deel venster **gegevens selecteren voor exporteren** . Selecteer het bestandspad, CSV of JSON-bestands type. Selecteer ook het regel scheidings teken, de methode van insluitende teken reeksen en het scheidings teken voor velden.
1. Selecteer **volgende** in het deel venster **locatie van uitvoer bestand selecteren** .
1. Selecteer **volgende** in het deel venster **gegevens exporteren** .

#### <a name="the-table-data-import-wizard"></a>De wizard tabel gegevens importeren

Een tabel uit een CSV-bestand importeren:

1. Klik met de rechter muisknop op de tabel van de data base die u wilt importeren.
1. Zoek en selecteer het CSV-bestand dat moet worden geïmporteerd en selecteer vervolgens **volgende**.
1. Selecteer de doel tabel (nieuw of bestaand), schakel het selectie vakje **tabel afkappen vóór importeren** in of uit en selecteer **volgende**.
1. Selecteer de code ring en de kolommen die u wilt importeren en selecteer vervolgens **volgende**.
1. Selecteer **volgende** in het deel venster **gegevens importeren** . De gegevens worden door de wizard geïmporteerd.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>De wizard SQL-gegevens exporteren en importeren uitvoeren vanuit het deel venster navigator
Gebruik een wizard voor het exporteren of importeren van SQL-gegevens die zijn gegenereerd vanuit MySQL Workbench of van de mysqldump-opdracht. U kunt de wizards openen vanuit het deel venster **Navigator** of u kunt **Server** selecteren in het hoofd menu.

#### <a name="export-data"></a>Gegevens exporteren

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Scherm afbeelding van het deel venster Navigator gebruiken om het venster gegevens exporteren in MySQL Workbench weer te geven.":::

U kunt het deel venster **gegevens exporteren** gebruiken om uw MySQL-gegevens te exporteren.

1. Selecteer in MySQL Workbench in het deel venster **Navigator** de optie **gegevens export**.

1. Selecteer in het deel venster **gegevens exporteren** elk schema dat u wilt exporteren.
 
   Voor elk schema kunt u specifieke schema objecten of tabellen selecteren die u wilt exporteren. Configuratie opties zijn onder andere exporteren naar een projectmap of een zelf opgenomen SQL-bestand, opgeslagen routines en gebeurtenissen dumpen of tabel gegevens overs Laan.

   U kunt ook **een resultatenset exporteren** gebruiken om een specifieke resultatenset in de SQL-Editor te exporteren naar een andere indeling, zoals CSV, JSON, HTML en XML.

1. Selecteer de database objecten die u wilt exporteren en configureer de gerelateerde opties.
1. Selecteer **vernieuwen** om de huidige objecten te laden.
1. Selecteer desgewenst **Geavanceerde opties** in de rechter bovenhoek om de export bewerking te verfijnen. U kunt bijvoorbeeld tabel vergrendelingen toevoegen, `replace` in plaats van `insert` instructies gebruiken en offerte-id's met apostroffen-tekens.
1. Selecteer **exporteren starten** om het export proces te starten.


#### <a name="import-data"></a>Gegevens importeren

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Scherm afbeelding van het deel venster Navigator gebruiken om het venster gegevens importeren in MySQL Workbench weer te geven.":::

U kunt het deel venster **gegevens importeren** gebruiken voor het importeren of herstellen van geëxporteerde gegevens uit de bewerking voor gegevens export of van de mysqldump-opdracht.

1. In MySQL Workbench selecteert u in het deel venster **Navigator** de optie **gegevens exporteren/herstellen**.
1. Selecteer de projectmap of het zelf opgenomen SQL-bestand, selecteer het schema dat u wilt importeren of selecteer de knop **Nieuw** om een nieuw schema te definiëren.
1. Selecteer **importeren starten** om het import proces te starten.

## <a name="next-steps"></a>Volgende stappen
- Zie [uw MySQL-data base migreren naar een Azure-Data Base voor MySQL met behulp van dump en herstellen](concepts-migrate-dump-restore.md)voor een andere migratie methode.
- Zie de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor meer informatie over het migreren van data bases naar een Azure-Data Base voor mysql.
