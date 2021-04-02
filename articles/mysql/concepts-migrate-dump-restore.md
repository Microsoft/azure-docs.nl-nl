---
title: Migreren met dump en Restore-Azure Database for MySQL
description: In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure Database for MySQL, met behulp van hulpprogram ma's zoals mysqldump, MySQL Workbench en PHPMyAdmin.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 10/30/2020
ms.openlocfilehash: f21587fe6a48d042ed98c126beb2a7dcaa39b7d8
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94537914"
---
# <a name="migrate-your-mysql-database-to-azure-database-for-mysql-using-dump-and-restore"></a>Uw MySQL-database migreren naar Azure Database voor MySQL met behulp van dumpen en terugzetten

[!INCLUDE[applies-to-single-flexible-server](includes/applies-to-single-flexible-server.md)]

In dit artikel worden twee algemene manieren uitgelegd voor het maken van back-ups en het herstellen van data bases in uw Azure Database for MySQL
- Dump en herstel vanaf de opdracht regel (met behulp van mysqldump)
- Dump en herstel met behulp van PHPMyAdmin

U kunt ook de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide) raadplegen voor gedetailleerde informatie en voor beelden over het migreren van data bases naar Azure database for MySQL. Deze hand leiding bevat richt lijnen voor een geslaagde planning en uitvoering van een MySQL-migratie naar Azure.

