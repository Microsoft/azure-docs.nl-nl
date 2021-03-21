---
title: Migreer MongoDB offline naar Azure Cosmos DB-API voor MongoDB, met behulp van MongoDB systeem eigen hulpprogram ma's
description: Meer informatie over hoe MongoDB systeem eigen hulpprogram ma's kunnen worden gebruikt voor het migreren van kleine gegevens sets van MongoDB-instanties naar Azure Cosmos DB
author: anfeldma-ms
ms.author: anfeldma
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.topic: tutorial
ms.date: 02/10/2021
ms.reviewer: sngun
ms.openlocfilehash: 2e9f3c877a5c4650d3e31fa414cac76837f4c9e8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "101655748"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-mongodb-native-tools"></a>Zelf studie: MongoDB migreren naar de API van Azure Cosmos DB voor MongoDB offline met MongoDB systeem eigen hulpprogram ma's

U kunt MongoDB systeem eigen hulpprogram ma's gebruiken om een offline migratie uit te voeren van data bases van een on-premises of een Cloud exemplaar van MongoDB naar de Azure Cosmos DB-API voor MongoDB.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
>
> * Kies het juiste MongoDB systeem eigen hulp programma voor uw gebruiks voorbeeld
> * De migratie uitvoeren.
> * Houd de migratie in de gaten.
> * Controleer of de migratie is geslaagd.

In deze zelf studie migreert u een gegevensset in MongoDB die wordt gehost op een virtuele machine van Azure naar Azure Cosmos DB API voor MongoDB met behulp van MongoDB systeem eigen hulpprogram ma's. De systeem eigen hulpprogram ma's van MongoDB zijn een set binaire bestanden die het manipuleren van gegevens op een bestaand MongoDB-exemplaar vergemakkelijken. Omdat Azure Cosmos DB een Mongo-API beschikbaar maakt, kunnen de oorspronkelijke hulpprogram ma's van MongoDB gegevens in Azure Cosmos DB invoegen. De focus van dit document is het migreren van gegevens uit een MongoDB-exemplaar met behulp van *mongoexport/mongoimport* of *mongodump/mongorestore*. Aangezien de systeem eigen hulpprogram ma's verbinding maken met MongoDB met behulp van verbindings reeksen, kunt u de hulpprogram ma's overal uitvoeren, maar we raden u echter aan deze hulpprogram ma's uit te voeren binnen hetzelfde netwerk als het MongoDB-exemplaar om Firewall problemen te voor komen. 

