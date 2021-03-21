---
title: Een Azure Data Factory maken op basis van een Azure Resource Manager-sjabloon (ARM)
description: Maak een Azure Data Factory-voorbeeldpijplijn op basis van een Azure Resource Manager-sjabloon (ARM).
ms.service: data-factory
tags: azure-resource-manager
author: dcstwh
ms.author: weetok
ms.reviewer: maghan, jingwang
ms.topic: quickstart
ms.custom: subject-armqs
ms.date: 07/16/2020
ms.openlocfilehash: f3e76bf16b702adb26183209d36189a53b695c40
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100373841"
---
# <a name="quickstart-create-an-azure-data-factory-using-arm-template"></a>Quickstart: Een Azure Data Factory maken op basis van een ARM-sjabloon

> [!div class="op_single_selector" title1="Selecteer de versie van de Data Factory-service die u gebruikt:"]
> * [Versie 1:](v1/data-factory-build-your-first-pipeline-using-arm.md)
> * [Huidige versie](quickstart-create-data-factory-resource-manager-template.md)

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

In deze quickstart wordt beschreven hoe u een Azure Resource Manager-sjabloon (ARM) gebruikt om een Azure Data Factory te maken. Met de pijplijn die u in deze data factory maakt, worden gegevens **gekopieerd** van één map naar een andere map in een Azure Blob Storage. Meer informatie over het **transformeren** van gegevens met behulp van Azure Data Factory vindt u in [Zelfstudie: Gegevens transformeren met Spark](transform-data-using-spark.md).

[!INCLUDE [About Azure Resource Manager](../../includes/resource-manager-quickstart-introduction.md)]

> [!NOTE]
> Dit artikel is geen gedetailleerde introductie tot de Data Factory-service. Zie [Inleiding tot Azure Data Factory](introduction.md) voor een inleiding tot Azure Data Factory-service.

Als uw omgeving voldoet aan de vereisten en u benkend bent met het gebruik van ARM-sjablonen, selecteert u de knop **Implementeren naar Azure**. De sjabloon wordt in Azure Portal geopend.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

## <a name="prerequisites"></a>Vereisten

### <a name="azure-subscription"></a>Azure-abonnement

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/) aan voordat u begint.

### <a name="create-a-file"></a>Een bestand maken

Open een tekstverwerker zoals **Kladblok** en maak een bestand met de naam **emp.txt** met de volgende inhoud:

```emp.txt
John, Doe
Jane, Doe
```

Sla het bestand op in de map **c:\ADFv2QuickStartPSH**. (Maak de map als deze nog niet bestaat.)

## <a name="review-template"></a>Sjabloon controleren

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure-quickstartsjablonen](https://azure.microsoft.com/resources/templates/101-data-factory-v2-blob-to-blob-copy/).

:::code language="json" source="~/quickstart-templates/101-data-factory-v2-blob-to-blob-copy/azuredeploy.json":::

Er zijn Azure-resources gedefinieerd in de sjabloon:

- [Microsoft.Storage/storageAccounts](/azure/templates/Microsoft.Storage/storageAccounts): Hiermee wordt een opslagaccount gedefinieerd.
- [Microsoft.DataFactory/factories](/azure/templates/microsoft.datafactory/factories): Hiermee wordt een Azure Data Factory gemaakt.
- [Microsoft.DataFactory/factories/linkedServices](/azure/templates/microsoft.datafactory/factories/linkedservices): Hiermee wordt een aan Azure Data Factory gekoppelde service gemaakt.
- [Microsoft.DataFactory/factories/datasets](/azure/templates/microsoft.datafactory/factories/datasets): Hiermee wordt een Azure Data Factory-gegevensset gemaakt.
- [Microsoft.DataFactory/factories/pipelines](/azure/templates/microsoft.datafactory/factories/pipelines): Hiermee wordt een Azure Data Factory-pijplijn gemaakt.

Meer voorbeelden van Azure Data Factory-sjablonen vindt u in de [galerie met quickstart-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular).

## <a name="deploy-the-template"></a>De sjabloon implementeren

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een Azure Data Factory-account, een opslagaccount en een blobcontainer gemaakt.

    [![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-data-factory-v2-blob-to-blob-copy%2Fazuredeploy.json)

2. Typ of selecteer de volgende waarden.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-deploy-arm-template.png" alt-text="ADF ARM-sjabloon implementeren":::

    Gebruik de standaardwaarden om de Azure Data Factory-resources te maken, tenzij er iets anders is aangegeven:

    - **Abonnement**: Selecteer een Azure-abonnement.
    - **Resourcegroep**: Selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en selecteer **OK**.
    - **Regio**: Selecteer een locatie.  Bijvoorbeeld *VS - Oost*.
    - **Data Factory-naam**: Gebruik de standaardwaarde.
    - **Locatie**: Gebruik de standaardwaarde.
    - **Naam van opslagaccount**: Gebruik de standaardwaarde.
    - **Blobcontainer**: Gebruik de standaardwaarde.

## <a name="review-deployed-resources"></a>Geïmplementeerde resources bekijken

1. Selecteer **Naar de resourcegroep gaan**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-go-to-resource-group.png" alt-text="Resourcegroep":::

2.  Verifieer dat uw Azure Data Factory is gemaakt.
    1. De naam van uw Azure Data Factory heeft de indeling - datafactory\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-sample.png" alt-text="Data Factory-voorbeeld":::

2. Verifieer dat uw opslagaccount is gemaakt.
    1. De naam van uw opslagaccount heeft de indeling - storage\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-storage-account.png" alt-text="Opslagaccount":::

3. Selecteer het gemaakte opslagaccount en selecteer vervolgens **Containers**.
    1. Selecteer op de pagina **Containers** de blobcontainer die u hebt gemaakt.
        1. De naam van uw blobcontainer heeft de indeling - blob\<uniqueid\>.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-blob-container.png" alt-text="Blobcontainer":::

### <a name="upload-a-file"></a>Bestand uploaden

1. Selecteer op de pagina **Containers** de optie **Uploaden**.

2. Selecteer in het rechterdeelvenster het vak **Bestanden**, blader naar het bestand **emp.txt** dat u eerder hebt gemaakt, en selecteer het.

3. Vouw de kop **Geavanceerd** uit.

4. In het vak **Uploaden naar map** voert u *invoer* in.

5. Selecteer de knop **Uploaden**. Als het goed is, ziet u in de lijst nu het bestand **emp.txt**, evenals de uploadstatus hiervan.

6. Selecteer het pictogram **Sluiten** (**X**) om de pagina **Blob uploaden** te sluiten.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-upload-blob-file.png" alt-text="Bestand uploaden naar invoermap":::

Laat de containerpagina openstaan, want u kunt deze aan het eind van deze quickstart gebruiken om de uitvoer te verifiëren.

### <a name="start-trigger"></a>Trigger starten

1. Ga naar de pagina **Data factory's** en selecteer de data factory die u hebt gemaakt. 

2. Selecteer de tegel **Maken en bewaken**. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author-monitor-tile.png" alt-text="Maken en bewaken":::

2. Selecteer het tabblad **Auteur** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-author.png" border="false":::.

3. Selecteer de gemaakte pijplijn - ArmtemplateSampleCopyPipeline.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-pipelines.png" alt-text="ARM-sjabloonpijplijn":::

4. Selecteer **Trigger toevoegen** > **Nu activeren**.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-trigger-now.png" alt-text="Trigger":::

5. In het rechterdeelvenster onder **Pijplijnuitvoering** selecteert u **OK**.

### <a name="monitor-the-pipeline"></a>De pijplijn bewaken

1. Selecteer het tabblad **Controleren** :::image type="icon" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-monitor.png" border="false":::.

2. U ziet de uitvoering van de activiteiten die zijn gekoppeld aan de pijplijnuitvoering. In deze QuickStart heeft de pijplijn slechts één activiteit en wel van het type Kopiëren. Daarom ziet u een uitvoering voor die activiteit.

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-successful-run.png" alt-text="Geslaagde uitvoering":::

### <a name="verify-the-output-file"></a>Het uitvoerbestand verifiëren

De uitvoermap wordt automatisch door de pijplijn gemaakt in de blobcontainer. Vervolgens wordt het bestand emp.txt gekopieerd van de invoermap naar de uitvoermap. 

1. Selecteer in de Azure-portal op de pagina **Containers** de optie **Vernieuwen** om de uitvoermap weer te geven. 

2. Selecteer **Uitvoer** in de lijst met mappen.

3. Controleer of het bestand **emp.txt** naar de uitvoermap is gekopieerd. 

    :::image type="content" source="media/quickstart-create-data-factory-resource-manager-template/data-factory-arm-template-output.png" alt-text="Uitvoer":::

## <a name="clean-up-resources"></a>Resources opschonen

De resources die u hebt gemaakt in de Quick Start kunt u op twee manieren opschonen. U kunt [de Azure-resourcegroep verwijderen](../azure-resource-manager/management/delete-resource-group.md), met alle resources uit de resourcegroep. Als u de andere resources intact wilt houden, verwijdert u alleen de data factory die u in deze zelfstudie hebt gemaakt.

Als u een resourcegroep verwijdert, worden alle resources die deze bevat, met inbegrip van de data factory's, ook verwijderd. Voer de volgende opdracht uit om de gehele resourcegroep te verwijderen: 

```azurepowershell-interactive
Remove-AzResourceGroup -ResourceGroupName $resourcegroupname
```

Als u alleen de data factory wilt verwijderen en niet de gehele resourcegroep, moet u de volgende opdracht uitvoeren: 

```azurepowershell-interactive
Remove-AzDataFactoryV2 -Name $dataFactoryName -ResourceGroupName $resourceGroupName
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een Azure Data Factory gemaakt met behulp van een ARM-sjabloon en de implementatie gevalideerd. Als u meer wilt weten over Azure Data Factory en Azure Resource Manager, vindt u meer informatie in de onderstaande artikelen.

- [Documentatie voor Azure Data Factory](index.yml)
- Meer informatie over [Azure Resource Manager](../azure-resource-manager/management/overview.md)
- Andere [Azure Data Factory ARM-sjablonen](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Datafactory&pageNumber=1&sort=Popular) ophalen