---
title: SMB meerdere kanalen inschakelen
description: Meer informatie over het inschakelen van SMB meerdere kanalen op Azure Premium-bestands shares.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 11/16/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 2f867fa6d4b7e1d864a85106b5d957a53d38eb76
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101732532"
---
# <a name="enable-smb-multichannel-on-a-filestorage-account-preview"></a>SMB meerdere kanalen inschakelen op een FileStorage-account (preview-versie) 

Azure FileStorage-accounts ondersteunen SMB meerdere kanalen (preview), waardoor de prestaties van een SMB 3. x-client toenemen door meerdere netwerk verbindingen tot stand te brengen met uw Premium-bestands shares. In dit artikel vindt u stapsgewijze instructies voor het inschakelen van SMB meerdere kanalen op een bestaand opslag account. Zie SMB multichannel-prestaties voor gedetailleerde informatie over Azure Files SMB meerdere kanalen.

## <a name="limitations"></a>Beperkingen

[!INCLUDE [storage-files-smb-multi-channel-restrictions](../../../includes/storage-files-smb-multi-channel-restrictions.md)]

### <a name="regional-availability"></a>Regionale beschikbaarheid

[!INCLUDE [storage-files-smb-multi-channel-regions](../../../includes/storage-files-smb-multi-channel-regions.md)]

## <a name="prerequisites"></a>Vereisten

- [Maak een FileStorage-account](./storage-how-to-create-file-share.md).
- Als u van plan bent de Azure PowerShell-module te gebruiken, [installeert u de 3.0.1-Preview-versie van de module](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview).

## <a name="getting-started"></a>Aan de slag

Open een Power shell-venster en meld u aan bij uw Azure-abonnement. Van daaruit kunt u zich registreren voor de preview van SMB meerdere kanalen met de volgende opdrachten.

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
> Het kan tot een uur duren voordat de registratie is uitgevoerd.

### <a name="verify-that-feature-registration-is-complete"></a>Controleer of de functie registratie is voltooid

Omdat het een uur kan duren om de functie in te scha kelen voor uw opslag account, kunt u de volgende opdracht gebruiken om te controleren of deze is geregistreerd voor uw abonnement:

```azurepowershell
Get-AzProviderFeature -FeatureName AllowSMBMultichannel -ProviderNamespace Microsoft.Storage
```


## <a name="enable-smb-multichannel"></a>SMB Multichannel inschakelen 
Wanneer u een FileStorage-account hebt gemaakt, kunt u de instructies volgen om de SMB-instellingen voor meerdere kanalen bij te werken voor uw opslag account.

# <a name="portal"></a>[Portal](#tab/azure-portal)
1. Meld u aan bij de Azure Portal en navigeer naar het FileStorage-opslag account waarvoor u SMB meerdere kanalen wilt configureren.
1. Selecteer **Bestands shares** onder **Bestands service** en selecteer vervolgens **instellingen voor bestands share**.
1. Schakel **SMB meerdere kanalen** in **op** aan (of **uit** om uit te scha kelen) en selecteer **Opslaan**.

:::image type="content" source="media/storage-files-enable-smb-multichannel/enable-smb-multichannel-on-storage-account.png" alt-text="Scherm afbeelding van opslag account, SMB meerdere kanalen wordt in-of uitgeschakeld.":::

Als de optie SMB meerdere kanalen niet wordt weer gegeven onder **instellingen voor bestands share** of als de instelling fout bij het bijwerken van de configuratie niet kan worden bijgewerkt, moet u ervoor zorgen dat uw abonnement is geregistreerd en dat uw account zich in een van de [ondersteunde regio's](#regional-availability) bevindt met een ondersteund account type en replicatie.

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Als u SMB meerdere kanalen wilt inschakelen met behulp van de module Azure PowerShell, moet u [de 3.0.1-Preview-versie](https://www.powershellgallery.com/packages/Az.Storage/3.0.1-preview) van de module installeren.

Stel de variabelen `$resourceGroupName` en de `$storageAccountName` resource groep en het opslag account in voordat u deze Power shell-opdrachten uitvoert.

```azurepowershell
# Enable SMB Multichannel on the premium storage account that's in one of the supported regions
Update-AzStorageFileServiceProperty -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -EnableSmbMultichannel $true 
```

# <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)
Azure CLI biedt nog geen ondersteuning voor het configureren van SMB meerdere kanalen. Zie de portal instructies voor het configureren van SMB meerdere kanalen in het opslag account.

---

> [!NOTE]
> Wijzigingen in de configuratie-instellingen van SMB meerdere kanalen zijn van toepassing op alle bestands shares onder het opslag account. U moet de share op de client opnieuw koppelen voordat de wijzigingen van kracht worden.


## <a name="next-steps"></a>Volgende stappen 

- [Koppel de bestands share](storage-how-to-use-files-windows.md) opnieuw om gebruik te maken van SMB meerdere kanalen.
- [Los eventuele problemen op die zijn gerelateerd aan SMB meerdere kanalen](storage-troubleshooting-files-performance.md#smb-multichannel-option-not-visible-under-file-share-settings).
- Zie voor meer informatie over de verbeteringen [SMB meerdere kanalen prestaties](storage-files-smb-multichannel-performance.md)
 - Zie [SMB Mulitchannel beheren](/azure-stack/hci/manage/manage-smb-multichannel)voor meer informatie over de Windows SMB-functie voor meerdere kanalen.