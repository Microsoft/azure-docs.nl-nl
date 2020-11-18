---
title: Uw beveiligings oplossing configureren om CEF-gegevens te verbinden met Azure Sentinel preview | Microsoft Docs
description: Meer informatie over het configureren van uw beveiligings oplossing voor het verbinden van CEF-gegevens met Azure Sentinel.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/30/2019
ms.author: yelevin
ms.openlocfilehash: e2ed3680a0867ab8f7e2ad41603883f07a4be427
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94655745"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STAP 2: uw beveiligings oplossing configureren voor het verzenden van CEF-berichten

In deze stap voert u de nood zakelijke configuratie wijzigingen in uw beveiligings oplossing zelf uit om logboeken te verzenden naar de CEF-agent.

## <a name="configure-a-solution-with-a-connector"></a>Een oplossing met een connector configureren

Als uw beveiligings oplossing al een bestaande connector heeft, gebruikt u de volgende instructies voor de connector:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco](connect-cisco.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)  
- [Fortinet](connect-fortinet.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Deep Security van Trend Micro](connect-trend-micro.md)
- [Zscaler](connect-zscaler.md)   

## <a name="configure-any-other-solution"></a>Andere oplossingen configureren

Als er geen connector bestaat voor uw specifieke beveiligings oplossing, gebruikt u de volgende algemene instructies voor het door sturen van logboeken naar de CEF-agent.

1. Ga naar het artikel specifieke configuratie voor de stappen voor het configureren van uw oplossing voor het verzenden van CEF-berichten. Als uw oplossing niet wordt weer gegeven, moet u op het apparaat deze waarden instellen, zodat het apparaat de benodigde logboeken naar de Azure Sentinel syslog-agent verzendt, op basis van de Log Analytics agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = TCP
    - Poort = 514
    - Indeling = CEF
    - IP-adres: Zorg ervoor dat u de CEF-berichten verzendt naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen.

   > [!NOTE]
   > Deze oplossing ondersteunt syslog RFC 3164 of RFC 5424.

1. Zoek naar om het relevante schema in Log Analytics te gebruiken voor de CEF-gebeurtenissen `CommonSecurityLog` .

1. Ga door naar stap 3: de [verbinding valideren](connect-cef-verify.md).

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).