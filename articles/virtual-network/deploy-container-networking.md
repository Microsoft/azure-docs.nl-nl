---
title: Container Network Interface-invoegtoepassing Azure Virtual Network implementeren | Microsoft Docs
description: Meer informatie over het implementeren van de Azure Virtual Network container Network Interface (CNI)-invoeg toepassing voor Kubernetes-clusters.
services: virtual-network
documentationcenter: na
author: aanandr
manager: NarayanAnnamalai
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 9/18/2018
ms.author: aanandr
ms.custom: ''
ms.openlocfilehash: b95b3cfdf8fea6e31015d945566803569b4ba064
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98222918"
---
# <a name="deploy-the-azure-virtual-network-container-network-interface-plug-in"></a>De Container Network Interface-invoegtoepassing (CNI) Azure Virtual Network implementeren

De Container Network Interface-invoegtoepassing (CNI) Azure Virtual Network wordt geïnstalleerd op een virtuele Azure-machine om mogelijkheden voor virtuele netwerken toe te voegen aan containers van Kubernetes Pods en Docker. Zie [Enable containers to use Azure Virtual Network capabilities](container-networking-overview.md) (Containers voorbereiden voor gebruik van de mogelijkheden van Azure Virtual Network) voor meer informatie over de invoegtoepassing. De invoegtoepassing kan bovendien worden gebruikt met Azure Kubernetes Service (AKS) door het kiezen van de optie [Geavanceerd](../aks/configure-azure-cni.md?toc=%2fazure%2fvirtual-network%2ftoc.json) (bij Netwerkconfiguratie), waarmee AKS-containers automatisch in een virtueel netwerk worden geplaatst.

## <a name="deploy-plug-in-for-acs-engine-kubernetes-cluster"></a>Invoegtoepassing implementeren voor ACS-Engine Kubernetes-cluster

