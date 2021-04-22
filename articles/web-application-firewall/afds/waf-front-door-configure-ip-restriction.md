---
title: WAF-regel voor IP-beperking configureren voor Azure Front Door
description: Leer hoe u een Web Application Firewall configureert om IP-adressen voor een bestaand Azure Front Door beperken.
services: web-application-firewall
author: vhorne
ms.service: web-application-firewall
ms.topic: article
ms.date: 12/22/2020
ms.author: tyao
ms.openlocfilehash: 32bf7a5ecc93fa23c8c704dc346048c26c086121
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107860847"
---
# <a name="configure-an-ip-restriction-rule-with-a-web-application-firewall-for-azure-front-door"></a>Een IP-beperkingsregel configureren met een Web Application Firewall voor Azure Front Door

In dit artikel wordt beschreven hoe u IP-beperkingsregels configureert in een Web Application Firewall (WAF) voor Azure Front Door met behulp van de sjabloon Azure Portal, Azure CLI, Azure PowerShell of een Azure Resource Manager.

Een regel voor toegangsbeheer op basis van IP-adressen is een aangepaste WAF-regel waarmee u de toegang tot uw webtoepassingen kunt controleren. Dit doet u door een lijst met IP-adressen of IP-adresbereiken op te geven in CIDR-indeling (Classless Inter-Domain Routing). Er zijn twee typen overeenkomende variabelen in ip-adresmatch: **RemoteAddr** en **SocketAddr.** RemoteAddr is het oorspronkelijke client-IP-adres dat meestal wordt verzonden via X-Forwarded-For-aanvraagheader. SocketAddr is het bron-IP-adres dat WAF ziet. Als uw gebruiker zich achter een proxy, is SocketAddr vaak het adres van de proxyserver.

Uw webtoepassing is standaard toegankelijk via internet. Als u de toegang tot clients wilt beperken vanuit een lijst met bekende IP-adressen of IP-adresbereiken, kunt u een IP-overeenkomende regel maken die de lijst met IP-adressen bevat als overeenkomende waarden en de operator in stelt op 'Not' (negate is true) en de actie **blokkeren.** Nadat een IP-beperkingsregel is toegepast, ontvangen aanvragen die afkomstig zijn van adressen buiten deze lijst met toegestane adressen het antwoord 403 Verboden.

## <a name="configure-a-waf-policy-with-the-azure-portal"></a>Een WAF-beleid configureren met de Azure Portal

### <a name="prerequisites"></a>Vereisten

