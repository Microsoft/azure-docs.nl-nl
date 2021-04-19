---
title: SMB meerdere kanalen inschakelen
description: Meer informatie over het inschakelen van SMB meerdere kanalen op Azure Premium-bestands shares.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/15/2021
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: da4e1a58aef28e5c47100a0311ff81a5af04a918
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107718971"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>SMB meerdere kanalen inschakelen voor een FileStorage-account (preview) 

Azure FileStorage-accounts ondersteunen SMB meerdere kanalen (preview), waardoor de prestaties van een SMB 3.x-client worden verhoogd door meerdere netwerkverbindingen met uw Premium-bestands shares tot stand te brengen. Dit artikel bevat stapsgewijs richtlijnen voor het inschakelen van SMB meerdere kanalen voor een bestaand opslagaccount. Zie SMB-prestaties Azure Files meerdere kanalen voor gedetailleerde informatie over SMB meerdere kanalen.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak een FileStorage-account.](./storage-how-to-create-file-share.md)
- Als u van plan bent om de Azure PowerShell te gebruiken, installeert u de [preview-versie 3.0.1 van de module](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Aan de slag

Open een PowerShell-venster en meld u aan bij uw Azure-abonnement. Van hier kunt u zich registreren voor de preview-versie van SMB meerdere kanalen met de volgende opdrachten.

```azurepowershell
Connect-AzAccount
# Setting your active subscription to the one you want to register for the preview. 
# Replace the <subscription-id> placeholder with your subscription id. 
$context = Get-AzSubscription -SubscriptionId <your-subscription-id> 
Set-AzContext $context

Register-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage 
Register-AzResourceProvider -ProviderNamespace Microsoft.Storage 
```

> [!NOTE]
> De registratie kan een uur duren.

### <a name="verify-that-feature-registration-is-complete"></a>Controleren of de functieregistratie is voltooid

Omdat het tot een uur kan duren voordat de functie in uw opslagaccount is ingeschakeld, kunt u de volgende opdracht gebruiken om te controleren of deze is geregistreerd voor uw abonnement:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>SMB Multichannel inschakelen 
Nadat u een FileStorage-account hebt gemaakt, kunt u de instructies volgen voor het bijwerken van SMB-instellingen voor meerdere kanalen voor uw opslagaccount.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan Azure Portal en navigeer naar het FileStorage-opslagaccount waar u SMB meerdere kanalen wilt configureren.
1. Selecteer **Bestands shares** onder **Bestandsservice** en selecteer vervolgens **Bestands share-instellingen.**
1. Schakel **SMB meerdere kanalen in** **(of** uit **om** uit te schakelen) en selecteer **Opslaan.**

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Schermopname van opslagaccount, smb meerdere kanalen is in-/uitschakelen."  lightbox="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png":::

Als de optie SMB meerdere kanalen  niet zichtbaar is onder Bestandsdelingsinstellingen of als er tijdens het bijwerken van de configuratie [](#regional-availability) een fout wordt weergegeven bij het bijwerken van de configuratie, controleert u of uw abonnement is geregistreerd en uw account zich in een van de ondersteunde regio's met ondersteund accounttype en replicatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u SMB meerdere kanalen wilt inschakelen met behulp van de Azure PowerShell module, moet u de [preview-versie 3.0.1](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) van de module installeren.

Stel de variabelen en `$resourceGroupName` in `$storageAccountName` op uw resourcegroep en opslagaccount voordat u deze PowerShell-opdrachten gaat uitvoeren.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Azure CLI biedt nog geen ondersteuning voor het configureren van SMB meerdere kanalen. Zie de portalinstructies voor het configureren van SMB meerdere kanalen in een opslagaccount.

---

> [!NOTE]
> Wijzigingen in de SMB-configuratie-instellingen voor meerdere kanalen zijn van toepassing op alle bestands shares onder het opslagaccount. U moet de share echter opnieuw op uw client ontkoppelen om de wijzigingen door te voeren.


## <a name="next-steps"></a>Volgende stappen 

- [Ontkoppel de bestands share](storage-how-to-use-files-windows.md) opnieuw om te profiteren van SMB meerdere kanalen.
- [Los eventuele problemen met betrekking tot SMB meerdere kanalen op.](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings)
- Zie Prestaties van [SMB](storage-files-smb-multichannel-performance.md) meerdere kanalen voor meer informatie over de verbeteringen
 - Zie [Manage SMB Mulitchannel (SMB meerdere](/azure-stack/hci/manage/manage-smb-multichannel)kanalen beheren) voor meer informatie over de functie Windows SMB meerdere kanalen.