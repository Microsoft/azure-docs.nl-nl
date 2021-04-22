---
title: Statisch uitgaand IP-adres configureren
description: Azure Firewall en door de gebruiker gedefinieerde routes configureren voor Azure Container Instances workloads die gebruikmaken van het openbare IP-adres van de firewall voor in- en uitvoeren
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: a03c59652b9409d54bbe63c63a31fdd2228ac34e
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107878682"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Eén openbaar IP-adres configureren voor uitgaand en inkomende verkeer naar een containergroep

Door een [containergroep met een](container-instances-container-groups.md) extern gericht IP-adres in te stellen, kunnen externe clients het IP-adres gebruiken voor toegang tot een container in de groep. Een browser heeft bijvoorbeeld toegang tot een web-app die wordt uitgevoerd in een container. Momenteel gebruikt een containergroep echter een ander IP-adres voor uitgaand verkeer. Dit ip-adres voor het egressie-adres wordt niet programmatisch blootgesteld, waardoor de bewaking en configuratie van clientfirewallregels voor containergroep complexer wordt.

Dit artikel bevat stappen voor het configureren van een containergroep in een [virtueel netwerk dat](container-instances-virtual-network-concepts.md) is geïntegreerd met [Azure Firewall](../firewall/overview.md). Door een door de gebruiker gedefinieerde route naar de containergroep en firewallregels in te stellen, kunt u verkeer van en naar de containergroep routeer en identificeren. In- en uit te gaan van een containergroep gebruiken het openbare IP-adres van de firewall. Eén IP-adres voor een egress kan worden gebruikt door meerdere containergroepen die zijn geïmplementeerd in het subnet van het virtuele netwerk dat is gedelegeerd aan Azure Container Instances.

In dit artikel gebruikt u de Azure CLI om de resources voor dit scenario te maken:

* Containergroepen die zijn geïmplementeerd op een gedelegeerd subnet [in het virtuele netwerk](container-instances-vnet.md) 
* Een Azure-firewall geïmplementeerd in het netwerk met een statisch openbaar IP-adres
* Een door de gebruiker gedefinieerde route op het subnet van de containergroepen
* Een NAT-regel voor ingressie van de firewall en een toepassingsregel voorgress

Vervolgens valideert u ingress en egress vanuit voorbeeldcontainergroepen via de firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>ACI implementeren in een virtueel netwerk

In veel gevallen hebt u mogelijk al een virtueel Azure-netwerk waarin u een containergroep kunt implementeren. Voor demonstratiedoeleinden maken de volgende opdrachten een virtueel netwerk en subnet wanneer de containergroep wordt gemaakt. Het subnet wordt gedelegeerd aan Azure Container Instances. 

De containergroep voert een kleine web-app uit vanuit de `aci-helloworld` afbeelding. Zoals u in andere artikelen in de documentatie kunt zien, verpakt deze afbeelding een kleine web-app die is geschreven in Node.js een statische HTML-pagina.

Als u er een nodig hebt, maakt u eerst een Azure-resourcegroep met [de opdracht az group create.][az-group-create] Bijvoorbeeld:

```azurecli
az group create --name myResourceGroup --location eastus
```

Gebruik een omgevingsvariabele voor de naam van de resourcegroep om de volgende opdrachtvoorbeelden te vereenvoudigen:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Maak de containergroep met de [opdracht az container create:][az-container-create]

```azurecli
az container create \
  --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --image mcr.microsoft.com/azuredocs/aci-helloworld \
  --vnet aci-vnet \
  --vnet-address-prefix 10.0.0.0/16 \
  --subnet aci-subnet \
  --subnet-address-prefix 10.0.0.0/24
```

> [!TIP]
> Pas de waarde van `--subnet address-prefix` aan voor de IP-adresruimte die u nodig hebt in uw subnet. Het kleinste ondersteunde subnet is /29, dat acht IP-adressen biedt. Sommige IP-adressen zijn gereserveerd voor gebruik door Azure.

Voor gebruik in een latere stap kunt u het privé-IP-adres van de containergroep op halen door de opdracht [az container show][az-container-show] uit te voeren:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Een Azure Firewall in het netwerk implementeren

In de volgende secties gebruikt u de Azure CLI om een Azure-firewall in het virtuele netwerk te implementeren. Zie Zelfstudie: Een app implementeren [en configureren Azure Firewall behulp van de Azure Portal.](../firewall/deploy-cli.md)

