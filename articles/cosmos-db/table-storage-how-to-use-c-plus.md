---
title: Azure Table Storage en Azure Cosmos DB Table-API gebruiken met C++
description: Sla gestructureerde gegevens op in de cloud met Azure Table Storage of de Azure Cosmos DB Table-API met behulp van C++.
ms.service: cosmos-db
ms.subservice: cosmosdb-table
ms.devlang: cpp
ms.topic: sample
ms.date: 10/07/2019
author: sakash279
ms.author: akshanka
ms.openlocfilehash: 79b58b76954cf15289e85dbf763b7a399897635d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94489876"
---
# <a name="how-to-use-azure-table-storage-and-azure-cosmos-db-table-api-with-c"></a>Azure-tabelopslag en Azure Cosmos DB Table-API gebruiken met C++
[!INCLUDE[appliesto-table-api](includes/appliesto-table-api.md)]

[!INCLUDE [storage-selector-table-include](../../includes/storage-selector-table-include.md)]
[!INCLUDE [storage-table-applies-to-storagetable-and-cosmos](../../includes/storage-table-applies-to-storagetable-and-cosmos.md)]

In deze handleiding worden veelvoorkomende scenario's beschreven met de Azure Table Storage-service of de Azure Cosmos DB Table-API. De voorbeelden zijn geschreven in C++ en maken gebruik van de [Azure Storage-clientbibliotheek voor C++](https://github.com/Azure/azure-storage-cpp/blob/master/README.md). Dit artikel behandelt de volgende scenario's:

* Een tabel maken en verwijderen
* Werken met tabelentiteiten

