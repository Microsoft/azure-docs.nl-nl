---
title: Persoonlijke koppeling instellen
description: Stel een persoonlijk eind punt in op een container register en Schakel toegang in via een persoonlijke koppeling in een lokaal virtueel netwerk. Toegang voor persoonlijke koppelingen is een functie van de laag Premium-Service.
ms.topic: article
ms.date: 10/01/2020
ms.openlocfilehash: 3193c65a2021d29f03bd9ae6cbc00fd6c349d9bf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93342297"
---
# <a name="connect-privately-to-an-azure-container-registry-using-azure-private-link"></a>Persoonlijke verbinding maken met een Azure container Registry met behulp van een persoonlijke Azure-koppeling


Beperk de toegang tot een REGI ster door particuliere IP-adressen voor het virtuele netwerk toe te wijzen aan de register eindpunten en met behulp van een [persoonlijke Azure-koppeling](../private-link/private-link-overview.md) Netwerk verkeer tussen de clients in het virtuele netwerk en de persoonlijke eind punten van het REGI ster passeren het virtuele netwerk en een privé-koppeling in het micro soft-backbone-netwerk, waardoor de bloot stelling van het open bare Internet wordt voor komen. Met persoonlijke koppeling kunt u ook persoonlijke toegang tot het REGI ster vanuit on-premises via [Azure ExpressRoute](../expressroute/expressroute-introduction.MD) private peering of een [VPN-gateway](../vpn-gateway/vpn-gateway-about-vpngateways.md).

