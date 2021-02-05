---
title: 'Zelfstudie: MongoDB online migreren naar de Azure Cosmos DB-API voor MongoDB'
titleSuffix: Azure Database Migration Service
description: Leer hoe u on-premises MongoDB online kunt migreren naar de Azure Cosmos DB-API voor MongoDB met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-nov-2020
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 359f268f69918ccfd9fe34a28c3f8d1c79988393
ms.sourcegitcommit: 1f1d29378424057338b246af1975643c2875e64d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99575607"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Zelfstudie: MongoDB online migreren naar de Azure Cosmos DB-API voor MongoDB met behulp van DMS

Met Azure Database Migration Service kunt u een onlinemigratie (met minimale downtime) van databases uitvoeren vanaf een on-premises of in de cloud aanwezig MongoDB -exemplaar naar de Azure Cosmos DB's API voor MongoDB.

In deze zelfstudie ziet u de stappen die bij het gebruik van Azure Database Migration Service horen om MongoDB-gegevens te migreren naar Azure Cosmos DB:
> [!div class="checklist"]
> 
> * Maak een exemplaar van de Azure Database Migration Service. 
> * Een migratieproject maken. 
> * De bron opgeven. 
> * Het doel opgeven. 
> * Toewijzen aan doeldatabases. 
> * De migratie uitvoeren. 
> * Houd de migratie in de gaten. 
> * Gegevens verifiëren in Azure Cosmos DB. 
> * Voltooi de migratie als u klaar bent. 

In deze zelfstudie migreert u met minimale downtime een gegevensset die in MongoDB wordt gehost in een virtuele Azure-machine naar de Azure Cosmos DB-API voor MongoDB met behulp van Azure Database Migration Service. Als u nog geen MongoDB-bron hebt ingesteld, raadpleegt u het artikel over het [installeren en configureren van MongoDB op een Windows-VM in Azure](../virtual-machines/windows/install-mongodb.md).

> [!NOTE]
> Als Azure Database Migration Service gebruikt om een onlinemigratie uit te voeren, is het vereist dat u een exemplaar maakt op basis van de prijscategorie Premium.

> [!IMPORTANT]
> Voor een optimale migratie-ervaring raadt Microsoft u aan een exemplaar van Azure Database Migration Service te maken in dezelfde Azure-regio als de doeldatabase. Het verplaatsen van gegevens naar regio's of geografieën kan het migratieproces vertragen.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

