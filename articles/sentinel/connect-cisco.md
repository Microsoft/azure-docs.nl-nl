---
title: Cisco-gegevens verbinden met Azure-Sentinel | Microsoft Docs
description: Leer hoe u uw Cisco ASA-apparaat verbindt met Azure Sentinel om Dash boards te bekijken, aangepaste waarschuwingen te maken en het onderzoek te verbeteren.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.assetid: 62029b5c-29d3-4336-8a22-a9db8214eb7e
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e8a64dd3e47384ba2bf7579f8052177252634622
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "85566038"
---
# <a name="connect-cisco-asa-to-azure-sentinel"></a>Cisco ASA verbinden met Azure Sentinel



In dit artikel wordt uitgelegd hoe u uw Cisco ASA-apparaat verbindt met Azure Sentinel. Met de Cisco ASA Data Connector kunt u eenvoudig uw Cisco ASA-logboeken verbinden met Azure Sentinel, voor het weer geven van Dash boards, het maken van aangepaste waarschuwingen en het verbeteren van onderzoek. Met Cisco ASA op Azure Sentinel krijgt u meer inzicht in het Internet gebruik van uw organisatie en worden de mogelijkheden voor beveiligings bewerkingen verbeterd. 



## <a name="forward-cisco-asa-logs-to-the-syslog-agent"></a>Cisco ASA-logboeken door sturen naar de syslog-agent

Cisco ASA biedt geen ondersteuning voor CEF, dus de logboeken worden verzonden als syslog en de Azure Sentinel agent weet hoe ze kunnen worden geparseerd alsof ze CEF-logboeken zijn. Cisco ASA configureren voor het door sturen van syslog-berichten naar uw Azure-werk ruimte via de syslog-agent:

1. Ga naar [syslog-berichten verzenden naar een externe syslog-server](https://aka.ms/asi-syslog-cisco-forwarding)en volg de instructies voor het instellen van de verbinding. Gebruik deze para meters wanneer u hierom wordt gevraagd:
    - Stel **poort** in op 514 of de poort die u in de agent hebt ingesteld.
    - Stel **syslog_ip** in op het IP-adres van de agent.

1. Als u het relevante schema in Log Analytics voor de Cisco-gebeurtenissen wilt gebruiken, zoekt u naar `CommonSecurityLog` .

1. Ga door naar [stap 3: de verbinding valideren](connect-cef-verify.md).




## <a name="next-steps"></a>Volgende stappen
In dit document hebt u geleerd hoe u Cisco ASA-toestellen verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](tutorial-detect-threats-built-in.md).
- [Gebruik werkmappen](tutorial-monitor-your-data.md) om uw gegevens te bewaken.


