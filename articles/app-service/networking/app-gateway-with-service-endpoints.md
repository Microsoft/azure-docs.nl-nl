---
title: Application Gateway integratie met Service-eind punten-Azure App Service | Microsoft Docs
description: Hierin wordt beschreven hoe Application Gateway integreert met Azure App Service beveiligd met Service-eind punten.
services: app-service
documentationcenter: ''
author: madsd
manager: ccompy
editor: ''
ms.assetid: 073eb49c-efa1-4760-9f0c-1fecd5c251cc
ms.service: app-service
ms.workload: web
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 12/09/2019
ms.author: madsd
ms.custom: seodec18, devx-track-azurecli
ms.openlocfilehash: 58886a8f7dc505a7e68d69eb00b4a2ebd776dd5a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98209850"
---
# <a name="application-gateway-integration-with-service-endpoints"></a>Integratie met Service-eind punten Application Gateway
Er zijn drie variaties van App Service waarvoor een enigszins verschillende configuratie van de integratie met Azure-toepassing gateway is vereist. De variaties zijn onder andere gewone App Service, ook wel bekend als multi tenant, interne Load Balancer (ILB) App Service Environment (ASE) en externe ASE. In dit artikel wordt uitgelegd hoe u dit kunt configureren met App Service (multi tenant) en overwegingen over ILB en externe ASE kunt bespreken.

## <a name="integration-with-app-service-multi-tenant"></a>Integratie met App Service (multi tenant)
App Service (multi tenant) heeft een openbaar op Internet gericht eind punt. Met behulp van [service-eind punten](../../virtual-network/virtual-network-service-endpoints-overview.md) kunt u alleen verkeer toestaan van een specifiek subnet binnen een Azure-Virtual Network en alle andere gegevens blok keren. In het volgende scenario gebruiken we deze functie om ervoor te zorgen dat een App Service-exemplaar alleen verkeer van een specifiek Application Gateway exemplaar kan ontvangen.

:::image type="content" source="./media/app-gateway-with-service-endpoints/service-endpoints-appgw.png" alt-text="Diagram toont het Internet dat naar een Application Gateway in een Azure-Virtual Network wordt gestroomd en via een Firewall pictogram naar instanties van apps in App Service loopt.":::

Er zijn twee delen voor deze configuratie naast het maken van de App Service en de Application Gateway. Het eerste deel is het inschakelen van service-eind punten in het subnet van de Virtual Network waar de Application Gateway wordt geïmplementeerd. Service-eind punten zorgen ervoor dat alle netwerk verkeer dat het subnet naar het App Service verlaat, wordt gelabeld met de specifieke subnet-ID. Het tweede deel is het instellen van een toegangs beperking van de specifieke Web-app om ervoor te zorgen dat alleen verkeer dat is gelabeld met deze specifieke subnet-ID is toegestaan. U kunt deze configureren met behulp van verschillende hulpprogram ma's, afhankelijk van de voor keur.

