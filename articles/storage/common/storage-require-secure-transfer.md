---
title: Veilige overdracht vereisen om beveiligde verbindingen te garanderen
titleSuffix: Azure Storage
description: Meer informatie over het vereisen van een veilige overdracht van aanvragen voor Azure Storage. Wanneer u een beveiligde overdracht van een opslag account nodig hebt, worden aanvragen die afkomstig zijn van een onbeveiligde verbinding, geweigerd.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 04/21/2020
ms.author: tamram
ms.reviewer: fryu
ms.subservice: common
ms.custom: devx-track-azurecli, devx-track-azurepowershell
ms.openlocfilehash: a0a942daae6b106bce928f8214e92c0c6294203d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555719"
---
# <a name="require-secure-transfer-to-ensure-secure-connections"></a>Veilige overdracht vereisen om beveiligde verbindingen te garanderen

U kunt uw opslag account zo configureren dat alleen aanvragen van beveiligde verbindingen worden geaccepteerd door de eigenschap **beveiligde overdracht vereist** voor het opslag account in te stellen. Wanneer u een veilige overdracht nodig hebt, worden aanvragen die afkomstig zijn van een onbeveiligde verbinding, geweigerd. Micro soft raadt u aan om altijd een veilige overdracht te vereisen voor al uw opslag accounts.

Wanneer beveiligde overdracht is vereist, moet een aanroep van een Azure Storage REST API bewerking worden uitgevoerd via HTTPS. Alle aanvragen via HTTP worden geweigerd.

Het is niet mogelijk om verbinding te maken met een Azure-bestands share via SMB zonder versleuteling wanneer beveiligde overdracht is vereist voor het opslag account. Voor beelden van Inveilige verbindingen zijn die zijn gemaakt via SMB 2,1, SMB 3,0 zonder versleuteling of een aantal versies van de Linux SMB-client.

Standaard is de eigenschap **beveiligde overdracht vereist** ingeschakeld wanneer u een opslag account maakt.

> [!NOTE]
> Omdat Azure Storage geen ondersteuning biedt voor HTTPS voor aangepaste domein namen, wordt deze optie niet toegepast wanneer u een aangepaste domein naam gebruikt. En klassieke opslag accounts worden niet ondersteund.

## <a name="require-secure-transfer-in-the-azure-portal"></a>Veilige overdracht vereisen in de Azure Portal

U kunt de eigenschap **beveiligde overdracht vereist** inschakelen wanneer u een opslag account maakt in de [Azure Portal](https://portal.azure.com). U kunt dit ook inschakelen voor bestaande opslag accounts.

### <a name="require-secure-transfer-for-a-new-storage-account"></a>Veilige overdracht vereisen voor een nieuw opslag account

1. Open het deel venster **opslag account maken** in de Azure Portal.
1. Selecteer onder **beveiligde overdracht vereist** de optie **ingeschakeld**.

   ![Blade opslag account maken](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_1.png)

### <a name="require-secure-transfer-for-an-existing-storage-account"></a>Veilige overdracht vereisen voor een bestaand opslag account

1. Selecteer een bestaand opslag account in de Azure Portal.
1. Selecteer in het menu venster voor het opslag account onder **instellingen** de optie **configuratie**.
1. Selecteer onder **beveiligde overdracht vereist** de optie **ingeschakeld**.

   ![Menu venster voor het opslag account](./media/storage-require-secure-transfer/secure_transfer_field_in_portal_en_2.png)

## <a name="require-secure-transfer-from-code"></a>Veilige overdracht van code vereisen

Als u de beveiligde overdracht via een programma wilt vereisen, stelt u de eigenschap _enableHttpsTrafficOnly_ in op _True_ voor het opslag account. U kunt deze eigenschap instellen met behulp van de opslag Resource provider REST API, client bibliotheken of hulpprogram ma's:

* [REST API](/rest/api/storagerp/storageaccounts)
* [PowerShell](/powershell/module/az.storage/set-azstorageaccount)
* [CLI](/cli/azure/storage/account)
* [NodeJS](https://www.npmjs.com/package/@azure/arm-storage/)
* [.NET SDK](https://www.nuget.org/packages/Microsoft.Azure.Management.Storage)
* [Python SDK](https://pypi.org/project/azure-mgmt-storage)
* [Ruby SDK](https://rubygems.org/gems/azure_mgmt_storage)

## <a name="require-secure-transfer-with-powershell"></a>Veilige overdracht vereisen met Power shell

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

Voor dit voor beeld is de Azure PowerShell-module AZ versie 0,7 of hoger vereist. Voer `Get-Module -ListAvailable Az` uit om de versie te bekijken. Als u PowerShell wilt installeren of upgraden, raadpleegt u [De Azure PowerShell-module installeren](/powershell/azure/install-Az-ps).

Voer `Connect-AzAccount` uit om een verbinding op te zetten met Azure.

 Gebruik de volgende opdracht regel om de instelling te controleren:

```powershell
Get-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}"
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : False
...

```

Gebruik de volgende opdracht regel om de instelling in te scha kelen:

```powershell
Set-AzStorageAccount -Name "{StorageAccountName}" -ResourceGroupName "{ResourceGroupName}" -EnableHttpsTrafficOnly $True
StorageAccountName     : {StorageAccountName}
Kind                   : Storage
EnableHttpsTrafficOnly : True
...

```

## <a name="require-secure-transfer-with-azure-cli"></a>Veilige overdracht vereisen met Azure CLI

[!INCLUDE [sample-cli-install](../../../includes/sample-cli-install.md)]

[!INCLUDE [quickstarts-free-trial-note](../../../includes/quickstarts-free-trial-note.md)]

 Gebruik de volgende opdracht om de instelling te controleren:

```azurecli-interactive
az storage account show -g {ResourceGroupName} -n {StorageAccountName}
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": false,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

Gebruik de volgende opdracht om de instelling in te scha kelen:

```azurecli-interactive
az storage account update -g {ResourceGroupName} -n {StorageAccountName} --https-only true
{
  "name": "{StorageAccountName}",
  "enableHttpsTrafficOnly": true,
  "type": "Microsoft.Storage/storageAccounts"
  ...
}

```

## <a name="next-steps"></a>Volgende stappen

[Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
