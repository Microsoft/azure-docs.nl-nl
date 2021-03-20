---
title: Telemetrie van DDoS Protection voor Azure DDoS Protection Standard weer geven en configureren
description: Meer informatie over het weer geven en configureren van DDoS Protection-telemetrie voor Azure DDoS Protection Standard.
services: ddos-protection
documentationcenter: na
author: yitoh
ms.service: ddos-protection
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 12/28/2020
ms.author: yitoh
ms.openlocfilehash: 0be184921ff0bd6b98dd2975acb4e0d5c8b26ba0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101716190"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS-beschermingstelemetrie bekijken en configureren

Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. Uitgebreide telemetrie wordt weer gegeven via Azure Monitor, met inbegrip van gedetailleerde metrische gegevens tijdens de duur van een DDoS-aanval. U kunt waarschuwingen configureren voor alle gewenste metrische Azure Monitor-gegevens die beschikbaar zijn gemaakt via DDoS Protection. Logboek registratie kan verder worden geïntegreerd met [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event hubs), OMS Log Analytics en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure monitor.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Telemetrie van DDoS-beveiliging weer geven
> * DDoS-beperkings beleid weer geven
> * DDoS Protection-telemetrie valideren en testen

### <a name="metrics"></a>Metrische gegevens

> [!NOTE]
> Terwijl er meerdere opties voor **aggregatie** worden weer gegeven op Azure Portal, worden alleen de aggregatie typen die in de onderstaande tabel staan vermeld, voor elke metriek ondersteund. Onze excuses voor deze Verwar ring en we werken eraan om dit op te lossen.

De volgende [metrische gegevens](../azure-monitor/essentials/metrics-supported.md#microsoftnetworkpublicipaddresses) zijn beschikbaar voor Azure DDoS Protection Standard. Deze metrische gegevens kunnen ook worden geëxporteerd via Diagnostische instellingen (Zie [logboek registratie voor diagnostische gegevens van DDoS weer geven en configureren](diagnostic-logging.md)).


| Metrisch | Weergave naam voor metrische gegevens | Eenheid | Aggregatietype | Beschrijving |
| --- | --- | --- | --- | --- |
| BytesDroppedDDoS | Binnenkomende bytes verloren DDoS | BytesPerSecond | Maximum | Binnenkomende bytes verloren DDoS| 
| BytesForwardedDDoS | Doorgestuurde binnenkomende bytes DDoS | BytesPerSecond | Maximum | Doorgestuurde binnenkomende bytes DDoS |
| BytesInDDoS | Binnenkomende bytes DDoS | BytesPerSecond | Maximum | Binnenkomende bytes DDoS |
| DDoSTriggerSYNPackets | Inkomende SYN-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum | Inkomende SYN-pakketten om DDoS-beperking te activeren |
| DDoSTriggerTCPPackets | Inkomende TCP-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum | Inkomende TCP-pakketten om DDoS-beperking te activeren |
| DDoSTriggerUDPPackets | Inkomende UDP-pakketten om DDoS-beperking te activeren | CountPerSecond | Maximum | Inkomende UDP-pakketten om DDoS-beperking te activeren |
| IfUnderDDoSAttack | Onder DDoS-aanval of niet | Count | Maximum | Onder DDoS-aanval of niet |
| PacketsDroppedDDoS | DDoS inkomende pakketten verwijderd | CountPerSecond | Maximum | DDoS inkomende pakketten verwijderd |
| PacketsForwardedDDoS | DDoS inkomende pakketten doorgestuurd | CountPerSecond | Maximum | DDoS inkomende pakketten doorgestuurd |
| PacketsInDDoS | DDoS inkomende pakketten | CountPerSecond | Maximum | DDoS inkomende pakketten |
| TCPBytesDroppedDDoS | DDoS binnenkomende TCP-bytes | BytesPerSecond | Maximum | DDoS binnenkomende TCP-bytes |
| TCPBytesForwardedDDoS | DDoS doorgestuurde binnenkomende TCP-bytes | BytesPerSecond | Maximum | DDoS doorgestuurde binnenkomende TCP-bytes |
| TCPBytesInDDoS | DDoS binnenkomende TCP-bytes | BytesPerSecond | Maximum | DDoS binnenkomende TCP-bytes |
| TCPPacketsDroppedDDoS | DDoS binnenkomende TCP-pakketten | CountPerSecond | Maximum | DDoS binnenkomende TCP-pakketten |
| TCPPacketsForwardedDDoS | Doorgestuurde binnenkomende TCP-pakketten DDoS | CountPerSecond | Maximum | Doorgestuurde binnenkomende TCP-pakketten DDoS |
| TCPPacketsInDDoS | Binnenkomende TCP-pakketten DDoS | CountPerSecond | Maximum | Binnenkomende TCP-pakketten DDoS |
| UDPBytesDroppedDDoS | Binnenkomend UDP-bytes verloren DDoS | BytesPerSecond | Maximum | Binnenkomend UDP-bytes verloren DDoS |
| UDPBytesForwardedDDoS | Doorgestuurde binnenkomende UDP-bytes DDoS | BytesPerSecond | Maximum | Doorgestuurde binnenkomende UDP-bytes DDoS |
| UDPBytesInDDoS | Binnenkomende UDP-bytes DDoS | BytesPerSecond | Maximum | Binnenkomende UDP-bytes DDoS |
| UDPPacketsDroppedDDoS | Verwijderde binnenkomende UDP-pakketten DDoS | CountPerSecond | Maximum | Verwijderde binnenkomende UDP-pakketten DDoS |
| UDPPacketsForwardedDDoS | Door inkomende UDP-pakketten DDoS doorgestuurd | CountPerSecond | Maximum | Door inkomende UDP-pakketten DDoS doorgestuurd |
| UDPPacketsInDDoS | Binnenkomende UDP-pakketten DDoS | CountPerSecond | Maximum | Binnenkomende UDP-pakketten DDoS |

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md) maken en moet DDoS Protection standaard zijn ingeschakeld op een virtueel netwerk.
- DDoS bewaakt open bare IP-adressen die zijn toegewezen aan bronnen in een virtueel netwerk. Als u geen resources met openbare IP-adressen in het virtuele netwerk hebt, moet u eerst een resource met een openbaar IP-adres maken. U kunt het open bare IP-adres van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) bewaken in het [virtuele netwerk voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusief Azure load balancers waarbij de virtuele machines in het virtuele netwerk zich bevinden), met uitzonde ring van Azure app service omgevingen. Als u wilt door gaan met deze zelf studie, kunt u snel een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine maken.  

