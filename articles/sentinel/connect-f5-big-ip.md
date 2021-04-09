---
title: F5 BIG-IP-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over het verbinden van F5 BIG-IP-gegevens naar Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: d068223f-395e-46d6-bb94-7ca1afd3503c
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 17c31bbe54326962c26ab53f702cbd28d1e36c6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100092788"
---
# <a name="connect-your-f5-big-ip-appliance"></a>Verbinding maken met uw F5 BIG-IP-apparaat 

Met de F5 BIG-IP-connector kunt u eenvoudig al uw F5 BIG-IP-logboeken met Azure Sentinel aansluiten om werkmappen weer te geven, aangepaste waarschuwingen te maken en het onderzoek te verbeteren. Dit geeft u meer inzicht in het netwerk van uw organisatie en verbetert de mogelijkheden van beveiligings bewerkingen. Integratie tussen F5 BIG-IP en Azure Sentinel maakt gebruik van REST API.

> [!NOTE]
> Gegevens worden opgeslagen in de geografische locatie van de werk ruimte waarop u Azure Sentinel uitvoert.

## <a name="configure-and-connect-f5-big-ip"></a>Configureren en verbinding maken F5 BIG-IP 

F5 BIG-IP kan Logboeken rechtstreeks integreren en exporteren naar Azure Sentinel.

1. Klik in de Azure-Sentinel-Portal op **gegevens connectors** en selecteer **F5 BIG-IP** en **Open vervolgens connector-pagina**. 
1. Als u verbinding wilt maken met uw F5 BIG-IP-adres, moet u een JSON-declaratie naar het API-eind punt van het systeem posten. Zie [de F5 BIG-IP met Azure-Sentinel integreren](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel)voor instructies over hoe u dit doet.
8. Kopieer op de pagina F5 BIG-IP connector de werk ruimte-ID en de primaire sleutel en plak ze als een instructie [voor het streamen van gegevens naar Azure log Analytics](https://devcentral.f5.com/s/articles/Integrating-the-F5-BIGIP-with-Azure-Sentinel#streaming-data-to-azure-log-analytics).
1. Nadat u de F5 BIG-IP-instructies hebt voltooid, ziet u op de Azure Sentinel connector-pagina de verbonden gegevens typen.
1. Als u het relevante schema in Log Analytics voor de F5 BIG-IP-gebeurtenissen wilt gebruiken, zoekt u naar **F5Telemetry_LTM_CL**, **F5Telemetry_system_CL** en **F5Telemetry_ASM_CL**.


## <a name="validate-connectivity"></a>Connectiviteit valideren

Het kan Maxi maal 20 minuten duren voordat uw logboeken in Log Analytics worden weer gegeven. 



## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u met F5 BIG-IP verbinding maakt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


