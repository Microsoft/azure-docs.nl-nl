---
title: Gegevens en modellen verkennen in Windows
titleSuffix: Azure Data Science Virtual Machine
description: Voer gegevens verkennen en model leren op de Windows-Data Science Virtual Machine.
services: machine-learning
ms.service: data-science-vm
ms.custom: devx-track-python
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 05/08/2020
ms.openlocfilehash: c44c40f2ddab53cf0beb5ecd48127d126fdb4333
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101657397"
---
# <a name="data-science-with-a-windows-data-science-virtual-machine"></a>Data Wetenschappen met een Windows-Data Science Virtual Machine

De Windows Data Science Virtual Machine (DSVM) is een krachtige ontwikkel omgeving voor data technologie waar u gegevens kunt verkennen en model leren. De omgeving is al gebouwd en gebundeld met verschillende populaire hulpprogram ma's voor gegevens analyse, zodat u snel aan de slag kunt gaan met uw analyse voor on-premises, Cloud-of hybride implementaties. 

De DSVM werkt nauw samen met Azure-Services. Het kan gegevens lezen en verwerken die al zijn opgeslagen in azure, in azure Synapse (voorheen SQL DW), Azure Data Lake, Azure Storage of Azure Cosmos DB. Het kan ook profiteren van andere hulpprogram ma's voor analyse, zoals Azure Machine Learning.

In dit artikel leert u hoe u uw DSVM kunt gebruiken om data Science-taken uit te voeren en te communiceren met andere Azure-Services. Hier volgen enkele dingen die u op de DSVM kunt doen:

- Gebruik een Jupyter Notebook om te experimenteren met uw gegevens in een browser met behulp van python 2, python 3 en micro soft R. (micro soft R is een bedrijfs klare versie van R, ontworpen voor prestaties.)
- Verken gegevens en ontwikkel modellen lokaal op het DSVM met behulp van Microsoft Machine Learning Server en python.
- Beheer uw Azure-resources met behulp van de Azure Portal of Power shell.
- Breid uw opslag ruimte uit en deel gegevens sets/code op grote schaal in uw hele team door een Azure Files share te maken als een koppelbaar station op uw DSVM.
- Deel code met uw team met behulp van GitHub. Toegang tot uw opslag plaats met behulp van de vooraf geïnstalleerde Git-clients: Git Bash en git GUI.
- Toegang tot Azure data-en Analytics-Services zoals Azure Blob Storage, Azure Cosmos DB, Azure Synapse (voorheen SQL DW) en Azure SQL Database.
- Bouw rapporten en een dash board met behulp van de Power BI Desktop-instantie die vooraf is geïnstalleerd op de DSVM en implementeer deze in de Cloud.

- Installeer extra hulpprogram ma's op uw virtuele machine.   