U kunt [DNS-instellingen configureren](../private-link/private-endpoint-overview.md#dns-configuration) voor de privé-eind punten van het REGI ster, zodat de instellingen kunnen worden omgezet in het toegewezen privé IP-adres van het REGI ster. Met DNS-configuratie kunnen clients en services in het netwerk het REGI ster blijven gebruiken op het Fully Qualified Domain Name van het REGI ster, zoals *myregistry.azurecr.io*. 

Deze functie is beschikbaar in de service tier van het **Premium** -container register. Momenteel kunnen Maxi maal tien privé-eind punten worden ingesteld voor een REGI ster. Zie [Azure container Registry-lagen](container-registry-skus.md)voor meer informatie over de service lagen en limieten voor het REGI ster.

[!INCLUDE [container-registry-scanning-limitation](../../includes/container-registry-scanning-limitation.md)]

## <a name="prerequisites"></a>Vereisten

* Als u de stappen van Azure CLI in dit artikel wilt gebruiken, wordt Azure CLI-versie 2.6.0 of hoger aanbevolen. Zie [Azure CLI installeren][azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren. Of voer uit in [Azure Cloud shell](../cloud-shell/quickstart.md).
* Als u nog geen container register hebt, maakt u er een (vereist een Premium-laag) en [importeert](container-registry-import-images.md) u een voor beeld van een open bare installatie kopie, zoals `mcr.microsoft.com/hello-world` van micro soft container Registry. Gebruik bijvoorbeeld de [Azure Portal][quickstart-portal] of de [Azure cli][quickstart-cli] om een REGI ster te maken.
* Als u toegang tot het REGI ster wilt configureren met behulp van een persoonlijke koppeling in een ander Azure-abonnement, moet u de resource provider registreren voor Azure Container Registry in dat abonnement. Bijvoorbeeld:

  ```azurecli
  az account set --subscription <Name or ID of subscription of private link>

  az provider register --namespace Microsoft.ContainerRegistry
  ``` 

De Azure CLI-voor beelden in dit artikel gebruiken de volgende omgevings variabelen. Vervang de waarden die van toepassing zijn voor uw omgeving. Alle voor beelden zijn opgemaakt voor de bash-shell:

```bash
REGISTRY_NAME=<container-registry-name>
REGISTRY_LOCATION=<container-registry-location> # Azure region such as westeurope where registry created
RESOURCE_GROUP=<resource-group-name>
VM_NAME=<virtual-machine-name>
```

[!INCLUDE [Set up Docker-enabled VM](../../includes/container-registry-docker-vm-setup.md)]

## <a name="set-up-private-link---cli"></a>Privé-koppeling instellen-CLI

### <a name="get-network-and-subnet-names"></a>Netwerk-en subnet namen ophalen

Als u deze nog niet hebt, hebt u de namen van een virtueel netwerk en subnet nodig om een persoonlijke koppeling in te stellen. In dit voor beeld gebruikt u hetzelfde subnet voor de virtuele machine en het persoonlijke eind punt van het REGI ster. In veel gevallen zou u het eind punt echter instellen in een afzonderlijk subnet. 

Wanneer u een virtuele machine maakt, maakt Azure standaard een virtueel netwerk in dezelfde resource groep. De naam van het virtuele netwerk is gebaseerd op de naam van de virtuele machine. Als u bijvoorbeeld een *myDockerVM* naam voor de virtuele machine hebt, is de standaard naam van het virtuele netwerk *myDockerVMVNET*, met een subnet met de naam *myDockerVMSubnet*. Stel deze waarden in omgevings variabelen in door de opdracht [AZ Network vnet List][az-network-vnet-list] uit te voeren:

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

### <a name="disable-network-policies-in-subnet"></a>Netwerk beleid in subnet uitschakelen

[Netwerk beleidsregels](../private-link/disable-private-endpoint-network-policy.md) , zoals netwerk beveiligings groepen, uitschakelen in het subnet voor het persoonlijke eind punt. De configuratie van uw subnet bijwerken met [AZ Network vnet subnet update][az-network-vnet-subnet-update]:

```azurecli
az network vnet subnet update \
 --name $SUBNET_NAME \
 --vnet-name $NETWORK_NAME \
 --resource-group $RESOURCE_GROUP \
 --disable-private-endpoint-network-policies
```

### <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren

Maak een [privé-DNS-zone](../dns/private-dns-privatednszone.md) voor het persoonlijke Azure-container register domein. In latere stappen maakt u DNS-records voor uw register domein in deze DNS-zone.

Als u een privé zone wilt gebruiken om de standaard DNS-omzetting voor uw Azure container Registry te overschrijven, moet de zone de naam **privatelink.azurecr.io**. Voer de volgende [AZ Network private-DNS zone Create][az-network-private-dns-zone-create] opdracht uit om de privé zone te maken:

```azurecli
az network private-dns zone create \
  --resource-group $RESOURCE_GROUP \
  --name "privatelink.azurecr.io"
```

### <a name="create-an-association-link"></a>Een koppelings koppeling maken

Voer [AZ Network private-DNS link vnet maken][az-network-private-dns-link-vnet-create] uit om uw persoonlijke zone te koppelen aan het virtuele netwerk. In dit voor beeld wordt een koppeling gemaakt met de naam *myDNSLink*.

```azurecli
az network private-dns link vnet create \
  --resource-group $RESOURCE_GROUP \
  --zone-name "privatelink.azurecr.io" \
  --name MyDNSLink \
  --virtual-network $NETWORK_NAME \
  --registration-enabled false
```

### <a name="create-a-private-registry-endpoint"></a>Een persoonlijk REGI ster-eind punt maken

In deze sectie maakt u het persoonlijke eind punt van het REGI ster in het virtuele netwerk. Haal eerst de resource-ID van het REGI ster op:

```azurecli
REGISTRY_ID=$(az acr show --name $REGISTRY_NAME \
  --query 'id' --output tsv)
```

Voer de opdracht [AZ Network private-endpoint Create][az-network-private-endpoint-create] uit om het persoonlijke eind punt van het REGI ster te maken.

In het volgende voor beeld wordt het eind punt *myPrivateEndpoint* en de *myConnection* voor de service verbinding gemaakt. Als u een container register bron voor het eind punt wilt opgeven, geeft u het `--group-ids registry` volgende door:

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

### <a name="get-private-ip-addresses"></a>Privé-IP-adressen ophalen

Voer [AZ Network private-endpoint show][az-network-private-endpoint-show] uit om een query uit te voeren op het eind punt voor de netwerk interface-id:

```azurecli
NETWORK_INTERFACE_ID=$(az network private-endpoint show \
  --name myPrivateEndpoint \
  --resource-group $RESOURCE_GROUP \
  --query 'networkInterfaces[0].id' \
  --output tsv)
```

Gekoppeld aan de netwerk interface in dit voor beeld zijn twee privé-IP-adressen voor het container register: een voor het REGI ster zelf en een voor het gegevens eindpunt van het REGI ster. Met de volgende opdracht [AZ resource show][az-resource-show] worden de privé-IP-adressen voor het container register en het gegevens eindpunt van het REGI ster opgehaald:

```azurecli
PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[1].properties.privateIPAddress' \
  --output tsv)

DATA_ENDPOINT_PRIVATE_IP=$(az resource show \
  --ids $NETWORK_INTERFACE_ID \
  --api-version 2019-04-01 \
  --query 'properties.ipConfigurations[0].properties.privateIPAddress' \
  --output tsv)
```

> [!NOTE]
> Als uw REGI ster [geo-gerepliceerd](container-registry-geo-replication.md)is, moet u een query uitvoeren voor het extra gegevens eindpunt voor elke register replica.

### <a name="create-dns-records-in-the-private-zone"></a>DNS-records in de privé zone maken

Met de volgende opdrachten maakt u DNS-records in de privé zone voor het eind punt van het REGI ster en het bijbehorende gegevens eindpunt. Als u bijvoorbeeld een REGI ster met de naam *myregistry* in de *Europa West* -regio hebt, zijn de namen van het eind punt `myregistry.azurecr.io` en `myregistry.westeurope.data.azurecr.io` . 

> [!NOTE]
> Als uw REGI ster [geo-gerepliceerd](container-registry-geo-replication.md)is, maakt u extra DNS-records voor het IP-adres van het gegevens eindpunt van de replica.

Eerst uitvoeren [AZ Network private-DNS record: Stel een Create][az-network-private-dns-record-set-a-create] in voor het maken van lege a-record sets voor het REGI ster-eind punt en het gegevens eindpunt:

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

Voer de opdracht [AZ Network private-DNS record-set a add-record][az-network-private-dns-record-set-a-add-record] uit om de a-records voor het REGI ster-eind punt en gegevens eindpunt te maken:

```azurecli
az network private-dns record-set a add-record \
  --record-set-name $REGISTRY_NAME \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $PRIVATE_IP

# Specify registry region in data endpoint name
az network private-dns record-set a add-record \
  --record-set-name ${REGISTRY_NAME}.${REGISTRY_LOCATION}.data \
  --zone-name privatelink.azurecr.io \
  --resource-group $RESOURCE_GROUP \
  --ipv4-address $DATA_ENDPOINT_PRIVATE_IP
```

De persoonlijke koppeling is nu geconfigureerd en gereed voor gebruik.

## <a name="set-up-private-link---portal"></a>Een persoonlijke koppeling instellen-Portal

Stel een persoonlijke koppeling in wanneer u een REGI ster maakt of Voeg een persoonlijke koppeling toe aan een bestaand REGI ster. Bij de volgende stappen wordt ervan uitgegaan dat u al een virtueel netwerk en een subnet hebt ingesteld met een VM om te testen. U kunt ook [een nieuw virtueel netwerk en subnet maken](../virtual-network/quick-create-portal.md).

### <a name="create-a-private-endpoint---new-registry"></a>Een persoonlijk eind punt maken-nieuw REGI ster

1. Wanneer u een REGI ster maakt in de portal, selecteert u op het tabblad **basis beginselen** in **SKU** de optie **Premium**.
1. Selecteer het tabblad **Netwerken**.
1. Selecteer in **netwerk connectiviteit** **persoonlijke eind punt**  >  **+ toevoegen**.
1. Voer de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    | Abonnement | Selecteer uw abonnement. |
    | Resourcegroep | Voer de naam van een bestaande groep in of maak een nieuwe.|
    | Name | Voer een unieke naam in. |
    | Subbron |**REGI ster** selecteren|
    | **Netwerken** | |
    | Virtueel netwerk| Selecteer het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, zoals *myDockerVMVNET*. |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waarop uw virtuele machine is geïmplementeerd. |
    |**Integratie van Privé-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(nieuw) privatelink.azurecr.io* |
    |||
1. Configureer de resterende register instellingen en selecteer vervolgens **controleren + maken**.

  ![REGI ster maken met een persoonlijk eind punt](./media/container-registry-private-link/private-link-create-portal.png)

### <a name="create-a-private-endpoint---existing-registry"></a>Een persoonlijk eind punt maken-bestaand REGI ster

1. Navigeer in de portal naar het container register.
1. Selecteer onder **instellingen** de optie **netwerken**.
1. Selecteer op het tabblad **privé-eind punten** **+ persoonlijk eind punt**.
1. Voer op het tabblad **basis beginselen** de volgende informatie in of Selecteer deze:

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
6. Voer de volgende informatie in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |Verbindingsmethode  | Selecteer **Verbinding maken met een Azure-resource in mijn directory**.|
    | Abonnement| Selecteer uw abonnement. |
    | Resourcetype | Selecteer **micro soft. ContainerRegistry/registers**. |
    | Resource |De naam van het REGI ster selecteren|
    |Subresource van doel |**REGI ster** selecteren|
    |||
7. Selecteer **Volgende: Configuratie**.
8. Voer de volgende gegevens in of Selecteer deze:

    | Instelling | Waarde |
    | ------- | ----- |
    |**Netwerken**| |
    | Virtueel netwerk| Selecteer het virtuele netwerk waarin uw virtuele machine is geïmplementeerd, zoals *myDockerVMVNET*. |
    | Subnet | Selecteer een subnet, zoals *myDockerVMSubnet* waarop uw virtuele machine is geïmplementeerd. |
    |**Integratie van Privé-DNS**||
    |Integreren met privé-DNS-zone |Selecteer **Ja**. |
    |Privé-DNS-zone |Selecteer *(nieuw) privatelink.azurecr.io* |
    |||

1. Selecteer **Controleren + maken**. De pagina **Beoordelen en maken** wordt weergegeven, waar uw configuratie wordt gevalideerd in Azure. 
2. Als u het bericht **Validatie geslaagd** ziet, selecteert u **Maken**.

Nadat het persoonlijke eind punt is gemaakt, worden de DNS-instellingen in de privé zone weer gegeven op de pagina **privé-eind punten** in de portal:

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Selecteer op het tabblad **privé-eind punten** het persoonlijke eind punt dat u hebt gemaakt.
1. Bekijk de koppelings instellingen en aangepaste DNS-instellingen op de pagina **overzicht** .

  ![DNS-instellingen voor het eind punt](./media/container-registry-private-link/private-endpoint-overview.png)

Uw persoonlijke koppeling is nu geconfigureerd en gereed voor gebruik.

## <a name="disable-public-access"></a>Open bare toegang uitschakelen

Voor veel scenario's schakelt u de toegang tot het REGI ster vanuit open bare netwerken uit. Deze configuratie voor komt dat clients buiten het virtuele netwerk de register eindpunten kunnen bereiken. 

### <a name="disable-public-access---cli"></a>Open bare toegang uitschakelen-CLI

Als u open bare toegang wilt uitschakelen met behulp van de Azure CLI, voert u [AZ ACR update][az-acr-update] uit en stelt `--public-network-enabled` u in op `false` . 

> [!NOTE]
> Het `public-network-enabled` argument vereist Azure CLI 2.6.0 of hoger. 

```azurecli
az acr update --name $REGISTRY_NAME --public-network-enabled false
```


### <a name="disable-public-access---portal"></a>Open bare toegang uitschakelen-Portal

1. Ga in de portal naar het container register en selecteer **instellingen > netwerk**.
1. Selecteer op het tabblad **open bare toegang** in **open bare netwerk toegang toestaan** de optie **uitgeschakeld**. Selecteer vervolgens **Opslaan**.

## <a name="validate-private-link-connection"></a>Verbinding met persoonlijke koppeling valideren

U moet controleren of de resources binnen het subnet van het particuliere eind punt verbinding maken met uw REGI ster via een particulier IP-adres en de juiste persoonlijke DNS-zone integratie hebben.

Als u de verbinding met de persoonlijke koppeling wilt valideren, moet u SSH naar de virtuele machine die u in het virtuele netwerk hebt ingesteld.

Voer een hulp programma uit, zoals `nslookup` of `dig` om het IP-adres van het REGI ster via de persoonlijke koppeling op te zoeken. Bijvoorbeeld:

```bash
dig $REGISTRY_NAME.azurecr.io
```

Voorbeeld uitvoer toont het IP-adres van het REGI ster in de adres ruimte van het subnet:

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

Vergelijk dit resultaat met het open bare IP-adres in `dig` uitvoer voor hetzelfde REGI ster via een openbaar eind punt:

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

### <a name="registry-operations-over-private-link"></a>Register bewerkingen via een persoonlijke koppeling

Controleer ook of u register bewerkingen kunt uitvoeren vanaf de virtuele machine in het subnet. Maak een SSH-verbinding met uw virtuele machine en voer [AZ ACR login][az-acr-login] uit om u aan te melden bij het REGI ster. Afhankelijk van uw VM-configuratie moet u mogelijk de volgende opdrachten voor voegsel met behulp van `sudo` .

```bash
az acr login --name $REGISTRY_NAME
```

Voer register bewerkingen uit, zoals `docker pull` het ophalen van een voorbeeld installatie kopie uit het REGI ster. Vervang door `hello-world:v1` een afbeelding en code die geschikt is voor uw REGI ster, voorafgegaan door de naam van de aanmeldings server van het REGI ster (alle kleine letters):

```bash
docker pull myregistry.azurecr.io/hello-world:v1
``` 

Docker haalt de installatie kopie op naar de virtuele machine.

## <a name="manage-private-endpoint-connections"></a>Verbindingen met privé-eind punten beheren

Beheer van de privé-eind punten van een REGI ster met de Azure Portal, of met behulp van opdrachten in de opdracht groep [AZ ACR private-endpoint-Connection][az-acr-private-endpoint-connection] . Bewerkingen omvatten goed keuren, verwijderen, weer geven, afwijzen of Details van de particuliere eindpunt verbindingen van een REGI ster weer gegeven.

Als u bijvoorbeeld de verbindingen van het privé-eind punt van een REGI ster wilt weer geven, voert u de opdracht [AZ ACR private-endpoint-Connection List][az-acr-private-endpoint-connection-list] uit. Bijvoorbeeld:

```azurecli
az acr private-endpoint-connection list \
  --registry-name $REGISTRY_NAME 
```

Wanneer u een verbinding met een privé-eind punt instelt met behulp van de stappen in dit artikel, accepteert het REGI ster automatisch verbindingen van clients en services met Azure RBAC-machtigingen voor het REGI ster. U kunt het eind punt zo instellen dat hand matige goed keuring van verbindingen is vereist. Zie [een particuliere-eindpunt verbinding beheren](../private-link/manage-private-endpoint.md)voor meer informatie over het goed keuren en afwijzen van privé-eindpunt verbindingen.

## <a name="add-zone-records-for-replicas"></a>Zone records voor replica's toevoegen

Als u in dit artikel een verbinding met een privé-eind punt aan een REGI ster toevoegt, maakt u DNS-records in de `privatelink.azurecr.io` zone voor het REGI ster en de bijbehorende gegevens eindpunten in de regio's waarnaar het REGI ster wordt [gerepliceerd](container-registry-geo-replication.md). 

Als u later een nieuwe replica toevoegt, moet u hand matig een nieuwe zone record toevoegen voor het eind punt van de gegevens in die regio. Als u bijvoorbeeld een replica van *myregistry* op de locatie *northeurope* maakt, voegt u een zone record toe voor `myregistry.northeurope.data.azurecr.io` . Zie [DNS-records maken in de privé zone](#create-dns-records-in-the-private-zone) in dit artikel voor instructies.

## <a name="dns-configuration-options"></a>Opties voor DNS-configuratie

Het persoonlijke eind punt in dit voor beeld is geïntegreerd met een privé-DNS-zone die is gekoppeld aan een basis-virtueel netwerk. Deze instelling maakt gebruik van de door Azure verschafte DNS-service om de open bare FQDN van het REGI ster te herstellen naar het privé-IP-adres in het virtuele netwerk. 

Persoonlijke koppeling ondersteunt extra DNS-configuratie scenario's die gebruikmaken van de privé zone, met inbegrip van aangepaste DNS-oplossingen. Stel dat u een aangepaste DNS-oplossing hebt geïmplementeerd in het virtuele netwerk, of on-premises in een netwerk waarmee u verbinding maakt met het virtuele netwerk met behulp van een VPN-gateway of Azure ExpressRoute. 

Als u in deze scenario's de open bare FQDN van het REGI ster wilt omzetten naar het privé-IP-adres, moet u een doorstuur server voor de Azure DNS-service configureren (168.63.129.16). De exacte configuratie opties en stappen zijn afhankelijk van uw bestaande netwerken en DNS. Zie voor beelden [Azure private endpoint DNS-configuratie](../private-link/private-endpoint-dns.md).

> [!IMPORTANT]
> Als voor hoge Beschik baarheid die u hebt gemaakt in meerdere regio's, wordt u aangeraden een afzonderlijke resource groep te gebruiken in elke regio en het virtuele netwerk en de bijbehorende privé-DNS-zone te plaatsen. Deze configuratie voor komt ook dat onvoorspelbare DNS-omzetting wordt veroorzaakt door het delen van dezelfde privé-DNS-zone.

## <a name="clean-up-resources"></a>Resources opschonen

Als u alle Azure-resources in dezelfde resource groep hebt gemaakt en deze niet meer nodig hebt, kunt u de resources eventueel verwijderen met behulp van één opdracht [AZ Group delete](/cli/azure/group) :

```azurecli
az group delete --name $RESOURCE_GROUP
```

Als u uw resources in de portal wilt opschonen, gaat u naar de resource groep. Zodra de resource groep is geladen, klikt u op **resource groep verwijderen** om de resource groep en de resources die daar zijn opgeslagen, te verwijderen.

## <a name="next-steps"></a>Volgende stappen

* Zie de documentatie van [Azure private link](../private-link/private-link-overview.md) voor meer informatie over persoonlijke koppelingen.
* Zie [regels configureren voor toegang tot een Azure container Registry achter een firewall](container-registry-firewall-access-rules.md)als u toegangs regels voor het REGI ster wilt instellen van achter een firewall van een client.

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
[az-resource-show]: /cli/azure/resource#az-resource-show
[quickstart-portal]: container-registry-get-started-portal.md
[quickstart-cli]: container-registry-get-started-azure-cli.md
[azure-portal]: https://portal.azure.com
