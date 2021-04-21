---
title: Podverkeer beveiligen met netwerkbeleid
titleSuffix: Azure Kubernetes Service
description: Meer informatie over het beveiligen van verkeer dat in en uit pods stroomt met behulp van Kubernetes-netwerkbeleid in Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/16/2021
ms.openlocfilehash: b05c4add0a62f07b187376d670f23179ba97f3a8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767435"
---
# <a name="secure-traffic-between-pods-using-network-policies-in-azure-kubernetes-service-aks"></a>Verkeer tussen pods beveiligen met behulp van netwerkbeleid in Azure Kubernetes Service (AKS)

Wanneer u moderne, op microservices gebaseerde toepassingen in Kubernetes gebruikt, wilt u vaak bepalen welke onderdelen met elkaar kunnen communiceren. Het principe van de minste bevoegdheden moet worden toegepast op de manier waarop verkeer tussen pods in een Azure Kubernetes Service (AKS)-cluster kan stromen. Stel dat u verkeer rechtstreeks naar back-endtoepassingen wilt blokkeren. Met *de functie Netwerkbeleid* in Kubernetes kunt u regels definiëren voor in- en uitverkeer tussen pods in een cluster.

In dit artikel wordt beschreven hoe u de engine voor netwerkbeleid installeert en Kubernetes-netwerkbeleid maakt om de verkeersstroom tussen pods in AKS te controleren. Netwerkbeleid mag alleen worden gebruikt voor Linux-knooppunten en -pods in AKS.

## <a name="before-you-begin"></a>Voordat u begint

Azure CLI versie 2.0.61 of hoger moet zijn geïnstalleerd en geconfigureerd. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren][install-azure-cli] als u de CLI wilt installeren of een upgrade wilt uitvoeren.

