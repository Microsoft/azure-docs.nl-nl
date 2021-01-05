---
title: DDoS-beveiligings waarschuwingen voor Azure DDoS Protection Standard weer geven en configureren
description: Meer informatie over het weer geven en configureren van DDoS-beveiligings waarschuwingen voor Azure DDoS Protection Standard.
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
ms.openlocfilehash: 4f9de2f956451cd6ab8bc8a7a0fc51903ec54694
ms.sourcegitcommit: 1140ff2b0424633e6e10797f6654359947038b8d
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97815899"
---
# <a name="view-and-configure-ddos-protection-alerts"></a>Waarschuwingen voor DDoS-beveiliging weer geven en configureren

Azure DDoS Protection Standard biedt uitgebreide aanval en visualisatie met DDoS-aanvals analyses. Klanten die hun virtuele netwerken beschermen tegen DDoS-aanvallen, hebben een gedetailleerde zicht baarheid van het aanvals verkeer en acties die zijn ondernomen om de aanval via rapporten voor aanvallen te beperken & beperkende stroom Logboeken. Uitgebreide telemetrie wordt weer gegeven via Azure Monitor, met inbegrip van gedetailleerde metrische gegevens tijdens de duur van een DDoS-aanval. U kunt waarschuwingen configureren voor een van de Azure Monitor metrische gegevens die door DDoS Protection worden weer gegeven. Logboek registratie kan verder worden geïntegreerd met [Azure Sentinel](../sentinel/connect-azure-ddos-protection.md), Splunk (Azure Event hubs), OMS Log Analytics en Azure Storage voor geavanceerde analyse via de diagnostische-interface voor Azure monitor.

In deze zelfstudie leert u het volgende:

> [!div class="checklist"]
> * Waarschuwingen configureren via Azure Monitor
> * Waarschuwingen configureren via de portal
> * Waarschuwingen in Azure Security Center weer geven
> * Uw waarschuwingen valideren en testen

## <a name="prerequisites"></a>Vereisten

- Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.
- Voordat u de stappen in deze zelf studie kunt volt ooien, moet u eerst een [Azure DDoS Standard-beveiligings plan](manage-ddos-protection.md) maken en moet DDoS Protection standaard zijn ingeschakeld op een virtueel netwerk.
- DDoS bewaakt open bare IP-adressen die zijn toegewezen aan bronnen in een virtueel netwerk. Als u geen resources met open bare IP-adressen in het virtuele netwerk hebt, moet u eerst een resource met een openbaar IP-adres maken. U kunt het open bare IP-adres van alle resources die zijn geïmplementeerd via Resource Manager (niet klassiek) bewaken in het [virtuele netwerk voor Azure-Services](../virtual-network/virtual-network-for-azure-services.md#services-that-can-be-deployed-into-a-virtual-network) (inclusief Azure load balancers waarbij de virtuele machines in het virtuele netwerk zich bevinden), met uitzonde ring van Azure app service omgevingen en Azure VPN gateway. Als u wilt door gaan met deze zelf studie, kunt u snel een virtuele [Windows](../virtual-machines/windows/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine maken.     

## <a name="configure-alerts-through-azure-monitor"></a>Waarschuwingen configureren via Azure Monitor

Met deze sjablonen kunt u waarschuwingen configureren voor alle open bare IP-adressen waarvoor u diagnostische logboek registratie hebt ingeschakeld. Om deze waarschuwings sjablonen te kunnen gebruiken, hebt u eerst een Log Analytics-werk ruimte nodig waarvoor Diagnostische instellingen zijn ingeschakeld. Zie [DDoS diagnostische logboek registratie weer geven en configureren](diagnostic-logging.md).

### <a name="azure-monitor-alert-rule"></a>Waarschuwings regel Azure Monitor
Met deze [Azure monitor waarschuwings regel](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/Azure%20Monitor%20Alert%20-%20DDoS%20Mitigation%20Started) wordt een eenvoudige query uitgevoerd om te detecteren wanneer er een actieve DDoS-beperking optreedt. Dit duidt op een mogelijke aanval. Actie groepen kunnen worden gebruikt om acties als resultaat van de waarschuwing aan te roepen.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FAzure%2520Monitor%2520Alert%2520-%2520DDoS%2520Mitigation%2520Started%2FDDoSMitigationStarted.json)

### <a name="azure-monitor-alert-rule-with-logic-app"></a>Waarschuwings regel Azure Monitor met logische app

