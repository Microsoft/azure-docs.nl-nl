---
title: Replicatie van gegevens configureren-Azure Database for MySQL
description: In dit artikel wordt beschreven hoe u Replicatie van inkomende gegevens instelt voor Azure Database for MySQL.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 01/13/2021
ms.openlocfilehash: d5a013fc4e4ef931579da4fa13f400d5f4fcff0d
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102030746"
---
# <a name="how-to-configure-azure-database-for-mysql-data-in-replication"></a>Azure Database for MySQL Replicatie van inkomende gegevens configureren

In dit artikel wordt beschreven hoe u [replicatie van inkomende gegevens](concepts-data-in-replication.md) instelt in azure database for MySQL door de bron-en replica servers te configureren. In dit artikel wordt ervan uitgegaan dat u een eerdere ervaring hebt met MySQL-servers en-data bases.

> [!NOTE]
> Dit artikel bevat verwijzingen naar de term _Slave_, een term die door micro soft niet meer wordt gebruikt. Zodra de term uit de software wordt verwijderd, verwijderen we deze uit dit artikel.
>

Voor het maken van een replica in de Azure Database for MySQL-service, [replicatie van inkomende gegevens](concepts-data-in-replication.md)  synchroniseert gegevens van een bron-mysql-server on-premises, in virtuele machines (vm's) of in Cloud database services. Replicatie van binnenkomende gegevens is gebaseerd op het binaire logbestand (binlog) met replicatie op basis van positie eigen aan MySQL. Meer informatie over binlog-replicatie vindt u in het [overzicht van MySQL binlog-replicatie](https://dev.mysql.com/doc/refman/5.7/en/binlog-replication-configuration-overview.html).

Bekijk de [beperkingen en vereisten](concepts-data-in-replication.md#limitations-and-considerations) van gegevens replicatie voordat u de stappen in dit artikel uitvoert.

## <a name="create-a-mysql-server-to-be-used-as-replica"></a>Een MySQL-server maken die als replica moet worden gebruikt

1. Een nieuwe Azure Database for MySQL-server maken

   Een nieuwe MySQL-server maken (bijvoorbeeld "replica.mysql.database.azure.com"). Raadpleeg [een Azure database for mysql-server maken met behulp van de Azure portal voor het](quickstart-create-mysql-server-database-using-azure-portal.md) maken van de server. Deze server is de replica-server in Replicatie van inkomende gegevens.

   > [!IMPORTANT]
   > De Azure Database for MySQL-server moet worden gemaakt in de Algemeen of de prijs categorie geoptimaliseerd voor geheugen.
   >

2. Dezelfde gebruikers accounts en bijbehorende bevoegdheden maken

   Gebruikers accounts worden niet gerepliceerd van de bron server naar de replica server. Als u van plan bent gebruikers toegang te geven tot de replica-server, moet u alle accounts en de bijbehorende bevoegdheden hand matig maken op deze nieuw gemaakte Azure Database for MySQL server.

3. Voeg het IP-adres van de bron server toe aan de firewall regels van de replica.

   Firewallregels bijwerken met de [Azure-portal](howto-manage-firewall-using-portal.md) of [Azure CLI](howto-manage-firewall-using-cli.md).

## <a name="configure-the-source-server"></a>De bron server configureren

De volgende stappen maken en configureren van de MySQL-server die on-premises wordt gehost, op een virtuele machine of database service die wordt gehost door andere cloud providers voor Replicatie van inkomende gegevens. Deze server is de bron in replicatie van gegevens.

1. Controleer de [vereisten van de bron server](concepts-data-in-replication.md#requirements) voordat u doorgaat.

2. Zorg ervoor dat de bron server zowel binnenkomend als uitgaand verkeer op poort 3306 toestaat en dat het een **openbaar IP-adres** heeft, de DNS openbaar toegankelijk is of een Fully QUALIFIED domain name (FQDN) heeft.

   Test de verbinding met de bron server door te proberen verbinding te maken vanaf een hulp programma zoals de MySQL-opdracht regel die wordt gehost op een andere computer of op basis van het [Azure Cloud shell](../cloud-shell/overview.md) dat beschikbaar is in de Azure Portal.

   Als uw organisatie een strikt beveiligings beleid heeft en niet alle IP-adressen op de bron server toestaat communicatie van Azure naar uw bron server mogelijk te maken, kunt u de onderstaande opdracht gebruiken om het IP-adres van uw MySQL-server te bepalen.

   1. Meld u aan bij uw Azure Database for MySQL met een hulp programma zoals de MySQL-opdracht regel.

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
   4. Voer de volgende opdracht uit in het hulp programma Ping om het IP-adres op te halen.

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
  
3. Binaire logboek registratie inschakelen

   Controleer of binaire logboek registratie is ingeschakeld op de bron door de volgende opdracht uit te voeren: 

   ```sql
   SHOW VARIABLES LIKE 'log_bin';
   ```

   Als de variabele [`log_bin`](https://dev.mysql.com/doc/refman/8.0/en/replication-options-binary-log.html#sysvar_log_bin) wordt geretourneerd met de waarde ' on ', wordt de binaire logboek registratie ingeschakeld op de server.
   
    Als `log_bin` wordt geretourneerd met de waarde uit en op de bron server on-premises of op virtuele machines waarop u toegang hebt tot het configuratie bestand (My. cnf), kunt u de onderstaande stappen volgen:
   1. Zoek uw MySQL-configuratie bestand (My. cnf) op de bron server. Bijvoorbeeld:/etc/my.cnf
   2. Open het configuratie bestand om het te bewerken en de sectie **mysqld** in het bestand te zoeken.
   3.  Voeg in de sectie mysqld de volgende regel toe:
   
       ```bash
       log-bin=mysql-bin.log
       ```
     
   4. Start de MySQL-bron server opnieuw op om de wijzigingen van kracht te laten worden.
   5. Wanneer de server opnieuw is opgestart, controleert u of binaire logboek registratie is ingeschakeld door dezelfde query uit te voeren als voor:
   
      ```sql
      SHOW VARIABLES LIKE 'log_bin';
      ```
   
4. Bron-serverinstellingen

   Replicatie van inkomende gegevens moet para meter `lower_case_table_names` consistent zijn tussen de bron-en replica servers. Deze para meter is standaard 1 in Azure Database for MySQL.

   ```sql
   SET GLOBAL lower_case_table_names = 1;
   ```

5. Een nieuwe replicatie functie maken en machtigingen instellen

   Maak een gebruikers account op de bron server die is geconfigureerd met replicatie bevoegdheden. Dit kan worden gedaan via SQL-opdrachten of een hulp programma zoals MySQL Workbench. Overweeg of u van plan bent te repliceren met SSL, omdat dit moet worden opgegeven bij het maken van de gebruiker. Raadpleeg de MySQL-documentatie voor meer informatie over het [toevoegen van gebruikers accounts](https://dev.mysql.com/doc/refman/5.7/en/user-names.html) op de bron server.

   In de volgende opdrachten heeft de nieuwe replicatie functie die u hebt gemaakt toegang tot de bron vanaf een wille keurige computer, niet alleen de computer die als host fungeert voor de bron zelf. Dit doet u door ' syncuser@ '% ' op te geven in de opdracht gebruiker maken. Raadpleeg de MySQL-documentatie voor meer informatie over het [opgeven van account namen](https://dev.mysql.com/doc/refman/5.7/en/account-names.html).

   **SQL-opdracht**

   *Replicatie met SSL*

   Als u SSL wilt vereisen voor alle gebruikers verbindingen, gebruikt u de volgende opdracht om een gebruiker te maken:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%' REQUIRE SSL;
   ```

   *Replicatie zonder SSL*

   Als SSL niet is vereist voor alle verbindingen, gebruikt u de volgende opdracht om een gebruiker te maken:

   ```sql
   CREATE USER 'syncuser'@'%' IDENTIFIED BY 'yourpassword';
   GRANT REPLICATION SLAVE ON *.* TO ' syncuser'@'%';
   ```

   **MySQL Workbench**

   Als u de replicatie functie in MySQL Workbench wilt maken, opent u het deel venster **gebruikers en bevoegdheden** in het **beheer** paneel en selecteert u vervolgens **account toevoegen**.

   :::image type="content" source="./media/howto-data-in-replication/users_privileges.png" alt-text="Gebruikers en bevoegdheden":::

   Typ de gebruikers naam in het veld **aanmeldings naam** .

   :::image type="content" source="./media/howto-data-in-replication/syncuser.png" alt-text="Gebruiker synchroniseren":::

   Selecteer het paneel **beheerders rollen** en selecteer vervolgens **replicatie-slave** in de lijst met **globale bevoegdheden**. Selecteer vervolgens **Toep assen** om de replicatie functie te maken.

   :::image type="content" source="./media/howto-data-in-replication/replicationslave.png" alt-text="Replicatie-slave":::

6. Stel de bron server in op de modus alleen-lezen

   Voordat u begint met het dumpen van de data base, moet de server in de modus alleen-lezen worden geplaatst. In de modus alleen-lezen kan de bron geen schrijf transacties verwerken. Beoordeel de impact op uw bedrijf en plan het alleen-lezen venster in een rustige tijd, indien nodig.

   ```sql
   FLUSH TABLES WITH READ LOCK;
   SET GLOBAL read_only = ON;
   ```

7. Naam en offset van binair logboek bestand ophalen

   Voer de [`show master status`](https://dev.mysql.com/doc/refman/5.7/en/show-master-status.html) opdracht uit om de huidige binaire logboek bestandsnaam en-offset te bepalen.

   ```sql
    show master status;
   ```

   De resultaten moeten er ongeveer als volgt uitzien. Noteer de naam van het binaire bestand, zoals het wordt gebruikt in latere stappen.

   :::image type="content" source="./media/howto-data-in-replication/masterstatus.png" alt-text="Resultaten van de hoofd status":::

## <a name="dump-and-restore-source-server"></a>Bron server dumpen en herstellen

1. Bepaal welke data bases en tabellen u wilt repliceren naar Azure Database for MySQL en voer de dump uit vanaf de bron server.

    U kunt mysqldump gebruiken om data bases van uw Master te dumpen. Raadpleeg [Dump & herstellen](concepts-migrate-dump-restore.md)voor meer informatie. Het is niet nodig om MySQL-bibliotheek en test bibliotheek te dumpen.

2. Stel de bron server in op de modus lezen/schrijven.

   Nadat de data base is gedumpt, wijzigt u de bron MySQL-server weer in de modus lezen/schrijven.

   ```sql
   SET GLOBAL read_only = OFF;
   UNLOCK TABLES;
   ```

3. Herstel het dump bestand naar een nieuwe server.

   Herstel het dump bestand naar de server die in de Azure Database for MySQL-service is gemaakt. Raadpleeg [dump & herstellen](concepts-migrate-dump-restore.md) voor het herstellen van een dump bestand naar een MySQL-server. Als het dump bestand groot is, uploadt u het naar een virtuele machine in azure binnen dezelfde regio als de replica server. Herstel het op de Azure Database for MySQL-server vanaf de virtuele machine.

## <a name="link-source-and-replica-servers-to-start-data-in-replication"></a>Bron-en replica servers koppelen om Replicatie van inkomende gegevens te starten

1. Stel de bron server in.

   Alle Replicatie van inkomende gegevens-functies worden uitgevoerd door opgeslagen procedures. U kunt alle procedures vinden op [replicatie van inkomende gegevens opgeslagen procedures](./reference-stored-procedures.md). De opgeslagen procedures kunnen worden uitgevoerd in de MySQL-shell of MySQL Workbench.

   Als u twee servers wilt koppelen en replicatie wilt starten, meldt u zich aan bij de doel replica server in de Azure DB voor MySQL-service en stelt u het externe exemplaar in als de bron server. Dit wordt gedaan met behulp van de `mysql.az_replication_change_master` opgeslagen procedure op de Azure DB voor mysql-server.

   ```sql
   CALL mysql.az_replication_change_master('<master_host>', '<master_user>', '<master_password>', 3306, '<master_log_file>', <master_log_pos>, '<master_ssl_ca>');
   ```

   - master_host: de hostnaam van de bron server
   - master_user: gebruikers naam voor de bron server
   - master_password: wacht woord voor de bron server
   - master_log_file: de naam van het binaire logboek bestand is niet actief `show master status`
   - master_log_pos: de positie van het binaire logboek van wordt uitgevoerd `show master status`
   - master_ssl_ca: de context van het CA-certificaat. Als SSL niet wordt gebruikt, geeft u een lege teken reeks door.

     Het is raadzaam deze para meter door te geven als een variabele. Zie de volgende voor beelden voor meer informatie.

   > [!NOTE]
   > Als de bron server wordt gehost in een Azure-VM, stelt u toegang tot Azure-Services toestaan in op aan om de bron-en replica servers toe te staan om met elkaar te communiceren. Deze instelling kan worden gewijzigd via de **beveiligings** opties voor de verbinding. Zie [firewall regels beheren via de portal](howto-manage-firewall-using-portal.md) voor meer informatie.

   **Voorbeelden**

   *Replicatie met SSL*

   De variabele `@cert` wordt gemaakt door de volgende mysql-opdrachten uit te voeren:

      ```sql
      SET @cert = '-----BEGIN CERTIFICATE-----
      PLACE YOUR PUBLIC KEY CERTIFICATE'`S CONTEXT HERE
      -----END CERTIFICATE-----'
      ```

   Replicatie met SSL wordt ingesteld tussen een bron server die wordt gehost in het domein ' companya.com ' en een replica server die wordt gehost in Azure Database for MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, @cert);
      ```

   *Replicatie zonder SSL*

   Replicatie zonder SSL wordt ingesteld tussen een bron server die wordt gehost in het domein ' companya.com ' en een replica server die wordt gehost in Azure Database for MySQL. Deze opgeslagen procedure wordt uitgevoerd op de replica.

      ```sql
      CALL mysql.az_replication_change_master('master.companya.com', 'syncuser', 'P@ssword!', 3306, 'mysql-bin.000002', 120, '');
      ```

2. Filteren.

   Als u het repliceren van sommige tabellen vanuit uw model wilt overs Laan, moet u de `replicate_wild_ignore_table` Server parameter op de replica server bijwerken. U kunt meer dan één tabel patroon opgeven met behulp van een lijst met door komma's gescheiden waarden.

   Raadpleeg de [MySQL-documentatie](https://dev.mysql.com/doc/refman/8.0/en/replication-options-replica.html#option_mysqld_replicate-wild-ignore-table) voor meer informatie over deze para meter.

   U kunt de [Azure Portal](howto-server-parameters.md) of [Azure cli](howto-configure-server-parameters-using-cli.md)gebruiken om de para meter bij te werken.

3. Replicatie starten.

   Roep de `mysql.az_replication_start` opgeslagen procedure aan om de replicatie te starten.

   ```sql
   CALL mysql.az_replication_start;
   ```

4. Controleer de replicatie status.

   Roep de [`show slave status`](https://dev.mysql.com/doc/refman/5.7/en/show-slave-status.html) opdracht op de replica server om de replicatie status weer te geven.

   ```sql
   show slave status;
   ```

   Als de status van `Slave_IO_Running` en `Slave_SQL_Running` Ja is en de waarde van `Seconds_Behind_Master` is 0, werkt replicatie goed. `Seconds_Behind_Master` Hiermee wordt aangegeven hoe laat de replica. Als de waarde niet 0 is, betekent dit dat de replica updates verwerkt.

## <a name="other-stored-procedures"></a>Andere opgeslagen procedures

### <a name="stop-replication"></a>Replicatie stoppen

Gebruik de volgende opgeslagen procedure om de replicatie tussen de bron-en replica server te stoppen:

```sql
CALL mysql.az_replication_stop;
```

### <a name="remove-replication-relationship"></a>Replicatie relatie verwijderen

Als u de relatie tussen de bron-en replica server wilt verwijderen, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_remove_master;
```

### <a name="skip-replication-error"></a>Replicatie fout overs Laan

Als u een replicatie fout wilt overs Laan en de replicatie wilt laten door gaan, gebruikt u de volgende opgeslagen procedure:

```sql
CALL mysql.az_replication_skip_counter;
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [replicatie van inkomende gegevens](concepts-data-in-replication.md) voor Azure database for MySQL.
