---
title: Web Application Firewall beleid voor geo-filtering configureren voor de Azure front-deur service
description: In deze zelfstudie leert u hoe u een beleid voor geofilters maakt en het beleid koppelt aan uw bestaande front-endhost van uw Front Door.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: conceptual
ms.date: 03/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 479b1d8ed1f4238486bb78e33a6139463578dbba
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563304"
---
# <a name="set-up-a-geo-filtering-waf-policy-for-your-front-door"></a>Een WAF-beleid voor geo-filtering instellen voor uw voor deur

In deze zelfstudie leert u hoe u Azure PowerShell gebruikt om een voorbeeldbeleid voor geofilters te maken en het beleid koppelt aan uw bestaande front-endhost van uw Front Door. In dit voorbeeldbeleid voor geofilters worden aanvragen uit alle andere landen/regio's, met uitzondering van de Verenigde Staten, geblokkeerd.

Als u nog geen abonnement op Azure hebt, maak dan nu een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="prerequisites"></a>Vereisten

Voordat u begint met het instellen van een beleid voor geofilters, stelt u uw PowerShell-omgeving in en maakt u een Front Door-profiel.
### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](/powershell/azure/) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en Az PowerShell te installeren.

#### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoog venster voor aanmelden

```
Install-Module -Name Az
Connect-AzAccount
```
Zorg ervoor dat bij u de huidige versie van PowerShellGet is geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.

```
Install-Module PowerShellGet -Force -AllowClobber
``` 
#### <a name="install-azfrontdoor-module"></a>De AzureRM.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-front-door-profile"></a>Een Front Door-profiel maken

Maak een Front Door-profiel door de instructies op te volgen in [Quickstart: Een Front Door-profiel maken](../../frontdoor/quickstart-create-front-door.md).

## <a name="define-geo-filtering-match-condition"></a>Overeenkomstvoorwaarde voor geofilters definiëren

Maak een voorbeeld van een overeenkomstvoorwaarde waarmee aanvragen worden geselecteerd die niet afkomstig zijn van 'US' (VS) met [New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) op parameters bij het maken van een overeenkomstvoorwaarde. De land-/regiocodes van twee letters naar land/regio-toewijzing zijn te vinden in [wat geo-filtering is op een domein voor Azure front-deur?](waf-front-door-geo-filtering.md).

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

Zoek de naam van de resourcegroep die het Front Door-profiel bevat met behulp van `Get-AzResourceGroup`. Maak vervolgens een `geoPolicy`-beleidsobject met `nonUSBlockRule` met behulp van [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy) in de opgegeven resourcegroep die het Front Door-profiel bevat. U moet een unieke naam opgeven voor het geo-beleid. 

In het volgende voor beeld wordt de naam van de resource groep *myResourceGroupFD1* met de veronderstelling dat u het voorste deur profiel hebt gemaakt met behulp van de instructies in de [Quick Start: een front deur](../../frontdoor/quickstart-create-front-door.md) -artikel maken. Vervang in het onderstaande voorbeeld de beleidsnaam *geoPolicyAllowUSOnly* door een unieke beleidsnaam.

```
$geoPolicy = New-AzFrontDoorWafPolicy `
-Name "geoPolicyAllowUSOnly" `
-resourceGroupName myResourceGroupFD1 `
-Customrule $nonUSBlockRule  `
-Mode Prevention `
-EnabledState Enabled
```

## <a name="link-waf-policy-to-a-front-door-frontend-host"></a>WAF-beleid koppelen aan een front-endhost van Front Door

Koppel het WAF-beleidobject aan de bestaande front-endhost van Front Door en werk de eigenschappen van Front Door bij. 

Als u dit wilt doen, haalt u eerst uw Front Door-object op met behulp van [Get-AzFrontDoor](/powershell/module/az.frontdoor/get-azfrontdoor). 

```
$geoFrontDoorObjectExample = Get-AzFrontDoor -ResourceGroupName myResourceGroupFD1
$geoFrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $geoPolicy.Id
```

Stel vervolgens de front-end-eigenschap WebApplicationFirewallPolicyLink in op de resourceId van de `geoPolicy`gebruikte [Set-AzFrontDoor](/powershell/module/az.frontdoor/set-azfrontdoor).

```
Set-AzFrontDoor -InputObject $geoFrontDoorObjectExample[0]
```

> [!NOTE] 
> U hoeft de eigenschap WebApplicationFirewallPolicyLink maar één keer in te stellen om een WAF-beleid te koppelen aan een front-endhost van Front Door. De volgende beleidsupdates worden automatisch toegepast op de front-endhost.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [Web Application Firewall](../overview.md).
- Lees hoe u [een Front Door maakt](../../frontdoor/quickstart-create-front-door.md).