> [!NOTE]
> Deze handleiding is gericht op de Azure-opslagclientbibliotheek voor C++ versie 1.0.0 en hoger. De aanbevolen versie is Storage Client Library 2.2.0. Deze is beschikbaar via [NuGet](https://www.nuget.org/packages/wastorage) of [GitHub](https://github.com/Azure/azure-storage-cpp/).
>

## <a name="create-accounts"></a>Accounts maken

### <a name="create-an-azure-service-account"></a>Een Azure-serviceaccount maken

[!INCLUDE [cosmos-db-create-azure-service-account](../../includes/cosmos-db-create-azure-service-account.md)]

### <a name="create-an-azure-storage-account"></a>Een Azure-opslagaccount maken

[!INCLUDE [cosmos-db-create-storage-account](../../includes/cosmos-db-create-storage-account.md)]

### <a name="create-an-azure-cosmos-db-table-api-account"></a>Een Azure Cosmos DB Table-API-account maken

[!INCLUDE [cosmos-db-create-tableapi-account](../../includes/cosmos-db-create-tableapi-account.md)]

## <a name="create-a-c-application"></a>Een C++-toepassing maken

In deze handleiding gebruikt u opslagfuncties van een C++-toepassing. Hiertoe installeert u de Azure Storage-clientbibliotheek voor C++.

Voor het installeren van de Azure Storage-clientbibliotheek voor C++ gebruikt u de volgende methoden:

* **Linux:** Volg de instructies op de pagina met het [Leesmij-bestand voor de Azure Storage-clientbibliotheek voor C++: Aan de slag met de Linux](https://github.com/Azure/azure-storage-cpp#getting-started-on-linux).
* **Windows:** In Windows gebruikt u [vcpkg](https://github.com/microsoft/vcpkg) als afhankelijkheidsbeheerder. Volg de [quickstart](https://github.com/microsoft/vcpkg#quick-start) om vcpkg te initialiseren. Voer vervolgens de volgende opdracht uit om de bibliotheek te installeren:

```powershell
.\vcpkg.exe install azure-storage-cpp
```

Voor het maken van de broncode en het exporteren naar Nuget kunt u een handleiding vinden in het [Leesmij](https://github.com/Azure/azure-storage-cpp#download--install)-bestand.

### <a name="configure-access-to-the-table-client-library"></a>Toegang tot de tabelclientbibliotheek configureren

Als u Azure Storage-API's wilt gebruiken om tabellen te openen, voegt u boven aan het C++-bestand de volgende `include`-instructies toe:

```cpp
#include <was/storage_account.h>
#include <was/table.h>
```

Een Azure-opslagclient of Cosmos DB-client gebruikt een opslagverbindingstekenreeks voor het opslaan van eindpunten en referenties voor toegang tot gegevensbeheerservices. Wanneer u een clienttoepassing uitvoert, moet u de opslagverbindingsreeks of Azure Cosmos DB-verbindingsreeks in de juiste indeling opgeven.

### <a name="set-up-an-azure-storage-connection-string"></a>Een Azure-opslagverbindingstekenreeks instellen

In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de Azure Storage-verbindingsreeks:  

```cpp
// Define the Storage connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_storage_account>;AccountKey=<your_storage_account_key>"));
```

Gebruik de naam van het Storage-account voor `<your_storage_account>`. Gebruik voor <your_storage_account_key> de toegangssleutel voor het Storage-account dat in [Azure Portal](https://portal.azure.com) wordt vermeld. Zie [Een opslagaccount maken](../storage/common/storage-account-create.md) voor meer informatie over Storage-accounts en toegangssleutels.

### <a name="set-up-an-azure-cosmos-db-connection-string"></a>Een Azure Cosmos DB-verbindingstekenreeks instellen

In dit voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de Azure Cosmos DB-verbindingsreeks:

```cpp
// Define the Azure Cosmos DB connection string with your values.
const utility::string_t storage_connection_string(U("DefaultEndpointsProtocol=https;AccountName=<your_cosmos_db_account>;AccountKey=<your_cosmos_db_account_key>;TableEndpoint=<your_cosmos_db_endpoint>"));
```

Gebruik de naam van uw Azure Cosmos DB-account voor `<your_cosmos_db_account>`. Voer de primaire sleutel in voor `<your_cosmos_db_account_key>`. Voer het eindpunt in dat in [Azure Portal](https://portal.azure.com) wordt vermeld voor `<your_cosmos_db_endpoint>`.

Als u uw toepassing op uw lokale Windows-computer wilt testen, kunt u de Azure-opslagemulator gebruiken die met de [Azure SDK](https://azure.microsoft.com/downloads/) is geïnstalleerd. De opslagemulator is een hulpprogramma dat de Azure Blob-, Queue- en Table-services simuleert die beschikbaar zijn op uw lokale ontwikkelcomputer. In het volgende voorbeeld ziet u hoe u een statisch veld kunt declareren voor het opslaan van de verbindingsreeks naar uw lokale opslagemulator:  

```cpp
// Define the connection string with Azure Storage Emulator.
const utility::string_t storage_connection_string(U("UseDevelopmentStorage=true;"));  
```

Als u de Azure-opslagemulator wilt starten, selecteert u op het Windows-bureaublad de knop **Start** of de Windows-toets. Voer *Microsoft Azure-opslagemulator* in en voer deze uit. Zie [De Azure-opslagemulator gebruiken voor ontwikkelen en testen](../storage/common/storage-use-emulator.md) voor meer informatie.

### <a name="retrieve-your-connection-string"></a>De verbindingsreeks ophalen

U kunt de klasse `cloud_storage_account` gebruiken als representatie van uw opslagaccountgegevens. Voor het ophalen van uw opslagaccountgegevens uit de opslagverbindingsreeks gebruikt u de methode `parse`.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);
```

Vervolgens haalt u een verwijzing op naar een `cloud_table_client`-klasse. Met deze klasse kunt u referentieobjecten voor tabellen en entiteiten ophalen vanuit de Table Storage-service. Met de volgende code maakt u een `cloud_table_client`-object met behulp van het opslagaccountobject dat u hierboven hebt opgehaald:  

```cpp
// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();
```

## <a name="create-and-add-entities-to-a-table"></a>Entiteiten maken en aan een tabel toevoegen

### <a name="create-a-table"></a>Een tabel maken

Met een `cloud_table_client`-object kunt u referentieobjecten ophalen voor tabellen en entiteiten. Met de volgende code maakt u een `cloud_table_client`-object en gebruikt u dit om een nieuwe tabel te maken.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);  

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();  
```

### <a name="add-an-entity-to-a-table"></a>Een entiteit toevoegen aan een tabel

Als u een entiteit wilt toevoegen aan een tabel, maakt u een nieuw object `table_entity` en geeft u dit door aan `table_operation::insert_entity`. Met de volgende code gebruikt u de voornaam van de klant als de rijsleutel en de achternaam als de partitiesleutel. De partitie- en rijsleutel van een entiteit vormen samen de unieke id van de entiteit in de tabel. Voor entiteiten met dezelfde partitiesleutel kunnen sneller query’s worden uitgevoerd dan voor entiteiten met verschillende partitiesleutels. Door verschillende partitiesleutels te gebruiken, kunt u een grotere schaalbaarheid van parallelle bewerkingen bewerkstelligen. Raadpleeg de [Controlelijst voor prestaties en schaalbaarheid van Microsoft Azure Storage](../storage/blobs/storage-performance-checklist.md) voor meer informatie.

Met de volgende code maakt u een nieuwe instantie van `table_entity`, met een aantal klantgegevens die kunnen worden opgeslagen. Vervolgens wordt met de code `table_operation::insert_entity` aangeroepen om een `table_operation`-object te maken om een entiteit in een tabel in te voegen. Vervolgens wordt de nieuwe tabelentiteit eraan gekoppeld. Ten slotte roept de code methode `execute` aan op het `cloud_table`-object. De nieuwe `table_operation` stuurt u een aanvraag naar de Table service om de nieuwe klantentiteit in te voegen in de tabel `people`.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Retrieve a reference to a table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table if it doesn't exist.
table.create_if_not_exists();

// Create a new customer entity.
azure::storage::table_entity customer1(U("Harp"), U("Walter"));

azure::storage::table_entity::properties_type& properties = customer1.properties();
properties.reserve(2);
properties[U("Email")] = azure::storage::entity_property(U("Walter@contoso.com"));

properties[U("Phone")] = azure::storage::entity_property(U("425-555-0101"));

// Create the table operation that inserts the customer entity.
azure::storage::table_operation insert_operation = azure::storage::table_operation::insert_entity(customer1);

// Execute the insert operation.
azure::storage::table_result insert_result = table.execute(insert_operation);
```

### <a name="insert-a-batch-of-entities"></a>Een batch entiteiten invoegen

U kunt in één schrijfbewerking een batch entiteiten invoegen in de tabelservice. Met de volgende code maakt u een `table_batch_operation`-object en voegt u hier vervolgens drie invoegbewerkingen aan toe. Elke invoegbewerking wordt toegevoegd door het maken van een nieuw entiteitsobject, de waarden ervan in te stellen en vervolgens de methode `insert` aan te roepen op het `table_batch_operation`-object om de entiteit aan een nieuwe invoegbewerking te koppelen. Vervolgens roept de code `cloud_table.execute` aan om de bewerking uit te voeren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define a batch operation.
azure::storage::table_batch_operation batch_operation;

// Create a customer entity and add it to the table.
azure::storage::table_entity customer1(U("Smith"), U("Jeff"));

azure::storage::table_entity::properties_type& properties1 = customer1.properties();
properties1.reserve(2);
properties1[U("Email")] = azure::storage::entity_property(U("Jeff@contoso.com"));
properties1[U("Phone")] = azure::storage::entity_property(U("425-555-0104"));

// Create another customer entity and add it to the table.
azure::storage::table_entity customer2(U("Smith"), U("Ben"));

azure::storage::table_entity::properties_type& properties2 = customer2.properties();
properties2.reserve(2);
properties2[U("Email")] = azure::storage::entity_property(U("Ben@contoso.com"));
properties2[U("Phone")] = azure::storage::entity_property(U("425-555-0102"));

// Create a third customer entity to add to the table.
azure::storage::table_entity customer3(U("Smith"), U("Denise"));

azure::storage::table_entity::properties_type& properties3 = customer3.properties();
properties3.reserve(2);
properties3[U("Email")] = azure::storage::entity_property(U("Denise@contoso.com"));
properties3[U("Phone")] = azure::storage::entity_property(U("425-555-0103"));

// Add customer entities to the batch insert operation.
batch_operation.insert_or_replace_entity(customer1);
batch_operation.insert_or_replace_entity(customer2);
batch_operation.insert_or_replace_entity(customer3);

// Execute the batch operation.
std::vector<azure::storage::table_result> results = table.execute_batch(batch_operation);
```

Een aantal zaken die u moet weten over batchbewerkingen:

* U kunt binnen één batch maximaal honderd `insert`-, `delete`-, `merge`-, `replace`-, `insert-or-merge`- en `insert-or-replace`-bewerkingen uitvoeren in een willekeurige combinatie.  
* Een batchbewerking kan een ophaalbewerking hebben als dit de enige bewerking in de batch is.  
* Alle entiteiten in een batchbewerking moeten dezelfde partitiesleutel hebben.  
* Een batchbewerking is beperkt tot een gegevensnettolading van 4 MB.  

## <a name="query-and-modify-entities"></a>Entiteiten wijzigen en er query's op uitvoeren

### <a name="retrieve-all-entities-in-a-partition"></a>Alle entiteiten in een partitie ophalen

Gebruik een `table_query`-object om een query uit te voeren op een tabel met alle entiteiten in een partitie. Het volgende codevoorbeeld geeft een filter op voor entiteiten waarbij `Smith` de partitiesleutel is. In dit voorbeeld worden de velden van elke entiteit in de queryresultaten naar de console afgedrukt.  

> [!NOTE]
> Deze methoden worden momenteel niet ondersteund voor C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Construct the query operation for all customer entities where PartitionKey="Smith".
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::generate_filter_condition(U("PartitionKey"), azure::storage::query_comparison_operator::equal, U("Smith")));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Print the fields for each customer.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

Met de query in dit voorbeeld retourneert alle entiteiten die voldoen aan de filtercriteria. Als u grote tabellen hebt en de tabelentiteiten vaak moet downloaden, raden we u aan uw gegevens in plaats daarvan op te slaan in Azure-opslagblobs.

### <a name="retrieve-a-range-of-entities-in-a-partition"></a>Een bereik van entiteiten in een partitie ophalen

Als u geen query wilt uitvoeren op alle entiteiten in een partitie, kunt u een bereik opgeven. Combineer het filter voor de partitiesleutel met een filter voor rijsleutels. Het volgende codevoorbeeld maakt gebruik van twee filters om alle entiteiten met de partitie `Smith` op te halen waarbij de rijsleutel (voornaam) begint met een letter die in het alfabet vóór de `E` komt. Vervolgens worden de resultaten van de query afgedrukt.  

> [!NOTE]
> Deze methoden worden momenteel niet ondersteund voor C++ in Azure Cosmos DB.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create the table query.
azure::storage::table_query query;

query.set_filter_string(azure::storage::table_query::combine_filter_conditions(
    azure::storage::table_query::generate_filter_condition(U("PartitionKey"),
    azure::storage::query_comparison_operator::equal, U("Smith")),
    azure::storage::query_logical_operator::op_and,
    azure::storage::table_query::generate_filter_condition(U("RowKey"), azure::storage::query_comparison_operator::less_than, U("E"))));

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Loop through the results, displaying information about the entity.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    const azure::storage::table_entity::properties_type& properties = it->properties();

    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key()
        << U(", Property1: ") << properties.at(U("Email")).string_value()
        << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
}  
```

### <a name="retrieve-a-single-entity"></a>Eén entiteit ophalen

U kunt een query schrijven om één specifieke entiteit op te halen. In de volgende code wordt `table_operation::retrieve_entity` gebruikt om klant `Jeff Smith` mee aan te duiden. Deze methode retourneert één entiteit in plaats van een verzameling. De geretourneerde waarde bevindt zich in `table_result`. Het opgeven van zowel partitie- als rijsleutels in een query is de snelste manier om één entiteit op te halen uit de Tabelservice.  

```cpp
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Output the entity.
azure::storage::table_entity entity = retrieve_result.entity();
const azure::storage::table_entity::properties_type& properties = entity.properties();

std::wcout << U("PartitionKey: ") << entity.partition_key() << U(", RowKey: ") << entity.row_key()
    << U(", Property1: ") << properties.at(U("Email")).string_value()
    << U(", Property2: ") << properties.at(U("Phone")).string_value() << std::endl;
```

### <a name="replace-an-entity"></a>Een entiteit vervangen

Als u een entiteit wilt vervangen, haalt u deze op uit de Tabelservice, wijzigt u het entiteitsobject en slaat u de wijzigingen weer op in de Tabelservice. Met de volgende code wijzigt u het telefoonnummer en het e-mailadres van een bestaande klant. Door deze code wordt `table_operation::replace_entity` gebruikt in plaats van dat `table_operation::insert_entity` wordt aangeroepen. Met deze aanpak wordt de entiteit op de server volledig vervangen, tenzij de entiteit op de server sinds het ophalen is gewijzigd. Als deze is gewijzigd, mislukt de bewerking. Daardoor wordt voorkomen dat de toepassing een wijziging overschrijft die door een ander onderdeel is aangebracht tussen het moment van ophalen en het moment van bijwerken. U kunt deze fout het best als volgt afhandelen: de entiteit opnieuw ophalen, de gewenste wijzigingen (indien nog geldig) aanbrengen en vervolgens een andere `table_operation::replace_entity`-bewerking uitvoeren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Replace an entity.
azure::storage::table_entity entity_to_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_replace = entity_to_replace.properties();
properties_to_replace.reserve(2);

// Specify a new phone number.
properties_to_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0106"));

// Specify a new email address.
properties_to_replace[U("Email")] = azure::storage::entity_property(U("JeffS@contoso.com"));

// Create an operation to replace the entity.
azure::storage::table_operation replace_operation = azure::storage::table_operation::replace_entity(entity_to_replace);

// Submit the operation to the Table service.
azure::storage::table_result replace_result = table.execute(replace_operation);
```

### <a name="insert-or-replace-an-entity"></a>Een entiteit invoegen of vervangen

`table_operation::replace_entity`-bewerkingen mislukken als de entiteit is gewijzigd nadat deze is opgehaald van de server. Voor een geslaagde `table_operation::replace_entity`-bewerking moet u de entiteit bovendien van de server eerst ophalen. Soms weet u niet of de entiteit op de server aanwezig is. De huidige opgeslagen waarden zijn niet relevant, omdat ze allemaal met de update worden overschreven. Gebruik een `table_operation::insert_or_replace_entity`-bewerking om dit resultaat te bereiken. Met deze bewerking wordt de entiteit ingevoegd als deze niet bestaat. Met deze bewerking wordt de entiteit vervangen als deze bestaat. In het volgende codevoorbeeld wordt de klantentiteit voor `Jeff Smith` nog steeds opgehaald, maar vervolgens op de server opgeslagen door middel van `table_operation::insert_or_replace_entity`. Eventuele wijzigingen die in de entiteit zijn aangebracht tussen het moment van ophalen en het moment van bijwerken, worden overschreven.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Insert or replace an entity.
azure::storage::table_entity entity_to_insert_or_replace(U("Smith"), U("Jeff"));
azure::storage::table_entity::properties_type& properties_to_insert_or_replace = entity_to_insert_or_replace.properties();

properties_to_insert_or_replace.reserve(2);

// Specify a phone number.
properties_to_insert_or_replace[U("Phone")] = azure::storage::entity_property(U("425-555-0107"));

// Specify an email address.
properties_to_insert_or_replace[U("Email")] = azure::storage::entity_property(U("Jeffsm@contoso.com"));

// Create an operation to insert or replace the entity.
azure::storage::table_operation insert_or_replace_operation = azure::storage::table_operation::insert_or_replace_entity(entity_to_insert_or_replace);

// Submit the operation to the Table service.
azure::storage::table_result insert_or_replace_result = table.execute(insert_or_replace_operation);
```

### <a name="query-a-subset-of-entity-properties"></a>Een query uitvoeren op een subset van entiteitseigenschappen

Met een query naar een tabel kunnen slechts enkele eigenschappen van een entiteit worden opgehaald. Met de query in de volgende code gebruikt u de methode `table_query::set_select_columns` om alleen de e-mailadressen van de entiteiten in de tabel te retourneren.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Define the query, and select only the Email property.
azure::storage::table_query query;
std::vector<utility::string_t> columns;

columns.push_back(U("Email"));
query.set_select_columns(columns);

// Execute the query.
azure::storage::table_query_iterator it = table.execute_query(query);

// Display the results.
azure::storage::table_query_iterator end_of_results;
for (; it != end_of_results; ++it)
{
    std::wcout << U("PartitionKey: ") << it->partition_key() << U(", RowKey: ") << it->row_key();

    const azure::storage::table_entity::properties_type& properties = it->properties();
    for (auto prop_it = properties.begin(); prop_it != properties.end(); ++prop_it)
    {
        std::wcout << ", " << prop_it->first << ": " << prop_it->second.str();
    }

    std::wcout << std::endl;
}
```

> [!NOTE]
> Het uitvoeren van een query voor een aantal eigenschappen van een entiteit is een efficiëntere bewerking dan het ophalen van alle eigenschappen.
>

## <a name="delete-content"></a>Inhoud verwijderen

### <a name="delete-an-entity"></a>Een entiteit verwijderen

U kunt een entiteit verwijderen nadat u deze hebt opgehaald. Nadat u een entiteit hebt opgehaald, roept u `table_operation::delete_entity` aan met de entiteit die u wilt verwijderen. Roep vervolgens methode `cloud_table.execute` aan. Met de volgende code haalt u een entiteit met partitiesleutel `Smith` en rijsleutel `Jeff` op en verwijdert u deze.

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Create an operation to retrieve the entity with partition key of "Smith" and row key of "Jeff".
azure::storage::table_operation retrieve_operation = azure::storage::table_operation::retrieve_entity(U("Smith"), U("Jeff"));
azure::storage::table_result retrieve_result = table.execute(retrieve_operation);

// Create an operation to delete the entity.
azure::storage::table_operation delete_operation = azure::storage::table_operation::delete_entity(retrieve_result.entity());

// Submit the delete operation to the Table service.
azure::storage::table_result delete_result = table.execute(delete_operation);  
```

### <a name="delete-a-table"></a>Een tabel verwijderen

Ten slotte wordt met het volgende codevoorbeeld een tabel uit een opslagaccount verwijderd. Een verwijderde tabel kan enige tijd na de verwijdering niet opnieuw worden gemaakt.  

```cpp
// Retrieve the storage account from the connection string.
azure::storage::cloud_storage_account storage_account = azure::storage::cloud_storage_account::parse(storage_connection_string);

// Create the table client.
azure::storage::cloud_table_client table_client = storage_account.create_cloud_table_client();

// Create a cloud table object for the table.
azure::storage::cloud_table table = table_client.get_table_reference(U("people"));

// Delete the table if it exists
if (table.delete_table_if_exists())
{
    std::cout << "Table deleted!";
}
else
{
    std::cout << "Table didn't exist";
}
```

## <a name="troubleshooting"></a>Problemen oplossen

Voor Visual Studio Community Edition: als er opbouwfouten voor uw project ontstaan vanwege de insluitbestanden *storage_account.h* en *table.h*, moet u de **/permissive-** -compileerschakelaar verwijderen:

1. Klik in **Solution Explorer** met de rechtermuisknop op het project en selecteer **Properties**.
1. Vouw in het dialoogvenster **Eigenschappenpagina’s** de optie **Configuratie-eigenschappen** en vervolgens **C/C++** uit en selecteer **Taal**.
1. Stel **Overeenstemmingsmodus** in op **Nee**.

## <a name="next-steps"></a>Volgende stappen

[Microsoft Azure Storage Explorer](../vs-azure-tools-storage-manage-with-storage-explorer.md) is een gratis, zelfstandige app van Microsoft waarmee u visueel met Azure Storage-gegevens kunt werken in Windows, macOS en Linux.

Volg deze links koppelingen meer informatie over Azure Storage en de tabel-API in Azure Cosmos DB:

* [Introductie tot de tabel-API](table-introduction.md)
* [Azure Storage-resources in C++ weergeven](../storage/common/storage-c-plus-plus-enumeration.md)
* [Naslaginformatie over de Storage-clientbibliotheek voor C++](https://azure.github.io/azure-storage-cpp)
* [Documentatie voor Azure Storage](https://azure.microsoft.com/documentation/services/storage/)
