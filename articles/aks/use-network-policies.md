---
title: Pod-verkeer beveiligen met netwerk beleid
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het beveiligen van verkeer dat in en uit het meren aantal stromen met behulp van Kubernetes-netwerk beleid in azure Kubernetes service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: 17e14859ecdfe11872d5b0526d755d01bc1b034a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104577849"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Verkeer beveiligen tussen een Peul netwerk beleid dat wordt gebruikt in azure Kubernetes service (AKS)

Wanneer u moderne, op micro Services gebaseerde toepassingen uitvoert in Kubernetes, wilt u vaak bepalen welke onderdelen met elkaar kunnen communiceren. Het principe van minimale bevoegdheden moet worden toegepast op de manier waarop verkeer tussen de peulen in een Azure Kubernetes service (AKS)-cluster kan stromen. Stel dat u het verkeer waarschijnlijk rechtstreeks wilt blok keren naar back-end-toepassingen. Met de functie *netwerk beleid* in Kubernetes kunt u regels definiëren voor binnenkomend en uitgaand verkeer tussen peulen in een cluster.

In dit artikel wordt beschreven hoe u de Network Policy Engine installeert en hoe u Kubernetes-netwerk beleid maakt om de stroom van verkeer tussen peul in AKS te beheren. Netwerk beleid mag alleen worden gebruikt voor Linux-knoop punten en een Peul in AKS.

## <a name="before-you-begin"></a>Voordat u begint

