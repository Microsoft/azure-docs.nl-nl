---
title: Voor beelden van Azure CLI voor Load Balancer
titleSuffix: Azure Load Balancer
description: Azure CLI-voorbeelden
services: load-balancer
documentationcenter: load-balancer
author: asudbring
ms.service: load-balancer
ms.devlang: na
ms.topic: article
ms.custom: seodec18, devx-track-azurecli
ms.tgt_pltfrm: ''
ms.workload: infrastructure
ms.date: 06/14/2018
ms.author: allensu
ms.openlocfilehash: fcc2a579f2fe9048dd58cd8b52c8c704c894936b
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87499344"
---
# <a name="azure-cli-samples-for-load-balancer"></a>Voor beelden van Azure CLI voor Load Balancer

De volgende tabel bevat koppelingen naar bash-scripts die zijn gebouwd met behulp van de Azure CLI.

* [Verkeer verdelen naar virtuele machines voor hoge beschikbaarheid](./scripts/load-balancer-linux-cli-sample-nlb.md)

  Maakt verschillende virtuele machines in een configuratie met hoge Beschik baarheid en taak verdeling.

* [Taken over VM's in meerdere beschikbaarheidszones verdelen](./scripts/load-balancer-linux-cli-sample-zone-redundant-frontend.md)

  Maakt drie Vm's in verschillende beschikbaarheids zones binnen een regio en Standard Load Balancer met een zone-redundant frontend-IP-adres. Deze load balancer configuratie helpt uw apps en gegevens te beschermen tegen onwaarschijnlijke storingen of verlies van een volledig Data Center.

* [Taken over VM's binnen een specifieke beschikbaarheidszone verdelen](./scripts/load-balancer-linux-cli-sample-zonal-frontend.md)

  Maakt drie Vm's, een Standard Load Balancer met zonegebonden front-IP-adres dat gegevens paden en bronnen in één zone voor een bepaalde regio kunt uitlijnen.

* [Taak verdeling meerdere websites op Vm's](./scripts/load-balancer-linux-cli-load-balance-multiple-websites-vm.md)

  Maakt twee Vm's met meerdere IP-configuraties die zijn gekoppeld aan een Beschikbaarheidsset van Azure, die toegankelijk is via een Azure Load Balancer.
