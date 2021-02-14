---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor-configuratie met ServiceNow
description: In dit artikel wordt beschreven hoe u uw ITSM-producten of-Services verbindt met ServiceNow in Secure export in Azure Monitor.
ms.subservice: logs
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: 8ca77c1fa1232f7aed88b0d6942d8a0085caf0d5
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100380216"
---
# <a name="connect-servicenow-to-azure-monitor"></a>ServiceNow verbinden met Azure Monitor

De volgende secties bevatten informatie over hoe u verbinding kunt maken met uw ServiceNow-product en beveiligde export in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet:

* Azure AD is geregistreerd.
* U hebt de ondersteunde versie van ServiceNow Event Management-ITOM (versie New York of hoger).
* De [toepassing](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.2.0?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3Devent%2520management%2520connectors&sl=sh) is geïnstalleerd op het ServiceNow-exemplaar.

## <a name="configure-the-servicenow-connection"></a>De ServiceNow-verbinding configureren

1. Gebruik de koppeling https://(exemplaar naam). Service-now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor de URI voor de beveiligde export definitie.

2. Volg de instructies op basis van de versie:
   * [Parijs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/task/azure-events-authentication.html)
