---
title: 'Zelfstudie: Routefilters voor Microsoft-peering configureren - Azure CLI'
description: In deze zelfstudie wordt beschreven hoe u routefilters voor Microsoft-peering configureert met behulp van Azure CLI.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/08/2020
ms.author: duau
ms.custom: devx-track-azurecli
ms.openlocfilehash: ac7fc5af21f11699331d41a074e88ae757170664
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975991"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-azure-cli"></a>Zelfstudie: Routefilters voor Microsoft-peering configureren: Azure CLI

> [!div class="op_single_selector"]
> * [Azure Portal](how-to-routefilter-portal.md)
> * [Azure PowerShell](how-to-routefilter-powershell.md)
> * [Azure-CLI](how-to-routefilter-cli.md)
> 

Met routefilters kunt u een subset van ondersteunde services gebruiken via Microsoft-peering. De stappen in dit artikel helpen u de routefilters voor ExpressRoute-circuits te configureren en te beheren.

Microsoft 365-services zoals Exchange Online, SharePoint Online en Skype voor Bedrijven zijn toegankelijk via Microsoft-peering. Wanneer Microsoft-peering wordt geconfigureerd in een ExpressRoute-circuit, worden alle voorvoegsels die betrekking hebben op deze services, geadverteerd via de BGP-sessies die tot stand zijn gebracht. Er wordt aan elk voorvoegsel een BGP-communitywaarde gekoppeld om de service te identificeren die via het voorvoegsel wordt aangeboden. Zie [BGP-community's](expressroute-routing.md#bgp) voor een lijst met de BGP-communitywaarden en de services waaraan ze zijn gekoppeld.

Dankzij de connectiviteit met alle Azure- en Microsoft 365-services wordt een groot aantal voorvoegsels geadverteerd via BGP. Door het grote aantal voorvoegsels neemt de grootte van de routeringstabellen die worden beheerd via routers in uw netwerk, aanzienlijk toe. Als u slechts een subset van services die worden aangeboden via Microsoft-peering wilt gebruiken, kunt u de grootte van de routeringstabellen op twee manieren verkleinen. U kunt:

* Ongewenste voorvoegsels uitfilteren door routefilters toe te passen op BGP-community's. Het toepassen van routefilters is een gangbare werkwijze en wordt gebruikt in veel netwerken.

* Definieer routefilters en pas deze toe op uw ExpressRoute-circuit. Een routefilter is een nieuwe resource waarmee u de lijst met services kunt selecteren die u wilt gebruiken via Microsoft-peering. ExpressRoute-routers verzenden alleen de lijst met voorvoegsels die horen bij de services die zijn gedefinieerd in het routefilter.

In deze zelfstudie leert u het volgende:
> [!div class="checklist"]
> - BGP-communitywaarden ophalen.
> - Een routefilter en een filterregel maken.
> - Het routefilter koppelen aan een ExpressRoute-circuit.

### <a name="about-route-filters"></a><a name="about"></a>Informatie over routefilters

Wanneer Micosoft-peering wordt geconfigureerd voor uw ExpressRoute-circuit, zetten de Microsoft Edge-routers twee BGP-sessies op met de Edge-routers via uw connectiviteitsprovider. Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met alle toegestane BGP-communitywaarden. Zodra een routefilter is gedefinieerd en gekoppeld aan een ExpressRoute-circuit, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden, geadverteerd naar uw netwerk.

Als u routefilters aan Microsoft 365-services wilt koppelen, moet u zijn gemachtigd om Microsoft 365-services te gebruiken via ExpressRoute. Als u niet bent gemachtigd om Microsoft 365-services te gebruiken via ExpressRoute, mislukt de bewerking voor het koppelen van routefilters. Zie [Azure ExpressRoute voor Microsoft 365](/microsoft-365/enterprise/azure-expressroute) voor meer informatie over het autorisatieproces.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld.
> 

## <a name="prerequisites"></a>Vereisten

Als u via Microsoft-peering verbinding met services wilt maken, moet u de volgende configuratiestappen uitvoeren:

* U moet een actief ExpressRoute-circuit hebben waarvoor Microsoft-peering is ingericht. U kunt de volgende instructies gebruiken om deze taken uit te voeren:
  * [Maak een ExpressRoute-circuit](howto-circuit-cli.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet zijn ingericht en ingeschakeld.
  * [Maak Microsoft-peering](howto-routing-cli.md) als u de BGP-sessie rechtstreeks beheert. Of zorg ervoor dat uw connectiviteitsprovider Microsoft-peering voor uw circuit inricht.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)] 

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u voor deze quickstart versie 2.0.28 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).