In dit artikel wordt een onlinemigratie beschreven van MongoDB naar Azure Cosmos DB's API voor MongoDB. Zie [MongoDB migreren naar Azure Cosmos DB's API voor offline MongoDB met behulp van DMS](tutorial-mongodb-cosmos-db.md) voor een offlinemigratie.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* [Voltooi de stappen voorafgaand aan de migratie](../cosmos-db/mongodb-pre-migration.md), zoals het schatten van de doorvoer of het kiezen van een partitiesleutel en het indexeringsbeleid.
* [Maak een Azure Cosmos DB-API voor een MongoDB-account](https://ms.portal.azure.com/#create/Microsoft.DocumentDB) en zorg ervoor dat [SSR (nieuwe poging voor serverzijde)](../cosmos-db/prevent-rate-limiting-errors.md) is ingeschakeld.
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van het Azure Resource Manager-implementatiemodel. Dit biedt site-naar-site-connectiviteit met uw on-premises bronservers met behulp van [ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md).

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eindpunt van de doeldatabase (bijvoorbeeld SQL-eindpunt, Cosmos DB-eindpunt, enzovoort)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat de NSG-regels (Network Security Group) van het virtuele netwerk de volgende communicatiepoorten niet blokkeren: 53, 443, 445, 9354 en 10000-20000. Zie het artikel [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie over verkeer filteren van verkeer via de netwerkbeveiligingsgroep voor virtuele netwerken.
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de bronserver van MongoDB. Standaard verloopt deze via TCP-poort 27017.
* Wanneer u een firewallapparaat gebruikt voor de brondatabase(s), moet u mogelijk firewallregels toevoegen om voor Azure Database Migration Service toegang tot de brondatabase(s) voor de migratie toe te staan.

## <a name="configure-azure-cosmos-db-server-side-retries-for-efficient-migration"></a>Nieuwe pogingen configureren aan de Azure Cosmos DB server voor een efficiënte migratie

Klanten die migreren van MongoDB naar Azure Cosmos DB profiteren van de mogelijkheden van resource beheer, die de mogelijkheid bieden om uw ingerichte RU/s van door Voer volledig te benutten. Azure Cosmos DB kan een opgegeven Data Migration service-aanvraag beperken tijdens de migratie als die aanvraag de door de container ingerichte RU/s overschrijdt. vervolgens moet de aanvraag opnieuw worden geprobeerd. Data Migration service kan nieuwe pogingen uitvoeren, maar de retour tijd die is betrokken bij de netwerk-hop tussen Data Migration service en Azure Cosmos DB beïnvloedt de totale reactie tijd van die aanvraag. Het verbeteren van de reactie tijd voor beperkte aanvragen kan de totale tijd verkorten die nodig is voor de migratie. Met de functie *opnieuw proberen* op de Server van Azure Cosmos db kan de service vertragings fout codes onderscheppen en opnieuw proberen met een veel lagere retour tijd, waardoor de reactie tijden van aanvragen aanzienlijk worden verbeterd.

De functie voor het opnieuw proberen van de server is te vinden op de Blade *functies* van de Azure Cosmos DB Portal

![Scherm opname van MongoDB Server-Side nieuwe poging.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-feature.png)

Als deze optie is *uitgeschakeld*, wordt u aangeraden deze in te scha kelen, zoals hieronder wordt weer gegeven

![Scherm opname van MongoDB Server-Side opnieuw proberen in te scha kelen.](media/tutorial-mongodb-to-cosmosdb-online/mongo-server-side-retry-enable.png)

## <a name="register-the-microsoftdatamigration-resource-provider"></a>Registreer de Microsoft.DataMigration-resourceprovider

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Portal-abonnementen weergeven](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Resourceproviders weergeven](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Resourceprovider registreren](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Azure Marketplace](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Azure Database Migration Service-exemplaar maken](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. Geef in het scherm **Migratieservice maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resourcegroep.

4. Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken.

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

   Het virtuele netwerk biedt Azure Database Migration Service toegang tot de broninstantie van MongoDB en het doelaccount van Azure Cosmos DB.

   Zie het artikel [Een virtueel netwerk maken met de Azure-portal](../virtual-network/quick-create-portal.md) voor meer informatie over het maken van een virtueel netwerk in de Azure-portal.

6. Selecteer een SKU in de prijscategorie Premium.

    > [!NOTE]
    > Onlinemigraties worden alleen ondersteund bij gebruik van deze categorie. Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Instellingen configureren van een Azure Database Migration Service-exemplaar](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat de service is gemaakt, zoek deze op in de Azure-portal, open hem en maak vervolgens een nieuw migratieproject.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

    ![Zoek alle exemplaren van Azure Database Migration Service](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens de instantie.

    U kunt de service-instantie van Azure Database Migration ook vinden via het zoekvenster in Azure Portal.

    ![Het zoekvenster gebruiken in de Azure-portal](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Selecteer + **Nieuw migratieproject**.

4. Geef in het scherm **Nieuw migratieproject** een naam op voor het project in het tekstvak **Type bronserver**, selecteer **MongoDB**, selecteer in het tekstvak **Type doelserver****CosmosDB (MongoDB-API)**, en selecteer bij **Type activiteit kiezen** de optie **Online gegevensmigratie [preview-versie]**.

    ![Database Migration Service-project maken](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Selecteer **Opslaan** en vervolgens **Create and run activity** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails** de verbindingsgegevens op voor de MongoDB-bronserver.

   > [!IMPORTANT]
   > Azure Database Migration Service biedt geen ondersteuning voor Azure Cosmos DB als bron.

    Er zijn drie modi om verbinding te maken met een bron:
   * **Standaardmodus**: deze accepteert een Fully Qualified Domain Name of een IP-adres, poortnummer en verbindingsreferenties.
   * **Modus verbindingsreeks**: deze accepteert een MongoDB-verbindingsreeks, zoals beschreven in het artikel [Connection String URI Format](https://docs.mongodb.com/manual/reference/connection-string/) (URI-indeling van verbindingsreeks).
   * **Gegevens uit Azure-opslag**: deze accepteert een SAS-URL van de blob-container. Selecteer **Blob contains BSON dump** als de blob-container BSON-dumps bevat die zijn geproduceerd door het [bsondump-hulpprogramma](https://docs.mongodb.com/manual/reference/program/bsondump/) van MongoDB en deselecteer het als de container JSON-bestanden bevat.

     Als u deze optie selecteert, controleer dan of de verbindingsreeks van het opslagaccount wordt weergegeven in de volgende indeling:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Vanwege het type dumpgegevens in Azure Storage moet u ook rekening houden met het volgende.

     * Voor BSON-dumps moeten de gegevens in de blob-container de bsondump-indeling hebben, zodat de gegevensbestanden worden geplaatst in mappen die worden genoemd naar de omvattende databases in de collection.bson-indeling. Metagegevensbestanden (indien aanwezig) moeten een naam krijgen op basis van de indeling *verzameling*.metadata.json.

     * Voor JSON-dumps moeten de bestanden in de blob-container worden geplaatst in mappen die zijn genoemd naar de omvattende databases. In elke databasemap moeten gegevensbestanden worden geplaatst in een submap met de naam 'data' en ze moeten een naam krijgen op basis van de indeling *verzameling*.json. Metagegevensbestanden (indien aanwezig) moeten worden geplaatst in een submap met de naam 'metadata' en ze moeten een naam krijgen op basis van dezelfde indeling *verzameling*.json. De metagegevensbestanden moeten de indeling hebben die wordt geproduceerd door het MongoDB-hulpprogramma bsondump.

    > [!IMPORTANT]
    > Het wordt afgeraden om een zelfondertekend certificaat te gebruiken op de Mongo-server. Als echter wel een zelfondertekend certificaat wordt gebruikt, moet u verbinding maken met de server in de **verbindingsreeksmodus** en ervoor zorgen dat de verbindingsreeks het volgende bevat: “”
    >
    >```
    >&sslVerifyCertificate=false
    >```

    U kunt het IP-adres gebruiken voor situaties waarin DNS-naamomzetting niet mogelijk is.

   ![Geef brondetails op](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Selecteer **Opslaan**.

   > [!NOTE]
   > Het adres van de bronserver moet het adres zijn van de primaire server als de bron een replicaset is. Het moet het adres van de router zijn als de bron een shard-MongoDB-cluster is. Voor een shard-MongoDB-cluster moet Azure Database Migration Service verbinding kunnen maken met de afzonderlijke shards in het cluster. Hiervoor moet mogelijk de firewall op meerdere machines worden geopend.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **Details migratiedoel** de verbindingsgegevens op voor het Azure Cosmos DB-doelaccount, dat bestaat uit het vooraf ingerichte Azure Cosmos DB's API voor MongoDB-account waarnaar u de MongoDB-database migreert.

    ![Doeldetails opgeven](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Selecteer **Opslaan**.

## <a name="map-to-target-databases"></a>Toewijzen aan doeldatabases

1. Wijs in het scherm **Toewijzen aan doeldatabases** de bron- en doeldatabase voor de migratie toe.

   Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

   Als de tekenreeks **Maken** verschijnt naast de naam van de database, is de doeldatabase niet gevonden met Azure Database Migration Service. De database wordt dan voor u gemaakt.

   Op dit punt tijdens de migratie geeft u een doorvoeraanvraageenheid op indien doorvoer van shares voor de database is gewenst. In Cosmos DB kunt u doorvoer inrichten op het niveau van de database of afzonderlijk voor elke verzameling. Doorvoer wordt gemeten in [Aanvraageenheden](../cosmos-db/request-units.md) (RU's). Meer informatie over de [prijzen van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Toewijzen aan doeldatabases](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Selecteer **Opslaan**.

3. Vouw in het scherm **Verzamelingsinstelling** de lijst met verzamelingen uit en bekijk welke verzamelingen worden gemigreerd.

   Azure Database Migration Service selecteert automatisch alle verzamelingen op de MongoDB-broninstantie die niet aanwezig zijn in het Azure Cosmos DB-doelaccount. Als u verzamelingen die al gegevens bevatten, opnieuw wilt migreren, moet u die verzamelingen expliciet selecteren in dit scherm.

   U kunt opgeven hoeveel RU's u voor de verzamelingen wilt gebruiken. In de meeste gevallen moet een waarde tussen de 500 (minimaal 1000 voor shard-verzamelingen) en 4000 voldoende zijn. Er worden slimme standaardwaarden voorgesteld op basis van de gezamenlijke grootte.

    > [!NOTE]
    > Voer de databasemigratie en de verzameling parallel uit, zo nodig met meerdere instanties van Azure Database Migration Service, om de uitvoering te versnellen.

   U kunt ook een shardsleutel opgeven om te profiteren van [partitionering in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) voor optimale schaalbaarheid. Lees de [best practices voor het selecteren van een shard-/partitiesleutel](../cosmos-db/partitioning-overview.md#choose-partitionkey). Als u geen partitiesleutel hebt, kunt u altijd gebruikmaken van **_id** als de shardsleutel voor een betere doorvoer.

   ![Verzamelingstabellen selecteren](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Selecteer **Opslaan**.

5. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

    ![Migratieoverzicht](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

* Selecteer **Migratie uitvoeren**.

   Het venster van de migratieactiviteit wordt weergegeven en de **status** van de activiteit wordt weergegeven.

   ![Activiteitsstatus](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

* Selecteer **Vernieuwen** in het scherm van de activiteitenmigratie om de weergave bij te werken totdat de **status** van de migratie als **Opnieuw afspelen** wordt weergegeven.

   > [!NOTE]
   > U kunt de activiteit selecteren voor meer informatie over metrische migratiegegevens op het niveau van een database of verzameling.

   ![Activiteitsstatus opnieuw afspelen](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Gegevens controleren in Cosmos DB

1. Breng wijzigingen aan uw MongoDB-brondatabase aan.
2. Maak verbinding met COSMOS DB om te verifiëren of de gegevens vanuit de MongoDB-bronserver worden gerepliceerd.

    ![Schermopname die laat zien waar u kunt verifiëren of de gegevens zijn gerepliceerd.](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Migratie voltooien

* Als alle documenten van de bron beschikbaar zijn op het COSMOS DB-doel, selecteert u **Voltooien** in het snelmenu van de migratieactiviteit om de migratie te voltooien.

    Met deze actie wordt het opnieuw afspelen van alle wijzigingen die in behandeling zijn, voltooid en tevens de migratie.

    ![Schermopname die de menu-optie Voltooien weergeeft.](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Optimalisatie na migratie

Nadat u de gegevens die zijn opgeslagen in de MongoDB-database, hebt gemigreerd naar de Azure Cosmos DB voor MongoDB-API, kunt u verbinding maken met Azure Cosmos DB en de gegevens beheren. U kunt na de migratie ook andere optimalisatiestappen uitvoeren, zoals het indexeringsbeleid optimaliseren, het standaardconsistentieniveau bijwerken, of de globale distributie configureren voor uw Azure Cosmos DB-account. Zie het artikel [Optimalisatie na migratie](../cosmos-db/mongodb-post-migration.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Informatie over Cosmos DB-service](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>Volgende stappen

* Lees de Microsoft-[handleiding voor databasemigratie](https://datamigration.microsoft.com/) voor hulp bij andere migratiescenario's.