---
title: Toegang tot gegevens in Jupyter-notebooks-Azure Notebooks preview
description: Meer informatie over toegang tot bestanden, REST-Api's, data bases en andere Azure Storage resources van een Jupyter-notebook.
ms.topic: how-to
ms.date: 12/04/2018
ms.custom: devx-track-python
ms.openlocfilehash: a833ff914c1ee53f024147371977ac1caa3800dc
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94842869"
---
# <a name="access-cloud-data-in-a-notebook"></a>Toegang tot cloudgegevens in een notebook

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Voor het uitvoeren van interessante werkzaamheden in een Jupyter-notebook zijn gegevens vereist. Gegevens zijn inderdaad de lifeblood van notebooks.

U kunt [gegevens bestanden zeker importeren in een project](work-with-project-data-files.md), zelfs met opdrachten zoals `curl` in een notitie blok, om een bestand rechtstreeks te downloaden. Het is echter waarschijnlijk dat u veel meer uitgebreide gegevens moet gebruiken die beschikbaar zijn vanuit niet-bestands bronnen, zoals REST Api's, relationele data bases en Cloud opslag, zoals Azure-tabellen.

In dit artikel vindt u een korte beschrijving van deze verschillende opties. Omdat de toegang tot gegevens het beste wordt gezien in actie, kunt u uitvoer bare code vinden in de [Azure notebooks-voor beelden: toegang tot uw gegevens](https://github.com/Microsoft/AzureNotebooks/blob/master/Samples/Access%20your%20data%20in%20Azure%20Notebooks.ipynb).

## <a name="rest-apis"></a>REST-API’s

Over het algemeen is de grote hoeveelheid gegevens die via internet beschikbaar is, toegankelijk via bestanden, maar via REST Api's. Gelukkig, omdat een notebook-cel kan de code bevatten die u wilt, kunt u code gebruiken om aanvragen te verzenden en JSON-gegevens te ontvangen. U kunt die JSON vervolgens converteren naar de indeling die u wilt gebruiken, zoals een Panda data frame.

Als u toegang wilt krijgen tot gegevens met behulp van een REST API, gebruikt u dezelfde code in de code cellen van een notitie blok die u in een andere toepassing gebruikt. De algemene structuur voor het gebruik van de bibliotheek aanvragen is als volgt:

```python
import pandas
import requests

# New York City taxi data for 2014
data_url = 'https://data.cityofnewyork.us/resource/gkne-dk5s.json'

# General data request; include other API keys and credentials as needed in the data argument
response = requests.get(data_url, data={"limit": "20"})

if response.status_code == 200:
    dataframe_rest2 = pandas.DataFrame.from_records(response.json())
    print(dataframe_rest2)
```

## <a name="azure-sql-database-and-sql-managed-instance"></a>Azure SQL Database en SQL Managed instance

U kunt toegang krijgen tot data bases in SQL Database of SQL Managed instance met behulp van de pyodbc-of pymssql-bibliotheken.

[Met python kunt u een query uitvoeren op een Azure-SQL database](../azure-sql/database/connect-query-python.md) geeft u instructies voor het maken van een data base in SQL database met AdventureWorks-gegevens en ziet u hoe u een query op die gegevens uitvoert. Dezelfde code wordt weer gegeven in de voorbeeld notitieblok voor dit artikel.

## <a name="azure-storage"></a>Azure Storage

Azure Storage biedt verschillende soorten niet-relationele opslag, afhankelijk van het type gegevens dat u hebt en hoe u toegang hebt tot het bestand:

- Table Storage: biedt een goedkope, high-volume opslag voor tabellaire gegevens, zoals verzamelde sensor logboeken, Diagnostische logboeken enzovoort.
- Blob-opslag: biedt bestands-achtige opslag voor elk type gegevens.

In het voor beeld-notebook wordt gedemonstreerd hoe u met zowel tabellen als blobs werkt, met inbegrip van het gebruik van een Shared Access Signature om alleen-lezen toegang tot blobs toe te staan.

## <a name="azure-cosmos-db"></a>Azure Cosmos DB

Azure Cosmos DB biedt een volledig geïndexeerd NoSQL-Archief voor JSON-documenten). De volgende artikelen bieden een aantal verschillende manieren om te werken met Cosmos DB van python:

- [Een SQL API-app bouwen met python](../cosmos-db/create-sql-api-python.md)
- [Bouw een kolf-app met de API van het Azure Cosmos DB voor MongoDB](../cosmos-db/create-mongodb-flask.md)
- [Een grafiek database maken met behulp van python en de Gremlin-API](../cosmos-db/create-graph-python.md)
- [Een Cassandra-app bouwen met python en Azure Cosmos DB](../cosmos-db/create-cassandra-python.md)
- [Een Table-API-app bouwen met python en Azure Cosmos DB](../cosmos-db/table-storage-how-to-use-python.md)

Wanneer u met Cosmos DB werkt, kunt u de [Azure-cosmosdb-tabel](https://pypi.org/project/azure-cosmosdb-table/) bibliotheek gebruiken.

## <a name="other-azure-databases"></a>Andere Azure-data bases

Azure biedt een aantal andere database typen die u kunt gebruiken. De onderstaande artikelen bevatten richt lijnen voor het openen van deze data bases van python:

- [Azure Database voor PostgreSQL: Python gebruiken om verbinding te maken en gegevens op te vragen](../postgresql/connect-python.md)
- [Snelstart: Azure Redis Cache gebruiken met Python](../azure-cache-for-redis/cache-python-get-started.md)
- [Azure Database voor MySQL: Python gebruiken om verbinding te maken en gegevens op te vragen](../mysql/connect-python.md)
- [Azure Data Factory](https://azure.microsoft.com/services/data-factory/)
  - [Wizard kopiëren voor Azure Data Factory](https://azure.microsoft.com/updates/code-free-copy-wizard-for-azure-data-factory/)

## <a name="next-steps"></a>Volgende stappen

- [Procedure: werken met Project-gegevens bestanden](work-with-project-data-files.md)