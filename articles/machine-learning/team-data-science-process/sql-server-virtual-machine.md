---
title: Gegevens verkennen in een SQL Server virtuele machine-team data Science process
description: Verken + proces gegevens en Genereer functies met behulp van python of SQL in een SQL Server virtuele machine in Azure.
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
ms.openlocfilehash: 8be878cf40967356d68e9be0765e898c81b5ba0a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93314619"
---
# <a name="process-data-in-sql-server-virtual-machine-on-azure"></a><a name="heading"></a>Gegevens verwerken in virtuele SQL Server-machine in Azure
In dit document wordt beschreven hoe u gegevens kunt verkennen en functies kunt genereren voor gegevens die zijn opgeslagen in een SQL Server VM in Azure. Dit doel kan worden voltooid door gegevens wrangling met behulp van SQL of door gebruik te maken van een programmeer taal zoals python.

> [!NOTE]
> In de SQL-voorbeeld instructies in dit document wordt ervan uitgegaan dat de gegevens zich in SQL Server bevindt. Als dat niet het geval is, raadpleegt u de Cloud data Science process-toewijzing voor informatie over het verplaatsen van uw gegevens naar SQL Server.
> 
> 

## <a name="using-sql"></a><a name="SQL"></a>SQL gebruiken
De volgende gegevens wrangling-taken in deze sectie worden beschreven met behulp van SQL:

