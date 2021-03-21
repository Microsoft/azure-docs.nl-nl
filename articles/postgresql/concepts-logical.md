---
title: Logische decodering-Azure Database for PostgreSQL-één server
description: Hierin worden logische decoderings-en wal2json voor change data capture in Azure Database for PostgreSQL-één-server beschreven
author: sr-msft
ms.author: srranga
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/09/2020
ms.openlocfilehash: 0ea58050c5dc952392df56b4fb556a0998eef165
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96938899"
---
# <a name="logical-decoding"></a>Logische decodering

Met [logische decodering in postgresql](https://www.postgresql.org/docs/current/logicaldecoding.html) kunt u gegevens wijzigingen streamen naar externe gebruikers. Logische decodering wordt populair gebruikt voor het streamen van gebeurtenissen en change data capture scenario's.

Logische decodering maakt gebruik van een uitvoer-invoeg toepassing om het Write-Ahead logboek (WAL) van post gres te converteren naar een lees bare indeling. Azure Database for PostgreSQL biedt de uitvoer-invoeg toepassingen [wal2json](https://github.com/eulerto/wal2json), [test_decoding](https://www.postgresql.org/docs/current/test-decoding.html) en pgoutput. pgoutput wordt beschikbaar gesteld door PostgreSQL van PostgreSQL versie 10 en hoger.

[Ga naar onze blog](https://techcommunity.microsoft.com/t5/azure-database-for-postgresql/change-data-capture-in-postgres-how-to-use-logical-decoding-and/ba-p/1396421)voor een overzicht van hoe post gres logische code ring werkt. 

> [!NOTE]
> Logische replicatie met PostgreSQL-publicatie/-abonnement wordt niet ondersteund met Azure Database for PostgreSQL-één server.


## <a name="set-up-your-server"></a>Uw server instellen 
Logische decodering en het [lezen van replica's](concepts-read-replicas.md) zijn beide afhankelijk van het post gres-logboek (Wal) voor het schrijven van gegevens. Deze twee functies hebben verschillende niveaus van logboek registratie nodig van post gres. Voor logische decodering is een hoger niveau van logboek registratie vereist dan bij het lezen van replica's.

Als u het juiste niveau van logboek registratie wilt configureren, gebruikt u de Azure Replication support-para meter. Ondersteuning voor Azure-replicatie heeft drie instellings opties:

* **Uit** : Hiermee wordt de minste informatie in de wal geplaatst. Deze instelling is niet beschikbaar op de meeste Azure Database for PostgreSQL-servers.  
* **Replica** : uitgebreidere dan **uit**. Dit is het minimale registratie niveau dat nodig is voor het werken met [replica's](concepts-read-replicas.md) . Deze instelling is de standaard waarde op de meeste servers.
* **Logisch** -uitgebreidere dan de **replica**. Dit is het minimale registratie niveau voor het werken met logische code ring. Het lezen van replica's werkt ook bij deze instelling.


### <a name="using-azure-cli"></a>Azure CLI gebruiken

1. Stel azure.replication_support in op `logical` .
   ```azurecli-interactive
   az postgres server configuration set --resource-group mygroup --server-name myserver --name azure.replication_support --value logical
   ``` 

2. Start de server opnieuw op om de wijziging toe te passen.
   ```azurecli-interactive
   az postgres server restart --resource-group mygroup --name myserver
   ```
3. Als u post gres 9,5 of 9,6 uitvoert en open bare netwerk toegang gebruikt, voegt u de firewall regel toe om het open bare IP-adres van de client toe te voegen van waaruit u de logische replicatie wilt uitvoeren. De naam van de firewall regel moet **_replrule** bevatten. Bijvoorbeeld *test_replrule*. Voer de opdracht [AZ post gres Server firewall-Rule Create](/cli/azure/postgres/server/firewall-rule) uit om een nieuwe firewall regel te maken op de server. 

### <a name="using-azure-portal"></a>Azure Portal gebruiken

1. Stel ondersteuning voor Azure-replicatie in op **logisch**. Selecteer **Opslaan**.

   :::image type="content" source="./media/concepts-logical/replication-support.png" alt-text="Azure Database for PostgreSQL-replicatie-ondersteuning van Azure-replicatie":::

2. Start de server opnieuw op om de wijziging toe te passen door **Ja** te selecteren.

   :::image type="content" source="./media/concepts-logical/confirm-restart.png" alt-text="Azure Database for PostgreSQL-replicatie: bevestig opnieuw opstarten":::

3. Als u post gres 9,5 of 9,6 uitvoert en open bare netwerk toegang gebruikt, voegt u de firewall regel toe om het open bare IP-adres van de client toe te voegen van waaruit u de logische replicatie wilt uitvoeren. De naam van de firewall regel moet **_replrule** bevatten. Bijvoorbeeld *test_replrule*. Klik vervolgens op **Opslaan**.

   :::image type="content" source="./media/concepts-logical/client-replrule-firewall.png" alt-text="Azure Database for PostgreSQL-Replication-firewall regel toevoegen":::

## <a name="start-logical-decoding"></a>Logische decodering starten

Logische decodering kan worden gebruikt via het streaming-protocol of de SQL-interface. Beide methoden gebruiken [replicatie sleuven](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html#LOGICALDECODING-REPLICATION-SLOTS). Een sleuf vertegenwoordigt een stroom van wijzigingen ten opzichte van één data base.

Voor het gebruik van een replicatie sleuf zijn de replicatie bevoegdheden van de post gres vereist. Op dit moment is de replicatie bevoegdheid alleen beschikbaar voor de gebruiker van de server beheerder. 

### <a name="streaming-protocol"></a>Streaming-Protocol
Het gebruik van wijzigingen met het streaming-protocol is vaak de voor keur. U kunt uw eigen consument/connector maken of een hulp programma gebruiken, zoals [Debezium](https://debezium.io/). 

Ga naar de wal2json-documentatie voor [een voor beeld van het streaming-protocol met pg_recvlogical](https://github.com/eulerto/wal2json#pg_recvlogical).


### <a name="sql-interface"></a>SQL-interface
In het onderstaande voor beeld gebruiken we de SQL-interface met de wal2json-invoeg toepassing.
 
1. Maak een sleuf.
   ```SQL
   SELECT * FROM pg_create_logical_replication_slot('test_slot', 'wal2json');
   ```
 
2. SQL-opdrachten verlenen. Bijvoorbeeld:
   ```SQL
   CREATE TABLE a_table (
      id varchar(40) NOT NULL,
      item varchar(40),
      PRIMARY KEY (id)
   );
   
   INSERT INTO a_table (id, item) VALUES ('id1', 'item1');
   DELETE FROM a_table WHERE id='id1';
   ```

3. De wijzigingen worden verbruikt.
   ```SQL
   SELECT data FROM pg_logical_slot_get_changes('test_slot', NULL, NULL, 'pretty-print', '1');
   ```

   De uitvoer ziet er als volgt uit:
   ```
   {
         "change": [
         ]
   }
   {
         "change": [
                  {
                           "kind": "insert",
                           "schema": "public",
                           "table": "a_table",
                           "columnnames": ["id", "item"],
                           "columntypes": ["character varying(40)", "character varying(40)"],
                           "columnvalues": ["id1", "item1"]
                  }
         ]
   }
   {
         "change": [
                  {
                           "kind": "delete",
                           "schema": "public",
                           "table": "a_table",
                           "oldkeys": {
                                 "keynames": ["id"],
                                 "keytypes": ["character varying(40)"],
                                 "keyvalues": ["id1"]
                           }
                  }
         ]
   }
   ```

4. Haal de sleuf weg nadat u deze hebt gebruikt.
   ```SQL
   SELECT pg_drop_replication_slot('test_slot'); 
   ```


## <a name="monitoring-slots"></a>Bewakings sleuven

U moet logische decodering bewaken. Alle ongebruikte replicatie sleuven moeten worden verwijderd. Sleuven wachten op post gres WAL-logboeken en relevante systeem catalogussen totdat wijzigingen door een gebruiker zijn gelezen. Als uw consument uitvalt of niet goed is geconfigureerd, wordt uw opslag door de niet-gebruikte logboeken gepoold en gevuld. Ook niet-verbruikte logboeken verhogen het risico van trans actie-ID wraparound. Beide situaties kunnen ertoe leiden dat de server niet meer beschikbaar is. Daarom is het essentieel dat logische replicatie sleuven continu worden verbruikt. Als een logische replicatie sleuf niet meer wordt gebruikt, moet u deze onmiddellijk neerzetten.

In de kolom actief in de pg_replication_slots weer gave wordt aangegeven of er een consument is verbonden met een sleuf.
```SQL
SELECT * FROM pg_replication_slots;
```

[Stel waarschuwingen](howto-alert-on-metric.md) in voor de *gebruikte opslag ruimte* en de *maximale vertraging* voor de metrische gegevens van replica's om u te waarschuwen wanneer de waarden de vorige normale drempel waarde verhogen. 

> [!IMPORTANT]
> U moet ongebruikte replicatie sleuven verwijderen. Als u dit niet doet, kan de server niet meer beschikbaar zijn.

## <a name="how-to-drop-a-slot"></a>Een sleuf verwijderen
Als u een replicatie sleuf niet actief verbruikt, moet u deze verwijderen.

Een replicatie sleuf verwijderen met de naam `test_slot` SQL:
```SQL
SELECT pg_drop_replication_slot('test_slot');
```

> [!IMPORTANT]
> Als u geen logische decodering meer gebruikt, wijzigt u azure.replication_support terug naar `replica` of `off` . De WAL-gegevens die `logical` worden bewaard door zijn uitgebreider en moeten worden uitgeschakeld wanneer de logische decodering niet wordt gebruikt. 

 
## <a name="next-steps"></a>Volgende stappen

* Ga naar de post gres-documentatie voor [meer informatie over logische decodering](https://www.postgresql.org/docs/current/logicaldecoding-explanation.html).
* Neem contact op met [ons team](mailto:AskAzureDBforPostgreSQL@service.microsoft.com) als u vragen hebt over logische decodering.
* Meer informatie over het [lezen van replica's](concepts-read-replicas.md).