Met deze [sjabloon](https://github.com/Azure/Azure-Network-Security/tree/master/Azure%20DDoS%20Protection/DDoS%20Mitigation%20Alert%20Enrichment) worden de benodigde onderdelen van een verrijkte waarschuwing voor DDoS-beperking geïmplementeerd: Azure monitor waarschuwings regel, actie groep en logische app. Het resultaat van het proces is een e-mail waarschuwing met details over het IP-adres onder aanval, met inbegrip van informatie over de bron die aan het IP is gekoppeld. De eigenaar van de resource wordt toegevoegd als ontvanger van het e-mail bericht, samen met het beveiligings team. Er wordt ook een Basic-beschikbaarheids test voor toepassingen uitgevoerd en de resultaten worden opgenomen in de e-mail waarschuwing.

[![Implementeren in Azure](../media/template-deployments/deploy-to-azure.svg)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2FAzure-Network-Security%2Fmaster%2FAzure%2520DDoS%2520Protection%2FDDoS%2520Mitigation%2520Alert%2520Enrichment%2FEnrich-DDoSAlert.json)

## <a name="configure-alerts-through-portal"></a>Waarschuwingen configureren via de portal

U kunt een van de beschik bare metrische gegevens voor DDoS-beveiliging selecteren om u te waarschuwen wanneer er een actieve beperking is tijdens een aanval, met behulp van de configuratie van de Azure Monitor waarschuwingen. 

1. Meld u aan bij de [Azure Portal](https://portal.azure.com/) en blader naar uw DDoS Protection-abonnement.
2. Selecteer **Metrische gegevens** onder **Bewaking**.
3. Selecteer **nieuwe waarschuwings regel** in de grijze navigatie balk. 
4. Voer uw eigen waarden in of Selecteer deze, of voer de volgende voorbeeld waarden in, accepteer de resterende standaard instellingen en selecteer vervolgens **waarschuwings regel maken**:

    |Instelling                  |Waarde                                                                                               |
    |---------                |---------                                                                                           |
    | Bereik                   | Selecteer **Resource selecteren**. </br> Selecteer het **abonnement** met het open bare IP-adres dat u wilt registreren, selecteer **openbaar IP-adres** voor het **bron type** en selecteer vervolgens het specifieke open bare IP-adres waarvoor u metrische gegevens wilt vastleggen. </br> Selecteer **Gereed**. | 
    | Voorwaarde | Selecteer **voor waarde selecteren**. </br> Onder signaal naam selecteert u **onder DDoS-aanval of niet**. </br> Selecteer onder **operator** de optie **groter dan of gelijk aan**. </br> Onder **aggregatie type** selecteert u **maximum**. </br> Voer bij **drempel waarde** *1* in. Voor de **onder DDoS-aanval of niet** metrisch betekent **0** dat u geen aanval ondervindt terwijl **1** betekent dat u een aanval ondervindt. </br> Selecteer **Gereed**. | 
    | Acties | Selecteer **actie groepen toevoegen**. </br> Selecteer **Actiegroep maken**. </br> Onder **meldingen**, onder **meldings type**, selecteert u **e-mail/SMS-bericht/push/stem**. </br> Voer onder **naam** _MyUnderAttackEmailAlert_ in. </br> Klik op de knop bewerken, selecteer **e-mail adres** en zoveel van de volgende opties die u nodig hebt, en selecteer vervolgens **OK**. </br> Selecteer **Controleren en maken**. | 
    | Details van waarschuwingsregel | Voer _MyDdosAlert_ in bij **naam van waarschuwings regel**. |

Binnen een paar minuten van aanvals detectie ontvangt u een e-mail bericht van Azure Monitor metrische gegevens die er ongeveer als volgt uitzien:

![Waarschuwing voor aanvallen](./media/manage-ddos-protection/ddos-alert.png)

U kunt ook meer te weten komen over het [configureren van webhooks](../azure-monitor/platform/alerts-webhooks.md?toc=%2fazure%2fvirtual-network%2ftoc.json) en [Logic apps](../logic-apps/logic-apps-overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) voor het maken van waarschuwingen.

## <a name="view-alerts-in-azure-security-center"></a>Waarschuwingen in Azure Security Center weer geven

Azure Security Center bevat een lijst met [beveiligings waarschuwingen](../security-center/security-center-managing-and-responding-alerts.md)met informatie over het onderzoeken en oplossen van problemen. Met deze functie krijgt u een uniforme weer gave van waarschuwingen, waaronder waarschuwingen met betrekking tot DDoS-aanvallen en de acties die zijn ondernomen om de aanval in een nabije periode te verminderen.
Er zijn twee specifieke waarschuwingen die u zult zien voor een detectie en beperking van de DDoS-aanval:

- **DDoS-aanval gedetecteerd voor openbaar IP-adres**: deze waarschuwing wordt gegenereerd wanneer de DDoS Protection-service detecteert dat een van uw open bare IP-adressen het doel is van een DDoS-aanval.
- **DDoS-aanval beperkt voor openbaar IP-** adres: deze waarschuwing wordt gegenereerd wanneer een aanval op het open bare IP-adressen is verholpen.
Als u de waarschuwingen wilt weer geven, opent u **Security Center** in de Azure Portal. Selecteer onder **bedreigingen beveiliging** de optie **beveiligings waarschuwingen**. De volgende scherm afbeelding toont een voor beeld van de waarschuwingen voor DDoS-aanvallen.

![DDoS-waarschuwing in Azure Security Center](./media/manage-ddos-protection/ddos-alert-asc.png)

De waarschuwingen bevatten algemene informatie over het open bare IP-adres dat wordt vermeld onder aanvallen, geo-en bedreigings informatie en herstel stappen.

## <a name="validate-and-test"></a>Valideren en testen

Zie [DDoS Detection valideren](test-through-simulations.md)als u een DDoS-aanval wilt simuleren om uw waarschuwingen te valideren.

## <a name="next-steps"></a>Volgende stappen

In deze zelfstudie heeft u het volgende geleerd:

- Waarschuwingen configureren via Azure Monitor
- Waarschuwingen configureren via de portal
- Waarschuwingen in Azure Security Center weer geven
- Uw waarschuwingen valideren en testen

Voor informatie over het testen en simuleren van een DDoS-aanval raadpleegt u de hand leiding voor simulatie tests:

> [!div class="nextstepaction"]
> [Testen via simulaties](test-through-simulations.md)