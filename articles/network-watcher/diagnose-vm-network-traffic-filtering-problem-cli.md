---
title: 'Quickstart: Een diagnose uitvoeren voor een probleem met netwerkverkeersfilters op een VM - Azure CLI'
titleSuffix: Azure Network Watcher
description: U leert hoe u met Azure CLI een diagnose uitvoert voor een probleem met netwerkverkeersfilters op een virtuele machine met behulp van de functie IP-stroomverificatie in Azure Network Watcher.
services: network-watcher
documentationcenter: network-watcher
author: KumudD
manager: twooley
editor: ''
tags: azure-resource-manager
Customer intent: I need to diagnose a virtual machine (VM) network traffic filter problem that prevents communication to and from a VM.
ms.assetid: ''
ms.service: network-watcher
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: network-watcher
ms.workload: infrastructure
ms.date: 04/20/2018
ms.author: kumud
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: 0a483bc6097c4dd76ed67e93e4313ad8c25cbc08
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94542351"
---
# <a name="quickstart-diagnose-a-virtual-machine-network-traffic-filter-problem---azure-cli"></a>Quickstart: Diagnose uitvoeren voor een probleem met netwerkverkeersfilters op een virtuele machine - Azure CLI

In deze snelstart implementeert u een VM (virtuele machine) en controleert u vervolgens de communicatie naar een IP-adres en URL, en vanaf een IP-adres. U stelt de oorzaak van mislukte communicatie vast en leert hoe u dit probleem kunt oplossen.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Voor deze quickstart is versie 2.0 of hoger van Azure CLI vereist. Als u Azure Cloud Shell gebruikt, is de nieuwste versie al geïnstalleerd. 

- De Azure CLI-opdrachten in deze quickstart zijn zo opgemaakt dat ze kunnen worden uitgevoerd in een Bash-shell.

## <a name="create-a-vm"></a>Een virtuele machine maken

Voordat u een VM kunt maken, maakt u eerst een resourcegroep die de VM bevat. Maak een resourcegroep maken met [az group create](/cli/azure/group). In het volgende voorbeeld wordt een resourcegroep met de naam *myResourceGroup* gemaakt op de locatie *eastus*:

```azurecli-interactive
az group create --name myResourceGroup --location eastus
```

Maak een VM met [az vm create](/cli/azure/vm). Als SSH-sleutels niet al bestaan op de standaardlocatie van de sleutel, worden ze met deze opdracht gemaakt. Als u een specifieke set sleutels wilt gebruiken, gebruikt u de optie `--ssh-key-value`. In het volgende voorbeeld wordt een VM gemaakt met de naam *myVm*:

```azurecli-interactive
az vm create \
  --resource-group myResourceGroup \
  --name myVm \
  --image UbuntuLTS \
  --generate-ssh-keys
```

Het maken van de virtuele machine duurt een paar minuten. Ga niet door met de resterende stappen totdat de VM is gemaakt en uitvoer wordt geretourneerd door de Azure CLI.

## <a name="test-network-communication"></a>Netwerkcommunicatie testen

Als u de netwerkcommunicatie met Network Watcher wilt testen, moet u eerst een netwerk-watcher inschakelen in de regio met de VM die u wilt testen. Gebruik vervolgens de functie IP-stroomverificatie in Network Watcher om de communicatie te testen.

### <a name="enable-network-watcher"></a>Netwerk-watcher inschakelen

