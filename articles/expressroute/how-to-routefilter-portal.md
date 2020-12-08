---
title: 'Zelfstudie: Routefilters voor Microsoft-peering configureren - Azure-portal'
description: In deze zelfstudie wordt beschreven hoe u routefilters voor Microsoft-peering configureert met behulp van de Azure-portal.
services: expressroute
author: duongau
ms.service: expressroute
ms.topic: tutorial
ms.date: 10/15/2020
ms.author: duau
ms.custom: seodec18
ms.openlocfilehash: d142028d197f9e279b5f1e05757946dc40d2c153
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92109133"
---
# <a name="tutorial-configure-route-filters-for-microsoft-peering-using-the-azure-portal"></a>Zelfstudie: Routefilters voor Microsoft-peering configureren met behulp van de Azure-portal

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

Wanneer Microsoft-peering wordt geconfigureerd voor uw ExpressRoute-circuit, worden met de Microsoft Edge-routers twee BGP-sessies tot stand gebracht met de Edge-routers via uw connectiviteitsprovider. Er worden geen routes geadverteerd naar uw netwerk. Als u dit wel wilt doen, moet u een routefilter koppelen.

Via een routefilter kunt u services identificeren die u wilt gebruiken via Microsoft-peering op uw ExpressRoute-circuit. Het is in feite een lijst met alle toegestane BGP-communitywaarden. Zodra een routefilter is gedefinieerd en gekoppeld aan een ExpressRoute-circuit, worden alle voorvoegsels die overeenkomen met de BGP-communitywaarden, geadverteerd naar uw netwerk.

Als u routefilters aan Microsoft 365-services wilt koppelen, moet u zijn gemachtigd om Microsoft 365-services te gebruiken via ExpressRoute. Als u niet bent gemachtigd om Microsoft 365-services te gebruiken via ExpressRoute, mislukt de bewerking voor het koppelen van routefilters. Zie [Azure ExpressRoute voor Microsoft 365](/microsoft-365/enterprise/azure-expressroute) voor meer informatie over het autorisatieproces.

> [!IMPORTANT]
> Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd vóór 1 augustus 2017, heeft alle servicevoorvoegsels die worden geadverteerd via Microsoft-peering, zelfs als er geen routefilters zijn gedefinieerd. Microsoft-peering van ExpressRoute-circuits die zijn geconfigureerd op of na 1 augustus 2017, heeft geen voorvoegsels die worden geadverteerd totdat een routefilter aan het circuit is gekoppeld.
> 

## <a name="prerequisites"></a>Vereisten

- Bekijk de [vereisten](expressroute-prerequisites.md) en [werkstromen](expressroute-workflows.md) voordat u begint met de configuratie.

- U moet een actief ExpressRoute-circuit hebben waarvoor Microsoft-peering is ingericht. U kunt de volgende instructies gebruiken om deze taken uit te voeren:
  - [Maak een ExpressRoute-circuit](expressroute-howto-circuit-portal-resource-manager.md) en laat het circuit inschakelen door de connectiviteitsprovider voordat u verdergaat. Het ExpressRoute-circuit moet zijn ingericht en ingeschakeld.
  - [Maak Microsoft-peering](expressroute-howto-routing-portal-resource-manager.md) als u de BGP-sessie rechtstreeks beheert. Of zorg ervoor dat de connectiviteitsprovider Microsoft-peering inricht voor het circuit.

## <a name="get-a-list-of-prefixes-and-bgp-community-values"></a><a name="prefixes"></a>Een lijst met voorvoegsels en BGP-communitywaarden ophalen

### <a name="get-a-list-of-bgp-community-values"></a>Een lijst met BGP-communitywaarden ophalen

BGP-communitywaarden die zijn gekoppeld aan services die toegankelijk zijn via Microsoft-peering, zijn beschikbaar op de pagina [ExpressRoute-routeringsvereisten](expressroute-routing.md).

### <a name="make-a-list-of-the-values-that-you-want-to-use"></a>Maak een lijst met waarden die u wilt gebruiken

