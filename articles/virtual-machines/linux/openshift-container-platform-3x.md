---
title: Open Shift container platform 3,11 implementeren in azure
description: Implementeer het open Shift container platform 3,11 in Azure.
author: haroldwongms
manager: mdotson
ms.service: virtual-machines-linux
ms.subservice: workloads
ms.topic: how-to
ms.workload: infrastructure
ms.date: 04/05/2020
ms.author: haroldw
ms.openlocfilehash: 0c60fdfda0c18f5a8feb11c3d9c5a386025670cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87368146"
---
# <a name="deploy-openshift-container-platform-311-in-azure"></a>Open Shift container platform 3,11 implementeren in azure

U kunt een van de volgende methoden gebruiken om open Shift container platform 3,11 te implementeren in Azure:

- U kunt de benodigde onderdelen van de Azure-infra structuur hand matig implementeren en vervolgens de documentatie van open [SHIFT container platform](https://docs.openshift.com/container-platform)volgen.
- U kunt ook een bestaande [Resource Manager-sjabloon](https://github.com/Microsoft/openshift-container-platform/) gebruiken die de implementatie van het open Shift container platform-cluster vereenvoudigt.
- Een andere mogelijkheid is om de [Azure Marketplace-aanbieding](https://azuremarketplace.microsoft.com/marketplace/apps/osatesting.open-shift-azure-proxy)te gebruiken.

Voor alle opties is een Red Hat-abonnement vereist. Tijdens de implementatie wordt het Red Hat Enterprise Linux-exemplaar geregistreerd bij het Red Hat-abonnement en gekoppeld aan de groeps-ID die de rechten voor open Shift container platform bevat.
Zorg ervoor dat u een geldige gebruikers naam, wacht woord en groeps-ID voor Red Hat Subscription Manager (RHSM) hebt. U kunt een activerings sleutel, organisatie-ID en groeps-ID gebruiken. U kunt deze informatie controleren door u aan te melden bij https://access.redhat.com .


## <a name="deploy-using-the-openshift-container-platform-resource-manager-311-template"></a>Implementeren met behulp van de sjabloon open Shift container platform Resource Manager 3,11

### <a name="private-clusters"></a>Particuliere clusters

Voor het implementeren van particuliere open Shift-clusters is meer dan alleen een openbaar IP-adres dat is gekoppeld aan de Master-load balancer (webconsole) of de infra structuur load balancer (router) vereist.  Een persoonlijk cluster maakt doorgaans gebruik van een aangepaste DNS-server (niet de standaard Azure DNS), een aangepaste domein naam (zoals contoso.com) en vooraf gedefinieerde virtuele netwerken.  Voor particuliere clusters moet u het virtuele netwerk vooraf configureren met alle juiste subnetten en DNS-server instellingen.  Gebruik vervolgens **existingMasterSubnetReference**, **existingInfraSubnetReference**, **existingCnsSubnetReference**en **existingNodeSubnetReference** om het bestaande subnet op te geven voor gebruik door het cluster.

Als de privécloud is geselecteerd (**masterClusterType**= privé), moet er een statisch privé-IP-adres worden opgegeven voor **masterPrivateClusterIp**.  Dit IP-adres wordt toegewezen aan de front-end van de hoofd load balancer.  Het IP-adres moet zich in de CIDR bevinden voor het hoofd-subnet en wordt niet gebruikt.  **masterClusterDnsType** moet worden ingesteld op aangepast en de naam van de hoofd-DNS moet worden opgegeven voor **masterClusterDns**.  De DNS-naam moet worden toegewezen aan het statische privé-IP-adres en wordt gebruikt om toegang te krijgen tot de console op de hoofd knooppunten.

Als er een particuliere router is geselecteerd (**routerClusterType**= privé), moet een statisch privé-IP-adres worden opgegeven voor **routerPrivateClusterIp**.  Dit IP-adres wordt toegewezen aan de front-end van de infra structuur load balancer.  Het IP-adres moet zich in de CIDR bevinden voor het infra structuur-subnet en niet in gebruik zijn.  **routingSubDomainType** moet worden ingesteld op aangepast en de DNS-naam van het Joker teken voor route ring moet worden opgegeven voor **routingSubDomain**.  

Als er persoonlijke Masters en particuliere router zijn geselecteerd, moet de aangepaste domein naam ook worden ingevoerd voor de **domainName**

Na een geslaagde implementatie is het Bastion-knoop punt het enige knoop punt met een openbaar IP-adres dat u kunt SSHen.  Zelfs als de hoofd knooppunten zijn geconfigureerd voor open bare toegang, zijn ze niet beschikbaar voor SSH-toegang.

Als u wilt implementeren met behulp van de Resource Manager-sjabloon, gebruikt u een parameter bestand voor het opgeven van de invoer parameters. Als u de implementatie verder wilt aanpassen, splitst u de GitHub-opslag plaats en wijzigt u de juiste items.

Enkele algemene aanpassings opties zijn, maar zijn niet beperkt tot:

- Bastion VM-grootte (variabele in azuredeploy.jsop)
- Naam conventies (variabelen in azuredeploy.jsop)
- Details van open Shift-cluster, gewijzigd via een hosts-bestand (deployOpenShift.sh)

### <a name="configure-the-parameters-file"></a>Het parameter bestand configureren

In de open [SHIFT container platform-sjabloon](https://github.com/Microsoft/openshift-container-platform) zijn meerdere vertakkingen beschikbaar voor verschillende versies van open Shift container platform.  Op basis van uw behoeften kunt u rechtstreeks vanuit de opslag plaats implementeren, of u kunt de opslag plaats splitsen en aangepaste wijzigingen aanbrengen in de sjablonen of scripts voordat u implementeert.

Gebruik de `appId` waarde van de service-principal die u eerder hebt gemaakt voor de `aadClientId` para meter.

In het volgende voor beeld ziet u een bestand met para meters met de naam azuredeploy.parameters.jsop met alle vereiste invoer.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "_artifactsLocation": {
            "value": "https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master"
        },
        "location": {
            "value": "eastus"
        },
        "masterVmSize": {
            "value": "Standard_E2s_v3"
        },
        "infraVmSize": {
            "value": "Standard_D4s_v3"
        },
        "nodeVmSize": {
            "value": "Standard_D4s_v3"
        },
        "cnsVmSize": {
            "value": "Standard_E4s_v3"
        },
        "osImageType": {
            "value": "defaultgallery"
        },
        "marketplaceOsImage": {
            "value": {
                "publisher": "RedHat",
                "offer": "RHEL",
                "sku": "7-RAW",
                "version": "latest"
            }
        },
        "storageKind": {
            "value": "changeme"
        },
        "openshiftClusterPrefix": {
            "value": "changeme"
        },
        "minorVersion": {
            "value": "69"
        },
        "masterInstanceCount": {
            "value": 3
        },
        "infraInstanceCount": {
            "value": 3
        },
        "nodeInstanceCount": {
            "value": 3
        },
        "cnsInstanceCount": {
            "value": 3
        },
        "osDiskSize": {
            "value": 64
        },
        "dataDiskSize": {
            "value": 64
        },
        "cnsGlusterDiskSize": {
            "value": 128
        },
        "adminUsername": {
            "value": "changeme"
        },
        "enableMetrics": {
            "value": "false"
        },
        "enableLogging": {
            "value": "false"
        },
        "enableCNS": {
            "value": "false"
        },
        "rhsmUsernameOrOrgId": {
            "value": "changeme"
        },
        "rhsmPoolId": {
            "value": "changeme"
        },
        "rhsmBrokerPoolId": {
            "value": "changeme"
        },
        "sshPublicKey": {
            "value": "GEN-SSH-PUB-KEY"
        },
        "keyVaultSubscriptionId": {
            "value": "255a325e-8276-4ada-af8f-33af5658eb34"
        },
        "keyVaultResourceGroup": {
            "value": "changeme"
        },
        "keyVaultName": {
            "value": "changeme"
        },
        "enableAzure": {
            "value": "true"
        },
        "aadClientId": {
            "value": "changeme"
        },
        "domainName": {
            "value": "contoso.com"
        },
        "masterClusterDnsType": {
            "value": "default"
        },
        "masterClusterDns": {
            "value": "console.contoso.com"
        },
        "routingSubDomainType": {
            "value": "nipio"
        },
        "routingSubDomain": {
            "value": "apps.contoso.com"
        },
        "virtualNetworkNewOrExisting": {
            "value": "new"
        },
        "virtualNetworkName": {
            "value": "changeme"
        },
        "addressPrefixes": {
            "value": "10.0.0.0/14"
        },
        "masterSubnetName": {
            "value": "changeme"
        },
        "masterSubnetPrefix": {
            "value": "10.1.0.0/16"
        },
        "infraSubnetName": {
            "value": "changeme"
        },
        "infraSubnetPrefix": {
            "value": "10.2.0.0/16"
        },
        "nodeSubnetName": {
            "value": "changeme"
        },
        "nodeSubnetPrefix": {
            "value": "10.3.0.0/16"
        },
        "existingMasterSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/mastersubnet"
        },
        "existingInfraSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/infrasubnet"
        },
        "existingCnsSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/cnssubnet"
        },
        "existingNodeSubnetReference": {
            "value": "/subscriptions/abc686f6-963b-4e64-bff4-99dc369ab1cd/resourceGroups/vnetresourcegroup/providers/Microsoft.Network/virtualNetworks/openshiftvnet/subnets/nodesubnet"
        },
        "masterClusterType": {
            "value": "public"
        },
        "masterPrivateClusterIp": {
            "value": "10.1.0.200"
        },
        "routerClusterType": {
            "value": "public"
        },
        "routerPrivateClusterIp": {
            "value": "10.2.0.200"
        },
        "routingCertType": {
            "value": "selfsigned"
        },
        "masterCertType": {
            "value": "selfsigned"
        }
    }
}
```

Vervang de para meters door uw specifieke informatie.

Verschillende releases kunnen verschillende para meters hebben, dus controleer de benodigde para meters voor de vertakking die u gebruikt.

### <a name="azuredeployparametersjson-file-explained"></a>azuredeploy.Parameters.jsin het bestand wordt uitgelegd

| Eigenschap | Beschrijving | Geldige opties | Standaardwaarde |
|----------|-------------|---------------|---------------|
| `_artifactsLocation`  | URL voor artefacten (JSON, scripts, enzovoort) |  |  https://RAW.githubusercontent.com/Microsoft/openshift-container-platform/Master  |
| `location` | Azure-regio voor het implementeren van resources voor |  |  |
| `masterVmSize` | Grootte van de hoofd-VM. Selecteer een van de toegestane VM-grootten in de azuredeploy.jsvoor het bestand |  | Standard_E2s_v3 |
| `infraVmSize` | Grootte van de infra structuur-VM. Selecteer een van de toegestane VM-grootten in de azuredeploy.jsvoor het bestand |  | Standard_D4s_v3 |
| `nodeVmSize` | Grootte van de virtuele machine van het app-knoop punt. Selecteer een van de toegestane VM-grootten in de azuredeploy.jsvoor het bestand |  | Standard_D4s_v3 |
| `cnsVmSize` | Grootte van de VM-knoop punt (native Storage) van de container. Selecteer een van de toegestane VM-grootten in de azuredeploy.jsvoor het bestand |  | Standard_E4s_v3 |
| `osImageType` | De RHEL-installatie kopie die moet worden gebruikt. defaultgallery: op aanvraag; Marketplace: afbeelding van derden | defaultgallery <br> marketplace | defaultgallery |
| `marketplaceOsImage` | Als `osImageType` is Marketplace, voert u de juiste waarden in voor ' Publisher ', ' offer ', ' SKU ', ' versie ' van de Marketplace-aanbieding. Deze para meter is een object type |  |  |
| `storageKind` | Het type opslag dat moet worden gebruikt  | bijgehouden<br> beheerde | bijgehouden |
| `openshiftClusterPrefix` | Het cluster voorvoegsel dat wordt gebruikt voor het configureren van hostnamen voor alle knoop punten.  Tussen 1 en 20 tekens |  | mycluster |
| `minoVersion` | De secundaire versie van open Shift container platform 3,11 om te implementeren |  | 69 |
| `masterInstanceCount` | Aantal te implementeren modellen knoop punten | 1, 3, 5 | 3 |
| `infraInstanceCount` | Aantal te implementeren infra structuur knooppunten | 1, 2, 3 | 3 |
| `nodeInstanceCount` | Aantal knoop punten dat moet worden geïmplementeerd | 1, 2, 3, 4, 5, 6, 7, 8, 9, 10, 11, 12, 13, 14, 15, 16, 17, 18, 19, 20, 21, 22, 23, 24, 25, 26, 27, 28, 29, 30 | 2 |
| `cnsInstanceCount` | Aantal CNS-knoop punten dat moet worden geïmplementeerd | 3, 4 | 3 |
| `osDiskSize` | Grootte van de besturingssysteem schijf voor de virtuele machine (in GB) | 64, 128, 256, 512, 1024, 2048 | 64 |
| `dataDiskSize` | Grootte van de gegevens schijf die moet worden gekoppeld aan knoop punten voor docker-volume (in GB) | 32, 64, 128, 256, 512, 1024, 2048 | 64 |
| `cnsGlusterDiskSize` | Grootte van de gegevens schijf die moet worden gekoppeld aan CNS-knoop punten voor gebruik door glusterfs (in GB | 32, 64, 128, 256, 512, 1024, 2048 | 128 |
| `adminUsername` | Gebruikers naam van de beheerder voor de aanmelding van zowel het besturings systeem (VM) als de eerste openshift-gebruiker |  | ocpadmin |
| `enableMetrics` | Metrieken inschakelen. Voor metrische gegevens zijn meer resources nodig, dus Selecteer de juiste grootte voor infra structuur-VM | waar <br> onjuist | onjuist |
| `enableLogging` | Schakel logboek registratie in. voor elasticsearch Pod is 8 GB RAM vereist. Selecteer de juiste grootte voor infra structuur-VM | waar <br> onjuist | onjuist |
| `enableCNS` | Native opslag in container inschakelen | waar <br> onjuist | onjuist |
| `rhsmUsernameOrOrgId` | Gebruikers naam of organisatie-ID van Red Hat Subscription Manager |  |  |
| `rhsmPoolId` | De groeps-ID van Red Hat Subscription Manager met de rechten van open Shift voor reken knooppunten |  |  |
| `rhsmBrokerPoolId` | De groeps-ID van Red Hat Subscription Manager met de rechten van open Shift voor modellen en infra structuur knooppunten. Als u geen andere groeps-Id's hebt, voert u dezelfde groeps-ID in als ' rhsmPoolId ' |  |
| `sshPublicKey` | Kopieer hier uw open bare SSH-sleutel |  |  |
| `keyVaultSubscriptionId` | De abonnements-ID van het abonnement dat de Key Vault bevat |  |  |
| `keyVaultResourceGroup` | De naam van de resource groep die de Key Vault bevat |  |  |
| `keyVaultName` | De naam van de Key Vault die u hebt gemaakt |  |  |
| `enableAzure` | Azure Cloud provider inschakelen | waar <br> onjuist | true |
| `aadClientId` | Azure Active Directory client-ID ook bekend als toepassings-ID voor Service-Principal |  |  |
| `domainName` | De naam van de aangepaste domein naam die moet worden gebruikt (indien van toepassing). Ingesteld op ' geen ' als er geen volledig particulier cluster wordt geïmplementeerd |  | geen |
| `masterClusterDnsType` | Domein type voor open Shift-webconsole. ' default ' maakt gebruik van het DNS-label van het Master-infra structuur openbaar IP-adres. met aangepast kunt u uw eigen naam definiëren | standaardinstelling <br> aangepast | standaardinstelling |
| `masterClusterDns` | De aangepaste DNS-naam die moet worden gebruikt voor toegang tot de open Shift-webconsole als u aangepast hebt geselecteerd voor `masterClusterDnsType` |  | console.contoso.com |
| `routingSubDomainType` | Als deze is ingesteld op ' nipio ', `routingSubDomain` gebruikt NIP.io.  Gebruik aangepast als u uw eigen domein hebt dat u wilt gebruiken voor route ring | nipio <br> aangepast | nipio |
| `routingSubDomain` | De DNS-naam voor joker tekens die u wilt gebruiken voor route ring als u aangepast hebt geselecteerd voor `routingSubDomainType` |  | apps.contoso.com |
| `virtualNetworkNewOrExisting` | Selecteer of u een bestaande Virtual Network wilt gebruiken of een nieuwe Virtual Network wilt maken | bestaand <br> nieuw | nieuw |
| `virtualNetworkResourceGroupName` | De naam van de resource groep voor het nieuwe Virtual Network als u ' nieuw ' hebt geselecteerd voor `virtualNetworkNewOrExisting` |  | resourceGroup (). naam |
| `virtualNetworkName` | De naam van de nieuwe Virtual Network die moet worden gemaakt als u nieuw hebt geselecteerd voor `virtualNetworkNewOrExisting` |  | openshiftvnet |
| `addressPrefixes` | Adres voorvoegsel van het nieuwe virtuele netwerk |  | 10.0.0.0/14 |
| `masterSubnetName` | De naam van het hoofd-subnet |  | mastersubnet |
| `masterSubnetPrefix` | CIDR die wordt gebruikt voor het hoofd-subnet: moet een subset zijn van de addressPrefix |  | 10.1.0.0/16 |
| `infraSubnetName` | De naam van het infra-subnet |  | infrasubnet |
| `infraSubnetPrefix` | CIDR die wordt gebruikt voor het infra structuur subnet: moet een subset zijn van de addressPrefix |  | 10.2.0.0/16 |
| `nodeSubnetName` | De naam van het subnet van het knoop punt |  | nodesubnet |
| `nodeSubnetPrefix` | CIDR die wordt gebruikt voor het subnet van het knoop punt: moet een subset zijn van de addressPrefix |  | 10.3.0.0/16 |
| `existingMasterSubnetReference` | Volledige verwijzing naar het bestaande subnet voor hoofd knooppunten. Niet nodig bij het maken van een nieuw vNet/subnet |  |  |
| `existingInfraSubnetReference` | Volledige verwijzing naar het bestaande subnet voor infra structuur knooppunten. Niet nodig bij het maken van een nieuw vNet/subnet |  |  |
| `existingCnsSubnetReference` | Volledige verwijzing naar het bestaande subnet voor de CNS-knoop punten. Niet nodig bij het maken van een nieuw vNet/subnet |  |  |
| `existingNodeSubnetReference` | Volledige verwijzing naar het bestaande subnet voor reken knooppunten. Niet nodig bij het maken van een nieuw vNet/subnet |  |  |
| `masterClusterType` | Geef op of het cluster particuliere of open bare hoofd knooppunten gebruikt. Als privé is gekozen, worden de hoofd knooppunten niet blootgesteld aan Internet via een openbaar IP-adres. In plaats daarvan wordt het privé-IP-adres gebruikt dat is opgegeven in de `masterPrivateClusterIp` | Open <br> eigen | Open |
| `masterPrivateClusterIp` | Als er knoop punten van een persoonlijke Master zijn geselecteerd, moet u een privé-IP-adres opgeven voor gebruik door de interne load balancer voor hoofd knooppunten. Dit statische IP-adres moet zich in het CIDR-blok bevinden voor het hoofd-subnet en wordt niet al gebruikt. Als er open bare hoofd knooppunten zijn geselecteerd, wordt deze waarde niet gebruikt, maar moet deze wel worden opgegeven |  | 10.1.0.200 |
| `routerClusterType` | Geef op of het cluster particuliere of open bare infra structuur knooppunten gebruikt. Als privé is gekozen, worden de infra structuur niet via een openbaar IP-adres blootgesteld aan Internet. In plaats daarvan wordt het privé-IP-adres gebruikt dat is opgegeven in de `routerPrivateClusterIp` | Open <br> eigen | Open |
| `routerPrivateClusterIp` | Als er particuliere infra structuur knooppunten zijn geselecteerd, moet er een privé-IP-adres worden opgegeven voor gebruik door de interne load balancer voor infra structuur knooppunten. Dit statische IP-adres moet zich in het CIDR-blok bevinden voor het infra structuur-subnet en niet al in gebruik. Als open bare-infra structuur knooppunten zijn geselecteerd, wordt deze waarde niet gebruikt, maar moet deze wel worden opgegeven |  | 10.2.0.200 |
| `routingCertType` | Aangepast certificaat voor routerings domein of het standaard zelfondertekende certificaat gebruiken-volg de instructies in de sectie **aangepaste certificaten** | selfsigned <br> aangepast | selfsigned |
| `masterCertType` | Aangepast certificaat voor hoofd domein of het standaard zelfondertekende certificaat gebruiken-volg de instructies in de sectie **aangepaste certificaten** | selfsigned <br> aangepast | selfsigned |

<br>

### <a name="deploy-using-azure-cli"></a>Implementeren met behulp van Azure CLI

> [!NOTE] 
> Voor de volgende opdracht is Azure CLI 2.0.8 of hoger vereist. U kunt de CLI-versie controleren met de `az --version` opdracht. Zie [Azure cli installeren](/cli/azure/install-azure-cli?view=azure-cli-latesti)voor informatie over het bijwerken van de CLI-versie.

In het volgende voor beeld worden het openshift-cluster en alle gerelateerde resources geïmplementeerd in een resource groep met de naam openshiftrg, met een implementatie naam van myOpenShiftCluster. Er wordt rechtstreeks naar de sjabloon verwezen vanuit de GitHub opslag plaats en er wordt een lokaal bestand met para meters met de naam azuredeploy.parameters.jsin het bestand gebruikt.

```azurecli 
az group deployment create -g openshiftrg --name myOpenShiftCluster \
      --template-uri https://raw.githubusercontent.com/Microsoft/openshift-container-platform/master/azuredeploy.json \
      --parameters @./azuredeploy.parameters.json