Als u al een netwerk-watcher hebt ingeschakeld in de regio VS - oost, gaat u verder met [IP-stroomverificatie gebruiken](#use-ip-flow-verify). Gebruik de opdracht [az network watcher configure](/cli/azure/network/watcher#az-network-watcher-configure) om een netwerk-watcher te maken in de regio VS Oost:

```azurecli-interactive
az network watcher configure \
  --resource-group NetworkWatcherRG \
  --locations eastus \
  --enabled
```

### <a name="use-ip-flow-verify"></a>IP-stroomverificatie gebruiken

Als u een VM maakt, wordt netwerkverkeer van en naar de VM standaard toegestaan en geweigerd in Azure. U kunt de standaardinstellingen in Azure later negeren en extra typen verkeer toestaan en weigeren. Gebruik de opdracht [az network watcher test-ip-flow](/cli/azure/network/watcher#az-network-watcher-test-ip-flow) om te testen of verkeer naar verschillende bestemmingen en vanaf een bron-IP-adres is toegestaan of wordt geweigerd.

Uitgaande communicatie van de VM naar een van de IP-adressen testen voor www.bing.com:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 13.107.21.200:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Na enkele seconden blijkt uit het resultaat dat toegang is toegestaan op basis van een beveiligingsregel met de naam **AllowInternetOutbound**.

Uitgaande communicatie van de VM naar 172.31.0.100 testen:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction outbound \
  --local 10.0.0.4:60000 \
  --protocol TCP \
  --remote 172.31.0.100:80 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Uit het resultaat blijkt dat toegang wordt geweigerd op basis van een beveiligingsregel met de naam **DefaultOutboundDenyAll**.

Binnenkomende communicatie van 172.31.0.100 naar de VM testen:

```azurecli-interactive
az network watcher test-ip-flow \
  --direction inbound \
  --local 10.0.0.4:80 \
  --protocol TCP \
  --remote 172.31.0.100:60000 \
  --vm myVm \
  --nic myVmVMNic \
  --resource-group myResourceGroup \
  --out table
```

Uit het resultaat blijkt dat toegang wordt geweigerd vanwege een beveiligingsregel met de naam **DefaultInboundDenyAll**. Nu u weet op basis van welke beveiligingsregels verkeer van en naar een VM is toegestaan of wordt geweigerd, kunt u bepalen hoe u de problemen wilt oplossen.

## <a name="view-details-of-a-security-rule"></a>Details van een beveiligingsregel weergeven

Als u wilt vaststellen waarom communicatie is toegestaan of wordt geblokkeerd op basis van de regels in [IP-stroomverificatie gebruiken](#use-ip-flow-verify), bekijkt u de effectieve beveiligingsregels voor de netwerkinterface via de opdracht [az network nic list-effective-nsg](/cli/azure/network/nic#az-network-nic-list-effective-nsg):

```azurecli-interactive
az network nic list-effective-nsg \
  --resource-group myResourceGroup \
  --name myVmVMNic
```

De geretourneerde uitvoer bevat de volgende tekst voor de regel **AllowInternetOutbound** op basis waarvan uitgaand verkeer naar www.bing.com was toegestaan in een vorige stap onder [IP-stroomverificatie gebruiken](#use-ip-flow-verify):

```console
{
 "access": "Allow",
 "additionalProperties": {},
 "destinationAddressPrefix": "Internet",
 "destinationAddressPrefixes": [
  "Internet"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": [
  "1.0.0.0/8",
  "2.0.0.0/7",
  "4.0.0.0/6",
  "8.0.0.0/7",
  "11.0.0.0/8",
  "12.0.0.0/6",
  ...
 ],
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/AllowInternetOutBound",
 "priority": 65001,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

In de vorige uitvoer ziet u dat **destinationAddressPrefix** **Internet** is. Het is niet duidelijk hoe 13.107.21.200 is gerelateerd aan **Internet**. Er worden verschillende adresvoorvoegsels weergegeven onder **expandedDestinationAddressPrefix**. Een van de voorvoegsels in de lijst is **12.0.0.0/6**, dat het bereik met IP-adressen 12.0.0.1-15.255.255.254 bevat. Aangezien 13.107.21.200 zich binnen dit adresbereik bevindt, is uitgaand verkeer toegestaan op basis van de regel **AllowInternetOutBound**. Daarnaast worden in de vorige uitvoer geen regels met hogere prioriteit (lagere waarde) weergegeven op basis waarvan deze regel wordt genegeerd. Als u uitgaande communicatie naar een IP-adres wilt weigeren, kunt u een beveiligingsregel met een hogere prioriteit toevoegen op basis waarvan uitgaand verkeer via poort 80 naar het IP-adres wordt geweigerd.

Toen u de opdracht `az network watcher test-ip-flow` in [IP-stroomverificatie gebruiken](#use-ip-flow-verify) uitvoerde om uitgaande communicatie van 172.131.0.100 te testen, bleek uit de uitvoer dat communicatie wordt geweigerd op basis van de regel **DefaultOutboundDenyAll**. De regel **DefaultOutboundDenyAll** is gelijk aan de regel **DenyAllOutBound** die wordt weergegeven in de volgende uitvoer via de opdracht `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Outbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllOutBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
}
```

In de regel wordt **0.0.0.0/0** vermeld als **destinationAddressPrefix**. Op basis van de regel wordt uitgaande communicatie naar 172.131.0.100 geweigerd, omdat het adres zich niet in **destinationAddressPrefix** bevindt van een van de overige regels voor uitgaand verkeer via de opdracht `az network nic list-effective-nsg`. Als u de uitgaande communicatie wilt toestaan, kunt u een beveiligingsregel toevoegen met een hogere prioriteit, op basis waarvan uitgaand verkeer naar poort 80 is toegestaan op 172.131.0.100.

Toen u de opdracht `az network watcher test-ip-flow` in [IP-stroomverificatie gebruiken](#use-ip-flow-verify) uitvoerde om binnenkomende communicatie van 172.131.0.100 te testen, bleek uit de uitvoer dat communicatie wordt geweigerd op basis van de regel **DefaultInboundDenyAll**. De regel **DefaultInboundDenyAll** is gelijk aan de regel **DenyAllInBound** die wordt weergegeven in de volgende uitvoer via de opdracht `az network nic list-effective-nsg`:

```console
{
 "access": "Deny",
 "additionalProperties": {},
 "destinationAddressPrefix": "0.0.0.0/0",
 "destinationAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "destinationPortRange": "0-65535",
 "destinationPortRanges": [
  "0-65535"
 ],
 "direction": "Inbound",
 "expandedDestinationAddressPrefix": null,
 "expandedSourceAddressPrefix": null,
 "name": "defaultSecurityRules/DenyAllInBound",
 "priority": 65500,
 "protocol": "All",
 "sourceAddressPrefix": "0.0.0.0/0",
 "sourceAddressPrefixes": [
  "0.0.0.0/0"
 ],
 "sourcePortRange": "0-65535",
 "sourcePortRanges": [
  "0-65535"
 ]
},
```

De regel **DenyAllInBound** is toegepast omdat, zoals wordt weergegeven in de uitvoer, de uitvoer via de opdracht `az network nic list-effective-nsg` geen andere regel met hoge prioriteit bevat, op basis waarvan binnenkomend verkeer via poort 80 van 172.131.0.100 naar de VM is toegestaan. Als u de binnenkomende communicatie wilt toestaan, kunt u een beveiligingsregel met een hogere prioriteit toevoegen, op basis waarvan binnenkomend verkeer van 172.131.0.100 via poort 80 is toegestaan.

Met de controles in deze snelstart is de Azure-configuratie getest. Als de controles onverwachte resultaten retourneren en u nog steeds netwerkproblemen ondervindt, controleert u of er geen firewall is geplaatst tussen de VM en het eindpunt waarmee u communiceert, en of het besturingssysteem op de VM geen firewall heeft waardoor communicatie is toegestaan of wordt geweigerd.

## <a name="clean-up-resources"></a>Resources opschonen

U kunt de opdracht [az group delete](/cli/azure/group) gebruiken om de resourcegroep en alle resources die deze bevat te verwijderen, als deze niet meer nodig zijn:

```azurecli-interactive
az group delete --name myResourceGroup --yes
```

## <a name="next-steps"></a>Volgende stappen

In deze snelstart hebt u een VM gemaakt en diagnose uitgevoerd voor netwerkfilters voor binnenkomend en uitgaand verkeer. U hebt geleerd dat verkeer van en naar een VM kan zijn toegestaan of worden geweigerd op basis van regels voor netwerkbeveiligingsgroepen. Meer informatie over [beveiligingsregels](../virtual-network/security-overview.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json) en het [maken van beveiligingsregels](../virtual-network/manage-network-security-group.md?toc=%2fazure%2fnetwork-watcher%2ftoc.json#create-a-security-rule).

Zelfs als de juiste netwerkverkeersfilters zijn toegepast, kan communicatie naar een VM mislukken, vanwege de configuratie van de routering. Zie [Diagnose uitvoeren voor problemen met VM-routering](diagnose-vm-network-routing-problem-cli.md) voor informatie over het vaststellen van routeringsproblemen met VM-netwerken. Of zie [Problemen met de verbinding oplossen ](network-watcher-connectivity-cli.md) om met één hulpprogramma de problemen vast te stellen met routering van uitgaand verkeer, latentie en verkeersfilters.
