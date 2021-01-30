---
title: Fouten onderzoeken met behulp van het dash board
description: Dit document bevat informatie over fouten op het ITSMC-dash board.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: ebd59e637e498b8088fe9a302b1bb12efdf2c173
ms.sourcegitcommit: b4e6b2627842a1183fce78bce6c6c7e088d6157b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/30/2021
ms.locfileid: "99089272"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Fouten onderzoeken met behulp van het ITSMC-dash board

Dit artikel bevat informatie over het IT Service Management-connector-dash board (ITSMC). Met het dash board kunt u de status van ITSMC onderzoeken.

## <a name="view-the-dashboard"></a>Het dashboard weergeven

Volg deze stappen om het dash board te openen.

1. Selecteer **alle resources** en zoek vervolgens **Service Desk (*uw werkruimte naam*)**.

   ![Scherm opname waarin de resources in Azure-Services worden weer gegeven.](media/itsmc-definition/create-new-connection-from-resource.png)

1. Selecteer in het linkerdeel venster **werkruimte gegevens bronnen** en selecteer vervolgens ITSM- **verbindingen**.

   ![Scherm opname van het selecteren van ITSM-verbindingen onder gegevens bronnen op werk plek.](media/itsmc-overview/add-new-itsm-connection.png)

1. Selecteer in de sectie **samen vatting** **samen vatting weer** geven om een samenvattings grafiek weer te geven.

    ![Scherm afbeelding met de optie samen vatting weer geven in de sectie samen vatting.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

1. Selecteer in de sectie **samen vatting** de grafiek om het dash board te openen.

    ![Scherm opname van de weer gave van de samenvattings grafiek.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

1. Controleer het dash board op status en eventuele fouten in de connector.
    ![Scherm opname van het dash board.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Dashboard elementen begrijpen

Het dash board bevat informatie over de waarschuwingen die zijn verzonden naar het ITSM-hulp programma met behulp van deze connector.

Het dash board is onderverdeeld in vier secties:

- **Werk items gemaakt**: in de grafiek en tabel wordt het aantal werk items op type weer gegeven. Selecteer de grafiek of de tabel om meer te weten te komen over uw werk items.
      ![Scherm opname van de sectie werk items die is gemaakt.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
- Betrokken **computers**: de tabel bevat details over de configuratie-items waarmee werk items zijn gemaakt.
      Selecteer rijen in de tabellen voor meer informatie over de configuratie-items.
      De tabel bevat een beperkt aantal rijen. Als u de hele lijst wilt weer geven, selecteert u **alles weer geven**.
      ![Scherm afbeelding met de sectie betrokken computers.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
- **Connector status**: de grafiek en de tabel bevatten informatie over de status van de connector. Selecteer de grafiek of de berichten in de tabel voor meer informatie. De tabel bevat een beperkt aantal rijen. Als u de hele lijst wilt weer geven, selecteert u **alles weer geven**.
      ![Scherm afbeelding met de sectie connector status.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
- **WAARSCHUWINGS regels**: deze sectie bevat informatie over het aantal gedetecteerde waarschuwings regels. Selecteer rijen in de tabellen voor meer informatie over de gedetecteerde regels. De tabel heeft een beperkt aantal rijen. Als u de hele lijst wilt weer geven, selecteert u **alles weer geven**.
      ![Scherm opname van de sectie waarschuwings regels.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)

## <a name="next-steps"></a>Volgende stappen

Bekijk [status fouten van common connectors](itsmc-dashboard-errors.md).
