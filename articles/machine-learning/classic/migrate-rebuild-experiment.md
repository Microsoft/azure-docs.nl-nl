---
title: 'ML Studio (klassiek): migreren naar Azure Machine Learning-opnieuw samen stellen experiment'
description: Studio-experimenten (klassiek) in Azure Machine Learning Designer opnieuw bouwen.
services: machine-learning
ms.service: machine-learning
ms.subservice: studio
ms.topic: how-to
author: xiaoharper
ms.author: zhanxia
ms.date: 03/08/2021
ms.openlocfilehash: bb944cb034fdd7cc51648314154a654bc1265533
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "103564965"
---
# <a name="rebuild-a-studio-classic-experiment-in-azure-machine-learning"></a>Een studio-experiment (klassiek) opnieuw bouwen in Azure Machine Learning

In dit artikel leert u hoe u een studio-experiment (klassiek) opnieuw bouwt in Azure Machine Learning. Zie [het artikel migratie overzicht](migrate-overview.md)voor meer informatie over het migreren van Studio (klassiek).

Studio- **experimenten** (klassiek) zijn vergelijkbaar met **pijp lijnen** in azure machine learning. In Azure Machine Learning pijp lijnen zijn echter gebaseerd op dezelfde back-end die de SDK aanstuurt. Dit betekent dat u twee opties hebt voor machine learning ontwikkeling: de ontwerper met slepen en neerzetten of code-eerste Sdk's.