## <a name="using-azure-portal"></a>Azure Portal gebruiken
Met Azure Portal voert u vier stappen uit om de installatie in te richten en te configureren. Als u bestaande resources hebt, kunt u de eerste stappen overs Laan.
1. Maak een App Service met behulp van een van de Quick starts in de App Service Documentatie, bijvoorbeeld [Snelstartgids voor .net core](../quickstart-dotnetcore.md)
2. Maak een Application Gateway met behulp van de Snelstartgids van de [Portal](../../application-gateway/quick-create-portal.md), maar sla de sectie backend-doelen toevoegen over.
3. Configureer [app service als een back-end in Application Gateway](../../application-gateway/configure-web-app-portal.md), maar sla de sectie toegang beperken niet over.
4. Maak tot slot de [toegangs beperking met behulp van service-eind punten](../../app-service/app-service-ip-restrictions.md#set-a-service-endpoint-based-rule).

U hebt nu toegang tot de App Service via Application Gateway, maar als u de App Service rechtstreeks probeert te openen, ontvangt u een 403 HTTP-fout melding die aangeeft dat de website is gestopt.

![Scherm afbeelding toont de tekst van de fout 403-verboden.](./media/app-gateway-with-service-endpoints/website-403-forbidden.png)

## <a name="using-azure-resource-manager-template"></a>Azure Resource Manager-sjabloon gebruiken
In de [Resource Manager-implementatie sjabloon][template-app-gateway-app-service-complete] wordt een volledig scenario ingericht. Het scenario bestaat uit een App Service exemplaar dat is vergrendeld met Service-eind punten en toegangs beperking om alleen verkeer van Application Gateway te ontvangen. De sjabloon bevat veel slimme standaard waarden en unieke postfixes die zijn toegevoegd aan de resource namen om eenvoudig te zijn. Als u deze wilt overschrijven, moet u de opslag plaats klonen of de sjabloon downloaden en bewerken. 

Als u de sjabloon wilt Toep assen, kunt u de knop implementeren in azure vinden in de beschrijving van de sjabloon, of u kunt de juiste Power shell/CLI gebruiken.

## <a name="using-azure-command-line-interface"></a>De Azure-opdracht regel interface gebruiken
Het [Azure cli](../../app-service/scripts/cli-integrate-app-service-with-application-gateway.md) -voor beeld bevat een app service die is vergrendeld met Service-eind punten en toegangs beperking om alleen verkeer van Application Gateway te ontvangen. Als u alleen verkeer naar een bestaand App Service van een bestaande Application Gateway wilt isoleren, is de volgende opdracht voldoende.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --rule-name AppGwSubnet --priority 200 --subnet mySubNetName --vnet-name myVnetName
```

In de standaard configuratie zorgt de opdracht voor beide installatie van de service-eindpunt configuratie in het subnet en de toegangs beperking in het App Service.

## <a name="considerations-for-ilb-ase"></a>Aandachtspunten voor ILB ASE
ILB ASE wordt niet blootgesteld aan Internet en verkeer tussen het exemplaar en een Application Gateway is daarom al geïsoleerd voor de Virtual Network. In de volgende [hand leiding](../environment/integrate-with-application-gateway.md) wordt een ILB-ASE geconfigureerd en geïntegreerd met een Application Gateway met behulp van Azure Portal. 

Als u ervoor wilt zorgen dat alleen verkeer van het Application Gateway subnet de ASE bereikt, kunt u een netwerk beveiligings groep (NSG) configureren die van invloed is op alle web-apps in de ASE. Voor de NSG kunt u het IP-adres bereik van het subnet opgeven en optioneel de poorten (80/443). Zorg ervoor dat u de [vereiste NSG-regels](../environment/network-info.md#network-security-groups) voor ASE niet overschrijft om correct te werken.

Als u verkeer naar een afzonderlijke web-app wilt isoleren, moet u toegangs beperkingen op basis van IP gebruiken omdat service-eind punten niet werken voor ASE. Het IP-adres moet de privé-IP van het Application Gateway-exemplaar zijn.

## <a name="considerations-for-external-ase"></a>Overwegingen voor externe ASE
Externe ASE heeft een openbaar gerichte load balancer, zoals multi tenant App Service. Service-eind punten werken niet voor ASE en daarom moet u toegangs beperkingen op basis van IP gebruiken met behulp van het open bare IP-adres van het Application Gateway-exemplaar. Als u een externe ASE wilt maken met behulp van de Azure Portal, kunt u deze [Snelstartgids](../environment/create-external-ase.md) volgen

[template-app-gateway-app-service-complete]: https://github.com/Azure/azure-quickstart-templates/tree/master/201-web-app-with-app-gateway-v2/ "Azure Resource Manager sjabloon voor het volledige scenario"

## <a name="considerations-for-kuduscm-site"></a>Overwegingen voor kudu/SCM-site
De SCM-site, ook wel kudu genoemd, is een beheer site, die bestaat voor elke web-app. Het is niet mogelijk om de service van de SCM-site terug te draaien en u wilt deze waarschijnlijk ook op afzonderlijke IP-adressen of een specifiek subnet vergren delen.

Als u dezelfde toegangs beperkingen wilt gebruiken als voor de hoofd site, kunt u de instellingen overnemen met de volgende opdracht.

```azurecli-interactive
az webapp config access-restriction set --resource-group myRG --name myWebApp --use-same-restrictions-for-scm-site
```

Als u afzonderlijke toegangs beperkingen wilt instellen voor de SCM-site, kunt u toegangs beperkingen toevoegen met behulp van de--SCM-site vlag zoals hieronder wordt weer gegeven.

```azurecli-interactive
az webapp config access-restriction add --resource-group myRG --name myWebApp --scm-site --rule-name KudoAccess --priority 200 --ip-address 208.130.0.0/16
```

## <a name="next-steps"></a>Volgende stappen
Zie [app service Environment-documentatie](/azure/app-service/environment)voor meer informatie over de app service environment.

Als u uw web-app verder wilt beveiligen, kunt u informatie over Web Application firewall op Application Gateway vinden in de [firewall documentatie van Azure Web Application](../../web-application-firewall/ag/ag-overview.md).
