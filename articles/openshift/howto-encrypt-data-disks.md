---
title: Permanente volume claims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat open Shift (ARO)
description: Uw eigen sleutel (BYOK)/door de klant beheerde sleutel (CMK) implementeren instructies voor Azure Red Hat open Shift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: Encryption, byok, Aro, CMK, open Shift, Red Hat
ms.openlocfilehash: cf028456cc8971678373d36214885c3f79df8e82
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/24/2021
ms.locfileid: "105047062"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Permanente volume claims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat open Shift (ARO) (preview)

Azure Storage gebruikt SSE (server side Encryption) om uw gegevens automatisch te [versleutelen](../storage/common/storage-service-encryption.md) wanneer deze in de cloud worden bewaard. Standaard worden gegevens versleuteld met door micro soft-platform beheerde sleutels. Voor extra controle over versleutelings sleutels kunt u uw eigen door de klant beheerde sleutels opgeven voor het versleutelen van gegevens in uw Azure Red Hat open Shift-clusters.

> [!NOTE]
> In deze fase is ondersteuning alleen beschikbaar voor het versleutelen van ARO permanente volumes met door de klant beheerde sleutels. Deze functie is momenteel niet beschikbaar voor de schijven van het hoofd-of worker-besturings systeem.

> [!IMPORTANT]
> ARO preview-functies zijn beschikbaar op self-service. Previews worden als ' as is ' en ' als beschikbaar ' gegeven en zijn uitgesloten van de service level agreements en de beperkte garantie. ARO-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat:

* U hebt een reeds bestaand ARO-cluster met openshift versie 4,4 (of hoger).

* U hebt het opdracht regel programma **OC** open Shift, base64 (onderdeel van coreutils) en de **AZ** Azure cli geïnstalleerd.

* U bent aangemeld bij uw ARO-cluster met behulp van **OC** als globale cluster-beheerder gebruiker (kubeadmin).

* U bent aangemeld bij de Azure CLI met **AZ** met een account dat is gemachtigd om ' Inzender ' toegang toe te kennen in hetzelfde abonnement als het Aro-cluster.

## <a name="limitations"></a>Beperkingen

