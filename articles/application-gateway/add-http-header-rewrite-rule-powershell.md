---
title: HTTP-headers in Azure-toepassing Gateway opnieuw schrijven
description: Dit artikel bevat informatie over het herschrijven van HTTP-headers in Azure-toepassing gateway met behulp van Azure PowerShell
services: application-gateway
author: abshamsft
ms.service: application-gateway
ms.topic: how-to
ms.date: 04/12/2019
ms.author: absha
ms.openlocfilehash: 29ca3aff7d75c7a14bf7b325719924936762d191
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101711685"
---
# <a name="rewrite-http-request-and-response-headers-with-azure-application-gateway---azure-powershell"></a>HTTP-aanvraag-en-antwoord headers herschrijven met Azure-toepassing gateway-Azure PowerShell

In dit artikel wordt beschreven hoe u Azure PowerShell kunt gebruiken om een SKU-exemplaar van [Application Gateway v2](./application-gateway-autoscaling-zone-redundant.md) te configureren om de HTTP-headers in aanvragen en antwoorden te herschrijven.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="before-you-begin"></a>Voordat u begint

- U moet Azure PowerShell lokaal uitvoeren om de stappen in dit artikel uit te voeren. U moet ook AZ module version 1.0.0 of later hebben geïnstalleerd. Voer uit `Import-Module Az` en controleer vervolgens `Get-Module Az` de versie die u hebt geïnstalleerd. Als u PowerShell wilt upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-az-ps). Nadat u de versie van PowerShell hebt gecontroleerd, voert u `Login-AzAccount` uit om een verbinding op te zetten met Azure.
- U moet een SKU-exemplaar van Application Gateway v2 hebben. Het opnieuw schrijven van headers wordt niet ondersteund in de V1-SKU. Als u de v2-SKU niet hebt, maakt u een [Application Gateway v2 SKU](./tutorial-autoscale-ps.md) -exemplaar voordat u begint.

## <a name="create-required-objects"></a>Vereiste objecten maken

Als u het opnieuw schrijven van HTTP-headers wilt configureren, moet u deze stappen volt ooien.

1. De objecten maken die vereist zijn voor het herschrijven van HTTP-headers:

   - **RequestHeaderConfiguration**: Hiermee geeft u de velden van de aanvraag header op die u wilt herschrijven en de nieuwe waarde voor de kopteksten.

   - **ResponseHeaderConfiguration**: Hiermee geeft u de velden van de antwoord header op die u wilt herschrijven en de nieuwe waarde voor de kopteksten.

   - **Actieset**: bevat de configuraties van de aanvraag en de antwoord headers die eerder zijn opgegeven.

   - **Voor waarde**: een optionele configuratie. Bij herschrijf voorwaarden wordt de inhoud van HTTP (S)-aanvragen en-antwoorden geëvalueerd. De actie herschrijven vindt plaats als de HTTP (S)-aanvraag of het antwoord overeenkomt met de voor waarde voor herschrijven.

     Als u meer dan één voor waarde aan een actie koppelt, treedt de actie alleen op wanneer aan alle voor waarden wordt voldaan. Met andere woorden, de bewerking is een logische en-bewerking.

   - **RewriteRule**: bevat meerdere combi Naties van Herschrijf acties/voor waarden voor herschrijven.

   - **RuleSequence**: een optionele configuratie die helpt bij het bepalen van de volg orde waarin regels voor herschrijven worden uitgevoerd. Deze configuratie is handig wanneer u meerdere regels voor herschrijven hebt opgegeven in een herschrijfset. Een regel voor herschrijven met een lagere regel reeks waarde wordt eerst uitgevoerd. Als u dezelfde regel reeks waarde toewijst aan twee regels voor herschrijven, is de volg orde van uitvoering niet-deterministisch.

     Als u de RuleSequence niet expliciet opgeeft, wordt de standaard waarde 100 ingesteld.

   - **RewriteRuleSet**: bevat meerdere herschrijf regels die worden gekoppeld aan een regel voor het door sturen van aanvragen.

2. Koppel de RewriteRuleSet aan een routerings regel. De herschrijf configuratie wordt gekoppeld aan de bron-listener via de routerings regel. Wanneer u een basis regel voor door sturen gebruikt, wordt de configuratie voor het opnieuw schrijven van kopteksten gekoppeld aan een bronhost en is het herschrijven van de globale header. Wanneer u een op pad gebaseerde routerings regel gebruikt, wordt de configuratie voor het herschrijven van kopteksten gedefinieerd op de URL-pad toewijzing. In dat geval geldt dit alleen voor het specifieke pad van een site.

