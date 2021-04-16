---
title: Ongebruikte ITSM-connectors verwijderen
description: In dit artikel wordt beschreven hoe u ITSM-connectors en de bijbehorende actiegroepen verwijdert.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: 3dc84ea6def9b762527226dbeb3e2eaab78ec200
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107387935"
---
# <a name="delete-unused-itsm-connectors"></a>Ongebruikte ITSM-connectors verwijderen

Het proces van het verwijderen van ongebruikte IT service management (ITSM)-connectors bestaat uit twee fasen. U verwijdert alle acties die zijn gekoppeld aan een ITSM-connector en vervolgens verwijdert u de connector zelf. U verwijdert eerst de acties omdat acties zonder connector fouten in uw abonnement kunnen veroorzaken.

## <a name="delete-associated-actions"></a>Gekoppelde acties verwijderen

1. Selecteer in Azure Portal de optie **Controleren.**
  
    ![Schermopname van de selectie Bewaken.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selecteer **Waarschuwingen.**
   
    ![Schermopname van de selectie Waarschuwingen.](media/itsmc-connector-deletion/itsmc-alert-selection.png)

3. Selecteer **Acties beheren.**
   
    ![Schermopname van de selectie Acties beheren.](media/itsmc-connector-deletion/itsmc-actions-selection.png)

4. Selecteer een actiegroep die is gekoppeld aan de ITSM-connector die u wilt verwijderen. In dit artikel wordt het voorbeeld van een Cherwell-connector gebruikt.
   
    ![Schermopname van acties die zijn gekoppeld aan de Cherwell-connector.](media/itsmc-connector-deletion/itsmc-actions-screen.png)

5. Controleer de informatie en selecteer **actiegroep verwijderen.**

    ![Schermopname van informatie over de actiegroep en de knop voor het verwijderen van de groep.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="delete-the-connector"></a>De connector verwijderen

1. Zoek in de zoekbalk naar **servicedesk**. Selecteer vervolgens **ServiceDesk** in de lijst met resources.

    ![Schermopname van het zoeken naar en selecteren van ServiceDesk.](media/itsmc-connector-deletion/itsmc-connector-selection.png)

2. Selecteer **ITSM-verbindingen** en selecteer vervolgens de Connector Cherwell.

    ![Schermopname van de Cherwell I T S M-connector.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)

3. Selecteer **Verwijderen**.

    ![Schermopname van de knop Verwijderen voor de I T S M-connector.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Volgende stappen

* [Problemen in een ITSM-connector oplossen](./itsmc-resync-servicenow.md)
