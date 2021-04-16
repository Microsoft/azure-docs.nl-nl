---
title: Een back-up van Azure Red Hat OpenShift 4-clustertoepassing maken met behulp van Velero
description: Meer informatie over het maken van een back-up van uw Azure Red Hat OpenShift clustertoepassingen met behulp van Velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: aro, openshift, az aro, red hat, cli
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: c8bf722bd77372cd89e7c64757347b5fd07eb1ed
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107481358"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Een toepassingsback-Azure Red Hat OpenShift 4-cluster maken

In dit artikel bereidt u uw omgeving voor op het maken van een back-up van Azure Red Hat OpenShift 4 clustertoepassing. U leert het volgende:

> [!div class="checklist"]
> * De vereisten instellen en de benodigde hulpprogramma's installeren
> * Een back-up van Azure Red Hat OpenShift 4-toepassing maken

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="install-velero"></a>Velero installeren

Als [u](https://velero.io/docs/main/basic-install/) Velero op uw systeem wilt installeren, volgt u het aanbevolen proces voor uw besturingssysteem.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Azure Storage-account en Blob-container instellen

Met deze stap maakt u een resourcegroep buiten de resourcegroep van het ARO-cluster.  Met deze resourcegroep kunnen de back-ups blijven bestaan en kunnen toepassingen worden hersteld naar nieuwe clusters.

```bash
AZURE_BACKUP_RESOURCE_GROUP=Velero_Backups
az group create -n $AZURE_BACKUP_RESOURCE_GROUP --location eastus

AZURE_STORAGE_ACCOUNT_ID="velero$(uuidgen | cut -d '-' -f5 | tr '[A-Z]' '[a-z]')"
az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_ID \
    --resource-group $AZURE_BACKUP_RESOURCE_GROUP \
    --sku Standard_GRS \
    --encryption-services blob \
    --https-only true \
    --kind BlobStorage \
    --access-tier Hot

BLOB_CONTAINER=velero
az storage container create -n $BLOB_CONTAINER --public-access off --account-name $AZURE_STORAGE_ACCOUNT_ID
```

## <a name="set-permissions-for-velero"></a>Machtigingen instellen voor Velero

### <a name="create-service-principal"></a>Een service-principal maken

Velero heeft machtigingen nodig voor het maken van back-ups en herstel. Wanneer u een service-principal maakt, geeft u Velero toestemming voor toegang tot de resourcegroep die u in de vorige stap hebt definiëren. Met deze stap wordt de resourcegroep van het cluster op halen:

```bash
export AZURE_RESOURCE_GROUP=$(az aro show --name <name of cluster> --resource-group <name of resource group> | jq -r .clusterProfile.resourceGroupId | cut -d '/' -f 5,5)
```


```bash
AZURE_SUBSCRIPTION_ID=$(az account list --query '[?isDefault].id' -o tsv)

AZURE_TENANT_ID=$(az account list --query '[?isDefault].tenantId' -o tsv)
```

```bash
AZURE_CLIENT_SECRET=$(az ad sp create-for-rbac --name "velero" --role "Contributor" --query 'password' -o tsv \
--scopes  /subscriptions/$AZURE_SUBSCRIPTION_ID)
AZURE_CLIENT_ID=$(az ad sp list --display-name "velero" --query '[0].appId' -o tsv)

```

```bash
cat << EOF  > ./credentials-velero.yaml
AZURE_SUBSCRIPTION_ID=${AZURE_SUBSCRIPTION_ID}
AZURE_TENANT_ID=${AZURE_TENANT_ID}
AZURE_CLIENT_ID=${AZURE_CLIENT_ID}
AZURE_CLIENT_SECRET=${AZURE_CLIENT_SECRET}
AZURE_RESOURCE_GROUP=${AZURE_RESOURCE_GROUP}
AZURE_CLOUD_NAME=AzurePublicCloud
EOF
```

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Velero installeren op Azure Red Hat OpenShift 4-cluster

Met deze stap installeert u Velero in een eigen project en de aangepaste [resourcedefinities](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) die nodig zijn voor back-ups en herstel met Velero. Zorg ervoor dat u bent aangemeld bij een Azure Red Hat OpenShift v4-cluster.


```bash
velero install \
--provider azure \
--plugins velero/velero-plugin-for-microsoft-azure:v1.1.0 \
--bucket $BLOB_CONTAINER \
--secret-file ~/path/to/credentials-velero.yaml \
--backup-location-config resourceGroup=$AZURE_BACKUP_RESOURCE_GROUP,storageAccount=$AZURE_STORAGE_ACCOUNT_ID \
--snapshot-location-config apiTimeout=15m \
--velero-pod-cpu-limit="0" --velero-pod-mem-limit="0" \
--velero-pod-mem-request="0" --velero-pod-cpu-request="0"
```

## <a name="create-a-backup-with-velero"></a>Een back-up maken met Velero

Als u een back-up van een toepassing wilt maken met Velero, moet u de naamruimte opnemen waarin deze toepassing zich in zich.  Als u een naamruimte hebt en alle resources in die naamruimte in de back-up wilt opnemen, moet u `nginx-example` de volgende opdracht uitvoeren in de terminal:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
U kunt de status van de back-up controleren door het volgende uit te doen:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Een geslaagde back-up wordt `phase:Completed` uitgevoerd en de objecten worden in de container in het opslagaccount geplaatst.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Een back-up maken met Velero om momentopnamen op te nemen

Als u een toepassingsback-up wilt maken met Velero om de permanente volumes van uw toepassing op te nemen, moet u de naamruimte opnemen waarin de toepassing zich in zich heeft en de vlag opnemen bij het maken van de `snapshot-volumes=true` back-up

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

U kunt de status van de back-up controleren door het volgende uit te doen:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Een geslaagde back-up met `phase:Completed` uitvoer en de objecten worden in de container in het opslagaccount geplaatst.

Zie Backup OpenShift resources the native way (Back-up maken van [OpenShift-resources](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) op de systeemeigen manier) voor meer informatie over het maken van back-ups en herstels met velero

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een Azure Red Hat OpenShift 4-clustertoepassing geback-upt. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een back-up van een OpenShift v4-clustertoepassing maken met velero
> * Een back-up van een OpenShift v4-clustertoepassing maken met momentopnamen met behulp van Velero


Lees het volgende artikel voor meer informatie over het maken van een Azure Red Hat OpenShift herstellen van een clustertoepassing met vier clusters.

* [Herstel van een Azure Red Hat OpenShift 4-clustertoepassing maken](howto-create-a-restore.md)
* [Herstel van een Azure Red Hat OpenShift 4-clustertoepassing maken, inclusief momentopnamen](howto-create-a-restore.md)
