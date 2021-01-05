---
title: Simulatie tests Azure DDoS Protection
description: Meer informatie over testen door simulaties
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/08/2020
ms.author: yitoh
ms.openlocfilehash: e3a665e3615c9ff3a68cf13eeaef5e8f41632f6a
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97900357"
---
# <a name="test-through-simulations"></a>Testen via simulaties

Het is een goed idee om uw hypo Thesen te testen op de manier waarop uw services reageren op aanvallen door periodieke simulaties uit te voeren. Controleer tijdens het testen of uw services of toepassingen op de verwachte manier blijven functioneren en er geen onderbreking is voor de gebruikers ervaring. Identificeer hiaten uit een technologie en proces oogpunt en neem ze op in de DDoS-respons strategie. We raden u aan om dergelijke tests uit te voeren in faserings omgevingen of tijdens niet-piek uren om de impact op de productie omgeving te minimaliseren.

We hebben een partnerschap gemaakt met [BreakingPoint Cloud](https://www.ixiacom.com/products/breakingpoint-cloud), een self-service Traffic generator, om een interface te bouwen waarin Azure-klanten verkeer kunnen genereren voor open bare eind punten van DDoS Protection voor simulaties. U kunt de simulatie gebruiken voor het volgende:

- Valideer hoe Azure DDoS Protection uw Azure-resources helpt beschermen tegen DDoS-aanvallen.
- Optimaliseer uw incidenten respons proces tijdens DDoS-aanval.
- Conformiteit van document DDoS.
- Train uw netwerk beveiligings teams.

## <a name="prerequisites"></a>Vereisten

- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md) maken met beveiligde open bare IP-adressen.
- U moet eerst een account met [BreakingPoint Cloud](http://breakingpoint.cloud/)maken. 

## <a name="configure-a-ddos-test-attack"></a>Een DDoS-test aanval configureren

1. Voer de volgende waarden in of Selecteer deze en selecteer vervolgens **Test starten**:

    |Instelling        |Waarde                                              |
    |---------      |---------                                          |
    |Doel-IP-adres           | Voer een van uw open bare IP-adressen in die u wilt testen.                     |
    |Poortnummer   | Voer _443_ in.                       |
    |DDoS-profiel | Mogelijke waarden zijn **DNS-Flooding**, **NTPv2 Flooding**, **SSDP-Flooding**, **TCP SYN Flooding**, **UDP 64B flood**, **UDP 128B flood**, **UDP 256B flood**, **UDP 512B flood**, **UDP 1024B flood**, **UDP 1514B flood**, **UDP-fragmentatie** **UDP memcached**.|
    |Test grootte       | Mogelijke waarden zijn **100.000 PPS, 50 Mbps en 4 bron-ip's**, **van persoonlijkheden PPS, 100 Mbps en 8 bron-ip's**, **400K PPS, 200 Mbps en 16 bron** Ip's, **800K PPS, 400 Mbps en 32 bron ip's**.                                  |
    |Test duur | Mogelijke waarden zijn **10 minuten**, **15 minuten**, **20 minuten**, **25 minuten**, **30 minuten**.|

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

Dit [API-script](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Breaking%20Point%20SDK) kan worden gebruikt om DDoS-tests te automatiseren door één keer uit te voeren of door gebruik te maken van cron om regel matige tests te plannen. Dit is handig om te controleren of uw logboek registratie juist is geconfigureerd en dat detectie-en antwoord procedures effectief zijn. De scripts vereisen een Linux-besturings systeem (getest met Ubuntu 18,04 LTS) en python 3. Installeer de vereisten en API-client met behulp van het meegeleverde script of door de documentatie op de [BreakingPoint-Cloud](http://breakingpoint.cloud/) website te gebruiken.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [weer geven en configureren van DDoS Protection-telemetrie](telemetry.md).
- Meer informatie over het [weer geven en configureren van DDoS diagnostische logboek registratie](diagnostic-logging.md).
- Meer informatie over het [inschakelen van DDoS snelle reacties](ddos-rapid-response.md).
