---
title: 'Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie'
titleSuffix: Azure Machine Learning
description: Train het machine learning-model om de prijzen van auto's te voorspellen met behulp van lineaire regressie. Deze zelfstudie is deel één van een serie van twee.
author: peterclu
ms.author: peterlu
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 09/28/2020
ms.custom: designer
ms.openlocfilehash: 2e46da1657a7473d3ff8cdf4ac43e5341b143455
ms.sourcegitcommit: ab829133ee7f024f9364cd731e9b14edbe96b496
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/28/2020
ms.locfileid: "97796105"
---
# <a name="tutorial-predict-automobile-price-with-the-designer"></a>Zelfstudie: Autoprijzen voorspellen met de ontwerpfunctie


In deze tweedelige zelfstudie leert u hoe u het Azure Machine Learning-ontwerpprogramma gebruikt om een machine learning-model te trainen en te implementeren waarmee de prijs van een willekeurige auto kan worden voorspeld. De ontwerpfunctie is een slepen-en-neerzettenprogramma waarmee u machine learning-modellen kunt maken zonder ook maar één regel code.

In deel 1 van de zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Een nieuwe pijplijn maken.
> * Gegevens importeren.
> * Gegevens voorbereiden.
> * Een machine learning-model trainen.
> * Een machine learning-model evalueren.

In [deel 2](tutorial-designer-automobile-price-deploy.md) van de zelfstudie gaat u uw model implementeren als een realtime deductie-eindpunt om de prijs van een willekeurige auto te voorspellen op basis van technische specificaties die u naar het eindpunt stuurt. 

> [!NOTE]
>Een voltooide versie van deze zelfstudie is als voorbeeldpijplijn beschikbaar.
>
>Ga naar de ontwerpfunctie in uw werkruimte om deze te zoeken. In de sectie **Nieuwe pijplijn** selecteert u **Voorbeeld 1 - Regressie: Voorspelling van de autoprijs (basis)** .

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-new-pipeline"></a>Een nieuwe pijplijn maken

Met Azure Machine Learning-pijplijnen kunnen meerdere stappen voor machine learning en gegevensverwerking in één resource worden georganiseerd. Met pijplijnen kunt u complexe machine learning-workflows voor verschillende projecten en gebruikers organiseren, beheren en opnieuw gebruiken.

Als u een Azure Machine Learning-pijplijn wilt maken, hebt u een Azure Machine Learning-werkruimte nodig. In deze sectie leert u hoe u deze beide resources maakt.

### <a name="create-a-new-workspace"></a>Een nieuwe werkruimte maken

Als u de ontwerpfunctie wilt gebruiken, hebt u een Azure Machine Learning-werkruimte nodig. De werkruimte is de resource op het hoogste niveau voor Azure Machine Learning en biedt een gecentraliseerde werkplek met alle artefacten die u in Azure Machine Learning maakt. Zie [Azure Machine Learning-werkruimten maken en beheren](how-to-manage-workspace.md) voor instructies over het maken van een werkruimte.

> [!NOTE]
> Als uw werkruimte gebruikmaakt van een virtueel netwerk, zijn er aanvullende configuratiestappen om de ontwerpfunctie te kunnen gebruiken. Zie [Azure Machine Learning Studio gebruiken in een virtueel Azure-netwerk](how-to-enable-studio-virtual-network.md) voor meer informatie

### <a name="create-the-pipeline"></a>Maak de pijplijn

1. Meld u aan op <a href="https://ml.azure.com?tabs=jre" target="_blank">ml.azure.com</a> en selecteer de werkruimte waar u mee wilt werken.

