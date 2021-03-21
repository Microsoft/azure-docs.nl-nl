---
title: 'Zelfstudie: aan de slag met het integreren met pijplijnen'
description: In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 2ea7c3c440fcf95e4512464333efe8461788bceb
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98219399"
---
# <a name="integrate-with-pipelines"></a>Integreren met pijplijnen

In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio. 

## <a name="overview"></a>Overzicht

U kunt een groot aantal verschillende taken in Azure Synapse integreren.

1. Ga in Synapse Studio naar de hub **Integreren**.
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken. Klik op het nieuwe pijplijn object om de ontwerp functie voor pijp lijnen te openen.
1. Vouw onder **activiteiten** de map **Synapse** uit en sleep een **notebook** -object naar de ontwerp functie.
1. Selecteer het tabblad **instellingen** van de eigenschappen van de notitieblok activiteit. Gebruik de vervolg keuzelijst om een notitie blok in uw huidige Synapse-werk ruimte te selecteren. 
1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken**.
1. In **Trigger kiezen** selecteert u **Nieuw** en stelt u **Terugkeerpatroon** in op Elk uur.
1. Selecteer **OK**. 
1. Selecteer **Alles publiceren**. 


## <a name="monitor-pipeline"></a>De pijplijn bewaken

1. Zodra de pijp lijn is gepubliceerd, kunt u de pijp lijn onmiddellijk laten uitvoeren, zonder te wachten op het volgende uur, selecteert u nu **trigger trigger toevoegen**  >  .
1. Ga in Synapse Studio naar de **monitor** hub en selecteer **pijplijn** uitvoeringen om de voortgang van de uitvoering van de pijp lijn te bewaken.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens visualiseren met Power BI](get-started-visualize-power-bi.md)