U moet de Azure CLI-versie 2.0.61 of hoger hebben geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!TIP]
> Als u de functie netwerk beleid hebt gebruikt tijdens de preview-versie, wordt u aangeraden [een nieuw cluster te maken](#create-an-aks-cluster-and-enable-network-policy).
> 
> Als u bestaande test clusters wilt blijven gebruiken die tijdens de preview worden gebruikt, voert u een upgrade van uw cluster uit naar een nieuwe Kubernetes-versie voor de nieuwste GA-versie en implementeert u vervolgens het volgende YAML-manifest om de gegevens van de Crashing-server en het Kubernetes-dash board te herstellen. Deze oplossing is alleen vereist voor clusters die de Calico-netwerk beleid-engine gebruiken.
>
> [Lees de inhoud van dit yaml-manifest][calico-aks-cleanup] als beveiligings best practice om te begrijpen wat er in het AKS-cluster is geïmplementeerd.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Overzicht van netwerk beleid

Alle peulen in een AKS-cluster kunnen standaard zonder beperkingen verkeer verzenden en ontvangen. Om de beveiliging te verbeteren, kunt u regels definiëren die de stroom van verkeer regelen. Back-end-toepassingen worden vaak alleen blootgesteld aan de vereiste front-end-services, bijvoorbeeld. Of database onderdelen zijn alleen toegankelijk voor de toepassings lagen waarmee ze verbinding maken.

Netwerk beleid is een Kubernetes-specificatie waarmee het toegangs beleid voor communicatie tussen het gehele element wordt gedefinieerd. Met netwerk beleid definieert u een geordende set regels voor het verzenden en ontvangen van verkeer en past u deze toe op een verzameling van peulen die overeenkomen met een of meer label-selectors.

Deze regels voor netwerk beleid worden gedefinieerd als YAML-manifesten. Netwerk beleid kan worden opgenomen als onderdeel van een breder manifest dat ook een implementatie of service maakt.

### <a name="network-policy-options-in-aks"></a>Opties voor netwerk beleid in AKS

Azure biedt twee manieren om netwerk beleid te implementeren. U kiest een optie voor netwerk beleid wanneer u een AKS-cluster maakt. De beleids optie kan niet worden gewijzigd nadat het cluster is gemaakt:

* De eigen implementatie van Azure, het *Azure-netwerk beleid* genoemd.
* *Calico network policies*, een open-source netwerk-en netwerk beveiligings oplossing die is gegrondvest door [tigera][tigera].

Beide implementaties gebruiken Linux *iptables* om het opgegeven beleid af te dwingen. Beleids regels worden omgezet in sets toegestane en niet-toegestane IP-paren. Deze paren worden vervolgens geprogrammeerd als IPTable-filter regels.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Verschillen tussen Azure-en Calico-beleid en hun mogelijkheden

| Mogelijkheid                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Ondersteunde platforms                      | Linux                      | Linux, Windows Server 2019 (preview-versie)  |
| Ondersteunde netwerk opties             | Azure-CNI                  | Azure CNI (Windows Server 2019 en Linux) en kubenet (Linux)  |
| Compatibiliteit met Kubernetes-specificatie | Alle beleids typen die worden ondersteund |  Alle beleids typen die worden ondersteund |
| Aanvullende functies                      | Geen                       | Uitgebreide beleids model dat bestaat uit globaal netwerk beleid, een globaal netwerk en een host-eind punt. `calicoctl`Zie [calicoctl User Reference][calicoctl](Engelstalig) voor meer informatie over het gebruik van de CLI voor het beheren van deze uitgebreide functies. |
| Ondersteuning                                  | Ondersteund door ondersteunings-en technisch team van Azure | Ondersteuning voor Calico-community. Zie voor meer informatie over aanvullende betaalde ondersteuning [project Calico-ondersteunings opties][calico-support]. |
| Logboekregistratie                                  | Regels die zijn toegevoegd aan of verwijderd uit IPTables worden geregistreerd op elke host onder */var/log/Azure-NPM.log* | Zie [Calico-onderdeel Logboeken][calico-logs] voor meer informatie. |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Een AKS-cluster maken en netwerk beleid inschakelen

Voor een overzicht van het netwerk beleid in actie, gaan we maken en vervolgens uitvouwen op een beleid dat verkeers stroom definieert:

* Al het verkeer naar pod geweigerd.
* Verkeer op basis van pod-labels toestaan.
* Verkeer toestaan op basis van naam ruimte.

Eerst gaan we een AKS-cluster maken dat netwerk beleid ondersteunt.

> [!IMPORTANT]
>
> De functie netwerk beleid kan alleen worden ingeschakeld wanneer het cluster is gemaakt. U kunt netwerk beleid niet inschakelen op een bestaand AKS-cluster.

Als u Azure-netwerk beleid wilt gebruiken, moet u de [Azure cni-invoeg toepassing][azure-cni] gebruiken en uw eigen virtuele netwerken en subnetten definiëren. Zie [geavanceerde netwerken configureren][use-advanced-networking]voor meer gedetailleerde informatie over het plannen van de vereiste subnet bereiken. Calico-netwerk beleid kan worden gebruikt met dezelfde Azure CNI-invoeg toepassing of met de Kubenet CNI-invoeg toepassing.

Het volgende voorbeeld script:

* Hiermee maakt u een virtueel netwerk en subnet.
* Hiermee maakt u een service-principal voor Azure Active Directory (Azure AD) voor gebruik met het AKS-cluster.
* Hiermee wijst u *Inzender* machtigingen toe voor de AKS-Cluster service-principal in het virtuele netwerk.
* Hiermee maakt u een AKS-cluster in het gedefinieerde virtuele netwerk en schakelt u netwerk beleid in.
    * De optie _Azure-netwerk_ beleid wordt gebruikt. Als u in plaats daarvan Calico wilt gebruiken als de optie netwerk beleid, gebruikt u de `--network-policy calico` para meter. Opmerking: Calico kan worden gebruikt met ofwel `--network-plugin azure` of `--network-plugin kubenet` .

Houd er rekening mee dat u, in plaats van een Service-Principal, een beheerde identiteit voor machtigingen kunt gebruiken. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

Geef uw eigen beveiligde *SP_PASSWORD* op. U kunt de *RESOURCE_GROUP_NAME* en *CLUSTER_NAME* variabelen vervangen:

```azurecli-interactive
RESOURCE_GROUP_NAME=myResourceGroup-NP
CLUSTER_NAME=myAKSCluster
LOCATION=canadaeast

# Create a resource group
az group create --name $RESOURCE_GROUP_NAME --location $LOCATION

# Create a virtual network and subnet
az network vnet create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name myVnet \
    --address-prefixes 10.0.0.0/8 \
    --subnet-name myAKSSubnet \
    --subnet-prefix 10.240.0.0/16

# Create a service principal and read in the application ID
SP=$(az ad sp create-for-rbac --output json)
SP_ID=$(echo $SP | jq -r .appId)
SP_PASSWORD=$(echo $SP | jq -r .password)

# Wait 15 seconds to make sure that service principal has propagated
echo "Waiting for service principal to propagate..."
sleep 15

# Get the virtual network resource ID
VNET_ID=$(az network vnet show --resource-group $RESOURCE_GROUP_NAME --name myVnet --query id -o tsv)

# Assign the service principal Contributor permissions to the virtual network resource
az role assignment create --assignee $SP_ID --scope $VNET_ID --role Contributor

# Get the virtual network subnet resource ID
SUBNET_ID=$(az network vnet subnet show --resource-group $RESOURCE_GROUP_NAME --vnet-name myVnet --name myAKSSubnet --query id -o tsv)
```

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Een AKS-cluster maken voor Azure-netwerk beleid

Maak het AKS-cluster en geef het virtuele netwerk, de gegevens van de Service-Principal en *Azure* op voor de netwerk-plugin en het netwerk beleid.

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --network-plugin azure \
    --network-policy azure
```

Het duurt een paar minuten om het cluster te maken. Wanneer het cluster gereed is, configureert `kubectl` u om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . Met deze opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd voor het gebruik ervan:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Een AKS-cluster maken voor Calico-netwerk beleid

Maak het AKS-cluster en geef het virtuele netwerk, service-principalgegevens, *Azure* voor de netwerk-plugin en *Calico* voor het netwerk beleid op. Als u *Calico* gebruikt als netwerk beleid, is Calico-netwerken mogelijk in zowel Linux-als Windows-knooppunt groepen.

Als u van plan bent om Windows-knooppunt groepen aan uw cluster toe te voegen, moet u de `windows-admin-username` `windows-admin-password` para meters en toevoegen aan die voldoen aan de [vereisten voor Windows Server-wacht woorden][windows-server-password]. Als u Calico met Windows-knooppunt groepen wilt gebruiken, moet u ook de registreren `Microsoft.ContainerService/EnableAKSWindowsCalico` .

Registreer de `EnableAKSWindowsCalico` functie vlag met de opdracht [AZ feature REGI ster][az-feature-register] , zoals weer gegeven in het volgende voor beeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Als u klaar bent, vernieuwt u de registratie van de resource provider *micro soft. container service* met de opdracht [AZ provider REGI ster][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Op dit moment is het gebruik van Calico-netwerk beleid met Windows-knoop punten beschikbaar op nieuwe clusters met Kubernetes versie 1,20 of hoger met Calico 3.17.2 en is vereist dat Azure CNI-netwerken worden gebruikt. Windows-knoop punten op AKS-clusters met Calico ingeschakeld hebben ook [direct server Return (DSR)][dsr] ingeschakeld.
>
> Voor clusters met alleen Linux-knooppunt Pools met Kubernetes 1,20 met eerdere versies van Calico, wordt de Calico-versie automatisch bijgewerkt naar 3.17.2.

Er is momenteel een preview-versie van Calico-netwerk beleid met Windows-knoop punten.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Maak een gebruikers naam om te gebruiken als beheerders referenties voor uw Windows Server-containers in uw cluster. Met de volgende opdrachten wordt u naar een gebruikers naam gevraagd en deze WINDOWS_USERNAME instellen voor gebruik in een latere opdracht (Houd er rekening mee dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell).

```azurecli-interactive
echo "Please enter the username to use as administrator credentials for Windows Server containers on your cluster: " && read WINDOWS_USERNAME
```

```azurecli
az aks create \
    --resource-group $RESOURCE_GROUP_NAME \
    --name $CLUSTER_NAME \
    --node-count 1 \
    --generate-ssh-keys \
    --service-cidr 10.0.0.0/16 \
    --dns-service-ip 10.0.0.10 \
    --docker-bridge-address 172.17.0.1/16 \
    --vnet-subnet-id $SUBNET_ID \
    --service-principal $SP_ID \
    --client-secret $SP_PASSWORD \
    --windows-admin-username $WINDOWS_USERNAME \
    --vm-set-type VirtualMachineScaleSets \
    --kubernetes-version 1.20.2 \
    --network-plugin azure \
    --network-policy calico
```

Het duurt een paar minuten om het cluster te maken. Standaard wordt uw cluster gemaakt met alleen een Linux-knooppunt groep. Als u Windows-knooppunt groepen wilt gebruiken, kunt u er een toevoegen. Bijvoorbeeld:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Wanneer het cluster gereed is, configureert `kubectl` u om verbinding te maken met uw Kubernetes-cluster met behulp van de opdracht [AZ AKS Get-credentials][az-aks-get-credentials] . Met deze opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd voor het gebruik ervan:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Alle binnenkomend verkeer naar een pod weigeren

Voordat u regels definieert om specifiek netwerk verkeer toe te staan, moet u eerst een netwerk beleid maken om al het verkeer te weigeren. Dit beleid geeft u een start punt om te beginnen met het maken van een allowlist voor alleen het gewenste verkeer. U kunt ook duidelijk zien dat verkeer wordt verwijderd wanneer het netwerk beleid wordt toegepast.

Voor de voor beelden van toepassings omgeving en verkeers regels maakt u eerst een naam ruimte met de naam *ontwikkeling* om het voor beeld uit te voeren:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Maak een voor beeld van een back-end-pod die NGINX uitvoert. Deze back-end pod kan worden gebruikt voor het simuleren van een voor beeld van een back-end-webtoepassing. Maak deze pod in de *ontwikkelings* naam ruimte en open poort *80* voor webverkeer. Voorzie de Pod met *app = webapp, Role = back-end* zodat we deze in de volgende sectie met een netwerk beleid kunnen richten:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Maak een andere pod en koppel een terminal sessie om te testen of u de standaard NGINX-webpagina kunt bereiken:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Gebruik bij de shell-prompt `wget` om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

In de volgende voorbeeld uitvoer ziet u dat de standaard NGINX-webpagina wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Een netwerk beleid maken en Toep assen

Nu u hebt bevestigd, kunt u de NGINX-webpagina van het voor beeld van de back-end pod gebruiken, een netwerk beleid maken om al het verkeer te weigeren. Maak een bestand `backend-policy.yaml` met de naam en plak het volgende YAML-manifest. Dit manifest maakt gebruik van een *podSelector* om het beleid te koppelen aan een Peul met de *app: webapp, Role: back-end* label, zoals uw voor beeld-NGINX pod. Er worden geen regels gedefinieerd bij inkomend *verkeer, waardoor* al het binnenkomende pod wordt geweigerd:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress: []
```

Ga naar [https://shell.azure.com](https://shell.azure.com) om Azure Cloud shell in uw browser te openen.

Pas het netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Het netwerk beleid testen

Laten we eens kijken of u de NGINX-webpagina op de back-end-pod opnieuw kunt gebruiken. Een andere test pod maken en een terminal sessie koppelen:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

In de shell-prompt kunt u gebruiken `wget` om te zien of u toegang hebt tot de standaard NGINX-webpagina. Stel deze keer een time-outwaarde in op *2* seconden. Het netwerk beleid blokkeert nu al het inkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weer gegeven in het volgende voor beeld:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Binnenkomend verkeer op basis van een pod label toestaan

In de vorige sectie is een back-end NGINX pod gepland en is er een netwerk beleid gemaakt om al het verkeer te weigeren. We gaan een front-end-pod maken en het netwerk beleid bijwerken zodat verkeer van de front-end kan worden toegestaan.

Werk het netwerk beleid bij om verkeer toe te staan van een Peul met de labels *app: webapp, Role: frontend* en in elke naam ruimte. Bewerk het vorige *back-yaml-* bestand en voeg *matchLabels* ingangs regels toe zodat uw manifest eruit ziet als in het volgende voor beeld:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector: {}
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

> [!NOTE]
> Dit netwerk beleid maakt gebruik van een *namespaceSelector* -en *podSelector* -element voor de ingangs regel. De syntaxis van de YAML is belang rijk voor de regels voor inkomend verkeer. In dit voor beeld moeten beide elementen overeenkomen voor de ingangs regel die moet worden toegepast. Kubernetes-versies vóór *1,12* kunnen deze elementen niet goed interpreteren en het netwerk verkeer beperken zoals verwacht. Zie [gedrag van en van selecters][policy-rules]voor meer informatie over dit gedrag.

Pas het bijgewerkte netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

Een pod plannen die als *app = webapp, Role = frontend* wordt aangeduid en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

In de shell-prompt kunt u gebruiken `wget` om te controleren of u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

Omdat de ingangs regel verkeer met de labels *app: webapp, Role: frontend* toestaat, is het verkeer van de front-end-pod toegestaan. In de volgende voorbeeld uitvoer ziet u de standaard NGINX webpagina die wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De Pod wordt automatisch verwijderd.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Een pod zonder een overeenkomend label testen

Het netwerk beleid staat verkeer toe van een Peul gelabelde *app: webapp, Role:* front-end, maar moet al het andere verkeer weigeren. Laten we eens kijken of een andere pod zonder die labels toegang hebben tot de back-end NGINX pod. Een andere test pod maken en een terminal sessie koppelen:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

In de shell-prompt kunt u gebruiken `wget` om te zien of u toegang hebt tot de standaard NGINX-webpagina. Het netwerk beleid blokkeert het inkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weer gegeven in het volgende voor beeld:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Alleen verkeer van binnen een gedefinieerde naam ruimte toestaan

In de vorige voor beelden hebt u een netwerk beleid gemaakt dat al het verkeer heeft geweigerd en vervolgens het beleid bijgewerkt zodat verkeer van Peul met een specifiek label wordt toegestaan. Een andere gang bare behoefte is om alleen verkeer binnen een bepaalde naam ruimte te beperken. Als de voor gaande voor beelden zijn voor verkeer in een *ontwikkelings* naam ruimte, moet u een netwerk beleid maken waarmee wordt voor komen dat verkeer van een andere naam ruimte, zoals *productie*, het gehele verschil bereikt.

Maak eerst een nieuwe naam ruimte voor het simuleren van een productie naam ruimte:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Een test pod plannen in de *productie* naam ruimte die is gelabeld als *app = webapp, Role = frontend*. Een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Gebruik bij de shell-prompt `wget` om te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend.development
```

Omdat de labels voor de pod overeenkomen met wat momenteel is toegestaan in het netwerk beleid, wordt het verkeer toegestaan. Het netwerk beleid bekijkt niet de naam ruimten, alleen de labels pod. In de volgende voorbeeld uitvoer ziet u de standaard NGINX webpagina die wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

### <a name="update-the-network-policy"></a>Het netwerk beleid bijwerken

Laten we de sectie *namespaceSelector* van de ingangs regel bijwerken zodat alleen verkeer binnen de *ontwikkelings* naam ruimte wordt toegestaan. Bewerk het manifest bestand *back-end-Policy. yaml* zoals wordt weer gegeven in het volgende voor beeld:

```yaml
kind: NetworkPolicy
apiVersion: networking.k8s.io/v1
metadata:
  name: backend-policy
  namespace: development
spec:
  podSelector:
    matchLabels:
      app: webapp
      role: backend
  ingress:
  - from:
    - namespaceSelector:
        matchLabels:
          purpose: development
      podSelector:
        matchLabels:
          app: webapp
          role: frontend
```

In complexere voor beelden kunt u meerdere insluitings regels definiëren, zoals een *namespaceSelector* en een *podSelector*.

Pas het bijgewerkte netwerk beleid toe met behulp van de opdracht [kubectl apply][kubectl-apply] en geef de naam van het yaml-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Het bijgewerkte netwerk beleid testen

Een andere pod plannen in de *productie* naam ruimte en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Gebruik bij de shell-prompt `wget` om te zien dat het netwerk beleid nu verkeer weigert:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Sluit de test pod af:

```console
exit
```

Wanneer het verkeer is geweigerd vanuit de *productie* naam ruimte, moet u een test pod weer plannen in de *ontwikkelings* naam ruimte en een terminal sessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Gebruik bij de shell-prompt `wget` om te zien dat het netwerk beleid het verkeer toestaat:

```console
wget -qO- http://backend
```

Verkeer is toegestaan omdat de pod in de naam ruimte is gepland die overeenkomt met wat is toegestaan in het netwerk beleid. In de volgende voorbeeld uitvoer ziet u de standaard NGINX opgehaalde webpagina:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminal sessie af. De test pod wordt automatisch verwijderd.

```console
exit
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebben we twee naam ruimten gemaakt en een netwerk beleid toegepast. Als u deze resources wilt opschonen, gebruikt u de opdracht [kubectl verwijderen][kubectl-delete] en geeft u de resource namen op:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Volgende stappen

Zie voor meer informatie over netwerk bronnen [netwerk concepten voor toepassingen in azure Kubernetes service (AKS)][concepts-network].

Zie [Kubernetes network policies][kubernetes-network-policies](Engelstalig) voor meer informatie over beleid.

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubernetes-network-policies]: https://kubernetes.io/docs/concepts/services-networking/network-policies/
[azure-cni]: https://github.com/Azure/azure-container-networking/blob/master/docs/cni.md
[policy-rules]: https://kubernetes.io/docs/concepts/services-networking/network-policies/#behavior-of-to-and-from-selectors
[aks-github]: https://github.com/azure/aks/issues
[tigera]: https://www.tigera.io/
[calicoctl]: https://docs.projectcalico.org/reference/calicoctl/
[calico-support]: https://www.tigera.io/tigera-products/calico/
[calico-logs]: https://docs.projectcalico.org/maintenance/troubleshoot/component-logs
[calico-aks-cleanup]: https://github.com/Azure/aks-engine/blob/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[use-advanced-networking]: configure-azure-cni.md
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip