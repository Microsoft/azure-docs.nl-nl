---
title: 'Zelfstudie: Python-scripts via Data Factory uitvoeren'
description: Informatie over het uitvoeren van Python-scripts als onderdeel van een pijplijn via Azure Data Factory met behulp van Azure Batch.
author: pkshultz
ms.devlang: python
ms.topic: tutorial
ms.date: 03/12/2021
ms.author: peshultz
ms.custom: mvc, devx-track-python
ms.openlocfilehash: 241a47ccf9021c6065fea907b4d9914744a64972
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103461688"
---
# <a name="tutorial-run-python-scripts-through-azure-data-factory-using-azure-batch"></a>Zelfstudie: Python-scripts uitvoeren via Azure Data Factory met behulp van Azure Batch

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Verifiëren met Batch- en Storage-accounts
> * Een script ontwikkelen en uitvoeren in Python
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Uw Python-workloads plannen
> * Uw analysepijplijn bewaken
> * Toegang tot uw logboekbestanden

In het volgende voorbeeld wordt een Python-script uitgevoerd dat CSV-invoer van een blob-opslagcontainer ontvangt, een gegevensbewerkingsproces uitvoert en de uitvoer naar een afzonderlijke blob-opslagcontainer schrijft.

Als u nog geen abonnement op Azure hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

