---
title: Gegevens migreren van Oracle naar Azure Cosmos DB Cassandra-API met behulp van Blitzz
description: Meer informatie over het migreren van gegevens uit Oracle Data Base naar Azure Cosmos DB Cassandra-API met behulp van Blitzz.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 08/21/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 15bcd0c54fc5f6614f4d1925759704309048acae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "93336437"
---
# <a name="migrate-data-from-oracle-to-azure-cosmos-db-cassandra-api-account-using-blitzz"></a>Gegevens migreren van Oracle naar Azure Cosmos DB Cassandra-API-account met behulp van Blitzz
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Cassandra-API in Azure Cosmos DB is een uitstekende keuze voor zakelijke workloads die op Oracle worden uitgevoerd om verschillende redenen, zoals:

* **Betere schaal baarheid en beschik baarheid:** Het elimineert afzonderlijke punten aan storingen, betere schaal baarheid en beschik baarheid voor uw toepassingen.

* **Aanzienlijke kosten besparingen:** U kunt kosten besparen met Azure Cosmos DB, inclusief de kosten van VM'S, band breedte en eventuele toepasselijke Oracle-licenties. Bovendien hoeft u de data centers, servers, SSD-opslag, netwerken en elektriciteits kosten niet te beheren.

* **Geen overhead voor beheer en controle:** Als volledig beheerde Cloud service verwijdert Azure Cosmos DB de overhead van het beheren en bewaken van een talloze instellingen.

