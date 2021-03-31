---
title: Statisch uitgaand IP-adres configureren
description: Azure firewall en door de gebruiker gedefinieerde routes configureren voor Azure Container Instances workloads die gebruikmaken van het open bare IP-adres van de firewall voor binnenkomend en uitgaand verkeer
ms.topic: article
ms.date: 07/16/2020
ms.openlocfilehash: 497645b9fe7f908cc9b8b4d7ed0ba5e201570160
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "89566565"
---
# <a name="configure-a-single-public-ip-address-for-outbound-and-inbound-traffic-to-a-container-group"></a>Eén openbaar IP-adres configureren voor uitgaand en binnenkomend verkeer naar een container groep

Door een [container groep](container-instances-container-groups.md) met een extern IP-adres in te stellen, kunnen externe clients het IP-adres gebruiken om toegang te krijgen tot een container in de groep. Een browser kan bijvoorbeeld toegang krijgen tot een web-app die wordt uitgevoerd in een container. Op dit moment gebruikt een container groep echter een ander IP-adres voor uitgaand verkeer. Dit aflopende IP-adres wordt niet programmatisch weer gegeven, waardoor de controle en configuratie van de firewall regels van de client complexer wordt.

In dit artikel worden de stappen beschreven voor het configureren van een container groep in een [virtueel netwerk](container-instances-virtual-network-concepts.md) dat is geïntegreerd met [Azure firewall](../firewall/overview.md). Door een door de gebruiker gedefinieerde route naar de container groep en firewall regels in te stellen, kunt u verkeer van en naar de container groep routeren en identificeren. Ingangs-en uitgang van container groep gebruiken het open bare IP-adres van de firewall. Eén uitgaand IP-adres kan worden gebruikt door meerdere container groepen die zijn geïmplementeerd in het subnet van het virtuele netwerk, gedelegeerd aan Azure Container Instances.

In dit artikel gebruikt u de Azure CLI om de resources voor dit scenario te maken:

* Container groepen die zijn geïmplementeerd op een gedelegeerd subnet [in het virtuele netwerk](container-instances-vnet.md) 
* Een Azure-firewall die in het netwerk is geïmplementeerd met een statisch openbaar IP-adres
* Een door de gebruiker gedefinieerde route op het subnet van de container groepen
* Een NAT-regel voor inkomend firewall en een toepassings regel voor uitgaand verkeer

Vervolgens valideert u ingangs-en uitgangs-container groepen via de firewall.

## <a name="deploy-aci-in-a-virtual-network"></a>ACI in een virtueel netwerk implementeren

In de meeste gevallen hebt u mogelijk al een virtueel Azure-netwerk waarin u een container groep kunt implementeren. Voor demonstratie doeleinden maken de volgende opdrachten een virtueel netwerk en subnet wanneer de container groep wordt gemaakt. Het subnet wordt overgedragen aan Azure Container Instances. 

De container groep voert een kleine web-app uit vanuit de `aci-helloworld` installatie kopie. Zoals in andere artikelen in de documentatie wordt weer gegeven, verpakt deze afbeelding een kleine web-app die is geschreven in Node.js die een statische HTML-pagina vormt.

Als u er één nodig hebt, maakt u eerst een Azure-resource groep met de opdracht [AZ Group Create][az-group-create] . Bijvoorbeeld:

```azurecli
az group create --name myResourceGroup --location eastus
```

Als u de volgende opdracht voorbeelden wilt vereenvoudigen, gebruikt u een omgevings variabele voor de naam van de resource groep:

```console
export RESOURCE_GROUP_NAME=myResourceGroup
```

Maak de container groep met de opdracht [AZ container Create][az-container-create] :

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
> Pas de waarde aan van `--subnet address-prefix` de IP-adres ruimte die u nodig hebt in uw subnet. Het kleinste ondersteunde subnet is/29, dat acht IP-adressen bevat. Sommige IP-adressen zijn gereserveerd voor gebruik door Azure.

Als u in een latere stap wilt gebruiken, moet u het privé-IP-adres van de container groep ophalen door de opdracht [AZ container Show] [AZ-container-Show] uit te voeren:

```azurecli
ACI_PRIVATE_IP="$(az container show --name appcontainer \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress.ip --output tsv)"
```

## <a name="deploy-azure-firewall-in-network"></a>Azure Firewall implementeren in het netwerk

In de volgende secties gebruikt u de Azure CLI voor het implementeren van een Azure-firewall in het virtuele netwerk. Zie [zelf studie: Azure firewall implementeren en configureren met behulp van de Azure Portal](../firewall/deploy-cli.md)voor achtergrond informatie.