Met de MongoDB-systeem eigen hulpprogram ma's kunnen gegevens alleen worden verplaatst als de host-hardware deze toestaat; de systeem eigen hulpprogram ma's kunnen de eenvoudigste oplossing zijn voor kleine gegevens sets, waarbij de totale migratie tijd geen betrekking heeft. De [MongoDb Spark-connector](https://docs.mongodb.com/spark-connector/current/), de [Azure data MIGRATION service (DMS)](../dms/tutorial-mongodb-cosmos-db.md)of [Azure Data Factory (ADF)](../data-factory/connector-azure-cosmos-db-mongodb-api.md) kan beter als u een schaal bare migratie pijplijn nodig hebt.

Als u nog geen MongoDB-bron hebt ingesteld, raadpleegt u het artikel over het [installeren en configureren van MongoDB op een Windows-VM in Azure](/previous-versions/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Vereisten

Voor het voltooien van deze zelfstudie hebt u het volgende nodig:

* [Voltooi de stappen voorafgaand aan de migratie](../cosmos-db/mongodb-pre-migration.md), zoals het schatten van de doorvoer of het kiezen van een partitiesleutel en het indexeringsbeleid.
* [Maak een Azure Cosmos DB-API voor het MongoDb-account](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Meld u aan bij uw MongoDB-exemplaar
    * [Down load en installeer de MongoDb-systeem eigen hulpprogram ma's via deze koppeling](https://www.mongodb.com/try/download/database-tools).
        * **Zorg ervoor dat de versie van uw MongoDB-systeem eigen hulpprogram ma's overeenkomt met uw bestaande MongoDB-exemplaar.**
        * Als uw MongoDB-exemplaar een andere versie heeft dan Azure Cosmos DB Mongo-API, installeert u vervolgens **beide MongoDb systeem eigen versies en gebruikt u de juiste versie van het hulp programma voor MongoDb en Azure Cosmos DB Mongo-API.**
    * Een gebruiker met `readWrite` machtigingen toevoegen, tenzij er al een bestaat. Verderop in deze zelf studie geeft u deze gebruikers naam/wacht woord op voor de *mongoexport* -en *mongodump* -hulpprogram ma's.

## <a name="configure-azure-cosmos-db-server-side-retries"></a>Nieuwe pogingen van Azure Cosmos DB server configureren

Klanten die migreren van MongoDB naar Azure Cosmos DB profiteren van de mogelijkheden van resource beheer, die de mogelijkheid bieden om uw ingerichte RU/s van door Voer volledig te benutten. Azure Cosmos DB kan een gegeven aanvraag beperken tijdens de migratie als die aanvraag de door de container ingerichte RU/s overschrijdt. vervolgens moet de aanvraag opnieuw worden geprobeerd. De round-trip tijd die is betrokken bij de netwerk-hop tussen het migratie programma en Azure Cosmos DB beïnvloedt de totale reactie tijd van die aanvraag; het is ook mogelijk dat MongoDB systeem eigen hulpprogram ma's geen nieuwe pogingen afhandelen. Met de functie *opnieuw proberen* op de Server van Azure Cosmos db kan de service vertragings fout codes onderscheppen en opnieuw proberen met een veel lagere retour tijd, waardoor de reactie tijden van aanvragen aanzienlijk worden verbeterd. Vanuit het perspectief van MongoDB systeem eigen hulpprogram ma's is de nood zaak om nieuwe pogingen af te handelen beperkt, waardoor uw ervaring tijdens de migratie positief wordt beïnvloed.

De functie voor het opnieuw proberen van de server is te vinden op de Blade *functies* van de Azure Cosmos DB Portal

![Scherm opname van de functie MongoDB SSR.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-feature.png)

Als deze optie is *uitgeschakeld*, wordt u aangeraden deze in te scha kelen, zoals hieronder wordt weer gegeven

![Scherm opname van MongoDB SSR inschakelen.](../dms/media/tutorial-mongodb-to-cosmosdb/mongo-server-side-retry-enable.png)

## <a name="choose-the-proper-mongodb-native-tool"></a>Kies het juiste MongoDB systeem eigen hulp programma

![Diagram van het selecteren van het best MongoDB systeem eigen hulp programma.](media/tutorial-mongotools-cosmos-db/mongodb-native-tool-selection-table.png)

* *mongoexport/mongoimport* is het beste paar migratie hulpprogramma's voor het migreren van een subset van uw MongoDb-data base.
    * *mongoexport* exporteert uw bestaande gegevens naar een door menselijke Lees bare JSON of CSV-bestand. *mongoexport* neemt een argument dat de subset van uw bestaande gegevens aangeeft die moeten worden geëxporteerd. 
    * *mongoimport* opent een JSON-of CSV-bestand en voegt de inhoud in het exemplaar van de doel database in (Azure Cosmos db in dit geval). 
    * JSON en CSV zijn geen compacte indelingen. u kunt overtollige netwerk kosten in rekening brengen als *mongoimport* gegevens verzendt naar Azure Cosmos db.
* *mongodump/mongorestore* is het beste paar migratie hulpprogramma's voor het migreren van uw hele MongoDb-data base. De compacte BSON-indeling maakt efficiënter gebruik van netwerk bronnen omdat de gegevens in Azure Cosmos DB worden ingevoegd.
    * *mongodump* exporteert uw bestaande gegevens als een BSON-bestand.
    * *mongorestore* IMPORTEERT uw BSON-bestands dump in azure Cosmos db.
* Als u alleen een klein JSON-bestand hebt dat u wilt importeren in Azure Cosmos DB Mongo API, is het *mongoimport* -hulp programma een snelle oplossing voor het opnemen van de gegevens.

## <a name="collect-the-azure-cosmos-db-mongo-api-credentials"></a>De Azure Cosmos DB-API-referenties voor Mongo verzamelen

Azure Cosmos DB Mongo-API biedt compatibele toegangs referenties voor het gebruik van MongoDB-systeem eigen hulpprogram ma's. U moet deze toegangs referenties voor handen hebben om gegevens te kunnen migreren naar Azure Cosmos DB Mongo-API. U vindt deze referenties als volgt:

1. Open de Azure Portal
1. Navigeer naar uw Azure Cosmos DB Mongo API-account
1. Selecteer in het navigatie venster links de Blade *verbindingsteken reeks* en u ziet een weer gave zoals hieronder:

    ![Scherm opname van Azure Cosmos DB referenties.](media/tutorial-mongotools-cosmos-db/cosmos-mongo-credentials.png)

    * *Host* -het Azure Cosmos DB-eind punt fungeert als een MongoDb-hostnaam
    * *Poort* : wanneer MongoDb systeem eigen hulpprogram ma's verbinding maken met Azure Cosmos DB, moet u deze poort expliciet opgeven
    * *Gebruikers naam* : het voor voegsel van de Azure Cosmos DB endpoint domein naam fungeert als de MongoDb-gebruikers naam
    * *Wacht woord* : de Azure Cosmos DB hoofd sleutel fungeert als het MongoDb-wacht woord
    * Let ook op het *SSL* -veld dat `true` het MongoDb systeem eigen hulp programma **moet** gebruiken voor het schrijven van gegevens naar Azure Cosmos db

## <a name="perform-the-migration"></a>De migratie uitvoeren

1. Kies de data base (s) en verzameling (en) die u wilt migreren. In dit voor beeld migreren we de *query* verzameling in de *EDX* -data base van MongoDb naar Azure Cosmos db.

In de rest van deze sectie vindt u instructies voor het gebruik van het paar hulpprogram ma's dat u in de vorige sectie hebt geselecteerd.

### <a name="mongoexportmongoimport"></a>*mongoexport/mongoimport*

1. Als u de gegevens uit het bron MongoDB-exemplaar wilt exporteren, opent u een Terminal op de computer met het MongoDB-exemplaar. Als het een Linux-computer is, typt u

    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json`

    In Windows is het uitvoer bare bestand `mongoexport.exe` . De *host*, *poort*, *gebruikers naam* en het *wacht woord* moeten worden ingevuld op basis van de eigenschappen van uw bestaande MongoDb-data base-instantie. 
    
    U kunt er ook voor kiezen om alleen een subset van de MongoDB-gegevensset te exporteren. Een manier om dit te doen is door een aanvullend filter argument toe te voegen:
    
    `mongoexport --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx.json --query '{"field1":"value1"}'`

    Alleen documenten die overeenkomen met het filter `{"field1":"value1"}` , worden geëxporteerd.

    Wanneer u de oproep hebt uitgevoerd, ziet u dat er een `edx.json` bestand wordt gemaakt:

    ![Scherm opname van mongoexport-aanroep.](media/tutorial-mongotools-cosmos-db/mongo-export-output.png)
1. U kunt dezelfde Terminal gebruiken om te importeren `edx.json` in azure Cosmos db. Als u `mongoimport` op een Linux-computer werkt, typt u

    `mongoimport --host HOST:PORT -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl --type json --writeConcern="{w:0}" --file edx.json`

    In Windows is het uitvoer bare bestand `mongoimport.exe` . De *host*, *poort*, *gebruikers naam* en het *wacht woord* moeten worden ingevuld op basis van de referenties van de Azure Cosmos DB die u eerder hebt verzameld. 
1. De Terminal uitvoer van *mongoimport* **bewaken** . U ziet dat er regels met tekst worden afgedrukt op de Terminal met updates voor de migratie status:

    ![Scherm opname van mongoimport-aanroep.](media/tutorial-mongotools-cosmos-db/mongo-import-output.png)    

1. Controleer ten slotte Azure Cosmos DB om te **controleren** of de migratie is geslaagd. Open de Azure Cosmos DB Portal en navigeer naar Data Explorer. U moet (1) zien dat er een *EDX* -data base met een *importedQuery* -verzameling is gemaakt, en (2) als u slechts een subset van gegevens hebt geëxporteerd, mag *importedQuery* *alleen* documenten bevatten die overeenkomen met de gewenste subset van de gegevens. In het onderstaande voor beeld is er slechts één document die overeenkomt met het filter `{"field1":"value1"}` :

    ![Scherm opname van Cosmos DB gegevens verificatie.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos.png)    

### <a name="mongodumpmongorestore"></a>*mongodump/mongorestore*

1. Als u een BSON-gegevens dump van uw MongoDB-exemplaar wilt maken, opent u een Terminal op de computer met het MongoDB-exemplaar. Als het een Linux-computer is, typt u

    `mongodump --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection query --out edx-dump`

    De *host*, *poort*, *gebruikers naam* en het *wacht woord* moeten worden ingevuld op basis van de eigenschappen van uw bestaande MongoDb-data base-instantie. U ziet dat er een `edx-dump` map wordt gemaakt en dat de mapstructuur van de `edx-dump` resource hiërarchie (data base en verzamelings structuur) van het bron MongoDb-exemplaar opnieuw wordt gegenereerd. Elke verzameling wordt vertegenwoordigd door een BSON-bestand:

    ![Scherm opname van mongodump-aanroep.](media/tutorial-mongotools-cosmos-db/mongo-dump-output.png)
1. U kunt dezelfde Terminal gebruiken om de inhoud van `edx-dump` naar Azure Cosmos DB te herstellen. Als u `mongorestore` op een Linux-computer werkt, typt u

    `mongorestore --host HOST:PORT --authenticationDatabase admin -u USERNAME -p PASSWORD --db edx --collection importedQuery --ssl edx-dump/edx/query.bson`

    In Windows is het uitvoer bare bestand `mongorestore.exe` . De *host*, *poort*, *gebruikers naam* en het *wacht woord* moeten worden ingevuld op basis van de referenties van de Azure Cosmos DB die u eerder hebt verzameld. 
1. De Terminal uitvoer van *mongorestore* **bewaken** . U ziet dat er regels worden afgedrukt voor de Terminal update op de migratie status:

    ![Scherm opname van mongorestore-aanroep.](media/tutorial-mongotools-cosmos-db/mongo-restore-output.png)    

1. Controleer ten slotte Azure Cosmos DB om te **controleren** of de migratie is geslaagd. Open de Azure Cosmos DB Portal en navigeer naar Data Explorer. Als het goed is, ziet u (1) dat er een *EDX* -data base met een *importedQuery* -verzameling is gemaakt, en (2) *importedQuery* de *volledige* gegevensset van de bron verzameling moet bevatten:

    ![Scherm opname van het controleren van Cosmos DB mongorestore-gegevens.](media/tutorial-mongotools-cosmos-db/mongo-review-cosmos-restore.png)    

## <a name="post-migration-optimization"></a>Optimalisatie na migratie

Nadat u de gegevens die zijn opgeslagen in de MongoDB-database, hebt gemigreerd naar de Azure Cosmos DB voor MongoDB-API, kunt u verbinding maken met Azure Cosmos DB en de gegevens beheren. U kunt na de migratie ook andere optimalisatiestappen uitvoeren, zoals het indexeringsbeleid optimaliseren, het standaardconsistentieniveau bijwerken, of de globale distributie configureren voor uw Azure Cosmos DB-account. Zie het artikel [Optimalisatie na migratie](../cosmos-db/mongodb-post-migration.md) voor meer informatie.

## <a name="additional-resources"></a>Aanvullende bronnen

* [Informatie over Cosmos DB-service](https://azure.microsoft.com/services/cosmos-db/)
* [Documentatie voor MongoDB data base-hulpprogram ma's](https://docs.mongodb.com/database-tools/)

## <a name="next-steps"></a>Volgende stappen

* Lees de Microsoft-[handleiding voor databasemigratie](https://datamigration.microsoft.com/) voor hulp bij andere migratiescenario's.