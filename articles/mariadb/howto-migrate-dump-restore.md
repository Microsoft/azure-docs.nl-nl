---
title: Migreren met dump en Restore-Azure Database for MariaDB
description: In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure Database for MariaDB, met behulp van hulpprogram ma's zoals mysqldump, MySQL Workbench en PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 8678304e72f11c486911ff4de00633224e878147
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564470"
---
# <a name="migrate-your-mariadb-database-to-azure-database-for-mariadb-using-dump-and-restore"></a>Uw MariaDB-data base migreren naar Azure Database for MariaDB met dump en herstel
In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure Database for MariaDB
- Dump en herstel vanaf de opdracht regel (met behulp van mysqldump) 
- Dump en herstel met behulp van PHPMyAdmin

## <a name="before-you-begin"></a>Voordat u begint
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Azure Database for MariaDB-server maken-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)
- [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) -opdracht regel programma dat op een computer is geïnstalleerd.
- MySQL Workbench [MySQL Workbench down load](https://dev.mysql.com/downloads/workbench/) of een ander mysql-hulp programma van derden om dump-en herstel opdrachten uit te voeren.

## <a name="use-common-tools"></a>Algemene hulpprogram ma's gebruiken
Gebruik veelvoorkomende hulpprogram ma's en hulpprogram ma's zoals MySQL Workbench of mysqldump om gegevens extern te verbinden en te herstellen in Azure Database for MariaDB. Gebruik deze hulpprogram ma's op de client computer met een Internet verbinding om verbinding te maken met de Azure Database for MariaDB. Gebruik een met SSL versleutelde verbinding voor best practices voor beveiliging, Zie [SSL-connectiviteit ook configureren in azure database for MariaDB](concepts-ssl-connection-security.md). U hoeft de dump bestanden niet te verplaatsen naar een speciale Cloud locatie wanneer u migreert naar Azure Database for MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Veelvoorkomende toepassingen voor dump en herstel
U kunt MySQL-hulpprogram ma's, zoals mysqldump en mysqlpump, gebruiken om data bases te dumpen en te laden in een Azure Database for MariaDB-server in verschillende algemene scenario's. 

<!--In other scenarios, you may use the [Import and Export](howto-migrate-import-export.md) approach instead.-->

- Gebruik database dumps wanneer u de gehele data base migreert. Deze aanbeveling bevindt zich bij het verplaatsen van een grote hoeveelheid gegevens of wanneer u de onderbreking van de service voor Live sites of toepassingen wilt minimaliseren. 
-  Zorg ervoor dat alle tabellen in de database de InnoDB-opslag-engine gebruiken bij het laden van gegevens in Azure Database for MariaDB. Azure Database for MariaDB ondersteunt alleen de InnoDB-opslag engine en biedt daarom geen ondersteuning voor alternatieve opslag engines. Als uw tabellen zijn geconfigureerd met andere opslag engines, converteert u deze naar de InnoDB-engine-indeling voordat u de migratie naar Azure Database for MariaDB.
   Als u bijvoorbeeld een WordPress of WebApp met behulp van de MyISAM-tabellen hebt, moet u deze tabellen eerst converteren door de migratie naar de InnoDB-indeling voordat u naar Azure Database for MariaDB herstelt. Gebruik de-component `ENGINE=InnoDB` om de engine in te stellen die wordt gebruikt bij het maken van een nieuwe tabel en vervolgens de gegevens over te dragen naar de compatibele tabel vóór de herstel bewerking. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- U kunt compatibiliteitsproblemen voorkomen door ervoor te zorgen dat dezelfde versie van MariaDB wordt gebruikt in het bron- en doelsysteem bij het dumpen van databases. Als uw bestaande MariaDB-server bijvoorbeeld versie 10,2 is, moet u migreren naar Azure Database for MariaDB geconfigureerd voor het uitvoeren van versie 10,2. De `mysql_upgrade` opdracht werkt niet op een Azure database for MariaDB-server en wordt niet ondersteund. Als u een upgrade wilt uitvoeren voor MariaDB-versies, moet u eerst de data base van uw lagere versie dumpen of exporteren naar een hogere versie van MariaDB in uw eigen omgeving. Voer vervolgens uit `mysql_upgrade` voordat u een migratie uitvoert in een Azure database for MariaDB.

## <a name="performance-considerations"></a>Prestatieoverwegingen
Bekijk de volgende overwegingen bij het dumpen van grote data bases om de prestaties te optimaliseren:
-   Gebruik de `exclude-triggers` optie in mysqldump bij het dumpen van data bases. Sluit triggers uit van dump bestanden om te voor komen dat trigger opdrachten worden geactiveerd tijdens het herstellen van gegevens. 
-   Gebruik de `single-transaction` optie om de modus voor transactie isolatie in te stellen op herhaalbaar lezen en een SQL-instructie voor het starten van een trans actie naar de server te verzenden voordat gegevens worden gedumpt. Het dumpen van veel tabellen binnen één trans actie leidt ertoe dat er tijdens het herstellen enkele extra opslag ruimte wordt gebruikt. De `single-transaction` optie en de `lock-tables` optie sluiten elkaar wederzijds uit omdat vergrendelings tabellen alle trans acties die in behandeling zijn, impliciet moeten worden doorgevoerd. Als u grote tabellen wilt dumpen, moet u de optie combi neren `single-transaction` met de `quick` optie. 
-   Gebruik de `extended-insert` syntaxis met meerdere rijen die verschillende waardelijsten bevat. Dit leidt tot een kleiner dump bestand en versnelt het invoegen wanneer het bestand opnieuw wordt geladen.
-  Gebruik de `order-by-primary` optie in mysqldump bij het dumpen van data bases, zodat de gegevens in de volg orde van de primaire sleutel worden gescripteerd.
-   Gebruik de `disable-keys` optie in mysqldump bij het dumpen van gegevens om refererende-sleutel beperkingen voor het laden uit te scha kelen. Het uitschakelen van externe-sleutel controles levert prestatie verbeteringen. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te waarborgen.
-   Gebruik gepartitioneerde tabellen wanneer dat nodig is.
-   Gegevens parallel laden. Vermijd te veel parallellisme waardoor u een resource limiet kunt bereiken en resources kunt bewaken met behulp van de metrische gegevens die beschikbaar zijn in de Azure Portal. 
-   Gebruik de `defer-table-indexes` optie in mysqlpump bij het dumpen van data bases, zodat het maken van de index plaatsvindt nadat tabellen gegevens zijn geladen.
-   Kopieer de back-upbestanden naar een Azure-Blob/-Store en voer de herstel bewerking uit. Dit is veel sneller dan het terugzetten via internet.

## <a name="create-a-backup-file"></a>Een back-upbestand maken
Als u een back-up wilt maken van een bestaande MariaDB-Data Base op de lokale on-premises server of op een virtuele machine, voert u de volgende opdracht uit met behulp van mysqldump: 
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De para meters die u moet opgeven, zijn:
- [uname] Uw data base-gebruikers naam 
- door Het wacht woord voor uw data base (er is geen spatie tussen-p en het wacht woord) 
- dbname De naam van uw data base 
- [upbestand. SQL] de bestands naam van de back-up van de data base 
- [--opt] De optie mysqldump 

Als u bijvoorbeeld een back-up wilt maken van een Data Base met de naam ' testdb ' op uw MariaDB-server met de gebruikers naam ' test ' en zonder wacht woord aan een bestand testdb_backup. SQL, gebruikt u de volgende opdracht. De opdracht maakt een back-up van de `testdb` Data base in een bestand met de naam `testdb_backup.sql` , dat alle SQL-instructies bevat die nodig zijn om de data base opnieuw te maken. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Als u specifieke tabellen in de Data Base wilt selecteren om een back-up te maken, vermeldt u de tabel namen gescheiden door spaties. Als u bijvoorbeeld alleen een back-up wilt maken van de tabellen Tabel1 en tabel2 van de ' testdb ', volgt u dit voor beeld: 
```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```
Als u een back-up van meer dan één data base tegelijk wilt maken, gebruikt u de-data base-switch en geeft u de database namen op, gescheiden door spaties. 
```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Een Data Base op de doel server maken
Maak een lege data base op de doel Azure Database for MariaDB server waarnaar u de gegevens wilt migreren. Gebruik een hulp programma zoals MySQL Workbench om de data base te maken. De data base kan dezelfde naam hebben als de Data Base waarin de dumping gegevens zijn opgenomen of u kunt een Data Base met een andere naam maken.

Als u verbinding wilt krijgen, zoekt u de verbindings gegevens in het **overzicht** van uw Azure database for MariaDB.

![De verbindings gegevens in de Azure Portal zoeken](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Voeg de verbindings gegevens toe aan uw MySQL Workbench.

![MySQL Workbench-verbindings reeks](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Uw MariaDB-data base herstellen
Wanneer u de doel database hebt gemaakt, kunt u de MySQL-opdracht of MySQL Workbench gebruiken om de gegevens in de specifieke zojuist gemaakte data base te herstellen vanuit het dump bestand.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In dit voor beeld herstelt u de gegevens in de zojuist gemaakte Data Base op de doel Azure Database for MariaDB server.
```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-using-phpmyadmin"></a>Exporteren met behulp van PHPMyAdmin
Als u wilt exporteren, kunt u gebruikmaken van de algemene hulp middelen phpMyAdmin, die u mogelijk al lokaal hebt geïnstalleerd in uw omgeving. Uw MariaDB-data base exporteren met behulp van PHPMyAdmin:
1. Open phpMyAdmin.
2. Selecteer uw data base. Klik op de naam van de data base in de lijst aan de linkerkant. 
3. Klik op de koppeling **exporteren** . Er wordt een nieuwe pagina weer gegeven om de dump van de Data Base weer te geven.
4. Klik in het gedeelte exporteren op de koppeling **Alles selecteren** om de tabellen in de data base te kiezen. 
5. Klik in het gebied SQL-opties op de gewenste opties. 
6. Klik op de optie **Opslaan als bestand** en de bijbehorende compressie optie en klik vervolgens op de knop **Go** . Er wordt een dialoog venster weer gegeven waarin u wordt gevraagd het bestand lokaal op te slaan.

## <a name="import-using-phpmyadmin"></a>Importeren met behulp van PHPMyAdmin
Het importeren van uw data base lijkt op het exporteren. Voer de volgende acties uit:
1. Open phpMyAdmin. 
2. Klik op de pagina phpMyAdmin Setup op **toevoegen** om uw Azure database for MariaDB-server toe te voegen. Geef de verbindings gegevens en aanmeldings gegevens op.
3. Maak een Data Base met de juiste naam en selecteer deze aan de linkerkant van het scherm. Als u de bestaande data base opnieuw wilt schrijven, klikt u op de naam van de data base, selecteert u alle selectie vakjes naast de tabel namen en selecteert u **neerzetten** om de bestaande tabellen te verwijderen. 
4. Klik op de **SQL** -koppeling om de pagina weer te geven waarop u SQL-opdrachten kunt typen of uw SQL-bestand moet uploaden. 
5. Gebruik de knop **Bladeren** om het database bestand te zoeken. 
6. Klik op de knop **Go** om de back-up te exporteren, de SQL-opdrachten uit te voeren en de data base opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen
- [Toepassingen verbinden met Azure database for MariaDB](./howto-connection-string.md).
 
<!--
- For more information about migrating databases to Azure Database for MariaDB, see the [Database Migration Guide](https://aka.ms/datamigration).
-->