## <a name="view-ddos-protection-telemetry"></a>Telemetrie van DDoS-beveiliging weer geven

Azure Monitor biedt in realtime telemetriegegevens over aanvallen. Telemetrie is alleen beschikbaar wanneer een openbaar IP-adres wordt beperkt. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en blader naar uw DDoS Protection-abonnement.
2. Selecteer **Metrische gegevens** onder **Bewaking**.
3. Selecteer **Bereik**. Selecteer het **abonnement** met het open bare IP-adres dat u wilt registreren, selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen en selecteer vervolgens **Toep assen**.
4. Selecteer het **aggregatie** type ten opzichte van **Max**.

De metrische namen hebben verschillende pakket typen en bytes versus pakketten, met een basis constructie van label namen op elke metriek als volgt:

- **Verwijderde code naam** (bijvoorbeeld **inkomende pakketten DDoS**): het aantal pakketten dat is verwijderd door het DDoS-beveiligings systeem.
- **Naam van doorgestuurde code** (bijvoorbeeld door **sturen van binnenkomende pakketten DDoS**): het aantal pakketten dat door het DDoS-systeem is doorgestuurd naar het VIP-doel verkeer dat niet is gefilterd.
- **Er is geen label naam** (bijvoorbeeld **DDoS**): het totale aantal pakketten dat is geleverd aan het reinigings systeem, waarmee de som van de pakketten die worden verwijderd en doorgestuurd, wordt weer geven.

## <a name="view-ddos-mitigation-policies"></a>DDoS-beperkings beleid weer geven

DDoS Protection Standard heeft drie automatisch afgestemde beperkende beleids regels (TCP SYN, TCP & UDP) voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarop DDoS is ingeschakeld. U kunt de drempel waarden voor het beleid weer geven door de  **binnenkomende TCP-pakketten te selecteren voor het activeren van DDoS-beperking** en **binnenkomende UDP-pakketten om DDoS-beperkende metrische gegevens te activeren** met het **aggregatie** type ' Max ', zoals wordt weer gegeven in de volgende afbeelding:

![Risico beperkings beleid weer geven](./media/manage-ddos-protection/view-mitigation-policies.png)

Beleids drempels worden automatisch geconfigureerd via het samen stellen van het netwerk verkeer op basis van Azure machine learning. Alleen als de drempel waarde voor het beleid wordt geschonden, treedt er een DDoS-beperking op voor het IP-adres onder aanval.

## <a name="validate-and-test"></a>Valideren en testen

Zie [DDoS Detection valideren](test-through-simulations.md)als u een DDoS-aanval wilt simuleren om DDoS Protection-telemetrie te valideren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

- Waarschuwingen voor DDoS Protection-metrische gegevens configureren
- Telemetrie van DDoS-beveiliging weer geven
- DDoS-beperkings beleid weer geven
- DDoS Protection-telemetrie valideren en testen

Ga verder met de volgende zelf studie voor meer informatie over het configureren van rapporten en stroom logboeken voor het beperken van aanvallen.

> [!div class="nextstepaction"]
> [Registratie in DDoS-diagnoselogboek bekijken en configureren](diagnostic-logging.md)