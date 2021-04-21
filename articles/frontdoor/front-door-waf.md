---
title: 'Zelfstudie: Een web-app schalen en beveiligen met Azure Front Door en Azure WAF (Web Application Firewall)'
description: In deze zelfstudie wordt uitgelegd hoe u Azure Web Application Firewall gebruikt met de Azure Front Door-service
services: frontdoor
documentationcenter: ''
author: duongau
ms.service: frontdoor
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/01/2020
ms.author: duau
ms.openlocfilehash: d315fa5b588c6e5f2e4643ca18626e400e6ca01b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107785645"
---
# <a name="tutorial-quickly-scale-and-protect-a-web-application-by-using-azure-front-door-and-azure-web-application-firewall-waf"></a>Zelfstudie: een webtoepassing snel schalen en beveiligen met Azure Front Door en Azure Web Application Firewall (WAF)

Veel webtoepassingen hebben in de afgelopen weken een snelle toename van verkeer ervaren door COVID-19. Bovendien zien deze webtoepassingen ook een toename in schadelijk verkeer, waaronder Denial of Service-aanvallen. Er is echter een efficiënte manier om uw toepassing uit te schalen bij verkeerspieken en te beschermen tegen aanvallen: configureer Azure Front Door met Azure WAF als een laag voor versnelling, caching en beveiliging vóór uw web-app. In dit artikel wordt uitgelegd hoe u Azure Front Door samen met Azure WAF kunt configureren voor elke web-app die binnen of buiten Azure wordt uitgevoerd. 

In deze zelfstudie gebruiken we Azure CLI om WAF te configureren. U kunt echter hetzelfde bereiken met behulp van de Azure-portal, Azure PowerShell, Azure Resource Manager of de Azure REST-API's. 

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - Een Front Door maken.
> - Een Azure WAF-beleid maken.
> - Regelsets configureren voor een WAF-beleid.
> - Een WAF-beleid koppelen aan Front Door.
> - Een aangepast domein configureren.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Vereisten

- In de instructies in deze zelfstudie wordt de Azure CLI gebruikt. [Raadpleeg deze handleiding](/cli/azure/get-started-with-azure-cli) om aan de slag te gaan met de Azure CLI.

  > [!TIP] 
  > Een eenvoudige en snelle manier om aan de slag te gaan met de Azure CLI is met [Bash in Azure Cloud Shell](../cloud-shell/quickstart.md).

- Zorg ervoor dat de extensie `front-door` is toegevoegd aan de Azure CLI:

   ```azurecli-interactive 
   az extension add --name front-door
   ```

> [!NOTE] 
> Raadpleeg de [Azure CLI-naslag voor front-door](/cli/azure/ext/front-door) voor meer informatie over de opdrachten die in deze zelfstudie worden gebruikt.

## <a name="create-an-azure-front-door-resource"></a>Een Azure Front Door-resource maken

```azurecli-interactive 
az network front-door create --backend-address <>  --accepted-protocols <> --name <> --resource-group <>
```

`--backend-address`: De FQDN (Fully Qualified Domain Name) van de toepassing die u wilt beschermen. Bijvoorbeeld `myapplication.contoso.com`.

`--accepted-protocols`: De protocollen die Azure Front Door moet ondersteunen voor uw webtoepassing. Bijvoorbeeld `--accepted-protocols Http Https`.

`--name`: De naam van uw Azure Front Door-resource.

`--resource-group`: De resourcegroep waarin u deze Azure Front Door-resource wilt plaatsen. Zie [Resourcegroepen beheren in Azure](../azure-resource-manager/management/manage-resource-groups-portal.md) voor meer informatie over resourcegroepen.

In de respons die u krijgt wanneer u deze opdracht uitvoert, zoekt u naar de sleutel `hostName`. U hebt deze waarde in een latere stap nodig. De `hostName` is de DNS-naam van de Azure Front Door-resource die u hebt gemaakt.

## <a name="create-an-azure-waf-profile-to-use-with-azure-front-door-resources"></a>Een Azure WAF-profiel maken voor gebruik met Azure Front Door-resources

```azurecli-interactive 
az network front-door waf-policy create --name <>  --resource-group <>  --disabled false --mode Prevention
```

`--name`: De naam van het nieuwe Azure WAF-beleid.

`--resource-group`: De resourcegroep waarin u deze WAF-resource wilt plaatsen. 

Met de bovenstaande CLI-code maakt u een WAF-beleid dat is ingeschakeld en dat zich in de preventiemodus bevindt. 

> [!NOTE] 
> Mogelijk wilt u het WAF-beleid in de detectiemodus maken en kijken hoe schadelijke aanvragen worden gedetecteerd en geregistreerd (zonder deze te blokkeren) voordat u besluit de beveiligingsmodus te gebruiken.

In de respons die u krijgt wanneer u deze opdracht uitvoert, zoekt u naar de sleutel `ID`. U hebt deze waarde in een latere stap nodig. 

Het veld `ID` moet de volgende indeling hebben:

