---
title: Een door de klant beheerde sleutel gebruiken om Azure-schijven te versleutelen in Azure Kubernetes Service (AKS)
description: Bring Your Own Keys (BYOK) om AKS-besturingssysteem- en gegevensschijven te versleutelen.
services: container-service
ms.topic: article
ms.date: 09/01/2020
ms.openlocfilehash: c5c555d7eb5142f5f41f65b24f754c65450a2713
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107776187"
---
# <a name="bring-your-own-keys-byok-with-azure-disks-in-azure-kubernetes-service-aks"></a>Bring Your Own Keys (BYOK) met Azure-schijven in Azure Kubernetes Service (AKS)

Azure Storage versleutelt alle gegevens in een opslagaccount at rest. Gegevens worden standaard versleuteld met door Microsoft beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u door de klant beheerde sleutels leveren voor versleuteling in rust voor zowel het besturingssysteem als de gegevensschijven voor uw AKS-clusters. Meer informatie over door de klant beheerde sleutels in [Linux][customer-managed-keys-linux] en [Windows.][customer-managed-keys-windows]

## <a name="limitations"></a>Beperkingen
* Ondersteuning voor gegevensschijfversleuteling is beperkt tot AKS-clusters met Kubernetes versie 1.17 en hoger.
* Versleuteling van het besturingssysteem en de gegevensschijf met door de klant beheerde sleutels kunnen alleen worden ingeschakeld bij het maken van een AKS-cluster.

## <a name="prerequisites"></a>Vereisten
* U moet bij het gebruik  van Azure Key Vault voor het versleutelen van beheerde schijven de beveiliging voor Key Vault verwijderen en ops manier inschakelen.
* U hebt azure CLI versie 2.11.1 of hoger nodig.

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault maken

Gebruik een Azure Key Vault om uw sleutels op te slaan.  U kunt eventueel de Azure Portal door de klant [beheerde sleutels configureren met Azure Key Vault][byok-azure-portal]

Maak een nieuwe *resourcegroep,* maak vervolgens een nieuw *Key Vault* en schakel de beveiliging voor zacht verwijderen en ops manier in.  Zorg ervoor dat u voor elke opdracht dezelfde regio- en resourcegroepnamen gebruikt.

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

## <a name="create-an-instance-of-a-diskencryptionset"></a>Een exemplaar van een DiskEncryptionSet maken

Vervang *myKeyVaultName* door de naam van uw sleutelkluis.  U hebt ook een sleutel *nodig die* is opgeslagen in Azure Key Vault volgende stappen uit te voeren.  Sla uw bestaande sleutel op in de Key Vault die u [][key-vault-generate] in de vorige stappen hebt gemaakt, of genereer een nieuwe sleutel en vervang *myKeyName* hieronder door de naam van uw sleutel.
    
```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId=$(az keyvault show --name myKeyVaultName --query "[id]" -o tsv)

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl=$(az keyvault key show --vault-name myKeyVaultName  --name myKeyName  --query "[key.kid]" -o tsv)

# Create a DiskEncryptionSet
az disk-encryption-set create -n myDiskEncryptionSetName  -l myAzureRegionName  -g myResourceGroup --source-vault $keyVaultId --key-url $keyVaultKeyUrl 
```

## <a name="grant-the-diskencryptionset-access-to-key-vault"></a>De DiskEncryptionSet toegang verlenen tot de sleutelkluis

Gebruik de DiskEncryptionSet en de resourcegroepen die u in de vorige stappen hebt gemaakt en verleen de resource DiskEncryptionSet toegang tot de Azure Key Vault.

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
desIdentity=$(az disk-encryption-set show -n myDiskEncryptionSetName  -g myResourceGroup --query "[identity.principalId]" -o tsv)

# Update security policy settings
az keyvault set-policy -n myKeyVaultName -g myResourceGroup --object-id $desIdentity --key-permissions wrapkey unwrapkey get
```

## <a name="create-a-new-aks-cluster-and-encrypt-the-os-disk"></a>Een nieuw AKS-cluster maken en de besturingssysteemschijf versleutelen

Maak een **nieuwe resourcegroep en** een AKS-cluster en gebruik vervolgens uw sleutel om de besturingssysteemschijf te versleutelen. Door de klant beheerde sleutels worden alleen ondersteund in Kubernetes-versies die hoger zijn dan 1.17. 

> [!IMPORTANT]
> Zorg ervoor dat u een nieuwe resoruce-groep voor uw AKS-cluster maakt

```azurecli-interactive
# Retrieve the DiskEncryptionSet value and set a variable
diskEncryptionSetId=$(az disk-encryption-set show -n mydiskEncryptionSetName -g myResourceGroup --query "[id]" -o tsv)

# Create a resource group for the AKS cluster
az group create -n myResourceGroup -l myAzureRegionName

# Create the AKS cluster
az aks create -n myAKSCluster -g myResourceGroup --node-osdisk-diskencryptionset-id $diskEncryptionSetId --kubernetes-version KUBERNETES_VERSION --generate-ssh-keys
```

Wanneer er nieuwe knooppuntgroepen worden toegevoegd aan het hierboven gemaakte cluster, wordt de door de klant beheerde sleutel gebruikt om de besturingssysteemschijf te versleutelen.

## <a name="encrypt-your-aks-cluster-data-diskoptional"></a>Uw AKS-clustergegevensschijf versleutelen (optioneel)
De versleutelingssleutel van de besturingssysteemschijf wordt gebruikt voor het versleutelen van de gegevensschijf als er geen sleutel is opgegeven voor de gegevensschijf uit v1.17.2 en u kunt AKS-gegevensschijven ook versleutelen met uw andere sleutels.

> [!IMPORTANT]
> Zorg ervoor dat u de juiste AKS-referenties hebt. De beheerde identiteit moet inzenderstoegang hebben tot de resourcegroep waarin de diskencryptionset is geïmplementeerd. Anders krijgt u een foutmelding dat de beheerde identiteit geen machtigingen heeft.

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

Maak een bestand met de **naam byok-azure-disk.yaml** dat de volgende informatie bevat.  Vervang myAzureSubscriptionId, myResourceGroup en myDiskEncrptionSetName door uw waarden en pas de yaml toe.  Zorg ervoor dat u de resourcegroep gebruikt waarin uw DiskEncryptionSet is geïmplementeerd.  Als u de Azure Cloud Shell gebruikt, kan dit bestand worden gemaakt met behulp van vi of nano, alsof het werkt op een virtueel of fysiek systeem:

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

Best [practices voor AKS-clusterbeveiliging bekijken][best-practices-security]

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ./operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys-windows]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[customer-managed-keys-linux]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions