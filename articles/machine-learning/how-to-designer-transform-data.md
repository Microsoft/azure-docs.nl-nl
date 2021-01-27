---
title: Gegevens transformeren in de ontwerp functie
titleSuffix: Azure Machine Learning
description: Meer informatie over het importeren en transformeren van gegevens in Azure Machine Learning Designer om uw eigen gegevens sets te maken.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
author: peterclu
ms.author: peterlu
ms.date: 06/28/2020
ms.topic: conceptual
ms.custom: how-to, designer
ms.openlocfilehash: 70f5e17c4cc42201e9aa3d36c9937f6ceb9527d0
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98880200"
---
# <a name="transform-data-in-azure-machine-learning-designer"></a>Gegevens transformeren in Azure Machine Learning Designer


In dit artikel leert u hoe u gegevens sets transformeert en opslaat in Azure Machine Learning Designer, zodat u uw eigen gegevens kunt voorbereiden voor machine learning.

U gebruikt het voor beeld van de gegevensset [inkomsten binaire classificatie voor volwassenen](./samples-designer.md) om twee gegevens sets voor te bereiden: één gegevensset met informatie over de optelling van de volwassene van alleen de Verenigde Staten en een andere gegevensset die informatie over de telling van niet-Amerikaanse volwassenen bevat.

In dit artikel leert u het volgende:

1. Transformeer een gegevensset om deze voor te bereiden voor de training.
1. Exporteer de resulterende gegevens sets naar een gegevens opslag.
1. Resultaten weer geven.

Deze procedure is een vereiste voor het artikel over het opnieuw [trainen van Designer-modellen](how-to-retrain-designer.md) . In dat artikel leert u hoe u de getransformeerde gegevens sets kunt gebruiken om meerdere modellen met pijplijn parameters te trainen.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="transform-a-dataset"></a>Een gegevensset transformeren

In deze sectie vindt u informatie over het importeren van de voor beeld-gegevensset en het splitsen van de gegevens in de data sets Verenigde Staten en niet-Amerikaanse. Zie [gegevens importeren](how-to-designer-import-data.md)voor meer informatie over het importeren van uw eigen gegevens in de ontwerp functie.

### <a name="import-data"></a>Gegevens importeren

Gebruik de volgende stappen om de voor beeld-gegevensset te importeren.

1. Meld u aan op <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> en selecteer de werkruimte waar u mee wilt werken.

1. Ga naar de ontwerp functie. Selecteer **eenvoudig te gebruiken-modules voor prebuild** om een nieuwe pijp lijn te maken.

1. Selecteer een standaard Compute-doel om de pijp lijn uit te voeren.

1. Aan de linkerkant van het pijplijncanvas ziet u een palet met gegevenssets en modules. Selecteer **Gegevenssets**. Bekijk vervolgens de sectie voor **beelden** .

1. Sleep en zet de gegevensset voor de **binaire inkomsten van de volwassene** af op het canvas.

1. Klik met de rechter muisknop op de module inkomsten gegevensset voor **volwassenen** en selecteer de uitvoer van de gegevensset **visualiseren**  >  

1. Gebruik het voorbeeld venster voor gegevens om de gegevensset te verkennen. Let op de kolom waarden "native-Country".

### <a name="split-the-data"></a>De gegevens splitsen

In deze sectie gebruikt u de [gesplitste gegevens module](algorithm-module-reference/split-data.md) om rijen te identificeren en te splitsen die ' Verenigde Staten ' bevatten in de kolom ' native land '. 

1. Vouw in het module palet links van het canvas de sectie **gegevens transformatie** uit en zoek de module **Split data** .

1. Sleep de module **Split data** naar het canvas en verwijder de module onder de module gegevensset.

1. Verbind de module gegevensset met de module **Split data** .

1. Selecteer de module **Split Data**.

1. Stel in het deel venster module details rechts van het canvas de **Splits modus** in op **reguliere expressie**.

1. Voer de **reguliere expressie** in: `\"native-country" United-States` .

    In de **reguliere expressie** modus wordt één kolom voor een waarde getest. Meer informatie over de module Split data kunt u vinden op de [pagina verwante algoritme module Reference](algorithm-module-reference/split-data.md).

Uw pijplijn ziet er als volgt uit:

:::image type="content" source="./media/how-to-designer-transform-data/split-data.png"alt-text="Scherm afbeelding die laat zien hoe u de pijp lijn en de module Split data configureert":::


## <a name="save-the-datasets"></a>De gegevens sets opslaan

Nu de pijp lijn is ingesteld voor het splitsen van de gegevens, moet u opgeven waar u wilt persistent maken van de data sets. Voor dit voor beeld gebruikt u de module **gegevens exporteren** om uw gegevensset op te slaan in een gegevens opslag. Zie [verbinding maken met Azure Storage-services](how-to-access-data.md) voor meer informatie over gegevens opslag.

1. Vouw in het module palet links van het canvas de sectie **gegevens invoer en uitvoer** uit en zoek de module **gegevens exporteren** .

1. Slepen en neerzetten twee **gegevens modules exporteren** onder de module **Split data** .