De ACS-Engine implementeert een Kubernetes-cluster met behulp van een Azure Resource Manager-sjabloon. De configuratie van het cluster is opgenomen in een JSON-bestand dat aan het hulpprogramma wordt doorgegeven bij het genereren van de sjabloon. Zie [Microsoft Azure Container Service Engine - Cluster Definition](https://github.com/Azure/acs-engine/blob/master/docs/clusterdefinition.md) (Microsoft Azure Container Service Engine - Clusterdefinitie) voor de volledige lijst met ondersteunde clusterinstellingen en de bijbehorende beschrijvingen. De invoegtoepassing is de standaardnetwerkinvoegtoepassing voor clusters die zijn gemaakt met de ACS-Engine. De volgende instellingen voor de netwerkconfiguratie zijn belangrijk bij het configureren van de invoegtoepassing:

  | Instelling                              | Beschrijving                                                                                                           |
  |--------------------------------------|------------------------------------------------------------------------------------------------------                 |
  | firstConsecutiveStaticIP             | Het IP-adres dat wordt toegewezen aan het hoofdknooppunt. Dit is een verplichte instelling.                                     |
  | clusterSubnet onder kubernetesConfig | CIDR van het subnet van het virtuele netwerk waar het cluster wordt geïmplementeerd, en van waaruit IP-adressen worden toegewezen aan Pods   |
  | vnetSubnetId onder masterProfile     | De resource-id van Azure Resource Manager van het subnet waarnaar het cluster moet worden geïmplementeerd                    |
  | vnetCidr                             | CIDR van het virtuele netwerk waarnaar het cluster wordt geïmplementeerd                                                             |
  | max-Pods onder kubeletConfig         | Maximum aantal Pods op elke virtuele machine van de agent. Voor de invoegtoepassing is de standaardwaarde 30. U kunt een waarde opgeven van maximaal 250  |

### <a name="example-configuration"></a>Voorbeeldconfiguratie

Het volgende json-voorbeeld is voor een cluster met deze eigenschappen:
-   1 hoofdknooppunt en 2 agent-knooppunten 
-   Wordt geïmplementeerd in een subnet met de naam *KubeClusterSubnet* (10.0.0.0/20), met daarin zowel het hoofdknooppunt als de agent-knooppunten.

```json
{
  "apiVersion": "vlabs",
  "properties": {
    "orchestratorProfile": {
      "orchestratorType": "Kubernetes",
      "kubernetesConfig": {
        "clusterSubnet": "10.0.0.0/20" --> Subnet allocated for the cluster
      }
    },
    "masterProfile": {
      "count": 1,
      "dnsPrefix": "ACSKubeMaster",
      "vmSize": "Standard_A2",
      "vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<Vnet Name>/subnets/KubeClusterSubnet",
      "firstConsecutiveStaticIP": "10.0.1.50", --> IP address allocated to the Master node
"vnetCidr": "10.0.0.0/16" --> Virtual network address space
    },
    "agentPoolProfiles": [
      {
        "name": "k8sagentpoo1",
        "count": 2,
        "vmSize": "Standard_A2_v2",
"vnetSubnetId": "/subscriptions/<subscription ID>/resourceGroups/<Resource Group Name>/providers/Microsoft.Network/virtualNetworks/<VNet Name>/subnets/KubeClusterSubnet",
        "availabilityProfile": "AvailabilitySet"
      }
    ],
    "linuxProfile": {
      "adminUsername": "KubeServerAdmin",
      "ssh": {
        "publicKeys": [
          {…}
        ]
      }
    },
    "servicePrincipalProfile": {
      "clientId": "dd438987-aa12-4754-b47d-375811889714",
      "secret": "azure123"
    }
  }
}
```

## <a name="deploy-plug-in-for-a-kubernetes-cluster"></a>Invoegtoepassing implementeren voor een Kubernetes-cluster

Voltooi de volgende stappen om de invoegtoepassing te installeren op elke virtuele Azure-machine in een Kubernetes-cluster:

1. [Down load en installeer de invoeg toepassing](#download-and-install-the-plug-in).
2. Wijs op elke virtuele machine van waaruit IP-adressen worden toegewezen aan Pods vooraf een groep met IP-adressen voor het virtuele netwerk toe. Elke virtuele Azure-machine wordt geleverd met een primair privé-IP-adres voor een virtueel netwerk op elke netwerkinterface. De groep met IP-adressen voor Pods wordt toegevoegd als secundaire adressen (*ipconfigs*) op de netwerkinterface van de virtuele machine, met behulp van een van de volgende opties:

   - **CLI**: [meerdere IP-adressen toewijzen met de Azure CLI](virtual-network-multiple-ip-addresses-cli.md)
   - **PowerShell**: [meerdere IP-adressen toewijzen met behulp van PowerShell](virtual-network-multiple-ip-addresses-powershell.md)
   - **Portal**: [meerdere IP-adressen toewijzen met behulp van de Azure Portal](virtual-network-multiple-ip-addresses-portal.md)
   - **Azure Resource Manager-sjabloon**: [meerdere IP-adressen toewijzen met behulp van sjablonen](./template-samples.md)

   Zorg ervoor dat u voldoende IP-adressen toevoegt voor alle Pods die u verwacht te gebruiken op de virtuele machine.

3. Selecteer de invoegtoepassing voor het aanbieden van netwerkvoorzieningen voor uw cluster door aan Kubelet de opdrachtregeloptie `–network-plugin=cni` door te geven tijdens het maken van het cluster. Kubernetes zoekt de invoegtoepassing en het configuratiebestand standaard in de mappen waarin ze al zijn geïnstalleerd.
4. Als u wilt dat uw Pods toegang hebben tot het internet, voegt u de volgende *iptables*-regel toe op uw virtuele Linux-machines om 'source NAT' toe te passen op internetverkeer. In het volgende voorbeeld is het opgegeven IP-adresbereik 10.0.0.0/8.

   ```bash
   iptables -t nat -A POSTROUTING -m iprange ! --dst-range 168.63.129.16 -m
   addrtype ! --dst-type local ! -d 10.0.0.0/8 -j MASQUERADE
   ```

   De regels blokkeren NAT-verkeer dat niet is bestemd voor de opgegeven IP-adresbereiken. De aanname is dat al het verkeer buiten de vorige bereiken internetverkeer is. U kunt ervoor kiezen om de IP-bereiken van het virtuele netwerk van de virtuele machine op te geven, van virtuele netwerken met peering en van on-premises netwerken.

   Virtuele Windows-machines passen automatisch 'source NAT' toe op verkeer met een bestemming buiten het subnet van de virtuele machine. Het is niet mogelijk om aangepaste IP-adresbereiken op te geven.

Na het voltooien van de vorige stappen, krijgen Pods die worden toegevoegd op de virtuele machines met de Kubernetes-agent automatisch privé-IP-adressen uit het virtuele netwerk.

## <a name="deploy-plug-in-for-docker-containers"></a>Invoegtoepassing implementeren voor Docker-containers

1. [Down load en installeer de invoeg toepassing](#download-and-install-the-plug-in).
2. Maak Docker-containers met de volgende opdracht:

   ```
   ./docker-run.sh \<container-name\> \<container-namespace\> \<image\>
   ```

Er worden vanuit de toegewezen groep automatisch IP-adressen toegewezen aan de containers. Als u load-balancing wilt toepassen op verkeer naar de Docker-containers, moet u ze achter een softwarematige load balancer plaatsen. Daarnaast moet u een load balancer-test configureren, op dezelfde manier als dat u een beleid en tests maakt voor een virtuele machine.

### <a name="cni-network-configuration-file"></a>Netwerkconfiguratiebestand van CNI

Het netwerkconfiguratiebestand van CNI heeft de JSON-indeling. Het bestand bevindt zich standaard in `/etc/cni/net.d` voor Linux en `c:\cni\netconf` voor Windows. Het bestand bepaalt de configuratie van de invoegtoepassing en verschilt voor Windows en Linux. De json hieronder is een voorbeeld van een Linux-configuratiebestand, gevolgd door een uitleg van enkele van de belangrijkste instellingen. U hoeft het bestand niet te wijzigen:

```json
{
       "cniVersion":"0.3.0",
       "name":"azure",
       "plugins":[
          {
             "type":"azure-vnet",
             "mode":"bridge",
             "bridge":"azure0",
             "ipam":{
                "type":"azure-vnet-ipam"
             }
          },
          {
             "type":"portmap",
             "capabilities":{
                "portMappings":true
             },
             "snat":true
          }
       ]
}
```

#### <a name="settings-explanation"></a>Uitleg van instellingen

- **cniVersion**: de CNI-invoegtoepassing Azure Virtual Network ondersteunt versies 0.3.0 en 0.3.1 van de [CNI-specificatie](https://github.com/containernetworking/cni/blob/master/SPEC.md).
- **name**: naam van het netwerk. Deze eigenschap kan worden ingesteld op elke unieke waarde.
- **type**: naam van de netwerkinvoegtoepassing. Ingesteld op *azure-vnet*.
- **mode**: operationele modus. Dit veld is optioneel. De enige modus die wordt ondersteund is 'bridge'. Zie [Operational modes](https://github.com/Azure/azure-container-networking/blob/master/docs/network.md) (Operationele modi) voor meer informatie.
- **bridge**: naam van de brug die wordt gebruikt om containers te verbinden met een virtueel netwerk. Dit veld is optioneel. Als u dit weglaat, kiest de invoegtoepassing automatisch een unieke naam op basis van de hoofdindex van de interface.
- **ipam-type**: naam van de IPAM-invoegtoepassing. Altijd ingesteld op *azure-vnet-ipam*.

## <a name="download-and-install-the-plug-in"></a>De invoegtoepassing downloaden en installeren

Download de invoegtoepassing van [GitHub](https://github.com/Azure/azure-container-networking/releases). Download de nieuwste versie voor het platform dat u gebruikt:

- **Linux**: [Azure-vnet-cni-linux-amd64- \<version no.\> . tgz](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-linux-amd64-v1.0.12-rc3.tgz)
- **Windows**: [Azure-vnet-cni-Windows-amd64- \<version no.\> . zip](https://github.com/Azure/azure-container-networking/releases/download/v1.0.12-rc3/azure-vnet-cni-windows-amd64-v1.0.12-rc3.zip)

Kopieer het installatiescript voor [Linux](https://github.com/Azure/azure-container-networking/blob/master/scripts/install-cni-plugin.sh) of [Windows](https://github.com/Azure/azure-container-networking/blob/master/scripts/Install-CniPlugin.ps1) naar uw computer. Sla het script op in een map `scripts` op uw computer en geef het bestand de naam `install-cni-plugin.sh` voor Linux of `install-cni-plugin.ps1` voor Windows. U kunt de invoegtoepassing installeren door het juiste script voor uw platform uit te voeren, waarbij u de versie opgeeft van de invoegtoepassing die u gebruikt. Geef bijvoorbeeld *v1.0.12-rc3* op:

   ```bash
   \$scripts/install-cni-plugin.sh [version]
   ```

   ```powershell
   scripts\\ install-cni-plugin.ps1 [version]
   ```

Het script installeert de invoegtoepassing onder `/opt/cni/bin` voor Linux en `c:\cni\bin` voor Windows. De geïnstalleerde invoegtoepassing wordt geleverd met een eenvoudig netwerkconfiguratiebestand dat direct kan worden gebruikt na de installatie. Het bestand hoeft niet te worden bijgewerkt. Zie [Netwerkconfiguratiebestand van CNI](#cni-network-configuration-file) voor meer informatie over de instellingen in het bestand.