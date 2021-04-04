---
title: Functies maken in SQL Server met behulp van SQL en python-team data Science process
description: Genereer functies voor gegevens die zijn opgeslagen in een SQL Server VM op Azure met behulp van SQL en python-onderdeel van het proces voor gegevens wetenschap van teams.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 3c20bf1c5856276c4c7ee0e37ed4ef2120d1d93d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "93322036"
---
# <a name="create-features-for-data-in-sql-server-using-sql-and-python"></a>Met SQL en Python functies maken voor gegevens in SQL Server
In dit document wordt beschreven hoe u functies kunt genereren voor gegevens die zijn opgeslagen in een SQL Server-VM op Azure, waardoor algoritmen efficiënter kunnen worden geleerd vanuit de gegevens. U kunt SQL of een programmeer taal zoals python gebruiken om deze taak uit te voeren. Beide benaderingen worden hier toegelicht.

Deze taak is een stap in het [team data Science process (TDSP)](./index.yml).

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) raadplegen en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

## <a name="prerequisites"></a>Vereisten
In dit artikel wordt ervan uitgegaan dat u het volgende hebt:

* Een Azure-opslag account gemaakt. Als u instructies nodig hebt, raadpleegt u [een Azure Storage account maken](../../storage/common/storage-account-create.md)
* Uw gegevens zijn opgeslagen in SQL Server. Als dat niet het geval is, raadpleegt u [gegevens verplaatsen naar een Azure SQL database voor Azure machine learning](move-sql-azure.md) voor instructies over het verplaatsen van de gegevens.

## <a name="feature-generation-with-sql"></a><a name="sql-featuregen"></a>Onderdelen genereren met SQL
In deze sectie wordt beschreven hoe u functies kunt genereren met behulp van SQL:  

