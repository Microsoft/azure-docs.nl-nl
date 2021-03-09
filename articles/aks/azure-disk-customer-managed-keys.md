---
title: Een door de klant beheerde sleutel gebruiken voor het versleutelen van Azure-schijven in azure Kubernetes service (AKS)
description: Gebruik uw eigen sleutels (BYOK) om AKS-besturings systeem en gegevens schijven te versleutelen.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: 4b1c311132cc812ccb2bbbc95c4b7414b108008c
ms.sourcegitcommit: 15d27661c1c03bf84d3974a675c7bd11a0e086e6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/09/2021
ms.locfileid: "102499200"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Breng uw eigen sleutels (BYOK) met Azure-schijven in azure Kubernetes service (AKS)

Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor versleuteling in rust voor het besturings systeem en de gegevens schijven voor uw AKS-clusters. Meer informatie over door de klant beheerde sleutels in [Linux][customer-managed-keys-linux] en [Windows][customer-managed-keys-windows].

## <a name="limitations"></a>Beperkingen
* Ondersteuning voor gegevens schijf versleuteling is beperkt tot AKS-clusters met Kubernetes versie 1,17 en hoger.
* Versleuteling van besturings systeem en gegevens schijf met door de klant beheerde sleutels kan alleen worden ingeschakeld bij het maken van een AKS-cluster.

## <a name="prerequisites"></a>Vereisten
* U moet de beveiliging voor voorlopig verwijderen en leegmaken inschakelen voor *Azure Key Vault* wanneer u Key Vault gebruikt om beheerde schijven te versleutelen.
* U hebt de Azure CLI-versie 2.11.1 of hoger nodig.

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault-exemplaar maken

Gebruik een Azure Key Vault-exemplaar om uw sleutels op te slaan.  U kunt eventueel de Azure Portal gebruiken om [door de klant beheerde sleutels te configureren met Azure Key Vault][byok-azure-portal]

Maak een nieuwe *resource groep*, maak een nieuw *Key Vault* -exemplaar en schakel de beveiliging voorlopig verwijderen en leegmaken in.  Zorg ervoor dat u dezelfde regio en de namen van de resource groepen voor elke opdracht gebruikt.

```azurecli-interactive
# Optionally retrieve Azure region short names for use on upcoming commands
az account list-locations
```

```azurecli-interactive
# Create new resource group in a supported Azure region
az group create -l myAzureRegionName -n myResourceGroup

# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n myKeyVaultName -g myResourceGroup -l myAzureRegionName  --enable-purge-protection true --enable-soft-delete true
```

## <a name="create-an-instance-of-a-diskencryptionset"></a>Een instantie van een DiskEncryptionSet maken

Vervang *myKeyVaultName* door de naam van uw sleutel kluis.  U hebt ook een *sleutel* nodig die is opgeslagen in azure Key Vault om de volgende stappen uit te voeren.  Sla uw bestaande sleutel op in het Key Vault dat u in de vorige stappen hebt gemaakt of [Genereer een nieuwe sleutel][key-vault-generate] en vervang *myKeyName* hieronder door de naam van uw sleutel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>De toegang tot de DiskEncryptionSet verlenen aan de sleutel kluis

Gebruik de DiskEncryptionSet-en resource groepen die u in de voor gaande stappen hebt gemaakt en verleen de DiskEncryptionSet-resource toegang tot de Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Een nieuw AKS-cluster maken en de besturingssysteem schijf versleutelen

Maak een **nieuwe resource groep** en AKS-cluster en gebruik vervolgens uw sleutel om de besturingssysteem schijf te versleutelen. Door de klant beheerde sleutels worden alleen ondersteund in versies van Kubernetes die groter zijn dan 1,17. 

> [!IMPORTANT]
> Zorg ervoor dat u een nieuwe resource-groep voor uw AKS-cluster maakt

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Wanneer er nieuwe knooppunt groepen worden toegevoegd aan het hierboven gemaakte cluster, wordt de door de klant beheerde sleutel die tijdens het maken is gegeven, gebruikt voor het versleutelen van de besturingssysteem schijf.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Uw AKS-cluster gegevens schijf versleutelen (optioneel)
De coderings sleutel van de besturingssysteem schijf wordt gebruikt voor het versleutelen van de gegevens schijf als er geen sleutel wordt gegeven voor de gegevens schijf van v 1.17.2, en u kunt ook AKS-gegevens schijven met uw andere sleutels versleutelen.

> [!IMPORTANT]
> Zorg ervoor dat u beschikt over de juiste AKS-referenties. De beheerde identiteit moet Inzender toegang hebben tot de resource groep waar de diskencryptionset wordt geïmplementeerd. Anders krijgt u een fout melding dat de beheerde identiteit geen machtigingen heeft.

```azurecli-interactive
# Retrieve your Azure Subscription Id from id property as shown below
az account list
```

```
someuser@Azure:~$ az account list
[
  {
    "cloudName": "AzureCloud",
    "id": "666e66d8-1e43-4136-be25-f25bb5de5893",
    "isDefault": true,
    "name": "MyAzureSubscription",
    "state": "Enabled",
    "tenantId": "3ebbdf90-2069-4529-a1ab-7bdcb24df7cd",
    "user": {
      "cloudShellID": true,
      "name": "someuser@azure.com",
      "type": "user"
    }
  }
]
```

Maak een bestand met de naam **byok-Azure-disk. yaml** dat de volgende informatie bevat.  Vervang myAzureSubscriptionId, myResourceGroup en myDiskEncrptionSetName door uw waarden en pas de yaml toe.  Zorg ervoor dat u de resource groep gebruikt waarin uw DiskEncryptionSet wordt geïmplementeerd.  Als u de Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van VI of nano alsof het werkt op een virtueel of fysiek systeem:

```
kind: StorageClass
apiVersion: storage.k8s.io/v1  
metadata:
  name: hdd
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Standard_LRS
  kind: managed
  diskEncryptionSetID: "/subscriptions/{myAzureSubscriptionId}/resourceGroups/{myResourceGroup}/providers/Microsoft.Compute/diskEncryptionSets/{myDiskEncryptionSetName}"
```
Voer vervolgens deze implementatie uit in uw AKS-cluster:
```azurecli-interactive
# Get credentials
az aks get-credentials --name myAksCluster --resource-group myResourceGroup --output table

# Update cluster
kubectl apply -f byok-azure-disk.yaml
```

## <a name="next-steps"></a>Volgende stappen

[Aanbevolen procedures voor AKS-cluster beveiliging][best-practices-security] controleren

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions