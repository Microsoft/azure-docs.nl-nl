---
title: Simulatie tests Azure DDoS Protection
description: Meer informatie over testen door simulaties
services: ddos-protection
documentationcenter: na
author: aletheatoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: 4e6c6b441684d3877a8b85f40b650b257a5159da
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107106068"
---
# <a name="test-through-simulations"></a>Testen via simulaties

Het is een goed idee om uw hypo Thesen te testen op de manier waarop uw services reageren op aanvallen door periodieke simulaties uit te voeren. Controleer tijdens het testen of uw services of toepassingen op de verwachte manier blijven functioneren en er geen onderbreking is voor de gebruikers ervaring. Identificeer hiaten uit een technologie en proces oogpunt en neem ze op in de DDoS-respons strategie. We raden u aan om dergelijke tests uit te voeren in faserings omgevingen of tijdens niet-piek uren om de impact op de productie omgeving te minimaliseren.

We zijn een partnerschap aangegaan met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), een selfservice-generator voor verkeer, om een interface te bouwen waarin Azure-klanten verkeer kunnen genereren via openbare eindpunten met DDoS Protection, voor simulaties. U kunt de simulatie gebruiken voor het volgende:

- Valideren hoe Azure DDoS Protection uw Azure-resources kan beschermen tegen DDoS-aanvallen.
- Uw proces van incidentreacties optimaliseren tijdens een DDoS-aanval.
- Naleving van DDoS documenteren.
- Uw netwerkbeveiligingsteams trainen.

> [!NOTE]
> BreakingPoint Cloud is alleen beschikbaar voor de open bare Cloud.

## <a name="prerequisites"></a>Vereisten

- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md) maken met beveiligde open bare IP-adressen.
- U moet eerst een account met [BreakingPoint Cloud](http://breakingpoint.cloud/)maken. 

## <a name="configure-a-ddos-test-attack"></a>Een DDoS-test aanval configureren

1. Voer de volgende waarden in of Selecteer deze en selecteer vervolgens **Test starten**:

    |Instelling        |Waarde                                              |
    |---------      |---------                                          |
    |Doel-IP-adres           | Voer een van uw open bare IP-adressen in die u wilt testen.                     |
    |Poortnummer   | Voer _443_ in.                       |
    |DDoS-profiel | Mogelijke waarden zijn `DNS Flood` , `NTPv2 Flood` , `SSDP Flood` , `TCP SYN Flood` , `UDP 64B Flood` , `UDP 128B Flood` , `UDP 256B Flood` , `UDP 512B Flood` , `UDP 1024B Flood` , `UDP 1514B Flood` , `UDP Fragmentation` , `UDP Memcached` .|
    |Test grootte       | Mogelijke waarden zijn `100K pps, 50 Mbps and 4 source IPs` :,, `200K pps, 100 Mbps and 8 source IPs` `400K pps, 200Mbps and 16 source IPs` , `800K pps, 400 Mbps and 32 source IPs` .                                  |
    |Test duur | Mogelijke waarden zijn `10 Minutes` , `15 Minutes` , `20 Minutes` , `25 Minutes` , `30 Minutes` .|

Dit moet er nu als volgt uitzien:

![Simulatie voor beeld van DDoS-aanval: BreakingPoint Cloud](./media/ddos-attack-simulation/ddos-attack-simulation-example-1.png)

## <a name="monitor-and-validate"></a>Controleren en valideren

1. Meld u aan bij https://portal.azure.com en ga naar uw abonnement.
1. Selecteer het open bare IP-adres waarop u de aanval hebt getest.
1. Selecteer **Metrische gegevens** onder **Bewaking**.
1. Voor **metrische gegevens** selecteert u _onder DDoS-aanval of niet_.

Zodra de bron is aangevallen, ziet u dat de waarde wordt gewijzigd van **0** in **1**, zoals in de volgende afbeelding:

![Simulatie van DDoS-aanvals voorbeeld: Portal](./media/ddos-attack-simulation/ddos-attack-simulation-example-2.png)

### <a name="breakingpoint-cloud-api-script"></a>BreakingPoint Cloud-API-script

Dit [API-script](https://aka.ms/ddosbreakingpoint) kan worden gebruikt om DDoS-tests te automatiseren door één keer uit te voeren of door gebruik te maken van cron om regel matige tests te plannen. Dit is handig om te controleren of uw logboek registratie juist is geconfigureerd en dat detectie-en antwoord procedures effectief zijn. De scripts vereisen een Linux-besturings systeem (getest met Ubuntu 18,04 LTS) en python 3. Installeer de vereisten en API-client met behulp van het meegeleverde script of door de documentatie op de [BreakingPoint-Cloud](http://breakingpoint.cloud/) website te gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [weer geven en configureren van DDoS Protection-telemetrie](telemetry.md).
- Meer informatie over het [weer geven en configureren van DDoS diagnostische logboek registratie](diagnostic-logging.md).
- Meer informatie over het [inschakelen van DDoS snelle reacties](ddos-rapid-response.md).
