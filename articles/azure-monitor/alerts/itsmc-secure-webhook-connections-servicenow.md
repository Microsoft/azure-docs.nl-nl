---
title: IT Service Management-connector-beveiligd exporteren in Azure Monitor-configuratie met ServiceNow
description: In dit artikel wordt beschreven hoe u uw ITSM-producten of-Services verbindt met ServiceNow in Secure export in Azure Monitor.
ms.topic: conceptual
author: nolavime
ms.author: v-jysur
ms.date: 12/31/2020
ms.openlocfilehash: cf19911bd8126bfb73f848c086aa817a7d7adb00
ms.sourcegitcommit: 18a91f7fe1432ee09efafd5bd29a181e038cee05
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103563688"
---
# <a name="connect-servicenow-to-azure-monitor"></a>ServiceNow verbinden met Azure Monitor

De volgende secties bevatten informatie over hoe u verbinding kunt maken met uw ServiceNow-product en beveiligde export in Azure.

## <a name="prerequisites"></a>Vereisten

Zorg ervoor dat u aan de volgende vereisten voldoet:

* Azure AD is geregistreerd.
* U hebt de ondersteunde versie van ServiceNow Event Management-ITOM (versie New York of hoger).
* De [toepassing](https://store.servicenow.com/sn_appstore_store.do#!/store/application/ac4c9c57dbb1d090561b186c1396191a/1.3.1?referer=%2Fstore%2Fsearch%3Flistingtype%3Dallintegrations%25253Bancillary_app%25253Bcertified_apps%25253Bcontent%25253Bindustry_solution%25253Boem%25253Butility%26q%3DEvent%2520Management%2520Connectors&sl=sh) is geïnstalleerd op het ServiceNow-exemplaar.

## <a name="configure-the-servicenow-connection"></a>De ServiceNow-verbinding configureren

1. Gebruik de koppeling https://(exemplaar naam). Service-now. com/API/sn_em_connector/em/inbound_event? source = azuremonitor de URI voor de beveiligde export definitie.

2. Volg de instructies op basis van de versie:
   * [Parijs](https://docs.servicenow.com/bundle/paris-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [Orlando](https://docs.servicenow.com/bundle/orlando-it-operations-management/page/product/event-management/concept/azure-integration.html)
   * [New York](https://docs.servicenow.com/bundle/newyork-it-operations-management/page/product/event-management/concept/azure-integration.html)