* Beschik baarheid voor door de klant beheerde sleutel versleuteling is specifiek voor een regio. Als u de status van een specifieke Azure-regio wilt bekijken, controleert u [Azure-regio's][supported-regions].
* Als u ultra disks wilt gebruiken, moet u deze eerst inschakelen in uw abonnement voordat u aan de slag gaat.

## <a name="declare-cluster--encryption-variables"></a>Versleutelings variabelen voor cluster & declareren
U moet de onderstaande variabelen zo configureren dat ze geschikt zijn voor u het ARO-cluster waarin u door de klant beheerde versleutelings sleutels wilt inschakelen:
```
aroCluster="mycluster"             # The name of the ARO cluster that you wish to enable CMK on. This may be obtained from **az aro list -o table**
buildRG="mycluster-rg"             # The name of the resource group used when you initially built the ARO cluster. This may be obtained from **az aro list -o table**
desName="aro-des"                  # Your Azure Disk Encryption Set name. This must be unique in your subscription.
vaultName="aro-keyvault-1"         # Your Azure Key Vault name. This must be unique in your subscription.
vaultKeyName="myCustomAROKey"      # The name of the key to be used within your Azure Key Vault. This is the name of the key, not the actual value of the key that you will rotate.
```

## <a name="obtain-your-subscription-id"></a>Uw abonnements-ID verkrijgen
Uw Azure-abonnements-ID wordt meerdere keren gebruikt in de configuratie van CMK. Vraag de app aan en sla deze op als een variabele:
```azurecli-interactive
# Obtain your Azure Subscription ID and store it in a variable
subId="$(az account list -o tsv | grep True | awk '{print $3}')"
```

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault-exemplaar maken
Een Azure Key Vault-exemplaar moet worden gebruikt om uw sleutels op te slaan. Maak een nieuwe Key Vault met de functie voor leegmaken van beveiliging ingeschakeld. Maak vervolgens een nieuwe sleutel in de kluis om uw eigen aangepaste sleutel op te slaan:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table

# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Een Azure Disk Encryption set maken

De set Azure Disk Encryption wordt gebruikt als het referentie punt voor schijven in ARO. Deze is verbonden met de Azure Key Vault die we in de vorige stap hebben gemaakt en haalt door de klant beheerde sleutels vanaf die locatie.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $buildRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>De schijf versleuteling toegang geven tot Key Vault
Gebruik de schijf versleutelingset die u in de voor gaande stappen hebt gemaakt en verleen de schijf versleuteling toegang tot Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's Azure Application ID value.
desIdentity="$(az disk-encryption-set show -n $desName -g $buildRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $buildRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Andere Id's verkrijgen die nodig zijn voor roltoewijzingen
We moeten het ARO-cluster toestaan om de schijf versleuteling te gebruiken voor het versleutelen van de permanente volume claims (Pvc's) in het ARO-cluster. Hiervoor gaan we een nieuwe Managed Service Identity (MSI) maken. We stellen ook andere machtigingen in voor de ARO MSI en voor de ingestelde schijf versleuteling.

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

### <a name="implement-other-role-assignments-required-for-cmk-encryption"></a>Implementeer andere roltoewijzingen die vereist zijn voor CMK-versleuteling
Pas de vereiste roltoewijzingen toe met behulp van de variabelen die in de vorige stap zijn verkregen:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $buildRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $buildRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks"></a>Een K8S-opslag klasse maken voor een versleutelde Premium-& Ultra schijven
Genereer opslag klassen die moeten worden gebruikt voor CMK voor Premium_LRS en UltraSSD_LRS schijven:

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

Voer vervolgens deze implementatie uit in uw ARO-cluster om de opslag klassen configuratie toe te passen:

```azurecli-interactive
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-cmk.yaml
oc apply -f managed-ultra-encrypted-cmk.yaml
```

## <a name="test-encryption-with-customer-managed-keys-optional"></a>Versleuteling testen met door de klant beheerde sleutels (optioneel)
Om te controleren of uw cluster gebruikmaakt van een door de klant beheerde sleutel voor PVC-versleuteling, wordt er een claim voor permanente volumes gemaakt met behulp van de nieuwe opslag klasse. In het onderstaande code fragment maakt u een pod en koppelt u een permanente volume claim met behulp van Premium-schijven.
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
### <a name="apply-the-test-pod-configuration-file-optional"></a>Het configuratie bestand van de test pod Toep assen (optioneel)
Voer de onderstaande opdrachten uit om de configuratie van de test pod toe te passen en de UID van de nieuwe claim permanente volume te retour neren. De UID wordt gebruikt om te controleren of de schijf is versleuteld met behulp van CMK.
```azurecli-interactive
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o jsonpath='{.items[0].metadata.uid}')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o jsonpath='{.spec.azureDisk.diskName}')"
```
> [!NOTE]
> Soms kan er sprake zijn van een lichte vertraging bij het Toep assen van roltoewijzingen binnen Azure Active Directory. Afhankelijk van de snelheid waarmee deze instructies worden uitgevoerd, kan de opdracht ' bepalen van de volledige Azure-schijf naam ' mogelijk niet worden uitgevoerd. Als dit het geval is, raadpleegt u de uitvoer van **OC PVC mypod-with-CMK-Encryption-PVC** om te controleren of de schijf is ingericht. Als de doorgifte van de roltoewijzing niet is voltooid, moet u mogelijk de pod & PVC-YAML *verwijderen* en opnieuw *Toep assen* .

### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey-optional"></a>Controleren of de PVC-schijf is geconfigureerd met ' EncryptionAtRestWithCustomerKey ' (optioneel)
De pod moet een permanente volume claim maken die verwijst naar de CMK-opslag klasse. Als u de volgende opdracht uitvoert, wordt gecontroleerd of het PVC naar verwachting is geïmplementeerd:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $buildRG -o json --query [encryption]
```

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: ../aks/operator-best-practices-cluster-security.md
[byok-azure-portal]: ../storage/common/customer-managed-keys-configure-key-vault.md
[customer-managed-keys]: ../virtual-machines/disk-encryption.md#customer-managed-keys
[key-vault-generate]: ../key-vault/general/manage-with-cli2.md
[supported-regions]: ../virtual-machines/disk-encryption.md#supported-regions