---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning-opnieuw samen stellen van webservice'
description: Studio-webservices als pijplijn eindpunten opnieuw samen stellen in Azure Machine Learning
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: 5e467d22cc3230bd9945fb276dc16cf1fa765bb6
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103565005"
---
# <a name="rebuild-a-studio-classic-web-service-in-azure-machine-learning"></a>Een studio-webservice (klassiek) opnieuw bouwen in Azure Machine Learning

In dit artikel leert u hoe u een studio-webservice opnieuw bouwt als een **eind punt** in azure machine learning.

Gebruik Azure Machine Learning pijplijn eindpunten om voor spellingen te doen, modellen opnieuw te trainen of een algemene pijp lijn uit te voeren. Met het REST-eind punt kunt u pijp lijnen uitvoeren vanaf elk platform. 

Dit artikel maakt deel uit van de Studio (klassiek) om de migratie reeks te Azure Machine Learning. Zie het [artikel migratie overzicht](migrate-overview.md)voor meer informatie over het migreren naar Azure machine learning.

> [!NOTE]
> Deze migratie reeks is gericht op de ontwerp functie voor slepen en neerzetten. Zie [machine learning modellen implementeren in azure](../how-to-deploy-and-where.md)voor meer informatie over het programmatisch implementeren van modellen.

## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](../how-to-manage-workspace.md#create-a-workspace)
- Een Azure Machine Learning training-pijp lijn. Zie voor meer informatie [een studio-experiment (klassiek) opnieuw bouwen in azure machine learning](migrate-rebuild-experiment.md).

## <a name="real-time-endpoint-vs-pipeline-endpoint"></a>Real-time eind punt VS pijplijn eindpunt

Studio-webservices zijn vervangen door **eind punten** in azure machine learning. Gebruik de volgende tabel om te kiezen welk eindpunt type u wilt gebruiken:

|Studio-webservice (klassiek)| Azure Machine Learning vervanging
|---|---|
|Aanvraag/reageren-webservice (realtime voor spelling)|Real-time eind punt|
|Batch-webservice (batch voorspelling)|Pijplijn eindpunt|
|RETRAIN web service (opnieuw trainen)|Pijplijn eindpunt| 


## <a name="deploy-a-real-time-endpoint"></a>Een real-time eind punt implementeren

In Studio (klassiek) hebt u een **aanvraag/antwoord-webservice** gebruikt om een model te implementeren voor voor spellingen in realtime. In Azure Machine Learning gebruikt u een **real-time eind punt**.

Er zijn meerdere manieren voor het implementeren van een model in Azure Machine Learning. Een van de eenvoudigste manieren is om de ontwerp functie te gebruiken om het implementatie proces te automatiseren. Gebruik de volgende stappen om een model te implementeren als een real-time-eind punt:

