---
title: Ingebouwde notebook opdrachten en-functies gebruiken in Azure Cosmos DB python-notebooks (preview)
description: Meer informatie over het gebruik van ingebouwde opdrachten en functies voor het uitvoeren van algemene bewerkingen met behulp van de ingebouwde python-notebooks van Azure Cosmos DB.
author: deborahc
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 05/19/2020
ms.author: dech
ms.openlocfilehash: b89fcf32ed033f359b4db601e36cc69bb899944d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98165821"
---
# <a name="use-built-in-notebook-commands-and-features-in-azure-cosmos-db-python-notebooks-preview"></a>Ingebouwde notebook opdrachten en-functies gebruiken in Azure Cosmos DB python-notebooks (preview)
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Met ingebouwde Jupyter-notebooks in Azure Cosmos DB kunt u uw gegevens van de Azure Portal analyseren en visualiseren. In dit artikel wordt beschreven hoe u ingebouwde notebook opdrachten en-functies gebruikt voor veelvoorkomende bewerkingen in python-notebooks.

## <a name="install-a-new-package"></a>Een nieuw pakket installeren
Nadat u de ondersteuning voor notebooks voor uw Azure Cosmos-accounts hebt ingeschakeld, kunt u een nieuw notitie blok openen en een pakket installeren.

In een nieuwe code-cel, voegt u de volgende code in en voert u deze uit, waarbij u vervangt ``PackageToBeInstalled`` door het gewenste python-pakket.
```python
import sys
!{sys.executable} -m pip install PackageToBeInstalled --user
```
Dit pakket is beschikbaar voor gebruik vanuit elk notebook in de Azure Cosmos-account werkruimte. 