U kunt meerdere herschrijf sets voor HTTP-headers maken en elke herschrijfset Toep assen op meerdere listeners. Maar u kunt slechts één herschrijfset Toep assen op een specifieke listener.

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

```azurepowershell
Connect-AzAccount
Select-AzSubscription -Subscription "<sub name>"
```

## <a name="specify-the-http-header-rewrite-rule-configuration"></a>De configuratie van de regel voor het opnieuw schrijven van HTTP-headers opgeven

In dit voor beeld wijzigen we een omleidings-URL door de locatie header te herschrijven in het HTTP-antwoord wanneer de locatie header een verwijzing naar azurewebsites.net bevat. Om dit te doen, voegen we een voor waarde toe om te evalueren of de locatie header in het antwoord azurewebsites.net bevat. We gebruiken het patroon `(https?)://.*azurewebsites.net(.*)$` . En we gebruiken `{http_resp_Location_1}://contoso.com{http_resp_Location_2}` als waarde voor de koptekst. Met deze waarde wordt *azurewebsites.net* vervangen door *contoso.com* in de locatie header.

```azurepowershell
$responseHeaderConfiguration = New-AzApplicationGatewayRewriteRuleHeaderConfiguration -HeaderName "Location" -HeaderValue "{http_resp_Location_1}://contoso.com{http_resp_Location_2}"
$actionSet = New-AzApplicationGatewayRewriteRuleActionSet -RequestHeaderConfiguration $requestHeaderConfiguration -ResponseHeaderConfiguration $responseHeaderConfiguration
$condition = New-AzApplicationGatewayRewriteRuleCondition -Variable "http_resp_Location" -Pattern "(https?):\/\/.*azurewebsites\.net(.*)$" -IgnoreCase
$rewriteRule = New-AzApplicationGatewayRewriteRule -Name LocationHeader -ActionSet $actionSet
$rewriteRuleSet = New-AzApplicationGatewayRewriteRuleSet -Name LocationHeaderRewrite -RewriteRule $rewriteRule
```

## <a name="retrieve-the-configuration-of-your-application-gateway"></a>De configuratie van uw toepassings gateway ophalen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
```

## <a name="retrieve-the-configuration-of-your-request-routing-rule"></a>De configuratie van uw regel voor aanvraag routering ophalen

```azurepowershell
$reqRoutingRule = Get-AzApplicationGatewayRequestRoutingRule -Name rule1 -ApplicationGateway $appgw
```

## <a name="update-the-application-gateway-with-the-configuration-for-rewriting-http-headers"></a>De toepassings gateway bijwerken met de configuratie voor het herschrijven van HTTP-headers

```azurepowershell
Add-AzApplicationGatewayRewriteRuleSet -ApplicationGateway $appgw -Name LocationHeaderRewrite -RewriteRule $rewriteRuleSet.RewriteRules
Set-AzApplicationGatewayRequestRoutingRule -ApplicationGateway $appgw -Name rule1 -RuleType $reqRoutingRule.RuleType -BackendHttpSettingsId $reqRoutingRule.BackendHttpSettings.Id -HttpListenerId $reqRoutingRule.HttpListener.Id -BackendAddressPoolId $reqRoutingRule.BackendAddressPool.Id -RewriteRuleSetId $rewriteRuleSet.Id
Set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="delete-a-rewrite-rule"></a>Een regel voor herschrijven verwijderen

```azurepowershell
$appgw = Get-AzApplicationGateway -Name "AutoscalingAppGw" -ResourceGroupName "<rg name>"
Remove-AzApplicationGatewayRewriteRuleSet -Name "LocationHeaderRewrite" -ApplicationGateway $appgw
$requestroutingrule= Get-AzApplicationGatewayRequestRoutingRule -Name "rule1" -ApplicationGateway $appgw
$requestroutingrule.RewriteRuleSet= $null
set-AzApplicationGateway -ApplicationGateway $appgw
```

## <a name="next-steps"></a>Volgende stappen

Zie [algemene scenario's voor het herschrijven van kopteksten](./rewrite-http-headers.md)voor meer informatie over het instellen van veelvoorkomende use cases.