Maak een Azure Front Door profiel door de instructies te volgen die worden beschreven in Quickstart: Een Front Door maken voor een globale [webtoepassing met hoge beschikbare toegang.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Een WAF-beleid maken

1. Selecteer op Azure Portal de optie Een **resource maken,** typ **Web Application Firewall** in het zoekvak en selecteer vervolgens Web Application Firewall **(WAF).**
2. Selecteer **Maken**.
3. Gebruik op **de pagina Een WAF-beleid** maken de volgende waarden om het tabblad **Basisinformatie te** voltooien:
   
   |Instelling  |Waarde  |
   |---------|---------|
   |Beleid voor     |Global WAF (Front Door)|
   |Abonnement     |Selecteer uw abonnement|
   |Resourcegroep     |Selecteer de resourcegroep waar uw Front Door zich.|
   |Beleidsnaam     |Typ een naam voor uw beleid|
   |Beleidsstatus     |Ingeschakeld|

   Selecteer **Volgende: Beleidsinstellingen**

1. Selecteer op **het tabblad Beleidsinstellingen** de optie **Preventie.** Typ in **de tekst Antwoord blokkeren** de tekst U bent *geblokkeerd.* zodat u kunt zien dat uw aangepaste regel van kracht is.
2. Selecteer **Volgende: Beheerde regels.**
3. Selecteer **Volgende: Aangepaste regels.**
4. Selecteer **Aangepaste regel toevoegen.**
5. Gebruik op **de pagina Aangepaste regel** toevoegen de volgende testwaarden om een aangepaste regel te maken:

   |Instelling  |Waarde  |
   |---------|---------|
   |Naam van aangepaste regel     |FdWafCustRule|
   |Status     |Ingeschakeld|
   |Regeltype     |Match|
   |Prioriteit    |100|
   |Overeenkomsttype     |IP-adres|
   |Variabele matchen|RemoteAddr|
   |Bewerking|Bevat niet|
   |IP-adres of -bereik|10.10.10.0/24|
   |Dan|Verkeer weigeren|

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/custom-rule.png" alt-text="Aangepaste regel":::

   Selecteer **Toevoegen**.
6. Selecteer **Volgende: Association**.
7. Selecteer **Front-endhost toevoegen.**
8. Selecteer **voor Front-end-host** de front-en-host en selecteer **Toevoegen.**
9. Selecteer **Controleren + maken**.
10. Nadat de beleidsvalidatie is uitgevoerd, selecteert **u Maken.**

### <a name="test-your-waf-policy"></a>Uw WAF-beleid testen

1. Nadat de implementatie van uw WAF-beleid is voltooid, bladert u naar Front Door front-endhostnaam.
2. Als het goed is, ziet u het aangepaste blokbericht.

   :::image type="content" source="../media/waf-front-door-configure-ip-restriction/waf-rule-test.png" alt-text="WAF-regeltest":::

   > [!NOTE]
   > Er is opzettelijk een privé-IP-adres gebruikt in de aangepaste regel om te garanderen dat de regel wordt uitgevoerd. Maak in een daadwerkelijke implementatie regels *voor toestaan* *en weigeren* met behulp van IP-adressen voor uw specifieke situatie.

## <a name="configure-a-waf-policy-with-the-azure-cli"></a>Een WAF-beleid configureren met de Azure CLI

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkingsbeleid, moet u uw CLI-omgeving instellen en een Azure Front Door maken.

#### <a name="set-up-the-azure-cli-environment"></a>De Azure CLI-omgeving instellen
1. Installeer de [Azure CLI](/cli/azure/install-azure-cli)of gebruik Azure Cloud Shell. Azure Cloud Shell is een gratis Bash-shell die u rechtstreeks in Azure Portal kunt uitvoeren. In deze shell is de Azure CLI vooraf geïnstalleerd en geconfigureerd voor gebruik met uw account. Selecteer de **knop** Proberen in de CLI-opdrachten die volgen en meld u vervolgens aan bij uw Azure-account in Cloud Shell sessie die wordt geopend. Nadat de sessie is gestart, voert u in `az extension add --name front-door` om de extensie Azure Front Door toevoegen.
 2. Als u de CLI lokaal in Bash gebruikt, meld u zich dan aan bij Azure met behulp van `az login` .

#### <a name="create-an-azure-front-door-profile"></a>Een Azure Front Door maken
Maak een Azure Front Door profiel door de instructies te volgen die worden beschreven in Quickstart: Een Front Door maken voor een globale [webtoepassing met hoge beschikbare toegang.](../../frontdoor/quickstart-create-front-door.md)

### <a name="create-a-waf-policy"></a>Een WAF-beleid maken

Maak een WAF-beleid met behulp van [de opdracht az network front-door waf-policy create.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_create) Vervang in het volgende voorbeeld de beleidsnaam *IPAllowPolicyExampleCLI* door een unieke beleidsnaam.

```azurecli-interactive 
az network front-door waf-policy create \
  --resource-group <resource-group-name> \
  --subscription <subscription ID> \
  --name IPAllowPolicyExampleCLI
  ```
### <a name="add-a-custom-ip-access-control-rule"></a>Een aangepaste REGEL voor IP-toegangsbeheer toevoegen

Gebruik de [opdracht az network front-door waf-policy custom-rule create](/cli/azure/network/front-door/waf-policy/rule#az_network_front_door_waf_policy_rule_create) om een aangepaste REGEL voor IP-toegangsbeheer toe te voegen voor het WAF-beleid dat u zojuist hebt gemaakt.

In de volgende voorbeelden:
-  Vervang *IPAllowPolicyExampleCLI door* uw unieke beleid dat u eerder hebt gemaakt.
-  Vervang *ip-address-range-1*, *ip-address-range-2* door uw eigen bereik.

Maak eerst een IP-regel voor toestaan voor het beleid dat u in de vorige stap hebt gemaakt. 
> [!NOTE]
> **--uitstellen** is vereist omdat een regel een overeenkomstvoorwaarde moet hebben die in de volgende stap moet worden toegevoegd.

```azurecli
az network front-door waf-policy rule create \
  --name IPAllowListRule \
  --priority 1 \
  --rule-type MatchRule \
  --action Block \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI --defer
```
Voeg vervolgens een voorwaarde voor overeenkomst toe aan de regel:

```azurecli
az network front-door waf-policy rule match-condition add \
--match-variable RemoteAddr \
--operator IPMatch \
--values "ip-address-range-1" "ip-address-range-2" \
--negate true \
--name IPAllowListRule \
  --resource-group <resource-group-name> \
  --policy-name IPAllowPolicyExampleCLI 
  ```
                                                   
### <a name="find-the-id-of-a-waf-policy"></a>De id van een WAF-beleid zoeken 
Zoek de id van een WAF-beleid met behulp van [de opdracht az network front-door waf-policy show.](/cli/azure/network/front-door/waf-policy#az_network_front_door_waf_policy_show) Vervang *IPAllowPolicyExampleCLI* in het volgende voorbeeld door uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door  waf-policy show \
     --resource-group <resource-group-name> \
     --name IPAllowPolicyExampleCLI
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Een WAF-beleid koppelen aan Azure Front Door front-endhost

Stel de Azure Front Door *WebApplicationFirewallPolicyLink-id* in op de beleids-id met behulp van [de opdracht az network front-door update.](/cli/azure/network/front-door#az_network_front_door_update) Vervang *IPAllowPolicyExampleCLI* door uw unieke beleid dat u eerder hebt gemaakt.

   ```azurecli
   az network front-door update \
     --set FrontendEndpoints[0].WebApplicationFirewallPolicyLink.id=/subscriptions/<subscription ID>/resourcegroups/resource-group-name/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/IPAllowPolicyExampleCLI \
     --name <frontdoor-name>
     --resource-group <resource-group-name>
   ```
In dit voorbeeld wordt het WAF-beleid toegepast op **FrontendEndpoints[0]**. U kunt het WAF-beleid koppelen aan een van uw front-ends.
> [!Note]
> U hoeft de eigenschap **WebApplicationFirewallPolicyLink** slechts één keer in te stellen om een WAF-beleid te koppelen aan Azure Front Door front-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.

## <a name="configure-a-waf-policy-with-azure-powershell"></a>Een WAF-beleid configureren met Azure PowerShell

### <a name="prerequisites"></a>Vereisten
Voordat u begint met het configureren van een IP-beperkingsbeleid, moet u uw PowerShell-omgeving instellen en een Azure Front Door maken.

#### <a name="set-up-your-powershell-environment"></a>Uw PowerShell-omgeving instellen
Azure PowerShell biedt een set cmdlets die gebruikmaken van het [Azure Resource Manager](../../azure-resource-manager/management/overview.md) voor het beheren van Azure-resources.

U kunt [Azure PowerShell](/powershell/azure/) op uw lokale computer installeren en in elke PowerShell-sessie gebruiken. Volg de instructies op de pagina om u aan te melden bij PowerShell met uw Azure-referenties en installeer vervolgens de Az-module.

1. Maak verbinding met Azure met behulp van de volgende opdracht en gebruik vervolgens een interactief dialoogvenster om u aan te melden.
    ```
    Connect-AzAccount
    ```
 2. Voordat u een Azure Front Door installeert, moet u ervoor zorgen dat de huidige versie van de PowerShellGet-module is geïnstalleerd. Voer de volgende opdracht uit en open PowerShell opnieuw.

    ```
    Install-Module PowerShellGet -Force -AllowClobber
    ``` 

3. Installeer de Az.FrontDoor-module met behulp van de volgende opdracht. 
    
    ```
    Install-Module -Name Az.FrontDoor
    ```
### <a name="create-an-azure-front-door-profile"></a>Een Azure Front Door maken
Maak een Azure Front Door profiel door de instructies te volgen die worden beschreven in Quickstart: Een Front Door maken voor een globale [webtoepassing met hoge beschikbare toegang.](../../frontdoor/quickstart-create-front-door.md)

### <a name="define-an-ip-match-condition"></a>Een IP-overeenkomstvoorwaarde definiëren
Gebruik de [opdracht New-AzFrontDoorWafMatchConditionObject](/powershell/module/az.frontdoor/new-azfrontdoorwafmatchconditionobject) om een IP-overeenkomstvoorwaarde te definiëren.
Vervang in het volgende voorbeeld *ip-address-range-1*, *ip-address-range-2* door uw eigen bereik.    
```powershell
$IPMatchCondition = New-AzFrontDoorWafMatchConditionObject `
-MatchVariable  RemoteAddr `
-OperatorProperty IPMatch `
-MatchValue "ip-address-range-1", "ip-address-range-2"
-NegateCondition 1
```
     
### <a name="create-a-custom-ip-allow-rule"></a>Een aangepaste IP-regel voor toestaan maken

Gebruik de [opdracht New-AzFrontDoorWafCustomRuleObject](/powershell/module/Az.FrontDoor/New-azfrontdoorwafcustomruleobject) om een actie te definiëren en een prioriteit in te stellen. In het volgende voorbeeld worden aanvragen die niet afkomstig zijn van client-IP's die overeenkomen met de lijst, geblokkeerd.

```azurepowershell
$IPAllowRule = New-AzFrontDoorWafCustomRuleObject `
-Name "IPAllowRule" `
-RuleType MatchRule `
-MatchCondition $IPMatchCondition `
-Action Block -Priority 1
```

### <a name="configure-a-waf-policy"></a>Een WAF-beleid configureren
Zoek de naam van de resourcegroep die het Azure Front Door bevat met behulp van `Get-AzResourceGroup` . Configureer vervolgens een WAF-beleid met de [IP-regel met behulp van New-AzFrontDoorWafPolicy.](/powershell/module/az.frontdoor/new-azfrontdoorwafpolicy)

```azurepowershell
  $IPAllowPolicyExamplePS = New-AzFrontDoorWafPolicy `
    -Name "IPRestrictionExamplePS" `
    -resourceGroupName <resource-group-name> `
    -Customrule $IPAllowRule`
    -Mode Prevention `
    -EnabledState Enabled
   ```

### <a name="link-a-waf-policy-to-an-azure-front-door-front-end-host"></a>Een WAF-beleid koppelen aan Azure Front Door front-endhost

Koppel een WAF-beleidsobject aan een bestaande front-endhost en werk Azure Front Door bij. Haal eerst het Azure Front Door op met behulp [van Get-AzFrontDoor.](/powershell/module/Az.FrontDoor/Get-AzFrontDoor) Stel vervolgens de eigenschap **WebApplicationFirewallPolicyLink** in op de resource-id van *$IPAllowPolicyExamplePS*, die u in de vorige stap hebt gemaakt, met behulp van de [opdracht Set-AzFrontDoor.](/powershell/module/Az.FrontDoor/Set-AzFrontDoor)

```azurepowershell
  $FrontDoorObjectExample = Get-AzFrontDoor `
    -ResourceGroupName <resource-group-name> `
    -Name $frontDoorName
  $FrontDoorObjectExample[0].FrontendEndpoints[0].WebApplicationFirewallPolicyLink = $IPBlockPolicy.Id
  Set-AzFrontDoor -InputObject $FrontDoorObjectExample[0]
```

> [!NOTE]
> In dit voorbeeld wordt het WAF-beleid toegepast op **FrontendEndpoints[0]**. U kunt een WAF-beleid koppelen aan een van uw front-ends. U hoeft de eigenschap **WebApplicationFirewallPolicyLink** slechts eenmaal in te stellen om een WAF-beleid te koppelen aan Azure Front Door front-end. Volgende beleidsupdates worden automatisch toegepast op de front-end.


## <a name="configure-a-waf-policy-with-a-resource-manager-template"></a>Een WAF-beleid configureren met een Resource Manager sjabloon
Als u de sjabloon wilt weergeven die een Azure Front Door en een WAF-beleid met aangepaste IP-beperkingsregels, gaat u [naar GitHub](https://github.com/Azure/azure-quickstart-templates/tree/master/201-front-door-waf-clientip).


## <a name="next-steps"></a>Volgende stappen

- Meer informatie over het [maken van Azure Front Door profiel](../../frontdoor/quickstart-create-front-door.md).
