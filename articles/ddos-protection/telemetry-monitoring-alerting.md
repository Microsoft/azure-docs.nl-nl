---
title: DDoS-beschermingstelemetrie bekijken en configureren
description: Meer informatie over het weer geven en configureren van DDoS Protection-telemetrie.
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
ms.openlocfilehash: eefb658c689128c1d91858ac906c09e71d05cda6
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888943"
---
# <a name="view-and-configure-ddos-protection-telemetry"></a>DDoS-beschermingstelemetrie bekijken en configureren

Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. Uitgebreide telemetrie wordt weer gegeven via Azure Monitor, met inbegrip van gedetailleerde metrische gegevens tijdens de duur van een DDoS-aanval. U kunt waarschuwingen configureren voor een van de Azure Monitor metrische gegevens die door DDoS Protection worden weer gegeven. Logboek registratie kan verder worden geïntegreerd met [Azure Sentinel](https://docs.microsoft.com/azure/sentinel/connect-azure-ddos-protection), Splunk (Azure Event hubs), OMS Log Analytics en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure monitor.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Waarschuwingen voor DDoS Protection-metrische gegevens configureren
> * Telemetrie van DDoS-beveiliging gebruiken
> * DDoS-beperkings beleid weer geven
> * Waarschuwingen voor DDoS-beveiliging in Azure Security Center weer geven

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md)maken.

## <a name="configure-alerts-for-ddos-protection-metrics"></a>Waarschuwingen voor DDoS Protection-metrische gegevens configureren

