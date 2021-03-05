---
title: Een Azure Red Hat open Shift 4-cluster toepassing maken met behulp van velero
description: Meer informatie over het maken van een back-up van uw Azure Red Hat open Shift-cluster toepassingen met velero
ms.service: azure-redhat-openshift
ms.topic: article
ms.date: 06/22/2020
author: troy0820
ms.author: b-trconn
keywords: Aro, open Shift, AZ Aro, Red Hat, cli
ms.custom: mvc
ms.openlocfilehash: 42ad24e4421b75a3942879bcac8f99b3c8b8138a
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102180952"
---
# <a name="create-an-azure-red-hat-openshift-4-cluster-application-backup"></a>Een Azure Red Hat open Shift 4-cluster toepassing back-up maken

In dit artikel maakt u een voor bereiding van uw omgeving voor het maken van een back-up van Azure Red Hat open Shift 4-cluster toepassing. U leert het volgende:

> [!div class="checklist"]
> * De vereisten instellen en de benodigde hulpprogram ma's installeren
> * Een Azure Red Hat open Shift 4-toepassing maken

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

### <a name="install-velero"></a>Velero installeren

Als u Velero op uw systeem wilt [installeren](https://velero.io/docs/main/basic-install/) , volgt u het aanbevolen proces voor uw besturings systeem.

### <a name="set-up-azure-storage-account-and-blob-container"></a>Azure Storage-account en BLOB-container instellen

Met deze stap maakt u een resource groep buiten de resource groep van het ARO-cluster.  Met deze resource groep kunnen back-ups persistent worden gemaakt en toepassingen naar nieuwe clusters herstellen.

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

## <a name="set-permissions-for-velero"></a>Machtigingen instellen voor velero

### <a name="create-service-principal"></a>Een service-principal maken

Velero heeft machtigingen nodig om back-ups te maken en op te slaan. Wanneer u een Service-Principal maakt, geeft u Velero toestemming om toegang te krijgen tot de resource groep die u in de vorige stap hebt gedefinieerd. In deze stap wordt de resource groep van het cluster opgehaald:

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

## <a name="install-velero-on-azure-red-hat-openshift-4-cluster"></a>Velero installeren op Azure Red Hat open Shift 4-cluster

Met deze stap wordt Velero geïnstalleerd in een eigen project en de [aangepaste resource definities](https://kubernetes.io/docs/tasks/extend-kubernetes/custom-resources/custom-resource-definitions/) die nodig zijn om back-ups te maken en op te slaan met Velero. Zorg ervoor dat u bent aangemeld bij een Azure Red Hat open Shift v4-cluster.


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

## <a name="create-a-backup-with-velero"></a>Een back-up maken met velero

Als u een back-up van een toepassing met Velero wilt maken, moet u de naam ruimte waarin deze toepassing zich bevindt, toevoegen.  Als u een `nginx-example` naam ruimte hebt en alle resources in die naam ruimte wilt toevoegen aan de back-up, voert u de volgende opdracht uit in de terminal:

```bash
velero create backup <name of backup> --include-namespaces=nginx-example
```
U kunt de status van de back-up controleren door het volgende uit te voeren:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Een geslaagde back-up wordt uitgevoerd `phase:Completed` en de objecten bevinden zich in de container in het opslag account.

## <a name="create-a-backup-with-velero-to-include-snapshots"></a>Maak een back-up met Velero om moment opnamen op te nemen

Als u een back-up van een toepassing wilt maken met Velero om de permanente volumes van uw toepassing op te nemen, moet u de naam ruimte die de toepassing bevat en de `snapshot-volumes=true` vlag toevoegen bij het maken van de back-up.

```bash
velero backup create <name of backup> --include-namespaces=nginx-example --snapshot-volumes=true --include-cluster-resources=true
```

U kunt de status van de back-up controleren door het volgende uit te voeren:

```bash
oc get backups -n velero <name of backup> -o yaml
```

Een geslaagde back-up met uitvoer `phase:Completed` en de objecten bevinden zich in de container in het opslag account.

Voor meer informatie over het maken van back-ups en herstel bewerkingen met Velero raadpleegt u [de systeem eigen manier van back-ups](https://www.openshift.com/blog/backup-openshift-resources-the-native-way) .

## <a name="next-steps"></a>Volgende stappen

In dit artikel is een back-up gemaakt van een Azure Red Hat open Shift 4-cluster toepassing. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een open Shift v4-cluster toepassing maken met behulp van velero
> * Een open Shift v4-cluster toepassing back-up maken met moment opnamen met behulp van velero


Ga naar het volgende artikel voor meer informatie over het maken van een Azure Red Hat open Shift 4-cluster toepassing herstellen.

* [Een Azure Red Hat open Shift 4-cluster toepassing herstellen](howto-create-a-restore.md)
* [Een Azure Red Hat open Shift 4-cluster toepassing herstellen met inbegrip van moment opnamen](howto-create-a-restore.md)
