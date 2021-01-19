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
ms.openlocfilehash: 80e5cc18888b0e014fc1f617ca27dee05146472e
ms.sourcegitcommit: ca215fa220b924f19f56513fc810c8c728dff420
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/19/2021
ms.locfileid: "98567672"
---
# <a name="step-2-configure-your-security-solution-to-send-cef-messages"></a>STAP 2: uw beveiligings oplossing configureren voor het verzenden van CEF-berichten

In deze stap voert u de nood zakelijke configuratie wijzigingen in uw beveiligings oplossing zelf uit om logboeken te verzenden naar de CEF-agent.

## <a name="configure-a-solution-with-a-connector"></a>Een oplossing met een connector configureren

Als uw beveiligings oplossing al een bestaande connector heeft, gebruikt u de volgende instructies voor de connector:

- [AI Vectra Detect](connect-ai-vectra-detect.md)
- [Check Point](connect-checkpoint.md)
- [Cisco ASA](connect-cisco.md)
- [Citrix WAF](connect-citrix-waf.md)
- [CyberArk Enterprise Password Vault](connect-cyberark.md)
- [ExtraHop Reveal(x)](connect-extrahop.md)
- [F5 ASM](connect-f5.md)
- [Force Point-producten](connect-forcepoint-casb-ngfw.md)
- [Fortinet](connect-fortinet.md)
- [Illusive Networks AMS](connect-illusive-attack-management-system.md)
- [One Identity Safeguard](connect-one-identity.md)
- [Palo Alto Networks](connect-paloalto.md)
- [Thycotic-geheim server](connect-thycotic-secret-server.md)
- [Deep Security van Trend Micro](connect-trend-micro.md)
- [Trend Micro TippingPoint](connect-trend-micro-tippingpoint.md)
- [Wirex Network forensische-platform](connect-wirex-systems.md)
- [Zscaler](connect-zscaler.md)
## <a name="configure-any-other-solution"></a>Andere oplossingen configureren

Als er geen connector bestaat voor uw specifieke beveiligings oplossing, gebruikt u de volgende algemene instructies voor het door sturen van logboeken naar de CEF-agent.

1. Ga naar het artikel specifieke configuratie voor de stappen voor het configureren van uw oplossing voor het verzenden van CEF-berichten. Als uw oplossing niet wordt weer gegeven, moet u op het apparaat deze waarden instellen, zodat het apparaat de benodigde logboeken naar de Azure Sentinel syslog-agent verzendt, op basis van de Log Analytics agent. U kunt deze para meters in uw apparaat wijzigen, op voor waarde dat u ze ook wijzigt in de syslog-daemon op de onderverklikker agent van Azure.
    - Protocol = TCP
    - Poort = 514
    - Indeling = CEF
    - IP-adres: Zorg ervoor dat u de CEF-berichten verzendt naar het IP-adres van de virtuele machine die u voor dit doel hebt toegewezen.

   Deze oplossing ondersteunt syslog RFC 3164 of RFC 5424.

1. Als u wilt zoeken naar CEF-gebeurtenissen in Log Analytics, voert u `CommonSecurityLog` in het query venster in.

1. Ga door naar stap 3: de [verbinding valideren](connect-cef-verify.md).

> [!NOTE]
> **De bron van het veld TimeGenerated wijzigen**
>
> - Standaard vult de Log Analytics agent het veld *TimeGenerated* in het schema in met de tijd dat de agent de gebeurtenis van de syslog-daemon heeft ontvangen. Als gevolg hiervan wordt de tijd waarop de gebeurtenis is gegenereerd in het bronsysteem, niet opgeslagen in Azure Sentinel.
>
> - U kunt echter de volgende opdracht uitvoeren, zodat het script wordt gedownload en uitgevoerd `TimeGenerated.py` . Met dit script wordt de Log Analytics-agent zo geconfigureerd dat het veld *TimeGenerated* wordt ingevuld met de oorspronkelijke tijd van de gebeurtenis op het bron systeem, in plaats van de tijd die door de agent is ontvangen.
>
>    ```bash
>    wget -O TimeGenerated.py https://raw.githubusercontent.com/Azure/Azure-Sentinel/master/DataConnectors/CEF/TimeGenerated.py && python TimeGenerated.py {ws_id}
>    ```

## <a name="next-steps"></a>Volgende stappen

In dit document hebt u geleerd hoe u CEF-apparaten verbindt met Azure Sentinel. Zie de volgende artikelen voor meer informatie over Azure Sentinel:
- Meer informatie over het [verkrijgen van inzicht in uw gegevens en mogelijke bedreigingen](quickstart-get-visibility.md).
- Ga aan de slag met [het detecteren van bedreigingen met Azure Sentinel](./tutorial-detect-threats-built-in.md).