## <a name="before-you-begin"></a>Voordat u begint
Als u deze hand leiding wilt door lopen, hebt u het volgende nodig:
- [Een Azure Database for MySQL-server maken - Azure Portal](quickstart-create-mysql-server-database-using-azure-portal.md)
- [mysqldump](https://dev.mysql.com/doc/refman/5.7/en/mysqldump.html) -opdracht regel programma dat op een computer is geïnstalleerd.
- [MySQL Workbench](https://dev.mysql.com/downloads/workbench/) of een ander mysql-hulp programma van derden om dump-en herstel opdrachten uit te voeren.

> [!TIP]
> Als u grote data bases wilt migreren met data base-grootten van meer dan 1 TBs, kunt u overwegen om gebruik te maken van de hulpprogram ma's van de Community zoals **mydumper/myloader** , die ondersteuning bieden voor parallel exporteren en importeren. Meer informatie [over het migreren van grote MySQL-data bases](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).


## <a name="common-use-cases-for-dump-and-restore"></a>Veelvoorkomend gebruik: cases voor dump en herstel

Meest voorkomende gebruiks voorbeelden zijn:

- **Overstappen van een andere beheerde service provider** : de meeste beheerde service provider biedt mogelijk geen toegang tot het fysieke opslag bestand om veiligheids redenen, dus is logische back-up en herstellen de enige optie om te migreren.
- **Migratie van on-premises omgeving of virtuele machine** -Azure database for MySQL biedt geen ondersteuning voor het herstellen van fysieke back-ups, waardoor logische back-ups en herstel bewerkingen als enige benadering worden uitgevoerd.
- **Als u uw back-upopslag verplaatst van lokaal redundant naar geografisch redundante opslag** -Azure database for MySQL kunt het configureren van lokaal redundante of geografisch redundante opslag voor back-up alleen toestaan tijdens het maken van de server. Zodra de server is ingericht, kunt u de optie voor redundantie van back-upopslag niet meer wijzigen. Als u uw back-upopslag wilt verplaatsen van lokaal redundante opslag naar geo-redundante opslag, is dump en Restore de enige optie. 
-  **Migratie van alternatieve opslag engines naar InnoDB** -Azure database for MySQL ondersteunt alleen InnoDB-opslag engine en biedt daarom geen ondersteuning voor alternatieve opslag engines. Als uw tabellen zijn geconfigureerd met andere opslag engines, converteert u deze naar de InnoDB-engine-indeling voordat u de migratie naar Azure Database for MySQL.

    Als u bijvoorbeeld een WordPress of WebApp met behulp van de MyISAM-tabellen hebt, moet u deze tabellen eerst converteren door de migratie naar de InnoDB-indeling voordat u naar Azure Database for MySQL herstelt. Gebruik de-component `ENGINE=InnoDB` om de engine in te stellen die wordt gebruikt bij het maken van een nieuwe tabel en vervolgens de gegevens over te dragen naar de compatibele tabel vóór de herstel bewerking.

   ```sql
   INSERT INTO innodb_table SELECT * FROM myisam_table ORDER BY primary_key_columns
   ```
> [!Important]
> - U kunt compatibiliteitsproblemen voorkomen door ervoor te zorgen dat dezelfde versie van MySQL wordt gebruikt in het bron- en doelsysteem bij het dumpen van databases. Als uw bestaande MySQL-server bijvoorbeeld versie 5,7 is, moet u migreren naar Azure Database for MySQL geconfigureerd voor het uitvoeren van versie 5,7. De `mysql_upgrade` opdracht werkt niet op een Azure database for mysql-server en wordt niet ondersteund. 
> - Als u een upgrade wilt uitvoeren naar MySQL-versies, moet u eerst de data base van uw lagere versie dumpen of exporteren naar een hogere versie van MySQL in uw eigen omgeving. Voer vervolgens uit `mysql_upgrade` voordat u een migratie uitvoert in een Azure database for MySQL.

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
-   Gebruik de `skip-definer` optie in mysqlpump om de instructie define en SQL Security te weglaten van de Create-instructies voor weer gaven en opgeslagen procedures.  Wanneer u het dump bestand opnieuw laadt, worden er objecten gemaakt die gebruikmaken van de standaard definiëren en SQL-BEVEILIGINGS waarden.
-   Kopieer de back-upbestanden naar een Azure-Blob/-Store en voer de herstel bewerking uit. Dit is veel sneller dan het terugzetten via internet.

## <a name="create-a-database-on-the-target-azure-database-for-mysql-server"></a>Een Data Base maken op de doel-Azure Database for MySQL server
Maak een lege data base op de doel Azure Database for MySQL server waarnaar u de gegevens wilt migreren. Gebruik een hulp programma zoals MySQL Workbench of mysql.exe om de data base te maken. De data base kan dezelfde naam hebben als de Data Base waarin de dumping gegevens zijn opgenomen of u kunt een Data Base met een andere naam maken.

Als u verbinding wilt krijgen, zoekt u de verbindings gegevens in het **overzicht** van uw Azure database for MySQL.

:::image type="content" source="./media/concepts-migrate-dump-restore/1_server-overview-name-login.png" alt-text="De verbindings gegevens in de Azure Portal zoeken":::

Voeg de verbindings gegevens toe aan uw MySQL Workbench.

:::image type="content" source="./media/concepts-migrate-dump-restore/2_setup-new-connection.png" alt-text="MySQL Workbench-verbindings reeks":::

## <a name="preparing-the-target-azure-database-for-mysql-server-for-fast-data-loads"></a>De doel Azure Database for MySQL server voorbereiden voor het snel laden van gegevens
De volgende server parameters en configuratie moeten worden gewijzigd om de doel Azure Database for MySQL server voor te bereiden voor sneller laden van gegevens.
- max_allowed_packet: ingesteld op 1073741824 (1 GB) om te voor komen dat een overflow probleem wordt veroorzaakt door lange rijen.
- slow_query_log: ingesteld op uit om het langzame query logboek uit te scha kelen. Hiermee elimineert u de overhead die wordt veroorzaakt door het langzaam registreren van query's tijdens het laden van gegevens.
- query_store_capture_mode: Stel deze optie in op geen om het query archief uit te scha kelen. Dit elimineert de overhead die wordt veroorzaakt door steekproef activiteiten door de query Store.
- innodb_buffer_pool_size: de server omhoog schalen naar 32 vCore geoptimaliseerd voor geheugen van de prijs categorie van de portal tijdens de migratie om de innodb_buffer_pool_size te verg Roten. Innodb_buffer_pool_size kunnen alleen worden verhoogd door de reken kracht voor Azure Database for MySQL server omhoog te schalen.
- innodb_io_capacity & innodb_io_capacity_max-Wijzig in 9000 van de server parameters in Azure Portal om het IO-gebruik te verbeteren en te optimaliseren voor de migratie snelheid.
- innodb_write_io_threads & innodb_write_io_threads-Wijzig in 4 van de server parameters in Azure Portal om de snelheid van de migratie te verbeteren.
- Opslaglaag omhoog schalen: de IOPs voor Azure Database for MySQL server neemt geleidelijk toe met de toename van de opslaglaag. Als u sneller wilt laden, kunt u de opslaglaag verhogen om de ingerichte IOPs te verg Roten. Houd er rekening mee dat de opslag alleen omhoog kan worden geschaald.

Zodra de migratie is voltooid, kunt u de server parameters en de configuratie van de compute-laag herstellen naar de vorige waarden.

## <a name="dump-and-restore-using-mysqldump-utility"></a>Dump en herstel met het hulp programma mysqldump

### <a name="create-a-backup-file-from-the-command-line-using-mysqldump"></a>Maak een back-upbestand via de opdracht regel met behulp van mysqldump
Als u een back-up wilt maken van een bestaande MySQL-data base op de lokale on-premises server of op een virtuele machine, voert u de volgende opdracht uit:
```bash
$ mysqldump --opt -u [uname] -p[pass] [dbname] > [backupfile.sql]
```

De para meters die u moet opgeven, zijn:
- [uname] Uw data base-gebruikers naam
- door Het wacht woord voor uw data base (er is geen spatie tussen-p en het wacht woord)
- dbname De naam van uw data base
- [upbestand. SQL] de bestands naam van de back-up van de data base
- [--opt] De optie mysqldump

Als u bijvoorbeeld een back-up wilt maken van een Data Base met de naam ' testdb ' op de MySQL-server met de gebruikers naam ' test ' en zonder wacht woord aan een bestand testdb_backup. SQL, gebruikt u de volgende opdracht. De opdracht maakt een back-up van de `testdb` Data base in een bestand met de naam `testdb_backup.sql` , dat alle SQL-instructies bevat die nodig zijn om de data base opnieuw te maken. Zorg ervoor dat de gebruikers naam ' test ' ten minste de bevoegdheid SELECT heeft voor tabellen die zijn gedumpt, de weer gave voor dumped weer gaven, TRIGGER voor triggers met dumping en vergren delen als de optie--single-trans actie niet wordt gebruikt.

```bash
GRANT SELECT, LOCK TABLES, SHOW VIEW ON *.* TO 'testuser'@'hostname' IDENTIFIED BY 'password';
```
Voer nu mysqldump uit om de back-up van de `testdb` Data Base te maken

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

### <a name="restore-your-mysql-database-using-command-line-or-mysql-workbench"></a>Uw MySQL-data base herstellen met behulp van opdracht regel of MySQL Workbench
Wanneer u de doel database hebt gemaakt, kunt u de MySQL-opdracht of MySQL Workbench gebruiken om de gegevens in de specifieke zojuist gemaakte data base te herstellen vanuit het dump bestand.
```bash
mysql -h [hostname] -u [uname] -p[pass] [db_to_restore] < [backupfile.sql]
```
In dit voor beeld herstelt u de gegevens in de zojuist gemaakte Data Base op de doel Azure Database for MySQL server.

Hier volgt een voor beeld van het gebruik van deze **MySQL** voor **één server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p testdb < testdb_backup.sql
```
Hier volgt een voor beeld van het gebruik van deze **MySQL** voor **flexibele server** :

```bash
$ mysql -h mydemoserver.mysql.database.azure.com -u myadmin -p testdb < testdb_backup.sql
```
---

## <a name="dump-and-restore-using-phpmyadmin"></a>Dump en herstel met behulp van PHPMyAdmin
Volg deze stappen om een Data Base te dumpen en te herstellen met behulp van PHPMyadmin.

> [!NOTE]
> Voor een enkele server moet de gebruikers naam de volgende indeling hebben, username@servername maar voor een flexibele server kunt u alleen ' gebruikers naam ' gebruiken als u ' username@servername ' gebruikt voor een flexibele server, mislukt de verbinding.

### <a name="export-with-phpmyadmin"></a>Exporteren met PHPMyadmin
Als u wilt exporteren, kunt u gebruikmaken van de algemene hulp middelen phpMyAdmin, die u mogelijk al lokaal hebt geïnstalleerd in uw omgeving. Uw MySQL-data base exporteren met behulp van PHPMyAdmin:
1. Open phpMyAdmin.
2. Selecteer uw data base. Klik op de naam van de data base in de lijst aan de linkerkant.
3. Klik op de koppeling **exporteren** . Er wordt een nieuwe pagina weer gegeven om de dump van de Data Base weer te geven.
4. Klik in het gedeelte exporteren op de koppeling **Alles selecteren** om de tabellen in de data base te kiezen.
5. Klik in het gebied SQL-opties op de gewenste opties.
6. Klik op de optie **Opslaan als bestand** en de bijbehorende compressie optie en klik vervolgens op de knop **Go** . Er wordt een dialoog venster weer gegeven waarin u wordt gevraagd het bestand lokaal op te slaan.

### <a name="import-using-phpmyadmin"></a>Importeren met behulp van PHPMyAdmin
Het importeren van uw data base lijkt op het exporteren. Voer de volgende acties uit:
1. Open phpMyAdmin.
2. Klik op de pagina phpMyAdmin Setup op **toevoegen** om uw Azure database for mysql-server toe te voegen. Geef de verbindings gegevens en aanmeldings gegevens op.
3. Maak een Data Base met de juiste naam en selecteer deze aan de linkerkant van het scherm. Als u de bestaande data base opnieuw wilt schrijven, klikt u op de naam van de data base, selecteert u alle selectie vakjes naast de tabel namen en selecteert u **neerzetten** om de bestaande tabellen te verwijderen.
4. Klik op de **SQL** -koppeling om de pagina weer te geven waarop u SQL-opdrachten kunt typen of uw SQL-bestand moet uploaden.
5. Gebruik de knop **Bladeren** om het database bestand te zoeken.
6. Klik op de knop **Go** om de back-up te exporteren, de SQL-opdrachten uit te voeren en de data base opnieuw te maken.

## <a name="known-issues"></a>Bekende problemen
Voor bekende problemen, tips en trucs raden we u aan onze [techcommunity-blog](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/tips-and-tricks-in-using-mysqldump-and-mysql-restore-to-azure/ba-p/916912)te bekijken.

## <a name="next-steps"></a>Volgende stappen
- [Toepassingen verbinden met Azure database for MySQL](./howto-connection-string.md).
- Zie de [hand leiding voor database migratie](https://github.com/Azure/azure-mysql/tree/master/MigrationGuide)voor meer informatie over het migreren van data bases naar Azure database for MySQL.
- Als u grote data bases wilt migreren met data base-grootten van meer dan 1 TBs, kunt u overwegen om gebruik te maken van de hulpprogram ma's van de Community zoals **mydumper/myloader** , die ondersteuning bieden voor parallel exporteren en importeren. Meer informatie [over het migreren van grote MySQL-data bases](https://techcommunity.microsoft.com/t5/azure-database-for-mysql/best-practices-for-migrating-large-databases-to-azure-database/ba-p/1362699).
