---
title: Permanente volumeclaims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat OpenShift (ARO)
description: ByOK (Bring Your Own Key) / DOOR de klant beheerde sleutel (CMK) implementeren instructies voor Azure Red Hat OpenShift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: versleuteling, byok, aro, cmk, openshift, red hat
ms.openlocfilehash: f6c80bab6f821dc7c85352bf57ebe255ae712d43
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783517"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Permanente volumeclaims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat OpenShift (ARO) (preview)

Azure Storage versleuteling aan de serverzijde (SSE) gebruikt om uw gegevens automatisch te versleutelen wanneer deze naar de cloud worden opgeslagen. [](../storage/common/storage-service-encryption.md) Gegevens worden standaard versleuteld met door het Microsoft-platform beheerde sleutels. Voor extra controle over versleutelingssleutels kunt u uw eigen door de klant beheerde sleutels leveren voor het versleutelen van gegevens in Azure Red Hat OpenShift clusters.

> [!NOTE]
> In deze fase bestaat er alleen ondersteuning voor het versleutelen van permanente ARO-volumes met door de klant beheerde sleutels. Deze functie is momenteel niet beschikbaar voor besturingssysteemschijven van hoofd- of werk knooppunt.

> [!IMPORTANT]
> Preview-functies van ARO zijn beschikbaar via selfservice en opt-in. Previews worden aangeboden 'as is' en 'as available', en ze worden uitgesloten van de serviceovereenkomsten en beperkte garantie. ARO-previews worden gedeeltelijk gedekt door klantondersteuning op basis van best effort. Daarom zijn deze functies niet bedoeld voor productiegebruik.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat:

* U hebt een bestaand ARO-cluster op OpenShift versie 4.4 (of hoger).

* U hebt het **opdrachtregelprogramma oc** OpenShift, base64 (onderdeel van coreutils) en **az** Azure CLI geïnstalleerd.

* U bent aangemeld bij uw ARO-cluster met **oc** als globale clusterbeheerder (kubeadmin).

* U bent aangemeld bij de Azure CLI met **az** met een account dat is gemachtigd om 'Inzender'-toegang te verlenen in hetzelfde abonnement als het ARO-cluster.

## <a name="limitations"></a>Beperkingen

* Beschikbaarheid voor door de klant beheerde sleutelversleuteling is regios specifiek. Als u de status voor een specifieke Azure-regio wilt bekijken, raadpleegt u [Azure-regio's.][supported-regions]
* Als u een Ultra Disks, moet u deze eerst inschakelen voor uw abonnement voordat u aan de slag gaat.

## <a name="declare-cluster--encryption-variables"></a>Clusterversleutelingsvariabelen & declareer
U moet de onderstaande variabelen zo configureren dat deze geschikt zijn voor het ARO-cluster waarin u door de klant beheerde versleutelingssleutels wilt inschakelen:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Uw abonnements-id verkrijgen
Uw Azure-abonnements-id wordt meerdere keren gebruikt in de configuratie van de CMK. Verkrijg deze en sla deze op als een variabele:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault maken
Een Azure Key Vault moet worden gebruikt om uw sleutels op te slaan. Maak een nieuw Key Vault met opstingsbeveiliging ingeschakeld. Maak vervolgens een nieuwe sleutel in de kluis om uw eigen aangepaste sleutel op te slaan:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Een Azure Disk Encryption-set maken

De Azure Disk Encryption set wordt gebruikt als referentiepunt voor schijven in ARO. Deze is verbonden met de Azure Key Vault die we in de vorige stap hebben gemaakt en haalt door de klant beheerde sleutels op van die locatie.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>De schijfversleutelingsset toegang verlenen tot Key Vault
Gebruik de schijfversleutelingsset die we in de vorige stappen hebben gemaakt en verleen de schijfversleutelingsset toegang tot Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Andere vereiste ID's voor roltoewijzingen verkrijgen
We moeten toestaan dat het ARO-cluster de schijfversleutelingsset gebruikt om de permanente volumeclaims (PVC's) in het ARO-cluster te versleutelen. Hiervoor maken we een nieuwe Managed Service Identity (MSI). We stellen ook andere machtigingen in voor de ARO MSI en voor de schijfversleutelingsset.

```azurecli-interactive
# First, get the Azure Application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $buildRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity Azure Application ID.
aroMSIAppId="$(az identity show -n $msiName -g $buildRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
buildRGResourceId="$(az group show -n $buildRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Andere roltoewijzingen implementeren die vereist zijn voor CMK-versleuteling
Pas de vereiste roltoewijzingen toe met behulp van de variabelen die u in de vorige stap hebt verkregen:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Een k8s-opslagklasse maken voor versleutelde Premium & Ultra-schijven
Genereer opslagklassen die moeten worden gebruikt voor CMK voor Premium_LRS en UltraSSD_LRS schijven:

```azurecli-interactive
# Premium Disks
cat > managed-premium-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-cmk.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-cmk
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/$subId/resourceGroups/$buildRG/providers/Microsoft.Compute/diskEncryptionSets/$desName"
  resourceGroup: $buildRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```

Voer vervolgens deze implementatie uit in uw ARO-cluster om de configuratie van de opslagklasse toe te passen:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Versleuteling testen met door de klant beheerde sleutels (optioneel)
Als u wilt controleren of uw cluster een door de klant beheerde sleutel gebruikt voor DEB-versleuteling, maken we een permanente volumeclaim met behulp van de nieuwe opslagklasse. Met het onderstaande codefragment wordt een pod gemaakt en wordt een permanente volumeclaim met behulp van Premium-schijven aan een pod bevestigd.
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-cmk-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-cmk
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-cmk-encryption
spec:
  containers:
  - name: mypod-with-cmk-encryption
    image: nginx:1.15.5
    resources:
      requests:
        cpu: 100m
        memory: 128Mi
      limits:
        cpu: 250m
        memory: 256Mi
    volumeMounts:
    - mountPath: "/mnt/azure"
      name: volume
  volumes:
    - name: volume
      persistentVolumeClaim:
        claimName: mypod-with-cmk-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file-optional"></a>Het configuratiebestand voor de testpod toepassen (optioneel)
Voer de onderstaande opdrachten uit om de podconfiguratie van de test toe te passen en de UID van de nieuwe permanente volumeclaim te retourneren. De UID wordt gebruikt om te controleren of de schijf is versleuteld met cmk.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Soms kan er een kleine vertraging zijn bij het toepassen van roltoewijzingen binnen Azure Active Directory. Afhankelijk van de snelheid waarmee deze instructies worden uitgevoerd, is het mogelijk dat de opdracht 'De volledige naam van de Azure-schijf bepalen' niet slaagt. Als dit het geval is, bekijkt u de uitvoer van **oc describe pvc mypod-with-cmk-encryption-pvc** om te controleren of de schijf is ingericht. Als de doorgave van de roltoewijzing niet is voltooid, moet u mogelijk de *POD-&* YAML verwijderen en opnieuw toepassen. 

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Controleer of DERM-schijf is geconfigureerd met EncryptionAtRestWithCustomerKey (optioneel)
De Pod moet een permanente volumeclaim maken die verwijst naar de CMK-opslagklasse. Door de volgende opdracht uit te voeren, wordt gevalideerd of de PVC is geïmplementeerd zoals verwacht:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions