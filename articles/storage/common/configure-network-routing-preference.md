---
title: Netwerkrouternigsvoorkeur configureren
titleSuffix: Azure Storage
description: Configureer de voorkeur voor netwerkroutering voor uw Azure-opslagaccount om op te geven hoe netwerkverkeer naar uw account wordt gerouteerd vanaf clients via internet.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: ed248480803370a75b40c18ee7d0e2641254d84a
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790451"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Netwerkrouteringsvoorkeur configureren voor Azure Storage

In dit artikel wordt beschreven hoe u de voorkeur voor netwerkroutering en routespecifieke eindpunten voor uw opslagaccount kunt configureren. 

De voorkeur voor netwerkroutering geeft aan hoe netwerkverkeer naar uw account wordt gerouteerd vanaf clients via internet. Routespecifieke eindpunten zijn nieuwe eindpunten die Azure Storage voor uw opslagaccount. Deze eindpunten routeren verkeer via een gewenst pad zonder uw standaardrouteringsvoorkeur te wijzigen. Zie Network [routing preference for Azure Storage (Netwerkrouteringsvoorkeur voor Azure Storage).](network-routing-preference.md)

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>De routeringsvoorkeur voor het standaard openbare eindpunt configureren

Standaard is de routeringsvoorkeur voor het openbare eindpunt van het opslagaccount ingesteld op Microsoft Global Network. U kunt kiezen tussen het wereldwijde netwerk van Microsoft en internetroutering als de standaardrouteringsvoorkeur voor het openbare eindpunt van uw opslagaccount. Zie Voor meer informatie over het verschil tussen deze twee typen routering Netwerkrouteringsvoorkeur [voor Azure Storage.](network-routing-preference.md) 

### <a name="portal"></a>[Portal](#tab/azure-portal)

Uw routeringsvoorkeur wijzigen in Internetroutering:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Navigeer naar uw opslagaccount in de portal.

3. Kies **onder Instellingen** de optie **Netwerken.**

    > [!div class="mx-imgBorder"]
    > ![Menuoptie Netwerken](./media/configure-network-routing-preference/networking-option.png)

4.  Wijzig op het tabblad Firewalls en virtuele  netwerken onder **Netwerkroutering** de instelling **Routeringsvoorkeur** in **Internetroutering.**

5.  Klik op **Opslaan**.

    > [!div class="mx-imgBorder"]
    > ![optie voor internetroutering](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om u te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslagaccount dat als host voor uw statische website zal worden gebruikt.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Als u uw routeringsvoorkeur wilt wijzigen in Internetroutering, gebruikt u de [opdracht Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) en stelt u de `--routing-choice` parameter in op `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resourcegroep die het opslagaccount bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslagaccount.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Meld u aan bij uw Azure-abonnement.

   - Meld u Azure Cloud Shell aan bij de Azure Portal [.](https://portal.azure.com)

   - Voer de opdracht [az login](/cli/azure/reference-index#az_login) uit om u aan te melden bij uw lokale installatie van de CLI:

     ```azurecli
     az login
     ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslagaccount dat als host voor uw statische website zal worden gebruikt.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Als u uw routeringsvoorkeur wilt wijzigen in Internetroutering, gebruikt u [de opdracht az storage account update](/cli/azure/storage/account#az_storage_account_update) en stelt u de parameter in op `--routing-choice` `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

---

## <a name="configure-a-route-specific-endpoint"></a>Een route-specifiek eindpunt configureren

U kunt ook een route-specifiek eindpunt configureren. U kunt bijvoorbeeld de routeringsvoorkeur voor het standaard-eindpunt instellen op Internetroutering en vervolgens een route-specifiek eindpunt publiceren waarmee verkeer tussen clients op internet en uw opslagaccount kan worden gerouteerd via het wereldwijde Microsoft-netwerk.

Deze voorkeur is alleen van invloed op het routespecifieke eindpunt. Deze voorkeur heeft geen invloed op uw standaardrouteringsvoorkeur.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Navigeer naar uw opslagaccount in de portal.

2.  Kies **onder Instellingen** de optie **Netwerken.**

3.  Kies op **het tabblad Firewalls** en virtuele netwerken onder Routespecifieke eindpunten publiceren de **routeringsvoorkeur** van uw routespecifieke eindpunt en klik vervolgens op **Opslaan.**

    In de volgende afbeelding ziet u de **geselecteerde routeringsoptie** voor Het Microsoft-netwerk.

    > [!div class="mx-imgBorder"]
    > ![Routeringsoptie voor Microsoft-netwerk](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Gebruik de opdracht [Set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) om een route-specifiek eindpunt te configureren. 

   - Als u een route-specifiek eindpunt wilt maken dat gebruikmaakt van de microsoft-netwerkrouteringsvoorkeur, stelt u de `-PublishMicrosoftEndpoint` parameter in op `true` . 

   - Als u een route-specifiek eindpunt wilt maken dat gebruikmaakt van de internetrouteringsvoorkeur, stelt u de `-PublishInternetEndpointTo` parameter in op `true` .  

   In het volgende voorbeeld wordt een route-specifiek eindpunt gemaakt dat gebruikmaakt van de Microsoft-netwerkrouteringsvoorkeur.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resourcegroep die het opslagaccount bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslagaccount.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Gebruik de opdracht [az storage account update](/azure/storage/account#az_storage_account_update) om een route-specifiek eindpunt te configureren. 

   - Als u een route-specifiek eindpunt wilt maken dat gebruikmaakt van de microsoft-netwerkrouteringsvoorkeur, stelt u de `--publish-microsoft-endpoints` parameter in op `true` . 

   - Als u een route-specifiek eindpunt wilt maken dat gebruikmaakt van de internetrouteringsvoorkeur, stelt u de `--publish-internet-endpoints` parameter in op `true` .  

   In het volgende voorbeeld wordt een route-specifiek eindpunt gemaakt dat gebruikmaakt van de Microsoft-netwerkrouteringsvoorkeur.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslagaccount.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>De eindpuntnaam voor een route-specifiek eindpunt zoeken

Als u een route-specifiek eindpunt hebt geconfigureerd, kunt u het eindpunt vinden in de eigenschappen van uw opslagaccount.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Kies **onder Instellingen** de optie **Eigenschappen.**

    > [!div class="mx-imgBorder"]
    > ![menuoptie Eigenschappen](./media/configure-network-routing-preference/properties.png)

2.  Het **Microsoft-eindpunt voor netwerkroutering** wordt weergegeven voor elke service die ondersteuning biedt voor routeringsvoorkeuren. In deze afbeelding ziet u het eindpunt voor de blob- en bestandsservices.

    > [!div class="mx-imgBorder"]
    > ![Microsoft-netwerkrouteringsoptie voor routespecifieke eindpunten](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Als u de eindpunten naar de console wilt afdrukken, gebruikt u `PrimaryEndpoints` de eigenschap van het opslagaccountobject.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resourcegroep die het opslagaccount bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslagaccount.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Als u de eindpunten naar de console wilt afdrukken, gebruikt u [de eigenschap az storage account show](/cli/azure/storage/account#az_storage_account_show) van het object storage account.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resourcegroep die het opslagaccount bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslagaccount.

---

## <a name="see-also"></a>Zie ook

- [Voorkeur voor netwerkroutering](network-routing-preference.md)
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligingsaanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