```

Het volt ooien van de implementatie duurt ten minste 60 minuten, op basis van het totale aantal geïmplementeerde knoop punten en geconfigureerde opties. De Bastion DNS FQDN en de URL van de open Shift-console worden afgedrukt op de Terminal wanneer de implementatie is voltooid.

```json
{
  "Bastion DNS FQDN": "bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com",
  "OpenShift Console URL": "http://openshiftlb.eastus.cloudapp.azure.com/console"
}
```

Als u niet wilt dat de opdracht regel wordt ingewisseld om de implementatie te volt ooien, voegt u `--no-wait` als een van de opties voor de groeps implementatie toe. De uitvoer van de implementatie kan worden opgehaald uit de Azure Portal in de implementatie sectie voor de resource groep.

## <a name="connect-to-the-openshift-cluster"></a>Verbinding maken met het open Shift-cluster

Wanneer de implementatie is voltooid, haalt u de verbinding op in de sectie uitvoer van de implementatie. Maak verbinding met de open Shift-console met uw browser met behulp van de open **SHIFT console-URL**. U kunt ook SSH-naar de bastion-host. Hieronder volgt een voor beeld waarin de gebruikers naam van de beheerder clusteradmin is en de Bastion open bare IP DNS FQDN is bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com:

```bash
$ ssh clusteradmin@bastiondns4hawllzaavu6g.eastus.cloudapp.azure.com
```

## <a name="clean-up-resources"></a>Resources opschonen

Gebruik de opdracht [AZ Group delete](/cli/azure/group) om de resource groep, open Shift-cluster en alle gerelateerde resources te verwijderen wanneer u deze niet meer nodig hebt.

```azurecli 
az group delete --name openshiftrg
```

## <a name="next-steps"></a>Volgende stappen

- [Taken na de implementatie](./openshift-container-platform-3x-post-deployment.md)
- [Problemen met openshift-implementatie in azure oplossen](./openshift-container-platform-3x-troubleshooting.md)
- [Aan de slag met open Shift container platform](https://docs.openshift.com)
