---
title: Beperk het verkeer dat wordt Azure Red Hat OpenShift een ARO-cluster (ARO)
description: Ontdek welke poorten en adressen vereist zijn voor het beheer van Azure Red Hat OpenShift (ARO)
author: sakthi-vetrivel
ms.author: jzim
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 04/09/2021
ms.openlocfilehash: 24c4686306aff9d84fe7bf74ddfdccff987244d9
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107368630"
---
# <a name="control-egress-traffic-for-your-azure-red-hat-openshift-aro-cluster-preview"></a>Beheer het verkeer voor het Azure Red Hat OpenShift (ARO) (preview)

Dit artikel bevat de benodigde gegevens waarmee u uitgaand verkeer van uw Azure Red Hat OpenShift cluster (ARO) kunt beveiligen. Het bevat de clustervereisten voor een eenvoudige ARO-implementatie en meer vereisten voor optionele Red Hat- en onderdelen van derden. Er [wordt](#private-aro-cluster-setup) een voorbeeld gegeven aan het einde van het configureren van deze vereisten met Azure Firewall. Houd er rekening mee dat u deze informatie kunt toepassen op Azure Firewall of op een uitgaande beperkingsmethode of elk apparaat.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgenomen dat u een nieuw cluster maakt. Als u een eenvoudig ARO-cluster nodig hebt, bekijkt u de [ARO-quickstart.](https://docs.microsoft.com/azure/openshift/tutorial-create-cluster)

> [!IMPORTANT]
> Preview-functies van ARO zijn beschikbaar via selfservice en opt-in. Previews worden aangeboden 'as is' en 'as available', en ze worden uitgesloten van de serviceovereenkomsten en beperkte garantie. ARO-previews worden gedeeltelijk gedekt door klantondersteuning op basis van best effort.

## <a name="minimum-required-fqdn--application-rules"></a>Minimaal vereiste FQDN-/toepassingsregels

Deze lijst is gebaseerd op de lijst met FQDN's in de OpenShift-documenten: https://docs.openshift.com/container-platform/4.6/installing/install_config/configuring-firewall.html

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-FQDN | Poort | Gebruik |
| ----------- | ----------- | ------------- |
| **`quay.io`** | **HTTPS:443** | Verplicht voor de installatie, gebruikt door het cluster. Dit wordt door het cluster gebruikt om de platformcontainer-afbeeldingen te downloaden. |
| **`registry.redhat.io`** | **HTTPS:443** | Verplicht voor kerninvoegtoepassingen. Dit wordt door het cluster gebruikt om kernonderdelen te downloaden, zoals dev-hulpprogramma's, op operator gebaseerde invoegtoepassingen en door Red Hat geleverde containerafbeeldingen.
| **`mirror.openshift.com`** | **HTTPS:443** | Dit is vereist in de VDI-omgeving of op uw laptop om toegang te krijgen tot gespiegelde installatie-inhoud en installatieafbeeldingen. Het is vereist in het cluster om handtekeningen voor platformversies te downloaden om te weten welke afbeeldingen moeten worden gehaald uit quay.io. |
| **`api.openshift.com`** | **HTTPS:443** | Vereist door het cluster om te controleren of er updates beschikbaar zijn voordat u de handtekeningen voor de afbeelding downloadt. |
| **`arosvc.azurecr.io`** | **HTTPS:443** | Intern privéregister voor ARO-operators.  Vereist als u de service-eindpunten Microsoft.ContainerRegistry niet toestaat op uw subnetten. |
| **`management.azure.com`** | **HTTPS:443** | Dit wordt door het cluster gebruikt voor toegang tot Azure-API's. |
| **`login.microsoftonline.com`** | **HTTPS:443** | Dit wordt door het cluster gebruikt voor verificatie bij Azure. |
| **`gcs.prod.monitoring.core.windows.net`** | **HTTPS:443** | Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken. |
| **`*.blob.core.windows.net`** | **HTTPS:443** | Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken. |
| **`*.servicebus.windows.net`** | **HTTPS:443** | Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken. |
| **`*.table.core.windows.net`** | **HTTPS:443** | Dit wordt gebruikt voor de bewaking van Microsoft Genève, zodat het ARO-team de cluster(s) van de klant kan bewaken. |

> [!NOTE] 
> Veel klanten die *.blob, *.table en andere grote adresruimten beschikbaar maken, maken zich zorgen over mogelijke gegevens exfiltratie. U kunt overwegen om de [OpenShift Egress Firewall](https://docs.openshift.com/container-platform/4.6/networking/openshift_sdn/configuring-egress-firewall.html) te gebruiken om te beveiligen dat toepassingen die in het cluster zijn geïmplementeerd, deze bestemmingen niet bereiken en om Azure Private Link te gebruiken voor specifieke toepassingsbehoeften.

---

## <a name="complete-list-of-required-and-optional-fqdns"></a>Volledige lijst met vereiste en optionele FQDN's

### <a name="first-group-installing-and-downloading-packages-and-tools"></a>EERSTE GROEP: PAKKETTEN EN HULPPROGRAMMA'S INSTALLEREN EN DOWNLOADEN

- **`quay.io`**: Verplicht voor de installatie, gebruikt door het cluster. Dit wordt door het cluster gebruikt om de platformcontainer-afbeeldingen te downloaden.
- **`registry.redhat.io`**: Verplicht voor kerninvoegtoepassingen. Dit wordt door het cluster gebruikt om kernonderdelen te downloaden, zoals hulpprogramma's voor ontwikkelen, op operator gebaseerde invoegtoepassingen of door Red Hat geleverde containerafbeeldingen, zoals onze middleware, de Universal Base Image...
- **`sso.redhat.com`**: deze is vereist in de VDI-omgeving of uw laptop om verbinding te maken met cloud.redhat.com. Dit is de site waar we het pull-geheim kunnen downloaden en enkele van de SaaS-oplossingen kunnen gebruiken die we in Red Hat aanbieden om onder andere uw abonnementen, clusterinventaris en terugboekingsrapportage te kunnen bewaken.
- **`openshift.org`**: deze is vereist in de VDI-omgeving of op uw laptop om verbinding te maken met het downloaden van RH CoreOS-afbeeldingen, maar in Azure worden ze uit de marketplace opgehaald. U hoeft geen besturingssysteemafbeeldingen te downloaden.

---

### <a name="second-group-telemetry"></a>TWEEDE GROEP: TELEMETRIE

Al deze sectie kan worden uitgedeeld, maar voordat we weten hoe het is, controleert u wat het is: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/about-remote-health-monitoring.html
- **`cert-api.access.redhat.com`**: Gebruik in uw VDI- of laptopomgeving.
- **`api.access.redhat.com`**: Gebruik in uw VDI- of laptopomgeving.
- **`infogw.api.openshift.com`**: Gebruik in uw VDI- of laptopomgeving.
- **`https://cloud.redhat.com/api/ingress`**: Gebruik in het cluster voor de insights-operator die integreert met de aaS Red Hat Insights.
In OpenShift Container Platform kunnen klanten zich af melden voor rapportage van status- en gebruiksgegevens. Met verbonden clusters kan Red Hat echter sneller reageren op problemen en onze klanten beter ondersteunen, en beter begrijpen hoe clusters voor productupgrades werken. Controleer hier de details: https://docs.openshift.com/container-platform/4.6/support/remote_health_monitoring/opting-out-of-remote-health-reporting.html .

---

### <a name="third-group-cloud-apis"></a>DERDE GROEP: CLOUD-API'S

- **`management.azure.com`**: Dit wordt door het cluster gebruikt voor toegang tot Azure-API's.

---

### <a name="fourth-group-other-openshift-requirements"></a>VIERDE GROEP: ANDERE OPENSHIFT-VEREISTEN

- **`mirror.openshift.com`**: deze is vereist in de VDI-omgeving of op uw laptop voor toegang tot gespiegelde installatie-inhoud en installatieafbeeldingen en is vereist in het cluster om handtekeningen voor platformversies te downloaden, die door het cluster worden gebruikt om te weten te komen welke afbeeldingen uit de quay.io.
- **`storage.googleapis.com/openshift-release`**: Alternatieve site voor het downloaden van handtekeningen voor de release van het platform, die door het cluster worden gebruikt om te weten welke afbeeldingen moeten worden gehaald uit quay.io.
- **`*.apps.<cluster_name>.<base_domain>`** (OF EQUIVALENTE ARO-URL): bij het toestaan van een lijst met domeinen wordt dit in uw bedrijfsnetwerk gebruikt om toepassingen te bereiken die zijn geïmplementeerd in OpenShift of om toegang te krijgen tot de OpenShift-console.
- **`api.openshift.com`**: Vereist door het cluster om te controleren of er updates beschikbaar zijn voordat u de handtekeningen voor de afbeelding downloadt.
- **`registry.access.redhat.com`**: Registertoegang is vereist in uw VDI- of laptopomgeving om dev-afbeeldingen te downloaden wanneer u het hulpprogramma ODO CLI gebruikt. (Dit CLI-hulpprogramma is een alternatief CLI-hulpprogramma voor ontwikkelaars die niet bekend zijn met kubernetes). https://docs.openshift.com/container-platform/4.6/cli_reference/developer_cli_odo/understanding-odo.html

---

### <a name="fifth-group-microsoft--red-hat-aro-monitoring-service"></a>VIJFDE GROEP: MICROSOFT & RED HAT ARO MONITORING SERVICE

- **`login.microsoftonline.com`**: Dit wordt door het cluster gebruikt voor verificatie bij Azure.
- **`gcs.prod.monitoring.core.windows.net`**: Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken.
- **`*.blob.core.windows.net`**: Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken.
- **`*.servicebus.windows.net`**: Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken.
- **`*.table.core.windows.net`**: Dit wordt gebruikt voor Microsoft Genève-bewaking, zodat het ARO-team de cluster(s) van de klant kan bewaken.

## <a name="aro-integrations"></a>ARO-integraties

### <a name="azure-monitor-for-containers"></a>Azure Monitor voor containers

Er zijn twee opties om toegang te bieden tot Azure Monitor voor containers. U kunt de Azure Monitor [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags)  toestaan of toegang verlenen tot de vereiste FQDN/toepassingsregels.  Hier volgen de [instructies voor](https://docs.microsoft.com/azure/azure-monitor/containers/container-insights-azure-redhat4-setup) het toevoegen van Azure Monitor aan uw bestaande ARO-cluster.

#### <a name="required-network-rules"></a>Vereiste netwerkregels

De volgende FQDN-/toepassingsregels zijn vereist:

| Doel-eindpunt                                                             | Protocol | Poort    | Gebruik  |
|----------------------------------------------------------------------------------|----------|---------|------|
| [ServiceTag](../virtual-network/service-tags-overview.md#available-service-tags) - **`AzureMonitor:443`**  | TCP           | 443      | Dit eindpunt wordt gebruikt voor het verzenden van metrische gegevens en logboeken naar Azure Monitor en Log Analytics. |

#### <a name="required-fqdn--application-rules"></a>Vereiste FQDN-/toepassingsregels

De volgende FQDN-/toepassingsregels zijn vereist voor ARO-clusters met de Azure Monitor voor containers ingeschakeld:

| FQDN                                    | Poort      | Gebruik      |
|-----------------------------------------|-----------|----------|
| **`dc.services.visualstudio.com`** | **`HTTPS:443`**    | Dit eindpunt wordt gebruikt voor metrische gegevens en het bewaken van telemetriegegevens met behulp Azure Monitor. |
| **`*.ods.opinsights.azure.com`**    | **`HTTPS:443`**    | Dit eindpunt wordt gebruikt door Azure Monitor voor het opnemen van log analytics-gegevens. |
| **`*.oms.opinsights.azure.com`** | **`HTTPS:443`** | Dit eindpunt wordt gebruikt door omsagent, dat wordt gebruikt om de Log Analytics-service te verifiëren. |
| **`*.monitoring.azure.com`** | **`HTTPS:443`** | Dit eindpunt wordt gebruikt voor het verzenden van metrische gegevens naar Azure Monitor. |


## <a name="private-aro-cluster-setup"></a>Installatie van privé-ARO-cluster
Het doel is om het ARO-cluster te beveiligen door Egress-verkeer via een Azure Firewall
### <a name="before"></a>Voor:
![Voor](media/concepts-networking/aro-private.jpg)
### <a name="after"></a>Na:
![Na](media/concepts-networking/aro-fw.jpg)

## <a name="create-a-private-aro-cluster"></a>Een ARO-privécluster maken

### <a name="set-up-vars-for-your-environment"></a>VARS instellen voor uw omgeving
```bash

CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster
AROVNET=aro-vnet # The name of your vnet from your created ARO cluster
JUMPSUBNET=jump-subnet
LOCATION=eastus # The location where ARO cluster is deployed

```

### <a name="create-a-resource-group"></a>Een resourcegroep maken
```bash
az group create -g "$RESOURCEGROUP" -l $LOCATION
```

### <a name="create-the-virtual-network"></a>Het virtuele netwerk maken
```bash
az network vnet create \
  -g $RESOURCEGROUP \
  -n $AROVNET \
  --address-prefixes 10.0.0.0/8
```

### <a name="add-two-empty-subnets-to-your-virtual-network"></a>Twee lege subnetten toevoegen aan uw virtuele netwerk
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "$CLUSTER-master" \
    --address-prefixes 10.10.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry

  az network vnet subnet create \
    -g $RESOURCEGROUP \
    --vnet-name $AROVNET \
    -n "$CLUSTER-worker" \
    --address-prefixes 10.20.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```

### <a name="disable-network-policies-for-private-link-service-on-your-virtual-network-and-subnets-this-is-a-requirement-for-the-aro-service-to-access-and-manage-the-cluster"></a>Schakel netwerkbeleid voor Private Link Service uit in uw virtuele netwerk en subnetten. Dit is een vereiste voor de ARO-service om het cluster te openen en te beheren.
```bash
az network vnet subnet update \
  -g "$RESOURCEGROUP" \
  --vnet-name $AROVNET \
  -n "$CLUSTER-master" \
  --disable-private-link-service-network-policies true
```
### <a name="create-a-firewall-subnet"></a>Een firewallsubnet maken
```bash
az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n "AzureFirewallSubnet" \
    --address-prefixes 10.100.1.0/26
```

## <a name="create-a-jump-host-vm"></a>Een jumphost-VM maken
### <a name="create-a-jump-subnet"></a>Een jump-subnet maken
```bash
  az network vnet subnet create \
    -g "$RESOURCEGROUP" \
    --vnet-name $AROVNET \
    -n $JUMPSUBNET \
    --address-prefixes 10.30.1.0/24 \
    --service-endpoints Microsoft.ContainerRegistry
```
### <a name="create-a-jump-host-vm"></a>Een jumphost-VM maken
```bash
VMUSERNAME=aroadmin

az vm create --name ubuntu-jump \
             --resource-group $RESOURCEGROUP \
             --ssh-key-values ~/.ssh/id_rsa.pub \
             --admin-username $VMUSERNAME \
             --image UbuntuLTS \
             --subnet $JUMPSUBNET \
             --public-ip-address jumphost-ip \
             --vnet-name $AROVNET 
```

## <a name="create-an-azure-red-hat-openshift-cluster"></a>Een Azure Red Hat OpenShift-cluster maken
### <a name="get-a-red-hat-pull-secret-optional"></a>Een pull-geheim voor Red Hat ophalen (optioneel)

Met een pull-geheim van Red Hat heeft uw cluster toegang tot Red Hat-containerregisters, samen met andere inhoud. Deze stap is optioneel, maar wordt wel aanbevolen.

1. **[Ga naar uw Red Hat OpenShift-clusterbeheerportal](https://cloud.redhat.com/openshift/install/azure/aro-provisioned) en meld u aan.**

   U moet zich aanmelden bij uw Red Hat-account of een nieuw Red Hat-account maken met uw zakelijke e-mailadres en de algemene voorwaarden accepteren.

2. **Klik op pull-geheim downloaden.**

Bewaar het opgeslagen `pull-secret.txt`-bestand op een veilige plek; het wordt gebruikt bij het maken van elk cluster.

Wanneer u de opdracht `az aro create` uitvoert, kunt u verwijzen naar uw pull-geheim met behulp van de parameter `--pull-secret @pull-secret.txt`. Voer `az aro create` uit vanuit de map waarin u het `pull-secret.txt`-bestand hebt opgeslagen. Vervang anders `@pull-secret.txt` door `@<path-to-my-pull-secret-file`.

Als u uw pull-geheim kopieert of ernaar verwijst in andere scripts, moet uw pull-geheim worden geformatteerd als een geldige JSON-tekenreeks.

```bash
az aro create \
  -g "$RESOURCEGROUP" \
  -n "$CLUSTER" \
  --vnet $AROVNET \
  --master-subnet "$CLUSTER-master" \
  --worker-subnet "$CLUSTER-worker" \
  --apiserver-visibility Private \
  --ingress-visibility Private \
  --pull-secret @pull-secret.txt
```

## <a name="create-an-azure-firewall"></a>Een Azure-firewall maken

### <a name="create-a-public-ip-address"></a>Een openbaar IP-adres maken
```bash
az network public-ip create -g $RESOURCEGROUP -n fw-ip --sku "Standard" --location $LOCATION
```
### <a name="update-install-azure-firewall-extension"></a>Installatie van Azure Firewall-extensie bijwerken
```bash
az extension add -n azure-firewall
az extension update -n azure-firewall
```

### <a name="create-azure-firewall-and-configure-ip-config"></a>IP Azure Firewall configureren en configureren
```bash
az network firewall create -g $RESOURCEGROUP -n aro-private -l $LOCATION
az network firewall ip-config create -g $RESOURCEGROUP -f aro-private -n fw-config --public-ip-address fw-ip --vnet-name $AROVNET

```

### <a name="capture-azure-firewall-ips-for-a-later-use"></a>IP Azure Firewall vastleggen voor later gebruik
```bash
FWPUBLIC_IP=$(az network public-ip show -g $RESOURCEGROUP -n fw-ip --query "ipAddress" -o tsv)
FWPRIVATE_IP=$(az network firewall show -g $RESOURCEGROUP -n aro-private --query "ipConfigurations[0].privateIpAddress" -o tsv)

echo $FWPUBLIC_IP
echo $FWPRIVATE_IP
```

### <a name="create-a-udr-and-routing-table-for-azure-firewall"></a>Een UDR en routeringstabel maken voor Azure Firewall
```bash
az network route-table create -g $RESOURCEGROUP --name aro-udr

az network route-table route create -g $RESOURCEGROUP --name aro-udr --route-table-name aro-udr --address-prefix 0.0.0.0/0 --next-hop-type VirtualAppliance --next-hop-ip-address $FWPRIVATE_IP
```

### <a name="add-application-rules-for-azure-firewall"></a>Toepassingsregels toevoegen voor Azure Firewall
Regel voor OpenShift om te werken op basis van deze [lijst:](https://docs.openshift.com/container-platform/4.3/installing/install_config/configuring-firewall.html#configuring-firewall_configuring-firewall)
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'ARO' \
 --action allow \
 --priority 100 \
 -n 'required' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns 'registry.redhat.io' '*.quay.io' 'sso.redhat.com' 'management.azure.com' 'mirror.openshift.com' 'api.openshift.com' 'quay.io' '*.blob.core.windows.net' 'gcs.prod.monitoring.core.windows.net' 'registry.access.redhat.com' 'login.microsoftonline.com' '*.servicebus.windows.net' '*.table.core.windows.net' 'grafana.com'
```
Optionele regels voor Docker-afbeeldingen:
```bash
az network firewall application-rule create -g $RESOURCEGROUP -f aro-private \
 --collection-name 'Docker' \
 --action allow \
 --priority 200 \
 -n 'docker' \
 --source-addresses '*' \
 --protocols 'http=80' 'https=443' \
 --target-fqdns '*cloudflare.docker.com' '*registry-1.docker.io' 'apt.dockerproject.org' 'auth.docker.io'
```

### <a name="associate-aro-subnets-to-fw"></a>ARO-subnetten koppelen aan fw
```bash
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-master" --route-table aro-udr
az network vnet subnet update -g $RESOURCEGROUP --vnet-name $AROVNET --name "$CLUSTER-worker" --route-table aro-udr
```

## <a name="test-the-configuration-from-the-jumpbox"></a>De configuratie testen vanuit de Jumpbox
Deze stappen werken alleen als u regels voor Docker-afbeeldingen hebt toegevoegd. 
### <a name="configure-the-jumpbox"></a>De jumpbox configureren
Meld u aan bij een jumpbox-VM en installeer `azure-cli` `oc-cli` , en `jq` -gebruiktils. Voor de installatie van openshift-cli controleert u de Red Hat-klantportal.
```bash
#Install Azure-cli
curl -sL https://aka.ms/InstallAzureCLIDeb | sudo bash
#Install jq
sudo apt install jq -y
```
### <a name="log-into-the-aro-cluster"></a>Aanmelden bij het ARO-cluster
Clusterreferenties opsommen:
```bash

# Login to Azure
az login
# Set Vars in Jumpbox
CLUSTER=aro-cluster # Name of your created cluster
RESOURCEGROUP=aro-rg # The name of your resource group where you created the ARO cluster

#Get the cluster credentials
ARO_PASSWORD=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminPassword')
ARO_USERNAME=$(az aro list-credentials -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.kubeadminUsername')
```
Een API-server-eindpunt op halen:
```bash
ARO_URL=$(az aro show -n $CLUSTER -g $RESOURCEGROUP -o json | jq -r '.apiserverProfile.url')
```

### <a name="download-the-oc-cli-to-the-jumpbox"></a>Download de OC CLI naar de jumpbox
```bash
cd ~
wget https://mirror.openshift.com/pub/openshift-v4/clients/ocp/latest/openshift-client-linux.tar.gz

mkdir openshift
tar -zxvf openshift-client-linux.tar.gz -C openshift
echo 'export PATH=$PATH:~/openshift' >> ~/.bashrc && source ~/.bashrc
```

Meld u aan met `oc login` :
```bash
oc login $ARO_URL -u $ARO_USERNAME -p $ARO_PASSWORD
```

### <a name="run-centos-to-test-outside-connectivity"></a>CentOS uitvoeren om externe connectiviteit te testen
Een pod maken
```bash
cat <<EOF | oc apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: centos
spec:
  containers:
  - name: centos
    image: centos
    ports:
    - containerPort: 80
    command:
    - sleep
    - "3600"
EOF
```
Zodra de pod wordt uitgevoerd, gaat u naar de pod en test u de verbinding buiten de pod.

```bash
oc exec -it centos -- /bin/bash
curl microsoft.com
```

## <a name="access-the-web-console-of-the-private-cluster"></a>Toegang tot de webconsole van het privécluster

### <a name="set-up-ssh-forwards-commands"></a>SSH Forwards-opdrachten instellen

```bash
sudo ssh -i $SSH_PATH -L 443:$CONSOLE_URL:443 aroadmin@$JUMPHOST

example:
sudo ssh -i /Users/jimzim/.ssh/id_rsa -L 443:console-openshift-console.apps.d5xm5iut.eastus.aroapp.io:443 aroadmin@104.211.18.56
```

### <a name="modify-the-etc-hosts-file-on-your-local-machine"></a>Het hosts-bestand op uw lokale computer wijzigen
```bash
##
# Host Database
#
127.0.0.1 console-openshift-console.apps.d5xm5iut.eastus.aroapp.io
127.0.0.1 oauth-openshift.apps.d5xm5iut.eastus.aroapp.io
```

### <a name="use-sshuttle-as-another-option"></a>Gebruik sstle als een andere optie

[SSJetle](https://github.com/sshuttle/sshuttle)


## <a name="clean-up-resources"></a>Resources opschonen

```bash

# Clean up the ARO cluster, vnet, firewall and jumpbox

# Remove udr from master and worker subnets first or will get error when deleting ARO cluster
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-master -g $RESOURCEGROUP --route-table aro-udr --remove routeTable
az network vnet subnet update --vnet-name $AROVNET -n aro-cluster-worker -g $RESOURCEGROUP --route-table aro-udr --remove routeTable

# Remove ARO Cluster
az aro delete -n $CLUSTER -g $RESOURCEGROUP

# Remove the resource group that contains the firewall, jumpbox and vnet
az group delete -n $RESOURCEGROUP
```