U kunt een van de beschik bare metrische gegevens voor DDoS-beveiliging selecteren om u te waarschuwen wanneer er een actieve beperking is tijdens een aanval, met behulp van de configuratie van de Azure Monitor waarschuwingen. Wanneer aan de voor waarden wordt voldaan, ontvangt het opgegeven adres een e-mail waarschuwing:

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer **metrische gegevens** onder **gedeelde services**.
4. Voer uw eigen waarden in of Selecteer deze, of voer de volgende voorbeeld waarden in, accepteer de resterende standaard instellingen en selecteer **OK**:

    |Instelling                  |Waarde                                                                                               |
    |---------                |---------                                                                                           |
    |Naam                     | Voer _MyDdosAlert_ in.                                                                                |
    |Abonnement             | Selecteer het abonnement dat het open bare IP-adres bevat waarvoor u waarschuwingen wilt ontvangen.        |
    |Resourcegroep           | Selecteer de resource groep die het open bare IP-adres bevat waarvoor u waarschuwingen wilt ontvangen.      |
    |Resource                 | Selecteer het open bare IP-adres met het open bare IP-adres waarvoor u waarschuwingen wilt ontvangen. DDoS bewaakt open bare IP-adressen die zijn toegewezen aan bronnen in een virtueel netwerk. Als u geen resources met open bare IP-adressen in het virtuele netwerk hebt, moet u eerst een resource met een openbaar IP-adres maken. U kunt het open bare IP-adres van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) bewaken in het [virtuele netwerk voor Azure-Services](https://docs.microsoft.com/azure/virtual-network/virtual-network-for-azure-services#services-that-can-be-deployed-into-a-virtual-network), met uitzonde ring van Azure app service omgevingen en Azure VPN gateway. Als u wilt door gaan met deze zelf studie, kunt u snel een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine maken.                   |
    |Gegevens                   | Selecteer **onder DDoS-aanval of niet**.                                                                |
    |Drempelwaarde                | 1- **1** betekent dat u een aanval ondervindt. **0** betekent dat u geen aanval ondervindt.                         |
    |Periode                   | Selecteer de waarde die u kiest.                                                                   |
    |Melden via E-mail         | Schakel het selectie vakje in.                                                                                 |
    |Extra beheerder | Voer uw e-mail adres in als u geen e-mail eigenaar, bijdrager of lezer bent voor het abonnement. |

    Binnen een paar minuten van aanvals detectie ontvangt u een e-mail bericht van Azure Monitor metrische gegevens die er ongeveer als volgt uitzien:

    ![Waarschuwing voor aanvallen](./media/manage-ddos-protection/ddos-alert.png)


Zie [DDoS Detection valideren](test-through-simulations.md)als u een DDoS-aanval wilt simuleren om uw waarschuwing te valideren.

U kunt ook meer te weten komen over het [configureren van webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van waarschuwingen.

## <a name="use-ddos-protection-telemetry"></a>Telemetrie van DDoS-beveiliging gebruiken

Telemetrie voor een aanval wordt via Azure Monitor in realtime verschaft. De telemetrie is alleen beschikbaar voor de duur dat een openbaar IP-adres wordt beperkt. U ziet geen telemetrie voor of nadat een aanval is verholpen.

1. Selecteer **alle services** bovenaan, links van de portal.
2. Voer *monitor* in het vak **filter** in. Wanneer de **monitor** wordt weer gegeven in de resultaten, selecteert u deze.
3. Selecteer **metrische gegevens** onder **gedeelde services**.
4. Selecteer het **abonnement** en de **resource groep** die het open bare IP-adres bevatten waarvoor u telemetrie wilt.
5. Selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u telemetrie wilt.
6. Er wordt een reeks **beschik bare metrische gegevens** weer gegeven aan de linkerkant van het scherm. Deze metrische gegevens, indien geselecteerd, worden weer gegeven in de **grafiek Azure monitor metrische gegevens** op het scherm overzicht.
7. Selecteer het **aggregatie** type ten opzichte van **Max**

De metrische namen hebben verschillende pakket typen en bytes versus pakketten, met een basis constructie van label namen op elke metriek als volgt:

- **Verwijderde code naam** (bijvoorbeeld **inkomende pakketten DDoS**): het aantal pakketten dat is verwijderd door het DDoS-beveiligings systeem.
- **Naam van doorgestuurde code** (bijvoorbeeld door **sturen van binnenkomende pakketten DDoS**): het aantal pakketten dat door het DDoS-systeem is doorgestuurd naar het VIP-doel verkeer dat niet is gefilterd.
- **Er is geen label naam** (bijvoorbeeld **DDoS**): het totale aantal pakketten dat is geleverd aan het reinigings systeem, waarmee de som van de pakketten die worden verwijderd en doorgestuurd, wordt weer geven.

Met deze [Azure monitor waarschuwings regel](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) wordt een eenvoudige query uitgevoerd om te detecteren wanneer er een actieve DDoS-beperking optreedt. Zie [DDoS Detection valideren](test-through-simulations.md)als u een DDoS-aanval wilt simuleren om telemetrie te valideren. 

## <a name="view-ddos-mitigation-policies"></a>DDoS-beperkings beleid weer geven

DDoS Protection Standard heeft drie automatisch afgestemde beperkende beleids regels (TCP SYN, TCP & UDP) voor elk openbaar IP-adres van de beveiligde bron, in het virtuele netwerk waarop DDoS is ingeschakeld. U kunt de drempel waarden voor het beleid weer geven door de  **binnenkomende TCP-pakketten te selecteren voor het activeren van DDoS-beperking** en **binnenkomende UDP-pakketten om DDoS-beperkende metrische gegevens te activeren** met het **aggregatie** type ' Max ', zoals wordt weer gegeven in de volgende afbeelding:

![Risico beperkings beleid weer geven](./media/manage-ddos-protection/view-mitigation-policies.png)

Beleids drempels worden automatisch geconfigureerd via het samen stellen van het netwerk verkeer op basis van Azure machine learning. Alleen als de drempel waarde voor het beleid wordt geschonden, treedt er een DDoS-beperking op voor het IP-adres onder aanval.

## <a name="view-ddos-protection-alerts-in-azure-security-center"></a>Waarschuwingen voor DDoS-beveiliging in Azure Security Center weer geven

Azure Security Center bevat een lijst met [beveiligings waarschuwingen](/azure/security-center/security-center-managing-and-responding-alerts)met informatie over het onderzoeken en oplossen van problemen. Met deze functie krijgt u een uniforme weer gave van waarschuwingen, waaronder waarschuwingen met betrekking tot DDoS-aanvallen en de acties die zijn ondernomen om de aanval in een nabije periode te verminderen.
Er zijn twee specifieke waarschuwingen die u zult zien voor een detectie en beperking van de DDoS-aanval:

- **DDoS-aanval gedetecteerd voor openbaar IP-adres**: deze waarschuwing wordt gegenereerd wanneer de DDoS Protection-service detecteert dat een van uw open bare IP-adressen het doel is van een DDoS-aanval.
- **DDoS-aanval beperkt voor openbaar IP-** adres: deze waarschuwing wordt gegenereerd wanneer een aanval op het open bare IP-adressen is verholpen.
Als u de waarschuwingen wilt weer geven, opent u **Security Center** in de Azure Portal. Selecteer onder **bedreigingen beveiliging** de optie **beveiligings waarschuwingen**. De volgende scherm afbeelding toont een voor beeld van de waarschuwingen voor DDoS-aanvallen.

![DDoS-waarschuwing in Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

De waarschuwingen bevatten algemene informatie over het open bare IP-adres dat wordt vermeld onder aanvallen, geo-en bedreigings informatie en herstel stappen.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

- Waarschuwingen voor DDoS Protection-metrische gegevens configureren
- Telemetrie van DDoS-beveiliging gebruiken
- DDoS-beperkings beleid weer geven
- Waarschuwingen voor DDoS-beveiliging in Azure Security Center weer geven

Ga verder met de volgende zelf studie voor meer informatie over het configureren van rapporten en stroom logboeken voor het beperken van aanvallen.

> [!div class="nextstepaction"]
> [Rapporten en stroomlogboeken voor DDoS-aanvalsbeperking configureren](reports-and-flow-logs.md)