* [Telling op basis van onderdelen genereren](#sql-countfeature)
* [Binning-functie generatie](#sql-binningfeature)
* [De functies uit één kolom implementeren](#sql-featurerollout)

> [!NOTE]
> Wanneer u aanvullende functies hebt gegenereerd, kunt u deze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en de primaire sleutel, die kunnen worden gekoppeld aan de oorspronkelijke tabel.
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Telling op basis van onderdelen genereren
In dit document ziet u twee manieren voor het genereren van functies voor tellingen. De eerste methode maakt gebruik van Voorwaardelijke som en de tweede methode gebruikt de component WHERE. Deze nieuwe functies kunnen vervolgens worden gekoppeld aan de oorspronkelijke tabel (met behulp van primaire-sleutel kolommen) om onderdelen naast de oorspronkelijke gegevens te tellen.

```sql
select <column_name1>,<column_name2>,<column_name3>, COUNT(*) as Count_Features from <tablename> group by <column_name1>,<column_name2>,<column_name3>

select <column_name1>,<column_name2> , sum(1) as Count_Features from <tablename>
where <column_name3> = '<some_value>' group by <column_name1>,<column_name2>
```

### <a name="binning-feature-generation"></a><a name="sql-binningfeature"></a>Binning-functie generatie
In het volgende voor beeld ziet u hoe u binning-functies genereert door binning (met behulp van vijf opslag locaties) een numerieke kolom die als functie kan worden gebruikt:

```sql
SELECT <column_name>, NTILE(5) OVER (ORDER BY <column_name>) AS BinNumber from <tablename>
```


### <a name="rolling-out-the-features-from-a-single-column"></a><a name="sql-featurerollout"></a>De functies uit één kolom implementeren
In deze sectie wordt gedemonstreerd hoe u één kolom in een tabel kunt samen stellen om extra functies te genereren. In het voor beeld wordt ervan uitgegaan dat er een kolom breedte graad of lengte graad is in de tabel van waaruit u functies wilt genereren.

Hier volgt een korte primer op de locatie gegevens voor de breedte graad/lengte graad (vanaf stack overflow `https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude` ). Hier volgen enkele nuttige zaken die u moet begrijpen over locatie gegevens voordat u functies van het veld maakt:

* Het teken geeft aan of we Noord of Zuid, Oost of West zijn op de wereld.
* Een niet-nul honderd cijfers duidt de lengte graad aan, maar er wordt geen breedte graad gebruikt.
* Het tien tallen cijfer geeft een positie aan van ongeveer 1.000 kilo meter. Het biedt nuttige informatie over de betreffende continent of Oceaan.
* Het aantal eenheden (één decimale graad) geeft een positie tot 111 kilo meter (60 zeemijl, ongeveer 69 kilo meters). Het geeft, ongeveer, in welke staat of welk land of welke regio we zich bevinden.
* De eerste decimaal positie is Maxi maal 11,1 km: deze kan de positie van één grote plaats onderscheiden van een naburige grote stad.
* De tweede decimaal positie is Maxi maal 1,1 km: er kan één Village van elkaar worden gescheiden.
* De derde decimale plaats is 110 m: het kan een groot agrarisch veld of institutionele campus identificeren.
* Het vierde decimaal punt is Maxi maal 11 m: het kan een perceel land identificeren. Het is vergelijkbaar met de gebruikelijke nauw keurigheid van een niet-gecorrigeerde GPS-eenheid zonder interferentie.
* De vijfde decimaal heeft een waarde van Maxi maal 1,1 m: het onderscheidt bomen van elkaar. Nauw keurigheid van dit niveau met commerciële GPS-eenheden kan alleen worden behaald met differentiële correctie.
* De zesde decimaal positie is Maxi maal 0,11 m: u kunt dit niveau gebruiken voor het indelen van structuren, voor het ontwerpen van landschappen, het bouwen van wegen. Het moet meer dan goed genoeg zijn voor het volgen van de bewegingen van Glaciers en rivieren. Dit doel kan worden bereikt door Painstaking-maat regelen te nemen met GPS, zoals een differentieeel gecorrigeerde GPS.

De locatie-informatie kan worden featurized door de informatie over de regio, locatie en plaats te scheiden. Eenmaal kan ook een REST-eind punt aanroepen, zoals de Bing Maps-API (Zie `https://msdn.microsoft.com/library/ff701710.aspx` de informatie over de regio/district ophalen).

```sql
select
    <location_columnname>
    ,round(<location_columnname>,0) as l1        
    ,l2=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 1 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),1,1) else '0' end     
    ,l3=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 2 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),2,1) else '0' end     
    ,l4=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 3 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),3,1) else '0' end     
    ,l5=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 4 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),4,1) else '0' end     
    ,l6=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 5 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),5,1) else '0' end     
    ,l7=case when LEN (PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1)) >= 6 then substring(PARSENAME(round(ABS(<location_columnname>) - FLOOR(ABS(<location_columnname>)),6),1),6,1) else '0' end     
from <tablename>
```

Deze op locatie gebaseerde functies kunnen verder worden gebruikt voor het genereren van extra aantal functies, zoals eerder beschreven.

> [!TIP]
> U kunt de records programmatisch invoegen met de taal van uw keuze. Mogelijk moet u de gegevens in segmenten invoegen om de schrijf efficiëntie te verbeteren. [Hier volgt een voor beeld van hoe u dit kunt doen met behulp van pyodbc](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python).
> Een andere mogelijkheid is om gegevens in de data base in te voegen met het [hulp programma BCP](/sql/tools/bcp-utility)
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De zojuist gegenereerde functie kan worden toegevoegd als een kolom aan een bestaande tabel of worden opgeslagen in een nieuwe tabel en gekoppeld aan de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt met behulp van de module [gegevens importeren](/azure/machine-learning/studio-module-reference/import-data) in azure ml, zoals hieronder wordt weer gegeven:

![Azure ML-lezers](./media/sql-server-virtual-machine/reader_db_featurizedinput.png)

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Een programmeer taal zoals python gebruiken
Python gebruiken om functies te genereren wanneer de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure-Blob met python. Zie [Azure Blob-gegevens in uw data Science-omgeving verwerken voor een](data-blob.md)vergelijking. Laad de gegevens uit de data base in een Pandas-gegevens frame om deze verder te verwerken. Het proces voor het maken van verbinding met de data base en het laden van de gegevens in het gegevens frame wordt beschreven in deze sectie.

De volgende connection string indeling kan worden gebruikt om vanuit python verbinding te maken met een SQL Server-Data Base met behulp van pyodbc (Vervang servername, dbname, username en password door uw specifieke waarden):

```python
#Set up the SQL Azure connection
import pyodbc
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. Met de volgende code worden de resultaten van een SQL Server data base in een Pandas-gegevens frame gelezen:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Nu kunt u samen werken met het gegevens frame van de Panda-Data, zoals beschreven in onderwerpen, [functies maken voor Azure Blob Storage-gegevens met behulp van Panda](./explore-data-blob.md).