---
title: 'Zelfstudie: Aan de slag met integreren met pijplijnen'
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
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365464"
---
# <a name="integrate-with-pipelines"></a>Integreren met pijplijnen

In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Een pijplijn maken en een notebookactiviteit toevoegen



1. Ga in Synapse Studio naar de hub **Integreren**.
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken. Klik op het nieuwe pijplijnobject om de pijplijnontwerper te openen.
1. Vouw **onder Activiteiten** de map **Synapse** uit en sleep een **Notebook-object** naar de ontwerpfunctie.
1. Selecteer het **tabblad Instellingen** van de eigenschappen van de Notebook-activiteit. Gebruik de vervolgkeuzelijst om een notebook uit uw huidige Synapse-werkruimte te selecteren.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Plannen dat de pijplijn elk uur wordt uitgevoerd

1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken**.
1. In **Trigger kiezen** selecteert u **Nieuw** en stelt u **Terugkeerpatroon** in op Elk uur.
1. Selecteer **OK**. 
1. Selecteer **Alles publiceren**. 


## <a name="monitor-pipeline-execution"></a>Pijplijnuitvoering bewaken

1. Zodra de pijplijn is gepubliceerd, selecteert u Trigger nu toevoegen om de pijplijn onmiddellijk uit te voeren zonder te wachten op het volgende  >  **uur.**
1. Ga Synapse Studio naar de **monitorhub.**
1. Selecteer **Pijplijnuitvoeringen om** de voortgang van de pijplijnuitvoering te controleren.



## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens visualiseren met Power BI](get-started-visualize-power-bi.md)