Gebruik eerst az [network vnet subnet create][az-network-vnet-subnet-create] om een subnet met de naam AzureFirewallSubnet toe te voegen voor de firewall. AzureFirewallSubnet is de *vereiste naam* van dit subnet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Gebruik de volgende [Azure CLI-opdrachten om](../firewall/deploy-cli.md) een firewall in het subnet te maken.

Als deze nog niet is geïnstalleerd, voegt u de firewallextensie toe aan de Azure CLI met behulp van [de opdracht az extension add:][az-extension-add]

```azurecli
az extension add --name azure-firewall
```

De firewall-resources maken:

```azurecli
az network firewall create \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus

az network public-ip create \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --allocation-method static \
  --sku standard
    
az network firewall ip-config create \
  --firewall-name myFirewall \
  --name FW-config \
  --public-ip-address fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet
```

Werk de firewallconfiguratie bij met de [opdracht az network firewall update:][az-network-firewall-update]

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Haal het privé-IP-adres van de firewall op met [de opdracht az network firewall ip-config list.][az-network-firewall-ip-config-list] Dit privé-IP-adres wordt gebruikt in een latere opdracht.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Haal het openbare IP-adres van de firewall op met [de opdracht az network public-ip show.][az-network-public-ip-show] Dit openbare IP-adres wordt gebruikt in een latere opdracht.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Door de gebruiker gedefinieerde route definiëren op ACI-subnet

Definieer een gebruiks gedefinieerde route op het ACI-subnet om verkeer om te leiden naar de Azure-firewall. Zie Netwerkverkeer [routeer voor meer informatie.](../virtual-network/tutorial-create-route-table-cli.md) 

### <a name="create-route-table"></a>Routetabel maken

Voer eerst de volgende [opdracht az network route-table create uit][az-network-route-table-create] om de routetabel te maken. Maak de routetabel in dezelfde regio als het virtuele netwerk.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Route maken

Voer [az network-route-table route create uit om][az-network-route-table-route-create] een route te maken in de routetabel. Als u verkeer naar de firewall wilt door sturen, stelt u het volgende hoptype in op en geef het privé-IP-adres van de firewall door als het adres `VirtualAppliance` van de volgende hop.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Routetabel koppelen aan ACI-subnet

Voer de [opdracht az network vnet subnet update][az-network-vnet-subnet-update] uit om de routetabel te koppelen aan het subnet dat is gedelegeerd Azure Container Instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Regels voor firewall configureren

Standaard worden Azure Firewall (blokken) inkomende en uitgaande verkeer niet geaccepteerd. 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>NAT-regel configureren op firewall naar ACI-subnet

Maak een [NAT-regel](../firewall/rule-processing.md) op de firewall om inkomende internetverkeer om te zetten en te filteren naar de toepassingscontainer die u eerder in het netwerk hebt gestart. Zie Inkomende internetverkeer filteren met Azure Firewall [DNAT voor meer informatie](../firewall/tutorial-firewall-dnat.md)

Maak een NAT-regel en -verzameling met behulp van [de opdracht az network firewall nat-rule create:][az-network-firewall-nat-rule-create]

```azurecli
az network firewall nat-rule create \
  --firewall-name myFirewall \
  --collection-name myNATCollection \
  --action dnat \
  --name myRule \
  --protocols TCP \
  --source-addresses '*' \
  --destination-addresses $FW_PUBLIC_IP \
  --destination-ports 80 \
  --resource-group $RESOURCE_GROUP_NAME \
  --translated-address $ACI_PRIVATE_IP \
  --translated-port 80 \
  --priority 200
```

Voeg zo nodig NAT-regels toe om verkeer naar andere IP-adressen in het subnet te filteren. Andere containergroepen in het subnet kunnen bijvoorbeeld IP-adressen voor inkomende verkeer blootstellen of andere interne IP-adressen kunnen worden toegewezen aan de containergroep na het opnieuw opstarten.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Uitgaande toepassingsregel maken op de firewall

Voer de volgende [az network firewall application-rule create-opdracht][az-network-firewall-application-rule-create] uit om een uitgaande regel op de firewall te maken. Deze voorbeeldregel staat toegang toe vanaf het subnet dat is gedelegeerd Azure Container Instances tot de FQDN. `checkip.dyndns.org` HTTP-toegang tot de site wordt in een latere stap gebruikt om het IP-adres van het Azure Container Instances.

