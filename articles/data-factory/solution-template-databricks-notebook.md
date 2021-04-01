---
title: Transformatie met Azure Databricks
description: Meer informatie over het gebruik van een oplossings sjabloon voor het transformeren van gegevens met behulp van een Databricks-notebook in Azure Data Factory.
ms.author: abnarain
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 04/27/2020
ms.openlocfilehash: ee663423071458605f37f07293693dbc91f592bb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100362111"
---
# <a name="transformation-with-azure-databricks"></a>Transformatie met Azure Databricks

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze zelf studie maakt u een end-to-end-pijp lijn die de activiteiten **validatie**, **kopie gegevens** en **notebook** bevat in azure Data Factory.

- **Validatie** zorgt ervoor dat de bron-gegevensset gereed is voor het gebruik van downstream voordat u de kopieer-en analyse taak start.

- **Kopieer gegevens** dupliceert de bron-gegevensset naar de Sink-opslag, die is gekoppeld als DBFS in de Azure Databricks notebook. Op deze manier kan de gegevensset rechtstreeks worden gebruikt door Spark.

- **Notebook** activeert de Databricks-notebook waarmee de gegevensset wordt getransformeerd. Het voegt ook de gegevensset toe aan een verwerkte map of Azure Azure Synapse Analytics.

Ter vereenvoudiging maakt de sjabloon in deze zelf studie geen geplande trigger. U kunt indien nodig een invoeg toepassing toevoegen.

![Diagram van de pijp lijn](media/solution-template-Databricks-notebook/pipeline-example.png)

## <a name="prerequisites"></a>Vereisten

- Een Azure Blob-opslag account met een container met de naam `sinkdata` voor gebruik als sink.

  Noteer de naam van het opslag account, de container naam en de toegangs sleutel. U hebt deze waarden later in de sjabloon nodig.

- Een Azure Databricks-werk ruimte.

## <a name="import-a-notebook-for-transformation"></a>Een notitie blok voor trans formatie importeren

Een **trans formatie** -notitie blok importeren in uw Databricks-werk ruimte:

1. Meld u aan bij uw Azure Databricks-werk ruimte en selecteer vervolgens **importeren**.
       ![Menu opdracht voor het importeren van een werk ruimte ](media/solution-template-Databricks-notebook/import-notebook.png) die het pad van de werk ruimte kan verschillen van het bestand dat wordt weer gegeven, maar onthoud het later.
1. Selecteer **importeren vanuit: URL**. Voer in het tekstvak in `https://adflabstaging1.blob.core.windows.net/share/Transformations.html` .

   ![Selecties voor het importeren van een notitie blok](media/solution-template-Databricks-notebook/import-from-url.png)

1. Nu gaan we de **trans formatie** -laptop bijwerken met de verbindings gegevens van uw opslag.

   Ga in het geïmporteerde notitie blok naar **opdracht 5** , zoals wordt weer gegeven in het volgende code fragment.

   - Vervang `<storage name>` en `<access key>` door uw eigen opslag verbindings gegevens.
   - Gebruik het opslag account bij de `sinkdata` container.

    ```python
    # Supply storageName and accessKey values  
    storageName = "<storage name>"  
    accessKey = "<access key>"  

    try:  
      dbutils.fs.mount(  
        source = "wasbs://sinkdata\@"+storageName+".blob.core.windows.net/",  
        mount_point = "/mnt/Data Factorydata",  
        extra_configs = {"fs.azure.account.key."+storageName+".blob.core.windows.net": accessKey})  

    except Exception as e:  
      # The error message has a long stack track. This code tries to print just the relevant line indicating what failed.

    import re
    result = re.findall(r"\^\s\*Caused by:\s*\S+:\s\*(.*)\$", e.message, flags=re.MULTILINE)
    if result:
      print result[-1] \# Print only the relevant error message
    else:  
      print e \# Otherwise print the whole stack trace.  
    ```

1. Genereer een **Databricks-toegangs token** voor Data Factory om toegang te krijgen tot Databricks.
   1. Selecteer in de werk ruimte Databricks uw gebruikers profiel pictogram in de rechter bovenhoek.
   1. **Gebruikers instellingen** selecteren.
    ![Menu opdracht voor gebruikers instellingen](media/solution-template-Databricks-notebook/user-setting.png)
   1. Selecteer **nieuw token genereren** onder het tabblad **toegangs tokens** .
   1. Selecteer **genereren**.

    ![Knop genereren](media/solution-template-Databricks-notebook/generate-new-token.png)

   *Sla het toegangs token* op voor later gebruik bij het maken van een Databricks-gekoppelde service. Het toegangs token ziet er ongeveer als volgt uit `dapi32db32cbb4w6eee18b7d87e45exxxxxx` .

## <a name="how-to-use-this-template"></a>Deze sjabloon gebruiken