Maak een lijst met de [BGP-communitywaarden](expressroute-routing.md#bgp) die u wilt gebruiken in het routefilter. 

## <a name="create-a-route-filter-and-a-filter-rule"></a><a name="filter"></a>Een routefilter en een filterregel maken

Een routefilter kan slechts één regel bevatten, en de regel moet van het type Toestaan zijn. Aan deze regel kan een lijst met BGP-communitywaarden zijn gekoppeld.

1. Selecteer **Een resource maken** en ga naar *Routefilter*, zoals weergegeven in de volgende afbeelding:

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

1. Plaats het routefilter in een resourcegroep. Zorg ervoor dat de locatie hetzelfde is als het ExpressRoute-circuit. Selecteer **Controleren en maken** en vervolgens **Maken**.

    :::image type="content" source="./media/how-to-routefilter-portal/create-route-filter-basic.png" alt-text="Schermopname van de pagina Routefilter":::

### <a name="create-a-filter-rule"></a>Een filterregel maken

1. Als u regels wilt toevoegen en bijwerken, selecteert u het tabblad Regel beheren voor het routefilter.

    :::image type="content" source="./media/how-to-routefilter-portal/manage-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

1. Selecteer de services waarmee u verbinding wilt maken in de vervolgkeuzelijst, en sla de regel op wanneer u klaar bent.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Schermopname van de pagina Routefilter":::

## <a name="attach-the-route-filter-to-an-expressroute-circuit"></a><a name="attach"></a>Het routefilter koppelen aan een ExpressRoute-circuit

Koppel het routefilter aan een circuit door de knop **+ Circuit toevoegen** te selecteren, en selecteer het ExpressRoute-circuit in de vervolgkeuzelijst.

:::image type="content" source="./media/how-to-routefilter-portal/add-circuit-to-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

Als met de connectiviteitsprovider peering is geconfigureerd voor uw ExpressRoute-circuit, vernieuwt u het circuit op de pagina ExpressRoute-circuit vóórdat u de knop **+ Circuit toevoegen** selecteert.

:::image type="content" source="./media/how-to-routefilter-portal/refresh-express-route-circuit.png" alt-text="Schermopname van de pagina Routefilter":::

## <a name="common-tasks"></a><a name="tasks"></a>Algemene taken

### <a name="to-get-the-properties-of-a-route-filter"></a><a name="getproperties"></a>De eigenschappen van een routefilter ophalen

U kunt de eigenschappen van een routefilter bekijken, wanneer u de resource opent in de portal.

:::image type="content" source="./media/how-to-routefilter-portal/view-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

### <a name="to-update-the-properties-of-a-route-filter"></a><a name="updateproperties"></a>De eigenschappen van een routefilter bijwerken

1. U kunt de lijst met BGP-communitywaarden bijwerken die zijn gekoppeld aan een circuit, door de knop **Regel beheren** te selecteren.

    :::image type="content" source="./media/how-to-routefilter-portal/update-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

1. Selecteer de servicecommunity's en selecteer vervolgens **Opslaan**.

    :::image type="content" source="./media/how-to-routefilter-portal/add-route-filter-rule.png" alt-text="Schermopname van de pagina Routefilter":::

### <a name="to-detach-a-route-filter-from-an-expressroute-circuit"></a><a name="detach"></a>Een routefilter loskoppelen van een ExpressRoute-circuit

Als u een circuit wilt loskoppelen van het routefilter, klikt u met de rechtermuisknop op het circuit en selecteert u **Loskoppelen**.

:::image type="content" source="./media/how-to-routefilter-portal/detach-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::


## <a name="clean-up-resources"></a>Resources opschonen

U kunt een routefilter verwijderen door de knop **Verwijderen** te selecteren. Zorg ervoor dat het routefilter niet is gekoppeld aan circuits voordat u dit doet.

:::image type="content" source="./media/how-to-routefilter-portal/delete-route-filter.png" alt-text="Schermopname van de pagina Routefilter":::

## <a name="next-steps"></a>Volgende stappen

Zie voor informatie over voorbeelden van routerconfiguratie:

> [!div class="nextstepaction"]
> [Voorbeelden van routerconfiguraties voor het instellen en beheren van routering](expressroute-config-samples-routing.md)
