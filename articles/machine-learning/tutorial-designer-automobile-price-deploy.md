---
title: 'Zelfstudie: ML-modellen implementeren met de ontwerpfunctie'
titleSuffix: Azure Machine Learning
description: Bouw een predictive analytics-oplossing in de Azure Machine Learning-ontwerpfunctie. Ontdek hoe u een machine learning-model traint, een score geeft en implementeert met behulp van slepen-en-neerzettenmodules.
author: likebupt
ms.author: keli19
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: tutorial
ms.date: 11/25/2020
ms.custom: designer
ms.openlocfilehash: 14be695f2f58b9738af11a3d2ca3f06592a1cc6e
ms.sourcegitcommit: 16c7fd8fe944ece07b6cf42a9c0e82b057900662
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96575955"
---
# <a name="tutorial-deploy-a-machine-learning-model-with-the-designer"></a>Zelfstudie: Een Machine Learning-model implementeren met de ontwerpfunctie


U kunt het voorspellende model dat is ontwikkeld in [deel 1 van de zelfstudie](tutorial-designer-automobile-price-train-score.md) implementeren zodat anderen het ook kunnen gebruiken. In deel een hebt u het model getraind. Nu gaat u nieuwe voorspellingen genereren op basis van gebruikersinvoer. In dit deel van de zelfstudie komen deze onderwerpen aan bod:

> [!div class="checklist"]
> * Een realtime deductiepijplijn maken.
> * Een deductiecluster maken.
> * Het realtime-eindpunt implementeren.
> * Het realtime-eindpunt testen.

## <a name="prerequisites"></a>Vereisten

Voltooi [deel 1 van de zelfstudie](tutorial-designer-automobile-price-train-score.md) om te leren hoe u een machine learning-model kunt trainen en een score kunt geven in de ontwerpfunctie.

[!INCLUDE [machine-learning-missing-ui](../../includes/machine-learning-missing-ui.md)]

## <a name="create-a-real-time-inference-pipeline"></a>Een realtime deductiepijplijn maken

Voor het implementeren van uw pijplijn moet u de trainingspijplijn eerst converteren naar een realtime deductiepijplijn. Tijdens dit proces worden trainingsmodules verwijderd en de invoer en uitvoer van webservices toegevoegd voor het afhandelen van aanvragen.

### <a name="create-a-real-time-inference-pipeline"></a>Een realtime deductiepijplijn maken

1. Selecteer boven het pijplijncanvas **Deductiepijplijn maken** > **Realtime deductiepijplijn**.

    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/tutorial2-create-inference-pipeline.png"alt-text="Schermopname waarin wordt weergegeven waar de knop pijplijn maken zich bevindt":::

    Uw pijplijn ziet er nu als volgt uit: 

   ![Schermopname van de verwachte configuratie van de pijplijn nadat deze is voorbereid voor implementatie](./media/tutorial-designer-automobile-price-deploy/real-time-inference-pipeline.png)

    Wanneer u **Deductiepijplijn maken** selecteert, gebeuren er verschillende dingen:
    
    * Het getrainde model wordt opgeslagen als een **Gegevensset** module in het palet van de module. U vindt deze onder **Mijn gegevenssets**.
    * Trainingsmodules zoals **Model trainen** en **Gegevens splitsen** worden verwijderd.
    * Het opgeslagen getrainde model wordt weer toegevoegd aan de pijplijn.
    * De modules **Webservice-invoer** en **Webservice-uitvoer** worden toegevoegd. Deze modules laten zien waar gebruikersgegevens de pijplijn binnenkomen en waar gegevens worden geretourneerd.

    > [!NOTE]
    > Standaard verwacht de **Webservice-invoer** hetzelfde gegevensschema als de trainingsgegevens die zijn gebruikt voor het maken van de voorspellende pijplijn. In dit scenario is de prijs in het schema opgenomen. De prijs wordt echter niet gebruikt als een factor tijdens de voorspelling.
    >