1. Voer uw voltooide trainings pijplijn ten minste één keer uit.
1. Nadat de uitvoering is voltooid, selecteert u aan de bovenkant van het canvas de optie pijp lijn voor inschakeling **pijplijn maken** in  >  **realtime**.

    ![pijp lijn voor realtime-interferentie maken](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    Met de ontwerper wordt de trainings pijplijn omgezet in een real-time pipeline-pijp lijn. Een vergelijk bare conversie vindt ook plaats in Studio (klassiek).

    In de ontwerper registreert de conversie stap ook [het getrainde model naar uw Azure machine learning-werk ruimte](../how-to-deploy-and-where.md#registermodel).

1. Selecteer **verzenden** om de pijp lijn voor realtime-deinterferentie uit te voeren en controleer of deze correct wordt uitgevoerd.

1. Nadat u de pijp lijn voor de deinterferentie hebt gecontroleerd, selecteert u **implementeren**.

1. Voer een naam in voor het eind punt en een reken type.

    In de volgende tabel worden de berekenings opties voor de implementatie in de ontwerp functie beschreven:

    | Rekendoel | Gebruikt voor | Beschrijving | Maken |
    | ----- |  ----- | ----- | -----  |
    |[Azure Kubernetes Service (AKS)](../how-to-deploy-azure-kubernetes-service.md) |Realtime deductie|Grootschalige productie-implementaties. Snelle reactie tijd en automatisch schalen van services.| Gebruiker gemaakt. Zie [Compute-doelen maken](../how-to-create-attach-compute-studio.md#inference-clusters)voor meer informatie. |
    |[Azure Container Instances ](../how-to-deploy-azure-container-instance.md)|Testen of ontwikkelen | Kleinschalige, op CPU gebaseerde workloads die minder dan 48 GB aan RAM-geheugen vereisen.| Automatisch gemaakt door Azure Machine Learning.

### <a name="test-the-real-time-endpoint"></a>Het realtime-eindpunt testen

Nadat de implementatie is voltooid, kunt u meer details bekijken en uw eind punt testen:

1. Ga naar het tabblad **eind punten** .
1. Selecteer een eind punt.
1. Selecteer het tabblad **Testen**.
    
    ![Scherm opname van het tabblad eind punten met de knop Test eindpunt](./media/migrate-rebuild-web-service/test-realtime-endpoint.png)

## <a name="publish-a-pipeline-endpoint-for-batch-prediction-or-retraining"></a>Een pijplijn eindpunt publiceren voor batch voorspelling of retraining

U kunt ook uw trainings pijplijn gebruiken om een **pijplijn eindpunt** te maken in plaats van een real-time-eind punt. Gebruik **pijplijn eindpunten** om batch voorspelling of retraining uit te voeren.

Pijp lijn-eind punten vervangen Studio (klassiek) **batch uitvoerings eindpunten**  en **Retrain Web Services**.

### <a name="publish-a-pipeline-endpoint-for-batch-prediction"></a>Een pijplijn eindpunt publiceren voor batch voorspelling

Het publiceren van een batch Voorspellings eindpunt is vergelijkbaar met het real-time-eind punt.

Gebruik de volgende stappen voor het publiceren van een pijplijn eindpunt voor batch voorspelling:

1. Voer uw voltooide trainings pijplijn ten minste één keer uit.

1. Nadat de uitvoering is voltooid, selecteert u aan de bovenkant van het canvas de optie pijp lijn voor het **afstellen van de pipeline**-  >  **batch** maken.

    ![Scherm opname van de knop voor het maken van een pijp lijn bij een trainings pijplijn](./media/migrate-rebuild-web-service/create-inference-pipeline.png)
        
    De ontwerper zet de trainings pijplijn om in een batch-deinterferentie pijplijn. Een vergelijk bare conversie vindt ook plaats in Studio (klassiek).

    In de ontwerp functie registreert deze stap ook [het getrainde model aan uw Azure machine learning-werk ruimte](../how-to-deploy-and-where.md#registermodel).

1. Selecteer **verzenden** om de batch pijp lijn te gebruiken en te controleren of deze is voltooid.

1. Nadat u de pijp lijn voor de deinterferentie hebt gecontroleerd, selecteert u **publiceren**.
 
1. Maak een nieuw pijplijn eindpunt of selecteer een bestaand eind punt.
    
    Een nieuw pijp lijn-eind punt maakt een nieuw REST-eind punt voor de pijp lijn. 

    Als u een bestaand pijplijn eindpunt selecteert, overschrijft u de bestaande pijp lijn niet. In plaats daarvan Azure Machine Learning versies van elke pijp lijn in het eind punt. U kunt opgeven welke versie moet worden uitgevoerd in uw REST-aanroep. U moet ook een standaard pijplijn instellen als voor de REST-aanroep geen versie is opgegeven.


 ### <a name="publish-a-pipeline-endpoint-for-retraining"></a>Een pijplijn eindpunt publiceren voor het opnieuw trainen

Als u een pipeline-eind punt voor het opnieuw trainen wilt publiceren, moet u al een pijp lijn concept hebben dat een model traint. Zie voor meer informatie over het bouwen van een trainings pijplijn een [Studio-experiment (klassiek) opnieuw bouwen](migrate-rebuild-experiment.md).

Als u het pijplijn eindpunt opnieuw wilt gebruiken voor het opnieuw trainen, moet u een **pijplijn parameter** voor uw invoer gegevensset maken. Zo kunt u uw trainings gegevensset dynamisch instellen, zodat u uw model opnieuw kunt trainen.

Gebruik de volgende stappen om het pipeline-eind punt voor retraining te publiceren:

1. Voer uw trainings pijplijn ten minste één keer uit.
1. Nadat de uitvoering is voltooid, selecteert u de module gegevensset.
1. Selecteer in het deel venster module Details de optie **instellen als pijplijn parameter**.
1. Geef een beschrijvende naam op zoals ' input Dataset '.    

    ![Scherm opname van markering voor het maken van een pijplijn parameter](./media/migrate-rebuild-web-service/create-pipeline-parameter.png)

    Hiermee maakt u een pijplijn parameter voor uw invoer gegevensset. Wanneer u het pipeline-eind punt voor training aanroept, kunt u een nieuwe gegevensset opgeven om het model opnieuw te trainen.

1. Selecteer **Publiceren**.

    ![Scherm opname van de knop publiceren in een trainings pijplijn](./media/migrate-rebuild-web-service/create-retraining-pipeline.png)


## <a name="call-your-pipeline-endpoint-from-the-studio"></a>Bel uw pijplijn eindpunt vanuit Studio

Nadat u een batch-Afleidings-of Retrain-eind punt hebt gemaakt, kunt u uw eind punt rechtstreeks vanuit uw browser aanroepen.

1. Ga naar het tabblad **pijp lijnen** en selecteer **pijplijn eindpunten**.
1. Selecteer het pijplijn eindpunt dat u wilt uitvoeren.
1. Selecteer **Indienen**.

    U kunt para meters voor de pijp lijn opgeven nadat u **verzenden** hebt geselecteerd.

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een studio-webservice (klassiek) opnieuw bouwt in Azure Machine Learning. De volgende stap is het [integreren van uw webservice met client-apps](migrate-rebuild-integrate-with-client-app.md).


Zie de andere artikelen in de migratie reeks Studio (klassiek):

1. [Overzicht migratie](migrate-overview.md).
1. [Gegevensset migreren](migrate-register-dataset.md).
1. [Bouw een studio-trainings pijplijn (klassiek) opnieuw](migrate-rebuild-experiment.md)op.
1. **Een studio-webservice (klassiek) opnieuw bouwen**.
1. [Een Azure machine learning-webservice integreren met client-apps](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R-script migreren](migrate-execute-r-script.md).
