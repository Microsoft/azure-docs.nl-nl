---
title: Aangepaste antwoorden configureren voor Web Application firewall (WAF) met Azure front deur
description: Meer informatie over het configureren van een aangepaste respons code en een bericht wanneer WAF een aanvraag blokkeert.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 06/10/2020
ms.author: victorh
ms.reviewer: tyao
ms.openlocfilehash: 8fc6e71494df36cd6f823661b18e4a3d8ce2938c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94563678"
---
# <a name="configure-a-custom-response-for-azure-web-application-firewall-waf"></a>Een aangepast antwoord configureren voor de firewall van Azure Web Application (WAF)

Wanneer WAF een aanvraag blokkeert vanwege een overeenkomende regel, retourneert deze standaard een 403-status code met **de aanvraag is een geblokkeerd** bericht. Het standaard bericht bevat ook de teken reeks voor het volgen van verwijzingen die kan worden gebruikt om een koppeling te maken naar [logboek vermeldingen](./waf-front-door-monitor.md) voor de aanvraag.  U kunt een aangepaste antwoord status code en een aangepast bericht met een verwijzing naar de teken reeks voor uw use-case configureren. In dit artikel wordt beschreven hoe u een aangepaste antwoord pagina configureert wanneer een aanvraag wordt geblokkeerd door WAF.

## <a name="configure-custom-response-status-code-and-message-use-portal"></a>Aangepaste antwoord status code en het bericht gebruik van de portal configureren

U kunt een aangepaste antwoord status code en hoofd tekst onder beleids instellingen configureren in de WAF-Portal.

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response-settings.png" alt-text="WAF-beleids instellingen":::

In het bovenstaande voor beeld hebben we de antwoord code als 403 bewaard en een kort bericht van ' Neem contact met ons opgenomen ', zoals wordt weer gegeven in de onderstaande afbeelding:

:::image type="content" source="../media/waf-front-door-configure-custom-response-code/custom-response.png" alt-text="Voor beeld van aangepaste antwoorden":::

"{{Azure-Ref}}" voegt de unieke referentie teken reeks in de hoofd tekst van het antwoord in. De waarde komt overeen met het veld TrackingReference in de `FrontdoorAccessLog` `FrontdoorWebApplicationFirewallLog` Logboeken en.

## <a name="configure-custom-response-status-code-and-message-use-powershell"></a>De status code en het bericht voor de aangepaste reactie configureren met Power shell

### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen

Azure PowerShell voorziet in een set van cmdlets die gebruikmaken van het [Azure Resource Manager](../../azure-resource-manager/management/overview.md)-model om uw Azure-resources te beheren. 

U kunt [Azure PowerShell](/powershell/azure/) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden met uw Azure-referenties en Az PowerShell te installeren.

### <a name="connect-to-azure-with-an-interactive-dialog-for-sign-in"></a>Verbinding maken met Azure met een interactief dialoogvenster voor aanmelden

```
Connect-AzAccount
Install-Module -Name Az

```
Zorg ervoor dat bij u de huidige versie van PowerShellGet is geïnstalleerd. Voer de onderstaande opdracht uit en open PowerShell opnieuw.
```
Install-Module PowerShellGet -Force -AllowClobber
``` 
### <a name="install-azfrontdoor-module"></a>De AzureRM.FrontDoor-module installeren 

```
Install-Module -Name Az.FrontDoor
```

### <a name="create-a-resource-group"></a>Een resourcegroep maken

In Azure kunt u verwante resources toewijzen aan een resourcegroep. Hier maken we een resource groep met behulp van [New-AzResourceGroup](/powershell/module/Az.resources/new-Azresourcegroup).

```azurepowershell-interactive
New-AzResourceGroup -Name myResourceGroupWAF
```

### <a name="create-a-new-waf-policy-with-custom-response"></a>Een nieuw WAF-beleid maken met een aangepast antwoord 

Hieronder ziet u een voor beeld van het maken van een nieuw WAF-beleid met een aangepaste antwoord status code ingesteld op 405 en bericht aan **u wordt geblokkeerd.** met [New-AzFrontDoorWafPolicy](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
# WAF policy setting
New-AzFrontDoorWafPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 405 `
-CustomBlockResponseBody "<html><head><title>You are blocked.</title></head><body></body></html>"
```

Aangepaste antwoord code of instellingen voor de antwoord tekst wijzigen van een bestaand WAF-beleid met behulp van [Update-AzFrontDoorFireWallPolicy](/powershell/module/az.frontdoor/Update-AzFrontDoorWafPolicy).

```azurepowershell
# modify WAF response code
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-EnabledState enabled `
-Mode Detection `
-CustomBlockResponseStatusCode 403
```

```azurepowershell
# modify WAF response body
Update-AzFrontDoorFireWallPolicy `
-Name myWAFPolicy `
-ResourceGroupName myResourceGroupWAF `
-CustomBlockResponseBody "<html><head><title>Forbidden</title></head><body>{{azure-ref}}</body></html>"
```

## <a name="next-steps"></a>Volgende stappen
- Meer informatie over [Web Application Firewall met Azure front-deur](../afds/afds-overview.md)