> [!TIP]
> Als u de functie voor netwerkbeleid tijdens de preview hebt gebruikt, raden we u aan [een nieuw cluster te maken.](#create-an-aks-cluster-and-enable-network-policy)
> 
> Als u bestaande testclusters wilt blijven gebruiken die netwerkbeleid tijdens de preview-versie hebben gebruikt, upgradet u uw cluster naar een nieuwe Kubernetes-versies voor de nieuwste GA-release en implementeert u vervolgens het volgende YAML-manifest om de crashende metrische gegevensserver en het Kubernetes-dashboard op te lossen. Deze oplossing is alleen vereist voor clusters die de Network Policy Engine van Nuco gebruiken.
>
> Bekijk als best practice de inhoud van dit [YAML-manifest][calico-aks-cleanup] om te begrijpen wat er in het AKS-cluster is geïmplementeerd.
>
> `kubectl delete -f https://raw.githubusercontent.com/Azure/aks-engine/master/docs/topics/calico-3.3.1-cleanup-after-upgrade.yaml`

## <a name="overview-of-network-policy"></a>Overzicht van netwerkbeleid

Alle pods in een AKS-cluster kunnen standaard zonder beperkingen verkeer verzenden en ontvangen. Om de beveiliging te verbeteren, kunt u regels definiëren die de verkeersstroom bepalen. Back-endtoepassingen worden bijvoorbeeld vaak alleen blootgesteld aan de vereiste front-endservices. Of databaseonderdelen zijn alleen toegankelijk voor de toepassingslagen die er verbinding mee maken.

Netwerkbeleid is een Kubernetes-specificatie die toegangsbeleid voor communicatie tussen pods definieert. Met behulp van netwerkbeleid definieert u een geordende set regels voor het verzenden en ontvangen van verkeer en past u deze toe op een verzameling pods die overeenkomen met een of meer label selectors.

Deze netwerkbeleidsregels worden gedefinieerd als YAML-manifesten. Netwerkbeleid kan worden opgenomen als onderdeel van een breder manifest dat ook een implementatie of service maakt.

### <a name="network-policy-options-in-aks"></a>Opties voor netwerkbeleid in AKS

Azure biedt twee manieren om netwerkbeleid te implementeren. U kiest een netwerkbeleidsoptie wanneer u een AKS-cluster maakt. De beleidsoptie kan niet worden gewijzigd nadat het cluster is gemaakt:

* De eigen implementatie van Azure, *azure-netwerkbeleid genoemd.*
* *Kalico-netwerkbeleid,* een opensource-netwerk- en netwerkbeveiligingsoplossing die is gebouwd [door Tigera][tigera].

Beide implementaties gebruiken Linux *IPTables om* het opgegeven beleid af te dwingen. Beleidsregels worden omgezet in sets toegestane en niet-toegestane IP-paren. Deze paren worden vervolgens geprogrammeerd als IPTable-filterregels.

### <a name="differences-between-azure-and-calico-policies-and-their-capabilities"></a>Verschillen tussen Azure- en Kalico-beleid en hun mogelijkheden

| Mogelijkheid                               | Azure                      | Calico                      |
|------------------------------------------|----------------------------|-----------------------------|
| Ondersteunde platforms                      | Linux                      | Linux, Windows Server 2019 (preview)  |
| Ondersteunde netwerkopties             | Azure CNI                  | Azure CNI (Windows Server 2019 en Linux) en kubenet (Linux)  |
| Naleving van Kubernetes-specificatie | Alle ondersteunde beleidstypen |  Alle ondersteunde beleidstypen |
| Aanvullende functies                      | Geen                       | Uitgebreid beleidsmodel dat bestaat uit Global Network Policy, Global Network Set en Host Endpoint. Zie voor meer informatie over het gebruik van de CLI voor het beheren van deze uitgebreide functies, refer aan `calicoctl` [de gebruikers.][calicoctl] |
| Ondersteuning                                  | Ondersteund door ondersteuning voor Azure engineeringteam | Ondersteuning voor Deco-community. Zie Ondersteuningsopties voor Project Kalico voor meer informatie over [aanvullende betaalde ondersteuning.][calico-support] |
| Logboekregistratie                                  | Regels die zijn toegevoegd/verwijderd in IPTables, worden op elke host geregistreerd onder */var/log/azure-npm.log* | Zie Logboeken van [Het Onderdeel van Kalico voor meer informatie][calico-logs] |

## <a name="create-an-aks-cluster-and-enable-network-policy"></a>Een AKS-cluster maken en netwerkbeleid inschakelen

Als u netwerkbeleid in actie wilt zien, gaan we een beleid maken en vervolgens uitbreiden dat de verkeersstroom definieert:

* Al het verkeer naar de pod weigeren.
* Sta verkeer toe op basis van podlabels.
* Sta verkeer toe op basis van naamruimte.

Eerst maken we een AKS-cluster dat ondersteuning biedt voor netwerkbeleid.

> [!IMPORTANT]
>
> De functie voor netwerkbeleid kan alleen worden ingeschakeld wanneer het cluster wordt gemaakt. U kunt netwerkbeleid niet inschakelen op een bestaand AKS-cluster.

Als u Azure Network Policy wilt gebruiken, moet u de [Azure CNI en][azure-cni] uw eigen virtuele netwerk en subnetten definiëren. Zie Geavanceerde netwerken configureren voor meer informatie over het plannen van de vereiste [subnetbereiken.][use-advanced-networking] Het Netwerkbeleid voor Het netwerk kan worden gebruikt met dezelfde Azure CNI-in plug-in of met de Kubenet CNI-in plug-in.

Het volgende voorbeeldscript:

* Hiermee maakt u een virtueel netwerk en subnet.
* Hiermee maakt u Azure Active Directory service-principal (Azure AD) voor gebruik met het AKS-cluster.
* Wijst *inzendermachtigingen* toe voor de AKS-clusterservice-principal in het virtuele netwerk.
* Hiermee maakt u een AKS-cluster in het gedefinieerde virtuele netwerk en schakelt u netwerkbeleid in.
    * De _optie Azure-netwerkbeleid_ wordt gebruikt. Als u in plaats daarvan Wilt gebruiken Als u Wilt gebruiken als de optie voor netwerkbeleid, gebruikt u de `--network-policy calico` parameter . Opmerking: Kalico kan worden gebruikt met `--network-plugin azure` of `--network-plugin kubenet` .

In plaats van een service-principal te gebruiken, kunt u een beheerde identiteit gebruiken voor machtigingen. Zie [Beheerde identiteiten gebruiken](use-managed-identity.md) voor meer informatie.

Geef uw eigen beveiligde *SP_PASSWORD*. U kunt de *RESOURCE_GROUP_NAME* en *CLUSTER_NAME* vervangen:

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

### <a name="create-an-aks-cluster-for-azure-network-policies"></a>Een AKS-cluster maken voor Azure-netwerkbeleid

Maak het AKS-cluster en geef het virtuele netwerk, de informatie over de service-principal en *Azure op* voor de netwerkinvoeging en het netwerkbeleid.

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

Het duurt een paar minuten om het cluster te maken. Wanneer het cluster klaar is, configureert u om verbinding te maken met uw Kubernetes-cluster met behulp van de `kubectl` [opdracht az aks get-credentials.][az-aks-get-credentials] Met deze opdracht worden referenties gedownload en wordt de Kubernetes CLI geconfigureerd voor het gebruik ervan:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

### <a name="create-an-aks-cluster-for-calico-network-policies"></a>Een AKS-cluster maken voor Het netwerkbeleid van Kalico

Maak het AKS-cluster en geef het virtuele netwerk, de informatie over de service-principal, *azure* voor de netwerkinvoegvoegsel en *de code voor* het netwerkbeleid op. Door *gebruik te maken van kalico* als netwerkbeleid, kunt u Met Behulp van Netwerkco netwerken op zowel Linux- als Windows-knooppuntgroepen.

Als u van plan bent Windows-knooppuntgroepen toe te voegen aan uw cluster, moet u de parameters en opnemen met die voldoen aan `windows-admin-username` `windows-admin-password` de [wachtwoordvereisten voor Windows Server.][windows-server-password] Als u Wilt Werkenco met Windows-knooppuntgroepen wilt gebruiken, moet u ook de `Microsoft.ContainerService/EnableAKSWindowsCalico` registreren.

Registreer de `EnableAKSWindowsCalico` functievlag met behulp van [de opdracht az feature register,][az-feature-register] zoals wordt weergegeven in het volgende voorbeeld:

```azurecli-interactive
az feature register --namespace "Microsoft.ContainerService" --name "EnableAKSWindowsCalico"
```

 U kunt de registratiestatus controleren met behulp van de opdracht [az feature list][az-feature-list]:

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/EnableAKSWindowsCalico')].{Name:name,State:properties.state}"
```

Wanneer u klaar bent, vernieuwt u de registratie van de resourceprovider *Microsoft.ContainerService* met behulp van [de opdracht az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

> [!IMPORTANT]
> Op dit moment is het gebruik van Het gebruik van Het Netwerkbeleid met Windows-knooppunten beschikbaar op nieuwe clusters met Kubernetes versie 1.20 of hoger met Versie 3.17.2 en vereist het gebruik van Azure CNI-netwerken. Voor Windows-knooppunten op AKS-clusters met Kalico is [ook Direct Server Return (DSR)][dsr] standaard ingeschakeld.
>
> Voor clusters met alleen Linux-knooppuntgroepen met Kubernetes 1.20 met eerdere versies van Kalico wordt de Versie van Nuco automatisch bijgewerkt naar 3.17.2.

Het beleid voor netwerknetwerken met Windows-knooppunten is momenteel beschikbaar als preview-versie.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

Maak een gebruikersnaam om te gebruiken als beheerdersreferenties voor uw Windows Server-containers in uw cluster. De volgende opdrachten vragen u om een gebruikersnaam en stellen deze in WINDOWS_USERNAME voor gebruik in een latere opdracht (onthoud dat de opdrachten in dit artikel worden ingevoerd in een BASH-shell).

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

Het duurt een paar minuten om het cluster te maken. Uw cluster wordt standaard alleen gemaakt met een Linux-knooppuntgroep. Als u Windows-knooppuntgroepen wilt gebruiken, kunt u er een toevoegen. Bijvoorbeeld:

```azurecli
az aks nodepool add \
    --resource-group $RESOURCE_GROUP_NAME \
    --cluster-name $CLUSTER_NAME \
    --os-type Windows \
    --name npwin \
    --node-count 1
```

Wanneer het cluster klaar is, configureert u om verbinding te maken met uw Kubernetes-cluster met behulp van de `kubectl` [opdracht az aks get-credentials.][az-aks-get-credentials] Met deze opdracht worden referenties gedownload en de Kubernetes CLI geconfigureerd voor het gebruik ervan:

```azurecli-interactive
az aks get-credentials --resource-group $RESOURCE_GROUP_NAME --name $CLUSTER_NAME
```

## <a name="deny-all-inbound-traffic-to-a-pod"></a>Al het binnenkomende verkeer naar een pod weigeren

Voordat u regels definieert om specifiek netwerkverkeer toe te staan, moet u eerst een netwerkbeleid maken om al het verkeer te weigeren. Dit beleid geeft u een beginpunt om te beginnen met het maken van een allowlist voor alleen het gewenste verkeer. U kunt ook duidelijk zien dat verkeer wordt weggevallen wanneer het netwerkbeleid wordt toegepast.

Voor de omgevings- en verkeersregels van de voorbeeldtoepassing maken we eerst een naamruimte met de naam *ontwikkeling om* de voorbeeldpods uit te voeren:

```console
kubectl create namespace development
kubectl label namespace/development purpose=development
```

Maak een voorbeeld van een back-endpod met NGINX. Deze back-endpod kan worden gebruikt om een voorbeeld van een webtoepassing in de back-end te simuleren. Maak deze pod in de *naamruimte* development en open poort *80 om* webverkeer te bedienen. Label de pod *met app=webapp,role=backend,* zodat we deze in de volgende sectie met een netwerkbeleid kunnen richten:

```console
kubectl run backend --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --labels app=webapp,role=backend --namespace development --expose --port 80
```

Maak nog een pod en koppel een terminalsessie om te testen of u de standaard NGINX-webpagina kunt bereiken:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Gebruik bij de shell-prompt om te bevestigen dat u toegang hebt tot de standaard `wget` NGINX-webpagina:

```console
wget -qO- http://backend
```

In de volgende voorbeelduitvoer ziet u dat de standaard NGINX-webpagina is geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

### <a name="create-and-apply-a-network-policy"></a>Een netwerkbeleid maken en toepassen

Nu u hebt bevestigd dat u de eenvoudige NGINX-webpagina op de voorbeeld-back-endpod kunt gebruiken, maakt u een netwerkbeleid om al het verkeer te weigeren. Maak een bestand met de `backend-policy.yaml` naam en plak het volgende YAML-manifest. In dit manifest wordt een *podSelector* gebruikt om het beleid te koppelen aan pods met het label *app:webapp,role:backend,* zoals uw voorbeeld-NGINX-pod. Er zijn geen regels gedefinieerd onder *inkomende*, zodat al het binnenkomende verkeer naar de pod wordt geweigerd:

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

Ga naar [https://shell.azure.com](https://shell.azure.com) om de Azure Cloud Shell openen in uw browser.

Pas het netwerkbeleid toe met behulp van de [opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-network-policy"></a>Het netwerkbeleid testen

Laten we eens kijken of u de NGINX-webpagina in de back-endpod opnieuw kunt gebruiken. Maak nog een testpod en koppel een terminalsessie:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Gebruik bij de shell-prompt om `wget` te zien of u toegang hebt tot de standaard NGINX-webpagina. Stel deze keer een time-outwaarde in op *2* seconden. Het netwerkbeleid blokkeert nu al het inkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weergegeven in het volgende voorbeeld:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de gekoppelde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-inbound-traffic-based-on-a-pod-label"></a>Inkomende verkeer toestaan op basis van een podlabel

In de vorige sectie is een back-end NGINX-pod gepland en is een netwerkbeleid gemaakt om al het verkeer te weigeren. We gaan een front-endpod maken en het netwerkbeleid bijwerken om verkeer van front-endpods toe te staan.

Werk het netwerkbeleid bij om verkeer van pods toe te staan met de labels *app:webapp,role:frontend* en in een naamruimte. Bewerk het vorige *backend-policy.yaml-bestand* en voeg regels voor het *binnenkomtagress matchLabels* toe, zodat uw manifest eruitziet als in het volgende voorbeeld:

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
> Dit netwerkbeleid maakt gebruik van *een naamruimteSelector* en een *podSelector-element* voor de regel voor ingress. De YAML-syntaxis is belangrijk om de regels voor ingress additief te maken. In dit voorbeeld moeten beide elementen overeenkomen om de regel voor ingress toe te passen. Kubernetes-versies vóór *1.12* interpreteren deze elementen mogelijk niet correct en beperken het netwerkverkeer zoals verwacht. Zie Gedrag van naar en van selectors voor meer informatie [over dit gedrag.][policy-rules]

Pas het bijgewerkte netwerkbeleid toe met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

Plan een pod met het label *app=webapp,role=frontend* en koppel een terminalsessie:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Gebruik bij de shell-prompt om `wget` te zien of u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend
```

Omdat de toegangsregel verkeer toestaat met pods die de *labels-app hebben: webapp,rol:* front-end, is het verkeer van de front-endpod toegestaan. In de volgende voorbeelduitvoer ziet u de standaard NGINX-webpagina die wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminalsessie af. De pod wordt automatisch verwijderd.

```console
exit
```

### <a name="test-a-pod-without-a-matching-label"></a>Een pod testen zonder een overeenkomend label

Het netwerkbeleid staat verkeer toe van pods met het label *app: webapp,rol: front-end,* maar moet al het andere verkeer weigeren. We gaan testen of een andere pod zonder deze labels toegang heeft tot de back-end NGINX-pod. Maak nog een testpod en koppel een terminalsessie:

```console
kubectl run --rm -it --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 network-policy --namespace development
```

Gebruik bij de shell-prompt om te zien of u toegang hebt tot de `wget` standaard NGINX-webpagina. Het netwerkbeleid blokkeert het binnenkomende verkeer, zodat de pagina niet kan worden geladen, zoals wordt weergegeven in het volgende voorbeeld:

```console
wget -qO- --timeout=2 http://backend
```

```output
wget: download timed out
```

Sluit de gekoppelde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="allow-traffic-only-from-within-a-defined-namespace"></a>Verkeer alleen toestaan vanuit een gedefinieerde naamruimte

In de vorige voorbeelden hebt u een netwerkbeleid gemaakt dat al het verkeer heeft geweigerd en vervolgens het beleid bijgewerkt om verkeer van pods met een specifiek label toe te staan. Een andere veelvoorkomende behoefte is om verkeer te beperken tot alleen binnen een bepaalde naamruimte. Als de vorige voorbeelden voor  verkeer in een ontwikkelingsnaamruimte waren, maakt u een netwerkbeleid dat voorkomt dat verkeer van een andere naamruimte, zoals *productie,* de pods bereikt.

Maak eerst een nieuwe naamruimte om een productienaamruimte te simuleren:

```console
kubectl create namespace production
kubectl label namespace/production purpose=production
```

Plan een testpod in de *productienaamruimte* die is gelabeld als *app=webapp,role=front-end.* Een terminalsessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Gebruik bij de shell-prompt om `wget` te bevestigen dat u toegang hebt tot de standaard NGINX-webpagina:

```console
wget -qO- http://backend.development
```

Omdat de labels voor de pod overeenkomen met wat momenteel is toegestaan in het netwerkbeleid, is het verkeer toegestaan. Het netwerkbeleid kijkt niet naar de naamruimten, alleen naar de podlabels. In de volgende voorbeelduitvoer ziet u de standaard NGINX-webpagina die wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

### <a name="update-the-network-policy"></a>Het netwerkbeleid bijwerken

We gaan de sectie naamruimte van de toegangsregel *Bijwerken,* zodat alleen verkeer vanuit de *ontwikkelingsnaamruimte* wordt toegestaan. Bewerk het *manifestbestand backend-policy.yaml* zoals wordt weergegeven in het volgende voorbeeld:

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

In complexere voorbeelden kunt u meerdere regels voor ingress definiëren, zoals een *naamruimteSelector* en vervolgens een *podSelector*.

Pas het bijgewerkte netwerkbeleid toe met behulp van [de opdracht kubectl apply][kubectl-apply] en geef de naam van uw YAML-manifest op:

```console
kubectl apply -f backend-policy.yaml
```

### <a name="test-the-updated-network-policy"></a>Het bijgewerkte netwerkbeleid testen

Plan nog een pod in de *productienaamruimte* en koppel een terminalsessie:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace production
```

Gebruik bij de shell-prompt `wget` om te zien dat het netwerkbeleid verkeer nu weert:

```console
wget -qO- --timeout=2 http://backend.development
```

```output
wget: download timed out
```

Sluit de testpod af:

```console
exit
```

Als het verkeer uit de *productienaamruimte* wordt  geweigerd, moet u een testpod weer plannen in de ontwikkelingsnaamruimte en een terminalsessie koppelen:

```console
kubectl run --rm -it frontend --image=mcr.microsoft.com/aks/fundamental/base-ubuntu:v0.0.11 --labels app=webapp,role=frontend --namespace development
```

Gebruik bij de shell-prompt om `wget` te zien of het netwerkbeleid het verkeer toestaat:

```console
wget -qO- http://backend
```

Verkeer is toegestaan omdat de pod is gepland in de naamruimte die overeenkomt met wat is toegestaan in het netwerkbeleid. In de volgende voorbeelduitvoer ziet u de standaard NGINX-webpagina die wordt geretourneerd:

```output
<!DOCTYPE html>
<html>
<head>
<title>Welcome to nginx!</title>
[...]
```

Sluit de gekoppelde terminalsessie af. De testpod wordt automatisch verwijderd.

```console
exit
```

## <a name="clean-up-resources"></a>Resources opschonen

In dit artikel hebben we twee naamruimten gemaakt en een netwerkbeleid toegepast. Als u deze resources wilt ops schonen, gebruikt u [de opdracht kubectl delete][kubectl-delete] en geeft u de resourcenamen op:

```console
kubectl delete namespace production
kubectl delete namespace development
```

## <a name="next-steps"></a>Volgende stappen

Zie Netwerkconcepten voor toepassingen in Azure Kubernetes Service (AKS) voor meer informatie over [netwerkresources.][concepts-network]

Zie [Kubernetes-netwerkbeleid][kubernetes-network-policies]voor meer informatie over beleidsregels.

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[concepts-network]: concepts-network.md
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[windows-server-password]: /windows/security/threat-protection/security-policy-settings/password-must-meet-complexity-requirements#reference
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[dsr]: ../load-balancer/load-balancer-multivip-overview.md#rule-type-2-backend-port-reuse-by-using-floating-ip