/abonnementen/**abonnement-id**/resourcegroepen/**resourcegroepnaam**/providers/Microsoft.Network/frontdoorwebapplicationfirewallpolicies/**WAF-beleidsnaam**

## <a name="add-managed-rule-sets-to-the-waf-policy"></a>Beheerde regelsets toevoegen aan dit WAF-beleid

U kunt beheerde regelsets toevoegen aan een WAF-beleid. Een beheerde regelset is een set regels die door Microsoft zijn samengesteld en beheerd waarmee u zich kunt beschermen tegen een bepaalde klasse van bedreigingen. In dit voorbeeld voegen we twee regelsets toe:
- De standaardregelset, waarmee u zich kunt beveiligen tegen veelvoorkomende webbedreigingen. 
- De regelset Bot Manager, waarmee u zich kunt beveiligen tegen schadelijke bots.

De standaardregelset toevoegen:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type DefaultRuleSet --version 1.0
   ```

De regelset Bot Manager toevoegen:

   ```azurecli-interactive 
   az network front-door waf-policy managed-rules add --policy-name <> --resource-group <> --type Microsoft_BotManagerRuleSet --version 1.0
   ```

`--policy-name`: De naam die u hebt opgegeven voor uw Azure WAF-resource.

`--resource-group`: De resourcegroep waarin u de WAF-resource hebt geplaatst.

## <a name="associate-the-waf-policy-with-the-azure-front-door-resource"></a>Het WAF-beleid koppelen aan de Azure Front Door-resource

In deze stap koppelen we het WAF-beleid dat we hebben gemaakt aan de Azure Front Door-resource die zich vóór uw webtoepassing bevindt:

```azurecli-interactive 
az network front-door update --name <> --resource-group <> --set frontendEndpoints[0].webApplicationFirewallPolicyLink='{"id":"<>"}'
```

`--name`: De naam die u hebt opgegeven voor uw Azure Front Door-resource.

`--resource-group`: De resourcegroep waarin u de Azure Front Door-resource hebt geplaatst.

`--set`: Hier werkt u het kenmerk `WebApplicationFirewallPolicyLink` bij voor het `frontendEndpoint` dat is gekoppeld aan uw Azure Front Door-resource met het nieuwe WAF-beleid. U moet de id van het WAF-beleid gebruiken uit de respons die u hebt gekregen toen u het WAF-profiel eerder in deze zelfstudie hebt gemaakt.

 > [!NOTE] 
> Het bovenstaande voorbeeld is van toepassing wanneer u geen aangepast domein gebruikt. Als u geen aangepaste domeinen gebruikt voor toegang tot uw webtoepassingen, kunt u de volgende sectie overslaan. In dat geval geeft u uw klanten de `hostName` die u hebt verkregen tijdens het maken van de Azure Front Door-resource. Ze gebruiken deze `hostName` om naar uw webtoepassing te gaan.

## <a name="configure-the-custom-domain-for-your-web-application"></a>Aangepast domein configureren voor uw webtoepassing

De naam van het aangepaste domein van uw webtoepassing is de naam die klanten gebruiken om naar uw toepassing te verwijzen, bijvoorbeeld www.contoso.com. In eerste instantie was de naam van dit aangepaste domein gekoppeld aan de locatie waar het domein zich bevond voordat u Azure Front Door introduceerde. Nadat u Azure Front Door en WAF hebt toegevoegd om de toepassing te beveiligen, moet de DNS-vermelding die overeenkomt met dat aangepaste domein verwijzen naar de Azure Front Door-resource. U kunt deze wijziging aanbrengen door de vermelding in uw DNS-server te koppelen aan de `hostName` van Azure Front Door die u hebt genoteerd tijdens het maken van de Azure Front Door-resource.

De specifieke stappen voor het bijwerken van uw DNS-records zijn afhankelijk van uw DNS-serviceprovider. Als u Azure DNS gebruikt voor het hosten van uw DNS-naam, raadpleegt u de informatie over [het bijwerken van een DNS-record](../dns/dns-operations-recordsets-cli.md) en verwijst u naar de `hostName` van Azure Front Door. 

Er is iets belangrijks dat u moet weten als uw klanten via de zone-apex (zoals contoso.com) toegang moeten krijgen tot uw website. In dit geval moet u Azure DNS en het bijbehorende [aliasrecordtype](../dns/dns-alias.md) gebruiken om uw DNS-naam te hosten. 

Bovendien moet u de configuratie van Azure Front Door bijwerken door [het aangepaste domein toe te voegen](./front-door-custom-domain.md), zodat Front Door op de hoogte is van de toewijzing.

Ten slotte, als u een aangepast domein gebruikt om uw webtoepassing te bereiken, en het HTTPS-protocol wilt inschakelen. U moet [de certificaten voor uw aangepast domein instellen in Azure Front Door](./front-door-custom-domain-https.md). 

## <a name="lock-down-your-web-application"></a>Uw webtoepassing vergrendelen

We adviseren u ervoor te zorgen dat alleen apparaten in de periferie van Azure Front Door kunnen communiceren met uw webtoepassing. U weet dan zeker dat niemand de beveiliging van Azure Front Door kan omzeilen en rechtstreeks toegang heeft tot uw toepassing. Als u deze vergrendeling wilt instellen, raadpleegt u [How do I lock down the access to my backend to only Azure Front Door?](./front-door-faq.yml#how-do-i-lock-down-the-access-to-my-backend-to-only-azure-front-door-) (Hoe kan ik de toegang tot mijn back-end beperken tot alleen Azure Front Door?).

## <a name="clean-up-resources"></a>Resources opschonen

Wanneer u de resources in deze zelfstudie niet langer nodig hebt, gebruikt u de opdracht [az group delete](/cli/azure/group#az_group_delete) om de resourcegroep, Front Door en het WAF-beleid te verwijderen:

```azurecli-interactive
  az group delete \
    --name <>
```
`--name`: De naam van de resourcegroep voor alle resources die in deze zelfstudie worden gebruikt.

## <a name="next-steps"></a>Volgende stappen

Voor meer informatie over het oplossen van problemen met Front Door, raadpleegt u deze handleidingen voor het oplossen van problemen:

> [!div class="nextstepaction"]
> [Algemene routeringsproblemen oplossen](front-door-troubleshoot-routing.md)