```azurecli
az network firewall application-rule create \
  --collection-name myAppCollection \
  --firewall-name myFirewall \
  --name Allow-CheckIP \
  --protocols Http=80 Https=443 \
  --resource-group $RESOURCE_GROUP_NAME \
  --target-fqdns checkip.dyndns.org \
  --source-addresses 10.0.0.0/24 \
  --priority 200 \
  --action Allow
```

## <a name="test-container-group-access-through-the-firewall"></a>Toegang tot containergroep testen via de firewall

In de volgende secties wordt gecontroleerd of het subnet dat is gedelegeerd Azure Container Instances correct is geconfigureerd achter de Azure-firewall. In de vorige stappen is zowel binnenkomend verkeer naar het subnet als uitgaand verkeer van het subnet via de firewall gerouteerd.

### <a name="test-ingress-to-a-container-group"></a>Ingress naar een containergroep testen

Test de binnenkomende toegang tot de *appcontainer die* wordt uitgevoerd in het virtuele netwerk door naar het openbare IP-adres van de firewall te bladeren. Eerder hebt u het openbare IP-adres opgeslagen in variabele $FW_PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

De uitvoer ziet er ongeveer zo uit:

```console
52.142.18.133
```

Als de NAT-regel op de firewall juist is geconfigureerd, ziet u het volgende wanneer u het openbare IP-adres van de firewall in uw browser in typt:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Blader naar het openbare IP-adres van de firewall":::

### <a name="test-egress-from-a-container-group"></a>Egress uit een containergroep testen


Implementeer de volgende voorbeeldcontainer in het virtuele netwerk. Wanneer deze wordt uitgevoerd, wordt er één HTTP-aanvraag naar verstuurd, waarin het IP-adres van de afzender (het ip-adres van `http://checkip.dyndns.org` het egress-adres) wordt weergegeven. Als de toepassingsregel op de firewall juist is geconfigureerd, wordt het openbare IP-adres van de firewall geretourneerd.

```azurecli
az container create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress \
  --image mcr.microsoft.com/azuredocs/aci-tutorial-sidecar \
  --command-line "curl -s http://checkip.dyndns.org" \
  --restart-policy OnFailure \
  --vnet aci-vnet \
  --subnet aci-subnet
```

Bekijk de containerlogboeken om te bevestigen dat het IP-adres hetzelfde is als het openbare IP-adres van de firewall.

```azurecli
az container logs \
  --resource-group $RESOURCE_GROUP_NAME \
  --name testegress 
```

De uitvoer ziet er ongeveer zo uit:

```console
<html><head><title>Current IP Check</title></head><body>Current IP Address: 52.142.18.133</body></html>
```

## <a name="next-steps"></a>Volgende stappen

In dit artikel stelt u containergroepen in een virtueel netwerk in achter een Azure-firewall. U hebt een door de gebruiker gedefinieerde route en NAT- en toepassingsregels geconfigureerd in de firewall. Met behulp van deze configuratie stelt u één statisch IP-adres in voor in- en Azure Container Instances.

Voor meer informatie over het beheren van verkeer en het beveiligen van Azure-resources, zie [Azure Firewall](../firewall/index.yml) documentatie.



[az-group-create]: /cli/azure/group#az_group_create
[az-container-create]: /cli/azure/container#az_container_create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_create
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-network-firewall-update]: /cli/azure/network/firewall#az_network_firewall_update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az_network_public_ip_show
[az-network-route-table-create]:/cli/azure/network/route-table/#az_network_route_table_create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az_network_route_table_route_create
[az-network-firewall-ip-config-list]: /cli/azure/network/firewall/ip-config#az_network_firewall_ip_config_list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az_network_vnet_subnet_update
[az-container-exec]: /cli/azure/container#az_container_exec
[az-vm-create]: /cli/azure/vm#az_vm_create
[az-vm-open-port]: /cli/azure/vm#az_vm_open_port
[az-vm-list-ip-addresses]: /cli/azure/vm#az_vm_list_ip_addresses
[az-network-firewall-application-rule-create]: /cli/azure/network/firewall/application-rule#az_network_firewall_application_rule_create
[az-network-firewall-nat-rule-create]: /cli/azure/network/firewall/nat-rule#az_network_firewall_nat_rule_create
