---
title: Palo Alto Networks-gegevens verbinden met Azure Sentinel | Microsoft Docs
description: Meer informatie over hoe u de Palo Alto Networks Data Connector kunt gebruiken om uw Palo Alto Networks-Logboeken eenvoudig met Azure Sentinel te verbinden.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: a4b21d67-1a72-40ec-bfce-d79a8707b4e1
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: 245db436fc3216fe5c8c8f51c50c0ac03190f9eb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "85564567"
---
# <a name="connect-palo-alto-networks-to-azure-sentinel"></a>Palo Alto Networks verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Palo Alto Networks-apparaat verbindt met Azure Sentinel. Met de Palo Alto Networks Data Connector kunt u eenvoudig uw Palo Alto Networks-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van het onderzoek. Door gebruik te maken van Palo Alto-netwerken op Azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 


## <a name="forward-palo-alto-networks-logs-to-the-syslog-agent"></a>Palo Alto Networks-logboeken door sturen naar de syslog-agent

Configureer Palo Alto-netwerken om syslog-berichten in de CEF-indeling door te sturen naar uw Azure-werk ruimte via de syslog-agent:
1.  Ga naar [CEF-configuratie handleidingen (common Event Format)](https://docs.paloaltonetworks.com/resources/cef) en down load de PDF voor uw type apparaat. Volg alle instructies in de hand leiding om uw Palo Alto Networks-apparaat in te stellen voor het verzamelen van CEF-gebeurtenissen. 

1.  Ga naar [syslog-bewaking configureren](https://docs.paloaltonetworks.com/pan-os/8-1/pan-os-admin/monitoring/use-syslog-for-monitoring/configure-syslog-monitoring) en volg stap 2 en 3 om CEF Event forwarding te configureren van uw Palo Alto Networks-apparaat naar Azure Sentinel.

    1. Zorg ervoor dat u de **syslog-server indeling** instelt op **BSD**.

       > [!NOTE]
       > De kopieer-en plak bewerkingen van de PDF kunnen de tekst wijzigen en wille keurige tekens invoegen. Om dit te voor komen, kopieert u de tekst naar een editor en verwijdert u alle tekens die de logboek indeling kunnen verstoren voordat u deze plakt, zoals u kunt zien in dit voor beeld.
 
        ![CEF-probleem met tekst kopiëren](./media/connect-cef/paloalto-text-prob1.png)

1. Als u het relevante schema in Log Analytics voor de gebeurtenissen van het Palo Alto-netwerken wilt gebruiken, zoekt u naar **CommonSecurityLog**.

1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Palo Alto Networks-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


