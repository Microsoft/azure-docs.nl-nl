---
title: Privé-eindpunt instellen met private link
description: Stel een privé-eindpunt in een containerregister in en schakel toegang in via een privékoppeling in een lokaal virtueel netwerk. Toegang tot Private Link is een functie van de Premium-servicelaag.
ms.topic: article
ms.date: 03/31/2021
ms.openlocfilehash: c47eb535163a1a584bc3892da61543bdf2b0f798
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481409"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Privé verbinding maken met een Azure-containerregister met behulp van Azure Private Link


Beperk de toegang tot een register door privé-IP-adressen van virtuele netwerken toe te wijzen aan de register-eindpunten en gebruik [Azure Private Link](../private-link/private-link-overview.md). Netwerkverkeer tussen de clients op het virtuele netwerk en de privé-eindpunten van het register gaat via het virtuele netwerk en een privékoppeling op het Backbone-netwerk van Microsoft, waardoor de blootstelling van het openbare internet wordt voorkomen. Private Link maakt ook privéregistertoegang mogelijk vanaf on-premises via [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) persoonlijke peering of een [VPN-gateway.](../vpn-gateway/vpn-gateway-about-vpngateways.md)

U kunt [DNS-instellingen configureren](../private-link/private-endpoint-overview.md#dns-configuration) voor de privé-eindpunten van het register, zodat de instellingen worden opgelost naar het toegewezen privé-IP-adres van het register. Met DNS-configuratie kunnen clients en services in het netwerk toegang blijven krijgen tot het register op de fully qualified domain name van het register, *zoals myregistry.azurecr.io.* 

Deze functie is beschikbaar in de **servicelaag Premium** Container Registry. Op dit moment kunnen maximaal 10 privé-eindpunten worden ingesteld voor een register. Zie Azure Container Registry servicelagen voor meer [informatie over registerservicelagen en -limieten.](container-registry-skus.md)

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Vereisten

* Als u de Azure CLI-stappen in dit artikel wilt gebruiken, wordt Azure CLI versie 2.6.0 of hoger aanbevolen. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren. Of voer uit in [Azure Cloud Shell](../cloud-shell/quickstart.md).
* Als u nog geen containerregister hebt, maakt u er [](container-registry-import-images.md) een (Premium-laag vereist) en importeert u een voorbeeld van een openbare afbeelding, `mcr.microsoft.com/hello-world` zoals vanuit Microsoft Container Registry. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of [de Azure CLI om][quickstart-cli] een register te maken.
* Als u registertoegang wilt configureren met behulp van een privékoppeling in een ander Azure-abonnement, moet u de resourceprovider registreren voor Azure Container Registry in dat abonnement. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

In de Azure CLI-voorbeelden in dit artikel worden de volgende omgevingsvariabelen gebruikt. Vervang waarden die geschikt zijn voor uw omgeving. Alle voorbeelden zijn opgemaakt voor de Bash-shell:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Private Link instellen - CLI

### <a name="get-network-and-subnet-names"></a>Netwerk- en subnetnamen op halen

Als u deze nog niet hebt, hebt u de namen van een virtueel netwerk en subnet nodig om een privékoppeling in te stellen. In dit voorbeeld gebruikt u hetzelfde subnet voor de virtuele machine en het privé-eindpunt van het register. In veel scenario's stelt u het eindpunt echter in een afzonderlijk subnet in. 

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resourcegroep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld de virtuele machine *myDockerVM* noemt, is de standaardnaam van het virtuele netwerk *myDockerVMVNET,* met een subnet met de *naam myDockerVMSubnet.* Stel deze waarden in omgevingsvariabelen in door de [opdracht az network vnet list uit te][az-network-vnet-list] voeren:

```azurecli
NETWORK_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Name: name}' --output tsv)

SUBNET_NAME=$(az network vnet list \
  --resource-group $RESOURCE_GROUP \
  --query '[].{Subnet: subnets[0].name}' --output tsv)

echo NETWORK_NAME=$NETWORK_NAME
echo SUBNET_NAME=$SUBNET_NAME
```

### <a name="disable-network-policies-in-subnet"></a>Netwerkbeleid uitschakelen in subnet

[Schakel netwerkbeleidsregels](../private-link/disable-private-endpoint-network-policy.md) uit, zoals netwerkbeveiligingsgroepen in het subnet voor het privé-eindpunt. Werk uw subnetconfiguratie bij [met az network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren

Maak een [privé Azure DNS zone voor](../dns/private-dns-privatednszone.md) het privédomein van het Azure-containerregister. In latere stappen maakt u DNS-records voor uw registerdomein in deze DNS-zone. Zie DNS-configuratieopties [](#dns-configuration-options)verderop in dit artikel voor meer informatie.

Als u een privézone wilt gebruiken om de standaard-DNS-resolutie voor uw Azure-containerregister te overschrijven, moet de zone de **naam privatelink.azurecr.io**. Voer de volgende [opdracht az network private-dns zone create][az-network-private-dns-zone-create] uit om de privézone te maken:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Een koppelingskoppeling maken

Voer [az network private-dns link vnet create uit om][az-network-private-dns-link-vnet-create] uw privézone te koppelen aan het virtuele netwerk. In dit voorbeeld wordt een koppeling met de *naam myDNSLink gemaakt.*

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Een privéregister-eindpunt maken

In deze sectie maakt u het privé-eindpunt van het register in het virtuele netwerk. Haal eerst de resource-id van het register op:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Voer de [opdracht az network private-endpoint create][az-network-private-endpoint-create] uit om het privé-eindpunt van het register te maken.

In het volgende voorbeeld wordt het eindpunt *myPrivateEndpoint en* de serviceverbinding *myConnection gemaakt.* Als u een containerregisterresource voor het eindpunt wilt opgeven, geeft u `--group-ids registry` door:

```azurecli
az network private-endpoint create \
    --name myPrivateEndpoint \
    --resource-group $RESOURCE_GROUP \
    --vnet-name $NETWORK_NAME \
    --subnet $SUBNET_NAME \
    --private-connection-resource-id $REGISTRY_ID \
    --group-ids registry \
    --connection-name myConnection
```

### <a name="get-endpoint-ip-configuration"></a>IP-configuratie van eindpunten krijgen

Als u DNS-records wilt configureren, moet u de IP-configuratie van het privé-eindpunt op halen. Gekoppeld aan de netwerkinterface van het privé-eindpunt in dit voorbeeld zijn twee privé-IP-adressen voor het containerregister: één voor het register zelf en één voor het gegevens-eindpunt van het register. 

Voer eerst [az network private-endpoint show uit om een][az-network-private-endpoint-show] query uit te voeren op het privé-eindpunt voor de netwerkinterface-id:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Met de [volgende az network nic show-opdrachten][az-network-nic-show] worden de privé-IP-adressen voor het containerregister en het gegevens-eindpunt van het register op halen:

```azurecli
REGISTRY_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateIpAddress" \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateIpAddress" \
  --output tsv)

# An FQDN is associated with each IP address in the IP configurations

REGISTRY_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry'].privateLinkConnectionProperties.fqdns" \
  --output tsv)

DATA_ENDPOINT_FQDN=$(az network nic show \
  --ids $NETWORK_INTERFACE_ID \
  --query "ipConfigurations[?privateLinkConnectionProperties.requiredMemberName=='registry_data_$REGISTRY_LOCATION'].privateLinkConnectionProperties.fqdns" \
  --output tsv)
```

> [!NOTE]
> Als uw register [geo-gerepliceerd](container-registry-geo-replication.md)is, moet u een query uitvoeren voor het extra gegevens-eindpunt voor elke registerreplica.

### <a name="create-dns-records-in-the-private-zone"></a>DNS-records maken in de privézone

Met de volgende opdrachten maakt u DNS-records in de privézone voor het register-eindpunt en het gegevens-eindpunt. Als u bijvoorbeeld een register met de naam *myregistry* in de regio *westeurope* hebt, zijn de eindpuntnamen `myregistry.azurecr.io` en `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Als uw register [geo-gerepliceerd](container-registry-geo-replication.md)is, maakt u aanvullende DNS-records voor het IP-adres van het gegevens-eindpunt van elke replica.

Voer eerst [az network private-dns record-set a create][az-network-private-dns-record-set-a-create] uit om lege A-recordsets te maken voor het register-eindpunt en het gegevens-eindpunt:

```azurecli
az network private-dns record-set a create \
  --name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP

# Specify registry region in data endpoint name
az network private-dns record-set a create \
  --name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP
```

Voer de [opdracht az network private-dns record-set a add-record][az-network-private-dns-record-set-a-add-record] uit om de A-records voor het register-eindpunt en het gegevens-eindpunt te maken:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $REGISTRY_PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

De privékoppeling is nu geconfigureerd en klaar voor gebruik.

## <a name="set-up-private-link---portal"></a>Private Link instellen - portal

Stel een privékoppeling in wanneer u een register maakt of voeg een privékoppeling toe aan een bestaand register. In de volgende stappen wordt ervan uit gegaan dat u al een virtueel netwerk en subnet hebt ingesteld met een VM om te testen. U kunt ook [een nieuw virtueel netwerk en subnet maken.](../virtual-network/quick-create-portal.md)

### <a name="create-a-private-endpoint---new-registry"></a>Een privé-eindpunt maken - nieuw register

1. Wanneer u een register maakt in de portal, selecteert u op het tabblad **Basisbeginselen** in **SKU** de optie **Premium**.
1. Selecteer het tabblad **Netwerken**.
1. Selecteer **privé-eindpunt**+ Toevoegen **in**  >  **Netwerkverbinding.**
1. Voer de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Voer de naam van een bestaande groep in of maak een nieuwe.|
    | Name | Voer een unieke naam in. |
    | Subresource |Register **selecteren**|
    | **Netwerken** | |
    | Virtueel netwerk| Selecteer het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, zoals *myDockerVMVNET.* |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waar uw virtuele machine is geïmplementeerd. |
    |**Privé-DNS-integratie**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw) privatelink.azurecr.io* |
    |||
1. Configureer de resterende registerinstellingen en selecteer **controleren en maken.**

  ![Register met privé-eindpunt maken](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Een privé-eindpunt maken - bestaand register

1. Navigeer in de portal naar uw containerregister.
1. Selecteer **onder Instellingen** de optie **Netwerken.**
1. Selecteer op **het tabblad Privé-eindpunten** **de optie + Privé-eindpunt.**
1. Voer op **het** tabblad Basisinformatie de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | **Projectgegevens** | |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Voer de naam van een bestaande groep in of maak een nieuwe.|
    | **Exemplaardetails** |  |
    | Naam | Voer een naam in. |
    |Region|Selecteer een regio.|
    |||
5. Selecteer **Volgende: Resource**.
6. Voer de volgende gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **Verbinding maken met een Azure-resource in mijn directory**.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **Microsoft.ContainerRegistry/registries.** |
    | Resource |Selecteer de naam van het register|
    |Subresource van doel |Register **selecteren**|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Voer de gegevens in of selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, zoals *myDockerVMVNET.* |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waar uw virtuele machine wordt geïmplementeerd. |
    |**Privé-DNS-integratie**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(Nieuw) privatelink.azurecr.io* |
    |||

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 
2. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Nadat het privé-eindpunt is gemaakt, worden de DNS-instellingen in de privézone weergegeven op de pagina **Privé-eindpunten** in de portal:

1. Navigeer in de portal naar uw containerregister en selecteer **Instellingen > Netwerken.**
1. Selecteer op **het tabblad Privé-eindpunten** het privé-eindpunt dat u hebt gemaakt.
1. Controleer op **de pagina** Overzicht de koppelingsinstellingen en aangepaste DNS-instellingen.

  ![DNS-instellingen voor eindpunt](./media/container-registry-private-link/private-endpoint-overview.png)

Uw privékoppeling is nu geconfigureerd en klaar voor gebruik.

## <a name="disable-public-access"></a>Openbare toegang uitschakelen

Voor veel scenario's schakelt u registertoegang vanuit openbare netwerken uit. Deze configuratie voorkomt dat clients buiten het virtuele netwerk de register-eindpunten bereiken. 

### <a name="disable-public-access---cli"></a>Openbare toegang uitschakelen - CLI

Als u openbare toegang wilt uitschakelen met behulp van de Azure CLI, moet [u az acr update uitvoeren][az-acr-update] en instellen op `--public-network-enabled` `false` . 

> [!NOTE]
> Voor `public-network-enabled` het argument is Azure CLI 2.6.0 of hoger vereist. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Openbare toegang uitschakelen - portal

1. Navigeer in de portal naar het containerregister en selecteer **Instellingen > Netwerken.**
1. Selecteer op **het tabblad Openbare** toegang in Openbare **netwerktoegang toestaan** de optie **Uitgeschakeld.** Selecteer vervolgens **Opslaan**.

## <a name="validate-private-link-connection"></a>Private Link-verbinding valideren

Controleer of de resources in het subnet van het privé-eindpunt via een privé-IP-adres verbinding maken met uw register en de juiste integratie van de privé-DNS-zone hebben.

Als u de private link-verbinding wilt valideren, moet u SSH gebruiken voor de virtuele machine die u in het virtuele netwerk hebt ingesteld.

Voer een hulpprogramma zoals of uit om het IP-adres van uw register op te `nslookup` zoeken via de private `dig` link. Bijvoorbeeld:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Voorbeelduitvoer toont het IP-adres van het register in de adresruimte van het subnet:

```console
[...]
; <<>> DiG 9.11.3-1ubuntu1.13-Ubuntu <<>> myregistry.azurecr.io
;; global options: +cmd
;; Got answer:
;; ->>HEADER<<- opcode: QUERY, status: NOERROR, id: 52155
;; flags: qr rd ra; QUERY: 1, ANSWER: 2, AUTHORITY: 0, ADDITIONAL: 1

;; OPT PSEUDOSECTION:
; EDNS: version: 0, flags:; udp: 65494
;; QUESTION SECTION:
;myregistry.azurecr.io.         IN      A

;; ANSWER SECTION:
myregistry.azurecr.io.  1783    IN      CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 10 IN A      10.0.0.7

[...]
```

Vergelijk dit resultaat met het openbare IP-adres in `dig` de uitvoer voor hetzelfde register via een openbaar eindpunt:

```console
[...]
;; ANSWER SECTION:
myregistry.azurecr.io.  2881    IN  CNAME   myregistry.privatelink.azurecr.io.
myregistry.privatelink.azurecr.io. 2881 IN CNAME xxxx.xx.azcr.io.
xxxx.xx.azcr.io.    300 IN  CNAME   xxxx-xxx-reg.trafficmanager.net.
xxxx-xxx-reg.trafficmanager.net. 300 IN CNAME   xxxx.westeurope.cloudapp.azure.com.
xxxx.westeurope.cloudapp.azure.com. 10  IN A 20.45.122.144

[...]
```

### <a name="registry-operations-over-private-link"></a>Registerbewerkingen via private link

Controleer ook of u registerbewerkingen kunt uitvoeren vanaf de virtuele machine in het subnet. Maak een SSH-verbinding met uw virtuele machine en voer [az acr login uit om][az-acr-login] u aan te melden bij het register. Afhankelijk van uw VM-configuratie moet u mogelijk de volgende opdrachten vooraf laten gaan door `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Voer registerbewerkingen uit, zoals `docker pull` om een voorbeeldafbeelding uit het register op te halen. Vervang door een afbeelding en tag die geschikt zijn voor uw register, met het voorvoegsel de naam van de aanmeldingsserver voor het register `hello-world:v1` (in kleine letters):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de afbeelding op naar de VM.

## <a name="manage-private-endpoint-connections"></a>Privé-eindpuntverbindingen beheren

Beheer de privé-eindpuntverbindingen van een register met behulp van de Azure Portal of met behulp van opdrachten in de [opdrachtgroep az acr private-endpoint-connection.][az-acr-private-endpoint-connection] Bewerkingen omvatten het goedkeuren, verwijderen, opsnoemen, afwijzen of het tonen van details van de privé-eindpuntverbindingen van een register.

Voer bijvoorbeeld de opdracht [az acr private-endpoint-connection list][az-acr-private-endpoint-connection-list] uit om de privé-eindpuntverbindingen van een register weer te geven. Bijvoorbeeld:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Wanneer u met behulp van de stappen in dit artikel een verbinding met een privé-eindpunt in stelt, accepteert het register automatisch verbindingen van clients en services met Azure RBAC-machtigingen voor het register. U kunt het eindpunt zo instellen dat verbindingen handmatig moeten worden goedgekeurd. Zie Manage a Private Endpoint Connection (Een privé-eindpuntverbinding beheren) voor meer informatie over het goedkeuren en afwijzen van [privé-eindpuntverbindingen.](../private-link/manage-private-endpoint.md)

> [!IMPORTANT]
> Als u momenteel een privé-eindpunt uit een register verwijdert, moet u mogelijk ook de koppeling van het virtuele netwerk naar de privézone verwijderen. Als de koppeling niet is verwijderd, ziet u mogelijk een fout die vergelijkbaar is met `unresolvable host` .

## <a name="dns-configuration-options"></a>DNS-configuratieopties

Het privé-eindpunt in dit voorbeeld kan worden geïntegreerd met een privé-DNS-zone die is gekoppeld aan een virtueel basisnetwerk. Bij deze installatie wordt de door Azure geleverde DNS-service rechtstreeks gebruikt om de openbare FQDN van het register om te zetten in de privé-IP-adressen in het virtuele netwerk. 

Private Link ondersteunt aanvullende DNS-configuratiescenario's die gebruikmaken van de privézone, inclusief met aangepaste DNS-oplossingen. U kunt bijvoorbeeld een aangepaste DNS-oplossing hebben geïmplementeerd in het virtuele netwerk of on-premises in een netwerk dat u verbindt met het virtuele netwerk met behulp van een VPN-gateway of Azure ExpressRoute. 

Als u in deze scenario's de openbare FQDN van het register wilt oplossen naar het privé-IP-adres, moet u een doorsturende server naar de Azure DNS-service (168.63.129.16) configureren. De exacte configuratieopties en stappen zijn afhankelijk van uw bestaande netwerken en DNS. Zie Azure [Private Endpoint DNS configuration (DNS-configuratie voor Azure-privé-eindpunten) voor voorbeelden.](../private-link/private-endpoint-dns.md)

> [!IMPORTANT]
> Als u voor hoge beschikbaarheid privé-eindpunten in verschillende regio's hebt gemaakt, raden we u aan een afzonderlijke resourcegroep in elke regio te gebruiken en het virtuele netwerk en de bijbehorende privé-DNS-zone in deze regio te plaatsen. Deze configuratie voorkomt ook onvoorspelbare DNS-resolutie die wordt veroorzaakt door het delen van dezelfde privé-DNS-zone.

### <a name="manually-configure-dns-records"></a>DNS-records handmatig configureren

In sommige scenario's moet u mogelijk handmatig DNS-records configureren in een privézone in plaats van de door Azure geleverde privézone te gebruiken. Zorg ervoor dat u records maakt voor elk van de volgende eindpunten: het register-eindpunt, het gegevens-eindpunt van het register en het gegevens-eindpunt voor eventuele extra regionale replica's. Als niet alle records zijn geconfigureerd, is het register mogelijk niet bereikbaar.

> [!IMPORTANT]
> Als u later een nieuwe replica toevoegt, moet u handmatig een nieuwe DNS-record toevoegen voor het gegevens-eindpunt in die regio. Als u bijvoorbeeld een replica van *myregistry* maakt op de locatie northeurope, voegt u een record toe voor `myregistry.northeurope.data.azurecr.io` .

De FQDN's en privé-IP-adressen die u nodig hebt om DNS-records te maken, zijn gekoppeld aan de netwerkinterface van het privé-eindpunt. U kunt deze informatie verkrijgen met behulp van de Azure CLI of via de portal:

* Voer met behulp van de Azure CLI de [opdracht az network nic show][az-network-nic-show] uit. Zie get [endpoint IP configuration (EINDPUNT-IP-configuratie) eerder](#get-endpoint-ip-configuration)in dit artikel voor opdrachten.

* Navigeer in de portal naar uw privé-eindpunt en selecteer **DNS-configuratie.**

Nadat u DNS-records hebt gemaakt, moet u ervoor zorgen dat de FQDN's van het register correct worden opgelost naar hun respectieve privé-IP-adressen.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resourcegroep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met behulp van één [az group delete-opdracht:](/cli/azure/group)

```azurecli
az group delete --name $RESOURCE_GROUP
```

Als u uw resources in de portal wilt ops schonen, gaat u naar de resourcegroep. Zodra de resourcegroep is geladen, klikt u op **Resourcegroep verwijderen** om de resourcegroep en de resources die daar zijn opgeslagen te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie de Private Link voor meer informatie [over](../private-link/private-link-overview.md) Azure Private Link.

* Zie Regels configureren voor toegang tot een Azure-containerregister achter een firewall als u registertoegangsregels wilt [instellen achter een clientfirewall.](container-registry-firewall-access-rules.md)

* [Verbindingsproblemen met het Azure-privé-eindpunt oplossen](../private-link/troubleshoot-private-endpoint-connectivity.md)

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - Internal -->
[azure-cli]: /cli/azure/install-azure-cli
[az-acr-create]: /cli/azure/acr#az-acr-create
[az-acr-show]: /cli/azure/acr#az-acr-show
[az-acr-repository-show]: /cli/azure/acr/repository#az-acr-repository-show
[az-acr-repository-list]: /cli/azure/acr/repository#az-acr-repository-list
[az-acr-login]: /cli/azure/acr#az-acr-login
[az-acr-private-endpoint-connection]: /cli/azure/acr/private-endpoint-connection
[az-acr-private-endpoint-connection-list]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-list
[az-acr-private-endpoint-connection-approve]: /cli/azure/acr/private-endpoint-connection#az-acr-private-endpoint-connection-approve
[az-acr-update]: /cli/azure/acr#az-acr-update
[az-group-create]: /cli/azure/group
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-vm-create]: /cli/azure/vm#az-vm-create
[az-network-vnet-subnet-show]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-show
[az-network-vnet-subnet-update]: /cli/azure/network/vnet/subnet/#az-network-vnet-subnet-update
[az-network-vnet-list]: /cli/azure/network/vnet/#az-network-vnet-list
[az-network-private-endpoint-create]: /cli/azure/network/private-endpoint#az-network-private-endpoint-create
[az-network-private-endpoint-show]: /cli/azure/network/private-endpoint#az-network-private-endpoint-show
[az-network-private-dns-zone-create]: /cli/azure/network/private-dns/zone#az-network-private-dns-zone-create
[az-network-private-dns-link-vnet-create]: /cli/azure/network/private-dns/link/vnet#az-network-private-dns-link-vnet-create
[az-network-private-dns-record-set-a-create]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-create
[az-network-private-dns-record-set-a-add-record]: /cli/azure/network/private-dns/record-set/a#az-network-private-dns-record-set-a-add-record
[az-network-nic-show]: /cli/azure/network/nic#az-network-nic-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
