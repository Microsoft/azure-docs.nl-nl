---
title: Een Azure Files StorageClass maken in Azure Red Hat OpenShift 4
description: Meer informatie over het maken van een Azure Files StorageClass in azure Red Hat open Shift
ms.service: container-service
ms.topic: article
ms.date: 10/16/2020
author: grantomation
ms.author: b-grodel
keywords: Aro, open Shift, AZ Aro, Red Hat, CLI, Azure file
ms.custom: mvc, devx-track-azurecli
ms.openlocfilehash: db87e61166da87300a5e1fb8433a42f3f47cf832
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96493821"
---
# <a name="create-an-azure-files-storageclass-on-azure-red-hat-openshift-4"></a>Een Azure Files StorageClass maken in Azure Red Hat OpenShift 4

In dit artikel maakt u een StorageClass voor Azure Red Hat open Shift 4 waarmee de opslag van ReadWriteMany (LSU) dynamisch wordt ingericht met behulp van Azure Files. U leert het volgende:

> [!div class="checklist"]
> * De vereisten instellen en de benodigde hulpprogram ma's installeren
> * Een Azure Red Hat open Shift 4-StorageClass maken met de Azure-bestands inrichting

Als u ervoor kiest om de CLI lokaal te installeren en te gebruiken, moet u Azure CLI 2.6.0 of hoger gebruiken voor deze zelfstudie. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli?view=azure-cli-latest) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="before-you-begin"></a>Voordat u begint

Een Azure Red Hat open Shift 4-cluster implementeren in uw abonnement, Zie [een Azure Red Hat open Shift 4-cluster maken](tutorial-create-cluster.md)


### <a name="set-up-azure-storage-account"></a>Azure Storage-account instellen

Met deze stap maakt u een resource groep buiten de resource groep van het Azure Red Hat open Shift (ARO)-cluster. Deze resource groep bevat de Azure Files-shares die zijn gemaakt door de dynamische inrichting van Azure Red Hat open SHIFT.

```bash
AZURE_FILES_RESOURCE_GROUP=aro_azure_files
LOCATION=eastus

az group create -l $LOCATION -n $AZURE_FILES_RESOURCE_GROUP

AZURE_STORAGE_ACCOUNT_NAME=aroazurefilessa

az storage account create \
    --name $AZURE_STORAGE_ACCOUNT_NAME \
    --resource-group $AZURE_FILES_RESOURCE_GROUP \
    --kind StorageV2 \
    --sku Standard_LRS
```

## <a name="set-permissions"></a>Machtigingen instellen
### <a name="set-resource-group-permissions"></a>Machtigingen voor de resource groep instellen

Voor de Service-Principal ARO is de machtiging ' Listkeys ophalen ' vereist voor de nieuwe resource groep voor het Azure-opslag account. Wijs de rol Inzender toe om dit te krijgen. 

```bash
ARO_RESOURCE_GROUP=aro-rg
CLUSTER=cluster
ARO_SERVICE_PRINCIPAL_ID=$(az aro show -g $ARO_RESOURCE_GROUP -n $CLUSTER –-query servicePrincipalProfile.clientId -o tsv)

az role assignment create –-role Contributor -–assignee $ARO_SERVICE_PRINCIPAL_ID -g $AZURE_FILES_RESOURCE_GROUP
```

### <a name="set-aro-cluster-permissions"></a>ARO-cluster machtigingen instellen

Voor het permanente volume Binder-service account van open Shift is het mogelijk om geheimen te lezen. Maak een open ploeg cluster rol en wijs deze toe om dit te krijgen.
```bash
ARO_API_SERVER=$(az aro list --query "[?contains(name,'$CLUSTER')].[apiserverProfile.url]" -o tsv)

oc login -u kubeadmin -p $(az aro list-credentials -g $ARO_RESOURCE_GROUP -n $CLUSTER --query=kubeadminPassword -o tsv) $APISERVER

oc create clusterrole azure-secret-reader \
    --verb=create,get \
    --resource=secrets

oc adm policy add-cluster-role-to-user azure-secret-reader system:serviceaccount:kube-system:persistent-volume-binder
```

## <a name="create-storageclass-with-azure-files-provisioner"></a>StorageClass maken met Azure Files inrichting

Met deze stap maakt u een StorageClass met een Azure Files-inrichting. In het StorageClass-manifest zijn de details van het opslag account vereist zodat het ARO-cluster weet dat er een opslag account buiten de huidige resource groep moet worden weer geven.

```bash
cat << EOF >> azure-storageclass-azure-file.yaml
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: azure-file
provisioner: kubernetes.io/azure-file
parameters:
  location: $LOCATION
  skuName: Standard_LRS 
  storageAccount: $AZURE_STORAGE_ACCOUNT_NAME
  resourceGroup: $AZURE_FILES_RESOURCE_GROUP
reclaimPolicy: Delete
volumeBindingMode: Immediate
EOF

oc create -f azure-storageclass-azure-file.yaml
```

## <a name="change-the-default-storageclass-optional"></a>De standaard StorageClass wijzigen (optioneel)

De standaard StorageClass op ARO wordt Managed-Premium genoemd en maakt gebruik van de Azure-Disk provisioner. Wijzig dit door de patch-opdrachten uit te geven op basis van de StorageClass-manifesten.

```bash
oc patch storageclass managed-premium -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"false"}}}'

oc patch storageclass azure-file -p '{"metadata": {"annotations":{"storageclass.kubernetes.io/is-default-class":"true"}}}'
```

## <a name="verify-azure-file-storageclass-optional"></a>Azure file StorageClass verifiëren (optioneel)

Maak een nieuwe toepassing en wijs er opslag aan toe.

```bash
oc new-project azfiletest
oc new-app –template httpd-example

#Wait for the pod to become Ready
curl $(oc get route httpd-example -n azfiletest -o jsonpath={.spec.host})

oc set volume dc/httpd-example --add --name=v1 -t pvc --claim-size=1G -m /data

#Wait for the new deployment to rollout
export POD=$(oc get pods --field-selector=status.phase==Running -o jsonpath={.items[].metadata.name})
oc exec $POD -- bash -c "mkdir ./data"
oc exec $POD -- bash -c "echo 'azure file storage' >> /data/test.txt"

oc exec $POD -- bash -c "cat /data/test.txt"
azure file storage
```
Het test.txt bestand wordt ook weer gegeven via de Storage Explorer in de Azure Portal. 

## <a name="next-steps"></a>Volgende stappen

In dit artikel hebt u dynamische permanente opslag gemaakt met behulp van Microsoft Azure-bestanden en Azure Red Hat open Shift 4. U hebt geleerd hoe u:

> [!div class="checklist"]
> * Een opslagaccount maken
> * Een StorageClass configureren op een Azure Red Hat open Shift 4-cluster met behulp van de Azure Files-inrichting

Ga naar het volgende artikel voor meer informatie over de ondersteunde bronnen van Azure Red Hat open Shift 4.

* [Ondersteunings beleid voor Azure Red Hat open Shift](support-policies-v4.md)