1. [Gegevens verkennen](#sql-dataexploration)
2. [Onderdelen genereren](#sql-featuregen)

### <a name="data-exploration"></a><a name="sql-dataexploration"></a>Gegevens verkennen
Hier volgen enkele voor beelden van SQL-scripts die kunnen worden gebruikt voor het verkennen van gegevens archieven in SQL Server.

> [!NOTE]
> Voor een praktijk voorbeeld kunt u de [NYC taxi-gegevensset](https://www.andresmh.com/nyctaxitrips/) gebruiken en verwijzen naar de IPNB getiteld [NYC data wrangling met behulp van IPython notebook en SQL Server](https://github.com/Azure/Azure-MachineLearning-DataScience/blob/master/Misc/DataScienceProcess/iPythonNotebooks/machine-Learning-data-science-process-sql-walkthrough.ipynb) voor een end-to-end-instructies.
> 
> 

1. Het aantal observaties per dag ophalen
   
    `SELECT CONVERT(date, <date_columnname>) as date, count(*) as c from <tablename> group by CONVERT(date, <date_columnname>)` 
2. De niveaus in een kolom categorische ophalen
   
    `select  distinct <column_name> from <databasename>`
3. Het aantal niveaus in combi natie van twee categorische kolommen ophalen 
   
    `select <column_a>, <column_b>,count(*) from <tablename> group by <column_a>, <column_b>`
4. De distributie voor numerieke kolommen ophalen
   
    `select <column_name>, count(*) from <tablename> group by <column_name>`

### <a name="feature-generation"></a><a name="sql-featuregen"></a>Onderdelen genereren
In deze sectie wordt beschreven hoe u functies kunt genereren met behulp van SQL:  

1. [Telling op basis van onderdelen genereren](#sql-countfeature)
2. [Binning-functie generatie](#sql-binningfeature)
3. [De functies uit één kolom implementeren](#sql-featurerollout)

> [!NOTE]
> Wanneer u aanvullende functies hebt gegenereerd, kunt u deze als kolommen toevoegen aan de bestaande tabel of een nieuwe tabel maken met de extra functies en de primaire sleutel, die kunnen worden gekoppeld aan de oorspronkelijke tabel. 
> 
> 

### <a name="count-based-feature-generation"></a><a name="sql-countfeature"></a>Telling op basis van onderdelen genereren
In de volgende voor beelden ziet u twee manieren voor het genereren van aantal functies. De eerste methode maakt gebruik van Voorwaardelijke som en de tweede methode gebruikt de component WHERE. Deze resultaten kunnen vervolgens worden samengevoegd met de oorspronkelijke tabel (met behulp van primaire-sleutel kolommen), zodat het aantal functies naast de oorspronkelijke gegevens wordt weer gegeven.

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

Hier volgt een korte beschrijving van de locatie gegevens voor de breedte graad/lengte graad (vanaf stack overflow [hoe de nauw keurigheid van de breedte graad en lengte graad wordt gemeten?](https://gis.stackexchange.com/questions/8650/how-to-measure-the-accuracy-of-latitude-and-longitude)). Deze richt lijnen zijn handig voor het opnemen van locaties als een of meer functies:

* Het teken geeft aan of er al dan niet Noord of Zuid, Oost of West op de wereld wordt vermeld.
* Een honderd cijfer dat niet gelijk is aan nul, vertelt ons dat er lengte graad wordt gebruikt, niet Latitude!
* Het tien tallen cijfer geeft een positie aan van ongeveer 1.000 kilo meter. Het geeft ons nuttige informatie over het continent of de Oceaan.
* Het aantal eenheden (één decimale graad) geeft een positie tot 111 kilo meter (60 zeemijl, ongeveer 69 kilo meters). U kunt de status, het land of de regio waarin u zich bevindt, melden.
* De eerste decimaal positie is Maxi maal 11,1 km: deze kan de positie van één grote plaats onderscheiden van een naburige grote stad.
* De tweede decimaal positie is Maxi maal 1,1 km: er kan één Village van elkaar worden gescheiden.
* De derde decimale plaats is 110 m: het kan een groot agrarisch veld of institutionele campus identificeren.
* Het vierde decimaal punt is Maxi maal 11 m: het kan een perceel land identificeren. Het is vergelijkbaar met de gebruikelijke nauw keurigheid van een niet-gecorrigeerde GPS-eenheid zonder interferentie.
* De vijfde decimaal heeft een waarde van Maxi maal 1,1 m: het onderscheidt bomen van elkaar. Nauw keurigheid van dit niveau met commerciële GPS-eenheden kan alleen worden behaald met differentiële correctie.
* De zesde decimaal positie is Maxi maal 0,11 m: u kunt dit gebruiken voor het indelen van structuren, voor het ontwerpen van landschappen, het bouwen van wegen. Het moet meer dan goed genoeg zijn voor het volgen van de bewegingen van Glaciers en rivieren. Dit kan worden bereikt door Painstaking-maat regelen te nemen met GPS, zoals een differentieeel gecorrigeerd GPS.

De locatie-informatie kan als volgt worden featurized: het scheiden van de regio, locatie en plaatsgegevens. U kunt ook een REST-eind punt aanroepen, zoals Bing Kaarten-API beschikbaar op [een locatie zoeken op basis van](/bingmaps/rest-services/locations/find-a-location-by-point) de regio/district-informatie ophalen.

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
> U kunt de records programmatisch invoegen met de taal van uw keuze. Mogelijk moet u de gegevens in segmenten invoegen om de schrijf efficiëntie te verbeteren (Zie voor een voor beeld van hoe u dit doet met pyodbc het voor beeld van [een HelloWorld om toegang te krijgen tot sqlserver met python](https://code.google.com/p/pypyodbc/wiki/A_HelloWorld_sample_to_access_mssql_with_python)). Een andere mogelijkheid is om gegevens in de data base in te voegen met behulp van het [bcp-hulp programma](/sql/tools/bcp-utility).
> 
> 

### <a name="connecting-to-azure-machine-learning"></a><a name="sql-aml"></a>Verbinding maken met Azure Machine Learning
De zojuist gegenereerde functie kan worden toegevoegd als een kolom aan een bestaande tabel of worden opgeslagen in een nieuwe tabel en gekoppeld aan de oorspronkelijke tabel voor machine learning. Functies kunnen worden gegenereerd of geopend als deze al zijn gemaakt met behulp van de module [gegevens importeren][import-data] in azure machine learning, zoals hieronder wordt weer gegeven:

![azureml-lezers][1] 

## <a name="using-a-programming-language-like-python"></a><a name="python"></a>Een programmeer taal zoals python gebruiken
Met python voor het verkennen van gegevens en het genereren van functies als de gegevens zich in SQL Server bevinden, is vergelijkbaar met het verwerken van gegevens in Azure-Blob met behulp van python zoals beschreven in [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md). Laad de gegevens uit de data base in een Pandas-gegevens frame voor meer verwerking. We documenteren het proces van het maken van een verbinding met de data base en het laden van de gegevens in het gegevens frame in deze sectie.

De volgende connection string indeling kan worden gebruikt om vanuit python verbinding te maken met een SQL Server-Data Base met behulp van pyodbc (Vervang servername, dbname, username en password door uw specifieke waarden):

```python
#Set up the SQL Azure connection
import pyodbc    
conn = pyodbc.connect('DRIVER={SQL Server};SERVER=<servername>;DATABASE=<dbname>;UID=<username>;PWD=<password>')
```

De [bibliotheek Pandas](https://pandas.pydata.org/) in python biedt een uitgebreide set gegevens structuren en hulpprogram ma's voor gegevens analyse voor het bewerken van gegevens voor python-programmering. De onderstaande code leest de resultaten van een SQL Server data base in een Pandas-gegevens frame:

```python
# Query database and load the returned results in pandas data frame
data_frame = pd.read_sql('''select <columnname1>, <columnname2>... from <tablename>''', conn)
```

Nu kunt u met het frame Pandas-gegevens werken, zoals is beschreven in het artikel [Azure Blob-gegevens in uw data Science-omgeving verwerken](data-blob.md).

## <a name="azure-data-science-in-action-example"></a>Voor beeld van Azure data Science in actie
Zie voor een volledig overzicht van het Azure data Science-proces met behulp van een open bare gegevensset het [Azure data Science-proces in actie](sql-walkthrough.md).

[1]: ./media/sql-server-virtual-machine/reader_db_featurizedinput.png


<!-- Module References -->
[import-data]: /azure/machine-learning/studio-module-reference/import-data