Gebruik eerst het [subnet AZ Network vnet Create][az-network-vnet-subnet-create] om een subnet met de naam AzureFirewallSubnet voor de firewall toe te voegen. AzureFirewallSubnet is de *vereiste* naam van dit subnet.

```azurecli
az network vnet subnet create \
  --name AzureFirewallSubnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet   \
  --address-prefix 10.0.1.0/26
```

Gebruik de volgende [Azure cli-opdrachten](../firewall/deploy-cli.md) om een firewall in het subnet te maken.

Als dat niet het geval is, voegt u de firewall uitbreiding toe aan de Azure CLI met behulp van de opdracht [AZ extension add][az-extension-add] :

```azurecli
az extension add --name azure-firewall
```

De firewall bronnen maken:

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

Werk de firewall configuratie bij met behulp van de opdracht [AZ Network Firewall update][az-network-firewall-update] :

```azurecli
az network firewall update \
  --name myFirewall \
  --resource-group $RESOURCE_GROUP_NAME
```

Haal het privé IP-adres van de firewall op met behulp van de opdracht [AZ Network Firewall IP-config list][az-network-firewall-ip-config-list] . Dit privé-IP-adres wordt gebruikt in een latere opdracht.


```azurecli
FW_PRIVATE_IP="$(az network firewall ip-config list \
  --resource-group $RESOURCE_GROUP_NAME \
  --firewall-name myFirewall \
  --query "[].privateIpAddress" --output tsv)"
```
Haal het open bare IP-adres van de firewall op met behulp van de opdracht [AZ Network public-ip show][az-network-public-ip-show] . Dit open bare IP-adres wordt gebruikt in een latere opdracht.

```azurecli
FW_PUBLIC_IP="$(az network public-ip show \
  --name fw-pip \
  --resource-group $RESOURCE_GROUP_NAME \
  --query ipAddress --output tsv)"
```

## <a name="define-user-defined-route-on-aci-subnet"></a>Door de gebruiker gedefinieerde route definiëren op ACI-subnet

Definieer een door gebruik gedefinieerde route op het ACI-subnet om het verkeer door te sturen naar de Azure-firewall. Zie [netwerk verkeer routeren](../virtual-network/tutorial-create-route-table-cli.md)voor meer informatie. 

### <a name="create-route-table"></a>Routetabel maken

Voer eerst de volgende [AZ Network route tabel Create][az-network-route-table-create] opdracht uit om de route tabel te maken. Maak de route tabel in dezelfde regio als het virtuele netwerk.

```azurecli
az network route-table create \
  --name Firewall-rt-table \
  --resource-group $RESOURCE_GROUP_NAME \
  --location eastus \
  --disable-bgp-route-propagation true
```

### <a name="create-route"></a>Route maken

Voer [AZ Network-route tabel route Create][az-network-route-table-route-create] uit om een route in de route tabel te maken. Als u verkeer wilt routeren naar de firewall, stelt u het type van de volgende hop in op `VirtualAppliance` en geeft u het privé IP-adres van de firewall door als het adres van de volgende hop.

```azurecli
az network route-table route create \
  --resource-group $RESOURCE_GROUP_NAME \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $FW_PRIVATE_IP
```

### <a name="associate-route-table-to-aci-subnet"></a>Route tabel koppelen aan ACI-subnet

Voer de opdracht [AZ Network vnet subnet update][az-network-vnet-subnet-update] uit om de route tabel te koppelen aan het subnet dat is overgedragen aan Azure container instances.

```azurecli
az network vnet subnet update \
  --name aci-subnet \
  --resource-group $RESOURCE_GROUP_NAME \
  --vnet-name aci-vnet \
  --address-prefixes 10.0.0.0/24 \
  --route-table Firewall-rt-table
```

## <a name="configure-rules-on-firewall"></a>Regels configureren op de firewall

Standaard worden inkomend en uitgaand verkeer door Azure Firewall geweigerd (geblokkeerd). 

### <a name="configure-nat-rule-on-firewall-to-aci-subnet"></a>NAT-regel op een firewall naar ACI-subnet configureren

Maak een [NAT-regel](../firewall/rule-processing.md) op de firewall voor het vertalen en filteren van inkomend Internet verkeer naar de toepassings container die u eerder in het netwerk hebt gestart. Zie [Inkomend Internet verkeer filteren met Azure firewall DNAT](../firewall/tutorial-firewall-dnat.md) voor meer informatie

Maak een NAT-regel en-verzameling met behulp van de opdracht [AZ Network Firewall NAT-Rule Create][az-network-firewall-nat-rule-create] :

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

