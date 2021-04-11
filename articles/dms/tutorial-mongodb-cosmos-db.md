---
title: 'Zelfstudie: MongoDB offline migreren naar de Azure Cosmos DB voor MongoDB-API'
titleSuffix: Azure Database Migration Service
description: Migreer vanuit MongoDB on-premises naar Azure Cosmos DB-API voor MongoDB offline, met behulp van Azure Database Migration Service.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: tutorial
ms.date: 02/03/2021
ms.openlocfilehash: 8977bb90087d9d3d4962d7eda50903d97da93539
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106443864"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-db-api-for-mongodb-offline"></a>Zelf studie: MongoDB migreren naar Azure Cosmos DB-API voor MongoDB offline

Gebruik Azure Database Migration Service voor het uitvoeren van een offline, eenmalige migratie van data bases van een on-premises of Cloud exemplaar van MongoDB naar de Azure Cosmos DB-API voor MongoDB.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Maak een exemplaar van de Azure Database Migration Service.
> * Maak een migratieproject met behulp van Azure Database Migration Service.
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.

In deze zelf studie migreert u een gegevensset in MongoDB die wordt gehost op een virtuele machine van Azure. Met behulp van Azure Database Migration Service migreert u de gegevensset naar de Azure Cosmos DB-API voor MongoDB. Zie [MongoDb installeren en configureren op een Windows-VM in azure](/previous-versions/azure/virtual-machines/windows/install-mongodb)als u nog geen MongoDb-bron hebt ingesteld.

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* [Voltooi de stappen voorafgaand aan de migratie](../cosmos-db/mongodb-pre-migration.md) , zoals het schatten van de door Voer en het kiezen van een partitie sleutel.
* [Maak een account voor de Azure Cosmos DB-API voor MongoDb](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Maak een Microsoft Azure Virtual Network voor Azure Database Migration Service met behulp van Azure Resource Manager. Dit implementatie model bevat een site-naar-site-verbinding met uw on-premises bron servers met behulp van [Azure ExpressRoute](../expressroute/expressroute-introduction.md) of [VPN](../vpn-gateway/vpn-gateway-about-vpngateways.md). Voor meer informatie over het maken van een virtueel netwerk raadpleegt u de [documentatie van Azure Virtual Network](../virtual-network/index.yml), met name de Quick Start-artikelen, met stapsgewijze Details.

    > [!NOTE]
    > Als u bij de installatie van een virtueel netwerk gebruikmaakt van ExpressRoute met netwerkpeering voor Microsoft, voegt u de volgende service-[eindpunten](../virtual-network/virtual-network-service-endpoints-overview.md) toe aan het subnet waarin de service wordt ingericht:
    >
    > * Eind punt van de doel database (bijvoorbeeld SQL-eind punt of Azure Cosmos DB-eind punt)
    > * Opslageindpunt
    > * Service Bus-eindpunt
    >
    > Deze configuratie is noodzakelijk omdat Azure Database Migration Service geen internetverbinding biedt.

* Zorg ervoor dat uw NSG-regels (netwerk beveiligings groep) voor uw virtuele netwerk niet de volgende communicatie poorten blok keren: 53, 443, 445, 9354 en 10000-20000. Raadpleeg [Netwerkverkeer filteren met netwerkbeveiligingsgroepen](../virtual-network/virtual-network-vnet-plan-design-arm.md) voor meer informatie.
* Stel uw Windows-firewall open voor toegang van Azure Database Migration Service tot de bronserver van MongoDB. Standaard verloopt deze via TCP-poort 27017.
* Wanneer u een firewall apparaat voor de bron database gebruikt, moet u mogelijk firewall regels toevoegen om Azure Database Migration Service toegang te geven tot de bron database voor migratie.

## <a name="configure-the-server-side-retry-feature"></a>De functie nieuwe poging aan server zijde configureren

U kunt profiteren van de mogelijkheden van resource beheer als u migreert van MongoDB naar Azure Cosmos DB. Met deze mogelijkheden kunt u volledig gebruik maken van uw ingerichte aanvraag eenheden (RU/s) van door voer. Azure Cosmos DB kan tijdens de migratie een bepaalde Database Migration Service aanvraag vertragen, als die aanvraag de door de container ingerichte RU/s overschrijdt. Vervolgens moet de aanvraag opnieuw worden geprobeerd.

Database Migration Service is geschikt voor het uitvoeren van nieuwe pogingen. Het is belang rijk om te begrijpen dat de retour tijd die is betrokken bij de netwerk-hop tussen Database Migration Service en Azure Cosmos DB invloed heeft op de totale reactie tijd van deze aanvraag. Het verbeteren van de reactie tijd voor beperkte aanvragen kan de totale tijd verkorten die nodig is voor de migratie.

Met de functie opnieuw proberen op de server van Azure Cosmos DB kan de service vertragings fout codes onderscheppen en opnieuw proberen met een veel lagere retour tijd, waardoor de reactie tijden van aanvragen aanzienlijk worden verbeterd.

Als u de nieuwe poging aan de server zijde wilt gebruiken, selecteert u in de Azure Cosmos DB-Portal **onderdelen**  >  **opnieuw proberen server**.

![Scherm afbeelding die laat zien waar u de nieuwe poging aan de server zijde zoekt.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Als de functie is uitgeschakeld, selecteert u **inschakelen**.

![Scherm afbeelding die laat zien hoe u de nieuwe poging aan server zijde inschakelt.](media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="register-the-resource-provider"></a>De resourceprovider registreren

1. Meld u aan bij de Azure-portal, selecteer **Alle services** en selecteer vervolgens **Abonnementen**.

   ![Scherm opname van portal-abonnementen.](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)

2. Selecteer het abonnement waarin u het Azure Database Migration Service-exemplaar wilt maken en selecteer vervolgens **Resourceproviders**.

    ![Scherm opname van resource providers.](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)

3. Zoek naar migratie en selecteer rechts van **Microsoft.DataMigration** de optie **Registreren**.

    ![Scherm opname van de registratie van de resource provider.](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Een instantie maken

1. Selecteer in de Azure-portal **Een resource maken**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service** uit de vervolgkeuzelijst.

    ![Schermopname van Azure Marketplace.](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2. Selecteer in het scherm **Azure Database Migration Service****Maken**.

    ![Scherm afbeelding die laat zien hoe u een instantie van Azure Database Migration Service maakt.](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3. Geef bij **migratie service maken** een naam op voor de service, het abonnement en een nieuwe of bestaande resource groep.

4. Selecteer de locatie waarop u het exemplaar van Azure Database Migration Service wilt maken. 

5. Selecteer een bestaand virtueel netwerk of maak een nieuw netwerk.

    Het virtuele netwerk biedt Azure Database Migration Service toegang tot de broninstantie van MongoDB en het doelaccount van Azure Cosmos DB.

    Zie [een virtueel netwerk maken met behulp van de Azure Portal](../virtual-network/quick-create-portal.md)voor meer informatie over het maken van een virtueel netwerk in de Azure Portal.

6. Selecteer een prijscategorie.

    Zie voor meer informatie over de kosten en prijscategorieën de [Pagina met prijzen](https://aka.ms/dms-pricing).

    ![Scherm opname van de configuratie-instellingen voor het exemplaar van Azure Database Migration Service.](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7. Selecteer **Maken** om de dienst te maken.

## <a name="create-a-migration-project"></a>Een migratieproject maken

Nadat u de service hebt gemaakt, zoekt u deze in de Azure Portal en opent u deze. Maak vervolgens een nieuw migratie project.

1. Selecteer in de Azure-portal **Alle diensten**, zoek naar Azure Database Migration Service, en selecteer vervolgens **Azure Database Migration Service**.

      ![Scherm afbeelding die laat zien hoe u alle exemplaren van Azure Database Migration Service kunt vinden.](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Zoek in het scherm **Azure Database Migration Service** naar de naam van de Azure Database Migration Service-instantie die u hebt gemaakt en selecteer vervolgens de instantie.

3. Selecteer **+ Nieuw migratie project**.

4. In **Nieuw migratie project** geeft u een naam op voor het project en selecteert u in het tekstvak **type bron server** de optie **MongoDb**. Selecteer in het tekstvak **doel server type** de optie **CosmosDB (MongoDb-API)** en selecteer vervolgens **type activiteit** voor het kiezen van **offline gegevens migratie**. 

    ![Scherm opname van project opties.](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5. Selecteer **Maken en uitvoeren van de activiteit** om het project te maken en de migratieactiviteit uit te voeren.

## <a name="specify-source-details"></a>Geef brondetails op

1. Geef in het scherm **Brondetails** de verbindingsgegevens op voor de MongoDB-bronserver.

   > [!IMPORTANT]
   > Azure Database Migration Service biedt geen ondersteuning voor Azure Cosmos DB als een bron.

    Er zijn drie modi om verbinding te maken met een bron:
   * **Standaard modus**, die een Fully Qualified Domain name of een IP-adres, poort nummer en verbindings referenties accepteert.
   * **Modus verbindingsteken reeks**, die een MongoDB accepteert Connection String zoals beschreven in de notatie van de [VERBINDINGS reeks-URI](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Gegevens uit Azure-opslag**: deze accepteert een SAS-URL van de blob-container. Select **BLOB bevat BSON-dumps** als de BLOB-container BSON-dumps heeft die zijn geproduceerd door het MongoDb [bsondump-hulp programma](https://docs.mongodb.com/manual/reference/program/bsondump/). Selecteer deze optie niet als de container JSON-bestanden bevat.

     Als u deze optie selecteert, moet u ervoor zorgen dat het opslag account connection string wordt weer gegeven in de volgende indeling:

     ```
     https://blobnameurl/container?SASKEY
     ```

     U vindt deze connection string van de BLOB-container in Azure Storage Explorer. Het maken van de SAS voor de desbetreffende container biedt u de URL in de gewenste indeling.
     
     Op basis van de type dump informatie in Azure Storage houdt u het volgende in acht:

     * Voor BSON-dumps moeten de gegevens in de BLOB-container de bsondump-indeling hebben. Plaats gegevens bestanden in mappen met de naam na de data bases in de indeling *verzameling. bson*. Geef een naam op voor de meta gegevens bestanden met behulp van de notatie *collection.metadata.jsop*.

     * Voor JSON-dumps moeten de bestanden in de blob-container worden geplaatst in mappen die zijn genoemd naar de omvattende databases. In elke databasemap moeten gegevens bestanden in een submap met de naam *gegevens* worden geplaatst en met de indeling *collection.jsop*. Plaats meta gegevens bestanden in een submap met de naam *meta gegevens* en met de naam met dezelfde indeling *collection.jsop*. De metagegevensbestanden moeten de indeling hebben die wordt geproduceerd door het MongoDB-hulpprogramma bsondump.

    > [!IMPORTANT]
    > Het is niet raadzaam om een zelfondertekend certificaat te gebruiken op de MongoDB-server. Als u één moet gebruiken, maakt u verbinding met de server met behulp van de connection string modus en zorgt u ervoor dat uw connection string aanhalings tekens ("") heeft.
    >
    >```
    >&sslVerifyCertificate=false
    >```

   U kunt ook het IP-adres gebruiken voor situaties waarin DNS-naam omzetting niet mogelijk is.

   ![Scherm opname van het opgeven van de bron gegevens.](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Selecteer **Opslaan**.

## <a name="specify-target-details"></a>Doeldetails opgeven

1. Geef in het scherm **doel Details van migratie** de verbindings gegevens voor het doel Azure Cosmos DB account op. Dit account is de vooraf ingerichte Azure Cosmos DB-API voor het MongoDB-account waarnaar u uw MongoDB-gegevens migreert.

    ![Scherm opname van het opgeven van de doel Details.](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Selecteer **Opslaan**.

## <a name="map-to-target-databases"></a>Toewijzen aan doeldatabases

1. Wijs in het scherm **Toewijzen aan doeldatabases** de bron- en doeldatabase voor de migratie toe.

    Als de doeldatabase de naam van de dezelfde database als de bron-database bevat, wordt in Azure Database Migration Service de doeldatabase standaard geselecteerd.

    Als **Create** wordt weer gegeven naast de naam van de data base, geeft dit aan dat Azure database Migration service de doel database niet heeft gevonden en de service de Data Base voor u maakt.

    Op dit moment in de migratie kunt u [doorvoer inrichten](../cosmos-db/set-throughput.md). In Azure Cosmos DB kunt u de door Voer inrichten op het niveau van de data base of afzonderlijk voor elke verzameling. De door Voer wordt gemeten in [aanvraag eenheden](../cosmos-db/request-units.md). Meer informatie over de [prijzen van Azure Cosmos DB](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Scherm opname van de toewijzing van doel databases.](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Selecteer **Opslaan**.
3. Vouw in het scherm **Verzamelingsinstelling** de lijst met verzamelingen uit en bekijk welke verzamelingen worden gemigreerd.

    Azure Database Migration Service selecteert automatisch alle verzamelingen die bestaan in het bron MongoDB-exemplaar dat niet bestaat op het doel Azure Cosmos DB account. Als u verzamelingen die al gegevens bevatten, opnieuw wilt migreren, moet u de verzamelingen in dit deel venster expliciet selecteren.

    U kunt opgeven hoeveel RU's u voor de verzamelingen wilt gebruiken. Er worden slimme standaardwaarden voorgesteld op basis van de gezamenlijke grootte.

    > [!NOTE]
    > Voer de database migratie en-verzameling parallel uit. Als dat nodig is, kunt u meerdere exemplaren van Azure Database Migration Service gebruiken om de uitvoering te versnellen.

    U kunt ook een shardsleutel opgeven om te profiteren van [partitionering in Azure Cosmos DB](../cosmos-db/partitioning-overview.md) voor optimale schaalbaarheid. Bekijk de [Aanbevolen procedures voor het selecteren van een Shard/partitie sleutel](../cosmos-db/partitioning-overview.md#choose-partitionkey).

    ![Scherm opname van het selecteren van verzamelingen tabellen.](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Selecteer **Opslaan**.

5. Geef in het tekstvak **Naam activiteit** van het scherm **Migratieoverzicht** een naam op voor de migratieactiviteit.

    ![Scherm opname van de nigration-samen vatting.](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>De migratie uitvoeren

Selecteer **Migratie uitvoeren**. Het venster migratie activiteit wordt weer gegeven en de status van de activiteit is **niet gestart**.

![Scherm opname van de status van de activiteit.](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>Bewaak de migratie

Selecteer **vernieuwen** in het scherm migratie activiteit om de weer gave bij te werken totdat de status van de migratie wordt weer gegeven als **voltooid**.

> [!NOTE]
> U kunt de activiteit selecteren voor meer informatie over metrische gegevens voor de migratie van data base-en verzamelings niveau.

![Screnshot die de status van de activiteit weer geven is voltooid.](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-azure-cosmos-db"></a>Gegevens in Azure Cosmos DB controleren

Nadat de migratie is voltooid, kunt u uw Azure Cosmos DB-account controleren om te controleren of alle verzamelingen zijn gemigreerd.

![Schermopname die laat zien waar in uw Azure Cosmos DB-account u kunt controleren of alle verzamelingen met succes zijn gemigreerd.](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="post-migration-optimization"></a>Optimalisatie na migratie

Nadat u de gegevens die zijn opgeslagen in MongoDB Data Base hebt gemigreerd naar de Azure Cosmos DB-API voor MongoDB, kunt u verbinding maken met Azure Cosmos DB en de gegevens beheren. U kunt ook andere optimalisatie stappen voor na de migratie uitvoeren. Dit kan omvatten het optimaliseren van het indexerings beleid, het bijwerken van het standaard consistentie niveau of het configureren van globale distributie voor uw Azure Cosmos DB-account. Zie [optimalisatie na de migratie](../cosmos-db/mongodb-post-migration.md)voor meer informatie.

## <a name="next-steps"></a>Volgende stappen

Bekijk de migratie richtlijnen voor aanvullende scenario's in de [Azure data base Migration Guide](https://datamigration.microsoft.com/).



