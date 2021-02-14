---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor-configuratie met ServiceNow
description: In dit artikel wordt beschreven hoe u uw ITSM-producten of-Services verbindt met ServiceNow in Secure export in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 7801145ba1bcaa2ffd543becfe190f05d232e8c8
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100104957"
---
# <a name="connect-servicenow-to-azure-monitor"></a>ServiceNow verbinden met Azure Monitor

De volgende secties bevatten informatie over hoe u verbinding kunt maken met uw ServiceNow-product en beveiligde export in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet:

* Azure AD is geregistreerd.
* U hebt de ondersteunde versie van ServiceNow Event Management-ITOM (versie New York of hoger).

## <a name="configure-the-servicenow-connection"></a>De ServiceNow-verbinding configureren

1. Gebruik de koppeling https://(exemplaar naam). Service-now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor de URI voor de beveiligde export definitie.

2. Volg de instructies op basis van de versie:
   * [Parijs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
