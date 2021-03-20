---
title: Problemen met ServiceNow-synchronisatie hand matig oplossen
description: Stel de verbinding opnieuw in op ServiceNow zodat waarschuwingen in Microsoft Azure opnieuw kunnen worden aangeroepen ServiceNow
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/17/2021
ms.openlocfilehash: 664f1522775d96b813b7cd99bdffdb26630497f0
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102037435"
---
# <a name="how-to-manually-fix-sync-problems"></a>Synchronisatie problemen hand matig oplossen

Azure Monitor kan verbinding maken met ITSM-providers (IT Service Management) van derden. ServiceNow is een van deze providers.

Uit veiligheids overwegingen moet u mogelijk het verificatie token vernieuwen dat voor uw verbinding wordt gebruikt met ServiceNow.
Gebruik het volgende synchronisatie proces om de verbinding opnieuw te activeren en het token te vernieuwen:

1. Zoek naar de oplossing in de bovenste Zoek banner en selecteer vervolgens de relevante oplossingen

    ![Scherm afbeelding met de bovenste Zoek banner en waar u de relevante oplossingen kunt selecteren.](media/itsmc-resync-servicenow/solution-search-8-bit.png)

1. Kies in het scherm oplossing de optie Alles selecteren in het abonnements filter en filter vervolgens op ' Service Desk '

    ![Scherm afbeelding die laat zien waar u selecteert selecteren en waar u wilt filteren op Service Desk.](media/itsmc-resync-servicenow/solutions-list-8-bit.png)

1. Selecteer de oplossing van uw ITSM-verbinding.
1. Selecteer ITSM-verbinding in het linkerdeel blad.

    ![Scherm afbeelding die laat zien waar u ITSM-verbindingen selecteert.](media/itsmc-resync-servicenow/itsm-connector-8-bit.png)

1. Selecteer elke connector in de lijst. 
    1. Klik op de naam van de connector om deze te configureren
    1. Alle connectors verwijderen die niet meer worden gebruikt

    1. De velden bijwerken volgens [deze definities](./itsmc-connections.md) op basis van uw partner type

    1. Klik op synchroniseren

       ![Scherm opname van de knop synchroniseren.](media/itsmc-resync-servicenow/resync-8-bit-2.png)

    1. Klik op opslaan

        ![Nieuwe verbinding](media/itsmc-resync-servicenow/save-8-bit.png)

f.    Bekijk de meldingen om te zien of het proces is gestart.