Voeg indien nodig NAT-regels toe om verkeer te filteren op andere IP-adressen in het subnet. Zo kunnen andere container groepen in het subnet IP-adressen voor inkomend verkeer openbaren of kunnen andere interne IP-adressen worden toegewezen aan de container groep nadat de computer opnieuw is opgestart.

### <a name="create-outbound-application-rule-on-the-firewall"></a>Regel voor uitgaande toepassing maken op de firewall

Voer de volgende [AZ Network Firewall Application-Rule Create][az-network-firewall-application-rule-create] opdracht uit om een uitgaande regel te maken op de firewall. Met deze voorbeeld regel krijgt toegang vanaf het subnet gedelegeerd aan Azure Container Instances naar de FQDN `checkip.dyndns.org` . HTTP-toegang tot de site wordt in een latere stap gebruikt om het uitgaande IP-adres te bevestigen van Azure Container Instances.

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

## <a name="test-container-group-access-through-the-firewall"></a>Toegang tot container groepen testen via de firewall

In de volgende secties wordt gecontroleerd of het subnet dat is overgedragen aan Azure Container Instances, juist is geconfigureerd achter de Azure-firewall. Met de vorige stappen wordt zowel binnenkomend verkeer naar het subnet als het uitgaande verkeer van het subnet via de firewall gerouteerd.

### <a name="test-ingress-to-a-container-group"></a>Ingress testen naar een container groep

Test de binnenkomende toegang tot de *appcontainer* die in het virtuele netwerk wordt uitgevoerd door naar het open bare IP-adres van de firewall te bladeren. U hebt eerder het open bare IP-adres opgeslagen in de variabele $FW _PUBLIC_IP:

```bash
echo $FW_PUBLIC_IP
```

De uitvoer ziet er ongeveer zo uit:

```console
52.142.18.133
```

Als de NAT-regel op de firewall juist is geconfigureerd, ziet u het volgende wanneer u het open bare IP-adres van de firewall in uw browser invoert:

:::image type="content" source="media/container-instances-egress-ip-address/aci-ingress-ip-address.png" alt-text="Blader naar het open bare IP-adres van de firewall":::

### <a name="test-egress-from-a-container-group"></a>Test uitvoer van een container groep


Implementeer de volgende voorbeeld container in het virtuele netwerk. Wanneer de service wordt uitgevoerd, wordt er één HTTP-aanvraag verzonden naar `http://checkip.dyndns.org` , die het IP-adres van de afzender (het uitgaande IP-adres) wordt weer gegeven. Als de toepassings regel op de firewall juist is geconfigureerd, wordt het open bare IP-adres van de firewall geretourneerd.

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

Raadpleeg de container Logboeken om te bevestigen dat het IP-adres hetzelfde is als het open bare IP-adres van de firewall.

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

In dit artikel kunt u container groepen instellen in een virtueel netwerk achter een Azure-firewall. U hebt een door de gebruiker gedefinieerde route-en NAT-en toepassings regels geconfigureerd op de firewall. Door deze configuratie te gebruiken, stelt u één statisch IP-adres in voor binnenkomend en uitgaand verkeer van Azure Container Instances.

Zie de [Azure firewall](../firewall/index.yml) -documentatie voor meer informatie over het beheren van verkeer en het beveiligen van Azure-resources.



[az-group-create]: /cli/azure/group#az-group-create
[az-container-create]: /cli/azure/container#az-container-create
[az-network-vnet-subnet-create]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-create
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-network-firewall-update]: /cli/azure/ext/azure-firewall/network/firewall#ext-azure-firewall-az-network-firewall-update
[az-network-public-ip-show]: /cli/azure/network/public-ip/#az-network-public-ip-show
[az-network-route-table-create]:/cli/azure/network/route-table/#az-network-route-table-create
[az-network-route-table-route-create]: /cli/azure/network/route-table/route#az-network-route-table-route-create
[az-network-firewall-ip-config-list]: /cli/azure/ext/azure-firewall/network/firewall/ip-config#ext-azure-firewall-az-network-firewall-ip-config-list
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet#az-network-vnet-subnet-update
[az-container-exec]: /cli/azure/container#az-container-exec
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-vm-open-port]: /cli/azure/vm#az-vm-open-port
[az-vm-list-ip-addresses]: /cli/azure/vm#az-vm-list-ip-addresses
[az-network-firewall-application-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/application-rule#ext-azure-firewall-az-network-firewall-application-rule-create
[az-network-firewall-nat-rule-create]: /cli/azure/ext/azure-firewall/network/firewall/nat-rule#ext-azure-firewall-az-network-firewall-nat-rule-create






