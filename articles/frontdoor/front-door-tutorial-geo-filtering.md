---
title: 'Zelfstudie: WAF-beleid voor geofilters configureren - Azure Front Door'
description: In deze zelfstudie leert u hoe u een WAF-beleid voor geofilters maakt en het beleid koppelt aan uw bestaande front-endhost van uw Front Door.
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: tutorial
ms.date: 09/14/2020
ms.author: duau
ms.openlocfilehash: f9b5869f7dd472c50ffb2c1c0dce765200f53882
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91324022"
---
# <a name="tutorial-how-to-set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Zelfstudie: WAF-beleid voor geofilters instellen voor uw Front Door
In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een voorbeeldbeleid voor geofilters te maken en het beleid koppelt aan uw bestaande front-endhost van uw Front Door. In dit voorbeeldbeleid voor geofilters worden aanvragen uit alle andere landen/regio's, met uitzondering van de Verenigde Staten, geblokkeerd.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Overeenkomstvoorwaarde voor geofilters definiëren.
> - Overeenkomstvoorwaarde voor geofilters toevoegen aan een regel.
> - Regels toevoegen aan een beleid.
> - WAF-beleid koppelen aan een front-endhost van Front Door.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten
* Voordat u begint met het instellen van een beleid voor geofilters, stelt u uw PowerShell-omgeving in en maakt u een Front Door-profiel.
* Maak Front Door door de instructies op te volgen in [Quickstart: Een Front Door-profiel maken](quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Overeenkomstvoorwaarde voor geofilters definiëren

Maak een voorbeeld van een overeenkomstvoorwaarde waarmee aanvragen worden geselecteerd die niet afkomstig zijn van 'US' (VS) met [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) op parameters bij het maken van een overeenkomstvoorwaarde. Toewijzing tweeletterige land-/regiocodes aan land/regio worden [hier](front-door-geo-filtering.md) vermeld.

```azurepowershell-interactive
$nonUSGeoMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable RemoteAddr `
-OperatorProperty GeoMatch `
-NegateCondition $true `
-MatchValue "US"
```
## <a name="add-geo-filtering-match-condition-to-a-rule-with-action-and-priority"></a>Overeenkomstvoorwaarde voor geofilter toevoegen aan een regel met Actie en Prioriteit

Maak vervolgens een CustomRule-object `nonUSBlockRule` op basis van de overeenkomstvoorwaarde, een Actie en een Prioriteit met behulp van [New-AzFrontDoorWafCustomRuleObject](/powershell/module/az.frontdoor/new-azfrontdoorwafcustomruleobject).  Een CustomRule kan meerdere overeenkomstvoorwaarden hebben.  In dit voorbeeld is Actie ingesteld op Blokkeren en is Prioriteit ingesteld op 1, de hoogste prioriteit.

```
$nonUSBlockRule = New-AzFrontDoorWafCustomRuleObject `
-Name "geoFilterRule" `
-RuleType MatchRule `
-MatchCondition $nonUSGeoMatchCondition `
-Action Block `
-Priority 1
```
## <a name="add-rules-to-a-policy"></a>Regels toevoegen aan een beleid
Zoek de naam van de resourcegroep die het Front Door-profiel bevat met behulp van `Get-AzResourceGroup`. Maak vervolgens een `geoPolicy`-beleidsobject met `nonUSBlockRule` met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resourcegroep die het Front Door-profiel bevat. U moet een unieke naam opgeven voor het beleid voor geofilters. 

In het onderstaande voorbeeld wordt de naam van de resourcegroep *FrontDoorQS_rg0* gebruikt in de veronderstelling dat u het Front Door-profiel hebt gemaakt met behulp van de instructies in het artikel [Quickstart: Een Front Door maken](quickstart-create-front-door.md). Vervang in het onderstaande voorbeeld de beleidsnaam *geoPolicyAllowUSOnly* door een unieke beleidsnaam.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName FrontDoorQS_rg0 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```
## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF-beleid koppelen aan een front-endhost van Front Door
Koppel het WAF-beleidobject aan de bestaande front-endhost van Front Door en werk de eigenschappen van Front Door bij. 

Als u dit wilt doen, haalt u eerst uw Front Door-object op met behulp van [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName FrontDoorQS_rg0
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```
Stel vervolgens de front-end-eigenschap WebApplicationFirewallPolicyLink in op de resourceId van de `geoPolicy`gebruikte [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> U hoeft de eigenschap WebApplicationFirewallPolicyLink maar één keer in te stellen om een WAF-beleid te koppelen aan een front-endhost van Front Door. De volgende beleidsupdates worden automatisch toegepast op de front-endhost.

## <a name="clean-up-resources"></a>Resources opschonen

In de voorgaande stappen hebt u een geo-filterregel geconfigureerd die is gekoppeld aan een WAF-beleid. Vervolgens koppelde u het beleid aan een frontend-host van uw Front Door. Als u de geo-filterregel of het WAF-beleid niet meer nodig hebt, moet u eerst het beleid loskoppelen van de frontend-host voordat het WAF-beleid kan worden verwijderd.

:::image type="content" source="media/front-door-geo-filtering/front-door-disassociate-policy.png" alt-text="WAF-beleid loskoppelen":::

## <a name="next-steps"></a>Volgende stappen

Ga verder met de volgende zelfstudie voor meer informatie over het configureren van een Web Application Firewall voor uw Front Door.

> [!div class="nextstepaction"]
> [Web Application Firewall en Front Door](front-door-waf.md)
