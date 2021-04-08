---
title: 'Azure CLI: de toegang voor importeren/exporteren naar beheerde schijven beperken met privékoppelingen'
description: Schakel privékoppelingen in voor uw beheerde schijven met Azure CLI. U kunt alleen in uw virtuele netwerk schijven veilig exporteren en importeren.
author: roygara
ms.service: virtual-machines
ms.topic: overview
ms.date: 08/11/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions, devx-track-azurecli
ms.openlocfilehash: 68b96401fc4fc6ea8916664978dbd4c094d9e5b4
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98684522"
---
# <a name="azure-cli---restrict-importexport-access-for-managed-disks-with-private-links"></a>Azure CLI: de toegang voor importeren/exporteren voor beheerde schijven beperken met privékoppelingen

U kunt [privé-eindpunten](../../private-link/private-endpoint-overview.md) gebruiken om het exporteren en importeren van beheerde schijven te beperken en om veilig toegang te krijgen tot gegevens via een [privékoppeling](../../private-link/private-link-overview.md) van clients in uw virtuele Azure-netwerk. Het privé-eindpunt gebruikt een IP-adres uit de adresruimte van het virtuele netwerk voor uw service voor beheerde schijven. Netwerkverkeer tussen de clients in het virtuele netwerk en beheerde schijven gaat over het virtuele netwerk en een persoonlijke koppeling in het fundamentele Microsoft-netwerk, waardoor de blootstelling van het openbare internet wordt voorkomen.

Om privékoppelingen te gebruiken voor het exporteren/importeren van beheerde schijf, maakt u eerst een resource voor schijftoegang en koppelt u deze aan uw virtuele netwerk in hetzelfde abonnement door een privé-eindpunt te maken. Vervolgens koppelt u een schijf of een momentopname aan een exemplaar van schijftoegang. Als laatste moet u ook de eigenschap NetworkAccessPolicy van de schijf of de momentopname instellen op `AllowPrivate`. Hiermee wordt de toegang tot het virtuele netwerk beperkt. 

U kunt de eigenschap NetworkAccessPolicy instellen op `DenyAll` om te voorkomen dat iemand de gegevens voor een schijf of momentopname exporteert. De standaardwaarde voor de eigenschap NetworkAccessPolicy is `AllowAll`.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [virtual-machines-disks-private-links-limitations](../../../includes/virtual-machines-disks-private-links-limitations.md)]


## <a name="log-in-into-your-subscription-and-set-your-variables"></a>Meld u aan bij uw abonnement en stel de variabelen in

```azurecli-interactive
subscriptionId=yourSubscriptionId
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessForPrivateLinks
vnetName=yourVNETForPrivateLinks
subnetName=yourSubnetForPrivateLinks
privateEndPointName=yourPrivateLinkForSecureMDExportImport
privateEndPointConnectionName=yourPrivateLinkConnection

#The name of an existing disk which is the source of the snapshot
sourceDiskName=yourSourceDiskForSnapshot

#The name of the new snapshot which will be secured via Private Links
snapshotNameSecuredWithPL=yourSnapshotNameSecuredWithPL

az login

az account set --subscription $subscriptionId

```

## <a name="create-a-disk-access-using-azure-cli"></a>Een schijftoegang maken met Azure CLI
```azurecli
az disk-access create -n $diskAccessName -g $resourceGroupName -l $region

diskAccessId=$(az disk-access show -n $diskAccessName -g $resourceGroupName --query [id] -o tsv)
```

## <a name="create-a-virtual-network"></a>Een Virtual Network maken

Netwerkbeleid zoals netwerkbeveiligingsgroepen (NSG) wordt niet ondersteund voor privé-eindpunten. Voor het implementeren van een privé-eindpunt op een bepaald subnet is een expliciete instelling uitschakelen vereist voor dat subnet. 

```azurecli
az network vnet create --resource-group $resourceGroupName \
    --name $vnetName \
    --subnet-name $subnetName
```
## <a name="disable-subnet-private-endpoint-policies"></a>Beleid voor privé-eindpunten van subnet uitschakelen

Azure implementeert resources in een subnet binnen een virtueel netwerk, dus moet u het subnet bijwerken om beleid voor privé-eindpunt netwerk uit te schakelen. 

```azurecli
az network vnet subnet update --resource-group $resourceGroupName \
    --name $subnetName  \
    --vnet-name $vnetName \
    --disable-private-endpoint-network-policies true
```
## <a name="create-a-private-endpoint-for-the-disk-access-object"></a>Een privé-eindpunt maken voor het object voor schijftoegang

```azurecli
az network private-endpoint create --resource-group $resourceGroupName \
    --name $privateEndPointName \
    --vnet-name $vnetName  \
    --subnet $subnetName \
    --private-connection-resource-id $diskAccessId \
    --group-ids disks \
    --connection-name $privateEndPointConnectionName
```

## <a name="configure-the-private-dns-zone"></a>Privé-DNS-zone configureren

Maak een Privé-DNS-zone voor de opslagblob, maak een koppelingslink met het Virtual Network en maak een DNS-zonegroep om het privé-eindpunt aan de Privé-DNS-zone te koppelen. 

```azurecli
az network private-dns zone create --resource-group $resourceGroupName \
    --name "privatelink.blob.core.windows.net"

az network private-dns link vnet create --resource-group $resourceGroupName \
    --zone-name "privatelink.blob.core.windows.net" \
    --name yourDNSLink \
    --virtual-network $vnetName \
    --registration-enabled false 

az network private-endpoint dns-zone-group create \
   --resource-group $resourceGroupName \
   --endpoint-name $privateEndPointName \
   --name yourZoneGroup \
   --private-dns-zone "privatelink.blob.core.windows.net" \
   --zone-name disks
```

## <a name="create-a-disk-protected-with-private-links"></a>Een schijf maken die wordt beveiligd met Private Links
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
diskName=yourDiskName
diskSkuName=Standard_LRS
diskSizeGB=128

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az disk create -n $diskName \
-g $resourceGroupName \
-l $region \
--size-gb $diskSizeGB \
--sku $diskSkuName \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="create-a-snapshot-of-a-disk-protected-with-private-links"></a>Een momentopname maken van een schijf die wordt beveiligd met persoonlijke koppelingen
```azurecli-interactive
resourceGroupName=yourResourceGroupName
region=northcentralus
diskAccessName=yourDiskAccessName
sourceDiskName=yourSourceDiskForSnapshot
snapshotNameSecuredWithPL=yourSnapshotName

diskId=$(az disk show -n $sourceDiskName -g $resourceGroupName --query [id] -o tsv)

diskAccessId=$(az resource show -n $diskAccessName -g $resourceGroupName --namespace Microsoft.Compute --resource-type diskAccesses --query [id] -o tsv)

az snapshot create -n $snapshotNameSecuredWithPL \
-g $resourceGroupName \
-l $region \
--source $diskId \
--network-access-policy AllowPrivate \
--disk-access $diskAccessId 
```

## <a name="next-steps"></a>Volgende stappen

- [Veelgestelde vragen over privékoppelingen](../faq-for-disks.md#private-links-for-securely-exporting-and-importing-managed-disks)
- [Beheerde momentopnamen exporteren/kopiëren als VHD naar een opslagaccount in een andere regio met CLI](/previous-versions/azure/virtual-machines/scripts/virtual-machines-cli-sample-copy-managed-disks-vhd)