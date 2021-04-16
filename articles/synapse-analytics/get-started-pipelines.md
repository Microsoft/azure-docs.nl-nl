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
ms.openlocfilehash: 19bff62883341947eb5290118494b8244c5476ac
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518249"
---
# <a name="integrate-with-pipelines"></a>Integreren met pijplijnen

In deze zelfstudie leert u hoe u pijplijnen en activiteiten kunt integreren met behulp van Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Een pijplijn maken en een notebookactiviteit toevoegen

1. Ga in Synapse Studio naar de hub **Integreren**.
1. Selecteer **+**  > **Pijplijn** om een nieuwe pijplijn te maken. Klik op het nieuwe pijplijnobject om de pijplijnontwerper te openen.
1. Vouw **onder Activiteiten** de map **Synapse** uit en sleep een **Notebook-object** naar de ontwerpfunctie.
1. Selecteer het **tabblad Instellingen** van de eigenschappen van notebookactiviteit. Gebruik de vervolgkeuzelijst om een notebook te selecteren in uw huidige Synapse-werkruimte.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>De pijplijn plannen om elk uur te worden uitgevoerd

1. Selecteer in de pijplijn **Trigger toevoegen** > **Nieuw/bewerken**.
1. In **Trigger kiezen** selecteert u **Nieuw** en stelt u **Terugkeerpatroon** in op Elk uur.
1. Selecteer **OK**. 
1. Selecteer **Alles publiceren**. 

## <a name="forcing-a-pipeline-to-run-immediately"></a>Afdwingen dat een pijplijn onmiddellijk wordt uitgevoerd

Zodra de pijplijn is gepubliceerd, kunt u deze onmiddellijk uitvoeren zonder dat u een uur moet wachten.

1. Open de pijplijn.
1. Klik **op Trigger toevoegen** Nu  >  **activeren.**

## <a name="monitor-pipeline-execution"></a>Pijplijnuitvoering bewaken

1. Ga naar de **monitorhub.**
1. Selecteer **Pijplijnuitvoeringen om** de voortgang van de pijplijnuitvoering te controleren.
1. In deze weergave kunt u schakelen tussen tabellaire **lijst weergeven** van een grafische **Gantt-grafiek.** 
1. Klik op de naam van een pijplijn om de status van activiteiten in die pijplijn te bekijken.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Gegevens visualiseren met Power BI](get-started-visualize-power-bi.md)
