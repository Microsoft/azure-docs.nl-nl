---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning-gegevensset opnieuw samen stellen'
description: Gegevens sets van Studio (klassiek) opnieuw samen stellen in Azure Machine Learning Designer
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 02/04/2021
ms.openlocfilehash: 4c04dd5a2b41b3db54b20c9e514767453951cc35
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "103564904"
---
# <a name="migrate-a-studio-classic-dataset-to-azure-machine-learning"></a>Een (klassieke) Studio-gegevensset migreren naar Azure Machine Learning

In dit artikel vindt u informatie over het migreren van een studio-gegevensset (klassiek) naar Azure Machine Learning. Zie [het artikel migratie overzicht](migrate-overview.md)voor meer informatie over het migreren van Studio (klassiek).

U hebt drie opties voor het migreren van een gegevensset naar Azure Machine Learning. Lees elke sectie om te bepalen welke optie het meest geschikt is voor uw scenario.


|Waar bevindt zich de gegevens? | Migratieoptie  |
|---------|---------|
|In Studio (klassiek)     |  Optie 1: [down load de gegevensset van Studio (klassiek) en upload deze naar Azure machine learning](#download-the-dataset-from-studio-classic).      |
|Cloudopslag     | Optie 2: [een gegevensset van een Cloud bron registreren](#import-data-from-cloud-sources). <br><br>  Optie 3: [Gebruik de module gegevens importeren om gegevens op te halen uit een Cloud bron](#import-data-from-cloud-sources).        |

> [!NOTE]
> Azure Machine Learning biedt ook ondersteuning voor [code-First-werk stromen](../how-to-create-register-datasets.md) voor het maken en beheren van gegevens sets. 

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](../how-to-manage-workspace.md#create-a-workspace)
- Een studio-gegevensset (klassiek) die moet worden gemigreerd.


## <a name="download-the-dataset-from-studio-classic"></a>De gegevensset van Studio downloaden (klassiek)

De eenvoudigste manier om een (klassieke) Studio-gegevensset naar Azure Machine Learning te migreren, is door uw gegevensset te downloaden en deze te registreren in Azure Machine Learning. Hiermee maakt u een nieuwe kopie van uw gegevensset en uploadt u deze naar een Azure Machine Learning gegevens opslag.

U kunt de volgende Studio-typen (Classic) rechtstreeks downloaden.

* Tekst zonder opmaak (. txt)
* Door komma's gescheiden waarden (CSV) met een header (. CSV) of zonder (.nh.csv)
* Door tabs gescheiden waarden (TSV) met een header (. tsv) of zonder (. NH. tsv)
* Excel-bestand
* Zip-bestand (. zip)

Gegevens sets rechtstreeks downloaden:
1. Ga naar uw studio (klassieke) werk ruimte ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Selecteer in de linker navigatie balk het tabblad **gegevens sets** .
1. Selecteer de gegevensset (s) die u wilt downloaden.
1. Selecteer in de onderste actie balk **downloaden**.

    ![Scherm afbeelding die laat zien hoe u een gegevensset kunt downloaden in Studio (klassiek)](./media/migrate-register-dataset/download-dataset.png)

Voor de volgende gegevens typen moet u de module **converteren naar CSV** gebruiken om gegevens sets te downloaden.

* SVMLight-gegevens (. SVMLight) 
* ARFF-gegevens (kenmerk relatie bestands indeling) (. ARFF) 
* R-object of werkruimte bestand (. RData
* Type gegevensset (. data). Het type gegevensset is Studio (klassiek) intern gegevens type voor module-uitvoer.

Uw gegevensset converteren naar een CSV en de resultaten downloaden:

1. Ga naar uw studio (klassieke) werk ruimte ( [https://studio.azureml.net](https://studio.azureml.net) ).
1. Een nieuw experiment maken.
1. Sleep de gegevensset die u wilt downloaden naar het canvas en zet deze neer.
1. Voeg een **conversie toe aan CSV** -module.
1. Verbind de invoer poort **converteren naar CSV** naar de uitvoer poort van de gegevensset.
1. Voer het experiment uit.
1. Klik met de rechter muisknop op de module **converteren naar CSV** .
1. Selecteer **resultaten gegevensset**  >  **downloaden**.

    ![Scherm afbeelding die laat zien hoe u een conversie naar CSV-pijp lijn kunt instellen](./media/migrate-register-dataset/csv-download-dataset.png)

### <a name="upload-your-dataset-to-azure-machine-learning"></a>Upload uw gegevensset naar Azure Machine Learning

Nadat u het gegevens bestand hebt gedownload, kunt u de gegevensset registreren in Azure Machine Learning:

1. Ga naar Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com)).
1. Selecteer in het navigatie deel venster links het tabblad **gegevens sets** .
1. Selecteer **gegevensset maken**  >  **op basis van lokale bestanden**.
    ![Scherm opname van het tabblad gegevens sets en de knop voor het maken van een lokaal bestand](./media/migrate-register-dataset/register-dataset.png)
1. Voer een naam en beschrijving in.
1. Selecteer voor **type gegevensset** **in tabel vorm**.

    > [!NOTE]
    > U kunt ook ZIP-bestanden uploaden als gegevens sets. Als u een ZIP-bestand wilt uploaden, selecteert u **bestand** voor **type gegevensset**.

1. Selecteer **voor gegevens opslag en bestanden** selecteren de gegevens opslag waarnaar u uw gegevenssetbestand wilt uploaden.

    Azure Machine Learning slaat de gegevensset standaard op in de standaardwerk ruimte blobarchief. Zie [verbinding maken met Storage services](../how-to-access-data.md)voor meer informatie over gegevens opslag.

1. Stel de instellingen voor het parseren van gegevens en het schema voor uw gegevensset in. Bevestig vervolgens uw instellingen.

## <a name="import-data-from-cloud-sources"></a>Gegevens importeren uit Cloud bronnen

Als uw gegevens zich al in een service voor Cloud opslag bevindt en u uw gegevens op de oorspronkelijke locatie wilt bewaren. U kunt een van de volgende opties gebruiken:

|Opname methode|Beschrijving|
|---| --- |
|Een Azure Machine Learning-gegevensset registreren|Gegevens opnemen uit lokale en online gegevens bronnen (BLOB, ADLS Gen1, ADLS Gen2, bestands share, SQL-data base). <br><br>Hiermee maakt u een verwijzing naar de gegevens bron, die vertraagd geëvalueerd tijdens runtime. Gebruik deze optie als u herhaaldelijk toegang hebt tot deze gegevensset en geavanceerde gegevens functies wilt inschakelen, zoals versie beheer en bewaking van gegevens.
|Gegevens module importeren|Gegevens opnemen uit online gegevens bronnen (BLOB, ADLS Gen1, ADLS Gen2, bestands share, SQL-data base). <br><br> De gegevensset wordt alleen geïmporteerd in de huidige uitvoering van de ontwerp pijplijn.


>[!Note]
> Studio (klassiek) gebruikers moeten er rekening mee houden dat de volgende Cloud bronnen niet systeem eigen worden ondersteund in Azure Machine Learning:
> - Hive-query
> - Azure Table
> - Azure Cosmos DB
> - On-premises SQL Database
>
> U wordt aangeraden om de gegevens met behulp van Azure Data Factory te migreren naar een ondersteunde opslag Services.  

### <a name="register-an-azure-machine-learning-dataset"></a>Een Azure Machine Learning-gegevensset registreren

Gebruik de volgende stappen om een gegevensset te registreren voor Azure Machine Learning van een Cloud service: 

1. [Maak een gegevens opslag](../how-to-connect-data-ui.md#create-datastores)die de Cloud Storage-service koppelt aan uw Azure machine learning-werk ruimte. 

1. [Registreer een gegevensset](../how-to-connect-data-ui.md#create-datasets). Als u een (klassieke) Studio-gegevensset migreert, selecteert u de instelling voor de **tabellaire** gegevensset.

Nadat u een gegevensset in Azure Machine Learning hebt geregistreerd, kunt u deze in Designer gebruiken:
 
1. Maak een nieuwe ontwerp pijplijn-concept.
1. Vouw in het palet module aan de linkerkant het gedeelte **gegevens sets** uit.
1. Sleep uw geregistreerde gegevensset op het canvas. 

### <a name="use-the-import-data-module"></a>De module gegevens importeren gebruiken

Gebruik de volgende stappen om gegevens rechtstreeks te importeren in uw ontwerp pijplijn:

1. [Maak een gegevens opslag](https://github.com/MicrosoftDocs/azure-docs-pr/blob/master/articles/machine-learning/how-to-connect-data-ui.md#create-datastores)die de Cloud Storage-service koppelt aan uw Azure machine learning-werk ruimte. 

Nadat u de gegevens opslag hebt gemaakt, kunt u de module [**import data**](../algorithm-module-reference/import-data.md) in de ontwerp functie gebruiken om gegevens van de Data Base op te nemen:

1. Maak een nieuwe ontwerp pijplijn-concept.
1. Zoek in het palet module aan de linkerkant de module **gegevens importeren** en sleep deze naar het canvas.
1. Selecteer de module **gegevens importeren** en gebruik de instellingen in het rechterdeel venster om de gegevens bron te configureren.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een (klassieke) Studio-gegevensset kunt migreren naar Azure Machine Learning. De volgende stap is het [opnieuw bouwen van een studio-trainings pijplijn (klassiek)](migrate-rebuild-experiment.md).


Zie de andere artikelen in de migratie reeks Studio (klassiek):

1. [Overzicht migratie](migrate-overview.md).
1. **Gegevens sets migreren**.
1. [Bouw een studio-trainings pijplijn (klassiek) opnieuw](migrate-rebuild-experiment.md)op.
1. [Een studio-webservice (klassiek) opnieuw bouwen](migrate-rebuild-web-service.md).
1. [Een Azure machine learning-webservice integreren met client-apps](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R-script migreren](migrate-execute-r-script.md).
