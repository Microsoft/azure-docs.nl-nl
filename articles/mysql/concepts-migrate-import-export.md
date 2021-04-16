---
title: Importeren en exporteren - Azure Database for MySQL
description: In dit artikel worden veelgebruikte manieren beschreven om databases in Azure Database for MySQL importeren en exporteren met behulp van hulpprogramma's zoals MySQL Workbench.
author: savjani
ms.author: pariks
ms.service: mysql
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 10/30/2020
ms.openlocfilehash: 641dfa2439513138b5dd8f56843e81c31eb38609
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389771"
---
# <a name="migrate-your-mysql-database-by-using-import-and-export"></a>Uw MySQL-database migreren met behulp van importeren en exporteren

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In dit artikel worden twee veelgebruikte methoden beschreven voor het importeren en exporteren van gegevens naar een Azure Database for MySQL server met behulp van MySQL Workbench.

Zie de resources van de migratiehandleiding voor gedetailleerde en [uitgebreide migratiehandleiding.](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) 

Zie de Database Migration Guide (Handleiding voor [databasemigratie) voor andere migratiescenario's.](https://datamigration.microsoft.com/) 

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het migreren van uw MySQL-database, moet u het volgende doen:
- Maak een [Azure Database for MySQL server met behulp van de Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md).
- Download en installeer [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) of een ander MySQL-hulpprogramma van derden voor het importeren en exporteren.

## <a name="create-a-database-on-the-azure-database-for-mysql-server"></a>Een database maken op de Azure Database for MySQL server
Maak een lege database op de Azure Database for MySQL server met behulp van MySQL Workbench, Toad of Navicat. De database kan dezelfde naam hebben als de database die de dumpgegevens bevat, of u kunt een database met een andere naam maken.

Ga als volgt te werk om verbinding te maken:

1. Zoek in Azure Portal verbindingsgegevens in **het** deelvenster Overzicht van uw Azure Database for MySQL.

   :::image type="content" source="./media/concepts-migrate-import-export/1_server-overview-name-login.png" alt-text="Schermopname van de Azure Database for MySQL serververbindingsgegevens in de Azure Portal.":::

1. Voeg de verbindingsgegevens toe aan MySQL Workbench.

   :::image type="content" source="./media/concepts-migrate-import-export/2_setup-new-connection.png" alt-text="Schermopname van de mySQL Workbench-connection string.":::

## <a name="determine-when-to-use-import-and-export-techniques"></a>Bepalen wanneer u import- en exporttechnieken gebruikt

> [!TIP]
> Gebruik in plaats daarvan de dump- en [herstelbenadering](concepts-migrate-dump-restore.md) voor scenario's waarin u de hele database wilt dumpen en herstellen.

In de volgende scenario's gebruikt u MySQL-hulpprogramma's om databases te importeren en exporteren naar uw MySQL-database. Ga voor andere hulpprogramma's naar de sectie Migratiemethoden (pagina 22) van de migratiehandleiding voor [MySQL naar Azure Database.](https://github.com/Azure/azure-mysql/blob/master/MigrationGuide/MySQL%20Migration%20Guide_v1.1.pdf) 

- Wanneer u selectief een paar tabellen moet kiezen om te importeren uit een bestaande MySQL-database in uw Azure MySQL-database, kunt u het beste de import- en exporttechniek gebruiken. Hierdoor kunt u onnodige tabellen weglaten uit de migratie om tijd en resources te besparen. Gebruik bijvoorbeeld de `--include-tables` schakelknop of `--exclude-tables` met [mysqlpump](https://dev.mysql.com/doc/refman/5.7/en/mysqlpump.html#option_mysqlpump_include-tables)en de `--tables` switch met [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html#option_mysqldump_tables).
- Wanneer u andere databaseobjecten dan tabellen verplaatst, maakt u deze objecten expliciet. Neem beperkingen op (primaire sleutel, vreemde sleutel en indexen), weergaven, functies, procedures, triggers en andere databaseobjecten die u wilt migreren.
- Wanneer u gegevens migreert uit andere externe gegevensbronnen dan een MySQL-database, maakt u platte bestanden en importeert u deze met behulp van [mysqlimport.](https://dev.mysql.com/doc/refman/5.7/en/mysqlimport.html)

> [!Important]
> Zowel Single Server als Flexible Server ondersteunen alleen de InnoDB-opslagen engine. Zorg ervoor dat alle tabellen in de database de InnoDB-opslagen engine gebruiken wanneer u gegevens laadt in uw Azure-database voor MySQL.
>
> Als uw brondatabase gebruikmaakt van een andere opslagen engine, converteert u deze naar de InnoDB-engine voordat u de database migreert. Als u bijvoorbeeld een WordPress- of web-app hebt die gebruikmaakt van de MyISAM-engine, converteert u eerst de tabellen door de gegevens te migreren naar InnoDB-tabellen. Gebruik de -component om de engine in te stellen voor het maken van een tabel en breng de gegevens vóór de migratie over naar `ENGINE=INNODB` de compatibele tabel.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```

## <a name="performance-recommendations-for-import-and-export"></a>Prestatieaanbevelingen voor importeren en exporteren

Voor optimale prestaties bij het importeren en exporteren van gegevens raden we u aan het volgende te doen:
-   Maak geclusterde indexen en primaire sleutels voordat u gegevens laadt. Laad de gegevens in de volgorde van de primaire sleutel.
-   Het maken van secundaire indexen vertragen totdat de gegevens zijn geladen.
-   Schakel beperkingen voor de foreign key uit voordat u de gegevens laadt. Het uitschakelen van controles van vreemde sleutels levert aanzienlijke prestatieverbeteringen op. Schakel de beperkingen in en controleer de gegevens na het laden om de referentiële integriteit te garanderen.
-   Gegevens parallel laden. Vermijd te veel parallellisme dat ertoe zou leiden dat u een resourcelimiet bereikt en controleer resources met behulp van de metrische gegevens die beschikbaar zijn in de Azure Portal.
-   Gebruik gepartitiede tabellen indien van toepassing.

## <a name="import-and-export-data-by-using-mysql-workbench"></a>Gegevens importeren en exporteren met behulp van MySQL Workbench
Er zijn twee manieren om gegevens te exporteren en importeren in MySQL Workbench: vanuit het contextmenu van de objectbrowser of vanuit het deelvenster Navigator. Elke methode heeft een ander doel.

> [!NOTE]
> Als u een verbinding toevoegt aan MySQL Single Server of Flexible Server (Preview) in MySQL Workbench, gaat u als volgt te werk:
> - Zorg ervoor dat voor MySQL Enkele server de gebruikersnaam de notatie *\<username@servername>* heeft.
> - Gebruik alleen voor MySQL Flexible *\<username>* Server. Als u gebruikt *\<username@servername>* om verbinding te maken, mislukt de verbinding.

### <a name="run-the-table-data-export-and-import-wizards-from-the-object-browser-context-menu"></a>De wizards tabelgegevens exporteren en importeren uitvoeren vanuit het contextmenu van de objectbrowser

:::image type="content" source="./media/concepts-migrate-import-export/p1.png" alt-text="Schermopname van de opdrachten voor het exporteren en importeren van de MySQL Workbench-wizard in het contextmenu van de objectbrowser.":::

De wizards voor tabelgegevens ondersteunen import- en exportbewerkingen met behulp van CSV- en JSON-bestanden. De wizards bevatten verschillende configuratieopties, zoals scheidingstekens, kolomselectie en coderingsselectie. U kunt elke wizard uitvoeren op lokale of extern verbonden MySQL-servers. De importactie omvat tabel-, kolom- en typetoewijzing.

Als u deze wizards wilt openen vanuit het contextmenu van de objectbrowser, klikt u met de rechtermuisknop op een tabel en selecteert u vervolgens **Wizard** Tabelgegevens exporteren of Wizard **Tabelgegevens importeren.**

#### <a name="the-table-data-export-wizard"></a>De wizard tabelgegevens exporteren

Een tabel exporteren naar een CSV-bestand:

1. Klik met de rechtermuisknop op de tabel van de database die moet worden geëxporteerd.
1. Selecteer **wizard Tabelgegevens exporteren.** Selecteer de kolommen die moeten worden geëxporteerd, de rij offset (indien van een) en het aantal (indien van) .
1. Selecteer in **het deelvenster Gegevens selecteren voor export** de optie **Volgende.** Selecteer het bestandspad, CSV- of JSON-bestandstype. Selecteer ook het regelscheidingsteken, de methode voor het insluiten van tekenreeksen en het veldscheidingsteken.
1. Selecteer in **het deelvenster Locatie van uitvoerbestand** selecteren de optie **Volgende.**
1. Selecteer volgende **in het** deelvenster Gegevens **exporteren.**

#### <a name="the-table-data-import-wizard"></a>De wizard tabelgegevens importeren

Een tabel importeren uit een CSV-bestand:

1. Klik met de rechtermuisknop op de tabel van de database die moet worden geïmporteerd.
1. Zoek en selecteer het CSV-bestand dat moet worden geïmporteerd en selecteer **vervolgens Volgende.**
1. Selecteer de doeltabel (nieuw of bestaand), schakel het selectievakje **Tabel** afkapen vóór importeren in of uit en selecteer **vervolgens Volgende.**
1. Selecteer de codering en de kolommen die moeten worden geïmporteerd en selecteer vervolgens **Volgende.**
1. Selecteer in **het deelvenster Gegevens** importeren de optie **Volgende.** De wizard importeert de gegevens.

### <a name="run-the-sql-data-export-and-import-wizards-from-the-navigator-pane"></a>De wizards SQL-gegevens exporteren en importeren uitvoeren vanuit het deelvenster Navigator
Gebruik een wizard om SQL-gegevens te exporteren of te importeren die zijn gegenereerd op basis van MySQL Workbench of met de opdracht mysqldump. U kunt de wizards openen vanuit het **deelvenster Navigator** of u kunt **Server selecteren** in het hoofdmenu.

#### <a name="export-data"></a>Gegevens exporteren

:::image type="content" source="./media/concepts-migrate-import-export/p2.png" alt-text="Schermopname van het gebruik van het deelvenster Navigator om het deelvenster Gegevensexport in MySQL Workbench weer te geven.":::

U kunt het deelvenster **Gegevensexport gebruiken** om uw MySQL-gegevens te exporteren.

1. Selecteer in MySQL Workbench in het **deelvenster Navigator** de optie **Gegevensexport.**

1. Selecteer in **het deelvenster Gegevensexport** elk schema dat u wilt exporteren.
 
   Voor elk schema kunt u specifieke schemaobjecten of tabellen selecteren die u wilt exporteren. Configuratieopties zijn onder andere exporteren naar een projectmap of een zelfstandig SQL-bestand, dumpen van opgeslagen routines en gebeurtenissen of het overslaan van tabelgegevens.

   U kunt ook Een **resultatenset** exporteren gebruiken om een specifieke resultatenset in de SQL-editor te exporteren naar een andere indeling, zoals CSV, JSON, HTML en XML.

1. Selecteer de databaseobjecten die u wilt exporteren en configureer de gerelateerde opties.
1. Selecteer **Vernieuwen om** de huidige objecten te laden.
1. Selecteer eventueel Geavanceerde **opties in** de rechterbovenhoek om de exportbewerking te verfijnen. Voeg bijvoorbeeld tabelvergrendelingen toe, gebruik `replace` in plaats van instructies en `insert` prijsopgave-id's met backtick-tekens.
1. Selecteer **Exporteren starten om** het exportproces te starten.


#### <a name="import-data"></a>Gegevens importeren

:::image type="content" source="./media/concepts-migrate-import-export/p3.png" alt-text="Schermopname van het gebruik van het deelvenster Navigator om het deelvenster Gegevensimport in MySQL Workbench weer te geven.":::

U kunt het deelvenster **Gegevens** importeren gebruiken om geëxporteerde gegevens te importeren of te herstellen vanuit de gegevensexportbewerking of met de opdracht mysqldump.

1. Selecteer in MySQL Workbench in het **deelvenster Navigator** de optie **Gegevens exporteren/herstellen.**
1. Selecteer de projectmap of het zelfstandige SQL-bestand, selecteer het schema waarin u wilt importeren of selecteer de knop **Nieuw** om een nieuw schema te definiëren.
1. Selecteer **Import starten om** het importproces te starten.

## <a name="next-steps"></a>Volgende stappen
- Zie Uw MySQL-database migreren naar een [Azure-database voor MySQL](concepts-migrate-dump-restore.md)met behulp van dump en herstel voor een andere migratiebenadering.
- Zie de Database Migration Guide (Handleiding voor databasemigratie) voor meer informatie over het migreren van databases naar een [Azure-database](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor MySQL.