1. Koppel elke uitvoer poort van de module **Split data** aan een andere module voor **export gegevens** .

    De pijp lijn moet er ongeveer als volgt uitzien:

    ![Scherm afbeelding die laat zien hoe u verbinding maakt met de gegevens modules voor exporteren](media/how-to-designer-transform-data/export-data-pipeline.png).

1. Selecteer de module **gegevens exporteren** die is verbonden met de meest *linkse* poort van de module **Split data** .

    De volg orde van de uitvoer poorten voor de module **Split data** . De eerste uitvoer poort bevat de rijen waar de reguliere expressie waar is. In dit geval bevat de eerste poort rijen voor Amerikaanse inkomsten en de tweede poort bevat rijen voor inkomsten op basis van een niet-Amerikaanse.

1. Stel in het detail venster van de module rechts van het canvas de volgende opties in:
    
    **Gegevens opslag type**: Azure Blob Storage

    **Data Store**: Selecteer een bestaande gegevens opslag of selecteer nieuwe gegevens opslag om er nu een te maken.

    **Pad**: `/data/us-income`

    **Bestands indeling**: CSV

    > [!NOTE]
    > In dit artikel wordt ervan uitgegaan dat u toegang hebt tot een gegevens opslag die is geregistreerd in de huidige Azure Machine Learning-werk ruimte. Zie [verbinding maken met Azure Storage-services](how-to-connect-data-ui.md#create-datastores)voor instructies over het instellen van een gegevens opslag.

    Als u geen gegevens opslag hebt, kunt u er nu een maken. Voor beeld: in dit artikel worden de gegevens sets opgeslagen in het standaard-Blob Storage-account dat is gekoppeld aan de werk ruimte. De gegevens sets worden opgeslagen in de `azureml` container in een nieuwe map met de naam `data` .

1.  Selecteer de module **gegevens exporteren** die zijn verbonden met de meest *rechtse* poort van de module **Split data** .

1. Stel in het detail venster van de module rechts van het canvas de volgende opties in:
    
    **Gegevens opslag type**: Azure Blob Storage

    **Data Store**: Selecteer dezelfde gegevens opslag als hierboven

    **Pad**: `/data/non-us-income`

    **Bestands indeling**: CSV

1. Controleer of de module voor het **exporteren van gegevens** die is verbonden met de linker poort van de **gesplitste gegevens** , het **pad** heeft `/data/us-income` .

1. Controleer of de **export gegevens** module die is verbonden met de juiste poort het **pad** heeft `/data/non-us-income` .

    De pijp lijn en instellingen moeten er als volgt uitzien:
    
    ![Scherm afbeelding die laat zien hoe de export gegevens modules worden geconfigureerd](media/how-to-designer-transform-data/us-income-export-data.png).

### <a name="submit-the-run"></a>De uitvoering verzenden

Nu de pijp lijn is ingesteld voor het splitsen en exporteren van de gegevens, verzendt u een pijplijn uitvoering.

1. Bovenaan het canvas selecteert u **Indienen**.

1. Selecteer in het dialoog venster **pijplijn uitvoering instellen** de optie **nieuwe maken** om een experiment te maken.

    Experimenten groeperen gerelateerde pijplijn uitvoeringen logisch gegroepeerd. Als u deze pijp lijn in de toekomst uitvoert, moet u hetzelfde experiment gebruiken voor logboek-en tracerings doeleinden.

1. Geef een beschrijvende naam op voor het experiment, zoals ' Split-tellingen-gegevens '.

1. Selecteer **Indienen**.

## <a name="view-results"></a>Resultaten weergeven

Nadat de pijp lijn is voltooid, kunt u uw resultaten weer geven door te navigeren naar de Blob-opslag in de Azure Portal. U kunt ook de intermediaire resultaten van de module **Split data** weer geven om te bevestigen dat uw gegevens correct zijn gesplitst.

1. Selecteer de module **Split Data**.

1. In de deelvenster Details van de module, rechts van het canvas, selecteert u **Uitvoer en logboeken**. 

1. Selecteer het pictogram visualiseren pictogram visualiseren ![ ](media/how-to-designer-transform-data/visualize-icon.png) naast **resultaten dataset1**. 

1. Controleer of de kolom ' systeem eigen land ' alleen de waarde ' Verenigde Staten ' bevat.

1. Selecteer het pictogram visualiseren pictogram visualiseren ![ ](media/how-to-designer-transform-data/visualize-icon.png) naast **resultaten dataset2**. 

1. Controleer of de kolom ' systeem eigen land ' niet de waarde ' Verenigde Staten ' bevat.

## <a name="clean-up-resources"></a>Resources opschonen

Sla deze sectie over als u wilt door gaan met deel 2 van deze procedure om [modellen met Azure machine learning Designer](how-to-retrain-designer.md)opnieuw te trainen.

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een gegevensset kunt transformeren en opslaat in een geregistreerde gegevens opslag.

Ga verder met het volgende deel van deze instructie-to-serie met [Retrain modellen met Azure machine learning Designer](how-to-retrain-designer.md) om uw getransformeerde gegevens sets en pijplijn parameters te gebruiken om machine learning modellen te trainen.