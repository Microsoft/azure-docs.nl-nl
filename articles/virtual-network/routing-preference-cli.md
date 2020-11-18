---
title: Routerings voorkeur configureren voor een openbaar IP-adres met behulp van Azure CLI
titlesuffix: Azure Virtual Network
description: Meer informatie over het maken van een openbaar IP-adres met een Internet verkeers routerings voorkeur met behulp van de Azure CLI.
services: virtual-network
documentationcenter: na
author: KumudD
manager: mtillman
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 05/18/2020
ms.author: mnayak
ms.custom: devx-track-azurecli
ms.openlocfilehash: 5e7a8c5552165324ef154767d1605e12b0c9ad22
ms.sourcegitcommit: c2dd51aeaec24cd18f2e4e77d268de5bcc89e4a7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94747856"
---
# <a name="configure-routing-preference-for-a-public-ip-address-using-azure-cli"></a>Routerings voorkeur configureren voor een openbaar IP-adres met behulp van Azure CLI

In dit artikel wordt beschreven hoe u routerings voorkeur configureert via ISP Network (**Internet** optie) voor een openbaar IP-adres met behulp van Azure cli. Nadat u het open bare IP-adres hebt gemaakt, kunt u dit koppelen aan de volgende Azure-resources voor binnenkomend en uitgaand verkeer naar Internet:

* Virtuele machine
* Schaalset voor virtuele machines
* Azure Kubernetes Service (AKS)
* Internet gerichte load balancer
* Application Gateway
* Azure Firewall

De routerings voorkeur voor openbaar IP-adres is standaard ingesteld op het micro soft Global Network voor alle Azure-Services en kan worden gekoppeld aan elke Azure-service.

> [!IMPORTANT]
> De voor keuren voor route ring is momenteel beschikbaar als open bare preview.
> Deze preview-versie wordt aangeboden zonder service level agreement en wordt niet aanbevolen voor productieworkloads. Misschien worden bepaalde functies niet ondersteund of zijn de mogelijkheden ervan beperkt. Zie [Supplemental Terms of Use for Microsoft Azure Previews (Aanvullende gebruiksvoorwaarden voor Microsoft Azure-previews)](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) voor meer informatie.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor dit artikel is versie 2.0.49 of hoger van de Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd.

## <a name="register-the-feature-for-your-subscription"></a>De functie voor uw abonnement registreren
De functie voor route ring is momenteel beschikbaar als preview-versie. Registreer de functie voor uw abonnement als volgt:
```azurecli
az feature register --namespace Microsoft.Network --name AllowRoutingPreferenceFeature
```

## <a name="create-a-resource-group"></a>Een resourcegroep maken
Een resourcegroep maken met de opdracht [az group create](/cli/azure/group#az-group-create). In het volgende voor beeld wordt een resource groep gemaakt in de regio **VS Oost** Azure:

```azurecli
  az group create --name myResourceGroup --location eastus
```
## <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken

Maak een openbaar IP-adres met een routerings voorkeur van het type ' Internet ' via opdracht [AZ Network Public-IP Create](/cli/azure/network/public-ip?view=azure-cli-latest#az-network-public-ip-create), met de volgende indeling, zoals hieronder wordt weer gegeven.

Met de volgende opdracht maakt u een nieuw openbaar IP-adres met **Internet** routering voor keur in de regio **VS Oost** Azure.

```azurecli
az network public-ip create \
--name MyRoutingPrefIP \
--resource-group MyResourceGroup \
--location eastus \
--ip-tags 'RoutingPreference=Internet' \
--sku STANDARD \
--allocation-method static \
--version IPv4
```

> [!NOTE]
>  Routerings voorkeur ondersteunt momenteel alleen open bare IP-adressen van IPV4.

U kunt het hierboven gemaakte open bare IP-adres koppelen aan een virtuele [Windows](../virtual-machines/windows/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -of [Linux](../virtual-machines/linux/overview.md?toc=%2fazure%2fvirtual-network%2ftoc.json) -machine. Gebruik de CLI-sectie op de pagina zelf studie: [een openbaar IP-adres koppelen aan een virtuele machine](associate-public-ip-address-vm.md#azure-cli) om de open bare IP aan uw VM te koppelen. U kunt ook het open bare IP-adres koppelen dat is gemaakt met met een [Azure Load Balancer](../load-balancer/load-balancer-overview.md)door het toe te wijzen aan de Load Balancer front- **End** -configuratie. Het openbare IP-adres doet dienst als een virtueel IP-adres (VIP) met taakverdeling.

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [routerings voorkeur in open bare IP-adressen](routing-preference-overview.md). 
- [Configureer routerings voorkeur voor een virtuele machine met behulp van de Azure cli](configure-routing-preference-virtual-machine-cli.md).

