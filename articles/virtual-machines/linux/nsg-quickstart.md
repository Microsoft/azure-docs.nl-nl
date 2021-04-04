---
title: Poorten naar een virtuele machine openen met Azure CLI
description: Meer informatie over het openen van een poort/het maken van een eind punt voor uw virtuele machine met behulp van de Azure CLI.
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 12/13/2017
ms.author: cynthn
ms.custom: devx-track-azurecli
ms.openlocfilehash: a96c0f7c6fb767b96be273a615149143043e8bc1
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91975124"
---
# <a name="open-ports-and-endpoints-to-a-vm-with-the-azure-cli"></a>Poorten en eind punten openen voor een virtuele machine met Azure CLI

U opent een poort of maakt een eind punt naar een virtuele machine (VM) in azure door een netwerk filter te maken op een subnet-of VM-netwerk interface. U plaatst deze filters, waarmee zowel binnenkomend als uitgaand verkeer worden beheerd op een netwerk beveiligings groep die is gekoppeld aan de resource die het verkeer ontvangt. We gebruiken een algemeen voor beeld van webverkeer op poort 80. In dit artikel wordt beschreven hoe u een poort naar een virtuele machine opent met de Azure CLI. 


Als u een netwerk beveiligings groep en-regels wilt maken, moet u de nieuwste [Azure cli](/cli/azure/install-az-cli2) installeren en u aanmelden bij een Azure-account met [AZ login](/cli/azure/reference-index).

Vervang in de volgende voor beelden voorbeeld parameter namen door uw eigen waarden. Voor beelden van parameter namen zijn *myResourceGroup*, *myNetworkSecurityGroup* en *myVnet*.


## <a name="quickly-open-a-port-for-a-vm"></a>Snel een poort openen voor een virtuele machine
Als u snel een poort voor een virtuele machine in een dev/test-scenario moet openen, kunt u de opdracht [AZ VM Open-Port](/cli/azure/vm) gebruiken. Met deze opdracht wordt een netwerk beveiligings groep gemaakt, een regel toegevoegd en toegepast op een VM of subnet. In het volgende voor beeld wordt poort *80* van de virtuele machine met de naam *myVM* in de resource groep met de naam *myResourceGroup* geopend.

```azurecli
az vm open-port --resource-group myResourceGroup --name myVM --port 80
```

Ga verder met de extra stappen in dit artikel voor meer controle over de regels, zoals het definiëren van een bron-IP-adres bereik.


## <a name="create-a-network-security-group-and-rules"></a>Een netwerk beveiligings groep en-regels maken
Maak de netwerk beveiligings groep met [AZ Network NSG Create](/cli/azure/network/nsg). In het volgende voor beeld wordt een netwerk beveiligings groep met de naam *myNetworkSecurityGroup* gemaakt op de locatie *eastus* :

```azurecli
az network nsg create \
    --resource-group myResourceGroup \
    --location eastus \
    --name myNetworkSecurityGroup
```

Voeg een regel met [AZ Network NSG regel Create](/cli/azure/network/nsg/rule) toe om http-verkeer naar uw webserver toe te staan (of corrigeer uw eigen scenario, zoals SSH-toegang of database verbinding). In het volgende voor beeld wordt een regel gemaakt met de naam *myNetworkSecurityGroupRule* om TCP-verkeer toe te staan op poort 80:

```azurecli
az network nsg rule create \
    --resource-group myResourceGroup \
    --nsg-name myNetworkSecurityGroup \
    --name myNetworkSecurityGroupRule \
    --protocol tcp \
    --priority 1000 \
    --destination-port-range 80
```


## <a name="apply-network-security-group-to-vm"></a>Netwerk beveiligings groep Toep assen op VM
Koppel de netwerk beveiligings groep aan de netwerk interface van de VM (NIC) met [AZ Network NIC update](/cli/azure/network/nic). In het volgende voor beeld wordt een bestaande netwerk adapter met de naam *myNic* gekoppeld aan de netwerk beveiligings groep met de naam *myNetworkSecurityGroup*:

```azurecli
az network nic update \
    --resource-group myResourceGroup \
    --name myNic \
    --network-security-group myNetworkSecurityGroup
```

U kunt ook uw netwerk beveiligings groep koppelen aan een subnet van een virtueel netwerk met [AZ Network vnet subnet update](/cli/azure/network/vnet/subnet) in plaats van alleen de netwerk interface op één virtuele machine. In het volgende voor beeld wordt een bestaand subnet met de naam *mySubnet* in het virtuele netwerk *myVnet* gekoppeld aan de netwerk beveiligings groep met de naam *myNetworkSecurityGroup*:

```azurecli
az network vnet subnet update \
    --resource-group myResourceGroup \
    --vnet-name myVnet \
    --name mySubnet \
    --network-security-group myNetworkSecurityGroup
```

## <a name="more-information-on-network-security-groups"></a>Meer informatie over netwerk beveiligings groepen
Met de snelle opdrachten kunt u aan de slag gaan met verkeer dat naar uw virtuele machine wordt gestroomd. Netwerk beveiligings groepen bieden veel fantastische functies en granulatie voor het beheren van de toegang tot uw resources. U kunt hier meer lezen over het [maken van een netwerk beveiligings groep en ACL-regels](tutorial-virtual-network.md#secure-network-traffic).

Voor Maxi maal beschik bare webtoepassingen moet u uw virtuele machines achter een Azure Load Balancer plaatsen. De load balancer distribueert verkeer naar Vm's, met een netwerk beveiligings groep die verkeer filtering biedt. Zie [Load Balancing Linux virtual machines in azure (Engelstalig) voor meer informatie over het maken van een Maxi maal beschik bare toepassing](tutorial-load-balancer.md).

## <a name="next-steps"></a>Volgende stappen
In dit voor beeld hebt u een eenvoudige regel gemaakt om HTTP-verkeer toe te staan. In de volgende artikelen vindt u informatie over het maken van meer gedetailleerde omgevingen:

* [Overzicht van Azure Resource Manager](../../azure-resource-manager/management/overview.md)
* [Wat is een netwerkbeveiligingsgroep (NSG)?](../../virtual-network/network-security-groups-overview.md)