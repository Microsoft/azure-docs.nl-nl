---
title: Fout onderzoek met dash board
description: Dit document bevat informatie over fout onderzoek met behulp van het dash board
ms.subservice: alerts
ms.topic: conceptual
author: nolavime
ms.author: nolavime
ms.date: 01/15/2021
ms.openlocfilehash: 9291689b362b5cbe651a72220196dd30b40745cf
ms.sourcegitcommit: fc23b4c625f0b26d14a5a6433e8b7b6fb42d868b
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/17/2021
ms.locfileid: "98540549"
---
# <a name="error-investigation-using-the-dashboard"></a>Fout onderzoek met behulp van het dash board

Deze pagina bevat informatie over het dash board van de ITSM-connector. Met dit dash board kunt u de status van uw ITSM-connector onderzoeken.

## <a name="how-to-view-the-dashboard"></a>Het dash board weer geven

Volg de volgende stappen om de fouten in het dash board weer te geven:

1. Zoek in **alle resources** naar **Service Desk (*de naam van uw werk ruimte*)**:

   ![Scherm opname van recente resources in het Azure Portal.](media/itsmc-definition/create-new-connection-from-resource.png)

2. Selecteer onder **gegevens bronnen voor werk ruimte** in het linkerdeel venster **ITSM-verbindingen**:

   ![Scherm opname van het menu-item ITSM-verbindingen.](media/itsmc-overview/add-new-itsm-connection.png)

3. Onder **samen vatting** in het linkervak **IT Service Management-connector** selecteert u **samen vatting weer geven**:

    ![Scherm opname van de weer gave-samen vatting.](media/itsmc-resync-servicenow/dashboard-view-summary.png)

4. Klik onder **samen vatting** in het linkervak **IT Service Management-connector** op de grafiek:

    ![Scherm opname van de weer gave van de grafiek.](media/itsmc-resync-servicenow/dashboard-graph-click.png)

5. Met dit dash board kunt u de status en de fouten in uw connector controleren.
    ![Scherm opname van de status van de connector.](media/itsmc-resync-servicenow/connector-dashboard.png)

## <a name="dashboard-elements"></a>Dashboard items

Het dash board bevat informatie over de waarschuwingen die zijn verzonden naar het ITSM-hulp programma met behulp van deze connector.
Het dash board is onderverdeeld in vier delen:

1. Werk item gemaakt: in de grafiek en de onderstaande tabel wordt het aantal werk items per type opgenomen. Als u op de grafiek of op de tabel klikt, kunt u meer informatie over de werk items bekijken.
    ![Scherm opname van het werk item dat wordt gemaakt.](media/itsmc-resync-servicenow/itsm-dashboard-workitems.png)
2. Betrokken computers: de tabellen bevatten details over de configuratie-items die configuratie-items hebben gemaakt.
    Als u op rijen in de tabellen klikt, kunt u meer informatie krijgen over de configuratie-items.
    De tabel bevat een beperkt aantal rijen als u wilt dat alle lijsten worden weer geven, klikt u op alles weer geven.
    ![Scherm opname van de betrokken computers.](media/itsmc-resync-servicenow/itsm-dashboard-impacted-comp.png)
3. Connector status: in de grafiek en de onderstaande tabel staan berichten over de status van de connector. Door te klikken op de grafiek op rijen in de tabel, kunt u meer informatie krijgen over de berichten van de connector status.
    De tabel bevat een beperkt aantal rijen als u wilt dat alle lijsten worden weer geven, klikt u op alles weer geven.
    ![Scherm opname van de status van de connector.](media/itsmc-resync-servicenow/itsm-dashboard-connector-status.png)
4. Waarschuwings regels: de tabellen bevatten de informatie over het aantal waarschuwings regels dat is gedetecteerd.
    Als u op rijen in de tabellen klikt, kunt u meer informatie krijgen over de regels die zijn gedetecteerd.
    De tabel bevat een beperkt aantal rijen als u wilt dat alle lijsten worden weer geven, klikt u op alles weer geven.
    ![Scherm opname van waarschuwings regels.](media/itsmc-resync-servicenow/itsm-dashboard-alert-rules.png)