---
title: Een persoonlijke koppelings service delen in een virtueel WAN
titleSuffix: Azure Virtual WAN
description: Stappen voor het configureren van een persoonlijke koppeling in een virtueel WAN
services: virtual-wan
author: erjosito
ms.service: virtual-wan
ms.topic: how-to
ms.date: 09/22/2020
ms.author: jomore
ms.custom: fasttrack-new
ms.openlocfilehash: cc8e7314c941035207ecf809a9d85ef46bd58379
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92913752"
---
# <a name="use-private-link-in-virtual-wan"></a>Privé-koppeling in virtuele WAN gebruiken

[Persoonlijke Azure-koppeling](../private-link/private-link-overview.md) is een technologie waarmee u Azure Platform-as-a-service-aanbiedingen kunt verbinden met behulp van particuliere IP-adres Connectiviteit door [privé-eind punten](../private-link/private-endpoint-overview.md)zichtbaar te maken. Met Azure Virtual WAN kunt u een persoonlijk eind punt implementeren in een van de virtuele netwerken die zijn verbonden met een virtuele hub. Dit biedt connectiviteit met andere virtuele netwerken of vertakkingen die zijn verbonden met hetzelfde virtuele WAN.

## <a name="before-you-begin"></a>Voordat u begint

In de stappen in dit artikel wordt ervan uitgegaan dat u al een virtueel WAN hebt geïmplementeerd met een of meer hubs, evenals ten minste twee virtuele netwerken die zijn verbonden met een virtueel WAN.

Als u een nieuwe virtuele WAN en een nieuwe hub wilt maken, gebruikt u de stappen in de volgende artikelen:

