---
title: De ITSM-connector en de actie die eraan zijn gekoppeld, verwijderen
description: Dit artikel bevat een uitleg over het verwijderen van de ITSM-connector en de actie groepen die eraan zijn gekoppeld.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/29/2020
ms.custom: references_regions
ms.openlocfilehash: e73510b6c61c58f6f0b2b8067a240214ee35a46c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102036483"
---
# <a name="deletion-of-unused-itsm-connectors"></a>Verwijdering van niet-gebruikte ITSM-connectors

Het proces voor het verwijderen van een ongebruikte connector bevat twee fasen:

1. Het verwijderen van de gekoppelde acties: alle acties die zijn gekoppeld aan de ITSM-connector, moeten worden verwijderd. Dit moet worden gedaan om niet te beschikken over acties zonder connector die fouten in uw abonnement kunnen veroorzaken.

2. De niet-gebruikte ITSM-connector wordt verwijderd.

## <a name="deletion-of-the-associated-actions"></a>De gekoppelde acties worden verwijderd

1. Als u de actie groep wilt vinden, gaat u naar scherm  ![ afbeelding van monitor selecteren.](media/itsmc-connector-deletion/itsmc-monitor-selection.png)

2. Selecteer  ![ scherm afbeelding van waarschuwingen selecteren.](media/itsmc-connector-deletion/itsmc-alert-selection.png)
3. Selecteer de  ![ scherm afbeelding acties beheren van selectie acties beheren.](media/itsmc-connector-deletion/itsmc-actions-selection.png)
4. Selecteer alle ITSM-connectors die zijn verbonden met Cher well  ![ scherm opname van ITSM-connectors die zijn verbonden met Cher well.](media/itsmc-connector-deletion/itsmc-actions-screen.png)
5. Verwijder de  ![ scherm opname van de actie groep van de actie groep verwijderen.](media/itsmc-connector-deletion/itsmc-action-deletion.png)

## <a name="deletion-of-the-unused-itsm-connector"></a>Ongebruikte ITSM-connector verwijderen

1. Zoek en selecteer ' Service Desk ' in de scherm afbeelding van de bovenste zoek balk  ![ van zoeken en selecteer ' Service Desk '.](media/itsmc-connector-deletion/itsmc-connector-selection.png)
2. Selecteer de ITSM-verbindingen en selecteer de scherm opname van de Cher well-connector  ![ van Cher well ITSM-connectors.](media/itsmc-connector-deletion/itsmc-cherwell-connector.png)
3. Selecteer verwijderen  ![ scherm opname van ITSM-connector verwijderen.](media/itsmc-connector-deletion/itsmc-connector-deletion.png)

## <a name="next-steps"></a>Volgende stappen

* [Problemen oplossen in ITSM-connector](./itsmc-resync-servicenow.md)