### <a name="sign-in-to-your-azure-account-and-select-your-subscription"></a>Aanmelden bij uw Azure-account en uw abonnement selecteren

Meld u aan bij uw Azure-account om de configuratie te starten. Als u de probeerfunctie gebruikt, wordt u automatisch aangemeld en kunt u het aanmelden overslaan. Gebruik de volgende voorbeelden als hulp bij het maken van verbinding:

```azurecli
az login
```

Controleer de abonnementen voor het account.

```azurecli-interactive
az account list
```

Selecteer het abonnement waarvoor u een ExpressRoute-circuit wilt maken.

```azurecli-interactive
az account set --subscription "<subscription ID>"
```

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Een lijst met voorvoegsels en BGP-communitywaarden ophalen

1. Gebruik de volgende cmdlet om de lijst met BGP-communitywaarden en voorvoegsels op te halen die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering:

    ```azurecli-interactive
    az network route-filter rule list-service-communities
    ```

1. Maak een lijst met de BGP-communitywaarden die u wilt gebruiken in het routefilter.

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Een routefilter en een filterregel maken

Een routefilter kan slechts één regel bevatten, en de regel moet van het type Toestaan zijn. Aan deze regel kan een lijst met BGP-communitywaarden zijn gekoppeld. Met de opdracht `az network route-filter create` wordt alleen een resource voor een routefilter gemaakt. Nadat u de resource hebt gemaakt, moet u een regel maken en deze koppelen aan het routefilterobject.

1. Voer de volgende opdracht uit om een resource voor een routefilter te maken:

    ```azurecli-interactive
    az network route-filter create -n MyRouteFilter -g MyResourceGroup
    ```

1. Voer de volgende opdracht uit om een routefilterregel te maken:
 
    ```azurecli-interactive
    az network route-filter rule create --filter-name MyRouteFilter -n CRM --communities 12076:5040 --access Allow -g MyResourceGroup
    ```

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Het routefilter koppelen aan een ExpressRoute-circuit

Voer de volgende opdracht uit om het routefilter aan het ExpressRoute-circuit te koppelen:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --route-filter MyRouteFilter
```

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een routefilter ophalen

Gebruik de volgende opdracht om de eigenschappen van een routefilter op te halen:

```azurecli-interactive
az network route-filter show -g ExpressRouteResourceGroupName --name MyRouteFilter 
```

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

Als het routefilter al is gekoppeld aan een circuit, worden wijzigingen in de BGP-communitylijst automatisch doorgegeven via de BGP-sessie die tot stand is gebracht. U kunt de BGP-communitylijst van uw routefilter bijwerken met behulp van de volgende opdracht:

```azurecli-interactive
az network route-filter rule update --filter-name MyRouteFilter -n CRM -g ExpressRouteResourceGroupName --add communities '12076:5040' --add communities '12076:5010'
```

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een routefilter loskoppelen van een ExpressRoute-circuit

Zodra een routefilter is losgekoppeld van het ExpressRoute-circuit, worden er geen voorvoegsels geadverteerd via de BGP-sessie. U kunt een routefilter loskoppelen van een ExpressRoute-circuit met behulp van de volgende opdracht:

```azurecli-interactive
az network express-route peering update --circuit-name MyCircuit -g ExpressRouteResourceGroupName --name MicrosoftPeering --remove routeFilter
```

## <a name="clean-up-resources"></a>Resources opschonen

U kunt een routefilter alleen verwijderen als het niet aan een circuit is gekoppeld. Zorg ervoor dat het routefilter niet is gekoppeld aan een circuit voordat u het probeert te verwijderen. U kunt een routefilter verwijderen met de volgende opdracht:

```azurecli-interactive
az network route-filter delete -n MyRouteFilter -g MyResourceGroup
```

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over voorbeelden van routerconfiguratie:

> [!div class="nextstepaction"]
> [Voorbeelden van routerconfiguraties voor het instellen en beheren van routering](expressroute-config-samples-routing.md)