Zie [Wat zijn Azure machine learning pijp lijnen](../concept-ml-pipelines.md#building-pipelines-with-the-python-sdk)voor meer informatie over het bouwen van pijp lijnen met de SDK.


## <a name="prerequisites"></a>Vereisten

- Een Azure-account met een actief abonnement. [Gratis een account maken](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Een Azure Machine Learning-werkruimte. [Een Azure Machine Learning-werkruimte maken](../how-to-manage-workspace.md#create-a-workspace)
- Een studio (klassiek) experiment dat moet worden gemigreerd.
- [Upload uw gegevensset](migrate-register-dataset.md) naar Azure machine learning.

## <a name="rebuild-the-pipeline"></a>De pijp lijn opnieuw samen stellen

Nadat u [uw gegevensset naar Azure machine learning hebt gemigreerd](migrate-register-dataset.md), bent u klaar om uw experiment te maken.

In Azure Machine Learning wordt de visuele grafiek een **pijp lijn concept** genoemd. In deze sectie maakt u uw klassieke experiment opnieuw als een pijp lijn concept.

1. Ga naar Azure Machine Learning Studio ([ml.Azure.com](https://ml.azure.com))
1. Selecteer in het navigatie deel venster aan de linkerkant **ontwerp functie** > **voor vooraf gedefinieerde modules,** ![ waarin wordt getoond hoe u een nieuwe pijp lijn concept maakt.](../media/tutorial-designer-automobile-price-train-score/launch-designer.png)

1. Bouw uw experiment hand matig opnieuw op met ontwerp modules.
    
    Raadpleeg de [module toewijzings tabel](migrate-overview.md#studio-classic-and-designer-module-mapping) om de vervangings modules te vinden. Veel studio-populairste modules (klassiek) hebben dezelfde versie in de ontwerp functie.

    > [!Important]
    > Als uw experiment gebruikmaakt van de script module Execute R, moet u extra stappen uitvoeren om uw experiment te migreren. Zie [R-script modules migreren](migrate-execute-r-script.md)voor meer informatie.

1. Wijzig de para meters.
    
    Selecteer elke module en wijzig de para meters in het deel venster module-instellingen aan de rechter kant. Gebruik de para meters om de functionaliteit van uw studio-experiment (klassiek) opnieuw te maken. Zie de [module verwijzing](../algorithm-module-reference/module-reference.md)voor meer informatie over elke module.

## <a name="submit-a-run-and-check-results"></a>Een uitvoer-en controle resultaten verzenden

Nadat u uw studio-experiment (klassiek) opnieuw hebt gemaakt, is het tijd om een **pijplijn uitvoering** te verzenden.

Een pijplijn uitvoering wordt uitgevoerd op een **reken doel** dat is gekoppeld aan uw werk ruimte. U kunt een standaard Compute-doel instellen voor de volledige pijp lijn of u kunt reken doelen per module opgeven.

Wanneer u een run van een pijp lijn concept verzendt, wordt deze omgezet in een **pijplijn uitvoering**. Elke pijplijn uitvoering wordt geregistreerd en aangemeld Azure Machine Learning.

Een standaard Compute-doel voor de volledige pijp lijn instellen:
1. Selecteer het **tandwiel pictogram tandwiel** ![ pictogram in de ontwerp functie ](../media/tutorial-designer-automobile-price-train-score/gear-icon.png) naast de naam van de pijp lijn.
1. Selecteer **reken doel selecteren**.
1. Selecteer een bestaande Compute of maak een nieuwe Compute door de instructies op het scherm te volgen.

Nu het berekenings doel is ingesteld, kunt u een pijplijn uitvoering verzenden:

1. Bovenaan het canvas selecteert u **Indienen**.
1. Selecteer **nieuwe maken** om een nieuw experiment te maken.
    
    Experimenten organiseren vergelijk bare pijplijn uitvoeringen tegelijk. Als u een pijplijn meerdere keren uitvoert, kunt u hetzelfde experiment selecteren voor achtereenvolgende uitvoeringen. Dit is handig voor logboek registratie en tracering.
1. Voer een naam voor het experiment in. Selecteer vervolgens **verzenden**.

    De eerste uitvoering kan Maxi maal 20 minuten duren. Aangezien de standaard Compute-instellingen een minimale knooppunt grootte hebben van 0, moet de ontwerp functie Resources toewijzen na inactiviteit. Opeenvolgende uitvoeringen nemen minder tijd in beslag, omdat de knoop punten al zijn toegewezen. Als u de uitvoerings tijd wilt versnellen, kunt u een reken resources maken met een minimale knooppunt grootte van 1 of hoger.

Nadat de uitvoering is voltooid, kunt u de resultaten van elke module controleren:

1. Klik met de rechter muisknop op de module waarvan u de uitvoer wilt weer geven.
1. Selecteer **visualiseren**, **uitvoer weer geven** of **logboek weer geven**.

    - **Visualiseren**: Bekijk een voor beeld van de gegevensset voor resultaten.
    - **Uitvoer weer geven**: Open een koppeling naar de opslag locatie voor uitvoer. Gebruik deze om de uitvoer te verkennen of te downloaden. 
    - **Logboek weer geven**: stuur programma-en systeem logboeken weer geven. Gebruik de **70_driver_log** om informatie weer te geven die betrekking heeft op uw door de gebruiker ingediende script, zoals fouten en uitzonde ringen.

> [!IMPORTANT]
> Ontwerp modules gebruiken open source Python-pakketten, vergeleken met C#-pakketten in Studio (klassiek). Als gevolg hiervan kan de module-uitvoer enigszins verschillen tussen de Designer en Studio (klassiek). 


## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u geleerd hoe u een studio-experiment (klassiek) opnieuw bouwt in Azure Machine Learning. De volgende stap is het [opnieuw samen stellen van webservices in azure machine learning](migrate-rebuild-web-service.md).


Zie de andere artikelen in de migratie reeks Studio (klassiek):

1. [Overzicht migratie](migrate-overview.md).
1. [Gegevensset migreren](migrate-register-dataset.md).
1. **Bouw een studio-trainings pijplijn (klassiek) opnieuw** op.
1. [Een studio-webservice (klassiek) opnieuw bouwen](migrate-rebuild-web-service.md).
1. [Een Azure machine learning-webservice integreren met client-apps](migrate-rebuild-integrate-with-client-app.md).
1. [Execute R-script migreren](migrate-execute-r-script.md).