* [Een virtueel WAN maken](virtual-wan-site-to-site-portal.md#openvwan)
* [Een hub maken](virtual-wan-site-to-site-portal.md#hub)
* [Een VNet verbinden met een hub](virtual-wan-site-to-site-portal.md#hub)

## <a name="create-a-private-link-endpoint"></a><a name="endpoint"></a>Een eind punt voor een persoonlijke verbinding maken

U kunt een persoonlijk eind punt voor een koppeling maken voor veel verschillende services. In dit voor beeld gebruiken we Azure SQL Database. U vindt meer informatie over het maken van een persoonlijk eind punt voor een Azure SQL Database in [Quick Start: Maak een persoonlijk eind punt met behulp van de Azure Portal](../private-link/create-private-endpoint-portal.md). In de volgende afbeelding ziet u de netwerk configuratie van de Azure SQL Database:

:::image type="content" source="./media/howto-private-link/create-private-link.png" alt-text="persoonlijke koppeling maken" lightbox="./media/howto-private-link/create-private-link.png":::

Nadat u de Azure SQL Database hebt gemaakt, kunt u het IP-adres van het privé-eind punt controleren door uw persoonlijke eind punten te doorzoeken:

:::image type="content" source="./media/howto-private-link/endpoints.png" alt-text="privé-eindpunten" lightbox="./media/howto-private-link/endpoints.png":::

Klik op het persoonlijke eind punt dat we hebben gemaakt, het persoonlijke IP-adres en de FQDN-naam (Fully Qualified Domain Name) moeten worden weer geven. Houd er rekening mee dat het persoonlijke eind punt een IP-adres heeft in het bereik van het VNet waar het is geïmplementeerd (10.1.3.0/24):

:::image type="content" source="./media/howto-private-link/sql-endpoint.png" alt-text="SQL-eind punt" lightbox="./media/howto-private-link/sql-endpoint.png":::

## <a name="verify-connectivity-from-the-same-vnet"></a><a name="connectivity"></a>Connectiviteit van hetzelfde VNet controleren

In dit voor beeld wordt de verbinding met de Azure SQL Database gecontroleerd vanaf een virtuele Ubuntu-machine waarop MS SQL-hulpprogram ma's zijn geïnstalleerd. De eerste stap is het controleren of de DNS-omzetting werkt en de Azure SQL Database Fully Qualified Domain name wordt omgezet naar een privé-IP-adres, in hetzelfde VNet waarin het persoonlijke eind punt is geïmplementeerd (10.1.3.0/24):

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Zoals u in de vorige uitvoer kunt zien, wordt de FQDN `wantest.database.windows.net` -naam toegewezen aan `wantest.privatelink.database.windows.net` , waardoor de privé-DNS-zone die is gemaakt via het persoonlijke eind punt, wordt omgezet naar het privé-IP-adres `10.1.3.228` . Als u wilt zoeken in de privé-DNS-zone, bevestigt u dat er een record is voor het persoonlijke eind punt dat is toegewezen aan het privé-IP-adres:

:::image type="content" source="./media/howto-private-link/dns-zone.png" alt-text="DNS-zone" lightbox="./media/howto-private-link/dns-zone.png":::

Nadat u de juiste DNS-omzetting hebt gecontroleerd, kunnen we proberen verbinding te maken met de Data Base:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.3.75
```

Zoals u kunt zien, gebruiken we een speciale SQL-query die ons het bron-IP-adres geeft dat de SQL-Server van de client ziet. In dit geval ziet de server de client met zijn privé-IP ( `10.1.3.75` ), wat betekent dat het verkeer rechtstreeks naar het persoonlijke eind punt gaat vanuit het VNet.

Houd er rekening mee dat u de variabelen moet instellen `username` en `password` overeenkomen met de referenties die zijn gedefinieerd in de Azure SQL database om de voor beelden in deze hand leiding te laten werken.

## <a name="connect-from-a-different-vnet"></a><a name="vnet"></a>Verbinding maken vanaf een ander VNet

Nu één VNet in azure Virtual WAN verbinding heeft met het persoonlijke eind punt, hebben alle andere VNets en vertakkingen die zijn verbonden met Virtual WAN ook toegang tot het certificaat. U moet verbinding maken via een van de modellen die door Azure Virtual WAN worden ondersteund, zoals [een wille keurig scenario](scenario-any-to-any.md) of het [Shared Services VNet-scenario](scenario-shared-services-vnet.md), om twee voor beelden te benoemen.

Zodra u verbinding hebt tussen het VNet of de vertakking met het VNet waar het persoonlijke eind punt is geïmplementeerd, moet u de DNS-omzetting configureren:

* Als u vanuit een VNet verbinding maakt met het persoonlijke eind punt, kunt u dezelfde persoonlijke zone gebruiken die is gemaakt met de Azure SQL Database.
* Als u verbinding maakt met het persoonlijke eind punt vanuit een vertakking (site-naar-site-VPN, punt-naar-site-VPN of ExpressRoute), moet u on-premises DNS-omzetting gebruiken.

In dit voor beeld maken we verbinding vanaf een ander VNet. Daarom koppelen we de privé-DNS-zone aan het nieuwe VNet, zodat de werk belastingen de Azure SQL Database FQDN-naam kunnen omzetten naar het privé-IP-adres. Dit doet u door de persoonlijke DNS-zone te koppelen aan het nieuwe VNet:

:::image type="content" source="./media/howto-private-link/dns-link.png" alt-text="DNS-koppeling" lightbox="./media/howto-private-link/dns-link.png":::

Nu moet elke virtuele machine in het gekoppelde VNet de Azure SQL Database FQDN correct omzetten naar het privé-IP-adres van de privé-koppeling:

```bash
$ nslookup wantest.database.windows.net
Server:         127.0.0.53
Address:        127.0.0.53#53

Non-authoritative answer:
wantest.database.windows.net    canonical name = wantest.privatelink.database.windows.net.
Name:   wantest.privatelink.database.windows.net
Address: 10.1.3.228
```

Als u wilt controleren of dit VNet (10.1.1.0/24) verbinding heeft met het oorspronkelijke VNet waar het persoonlijke eind punt is geconfigureerd (10.1.3.0/24), kunt u de juiste route tabel controleren op elke virtuele machine in het VNet:

:::image type="content" source="./media/howto-private-link/effective-routes.png" alt-text="efficiënte routes" lightbox="./media/howto-private-link/effective-routes.png":::

Zoals u ziet, is er een route die verwijst naar het VNet-10.1.3.0/24 dat is geïnjecteerd door de Virtual Network gateways in azure Virtual WAN. Nu kunt u de verbinding met de data base tot slot testen:

```bash
$ query="SELECT CONVERT(char(15), CONNECTIONPROPERTY('client_net_address'));"
$ sqlcmd -S wantest.database.windows.net -U $username -P $password -Q "$query"

10.1.1.75
```

In dit voor beeld laten we zien hoe het maken van een persoonlijk eind punt in een van de VNets die is gekoppeld aan een virtueel WAN, connectiviteit biedt voor de rest van VNets en branches in het virtuele WAN.

## <a name="next-steps"></a>Volgende stappen

Raadpleeg de [Veelgestelde vragen](virtual-wan-faq.md)voor meer informatie over Virtual WAN.