> [!TIP]
> Als uw notitie blok een aangepast pakket vereist, raden we u aan een cel in uw notitie blok toe te voegen om het pakket te installeren, omdat pakketten worden verwijderd als u [de werk ruimte opnieuw instelt](#reset-notebooks-workspace).  

## <a name="run-a-sql-query"></a>Een SQL-query uitvoeren

U kunt de ``%%sql`` opdracht Magic gebruiken om een [SQL-query](sql-query-getting-started.md) uit te voeren op een wille keurige container in uw account. Gebruik de syntaxis:

```python
%%sql --database {database_id} --container {container_id}
{Query text}
```

- Vervang ``{database_id}`` en door ``{container_id}`` de naam van de data base en de container in uw Cosmos-account. Als de ``--database`` ``--container`` argumenten en niet zijn opgenomen, wordt de query uitgevoerd op de [standaard database en-container](#set-default-database-for-queries).
- U kunt alle SQL-query's uitvoeren die geldig zijn in Azure Cosmos DB. De query tekst moet op een nieuwe regel staan.

Bijvoorbeeld: 
```python
%%sql --database RetailDemo --container WebsiteData
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
Voer ```%%sql?``` in een cel uit om de Help-documentatie voor de SQL Magic-opdracht in het notitie blok te bekijken.

## <a name="run-a-sql-query-and-output-to-a-pandas-dataframe"></a>Een SQL-query en-uitvoer uitvoeren naar een Panda data frame

U kunt de resultaten van een ``%%sql`` query naar een [Panda data frame](https://pandas.pydata.org/pandas-docs/stable/reference/api/pandas.DataFrame.html#pandas.DataFrame)uitvoeren. Gebruik de syntaxis: 

```python
%%sql --database {database_id} --container {container_id} --output {outputDataFrameVar}
{Query text}
```
- Vervang ``{database_id}`` en door ``{container_id}`` de naam van de data base en de container in uw Cosmos-account. Als de ``--database`` ``--container`` argumenten en niet zijn opgenomen, wordt de query uitgevoerd op de [standaard database en-container](#set-default-database-for-queries).
- Vervang door ``{outputDataFrameVar}`` de naam van de data frame-variabele die de resultaten bevat.
- U kunt alle SQL-query's uitvoeren die geldig zijn in Azure Cosmos DB. De query tekst moet op een nieuwe regel staan. 

Bijvoorbeeld:

```python
%%sql --database RetailDemo --container WebsiteData --output df_cosmos
SELECT c.Action, c.Price as ItemRevenue, c.Country, c.Item FROM c
```
```python
df_cosmos.head(10)

    Action    ItemRevenue    Country/Region    Item
0    Viewed    9.00    Tunisia    Black Tee
1    Viewed    19.99    Antigua and Barbuda    Flannel Shirt
2    Added    3.75    Guinea-Bissau    Socks
3    Viewed    3.75    Guinea-Bissau    Socks
4    Viewed    55.00    Czech Republic    Rainjacket
5    Viewed    350.00    Iceland    Cosmos T-shirt
6    Added    19.99    Syrian Arab Republic    Button-Up Shirt
7    Viewed    19.99    Syrian Arab Republic    Button-Up Shirt
8    Viewed    33.00    Tuvalu    Red Top
9    Viewed    14.00    Cabo Verde    Flip Flop Shoes
```
## <a name="set-default-database-for-queries"></a>Standaard database voor query's instellen
U kunt instellen dat de standaard database ```%%sql``` opdrachten voor het notitie blok worden gebruikt. Vervang door ```{database_id}``` de naam van uw data base.

```python
%database {database_id}
```
Voer ```%database?``` in een cel uit om de documentatie in het notitie blok te bekijken.

## <a name="set-default-container-for-queries"></a>Standaard container voor query's instellen
U kunt instellen dat de standaard container ```%%sql``` opdrachten worden gebruikt voor het notitie blok. Vervang door ```{container_id}``` de naam van de container.

```python
%container {container_id}
```
Voer ```%container?``` in een cel uit om de documentatie in het notitie blok te bekijken.

## <a name="upload-json-items-to-a-container"></a>JSON-items uploaden naar een container
U kunt de ``%%upload`` opdracht Magic gebruiken om gegevens van een JSON-bestand te uploaden naar een opgegeven Azure Cosmos-container. Gebruik de volgende opdracht om de items te uploaden:

```python
%%upload --databaseName {database_id} --containerName {container_id} --url {url_location_of_file}
```

- Vervang ``{database_id}`` en door ``{container_id}`` de naam van de data base en de container in uw Azure Cosmos-account. Als de ``--database`` ``--container`` argumenten en niet zijn opgenomen, wordt de query uitgevoerd op de [standaard database en-container](#set-default-database-for-queries).
- Vervang door ``{url_location_of_file}`` de locatie van het JSON-bestand. Het bestand moet een matrix met geldige JSON-objecten zijn en moet toegankelijk zijn via het open bare Internet.

Bijvoorbeeld:

```python
%%upload --database databaseName --container containerName --url 
https://contoso.com/path/to/data.json
```
```
Documents successfully uploaded to ContainerName
Total number of documents imported : 2654
Total time taken : 00:00:38.1228087 hours
Total RUs consumed : 25022.58
```
Met de uitvoer statistieken kunt u de daad werkelijke RU/s berekenen die worden gebruikt voor het uploaden van de items. Als 25.000 RUs bijvoorbeeld meer dan 38 seconden is verbruikt, is de daad werkelijke RU/s 25.000 RUs/38 seconden = 658 RU/s.

U kunt bestanden (zoals CSV-of JSON-bestanden) opslaan in de lokale notebook-werk ruimte. We raden u aan een cel in uw notitie blok toe te voegen om bestanden op te slaan. U kunt deze bestanden weer geven vanuit de geïntegreerde terminal in de notebook-omgeving. U kunt de opdracht ' ls ' gebruiken om de opgeslagen bestanden weer te geven. Deze bestanden worden echter verwijderd als u de werk ruimte opnieuw instelt. Het is daarom het beste om permanente opslag te gebruiken, zoals GitHub of een opslag account in plaats van de lokale werk ruimte.

## <a name="run-another-notebook-in-current-notebook"></a>Een ander notitie blok in het huidige notitie blok uitvoeren 
U kunt de ``%%run`` opdracht Magic gebruiken om een ander notitie blok in uw werk ruimte uit te voeren vanuit uw huidige notitie blok. Gebruik de syntaxis:

```python
%%run ./path/to/{notebookName}.ipynb
```
Vervang door ``{notebookName}`` de naam van het notitie blok dat u wilt uitvoeren. Het notitie blok moet zich in de huidige werk ruimte mijn notitie blokken bevinden. 

## <a name="use-built-in-nteract-data-explorer"></a>Ingebouwde nteract Data Explorer gebruiken
U kunt de ingebouwde [nteract Data Explorer](https://blog.nteract.io/designing-the-nteract-data-explorer-f4476d53f897) gebruiken om een data frame te filteren en te visualiseren. Als u deze functie wilt inschakelen, stelt u de optie ``pd.options.display.html.table_schema`` in op ``True`` ``pd.options.display.max_rows`` de gewenste waarde (u kunt instellen op ``pd.options.display.max_rows`` ``None`` om alle resultaten weer te geven).

```python
import pandas as pd
pd.options.display.html.table_schema = True
pd.options.display.max_rows = None

df_cosmos.groupby("Item").size()
```
:::image type="content" source="media/use-notebook-features-and-commands/nteract-built-in-chart.png" alt-text="nteract Data Explorer":::

## <a name="use-the-built-in-python-sdk"></a>De ingebouwde python-SDK gebruiken
Versie 4 van de [Azure Cosmos DB PYTHON SDK voor SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos) is geïnstalleerd en opgenomen in de notebook-omgeving voor het Azure Cosmos-account.

Gebruik het ingebouwde ``cosmos_client`` exemplaar om een SDK-bewerking uit te voeren. 

Bijvoorbeeld:

```python
## Import modules as needed
from azure.cosmos.partition_key import PartitionKey

## Create a new database if it doesn't exist
database = cosmos_client.create_database_if_not_exists('RetailDemo')

## Create a new container if it doesn't exist
container = database.create_container_if_not_exists(id='WebsiteData', partition_key=PartitionKey(path='/CartID'))
```
Zie [PYTHON SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos/samples)-voor beelden. 

> [!IMPORTANT]
> De ingebouwde python-SDK wordt alleen ondersteund voor SQL-(core) API-accounts. Voor andere Api's moet u [het relevante python-stuur programma installeren](#install-a-new-package) dat overeenkomt met de API. 

## <a name="create-a-custom-instance-of-cosmos_client"></a>Een aangepast exemplaar van maken ``cosmos_client``
Voor meer flexibiliteit kunt u een aangepast exemplaar van maken ``cosmos_client`` om het volgende te doen:

- Het [verbindings beleid](/python/api/azure-cosmos/azure.cosmos.documents.connectionpolicy?preserve-view=true&view=azure-python-preview) aanpassen
- Voer bewerkingen uit voor een ander Azure Cosmos-account dan dat waarin u zich bevindt

U kunt toegang krijgen tot de connection string en de primaire sleutel van het huidige account via de [omgevings variabelen](#access-the-account-endpoint-and-primary-key-env-variables). 

```python
import azure.cosmos.cosmos_client as cosmos
import azure.cosmos.documents as documents

# These should be set to a region you've added for Azure Cosmos DB
region_1 = "Central US" 
region_2 = "East US 2"

custom_connection_policy = documents.ConnectionPolicy()
custom_connection_policy.PreferredLocations = [region_1, region_2] # Set the order of regions the SDK will route requests to. The regions should be regions you've added for Cosmos, otherwise this will error.

# Create a new instance of CosmosClient, getting the endpoint and key from the environment variables
custom_client = cosmos.CosmosClient(url=COSMOS.ENDPOINT, credential=COSMOS.KEY, connection_policy=custom_connection_policy)

```
## <a name="access-the-account-endpoint-and-primary-key-env-variables"></a>Toegang tot het account eindpunt en de primaire-sleutel env-variabelen
U kunt toegang krijgen tot het ingebouwde eind punt en de sleutel van het account waarmee uw notebook zich bevindt.

```python
endpoint = COSMOS.ENDPOINT
primary_key = COSMOS.KEY
```
> [!IMPORTANT]
> De ``COSMOS.ENDPOINT`` ``COSMOS.KEY`` variabelen en zijn alleen van toepassing op de SQL-API. Voor andere Api's zoekt u het eind punt en de sleutel in de **verbindings reeksen** of de Blade **sleutels** in uw Azure Cosmos-account.  

## <a name="reset-notebooks-workspace"></a>Werk ruimte notitie blokken opnieuw instellen
Als u de werk ruimte notitie blokken opnieuw wilt instellen op de standaard instellingen, selecteert u **werk ruimte opnieuw instellen** op de opdracht balk. Hiermee worden alle aangepaste geïnstalleerde pakketten verwijderd en wordt de Jupyter-server opnieuw gestart. Uw notitie blokken, bestanden en Azure Cosmos-resources worden niet beïnvloed.  

:::image type="content" source="media/use-notebook-features-and-commands/reset-workspace.png" alt-text="Werk ruimte notitie blokken opnieuw instellen":::

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over de voor delen van [Azure Cosmos DB Jupyter-notebooks](cosmosdb-jupyter-notebooks.md)
- Meer informatie over de [Azure Cosmos DB PYTHON SDK voor SQL API](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/cosmos/azure-cosmos)