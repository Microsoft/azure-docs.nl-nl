---
title: Permanente volume claims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat open Shift (ARO)
description: Uw eigen sleutel (BYOK)/door de klant beheerde sleutel (CMK) implementeren instructies voor Azure Red Hat open Shift
ms.topic: article
ms.date: 02/24/2021
author: stuartatmicrosoft
ms.author: stkirk
ms.service: azure-redhat-openshift
keywords: Encryption, byok, Aro, open Shift, Red Hat
ms.openlocfilehash: 09e1f92a967c7b77d3bb8e27769f4cafd4fd4f53
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102054599"
---
# <a name="encrypt-persistent-volume-claims-with-a-customer-managed-key-cmk-on-azure-red-hat-openshift-aro-preview"></a>Permanente volume claims versleutelen met een door de klant beheerde sleutel (CMK) op Azure Red Hat open Shift (ARO) (preview)

Azure Storage versleutelt alle gegevens in een opslag account in rust. Standaard worden gegevens versleuteld met door micro soft-platform beheerde sleutels die besturings systeem-en gegevens schijven bevatten. Voor meer controle over versleutelings sleutels kunt u door de klant beheerde sleutels leveren voor het versleutelen van gegevens in uw Azure Red Hat open Shift-clusters.

> [!NOTE]
> In deze fase is ondersteuning alleen beschikbaar voor het versleutelen van ARO permanente volumes met door de klant beheerde sleutels. Deze functie is momenteel niet beschikbaar voor schijven van het besturings systeem.

> [!IMPORTANT]
> ARO preview-functies zijn beschikbaar op self-service. Previews worden als ' as is ' en ' als beschikbaar ' gegeven en zijn uitgesloten van de service level agreements en de beperkte garantie. ARO-previews worden gedeeltelijk gedekt door de klant ondersteuning. Daarom zijn deze functies niet bedoeld voor productie gebruik.

## <a name="before-you-begin"></a>Voordat u begint
In dit artikel wordt ervan uitgegaan dat:

* U hebt een reeds bestaand ARO-cluster met openshift versie 4,4 (of hoger).

* U hebt het opdracht regel programma ' OC ' open Shift, base64 (deel van de kern hulppr.) en de ' AZ ' Azure CLI geïnstalleerd.

* U bent aangemeld bij uw ARO-cluster met behulp van *OC* als globale cluster-beheerder gebruiker (kubeadmin).

* U bent aangemeld bij de Azure CLI met *AZ* met een account dat is gemachtigd om ' Inzender ' toegang toe te kennen in hetzelfde abonnement als het Aro-cluster.

## <a name="limitations"></a>Beperkingen

