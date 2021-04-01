---
title: Force Point-producten verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van Force Point-producten met Azure Sentinel.
services: sentinel
author: yelevin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/20/2020
ms.author: yelevin
ms.openlocfilehash: eb099a786a84f9b7d0a6f0dc6e6df9c3459af295
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "77588226"
---
# <a name="connect-your-forcepoint-products-to-azure-sentinel"></a>Uw Force Point-producten verbinden met Azure Sentinel

> [!IMPORTANT]
> De Force Point Products Data Connector in azure Sentinel is momenteel beschikbaar als open bare preview. Deze functie wordt zonder service level agreement gegeven en wordt niet aanbevolen voor productie werkbelastingen. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.


In dit artikel wordt uitgelegd hoe u uw Force Point-producten verbindt met Azure Sentinel. 

Met de Force Point-gegevens connectors kunt u verbinding maken met Force Point Cloud Access Security Broker en Force Point Next Generation firewall-logboeken met Azure Sentinel. Op deze manier kunt u automatisch door de gebruiker gedefinieerde Logboeken in realtime naar Azure-Sentinel exporteren. Met de connector beschikt u over verrijkte zicht baarheid van gebruikers activiteiten die door Force Point-producten worden vastgelegd. Daarnaast is er meer correlatie met gegevens van Azure-werk belastingen en andere feeds mogelijk en wordt de bewakings mogelijkheid verbeterd met werkmappen binnen Azure Sentinel.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.



## <a name="forward-forcepoint-product-logs-to-the-syslog-agent"></a>Force Point-product logboeken door sturen naar de syslog-agent 

Configureer het Force Point-product om syslog-berichten in de CEF-indeling door te sturen naar uw Azure-werk ruimte via de syslog-agent.

1. Stel het Force Point-product in op Azure Sentinel Integration, zoals wordt beschreven in de volgende installatie handleidingen:
 - [Force Point CASB-integratie handleiding](https://frcpnt.com/casb-sentinel)
 - [Force Point NGFW-integratie handleiding](https://frcpnt.com/ngfw-sentinel)

2. Zoek naar CommonSecurityLog om het relevante schema te gebruiken in Log Analytics met DeviceVendor-naam bevat ' Force Point '. 

3. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).



## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u Force Point-producten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:

- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).

- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).

- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.