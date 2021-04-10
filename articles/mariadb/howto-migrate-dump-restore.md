---
title: Migreren met dump en Restore-Azure Database for MariaDB
description: In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure-Data Base voor MariaDB met behulp van hulpprogram ma's zoals mysqldump, MySQL Workbench en phpMyAdmin.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.subservice: migration-guide
ms.topic: how-to
ms.date: 2/27/2020
ms.openlocfilehash: 35b1e84bdf74afd9577c7c98f023179dd769cd66
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105628217"
---
# <a name="migrate-your-mariadb-database-to-an-azure-database-for-mariadb-by-using-dump-and-restore"></a>Uw MariaDB-data base migreren naar een Azure-Data Base voor MariaDB met dump en herstel

In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure data base for MariaDB:
- Dump en herstel met behulp van een opdracht regel programma (met behulp van mysqldump) 
- Dump en herstel met behulp van phpMyAdmin

## <a name="prerequisites"></a>Vereisten
Ga als volgt te werk voordat u begint met het migreren van uw Data Base:
- Een [Azure database for MariaDB server-Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)maken.
- Installeer het [mysqldump](https://mariadb.com/kb/en/library/mysqldump/) -opdracht regel programma.
- Down load en Installeer [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) of een ander mysql-hulp programma van derden voor het uitvoeren van dump-en Restore-opdrachten.

## <a name="use-common-tools"></a>Algemene hulpprogram ma's gebruiken
Gebruik veelvoorkomende hulpprogram ma's en hulpprogram ma's zoals MySQL Workbench of mysqldump om gegevens extern te verbinden en te herstellen in uw Azure-Data Base voor MariaDB. Gebruik deze hulpprogram ma's op de client computer met een Internet verbinding om verbinding te maken met de Azure data base for MariaDB. Gebruik een met SSL versleutelde verbinding als een aanbevolen beveiligings procedure. Zie [SSL-connectiviteit configureren in azure database for MariaDB](concepts-ssl-connection-security.md)voor meer informatie. U hoeft de dump bestanden niet te verplaatsen naar een speciale Cloud locatie wanneer u gegevens migreert naar uw Azure-Data Base voor MariaDB. 

## <a name="common-uses-for-dump-and-restore"></a>Veelvoorkomende toepassingen voor dump en herstel
U kunt MySQL-hulpprogram ma's, zoals mysqldump en mysqlpump, gebruiken om data bases te dumpen en te laden in een Azure data base for MariaDB-server in verschillende algemene scenario's. 

- Gebruik database dumps wanneer u een volledige data base migreert. Deze aanbeveling houdt in dat u een grote hoeveelheid gegevens verplaatst of wanneer u de onderbreking van de service voor Live sites of toepassingen wilt minimaliseren. 
-  Zorg ervoor dat alle tabellen in de data base de InnoDB-opslag engine gebruiken wanneer u gegevens laadt in uw Azure-Data Base voor MariaDB. Azure Database for MariaDB ondersteunt alleen de InnoDB-opslag engine en geen andere opslag engines. Als uw tabellen zijn geconfigureerd met andere opslag engines, zet u ze om in de InnoDB-engine-indeling voordat u deze migreert naar uw Azure-Data Base voor MariaDB.
   
   Als u bijvoorbeeld een WordPress-app of een web-app hebt die gebruikmaakt van MyISAM-tabellen, moet u deze tabellen eerst converteren door ze te migreren naar InnoDB-indeling voordat u ze herstelt in uw Azure Data Base voor MariaDB. Gebruik de-component `ENGINE=InnoDB` om de engine in te stellen die moet worden gebruikt voor het maken van een nieuwe tabel en breng vervolgens de gegevens over naar de compatibele tabel voordat u deze herstelt. 

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
- Als u compatibiliteits problemen wilt voor komen wanneer u data bases wilt dumpen, moet u ervoor zorgen dat u dezelfde versie van MariaDB gebruikt op de bron-en doel systemen. Als uw bestaande MariaDB-server bijvoorbeeld versie 10,2 is, moet u migreren naar uw Azure-Data Base voor MariaDB die is geconfigureerd voor het uitvoeren van versie 10,2. De `mysql_upgrade` opdracht werkt niet op een Azure database for MariaDB-server en wordt niet ondersteund. Als u een upgrade wilt uitvoeren voor MariaDB-versies, moet u eerst de data base van uw eerdere versie dumpen of exporteren naar een nieuwere versie van MariaDB in uw eigen omgeving. U kunt vervolgens uitvoeren `mysql_upgrade` voordat u migreert naar Azure data base for MariaDB.

## <a name="performance-considerations"></a>Prestatieoverwegingen
Houd rekening met de volgende overwegingen om de prestaties te optimaliseren wanneer u grote data bases wilt dumpen:
-   Gebruik de `exclude-triggers` optie in mysqldump. Sluit triggers uit van dump bestanden om te voor komen dat de trigger opdrachten worden geactiveerd tijdens het herstellen van gegevens. 
-   Gebruik de `single-transaction` optie om de modus voor transactie isolatie in te stellen op herhaalbaar lezen en een SQL-instructie voor het starten van een trans actie naar de server te verzenden voordat gegevens worden gedumpt. Het dumpen van veel tabellen binnen één trans actie leidt ertoe dat er extra opslag ruimte wordt verbruikt tijdens het terugzetten. De `single-transaction` optie en de `lock-tables` optie sluiten elkaar wederzijds uit. Dit komt doordat voor VERGRENDELings tabellen alle trans acties die in behandeling zijn, impliciet moeten worden doorgevoerd. Als u grote tabellen wilt dumpen, moet u de optie combi neren `single-transaction` met de `quick` optie. 
-   Gebruik de `extended-insert` syntaxis met meerdere rijen die verschillende waardelijsten bevat. Deze benadering resulteert in een kleiner dump bestand en versnelt het invoegen wanneer het bestand opnieuw wordt geladen.
-  Gebruik de `order-by-primary` optie in mysqldump wanneer u data bases wilt dumpen, zodat de gegevens in de volg orde van de primaire sleutel worden gescripteerd.
-   Gebruik de `disable-keys` optie in mysqldump wanneer u gegevens wilt dumpen, om beperkingen voor refererende sleutels uit te scha kelen voordat de belasting wordt geladen. Het uitschakelen van externe-sleutel controles helpt de prestaties te verbeteren. Schakel de beperkingen in en controleer de gegevens na de belasting om de referentiële integriteit te waarborgen.
-   Gebruik gepartitioneerde tabellen wanneer dat nodig is.
-   Gegevens parallel laden. Vermijd te veel parallellisme, waardoor u een resource limiet kunt oplopen en resources bewaken met behulp van de metrische gegevens die beschikbaar zijn in de Azure Portal. 
-   Gebruik de `defer-table-indexes` optie in mysqlpump wanneer u data bases wilt dumpen, zodat het maken van de index plaatsvindt nadat tabel gegevens zijn geladen.
-   Kopieer de back-upbestanden naar een Azure Blob Store en voer de herstel bewerking uit. Deze aanpak is veel sneller dan het herstel via internet.

## <a name="create-a-backup-file"></a>Een back-upbestand maken

Als u een back-up wilt maken van een bestaande MariaDB-Data Base op de lokale on-premises server of op een virtuele machine, voert u de volgende opdracht uit met behulp van mysqldump: 

```bash
$ mysqldump --opt -u <uname> -p<pass> <dbname> > <backupfile.sql>
```

De para meters die u moet opgeven, zijn:
- *\<uname>*: De gebruikers naam van uw data base 
- *\<pass>*: Het wacht woord voor uw data base (Houd er rekening mee dat er geen spatie is tussen-p en het wacht woord) 
- *\<dbname>*: De naam van uw data base 
- *\<backupfile.sql>*: De bestands naam voor de back-up van de data base 
- *\<--opt>*: De optie mysqldump 

Als u bijvoorbeeld een back-up wilt maken van een Data Base met de naam *testdb* op uw MariaDB-server met de gebruikers naam *test* gebruiker en zonder wacht woord aan een bestand testdb_backup. SQL, gebruikt u de volgende opdracht. De opdracht maakt een back-up van de `testdb` Data base in een bestand met de naam `testdb_backup.sql` , dat alle SQL-instructies bevat die nodig zijn om de data base opnieuw te maken. 

```bash
$ mysqldump -u root -p testdb > testdb_backup.sql
```
Als u specifieke tabellen wilt selecteren waarvan u een back-up wilt maken in uw data base, geeft u de tabel namen op, gescheiden door spaties. Als u bijvoorbeeld alleen een back-up wilt maken van de tabellen Tabel1 en tabel2 van de *testdb*, volgt u dit voor beeld: 

```bash
$ mysqldump -u root -p testdb table1 table2 > testdb_tables_backup.sql
```

Als u een back-up van meer dan één data base tegelijk wilt maken, gebruikt u de-data base-switch en geeft u een lijst van de database namen op, gescheiden door spaties. 

```bash
$ mysqldump -u root -p --databases testdb1 testdb3 testdb5 > testdb135_backup.sql 
```

## <a name="create-a-database-on-the-target-server"></a>Een Data Base op de doel server maken
Maak een lege data base op de doel Azure Database for MariaDB server waarnaar u de gegevens wilt migreren. Gebruik een hulp programma zoals MySQL Workbench om de data base te maken. De data base kan dezelfde naam hebben als de data base die de dumped gegevens bevat of u kunt een Data Base maken met een andere naam.

Als u verbinding wilt krijgen, zoekt u de verbindings gegevens in het deel venster **overzicht** van uw Azure Data Base voor MariaDB.

![Scherm opname van het overzichts deel venster voor een Azure data base for MariaDB-server in de Azure Portal.](./media/howto-migrate-dump-restore/1_server-overview-name-login.png)

Voeg in MySQL Workbench de verbindings gegevens toe.

![Scherm opname van het deel venster MySQL-verbindingen in MySQL Workbench.](./media/howto-migrate-dump-restore/2_setup-new-connection.png)

## <a name="restore-your-mariadb-database"></a>Uw MariaDB-data base herstellen
Nadat u de doel database hebt gemaakt, kunt u de MySQL-opdracht of MySQL Workbench gebruiken om de gegevens in de zojuist gemaakte data base uit het dump bestand te herstellen.

```bash
mysql -h <hostname> -u <uname> -p<pass> <db_to_restore> < <backupfile.sql>
```

In dit voor beeld herstelt u de gegevens in de zojuist gemaakte Data Base op de doel Azure Database for MariaDB server.

```bash
$ mysql -h mydemoserver.mariadb.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```

## <a name="export-your-mariadb-database-by-using-phpmyadmin"></a>Uw MariaDB-data base exporteren met behulp van phpMyAdmin

Als u wilt exporteren, kunt u het algemene hulp programma phpMyAdmin gebruiken, dat mogelijk al lokaal is geïnstalleerd in uw omgeving. Ga als volgt te werk om uw MariaDB-data base te exporteren:
1. Open phpMyAdmin.
1. Selecteer uw data base in het linkerdeel venster en selecteer vervolgens de koppeling **exporteren** . Er wordt een nieuwe pagina weer gegeven om de dump van de Data Base weer te geven.
1. Selecteer in het gebied **exporteren** de koppeling **Alles selecteren** om de tabellen in de data base te kiezen. 
1. Selecteer in het gebied **SQL-opties** de gewenste opties. 
1. Selecteer de optie **Opslaan als bestand** en de bijbehorende compressie optie en selecteer vervolgens **Go**. Sla het bestand lokaal op bij de prompt.

## <a name="import-your-database-by-using-phpmyadmin"></a>Uw data base importeren met behulp van phpMyAdmin

Het import proces is vergelijkbaar met het export proces. Ga als volgt te werk:
1. Open phpMyAdmin. 
1. Selecteer op de pagina Setup van phpMyAdmin **toevoegen** om uw Azure database for MariaDB-server toe te voegen. 
1. Voer de verbindings gegevens en aanmeldings gegevens in.
1. Maak een Data Base met de juiste naam en selecteer deze in het linkerdeel venster. Als u de bestaande data base opnieuw wilt schrijven, selecteert u de naam van de data base, selecteert u alle selectie vakjes naast de tabel namen en selecteert u **neerzetten** om de bestaande tabellen te verwijderen. 
1. Selecteer de **SQL** -koppeling om de pagina weer te geven waar u SQL-opdrachten kunt invoeren of uw SQL-bestand moet uploaden. 
1. Selecteer de knop **Bladeren** om het database bestand te zoeken. 
1. Selecteer de knop **Go** om de back-up te exporteren, de SQL-opdrachten uit te voeren en de data base opnieuw te maken.

## <a name="next-steps"></a>Volgende stappen
- [Verbind toepassingen met uw Azure Data Base voor MariaDB](./howto-connection-string.md).