> [!NOTE]
> Er gelden extra gebruiks kosten voor veel van de services voor gegevens opslag en analyse die in dit artikel worden vermeld. Zie de pagina met [prijzen voor Azure](https://azure.microsoft.com/pricing/) voor meer informatie.
> 
> 

## <a name="prerequisites"></a>Vereisten

* U hebt een Azure-abonnement nodig. U kunt [zich registreren voor een gratis proef versie](https://azure.microsoft.com/free/).
* Instructies voor het inrichten van een Data Science Virtual Machine op de Azure Portal zijn beschikbaar voor het [maken van een virtuele machine](https://portal.azure.com/#create/microsoft-dsvm.dsvm-windowsserver-2016).


[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]


## <a name="use-jupyter-notebooks"></a>Jupyter-notebooks gebruiken
De Jupyter Notebook biedt een op een browser gebaseerde IDE voor het verkennen en model leren van gegevens. U kunt python 2, python 3 of R (zowel open source als Microsoft R Server) gebruiken in een Jupyter Notebook.

Als u de Jupyter Notebook wilt starten, selecteert u het pictogram **Jupyter notebook** in het menu **Start** of op het bureau blad. In de DSVM-opdracht prompt kunt u ook de opdracht uitvoeren ```jupyter notebook``` vanuit de directory waarin u bestaande notitie blokken hebt of waar u nieuwe notitie blokken wilt maken.  

Nadat u Jupyter hebt gestart, gaat u naar de `/notebooks` map voor bijvoorbeeld notebooks die vooraf zijn verpakt in de DSVM. U kunt nu:

* Selecteer het notitie blok om de code te zien.
* Voer elke cel uit door SHIFT + ENTER te selecteren.
* Voer het hele notitie blok uit door de **cel**  >  **Run** te selecteren.
* Maak een nieuw notitie blok door het Jupyter-pictogram (linkerbovenhoek) te selecteren, de knop **Nieuw** aan de rechter kant te selecteren en vervolgens de taal voor het notitie blok te kiezen (ook wel kernels genoemd).   

> [!NOTE]
> Momenteel worden python 2,7-, python 3,6-, R-, Julia-en PySpark-kernels in Jupyter ondersteund. De R-kernel ondersteunt programmering in open source R en micro soft R.   
> 
> 

Wanneer u zich in het notitie blok bevindt, kunt u uw gegevens verkennen, het model bouwen en het model testen met behulp van uw favoriete bibliotheken.

## <a name="explore-data-and-develop-models-with-microsoft-machine-learning-server"></a>Gegevens verkennen en modellen ontwikkelen met Microsoft Machine Learning Server
U kunt talen als R en python gebruiken om uw gegevens analyse rechtstreeks op de DSVM uit te voeren.

Voor R kunt u een IDE gebruiken zoals RStudio die u kunt vinden in het menu Start of op het bureau blad. U kunt ook R-Hulpprogram Ma's voor Visual Studio gebruiken. Micro soft heeft extra bibliotheken op de open source-KRANs R geboden om schaal bare analyses mogelijk te maken en de mogelijkheid om gegevens te analyseren die groter zijn dan de geheugen grootte die is toegestaan in parallelle gesegmenteerde analyse. 

Voor python kunt u een IDE gebruiken zoals de versie van Visual Studio Community, waarop de uitbrei ding Python Tools for Visual Studio (PTVS) vooraf is geïnstalleerd. Standaard wordt alleen python 3,6, de hoofd-Conda-omgeving, geconfigureerd op PTVS. Voer de volgende stappen uit om Anaconda python 2,7 in te scha kelen:

1. Maak aangepaste omgevingen voor elke versie door naar **hulpprogram ma's**  >  **python tools** python  >  -**omgevingen** te gaan en vervolgens **+ Custom** te selecteren in de versie van Visual Studio community.
1. Geef een beschrijving en stel het pad voor het voor voegsel van de omgeving in als **c:\anaconda\envs\python2** voor anaconda python 2,7.
1. Selecteer **Automatische detectie**  >  **Toep assen** om de omgeving op te slaan.

Raadpleeg de [PTVS-documentatie](/visualstudio/python/) voor meer informatie over het maken van python-omgevingen.

Nu kunt u een nieuw python-project maken. Ga naar **bestand**  >  **Nieuw**  >  **project**  >  **python** en selecteer het type python-toepassing dat u wilt maken. U kunt de python-omgeving voor het huidige project instellen op de gewenste versie (python 2,7 of 3,6) door met de rechter muisknop te klikken op **python-omgevingen** en vervolgens **python-omgevingen toevoegen/verwijderen** te selecteren. Meer informatie over het werken met PTVS vindt u in de [product documentatie](/visualstudio/python/).



## <a name="manage-azure-resources"></a>Azure-resources beheren
Met de DSVM kunt u uw analyse oplossing niet lokaal op de virtuele machine maken. U kunt hiermee ook toegang krijgen tot services op het Azure-Cloud platform. Azure biedt verschillende reken-, opslag-, gegevens analyse-en andere services die u kunt beheren en gebruiken vanuit uw DSVM.

Als u uw Azure-abonnement en cloud resources wilt beheren, hebt u twee opties:
+ Gebruik uw browser en ga naar de [Azure Portal](https://portal.azure.com).

+ Gebruik Power shell-scripts. Voer Azure PowerShell uit vanuit een snelkoppeling op het bureau blad of vanuit het menu **Start** . Raadpleeg de [Microsoft Azure PowerShell-documentatie](../../azure-resource-manager/management/manage-resources-powershell.md) voor volledige informatie. 

## <a name="extend-storage-by-using-shared-file-systems"></a>Opslag uitbreiden met behulp van gedeelde bestands systemen
Gegevens wetenschappers kunnen grote gegevens sets, code of andere resources binnen het team delen. De DSVM heeft ongeveer 45 GB beschik bare ruimte. Als u uw opslag wilt uitbreiden, kunt u Azure Files gebruiken en deze koppelen aan een of meer DSVM-instanties of toegang krijgen via een REST API. U kunt ook de [Azure Portal](../../virtual-machines/windows/attach-managed-disk-portal.md) gebruiken of [Azure PowerShell](../../virtual-machines/windows/attach-disk-ps.md) gebruiken om extra toegewezen gegevens schijven toe te voegen. 

> [!NOTE]
> De maximale ruimte op de Azure Files share is 5 TB. De maximale grootte voor elk bestand is 1 TB. 

U kunt dit script in Azure PowerShell gebruiken om een Azure Files share te maken:

```powershell
# Authenticate to Azure.
Connect-AzAccount
# Select your subscription
Get-AzSubscription –SubscriptionName "<your subscription name>" | Select-AzSubscription
# Create a new resource group.
New-AzResourceGroup -Name <dsvmdatarg>
# Create a new storage account. You can reuse existing storage account if you want.
New-AzStorageAccount -Name <mydatadisk> -ResourceGroupName <dsvmdatarg> -Location "<Azure Data Center Name For eg. South Central US>" -Type "Standard_LRS"
# Set your current working storage account
Set-AzCurrentStorageAccount –ResourceGroupName "<dsvmdatarg>" –StorageAccountName <mydatadisk>

# Create an Azure Files share
$s = New-AzStorageShare <<teamsharename>>
# Create a directory under the file share. You can give it any name
New-AzStorageDirectory -Share $s -Path <directory name>
# List the share to confirm that everything worked
Get-AzStorageFile -Share $s
```

Nu u een Azure Files share hebt gemaakt, kunt u deze koppelen aan een virtuele machine in Azure. We raden aan dat u de virtuele machine in hetzelfde Azure-Data Center als het opslag account plaatst om latentie en kosten voor gegevens overdracht te voor komen. Hier volgen de Azure PowerShell-opdrachten voor het koppelen van het station op de DSVM:

```powershell
# Get the storage key of the storage account that has the Azure Files share from the Azure portal. Store it securely on the VM to avoid being prompted in the next command.
cmdkey /add:<<mydatadisk>>.file.core.windows.net /user:<<mydatadisk>> /pass:<storage key>

# Mount the Azure Files share as drive Z on the VM. You can choose another drive letter if you want.
net use z:  \\<mydatadisk>.file.core.windows.net\<<teamsharename>>
```

U hebt nu toegang tot dit station, net zoals bij een normaal station op de virtuele machine.

## <a name="share-code-in-github"></a>Code delen in GitHub
GitHub is een code opslagplaats waar u code voorbeelden en bronnen voor diverse hulp middelen kunt vinden met behulp van technologieën die worden gedeeld door de ontwikkelaars community. Het gebruikt Git als technologie om versies van de code bestanden bij te houden en op te slaan. GitHub is ook een platform waar u uw eigen opslag plaats kunt maken om de gedeelde code en documentatie van uw team op te slaan, versie beheer te implementeren en te bepalen wie toegang heeft tot het weer geven en bijdragen van code. 

Ga naar de [Help-pagina's van github](https://help.github.com/) voor meer informatie over het gebruik van Git. U kunt GitHub gebruiken als een van de manieren om samen te werken met uw team, code te gebruiken die is ontwikkeld door de community en de code naar de community te bijdragen.

De DSVM wordt geladen met client hulpprogramma's op de opdracht regel en in de gebruikers interface om toegang te krijgen tot de GitHub-opslag plaats. Het opdracht regel programma voor het werken met Git en GitHub heet Git Bash. Visual Studio is geïnstalleerd op de DSVM en heeft de Git-extensies. U vindt pictogrammen voor deze hulpprogram ma's in het menu **Start** en op het bureau blad.

Als u code uit een GitHub-opslag plaats wilt downloaden, gebruikt u de ```git clone``` opdracht. Als u bijvoorbeeld de data Science-opslag plaats wilt downloaden die door micro soft in de huidige map is gepubliceerd, kunt u de volgende opdracht uitvoeren in Git Bash:

```bash
git clone https://github.com/Azure/DataScienceVM.git
```

In Visual Studio kunt u dezelfde kloon bewerking uitvoeren. In de volgende scherm afbeelding ziet u hoe u de hulpprogram ma's Git en GitHub opent in Visual Studio:

![Scherm opname van Visual Studio waarbij de GitHub-verbinding wordt weer gegeven](./media/vm-do-ten-things/VSGit.png)

U vindt meer informatie over het gebruik van Git om te werken met uw GitHub-opslag plaats van resources die beschikbaar zijn op github.com. Het [Cheat-blad](https://services.github.com/on-demand/downloads/github-git-cheat-sheet.pdf) is een nuttige verwijzing.

## <a name="access-azure-data-and-analytics-services"></a>Toegang tot Azure data en Analytics Services
### <a name="azure-blob-storage"></a>Azure Blob Storage
Azure Blob-opslag is een betrouw bare, voordelige service voor Cloud opslag voor gegevens groot en klein. In deze sectie wordt beschreven hoe u gegevens kunt verplaatsen naar Blob-opslag en hoe u toegang hebt tot gegevens die zijn opgeslagen in een Azure-Blob.

#### <a name="prerequisites"></a>Vereisten

* Maak uw Azure Blob-opslag account vanuit het [Azure Portal](https://portal.azure.com).

   ![Scherm opname van het proces voor het maken van het opslag account in de Azure Portal](./media/vm-do-ten-things/create-azure-blob.png)

* Controleer of het opdracht regel programma AzCopy vooraf is geïnstalleerd: ```C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy.exe``` . De map met azcopy.exe bevindt zich al in uw omgevings variabele PATH. u kunt dus voor komen dat u het volledige pad naar de opdracht typt wanneer u dit hulp programma uitvoert. Zie de [AzCopy-documentatie](../../storage/common/storage-use-azcopy-v10.md)voor meer informatie over het hulp programma AzCopy.
* Start het Azure Storage Explorer-hulp programma. U kunt deze downloaden van de  [Storage Explorer webpagina](https://storageexplorer.com/). 

   ![Scherm afbeelding van Azure Storage Explorer toegang tot een opslag account](./media/vm-do-ten-things/AzureStorageExplorer_v4.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azcopy"></a>Gegevens verplaatsen van een virtuele machine naar een Azure-Blob: AzCopy

Als u gegevens wilt verplaatsen tussen uw lokale bestanden en Blob-opslag, kunt u AzCopy gebruiken op de opdracht regel of in Power shell:

```powershell
AzCopy /Source:C:\myfolder /Dest:https://<mystorageaccount>.blob.core.windows.net/<mycontainer> /DestKey:<storage account key> /Pattern:abc.txt
```

Vervang **C:\myfolder** door het pad waar het bestand is opgeslagen, **mystorageaccount** met de naam van het Blob Storage-account, **mycontainer** met de container naam en de sleutel van het **opslag account** met de toegangs sleutel voor de Blob-opslag. U kunt de referenties van uw opslag account vinden in de [Azure Portal](https://portal.azure.com).

Voer de opdracht AzCopy uit in Power shell of vanaf een opdracht prompt. Hier volgt een voor beeld van het gebruik van de AzCopy-opdracht:

```powershell
# Copy *.sql from a local machine to an Azure blob
"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Source:"c:\Aaqs\Data Science Scripts" /Dest:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /DestKey:[ENTER STORAGE KEY] /S /Pattern:*.sql

# Copy back all files from an Azure blob container to a local machine

"C:\Program Files (x86)\Microsoft SDKs\Azure\AzCopy\azcopy" /Dest:"c:\Aaqs\Data Science Scripts\temp" /Source:https://[ENTER STORAGE ACCOUNT].blob.core.windows.net/[ENTER CONTAINER] /SourceKey:[ENTER STORAGE KEY] /S
```

Nadat u de AzCopy-opdracht hebt uitgevoerd om naar een Azure-Blob te kopiëren, wordt uw bestand weer gegeven in Azure Storage Explorer.

![Scherm afbeelding van het opslag account waarin het geüploade CSV-bestand wordt weer gegeven](./media/vm-do-ten-things/AzCopy_run_finshed_Storage_Explorer_v3.png)

#### <a name="move-data-from-a-vm-to-an-azure-blob-azure-storage-explorer"></a>Gegevens verplaatsen van een virtuele machine naar een Azure-Blob: Azure Storage Explorer

U kunt ook gegevens uit het lokale bestand in uw virtuele machine uploaden met behulp van Azure Storage Explorer:

* Als u gegevens wilt uploaden naar een container, selecteert u de doel container en selecteert u de knop **uploaden** . ![ Scherm afbeelding van de knop uploaden in Azure Storage Explorer](./media/vm-do-ten-things/storage-accounts.png)
* Selecteer het beletsel teken (**...**) rechts van het vak **bestanden** , selecteer een of meer bestanden die u wilt uploaden uit het bestands systeem en selecteer **uploaden** om te beginnen met het uploaden van de bestanden. ![ Scherm afbeelding van het dialoog venster bestanden uploaden](./media/vm-do-ten-things/upload-files-to-blob.png)

#### <a name="read-data-from-an-azure-blob-python-odbc"></a>Gegevens lezen uit een Azure-Blob: python ODBC

U kunt de BlobService-bibliotheek gebruiken om gegevens rechtstreeks te lezen uit een BLOB in een Jupyter Notebook of in een python-programma.

Importeer eerst de vereiste pakketten:

```python
import pandas as pd
from pandas import Series, DataFrame
import numpy as np
import matplotlib.pyplot as plt
from time import time
import pyodbc
import os
from azure.storage.blob import BlobService
import tables
import time
import zipfile
import random
```

Sluit vervolgens uw referenties voor het Blob Storage-account en lees de gegevens uit de blob:

```python
CONTAINERNAME = 'xxx'
STORAGEACCOUNTNAME = 'xxxx'
STORAGEACCOUNTKEY = 'xxxxxxxxxxxxxxxx'
BLOBNAME = 'nyctaxidataset/nyctaxitrip/trip_data_1.csv'
localfilename = 'trip_data_1.csv'
LOCALDIRECTORY = os.getcwd()
LOCALFILE =  os.path.join(LOCALDIRECTORY, localfilename)

#download from blob
t1 = time.time()
blob_service = BlobService(account_name=STORAGEACCOUNTNAME,account_key=STORAGEACCOUNTKEY)
blob_service.get_blob_to_path(CONTAINERNAME,BLOBNAME,LOCALFILE)
t2 = time.time()
print(("It takes %s seconds to download "+BLOBNAME) % (t2 - t1))

#unzip downloaded files if needed
#with zipfile.ZipFile(ZIPPEDLOCALFILE, "r") as z:
#    z.extractall(LOCALDIRECTORY)

df1 = pd.read_csv(LOCALFILE, header=0)
df1.columns = ['medallion','hack_license','vendor_id','rate_code','store_and_fwd_flag','pickup_datetime','dropoff_datetime','passenger_count','trip_time_in_secs','trip_distance','pickup_longitude','pickup_latitude','dropoff_longitude','dropoff_latitude']
print 'the size of the data is: %d rows and  %d columns' % df1.shape
```

De gegevens worden als een gegevens frame gelezen:

![Scherm afbeelding van de eerste 10 rijen met gegevens](./media/vm-do-ten-things/IPNB_data_readin.png)


### <a name="azure-synapse-analytics-and-databases"></a>Azure Synapse Analytics en data bases
Azure Synapse Analytics is een elastisch data warehouse als een service met een SQL Server ervaring op bedrijfs niveau.

U kunt Azure Synapse Analytics inrichten door de instructies in [dit artikel](../../synapse-analytics/sql-data-warehouse/create-data-warehouse-portal.md)te volgen. Nadat u Azure Synapse Analytics hebt ingericht, kunt u [deze procedure](../team-data-science-process/sqldw-walkthrough.md) gebruiken om gegevens te uploaden, te verkennen en te model leren met behulp van gegevens in azure Synapse Analytics.

#### <a name="azure-cosmos-db"></a>Azure Cosmos DB
Azure Cosmos DB is een NoSQL-data base in de Cloud. U kunt het gebruiken om te werken met documenten zoals JSON, en om de documenten op te slaan en op te vragen.

Gebruik de volgende vereiste stappen om toegang te krijgen tot Azure Cosmos DB van de DSVM:

1. De Azure Cosmos DB python SDK is al geïnstalleerd op de DSVM. Voer ```pip install pydocumentdb --upgrade``` uit vanaf een opdracht prompt om het bij te werken.
2. Maak een Azure Cosmos DB-account en-data base via de [Azure Portal](https://portal.azure.com).
3. Down load het hulp programma voor gegevens migratie van Azure Cosmos DB van het [micro soft Download centrum](https://www.microsoft.com/download/details.aspx?id=53595) en pak het uit naar een gewenste map.
4. Importeer JSON-gegevens (Volcano-gegevens) die zijn opgeslagen in een [open bare BLOB](https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json) in azure Cosmos DB met de volgende opdracht parameters voor het hulp programma voor migratie. (Gebruik dtui.exe uit de map waarin u het hulp programma voor gegevens migratie Azure Cosmos DB hebt geïnstalleerd.) Voer de bron-en doel locatie in met de volgende para meters:
   
    `/s:JsonFile /s.Files:https://data.humdata.org/dataset/a60ac839-920d-435a-bf7d-25855602699d/resource/7234d067-2d74-449a-9c61-22ae6d98d928/download/volcano.json /t:DocumentDBBulk /t.ConnectionString:AccountEndpoint=https://[DocDBAccountName].documents.azure.com:443/;AccountKey=[[KEY];Database=volcano /t.Collection:volcano1`

Nadat u de gegevens hebt geïmporteerd, gaat u naar Jupyter en opent u het notitie blok met de titel *DocumentDBSample*. Het bevat python-code om toegang te krijgen tot Azure Cosmos DB en een eenvoudige query uit te voeren. Ga naar de [documentatie pagina](../../cosmos-db/index.yml)van de service voor meer informatie over Azure Cosmos db.

## <a name="use-power-bi-reports-and-dashboards"></a>Power BI-rapporten en-dash boards gebruiken 
U kunt het JSON-bestand van Volcano visualiseren vanuit het voor gaande Azure Cosmos DB voor beeld in Power BI Desktop om visuele inzichten in de gegevens te krijgen. In het [Power bi-artikel](../../cosmos-db/powerbi-visualize.md)vindt u gedetailleerde stappen. Dit zijn de stappen op hoog niveau:

1. Open Power BI Desktop en selecteer **Gegevens ophalen**. Geef de URL op als: `https://cahandson.blob.core.windows.net/samples/volcano.json` .
2. U ziet de JSON-records die zijn geïmporteerd als een lijst. De lijst converteren naar een tabel zodat Power BI ermee kan werken.
4. Vouw de kolommen uit door het pictogram uitvouwen (pijl) te selecteren.
5. U ziet dat de locatie een **record** veld is. Vouw de record uit en selecteer alleen de coördinaten. **Coordinate** is een lijst kolom.
6. Voeg een nieuwe kolom toe om de lijst coördinaten kolom te converteren naar een door komma's gescheiden kolom **LatLong** . Voeg de twee elementen in het veld coördinaten lijst samen met behulp van de formule ```Text.From([coordinates]{1})&","&Text.From([coordinates]{0})``` .
7. Converteer de kolom **verhoging** naar decimaal en selecteer de knoppen **sluiten** en **Toep assen** .

In plaats van de voor gaande stappen kunt u de volgende code plakken. De stappen die worden gebruikt in de Geavanceerde editor in Power BI om de gegevens transformaties in een query taal te schrijven.

```pqfl
let
    Source = Json.Document(Web.Contents("https://cahandson.blob.core.windows.net/samples/volcano.json")),
    #"Converted to Table" = Table.FromList(Source, Splitter.SplitByNothing(), null, null, ExtraValues.Error),
    #"Expanded Column1" = Table.ExpandRecordColumn(#"Converted to Table", "Column1", {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}, {"Volcano Name", "Country", "Region", "Location", "Elevation", "Type", "Status", "Last Known Eruption", "id"}),
    #"Expanded Location" = Table.ExpandRecordColumn(#"Expanded Column1", "Location", {"coordinates"}, {"coordinates"}),
    #"Added Custom" = Table.AddColumn(#"Expanded Location", "LatLong", each Text.From([coordinates]{1})&","&Text.From([coordinates]{0})),
    #"Changed Type" = Table.TransformColumnTypes(#"Added Custom",{{"Elevation", type number}})
in
    #"Changed Type"
```

U hebt nu de gegevens in uw Power BI gegevens model. Uw Power BI Desktop-exemplaar moet er als volgt uitzien:

![Power BI Desktop](./media/vm-do-ten-things/PowerBIVolcanoData.png)

U kunt beginnen met het maken van rapporten en visualisaties met behulp van het gegevens model. U kunt de stappen in [dit Power bi artikel](../../cosmos-db/powerbi-visualize.md#build-the-reports) volgen om een rapport te maken.

## <a name="scale-the-dsvm-dynamically"></a>De DSVM dynamisch schalen 
U kunt de DSVM omhoog en omlaag schalen om te voldoen aan de behoeften van uw project. Als u de virtuele machine niet 's avonds of in het weekend hoeft te gebruiken, kunt u de VM afsluiten vanuit de [Azure Portal](https://portal.azure.com).

> [!NOTE]
> U kunt kosten berekenen als u alleen de knop Shutdown gebruikt voor het besturings systeem op de virtuele machine. In plaats daarvan moet u de toewijzing van uw DSVM ongedaan maken met behulp van de Azure Portal of Cloud Shell.
> 
> 

Mogelijk moet u bepaalde grootschalige analyses afhandelen en meer CPU, geheugen of schijf capaciteit nodig hebben. Als dat het geval is, kunt u een keuze van de VM-grootten van CPU-kernen, GPU-exemplaren voor diep gaande lessen, geheugen capaciteit en schijf typen (inclusief Solid-state drives) vinden die voldoen aan uw berekenings-en budgettaire behoeften. De volledige lijst met virtuele machines, samen met de prijzen per uur berekend, is beschikbaar op de pagina met [prijzen voor Azure virtual machines](https://azure.microsoft.com/pricing/details/virtual-machines/) .


## <a name="add-more-tools"></a>Meer hulp middelen toevoegen
De hulpprogram ma's die vooraf zijn ingebouwd in de DSVM, kunnen een groot aantal algemene vereisten voor gegevens analyse verpakken. Dit bespaart u tijd omdat u uw omgevingen niet één voor één hoeft te installeren en configureren. Ook bespaart u geld, want u betaalt alleen voor de resources die u gebruikt.

U kunt andere Azure data-en Analytics-Services in dit artikel gebruiken om uw analyse omgeving uit te breiden. In sommige gevallen hebt u mogelijk extra hulp middelen nodig, waaronder een aantal eigen partner tools. U hebt volledige beheerders toegang op de virtuele machine om nieuwe hulpprogram ma's te installeren die u nodig hebt. U kunt ook aanvullende pakketten installeren in Python en R die niet vooraf zijn geïnstalleerd. Voor python kunt u ofwel of gebruiken ```conda``` ```pip``` . Voor R kunt u gebruiken ```install.packages()``` in de r-console of de IDE gebruiken en **pakketten**  >  **installeren**.

## <a name="deep-learning"></a>Deep learning

Naast de voor beelden op basis van het Framework, kunt u een set uitgebreide instructies verkrijgen die zijn gevalideerd op de DSVM. Met deze procedures kunt u snel aan de slag met het ontwikkelen van geavanceerde toepassingen in domeinen zoals afbeeldings-en tekst-en taal informatie.   


- [Neural netwerken uitvoeren in verschillende frameworks: in](https://github.com/ilkarman/DeepLearningFrameworks)dit scenario ziet u hoe u code kunt migreren van het ene naar het andere Framework. Er wordt ook gedemonstreerd hoe u modellen en runtime prestaties in Frameworks kunt vergelijken. 

- [Een hand leiding met instructies voor het bouwen van een end-to-end oplossing voor het detecteren van producten binnen installatie kopieën](https://github.com/Azure/cortana-intelligence-product-detection-from-images): detectie van afbeeldingen is een techniek waarmee objecten in afbeeldingen kunnen worden gevonden en geclassificeerd. Deze technologie biedt de mogelijkheid om enorme voor delen te bieden in veel zakelijke domeinen in de praktijk. Detail handelaren kunnen bijvoorbeeld deze techniek gebruiken om te bepalen welk product een klant uit het schap heeft gekozen. Met deze informatie kunt u de product inventaris van beheer opslaan. 

- [Diep gaande informatie over audio: in](/archive/blogs/machinelearning/hearing-ai-getting-started-with-deep-learning-for-audio-on-azure)deze zelf studie leert u hoe u een diep-Learning model kunt trainen voor de detectie van audio gebeurtenissen op de [gegevensset stads geluiden](https://serv.cusp.nyu.edu/projects/urbansounddataset/urbansound8k.html). Het bevat ook een overzicht van hoe u met audio gegevens werkt.

- [Classificatie van tekst documenten](https://github.com/anargyri/lstm_han): in dit scenario ziet u hoe u twee Neural-netwerk architecturen bouwt en traint: hiërarchische netwerk-en LSTM-netwerk (Long Short Term Memory). Deze Neural-netwerken gebruiken de Keras-API voor diep gaande lessen om tekst documenten te classificeren. 

## <a name="summary"></a>Samenvatting
In dit artikel worden enkele dingen beschreven die u kunt doen op de micro soft-Data Science Virtual Machine. Er zijn nog veel meer dingen die u kunt doen om de DSVM een efficiënte analyse omgeving te maken.