1. Selecteer **Designer**.

    ![Schermopname van de visuele werkruimte met instructies voor toegang tot de ontwerpfunctie](./media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Selecteer **Gebruiksvriendelijke vooraf gebouwde modules**.

1. Selecteer bovenaan het canvas de standaardpijplijnnaam **Pipeline-Created-on**. Wijzig de naam in *Voorspelling van de autoprijs*. De naam hoeft niet uniek te zijn.

## <a name="set-the-default-compute-target"></a>Het standaardrekendoel instellen

Een pijplijn wordt uitgevoerd op een rekendoel. Dit is een rekenresource die aan uw werkruimte is gekoppeld. Nadat u een rekendoel hebt gemaakt, kunt u dit voor toekomstige uitvoeringen opnieuw gebruiken.

U kunt een **standaardrekendoel** voor de gehele pijplijn instellen. Hierdoor geeft u aan dat voor elke module standaard hetzelfde rekendoel moet worden gebruikt. U kunt echter ook rekendoelen per module opgeven.

1. Naast de pijplijnnaam selecteert u het **tandwielpictogram** ![Schermopname van het tandwielpictogram](./media/tutorial-designer-automobile-price-train-score/gear-icon.png) bovenaan het canvas om het deelvenster **Instellingen** te openen.

1. In het deelvenster **Instellingen** rechts naast het canvas selecteert u **Rekendoel selecteren**.

    Als u al een beschikbaar rekendoel hebt, kunt u dit selecteren om deze pijplijn uit te voeren.

    > [!NOTE]
    > Met de ontwerpfunctie kunnen alleen trainingsexperimenten worden uitgevoerd op rekenexemplaren van Azure Machine Learning Compute maar andere rekendoelen worden niet weergegeven.

1. Voer een naam in voor de rekenresource.

1. Selecteer **Opslaan**.

    > [!NOTE]
    > Het duurt ongeveer vijf minuten om een rekenresource te maken. Nadat de resource is gemaakt, kunt u dit opnieuw gebruiken en deze wachttijd voor toekomstige uitvoeringen overslaan.
    >
    > De schaal van de rekenresource wordt automatisch aangepast naar nul knooppunten wanneer deze inactief is, om kosten te besparen. Wanneer u de resource na een vertraging opnieuw nodig hebt, duurt het mogelijk ongeveer vijf minuten voordat de schaal weer terug is gezet.

## <a name="import-data"></a>Gegevens importeren

Er zijn diverse voorbeeldgegevenssets meegeleverd in de ontwerpfunctie waarmee u kunt experimenteren. Voor deze zelfstudie gebruikt u **Automobile price data (Raw)** . 

1. Aan de linkerkant van het pijplijncanvas ziet u een palet met gegevenssets en modules. Selecteer **Voorbeeldgegevenssets** om de beschikbare voorbeeldgegevenssets te bekijken.

1. Selecteer de gegevensset **Automobile price data (Raw)** en sleep deze naar het canvas.

   ![Gegevens naar het canvas slepen](./media/tutorial-designer-automobile-price-train-score/drag-data.gif)

### <a name="visualize-the-data"></a>De gegevens visualiseren

U kunt de gegevens visualiseren voor meer informatie over de gegevensset die u gebruikt.

1. Klik met de rechtermuisknop **Automobile price data (Raw)** en selecteer **Visualiseren**.

1. Selecteer de verschillende kolommen in het gegevensvenster om informatie over elke kolom weer te geven.

    Elke auto wordt weergegeven als een rij. De variabelen die aan elke auto zijn gekoppeld, worden weergegeven als kolommen. Er zijn 205 rijen en 26 kolommen in deze gegevensset.

## <a name="prepare-data"></a>Gegevens voorbereiden

Voor gegevenssets moet u doorgaans enige verwerking vooraf uitvoeren voordat u de analyse kunt uitvoeren. U hebt wellicht al gezien dat er een aantal waarden ontbreekt toen u de gegevensset inspecteerde. Deze ontbrekende waarden moeten worden opgeschoond, zodat de gegevens correct kunnen worden geanalyseerd.

### <a name="remove-a-column"></a>Een kolom verwijderen

Wanneer u een model traint, moet u iets doen aan de ontbrekende gegevens. In deze gegevensset ontbreken veel waarden in de kolom **normalized-losses**. Deze kolom gaat u dus helemaal uit het model verwijderen.

1. In het modulepalet aan de linkerkant van het canvas vouwt u de sectie **Gegevenstransformatie** uit en zoekt u de module **Select Columns in Dataset**.

1. Sleep de module **Select Columns in Dataset** naar het canvas. Zet de module onder de gegevenssetmodule neer.

1. Koppel de gegevensset **Automobile price data (Raw)** aan de module **Select Columns in Dataset**. Sleep van de uitvoerpoort van de gegevensset (de kleine cirkel onderaan de gegevensset op het canvas) naar de invoerpoort van **Select Columns in Dataset** (de kleine cirkel bovenaan de module).

    > [!TIP]
    > U maakt een gegevensstroom via uw pijplijn wanneer u de uitvoerpoort van de ene module wilt koppelen aan een invoerpoort van de andere module.
    >

    ![Modules koppelen](./media/tutorial-designer-automobile-price-train-score/connect-modules.gif)

1. Selecteer de module **Select Columns in Dataset**.

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u **Kolom bewerken**.

1. Vouw de vervolgkeuzelijst **Kolomnamen** naast **Invoegen** uit en selecteer **Alle kolommen**.

1. Selecteer de **+** om een nieuwe regel toe te voegen.

1. Bij de vervolgkeuzemenu's selecteert u **Uitsluiten** en **Kolomnamen**.
    
1. Voer *normalized-losses* in het tekstvak in.

1. Rechtsonder selecteert u **Opslaan** om de kolomselector te sluiten.

    ![Een kolom uitsluiten](./media/tutorial-designer-automobile-price-train-score/exclude-column.png)

1. Selecteer de module **Select Columns in Dataset**. 

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u het tekstvak **Opmerking** en voert u *Genormaliseerde verliezen uitsluiten* in.

    Opmerkingen worden op de grafiek weergegeven om uw pijplijn te helpen organiseren.

### <a name="clean-missing-data"></a>Ontbrekende gegevens opschonen

Er ontbreken nog steeds waarden in uw gegevensset nadat u de kolom **normalized-losses** hebt verwijderd. U kunt de resterende ontbrekende gegevens verwijderen aan de hand van de module **Clean Missing Data**.

> [!TIP]
> Voor de meeste modules geldt dat voor het gebruik van de module de ontbrekende invoergegevens moeten worden opgeschoond met de ontwerpfunctie.

1. In het modulepalet aan de linkerkant van het canvas vouwt u de sectie **Gegevenstransformatie** uit en zoekt u de module **Clean Missing Data**.

1. Sleep de module **Clean Missing Data** naar het pijplijncanvas. Koppel deze module aan de module **Select Columns in Dataset**. 

1. Selecteer de module **Clean Missing Data**.

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u **Kolom bewerken**.

1. In het venster **Kolommen die moeten worden gewist** dat wordt weergegeven, vouwt u het vervolgkeuzemenu naast **Invoegen** uit. Selecteer **Alle kolommen**

1. Selecteer **Opslaan**

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u **Gehele rij verwijderen** onder **Wismodus**.

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u het vak **Opmerking** en voert u *Rijen met ontbrekende waarden verwijderen* in. 

    Uw pijplijn ziet er als volgt uit:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-clean.png"alt-text="Selecteer-kolom":::

## <a name="train-a-machine-learning-model"></a>Een machine learning-model trainen

Nu u over de modules beschikt om de gegevens te verwerken, kunt u de trainingsmodules instellen.

Omdat u de prijs wilt voorspellen, wat een getal is, gebruikt u een regressiealgoritme. In dit voorbeeld gebruikt u een lineair regressiemodel.

### <a name="split-the-data"></a>De gegevens splitsen

Het splitsen van gegevens is een algemene taak bij machine learning. U splitst uw gegevens in twee afzonderlijke gegevenssets. Met de ene gegevensset wordt het model getraind en met de andere gegevensset wordt getest hoe goed het model presteert.

1. Vouw in het modulepalet de sectie **Gegevenstransformatie** uit en zoek de module **Split Data**.

1. Sleep de module **Split Data** naar het pijplijncanvas.

1. Koppel de linkerpoort van de module **Clean Missing Data** aan de module **Split Data**.

    > [!IMPORTANT]
    > Controleer of de linkeruitvoerpoort van **Clean Missing Data** inderdaad aan **Split Data** wordt gekoppeld. De linkerpoort bevat de opgeschoonde gegevens. De rechterpoort bevat de verwijderde gegevens.

1. Selecteer de module **Split Data**.

1. In het deelvenster Details van de module aan de rechterkant van het canvas stelt u het **gedeelte van rijen in de eerste uitvoergegevensset** in op 0,7.

    Met deze optie wordt 70% van de gegevens gebruikt om het model te trainen en 30% om het model te testen. De gegevensset van 70% is toegankelijk via de linkeruitvoerpoort. De resterende gegevens zijn beschikbaar via de rechteruitvoerpoort.

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u het vak **Opmerking** en voert u *De gegevensset splitsen in een trainingsset (0,7) en een testset (0,3)* in.

### <a name="train-the-model"></a>Het model trainen

Train het model door hiervoor een gegevensset te gebruiken waarin de prijs een van de elementen is. Het algoritme bouwt een model waarmee de relatie tussen de functies en de prijs wordt uitgelegd, zoals dat uit de trainingsgegevens blijkt.

1. In het modulepalet vouwt u **Machine learning-algoritmen** uit.
    
    Met deze optie worden diverse categorieën van modules weergegeven die u kunt gebruiken om leeralgoritmen te initialiseren.

1. Selecteer **Regression** > **Linear Regression** en sleep dit naar het pijplijncanvas.

1. In het modulepalet vouwt u de sectie **Module training** uit en sleept u de module **Train Model** naar het canvas.

1. Koppel de uitvoer van de module **Linear Regression** aan de linkerinvoer van de module **Train Model**.

1. Koppel de trainingsgegevensuitvoer (linkerpoort) van de module **Split Data** aan de rechterinvoer van de module **Train Model**.
    
    > [!IMPORTANT]
    > Controleer of de linkeruitvoerpoort van **Split Data** inderdaad aan **Train Model** wordt gekoppeld. De linkerpoort bevat de trainingsset. De rechterpoort bevat de testset.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-model.png"alt-text="Schermopname met de juiste configuratie van de module Train Model. De module Linear Regression wordt gekoppeld aan de linkerpoort van de module Train Model en de module Split Data wordt gekoppeld aan de rechterpoort van Train Model.":::

1. Selecteer de module **Train Model**.

1. In het deelvenster Details van de module aan de rechterkant van het canvas selecteert u de selector **Kolom bewerken**.

1. In het dialoogvenster **Kolom labelen** vouwt u het vervolgkeuzemenu uit en selecteert u **Kolomnamen**. 

1. In het tekstvak voert u *price* in om de waarde op te geven die door uw model gaat worden voorspeld.

    >[!IMPORTANT]
    > Zorg ervoor dat u de kolomnaam exact opgeeft. Gebruik geen hoofdletters in het woord **price**. 

    Uw pijplijn ziet er als volgt uit:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-train-graph.png"alt-text="Schermopname met de juiste configuratie van de pijplijn nadat de module Train Model is toegevoegd.":::

### <a name="add-the-score-model-module"></a>De module Score Model toevoegen

Nadat u uw model hebt getraind aan de hand van 70% van de gegevens, kunt u dit gebruiken om de overige 30% van een score te voorzien, om te zien hoe goed uw model werkt.

1. Voer *Score Model* in het zoekvak in om de module **Score Model** te zoeken. Sleep de module naar het pijplijncanvas. 

1. Koppel de uitvoer van de module **Train Model** aan de linkerinvoerpoort van de module **Score Model**. Koppel de testgegevensuitvoer (rechterpoort) van de module **Split Data** aan de rechterinvoerpoort van de module **Score Model**.

### <a name="add-the-evaluate-model-module"></a>De module Evaluate Model toevoegen

Gebruik de module **Evaluate Model** om te evalueren hoe goed uw model heeft gescoord voor de testgegevensset.

1. Voer *evaluate* in het zoekvak in om de module **Evaluate Model** te zoeken. Sleep de module naar het pijplijncanvas. 

1. Koppel de uitvoer van de module **Score Model** aan de linkerinvoer van **Evaluate Model**. 

    De uiteindelijke pijplijn ziet er als volgt uit:

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/pipeline-final-graph.png"alt-text="Schermopname met de juiste configuratie van de pijplijn.":::

## <a name="submit-the-pipeline"></a>De pijplijn indienen

Nu uw pijplijn helemaal is ingesteld, kunt u een uitvoering van de pijplijn indienen om uw machine learning-model te trainen. U kunt op elk gewenst moment een geldige pijplijnuitvoering indienen. Deze kan worden gebruikt om wijzigingen in uw pijplijn te controleren tijdens de ontwikkeling.

1. Bovenaan het canvas selecteert u **Indienen**.

1. In het dialoogvenster **Pijplijnuitvoering instellen** selecteert u **Nieuwe maken**.

    > [!NOTE]
    > Aan de hand van experimenten kunt u vergelijkbare pijplijnuitvoeringen groeperen. Als u een pijplijn meerdere keren uitvoert, kunt u hetzelfde experiment selecteren voor achtereenvolgende uitvoeringen.

    1. Voer **Zelfstudie-CarPrices** in voor **Naam van nieuw experiment**.

    1. Selecteer **Indienen**.
    
    U kunt de uitvoeringsstatus en details rechtsboven in het canvas zien.
    
    Als dit de eerste keer is dat u de pijplijn uitvoert, kan het maximaal 20 minuten duren voor uw pijplijn helemaal is uitgevoerd. De standaardrekeninstellingen hebben een minimale knooppuntgrootte van 0, wat betekent dat de ontwerpfunctie na inactiviteit resources moet toewijzen. Herhaalde pijplijnuitvoeringen kosten minder tijd, omdat de rekenresources al zijn toegewezen. Bovendien gebruikt de ontwerpfunctie in de cache opgeslagen resultaten voor elke module om de efficiëntie verder te verbeteren.

### <a name="view-scored-labels"></a>Gescoorde labels weergeven

Nadat de uitvoering is voltooid, kunt u de resultaten van de pijplijnuitvoering weergeven. Kijk eerst naar de voorspellingen die door het regressiemodel zijn gegenereerd.

1. Klik met de rechtermuisknop op de module **Score Model** en selecteer **Visualiseren** om de uitvoer weer te geven.

    Hier ziet u de voorspelde prijzen en de daadwerkelijke prijzen van de testgegevens.

    :::image type="content" source="./media/tutorial-designer-automobile-price-train-score/score-result.png"alt-text="Schermopname van de uitvoervisualisatie, waarin de kolom Gescoorde label is gemarkeerd":::

### <a name="evaluate-models"></a>Modellen evalueren

Gebruik **Evaluate Model** om te zien hoe goed het getrainde model heeft gepresteerd met de testgegevensset.

1. Klik met de rechtermuisknop op de module **Evaluate Model** en selecteer **Visualiseren** om de uitvoer weer te geven.

De volgende statistieken worden weergegeven voor uw model:

* **Gemiddelde absolute fout** (Mean Absolute Error of MAE): Het gemiddelde van de absolute fouten. Een fout is het verschil tussen de voorspelde waarde en de daadwerkelijke waarde.
* **Standaardafwijking** (Root Mean Squared Error of RMSE): De vierkantswortel uit het gemiddelde aan kwadratische fouten voor de voorspellingen op basis van de testgegevensset.
* **Relatieve absolute fout**: Het gemiddelde aan absolute fouten ten opzichte van het absolute verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Relatieve kwadratische fout**: Het gemiddelde aan kwadratische fouten ten opzichte van het kwadratische verschil tussen de werkelijke waarden en het gemiddelde van alle werkelijke waarden.
* **Determinatiecoëfficiënt**: Deze statische meetwaarde, ook wel R²-waarde genoemd, geeft aan hoe goed het model is in het voorspellen van de gegevens.

Voor elk van de foutstatistieken geldt: hoe kleiner hoe beter. Een lagere waarde geeft aan dat de voorspellingen dichter bij de daadwerkelijke waarden liggen. Hoe dichter de determinatiecoëfficiënt bij één (1,0) ligt, hoe beter de voorspellingen.

## <a name="clean-up-resources"></a>Resources opschonen

Sla deze sectie over als u door wilt gaan met deel 2 van de zelfstudie [Modellen implementeren](tutorial-designer-automobile-price-deploy.md).

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deel 2 leert u hoe u uw model kunt implementeren als een realtime-eindpunt.

> [!div class="nextstepaction"]
> [Doorgaan met het implementeren van modellen](tutorial-designer-automobile-price-deploy.md)
