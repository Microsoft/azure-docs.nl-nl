---
title: Fouten onderzoeken met behulp van het ITSMC-dash board
description: Meer informatie over het gebruik van het dash board van IT Service Management-connector om fouten te onderzoeken.
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 5cbd501b9ccb408ee23cb5c8ed9cde2689ef79fb
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100611013"
---
# <a name="investigate-errors-by-using-the-itsmc-dashboard"></a>Fouten onderzoeken met behulp van het ITSMC-dash board

Dit artikel bevat informatie over het IT Service Management-connector-dash board (ITSMC). Met het dash board kunt u de status van de connector onderzoeken.

## <a name="view-errors"></a>Fouten weer geven

Fouten in het dash board weer geven:

1. Selecteer **alle resources** en zoek vervolgens **Service Desk (*uw werkruimte naam*)**.

   ![Scherm opname waarin de resources in Azure-Services worden weer gegeven.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het selecteren van ITSM-verbindingen onder gegevens bronnen op werk plek.](media/itsmc-overview/add-new-itsm-connection.png)

3. Onder **samen vatting**, in het gedeelte **IT Service Management-connector** , selecteert u **samen vatting weer geven**:

   ![Scherm afbeelding met de knop samen vatting weer geven.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Wanneer een grafiek wordt weer gegeven in het gebied **IT Service Management-connector** , selecteert u deze:

   ![Scherm afbeelding waarin de selectie van een grafiek wordt weer gegeven.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Het dash board wordt weer gegeven. Gebruik dit om de status en de fouten in uw connector te controleren.
   
   ![Scherm opname van de status van de connector in het dash board.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="understand-dashboard-elements"></a>Dashboard elementen begrijpen

Het dash board bevat informatie over de waarschuwingen die via deze connector naar het ITSM-hulp programma zijn verzonden. Het dash board is onderverdeeld in vier delen.

### <a name="created-work-items"></a>Gemaakte werk items 

In het gebied **werk items gemaakt** bevat de grafiek en de tabel daaronder het aantal werk items per type. Als u de grafiek of de tabel selecteert, kunt u meer informatie over de werk items bekijken.

![Scherm opname van een gemaakt werk item.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)

### <a name="affected-computers"></a>Betrokken computers 

In het gebied betrokken **computers** worden de computers en de bijbehorende werk items in de tabel weer gegeven. Als u rijen in de tabellen selecteert, kunt u meer informatie over de computers krijgen.

De tabel bevat een beperkt aantal rijen. Als u alle rijen wilt weer geven, selecteert u **alles weer geven**.

![Scherm opname die de betrokken computers weergeeft.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)

### <a name="connector-status"></a>Connectorstatus 

In het gebied STATUS van de **connector** bevatten de grafiek en de tabel daaronder berichten over de status van de connector. Door de grafiek of rijen in de tabel te selecteren, kunt u meer informatie krijgen over de berichten.

De tabel bevat een beperkt aantal rijen. Als u alle rijen wilt weer geven, selecteert u **alles weer geven**.

Zie [dit artikel](itsmc-dashboard-errors.md)voor meer informatie over de berichten in de tabel.

![Scherm opname van de status van de connector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)

### <a name="alert-rules"></a>Waarschuwingsregels 

In het gebied **WAARSCHUWINGS regels** bevat de tabel informatie over het aantal gedetecteerde waarschuwings regels. Als u rijen in de tabel selecteert, kunt u meer informatie krijgen over de gedetecteerde regels.
    
De tabel bevat een beperkt aantal rijen. Als u alle rijen wilt weer geven, selecteert u **alles weer geven**.

![Scherm opname van waarschuwings regels.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)