Er zijn verschillende manieren om de data base-workloads van het ene platform naar het andere te migreren. [Blitzz](https://www.blitzz.io) is een hulp programma dat een veilige en betrouw bare manier biedt voor het uitvoeren van een back-uptijd van nul van een groot aantal data bases tot Azure Cosmos db. In dit artikel worden de stappen beschreven die nodig zijn om gegevens van Oracle Data Base te migreren naar Azure Cosmos DB Cassandra-API met behulp van Blitzz.

## <a name="benefits-using-blitzz-for-migration"></a>Voor delen van het gebruik van Blitzz voor migratie

De migratie oplossing van Blitzz volgt een stapsgewijze benadering van het migreren van complexe operationele workloads. Hier volgen enkele van de belangrijkste aspecten van het migratie plan voor Blitzz met een nul-uitval tijd:

* Het biedt automatische migratie van bedrijfs logica (tabellen, indexen, weer gaven) van Oracle Data Base naar Azure Cosmos DB. U hoeft geen schema's hand matig te maken.

* Blitzz biedt de replicatie van grote volumes en parallelle data bases. De bron-en doel platformen kunnen tijdens de migratie in-sync zijn met behulp van een techniek genaamd Change-Data-Capture (CDC). Door CDC te gebruiken, haalt Blitzz continu een stroom wijzigingen van de bron database (Oracle) op en past deze toe op de doel database (Azure Cosmos DB).

* Het is fout tolerant en garandeert precies één keer wanneer er gegevens worden geleverd, zelfs tijdens een hardware-of software fout in het systeem.

* Het beveiligt de gegevens tijdens de overdracht met behulp van een aantal beveiligings methoden zoals TLS/SSL, versleuteling.

* Het biedt services voor het converteren van complexe bedrijfs logica die is geschreven in PL/SQL naar gelijkwaardige bedrijfs logica in Azure Cosmos DB.

## <a name="steps-to-migrate-data"></a>Stappen voor het migreren van gegevens

In deze sectie worden de stappen beschreven die nodig zijn voor het instellen van Blitzz en het migreren van gegevens van Oracle Data Base naar Azure Cosmos DB.

1. Voeg een beveiligings certificaat toe vanaf de computer waarop u de Blitzz-Replicant wilt installeren. Dit certificaat is vereist voor de Blitzz Replicant om een TLS-verbinding met het opgegeven Azure Cosmos DB-account tot stand te brengen. U kunt het certificaat toevoegen aan de hand van de volgende stappen:

   ```bash
   wget https://cacert.omniroot.com/bc2025.crt
   mv bc2025.crt bc2025.cer
   keytool -keystore $JAVA_HOME/lib/security/cacerts -importcert -alias bc2025ca -file bc2025.cer
   ```

1. OE kan de Blitzz-installatie en de binaire bestanden ophalen door een demo op de [Blitzz-website](https://www.blitzz.io)aan te vragen. U kunt ook een [e-mail](mailto:success@blitzz.io) verzenden naar het team.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/blitzz-replicant-download.png" alt-text="Blitzz Replicant-hulp programma downloaden":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/replicant-files.png" alt-text="Blitzz Replicant-bestanden":::

1. Stel in de CLI-Terminal de configuratie van de bron database in. Open het configuratie bestand met behulp van de **`vi conf/conn/oracle.yml`** opdracht en voeg een door komma's gescheiden lijst met IP-adressen van de Oracle-knoop punten, het poort nummer, de gebruikers naam, het wacht woord en andere vereiste gegevens toe. De volgende code toont een voor beeld van een configuratie bestand:

   ```bash
   type: ORACLE

   host: localhost
   port: 53546

   service-name: IO

   username: '<Username of your Oracle database>'
   password: '<Password of your Oracle database>'

   conn-cnt: 30
   use-ssl: false
   ```

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/open-connection-editor-oracle.png" alt-text="Oracle-verbindings editor openen":::

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-connection-configuration.png" alt-text="Configuratie van Oracle-verbinding":::

   Sla het bestand op en sluit het als u de configuratie gegevens hebt ingevuld.

1. Desgewenst kunt u het filter bestand van de bron database instellen. Het filter bestand geeft aan welke schema's of tabellen moeten worden gemigreerd. Open het configuratie bestand met behulp van **`vi filter/oracle_filter.yml`** de opdracht en voer de volgende configuratie gegevens in:

   ```bash

   allow:
   -    schema: “io_blitzz”
   Types: [TABLE]
   ```
 
   Sla het bestand op en sluit het als u de details van het database filter hebt ingevuld.

1. Vervolgens stelt u de configuratie van de doel database in. Voordat u de configuratie definieert, [maakt u een Azure Cosmos DB Cassandra-API-account](create-cassandra-dotnet.md#create-a-database-account). [Kies de juiste partitie sleutel](partitioning-overview.md#choose-partitionkey) van uw gegevens en maak vervolgens een spatie en een tabel om de gemigreerde gegevens op te slaan.

1. Voordat u de gegevens migreert, verhoogt u de doorvoer capaciteit van de container naar de hoeveelheid die de toepassing nodig heeft om snel te migreren. U kunt de door Voer bijvoorbeeld verhogen naar 100000 RUs. Wanneer u de door Voer hebt geschaald voordat u de migratie start, helpt u om uw gegevens in minder tijd te migreren. 

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/scale-throughput.png" alt-text="Azure Cosmos-container schalen in":::

   U moet de door Voer verminderen nadat de migratie is voltooid. Op basis van de hoeveelheid opgeslagen gegevens en RUs die is vereist voor elke bewerking, kunt u een schatting maken van de door Voer die is vereist na de gegevens migratie. Zie voor meer informatie over het maken van een schatting van het RUs-vereiste [door Voer inrichten voor containers en data bases](set-throughput.md) en [een schatting van ru/s met behulp van de artikelen van de Azure Cosmos DB capacity planner](estimate-ru-with-capacity-planner.md) .

1. Haal het **contact punt, de poort, de gebruikers naam** en het **primaire wacht woord** van uw Azure Cosmos-account op in het deel venster **verbindings reeks** . U gebruikt deze waarden in het configuratie bestand.

1. Stel in de CLI-Terminal de configuratie van de doel database in. Open het configuratie bestand met behulp **`vi conf/conn/cosmosdb.yml`** van de opdracht en voeg een door komma's gescheiden lijst toe met de URI, het poort nummer, de gebruikers naam, het wacht woord en andere vereiste para meters. Hier volgt een voor beeld van de inhoud van het configuratie bestand:

   ```bash
   type: COSMOSDB

   host: `<Azure Cosmos account’s Contact point>`
   port: 10350

   username: 'blitzzdemo'
   password: `<Your Azure Cosmos account’s primary password>'

   max-connections: 30
   use-ssl: false
   ```

1. Migreer vervolgens de gegevens met behulp van Blitzz. U kunt de blizz-Replicant uitvoeren in de **volledige** of **momentopname** modus:

   * **Volledige modus** – in deze modus wordt de Replicant nog steeds uitgevoerd na de migratie en wordt er geluisterd naar wijzigingen op het Oracle-bron systeem. Als er wijzigingen worden gedetecteerd, worden deze in realtime gerepliceerd op het doel-Azure Cosmos-account.

   * **Momentopname modus** : in deze modus kunt u schema migratie en eenmalige gegevens replicatie uitvoeren. Realtime-replicatie wordt niet ondersteund met deze optie.


   Als u de bovenstaande twee modi gebruikt, kan de migratie worden uitgevoerd met een downtime van nul.

1. Als u gegevens wilt migreren, voert u de volgende opdracht uit vanaf de Blitzz Replicant CLI-terminal:

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing
   ```

   De Replicant-gebruikers interface toont de voortgang van de replicatie. Zodra de schema migratie en de momentopname bewerking zijn uitgevoerd, wordt in de voortgang 100% weer gegeven. Nadat de migratie is voltooid, kunt u de gegevens valideren in de Azure Cosmos-doel database.

   :::image type="content" source="./media/oracle-migrate-cosmos-db-blitzz/oracle-data-migration-output.png" alt-text="Uitvoer van Oracle-gegevens migratie":::

1. Omdat u de volledige modus voor migratie hebt gebruikt, kunt u bewerkingen uitvoeren zoals het invoegen, bijwerken of verwijderen van gegevens in de Oracle-bron database. U kunt later controleren of ze realtime worden gerepliceerd in de Azure Cosmos-doel database. Na de migratie moet u de door Voer die is geconfigureerd voor de Azure Cosmos-container verlagen.

1. U kunt de Replicant wille keurig punt stoppen en opnieuw starten met **--** de switch hervatten. De replicatie wordt hervat vanaf het punt waarop deze is gestopt zonder dat er wordt gereageerd op de consistentie van de gegevens. De volgende opdracht laat zien hoe u de schakel optie hervatten kunt gebruiken.

   ```bash
   ./bin/replicant full conf/conn/oracle.yaml conf/conn/cosmosdb.yaml --filter filter/oracle_filter.yaml --replace-existing --resume
   ```

Zie de [Blitzz Replicant-demo](https://www.youtube.com/watch?v=y5ZeRK5A-MI)voor meer informatie over het migreren van gegevens naar doel, realtime migratie.

## <a name="next-steps"></a>Volgende stappen

* [Doorvoer voor containers en databases inrichten](set-throughput.md) 
* [Best practices voor de partitie sleutel](partitioning-overview.md#choose-partitionkey)
* [Een raming van ru/s met de Azure Cosmos DB capaciteits planner](estimate-ru-with-capacity-planner.md) artikelen