* Beschik baarheid voor door de klant beheerde sleutel versleuteling is specifiek voor een regio. Als u de status van een specifieke Azure-regio wilt bekijken, controleert u [Azure-regio's][supported-regions].
* Als u ultra disks gebruikt, schakelt u ultra disk in voor uw abonnement voordat u aan de slag gaat.

## <a name="create-an-azure-key-vault-instance"></a>Een Azure Key Vault-exemplaar maken
Een Azure Key Vault-exemplaar moet worden gebruikt om uw sleutels op te slaan. Maak een nieuwe Key Vault met opschoon beveiliging en voorlopig verwijderen ingeschakeld. Maak vervolgens een nieuwe sleutel in de kluis om uw eigen aangepaste sleutel op te slaan:

```azurecli-interactive
# Create an Azure Key Vault resource in a supported Azure region
az keyvault create -n $vaultName -g $buildRG --enable-purge-protection true -o table
# Create the actual key within the Azure Key Vault
az keyvault key create --vault-name $vaultName --name $vaultKeyName --protection software -o jsonc
```

## <a name="create-an-azure-disk-encryption-set"></a>Een Azure Disk Encryption set maken

De Azure Disk Encryption set wordt gebruikt als het referentie punt voor schijven in ARO. Deze is verbonden met de Azure Key Vault die we in de vorige stap hebben gemaakt en haalt door de klant beheerde sleutels vanaf die locatie.

```azurecli-interactive
# Retrieve the Key Vault Id and store it in a variable
keyVaultId="$(az keyvault show --name $vaultName --query [id] -o tsv)"

# Retrieve the Key Vault key URL and store it in a variable
keyVaultKeyUrl="$(az keyvault key show --vault-name $vaultName --name $vaultKeyName  --query [key.kid] -o tsv)"

# Create an Azure disk encryption set
az disk-encryption-set create -n $desName -g $myRG --source-vault $keyVaultId --key-url $keyVaultKeyUrl -o table
```

## <a name="grant-the-disk-encryption-set-access-to-key-vault"></a>De schijf versleuteling toegang geven tot Key Vault
Gebruik de schijf versleutelingset die u in de voor gaande stappen hebt gemaakt en verleen de schijf versleuteling toegang tot Azure Key Vault:

```azurecli-interactive
# First, find the disk encryption set's AppId value.
desIdentity="$(az disk-encryption-set show -n $desName -g $myRG --query [identity.principalId] -o tsv)"

# Next, update the Key Vault security policy settings to allow access to the disk encryption set.
az keyvault set-policy -n $vaultName -g $myRG --object-id $desIdentity --key-permissions wrapkey unwrapkey get -o table

# Now, ensure the disk encryption set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc
```

### <a name="obtain-other-ids-required-for-role-assignments"></a>Andere Id's verkrijgen die nodig zijn voor roltoewijzingen
We moeten het ARO-cluster toestaan om de schijf versleuteling te gebruiken voor het versleutelen van de permanente volume claims (Pvc's) in het ARO-cluster. Hiervoor maakt u een nieuwe Managed Service Identity (MSI). We stellen ook andere machtigingen in voor de Aro MSI en voor de schijf versleutelings.
```
# First, get the application ID of the service principal used in the ARO cluster.
aroSPAppId="$(oc get secret azure-credentials -n kube-system -o jsonpath='{.data.azure_client_id}' | base64 --decode)"

# Next, get the object ID of the service principal used in the ARO cluster.
aroSPObjId="$(az ad sp show --id $aroSPAppId -o tsv --query [objectId])"

# Set the name of the ARO Managed Service Identity. 
msiName="$aroCluster-msi"

# Create the Managed Service Identity (MSI) required for disk encryption.
az identity create -g $myRG -n $msiName -o jsonc

# Get the ARO Managed Service Identity application ID.
aroMSIAppId="$(az identity show -n $msiName -g $myRG -o tsv --query [clientId])"

# Get the resource ID for the disk encryption set and the Key Vault resource group.
myRGResourceId="$(az group show -n $myRG -o tsv --query [id])"
```

### <a name="implement-other-role-assignments-required-for-byokcmk-encryption"></a>Implementeer andere roltoewijzingen die vereist zijn voor BYOK/CMK-versleuteling
Pas de vereiste roltoewijzingen toe met behulp van de variabelen die in de vorige stap zijn verkregen:

```azurecli-interactive
# Ensure the Azure Disk Encryption Set can read the contents of the Azure Key Vault.
az role assignment create --assignee $desIdentity --role Reader --scope $keyVaultId -o jsonc

# Assign the MSI AppID 'Reader' permission over the disk encryption set & Key Vault resource group.
az role assignment create --assignee $aroMSIAppId --role Reader --scope $myRGResourceId -o jsonc

# Assign the ARO Service Principal 'Contributor' permission over the disk encryption set & Key Vault Resource Group.
az role assignment create --assignee $aroSPObjId --role Contributor --scope $myRGResourceId -o jsonc
```

## <a name="create-a-k8s-storage-class-for-encrypted-premium--ultra-disks-optional"></a>Een K8S-opslag klasse voor versleutelde Premium-& Ultra schijven maken (optioneel)
Genereer opslag klassen die moeten worden gebruikt voor BYOK/CMK voor Premium_LRS en UltraSSD_LRS schijven:
```
# Premium Disks
cat > managed-premium-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-premium-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: Premium_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF

# Ultra Disks
cat > managed-ultra-encrypted-byok.yaml<< EOF
kind: StorageClass
apiVersion: storage.k8s.io/v1
metadata:
  name: managed-ultra-encrypted-byok
provisioner: kubernetes.io/azure-disk
parameters:
  skuname: UltraSSD_LRS
  kind: Managed
  diskEncryptionSetID: "/subscriptions/subId/resourceGroups/myRG/providers/Microsoft.Compute/diskEncryptionSets/desName"
  resourceGroup: myRG
  cachingmode: None
  diskIopsReadWrite: "2000"  # minimum value: 2 IOPS/GiB
  diskMbpsReadWrite: "320"   # minimum value: 0.032/GiB
reclaimPolicy: Delete
allowVolumeExpansion: true
volumeBindingMode: WaitForFirstConsumer
EOF
```
### <a name="set-up-your-storage-class-configuration"></a>De configuratie van de opslag klasse instellen
Vervang de variabelen die uniek zijn voor uw ARO-cluster in de twee opslag klassen configuratie bestanden:
```
# Insert your current active subscription ID into the configuration
sed -i "s/subId/$subId/g" managed-premium-encrypted-byok.yaml
sed -i "s/subId/$subId/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the Resource Group which contains the disk encryption set and Key Vault
sed -i "s/myRG/$myRG/g" managed-premium-encrypted-byok.yaml
sed -i "s/myRG/$myRG/g" managed-ultra-encrypted-byok.yaml

# Replace the name of the disk encryption set
sed -i "s/desName/$desName/g" managed-premium-encrypted-byok.yaml
sed -i "s/desName/$desName/g" managed-ultra-encrypted-byok.yaml
```
Voer vervolgens deze implementatie uit in uw ARO-cluster om de opslag klassen configuratie toe te passen:
```
# Update cluster with the new storage classes
oc apply -f managed-premium-encrypted-byok.yaml
oc apply -f managed-ultra-encrypted-byok.yaml
```
## <a name="test-encryption-with-customer-managed-keys"></a>Versleuteling testen met door de klant beheerde sleutels
Als u wilt controleren of uw cluster gebruikmaakt van een door de klant beheerde sleutel voor PVC-versleuteling, maakt u een claim voor een permanente volume met behulp van de juiste opslag klasse. In het onderstaande code fragment maakt u een pod en koppelt u een permanente volume claim met behulp van standaard schijven
```
# Create a pod which uses a persistent volume claim referencing the new storage class
cat > test-pvc.yaml<< EOF
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
  name: mypod-with-byok-encryption-pvc
spec:
  accessModes:
  - ReadWriteOnce
  storageClassName: managed-premium-encrypted-byok
  resources:
    requests:
      storage: 1Gi
---
kind: Pod
apiVersion: v1
metadata:
  name: mypod-with-byok-encryption
spec:
  containers:
  - name: mypod-with-byok-encryption
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
        claimName: mypod-with-byok-encryption-pvc
EOF
```
### <a name="apply-the-test-pod-configuration-file"></a>Het configuratie bestand van de test pod Toep assen
Voer de onderstaande opdrachten uit om de configuratie van de test pod toe te passen en de UID van de nieuwe claim permanente volume te retour neren. De UID wordt gebruikt om te controleren of de schijf is versleuteld met BYOK/CMK.
```
# Apply the test pod configuration file and set the PVC UID as a variable to query in Azure later.
pvcUid="$(oc apply -f test-pvc.yaml -o json | jq -r '.items[0].metadata.uid')"

# Determine the full Azure Disk name.
pvName="$(oc get pv pvc-$pvcUid -o json |jq -r '.spec.azureDisk.diskName')"
```
### <a name="verify-pvc-disk-is-configured-with-encryptionatrestwithcustomerkey"></a>Controleren of de PVC-schijf is geconfigureerd met ' EncryptionAtRestWithCustomerKey ' 
De pod moet een permanente volume claim maken die verwijst naar de BYOK/CMK-opslag klasse. Als u de volgende opdracht uitvoert, wordt gecontroleerd of het PVC naar verwachting is geïmplementeerd:
```azurecli-interactive
# Describe the OpenShift cluster-wide persistent volume claims
oc describe pvc

# Verify with Azure that the disk is encrypted with a customer-managed key
az disk show -n $pvName -g $myRG -o json --query [encryption]
```

## <a name="next-steps"></a>Volgende stappen

<!-- LINKS - external -->

<!-- LINKS - internal -->
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
[best-practices-security]: /azure/aks/operator-best-practices-cluster-security
[byok-azure-portal]: /azure/storage/common/storage-encryption-keys-portal
[customer-managed-keys]: /azure/virtual-machines/windows/disk-encryption#customer-managed-keys
[key-vault-generate]: /azure/key-vault/key-vault-manage-with-cli2
[supported-regions]: /azure/virtual-machines/windows/disk-encryption#supported-regions