* Een geïnstalleerde [Python](https://www.python.org/downloads/)-distributie voor lokaal testen.
* Het `pip`-pakket [azure-storage-blob](https://pypi.org/project/azure-storage-blob/).
* De [iris.csv-gegevensset](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv)
* Een Azure Batch-account en een gekoppeld Azure Storage-account. Zie [Een Batch-account maken](quick-create-portal.md#create-a-batch-account) voor meer informatie over het maken en koppelen van Batch-accounts aan opslagaccounts.
* Een Azure Data Factory-account. Zie [Een data factory maken](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory) voor meer informatie over het maken van een data factory via de Azure-portal.
* [Batch Explorer](https://azure.github.io/BatchExplorer/).
* [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Meld u aan bij de Azure Portal op [https://portal.azure.com](https://portal.azure.com).

[!INCLUDE [batch-common-credentials](../../includes/batch-common-credentials.md)]

## <a name="create-a-batch-pool-using-batch-explorer"></a>Een batch-pool maken met behulp van Batch Explorer

In deze sectie gebruikt u Batch Explorer om de batch-pool te maken die wordt gebruikt door uw Azure Data Factory-pijplijn. 

1. Meld u aan bij Batch Explorer met uw Azure-referenties.
1. Selecteer uw Batch-account
1. Maak een pool door **Pools** aan de linkerkant te selecteren en klik vervolgens op de knop **Toevoegen** boven het zoekformulier. 
    1. Kies een id en weergavenaam. In dit voorbeeld gebruiken we `custom-activity-pool`.
    1. Stel het schaaltype in op **Vaste grootte** en stel het toegewezen aantal knooppunten in op 2.
    1. Onder **Gegevenswetenschap** selecteert u **Dsvm Windows** als besturingssysteem.
    1. Kies `Standard_f2s_v2` als de grootte van de virtuele machine.
    1. Schakel de begintaak in en voeg de opdracht `cmd /c "pip install azure-storage-blob pandas"`toe. De gebruikers-id kan blijven als standaard **Pool-gebruiker**.
    1. Selecteer **OK**.

## <a name="create-blob-containers"></a>Blob-containers maken

Hier maakt u blob-containers waarmee de invoer- en uitvoer bestanden voor de OCR Batch-taak worden opgeslagen.

1. Meld u aan bij Storage Explorer met uw Azure-referenties.
1. Met het opslagaccount dat is gekoppeld aan uw Batch-account, maakt u twee blob-containers (één voor invoerbestanden, één voor uitvoerbestanden) door de stappen te volgen op [Een blob-container maken](../vs-azure-tools-storage-explorer-blobs.md#create-a-blob-container).
    * In dit voorbeeld roepen we onze invoercontainer `input`en onze uitvoercontainer`output` aan.
1. Upload [`iris.csv`](https://github.com/Azure-Samples/batch-adf-pipeline-tutorial/blob/master/iris.csv) naar uw invoercontainer `input` met behulp van Storage Explorer door de stappen te volgen in [Blobs in een BLOB-container beheren](../vs-azure-tools-storage-explorer-blobs.md#managing-blobs-in-a-blob-container).

## <a name="develop-a-script-in-python"></a>Een script ontwikkelen in Python

Het volgende Python-script laadt de `iris.csv` gegevensset uit uw `input` container, voert een gegevensverwerkingsproces uit en slaat de resultaten op in de `output`-container.

``` python
# Load libraries
from azure.storage.blob import BlobClient
import pandas as pd

# Define parameters
connectionString = "<storage-account-connection-string>"
containerName = "output"
outputBlobName  = "iris_setosa.csv"

# Establish connection with the blob storage account
blob = BlobClient.from_connection_string(conn_str=connectionString, container_name=containerName, blob_name=outputBlobName)

# Load iris dataset from the task node
df = pd.read_csv("iris.csv")

# Take a subset of the records
df = df[df['Species'] == "setosa"]

# Save the subset of the iris dataframe locally in task node
df.to_csv(outputBlobName, index = False)

with open(outputBlobName, "rb") as data:
    blob.upload_blob(data)
```

Sla het script op als `main.py` en uploadt dit script naar de **Azure Storage** `input`-container. Zorg ervoor dat u de functionaliteit lokaal test en valideert voordat u deze uploadt naar uw blob-container:

``` bash
python main.py
```

## <a name="set-up-an-azure-data-factory-pipeline"></a>Een Azure Data Factory-pijplijn instellen

In deze sectie maakt en valideert u een pijplijn met behulp van uw Python-script.

1. Volg de stappen voor het maken van een data factory in het gedeelte "Een data factory maken" van [dit artikel](../data-factory/quickstart-create-data-factory-portal.md#create-a-data-factory).
1. Selecteer in het vak **Factory-resources** de +-knop (plusteken) en selecteer vervolgens **Pijplijn**
1. Stel op het tabblad **Algemeen** de naam van de pijplijn in als "Python uitvoeren"

    ![Stel op het tabblad Algemeen de naam van de pijplijn in als 'Python uitvoeren'](./media/run-python-batch-azure-data-factory/create-pipeline.png)

1. Vouw in het vak **Activiteiten** **Batch-service** uit. Sleep de aangepaste activiteit uit de werkset **Activiteiten** naar het ontwerpoppervlak voor pijplijnen. Vul de volgende tabbladen in voor de aangepaste activiteit:
    1. Geef op het tabblad **Algemeen** **TestPipeline** op als naam ![ op het tabblad Algemeen en geef testPipeline op bij naam.](./media/run-python-batch-azure-data-factory/create-custom-task.png)
    1. Voeg op het tabblad **Azure batch** het **batch-account** toe dat in de vorige stappen is gemaakt en **test de verbinding** om er zeker van te zijn dat het lukt.
    ![Voeg op het tabblad Azure Batch het Batch-account toe dat in de vorige stappen is gemaakt en test vervolgens de verbinding](./media/run-python-batch-azure-data-factory/integrate-pipeline-with-azure-batch.png)
    1. Op het tabblad **instellingen** :
        1. Stel de **opdracht** in op als `python main.py` .
        1. Voeg voor de **Resource gekoppelde service** het opslagaccount toe dat in de vorige stappen is gemaakt. Test de verbinding om er zeker van te zijn dat deze is geslaagd.
        1. Selecteer in het pad **Map** de naam van de **Azure Blob Storage**-container die het Python-script en de bijbehorende invoer bevat. Hiermee worden de geselecteerde bestanden van de container naar de exemplaren voor het groepsknooppunt gedownload voordat het Python-script wordt uitgevoerd.

        ![Selecteer in het mappad de naam van de Azure Blob Storage-container](./media/run-python-batch-azure-data-factory/create-custom-task-py-script-command.png)

1. Klik in de pijplijnwerkbalk boven het canvas op **Valideren** om de instellingen voor de pijplijn te valideren. Controleer of de pijplijn is gevalideerd. Klik op de &gt;&gt; (pijl-rechts) om de uitvoergegevens van de validatie te sluiten.
1. Klik op **fouten opsporen** om de pijplijn te testen en te controleren of deze correct werkt.
1. Klik op **Publiceren** om de pijplijn te publiceren.
1. Klik op **Activeren** om het Python-script uit te voeren als onderdeel van een batch-proces.

    ![Klik op Activeren om het Python-script uit te voeren als onderdeel van een batch-proces.](./media/run-python-batch-azure-data-factory/create-custom-task-py-success-run.png)

### <a name="monitor-the-log-files"></a>De logboekbestanden bewaken

Als er waarschuwingen of fouten worden gegenereerd door de uitvoering van het script, kunt u `stdout.txt` of `stderr.txt` uitchecken voor meer informatie over de geregistreerde uitvoer.

1. Selecteer **Taken** aan de linkerkant van Batch Explorer.
1. Kies de taak die door uw data factory is gemaakt. Als u de naam van de pool `custom-activity-pool` hebt opgegeven, selecteert u `adfv2-custom-activity-pool`.
1. Klik op de taak met de afsluitcode voor de fout.
1. Bekijk `stdout.txt` en `stderr.txt` om uw probleem te onderzoeken en vast te stellen.

## <a name="clean-up-resources"></a>Resources opschonen

Hoewel jobs en taken zelf niet in rekening worden gebracht, worden rekenknooppunten wel in rekening gebracht. Daarom is het raadzaam om pools alleen toe te wijzen als dat nodig is. Wanneer u de pool verwijdert, wordt ook alle taakuitvoer op de knooppunten verwijderd. De invoer- en uitvoerbestanden blijven echter aanwezig in het opslagaccount. Wanneer u het Batch-account en het opslagaccount niet meer nodig hebt, kunt u deze ook verwijderen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

> [!div class="checklist"]
> * Verifiëren met Batch- en Storage-accounts
> * Een script ontwikkelen en uitvoeren in Python
> * Een pool van rekenknooppunten maken om een toepassing uit te voeren
> * Uw Python-workloads plannen
> * Uw analysepijplijn bewaken
> * Toegang tot uw logboekbestanden

Zie voor meer informatie over Azure Data Factory:

> [!div class="nextstepaction"]
> [Overzicht van Azure Data Factory](../data-factory/introduction.md)