1. Ga naar de **trans formatie met Azure Databricks** sjabloon en maak nieuwe gekoppelde services voor de volgende verbindingen.

   ![Instelling verbindingen](media/solution-template-Databricks-notebook/connections-preview.png)

    - **Bron-BLOB-verbinding** : voor toegang tot de bron gegevens.

       Voor deze oefening kunt u de open bare Blob-opslag gebruiken die de bron bestanden bevat. Raadpleeg de volgende scherm afbeelding voor de configuratie. Gebruik de volgende **SAS-URL** om verbinding te maken met de bron opslag (alleen-lezen toegang):

       `https://storagewithdata.blob.core.windows.net/data?sv=2018-03-28&si=read%20and%20list&sr=c&sig=PuyyS6%2FKdB2JxcZN0kPlmHSBlD8uIKyzhBWmWzznkBw%3D`

        ![Selecties voor de verificatie methode en SAS-URL](media/solution-template-Databricks-notebook/source-blob-connection.png)

    - **Doel-BLOB-verbinding** -om de gekopieerde gegevens op te slaan.

       Selecteer uw Sink Storage-Blob in het venster **nieuwe gekoppelde service** .

       ![Sink Storage-BLOB als een nieuwe gekoppelde service](media/solution-template-Databricks-notebook/destination-blob-connection.png)

    - **Azure Databricks** : om verbinding te maken met het Databricks-cluster.

        Een Databricks-gekoppelde service maken met behulp van de toegangs sleutel die u eerder hebt gegenereerd. U kunt ervoor kiezen om een *interactief cluster* te selecteren als u er een hebt. In dit voor beeld wordt de optie **nieuw taak cluster** gebruikt.

        ![Selecties voor het maken van verbinding met het cluster](media/solution-template-Databricks-notebook/databricks-connection.png)

1. Selecteer **deze sjabloon gebruiken**. U ziet een pijp lijn die is gemaakt.

    ![Een pijplijn maken](media/solution-template-Databricks-notebook/new-pipeline.png)

## <a name="pipeline-introduction-and-configuration"></a>Inleiding en configuratie van pijp lijn

In de nieuwe pijp lijn worden de meeste instellingen automatisch geconfigureerd met standaard waarden. Controleer de configuraties van uw pijp lijn en breng de gewenste wijzigingen aan.

1. Controleer in de **vlag Beschik baarheid** **validatie** activiteit of de waarde van de bron **gegevensset** is ingesteld op `SourceAvailabilityDataset` die u eerder hebt gemaakt.

   ![Waarde van bron gegevensset](media/solution-template-Databricks-notebook/validation-settings.png)

1. Controleer in het bestand **gegevens kopiëren** **naar BLOB** de tabbladen **bron** en **sink** . Wijzig de instellingen indien nodig.

   - Tabblad Bron van **bron** ![](media/solution-template-Databricks-notebook/copy-source-settings.png)

   - Tabblad Sink van **sink** ![](media/solution-template-Databricks-notebook/copy-sink-settings.png)

1. Controleer de paden en instellingen naar behoefte in de **trans formatie** van de **notitieblok** activiteit en werk deze bij.

   **Databricks gekoppelde service** moet vooraf zijn ingevuld met de waarde uit een vorige stap, zoals wordt weer gegeven: ![ gevulde waarde voor de gekoppelde Databricks-service](media/solution-template-Databricks-notebook/notebook-activity.png)

   De instellingen van het **notitie blok** controleren:
  
    1. Selecteer het tabblad **instellingen** . Controleer bij **pad naar notebook** of het standaardpad juist is. Mogelijk moet u bladeren en het juiste pad voor het notitie blok kiezen.

       ![Pad naar notebook](media/solution-template-Databricks-notebook/notebook-settings.png)

    1. Vouw de Select-para **meters** uit en controleer of de para meters overeenkomen met wat wordt weer gegeven in de volgende scherm afbeelding. Deze para meters worden door gegeven aan de Databricks-notebook van Data Factory.

       ![Basis parameters](media/solution-template-Databricks-notebook/base-parameters.png)

1. Controleer of de **pijplijn parameters** overeenkomen met wat wordt weer gegeven in de volgende scherm afbeelding: ![ pijplijn parameters](media/solution-template-Databricks-notebook/pipeline-parameters.png)

1. Verbinding maken met uw gegevens sets.

    >[!NOTE]
    >In de onderstaande gegevens sets is het bestandspad automatisch in de sjabloon opgegeven. Als er vereiste wijzigingen zijn, moet u ervoor zorgen dat u het pad voor de **container** en de **map** opgeeft als een verbindings fout.

   - **SourceAvailabilityDataset** : Controleer of de bron gegevens beschikbaar zijn.

     ![Selecties voor de gekoppelde service en het bestandspad voor SourceAvailabilityDataset](media/solution-template-Databricks-notebook/source-availability-dataset.png)

   - **SourceFilesDataset** : voor toegang tot de bron gegevens.

       ![Selecties voor de gekoppelde service en het bestandspad voor SourceFilesDataset](media/solution-template-Databricks-notebook/source-file-dataset.png)

   - **DestinationFilesDataset** : de gegevens naar de Sink-doel locatie kopiëren. Gebruik de volgende waarden:

     - **Gekoppelde service**  -  `sinkBlob_LS` , die in een vorige stap is gemaakt.

     - **Bestandspad**  -  `sinkdata/staged_sink` .

       ![Selecties voor de gekoppelde service en het bestandspad voor DestinationFilesDataset](media/solution-template-Databricks-notebook/destination-dataset.png)

1. Selecteer **debug** om de pijp lijn uit te voeren. U kunt de koppeling naar Databricks-logboeken vinden voor meer gedetailleerde Spark-Logboeken.

    ![Koppeling naar Databricks-logboeken vanuit uitvoer](media/solution-template-Databricks-notebook/pipeline-run-output.png)

    U kunt het gegevens bestand ook controleren met behulp van Azure Storage Explorer.

    > [!NOTE]
    > Voor het correleren met Data Factory pijplijn uitvoeringen, wordt in dit voor beeld de ID van de pijplijn uitvoering van de data factory toegevoegd aan de uitvoermap. Dit helpt bij het bijhouden van de bestanden die door elke uitvoering worden gegenereerd.
    > ![ID van toegevoegde pijplijn uitvoering](media/solution-template-Databricks-notebook/verify-data-files.png)

## <a name="next-steps"></a>Volgende stappen

- [Inleiding tot Azure Data Factory](introduction.md)
