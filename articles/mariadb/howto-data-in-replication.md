---
title: Replicatie van gegevens configureren-Azure Database for MariaDB
description: In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt in Azure Database for MariaDB.
author: savjani
ms.author: pariks
ms.service: mariadb
ms.topic: how-to
ms.date: 01/18/2021
ms.openlocfilehash: 67e4da13d6954342b9979eb57a35c812cb63bb3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98665120"
---
# <a name="configure-data-in-replication-in-azure-database-for-mariadb"></a>Replicatie van inkomende gegevens configureren in Azure Database for MariaDB

In dit artikel wordt beschreven hoe u [replicatie van inkomende gegevens](concepts-data-in-replication.md) instelt in azure database for MariaDB door de bron-en replica servers te configureren. In dit artikel wordt ervan uitgegaan dat u een eerdere ervaring hebt met MariaDB-servers en-data bases.

Voor het maken van een replica in de Azure Database for MariaDB-service, [replicatie van inkomende gegevens](concepts-data-in-replication.md) synchroniseert gegevens van een bron MariaDB on-premises, in virtuele machines (vm's) of in Cloud database services. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MariaDB. Zie het [overzicht van binlog-replicatie](https://mariadb.com/kb/en/library/replication-overview/)voor meer informatie over binlog-replicatie.

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van gegevens replicatie voordat u de stappen in dit artikel uitvoert.

> [!NOTE]
> Als uw bron Server versie 10,2 of nieuwer is, raden we u aan om Replicatie van inkomende gegevens in te stellen met behulp van de [algemene trans actie-id](https://mariadb.com/kb/en/library/gtid/).

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.

## <a name="create-a-mariadb-server-to-use-as-a-replica"></a>Een MariaDB-server maken om te gebruiken als replica

1. Maak een nieuwe Azure Database for MariaDB-server (bijvoorbeeld replica.mariadb.database.azure.com). De server is de replica server in Replicatie van inkomende gegevens.

    Zie [een Azure database for MariaDB-server maken met behulp van de Azure Portal](quickstart-create-mariadb-server-database-using-azure-portal.md)voor meer informatie over het maken van een server.

   > [!IMPORTANT]
   > U moet de Azure Database for MariaDB-server maken in de prijs Categorieën Algemeen of geoptimaliseerd voor geheugen.

2. Maak identieke gebruikers accounts en bijbehorende bevoegdheden.

    Gebruikers accounts worden niet gerepliceerd van de bron server naar de replica server. Als u gebruikers toegang wilt geven tot de replica-server, moet u hand matig alle accounts en de bijbehorende bevoegdheden maken op de nieuw gemaakte Azure Database for MariaDB-server.

3. Voeg het IP-adres van de bron server toe aan de firewall regels van de replica. 

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-portal.md) of [Azure CLI](howto-manage-firewall-cli.md).

## <a name="configure-the-source-server"></a>De bron server configureren

In de volgende stappen wordt de MariaDB-server die on-premises, in een VM of in een Cloud database service voor Replicatie van inkomende gegevens wordt gehost, voor bereid en geconfigureerd. De MariaDB-server is de bron in Replicatie van inkomende gegevens.

1. Controleer de [vereisten van de primaire server](concepts-data-in-replication.md#requirements) voordat u doorgaat. 

2. Zorg ervoor dat de bron server zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat en dat de bron server een **openbaar IP-adres** heeft, de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft. 

   Test de verbinding met de bron server door te proberen verbinding te maken vanaf een hulp programma zoals de MySQL-opdracht regel die wordt gehost op een andere computer of op basis van het [Azure Cloud shell](../cloud-shell/overview.md) dat beschikbaar is in de Azure Portal.

   Als uw organisatie een strikt beveiligings beleid heeft en niet alle IP-adressen op de bron server toestaat communicatie van Azure naar uw bron server in te scha kelen, kunt u de onderstaande opdracht gebruiken om het IP-adres van uw Azure Database for MariaDB server te bepalen.

   1. Meld u aan bij uw Azure Database for MariaDB met een hulp programma zoals de MySQL-opdracht regel.
   2. Voer de onderstaande query uit.

      ```bash
      mysql> SELECT @@global.redirect_server_host;
      ```

      Hieronder ziet u een voorbeeld van uitvoer:

      ```bash
      +-----------------------------------------------------------+
      | @@global.redirect_server_host                             |
      +-----------------------------------------------------------+
      | e299ae56f000.tr1830.westus1-a.worker.database.windows.net |
       +-----------------------------------------------------------+
      ```

   3. Sluit af vanaf de MySQL-opdracht regel.
   4. Voer de onderstaande stappen uit in het hulp programma Ping om het IP-adres op te halen.

      ```bash
      ping <output of step 2b>
      ```

      Bijvoorbeeld:

      ```bash
      C:\Users\testuser> ping e299ae56f000.tr1830.westus1-a.worker.database.windows.net
      Pinging tr1830.westus1-a.worker.database.windows.net (**11.11.111.111**) 56(84) bytes of data.
      ```

   5. Configureer de firewall regels van uw bron server zodanig dat het IP-adres van de vorige stap wordt vermeld op poort 3306.

   > [!NOTE]
   > Dit IP-adres kan gewijzigd worden door onderhouds-en implementatie bewerkingen. Deze verbindings methode is alleen voor klanten die niet in staat zijn om alle IP-adressen op 3306-poort toe te staan.

3. Schakel binaire logboek registratie in.

    Als u wilt zien of binaire logboek registratie is ingeschakeld op de primaire, voert u de volgende opdracht in:

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de variabele [`log_bin`](https://mariadb.com/kb/en/library/replication-and-binary-log-server-system-variables/#log_bin) de waarde retourneert `ON` , wordt de binaire logboek registratie ingeschakeld op de server.

   Als `log_bin` de waarde wordt geretourneerd `OFF` , bewerkt u het bestand **My. cnf** zodat `log_bin=ON` binaire logboek registratie wordt ingeschakeld. Start de server opnieuw op om de wijziging van kracht te laten worden.

4. Configureer de instellingen van de bron server.

    Replicatie van inkomende gegevens moet de para meter `lower_case_table_names` consistent zijn tussen de bron-en replica servers. De `lower_case_table_names` para meter wordt standaard ingesteld op `1` Azure database for MariaDB.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Maak een nieuwe replicatie functie en stel machtigingen in.

   Maak een gebruikers account op de bron server die is geconfigureerd met replicatie bevoegdheden. U kunt een account maken met behulp van SQL-opdrachten of MySQL Workbench. Als u van plan bent om met SSL te repliceren, moet u dit opgeven wanneer u het gebruikers account maakt.

   Zie de [MariaDB-documentatie](https://mariadb.com/kb/en/library/create-user/)voor meer informatie over het toevoegen van gebruikers accounts op de bron server.

   Met behulp van de volgende opdrachten heeft de nieuwe replicatie functie vanaf elke computer toegang tot de bron, niet alleen de computer die als host fungeert voor de bron zelf. Geef voor deze toegang **syncuser \@ '% '** op in de opdracht om een gebruiker te maken.

   Zie [account namen opgeven](https://mariadb.com/kb/en/library/create-user/#account-names)voor meer informatie over MariaDB-documentatie.

   **SQL-opdracht**

   - Replicatie met SSL

       Als u SSL wilt vereisen voor alle gebruikers verbindingen, voert u de volgende opdracht in om een gebruiker te maken:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
       ```

   - Replicatie zonder SSL

       Als SSL niet is vereist voor alle verbindingen, voert u de volgende opdracht in om een gebruiker te maken:

       ```sql
       CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
       GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
       ```

   **MySQL Workbench**

   Als u de replicatie functie in MySQL Workbench wilt maken, selecteert u in het deel venster **beheer** de optie **gebruikers en bevoegdheden**. Selecteer vervolgens **account toevoegen**.

   ![Gebruikers en bevoegdheden](./media/howto-data-in-replication/users_privileges.png)

   Voer een gebruikers naam in het veld **aanmeldings naam** in.

   ![Gebruiker synchroniseren](./media/howto-data-in-replication/syncuser.png)

   Selecteer het paneel **beheerders rollen** en selecteer in de lijst met **globale bevoegdheden** **replicatie-slave**. Selecteer **Toep assen** om de replicatie functie te maken.

   ![Replicatie-slave](./media/howto-data-in-replication/replicationslave.png)

6. Stel de bron server in op de modus alleen-lezen.

   Voordat u een Data Base dumpt, moet de server in de modus alleen-lezen worden geplaatst. In de modus alleen-lezen kan de bron geen schrijf transacties verwerken. Als u bedrijfs impact wilt voor komen, moet u het alleen-lezen venster tijdens een rustige tijd plannen.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. De huidige binaire logboek bestandsnaam en-offset ophalen.

   Voer de opdracht uit om de naam van het huidige binaire logboek bestand en-offset te bepalen [`show master status`](https://mariadb.com/kb/en/library/show-master-status/) .

   ```sql
   show master status;
   ```

   De resultaten moeten er ongeveer uitzien als in de volgende tabel:

   ![Resultaten van de hoofd status](./media/howto-data-in-replication/masterstatus.png)

   Noteer de naam van het binaire bestand, omdat deze wordt gebruikt in latere stappen.

8. De GTID-positie ophalen (optioneel, vereist voor replicatie met GTID).

   Voer de functie uit [`BINLOG_GTID_POS`](https://mariadb.com/kb/en/library/binlog_gtid_pos/) om de GTID-positie voor de bijbehorende binlog-bestands naam en-offset op te halen.
  
    ```sql
    select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);
    ```

## <a name="dump-and-restore-the-source-server"></a>De bron server dumpen en herstellen

1. Dump alle data bases van de bron server.

   Gebruik mysqldump om alle data bases van de bron server te dumpen. Het is niet nodig om de MySQL-bibliotheek en test bibliotheek te dumpen.

    Zie [dump and Restore](howto-migrate-dump-restore.md)(Engelstalig) voor meer informatie.

2. Stel de bron server in op de modus lezen/schrijven.

   Nadat de data base is gedumpt, wijzigt u de bron MariaDB-server weer in de modus lezen/schrijven.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Herstel het dump bestand naar de nieuwe server.

   Herstel het dump bestand naar de server die in de Azure Database for MariaDB-service is gemaakt. Zie [dump & herstellen](howto-migrate-dump-restore.md) voor het herstellen van een dump bestand naar een MariaDB-server.

   Als het dump bestand groot is, uploadt u het naar een virtuele machine in azure binnen dezelfde regio als de replica server. Zet het bestand terug naar de Azure Database for MariaDB-server vanuit de VM.

## <a name="link-the-source-and-replica-servers-to-start-data-in-replication"></a>Koppel de bron-en replica servers om Replicatie van inkomende gegevens te starten

1. Stel de bron server in.

   Alle Replicatie van inkomende gegevens-functies worden uitgevoerd door opgeslagen procedures. U kunt alle procedures vinden op [replicatie van inkomende gegevens opgeslagen procedures](reference-stored-procedures.md). Opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench.

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doel replica server in de Azure DB for MariaDB-service. Stel vervolgens het externe exemplaar in als de bron server met behulp van de `mysql.az_replication_change_master` of `mysql.az_replication_change_master_with_gtid` opgeslagen procedure op de Azure DB for MariaDB-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   of

   ```sql
   CALL mysql.az_replication_change_master_with_gtid('<master_host>', '<master_user>', '<master_password>', 3306, '<master_gtid_pos>', '<master_ssl_ca>');
   ```

   - master_host: de hostnaam van de bron server
   - master_user: gebruikers naam voor de bron server
   - master_password: wacht woord voor de bron server
   - master_log_file: de naam van het binaire logboek bestand is niet actief `show master status`
   - master_log_pos: de positie van het binaire logboek van wordt uitgevoerd `show master status`
   - master_gtid_pos: GTID positie van uitvoering `select BINLOG_GTID_POS('<binlog file name>', <binlog offset>);`
   - master_ssl_ca: de context van het CA-certificaat. Als u geen gebruik maakt van SSL, geeft u een lege teken reeks door. *


    * We raden u aan de para meter master_ssl_ca aan te geven als een variabele. Zie de volgende voor beelden voor meer informatie.

   **Voorbeelden**

   - Replicatie met SSL

       Maak de variabele `@cert` door de volgende opdrachten uit te voeren:

       ```sql
       SET @cert = '-----BEGIN CERTIFICATE-----
       PLACE YOUR PUBLIC KEY CERTIFICATE\'S CONTEXT HERE
       -----END CERTIFICATE-----'
       ```

       Replicatie met SSL wordt ingesteld tussen een bron server die wordt gehost in het domein companya.com en een replica server gehost in Azure Database for MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, @cert);
       ```

   - Replicatie zonder SSL

       Replicatie zonder SSL wordt ingesteld tussen een bron server die wordt gehost in het domein companya.com en een replica server gehost in Azure Database for MariaDB. Deze opgeslagen procedure wordt uitgevoerd op de replica.

       ```sql
       CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mariadb-bin.000016', 475, '');
       ```

2. Replicatie starten.

   Roep de `mysql.az_replication_start` opgeslagen procedure aan om de replicatie te starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

3. Controleer de replicatie status.

   Roep de [`show slave status`](https://mariadb.com/kb/en/library/show-slave-status/) opdracht op de replica server om de replicatie status weer te geven.

   ```sql
   show slave status;
   ```

   Als `Slave_IO_Running` `Slave_SQL_Running` de status en `yes` de waarde van is, werkt de `Seconds_Behind_Master` `0` replicatie. `Seconds_Behind_Master` Hiermee wordt aangegeven hoe laat de replica. Als dat niet het geval `0` is, verwerkt de replica de updates.

4. Werk de bijbehorende server variabelen bij om de replicatie van gegevens te beveiligen (alleen vereist voor replicatie zonder GTID).

    Vanwege een systeem eigen replicatie beperking in MariaDB moet u voor-  [`sync_master_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_master_info) en- [`sync_relay_log_info`](https://mariadb.com/kb/en/library/replication-and-binary-log-system-variables/#sync_relay_log_info) variabelen instellen voor replicatie zonder het GTID-scenario.

    Controleer de gegevens van de replica server `sync_master_info` en de `sync_relay_log_info` variabelen om er zeker van te zijn dat de replicatie stabiel is en stel de variabelen in op `1` .

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Gebruik de volgende opgeslagen procedure om de replicatie tussen de bron-en replica server te stoppen:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-the-replication-relationship"></a>De replicatie relatie verwijderen

Als u de relatie tussen de bron-en replica server wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-the-replication-error"></a>De replicatie fout overs Laan

Als u een replicatie fout wilt overs Laan en replicatie wilt toestaan, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen

Meer informatie over [replicatie van inkomende gegevens](concepts-data-in-replication.md) voor Azure database for MariaDB.
