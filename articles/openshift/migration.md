---
title: Migreren van een Azure Red Hat OpenShift 3.11 naar Azure Red Hat OpenShift 4
description: Migreren van een Azure Red Hat OpenShift 3.11 naar Azure Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 08/13/2020
keywords: migratie, Aro, open Shift, Red Hat
ms.openlocfilehash: d875389419e1442b942b4b42389664ffdd064ba3
ms.sourcegitcommit: 58ff80474cd8b3b30b0e29be78b8bf559ab0caa1
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100634104"
---
# <a name="migrate-from-azure-red-hat-openshift-311-to-azure-red-hat-openshift-4"></a>Migreren van Azure Red Hat open Shift 3,11 naar Azure Red Hat open Shift 4

Azure Red Hat open Shift op openshift 4 brengt Kubernetes 1,16 over Red Hat-kern besturingssystemen, persoonlijke clusters, uw eigen ondersteuning voor het virtuele netwerk en de volledige rol cluster beheerder. Daarnaast zijn er veel nieuwe functies beschikbaar, zoals ondersteuning voor het operator raamwerk, de operator hub en open Shift service-net.

Als u wilt overstappen van Azure Red Hat open Shift 3,11 naar Azure Red Hat open Shift 4, controleert u de [verschillen in opslag, netwerken, logboek registratie, beveiliging en bewaking](https://docs.openshift.com/container-platform/4.4/migration/migrating_3_4/planning-migration-3-to-4.html).

In dit artikel laten we zien hoe u migreert van een Azure Red Hat open Shift 3,11-cluster naar een Azure Red Hat 4-cluster.

> [!NOTE]
> Red Hat open Shift migratie hulpprogramma's, zoals het hulp programma voor migratie van de besturings elementen voor het beheer van de hulp middelen voor het migreren van het cluster (CAM), kunnen niet worden gebruikt met Azure Red Hat open Shift 3,11-clusters.

## <a name="before-you-begin"></a>Voordat u begint

In dit artikel wordt ervan uitgegaan dat u een bestaand Azure Red Hat open Shift 3,11-cluster hebt.

## <a name="create-a-target-azure-red-hat-openshift-4-cluster"></a>Een Azure Red Hat open Shift 4-cluster maken

Maak eerst [de Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md) die u wilt gebruiken als het doel cluster. Hier gebruiken we de basis configuratie. Als u geïnteresseerd bent in verschillende instellingen, raadpleegt u de [zelf studie een Azure Red Hat open Shift 4-cluster maken](tutorial-create-cluster.md).

Maak een virtueel netwerk met twee lege subnetten voor de Master-en worker-knoop punten.

```azurecli-interactive
    az network vnet create \
    --resource-group $RESOURCEGROUP \
    --name aro-vnet \
    --address-prefixes 10.0.0.0/22

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name master-subnet \
    --address-prefixes 10.0.0.0/23 \
    --service-endpoints Microsoft.ContainerRegistry

    az network vnet subnet create \
    --resource-group $RESOURCEGROUP \
    --vnet-name aro-vnet \
    --name worker-subnet \
    --address-prefixes 10.0.2.0/23 \
    --service-endpoints Microsoft.ContainerRegistry
```

Gebruik vervolgens de volgende opdracht om het cluster te maken.

```azurecli-interactive
az aro create \
  --resource-group $RESOURCEGROUP \
  --name $CLUSTER \
  --vnet aro-vnet \
  --master-subnet master-subnet \
  --worker-subnet worker-subnet \
  # --domain foo.example.com # [OPTIONAL] custom domain
  # --pull-secret @pull-secret.txt # [OPTIONAL]
```

## <a name="configure-the-target-openshift-4-cluster"></a>De doel-open ploeg 4-cluster configureren

### <a name="authentication"></a>Verificatie

Om gebruikers te laten communiceren met Azure Red Hat open Shift, moeten ze eerst worden geverifieerd bij het cluster. De verificatie laag identificeert de gebruiker die is gekoppeld aan aanvragen voor de open Shift API van Azure. De autorisatie laag gebruikt vervolgens informatie over de gebruiker die de aanvraag heeft ingediend om te bepalen of het verzoek is toegestaan.

Wanneer een Azure Red Hat open Shift 4-cluster wordt gemaakt, wordt er een tijdelijke gebruiker met beheerders rechten gemaakt. [Verbinding maken met uw cluster](tutorial-connect-cluster.md), gebruikers en groepen toevoegen en [de juiste machtigingen configureren](https://docs.openshift.com/container-platform/4.6/authentication/understanding-authentication.html) voor beide.

### <a name="networking"></a>Netwerken

Azure Red Hat open Shift 4 maakt gebruik van een aantal verschillende Opera tors voor het instellen van het netwerk in uw cluster: [cluster netwerk operator](https://docs.openshift.com/container-platform/4.6/networking/cluster-network-operator.html#nw-cluster-network-operator_cluster-network-operator), [DNS-operator](https://docs.openshift.com/container-platform/4.6/networking/dns-operator.html)en de [ingangs operator](https://docs.openshift.com/container-platform/4.6/networking/ingress-operator.html). Zie het [netwerk diagram](concepts-networking.md) en [informatie over netwerken](https://docs.openshift.com/container-platform/4.6/networking/understanding-networking.html)voor meer informatie over het instellen van netwerken in een Azure Red Hat open Shift 4-cluster.

### <a name="storage"></a>Storage
Azure Red Hat open Shift 4 ondersteunt de volgende PersistentVolume-invoeg toepassingen:

- AWS Elastic Block Store (EBS)
- Azure-schijf
- Azure-bestand
- GCE permanente schijf
- HostPath
- iSCSI
- Lokaal volume
- NFS
- Red Hat open Shift container Storage

Zie voor meer informatie over het configureren van deze opslag typen [permanente opslag configureren](https://access.redhat.com/documentation/azure_red_hat_openshift/4/html/storage/configuring-persistent-storage).

### <a name="registry"></a>Register

Azure Red Hat open Shift 4 kan installatie kopieën bouwen van uw bron code, deze implementeren en hun levens cyclus beheren. Om dit mogelijk te maken, biedt Azure Red Hat open Shift vier een [intern, geïntegreerd REGI ster voor container installatie kopieën](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html) dat kan worden geïmplementeerd in uw Azure Red Hat open Shift-omgeving om installatie kopieën lokaal te beheren.

Als u externe registers gebruikt, zoals [Azure container Registry](../container-registry/index.yml), [Red Hat kade](ttps://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-quay-overview_registry-options)-registers of een rood Hat-bestand met [verificatie mogelijkheden](https://docs.openshift.com/container-platform/4.6/registry/registry-options.html#registry-authentication-enabled-registry-overview_registry-options), voert u de stappen uit om referenties aan het cluster toe te voegen zodat het cluster toegang heeft tot de opslag plaatsen.

### <a name="monitoring"></a>Bewaking

Azure Red Hat open Shift bevat een vooraf geconfigureerde, vooraf geïnstalleerde en automatisch bijgewerkte bewakings stack die is gebaseerd op het open-source project Prometheus en het bredere milieu systeem. Het biedt bewaking van cluster onderdelen en bevat een set waarschuwingen om de Cluster beheerder onmiddellijk op de hoogte te stellen van de problemen die zich voordoen en een set Grafana-Dash boards. De cluster bewakings stack wordt alleen ondersteund voor het bewaken van Azure Red Hat open Shift-clusters. Zie [cluster monitoring voor Azure Red Hat open Shift](https://docs.openshift.com/container-platform/4.6/monitoring/understanding-the-monitoring-stack.html)voor meer informatie.

Als u Azure Monitor hebt gebruikt [voor containers voor Azure Red Hat open shift 3,11](../azure-monitor/containers/container-insights-azure-redhat-setup.md), kunt u ook Azure monitor inschakelen voor containers voor [Azure Red Hat open Shift 4-clusters](../azure-monitor/containers/container-insights-azure-redhat4-setup.md) en door gaan met het gebruik van dezelfde log Analytics-werk ruimte.

## <a name="move-your-dns-or-load-balancer-configuration-to-the-new-cluster"></a>Uw DNS-of Load Balancer-configuratie verplaatsen naar het nieuwe cluster

Als u Azure Traffic Manager gebruikt, voegt u eind punten toe om te verwijzen naar uw doel cluster en de prioriteit van deze eind punten te bepalen.

## <a name="deploy-application-to-your-target-cluster"></a>Toepassing implementeren in uw doel cluster

Zodra u het doel cluster correct hebt geconfigureerd voor uw werk belasting, [maakt u verbinding met uw cluster](tutorial-connect-cluster.md) en maakt u de benodigde toepassingen, onderdelen of services voor uw projecten. Met Azure Red Hat open Shift kunt u deze maken op basis van Git, container installatie kopieën, de Red Hat Developer Catalog, een Dockerfile, een YAML/JSON-definitie of door een database service te selecteren in de catalogus.

## <a name="delete-your-source-cluster"></a>Het bron cluster verwijderen
Nadat u hebt bevestigd dat uw Azure Red Hat open Shift 4-cluster correct is ingesteld, verwijdert u uw Azure Red Hat open Shift 3,11-cluster.

```
az openshift delete --name $CLUSTER_NAME
                    --resource-group $RESOURCE_GROUP
                    [--no-wait]
                    [--subscription]
                    [--yes]
```
## <a name="next-steps"></a>Volgende stappen
Bekijk [hier](https://docs.openshift.com/container-platform/4.6/welcome/index.html)de Red Hat open Shift-documentatie.