1. Selecteer **Verzenden** en gebruik hetzelfde rekendoel en experiment dat u in deel 1 hebt gebruikt.

    Als dit de eerste keer is dat u de pijplijn uitvoert, kan het maximaal 20 minuten duren voor uw pijplijn helemaal is uitgevoerd. De standaardrekeninstellingen hebben een minimale knooppuntgrootte van 0, wat betekent dat de ontwerpfunctie na inactiviteit resources moet toewijzen. Herhaalde pijplijnuitvoeringen kosten minder tijd, omdat de rekenresources al zijn toegewezen. Bovendien gebruikt de ontwerpfunctie in de cache opgeslagen resultaten voor elke module om de efficiëntie verder te verbeteren.

1. Selecteer **Implementeren**.

## <a name="create-an-inferencing-cluster"></a>Een deductiecluster maken

In het dialoogvenster dat wordt weergegeven kunt u willekeurige Azure Kubernetes Service (AKS)-clusters selecteren om uw model naar te implementeren. Als u geen AKS-cluster hebt, gebruikt u de volgende stappen om er een te maken.

1. Selecteer **Compute** in het dialoogvenster dat wordt weergegeven om naar de pagina **Compute** te gaan.

1. Selecteer **Deductieclusters** >  **+ Nieuw** op het navigatielint.

    ![Schermopname waarin wordt weergegeven waar het deelvenster nieuwe deductiecluster zich bevindt](./media/tutorial-designer-automobile-price-deploy/new-inference-cluster.png)
   
1. Configureer een nieuwe Kubernetes Service in het deelvenster Deductiecluster.

1. Geef *aks-compute* op als **Compute-naam**.
    
1. Selecteer een regio in de buurt die beschikbaar is voor de **Regio**.

1. Selecteer **Maken**.

    > [!NOTE]
    > Het duurt ongeveer 15 minuten om een AKS-service te maken. U kunt de inrichtingsstatus bekijken op de pagina **Deductieclusters**.
    >

## <a name="deploy-the-real-time-endpoint"></a>Het realtime-eindpunt implementeren

Wanneer de inrichting van uw AKS-service is voltooid, gaat u terug naar de realtime deductiepijplijn om de implementatie te voltooien.

1. Selecteer **Implementeren** boven het canvas.

1. Selecteer **Nieuw realtime-eindpunt implementeren**. 

1. Selecteer de AKS-cluster die u hebt gemaakt.

1. Selecteer **Implementeren**.
    
    :::image type="content" source="./media/tutorial-designer-automobile-price-deploy/setup-endpoint.png"alt-text="Schermopname waarin het instellen van een nieuw realtime-eindpunt wordt weergegeven":::

    Nadat de implementatie is voltooid, wordt een melding boven het canvas weergegeven. Dit kan enkele minuten duren.

## <a name="view-the-real-time-endpoint"></a>Het realtime-eindpunt bekijken

Nadat de implementatie is voltooid, kunt u uw realtime-eindpunt bekijken door naar de pagina **Eindpunten** te gaan.

1. Selecteer op de pagina **Eindpunten** het eindpunt dat u hebt geïmplementeerd.

1. Op het tabblad **Details** kunt u meer informatie zien, zoals de REST URI, status en tags.

1. Op het tabblad **Gebruiken** kunt u beveiligingssleutels vinden en verificatiemethoden instellen.

1. Op het tabblad **Implementatielogboeken** vindt u de gedetailleerde implementatielogboeken van uw realtime-eindpunt. 

Zie [Een Azure Machine Learning-model gebruiken dat als een webservice is geïmplementeerd](how-to-consume-web-service.md) voor meer informatie over het gebruiken van uw webservice

## <a name="clean-up-resources"></a>Resources opschonen

[!INCLUDE [aml-ui-cleanup](../../includes/aml-ui-cleanup.md)]

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie hebt u de belangrijkste stappen geleerd voor het maken, implementeren en gebruiken van een machine learning-model in de ontwerpfunctie. Bekijk de volgende koppelingen voor meer informatie over hoe u de ontwerpfunctie kunt gebruiken:

+ [Voorbeelden van ontwerpfunctie](samples-designer.md): Meer informatie over het gebruik van de ontwerpfunctie om andere typen problemen op te lossen.
+ [Azure Machine Learning Studio gebruiken in een virtueel Azure-netwerk](how-to-enable-studio-virtual-network.md).