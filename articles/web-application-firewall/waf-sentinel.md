---
title: Azure Sentinel gebruiken met Azure Web Application firewall
description: Dit artikel laat u zien hoe u Azure Sentinel kunt gebruiken met Azure Web Application firewall (WAF)
services: web-application-firewall
author: TreMansdoerfer
ms.service: web-application-firewall
ms.date: 10/12/2020
ms.author: victorh
ms.topic: how-to
ms.openlocfilehash: 6e1d9b8a53eaf69c2294ab42dc0718863e6c1837
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99804934"
---
# <a name="using-azure-sentinel-with-azure-web-application-firewall"></a>Azure Sentinel gebruiken met Azure Web Application firewall

Azure Web Application firewall (WAF) in combi natie met Azure Sentinel kan beveiligings gegevens gebeurtenis beheer bieden voor WAF-resources. Azure Sentinel biedt beveiligings analyse met behulp van Log Analytics, waarmee u eenvoudig uw WAF-gegevens kunt opdelen en weer geven. Met Azure Sentinel hebt u toegang tot vooraf gemaakte werkmappen en kunt u deze aanpassen aan de behoeften van uw organisatie. De werkmap kan Analytics weer geven voor WAF op Azure Content Delivery Network (CDN), WAF in azure front-deur en WAF op Application Gateway in verschillende abonnementen en werk ruimten.

## <a name="waf-log-analytics-categories"></a>WAF log Analytics-Categorieën

WAF log Analytics wordt onderverdeeld in de volgende categorieën:  

- Alle uitgevoerde WAF-acties 
- Top 40 geblokkeerde aanvragen URI-adressen 
- Belangrijkste 50 gebeurtenis triggers,  
- Berichten in de loop van de tijd 
- Volledige bericht gegevens 
- Aanvals gebeurtenissen op berichten  
- Aanvals gebeurtenissen na verloop van tijd 
- ID-filter bijhouden 
- ID-berichten bijhouden 
- Top 10 van IP-adressen voor aanvallen 
- Aanvals berichten van IP-adressen 

## <a name="waf-workbook-examples"></a>Voor beelden van WAF-werkmap

In de volgende WAF werkmap-voor beelden worden voorbeeld gegevens weer gegeven:

:::image type="content" source="media//waf-sentinel/waf-actions-filter.png" alt-text="WAF-actie filter":::

:::image type="content" source="media//waf-sentinel/top-50-event-trigger.png" alt-text="Belangrijkste 50 gebeurtenissen":::

:::image type="content" source="media//waf-sentinel/attack-events.png" alt-text="Aanvals gebeurtenissen":::

:::image type="content" source="media//waf-sentinel/top-10-attacking-ip-address.png" alt-text="Top 10 van IP-adressen voor aanvallen":::

## <a name="launch-a-waf-workbook"></a>Een WAF-werkmap openen

De WAF-werkmap werkt voor alle Azure front-deur, Application Gateway en CDN Waf's. Voordat u de gegevens van deze resources verbindt, moet log Analytics zijn ingeschakeld voor uw bron. 

Als u log Analytics voor elke resource wilt inschakelen, gaat u naar uw afzonderlijke Azure front-deur, Application Gateway of CDN-resource:

1. Selecteer **Diagnostische instellingen**.
2. Selecteer **+ Diagnostische instelling toevoegen**. 
3. Op de pagina Diagnostische instellingen:
   1. Typ een naam. 
   1. Selecteer **verzenden naar log Analytics**. 
   1. Kies de werk ruimte logboek bestemming. 
   1. Selecteer de logboek typen die u wilt analyseren:
      1. Application Gateway: ' ApplicationGatewayAccessLog ' en ' ApplicationGatewayFirewallLog '
      1. Azure front-deur: ' FrontDoorAccessLog ' en ' FrontDoorFirewallLog '
      1. CDN: ' AzureCdnAccessLog '
   1. Selecteer **Opslaan**.

   :::image type="content" source="media//waf-sentinel/diagnostics-setting.png" alt-text="Diagnostische instelling":::

4. Op de start pagina van Azure typt u **Azure Sentinel** in de zoek balk en selecteert u de **Azure-Sentinel** -resource. 
2. Selecteer een al actieve werk ruimte of maak een nieuwe werk ruimte. 
3. Klik in het linkerdeel venster onder **Configuration** Select **Data connectors**.
4. Zoek naar **micro soft Web Application firewall** en selecteer **micro soft Web Application firewall (WAF)**. Selecteer de pagina **connector openen** aan de rechter kant.

   :::image type="content" source="media//waf-sentinel/data-connectors.png" alt-text="Gegevensconnectors":::

8. Volg de instructies onder **configuratie** voor elke WAF-resource waarvoor u logboek analyse gegevens wilt hebben als u dit nog niet eerder hebt gedaan.
6. Als u de afzonderlijke WAF-resources hebt geconfigureerd, selecteert u het tabblad **volgende stappen** . Selecteer een van de aanbevolen werkmappen. In deze werkmap worden alle logboek analyse gegevens gebruikt die eerder zijn ingeschakeld. Er moet nu een Working WAF-werkmap bestaan voor uw WAF-resources.

   :::image type="content" source="media//waf-sentinel/waf-workbooks.png" alt-text="WAF werkmappen":::


## <a name="next-steps"></a>Volgende stappen

- [Meer informatie over Azure Sentinel](../sentinel/overview.md)
- [Meer informatie over Azure Monitor werkmappen](../azure-monitor/platform/workbooks-overview.md)
