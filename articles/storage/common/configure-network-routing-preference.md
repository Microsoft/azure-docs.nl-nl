---
title: Netwerkrouternigsvoorkeur configureren
titleSuffix: Azure Storage
description: Configureer voorkeurs netwerk routering voor uw Azure-opslag account om op te geven hoe netwerk verkeer via internet naar uw account wordt doorgestuurd.
services: storage
author: normesta
ms.service: storage
ms.topic: how-to
ms.date: 03/17/2021
ms.author: normesta
ms.reviewer: santoshc
ms.subservice: common
ms.openlocfilehash: 0738f7e427c2ff094c9b6df7539ba67dff80d095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104589851"
---
# <a name="configure-network-routing-preference-for-azure-storage"></a>Voor keuren voor netwerk routering configureren voor Azure Storage

In dit artikel wordt beschreven hoe u de voor keuren voor netwerk Routering en route-specifieke eind punten voor uw opslag account kunt configureren. 

Met de voorkeurs methode voor netwerk routering geeft u op hoe netwerk verkeer via internet naar uw account wordt doorgestuurd. Route-specifieke eind punten zijn nieuwe eind punten die Azure Storage maken voor uw opslag account. Deze eind punten routeren verkeer via een gewenst pad zonder de standaard routerings voorkeur te wijzigen. Zie [netwerk routering voor Azure Storage voor](network-routing-preference.md)meer informatie.

## <a name="configure-the-routing-preference-for-the-default-public-endpoint"></a>De routerings voorkeur voor het standaard open bare eind punt configureren

De routerings voorkeur voor het open bare eind punt van het opslag account wordt standaard ingesteld op het globale netwerk van micro soft. U kunt kiezen tussen het globale netwerk van micro soft en Internet routering als de standaard routerings voorkeur voor het open bare eind punt van uw opslag account. Zie [netwerk routering voor Azure Storage voor](network-routing-preference.md)meer informatie over het verschil tussen deze twee typen route ring. 

### <a name="portal"></a>[Portal](#tab/azure-portal)

De routerings voorkeur wijzigen in Internet routering:

1. Meld u aan bij [Azure Portal](https://portal.azure.com).

2. Navigeer naar uw opslag account in de portal.

3. Kies onder **instellingen** de optie **netwerken**.

    > [!div class="mx-imgBorder"]
    > ![Menu optie netwerken](./media/configure-network-routing-preference/networking-option.png)

4.  Wijzig op het tabblad **firewalls en virtuele netwerken** onder **netwerk routering** de voorkeurs instelling voor **route ring** naar **Internet routering**.

5.  Klik op **Opslaan**.

    > [!div class="mx-imgBorder"]
    > ![routerings optie voor Internet](./media/configure-network-routing-preference/internet-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Meld u aan bij uw Azure-abonnement met de `Connect-AzAccount` opdracht en volg de instructies op het scherm om te verifiëren.

   ```powershell
   Connect-AzAccount
   ```

2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```powershell
   $context = Get-AzSubscription -SubscriptionId <subscription-id>
   Set-AzContext $context
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Als u de routerings voorkeur voor Internet routering wilt wijzigen, gebruikt u de opdracht [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) en stelt `--routing-choice` u de para meter in op `InternetRouting` .

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -RoutingChoice InternetRouting
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resource groep die het opslag account bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslag account.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Meld u aan bij uw Azure-abonnement.

   - Meld u aan bij de [Azure Portal](https://portal.azure.com)om Azure Cloud shell te starten.

   - Als u zich wilt aanmelden bij de lokale installatie van de CLI, voert u de opdracht [AZ login](/cli/azure/reference-index#az-login) :

     ```azurecli
     az login
     ```
2. Als uw identiteit is gekoppeld aan meer dan één abonnement, stelt u uw actieve abonnement in op het abonnement van het opslag account dat als host voor uw statische website gaat.

   ```azurecli
   az account set --subscription <subscription-id>
   ```

   Vervang de `<subscription-id>` waarde van de tijdelijke aanduiding door de id van uw abonnement.

3. Als u de routerings voorkeur voor Internet routering wilt wijzigen, gebruikt u de opdracht [AZ Storage account update](/cli/azure/storage/account#az_storage_account_update) en stelt `--routing-choice` u de para meter in op `InternetRouting` .

   ```azurecli
   az storage account update --name <storage-account-name> --routing-choice InternetRouting
   ```

   Vervang de waarde van de tijdelijke plaatsaanduiding `<storage-account-name>` door de naam van uw opslagaccount.

---

## <a name="configure-a-route-specific-endpoint"></a>Een route-specifiek eind punt configureren

U kunt ook een route-specifiek eind punt configureren. U kunt bijvoorbeeld de routerings voorkeur voor het standaard eindpunt instellen op *Internet routering* en vervolgens een route-specifiek eind punt publiceren dat verkeer tussen clients op het Internet mogelijk maakt en uw opslag account kan worden gerouteerd via het globale micro soft-netwerk.

Deze voor keur is alleen van invloed op het route-specifieke eind punt. Deze voor keur heeft geen invloed op uw standaard routerings voorkeur.  

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Navigeer naar uw opslag account in de portal.

2.  Kies onder **instellingen** de optie **netwerken**.

3.  Kies op het tabblad **firewalls en virtuele netwerken** onder **route-specifieke eind punten publiceren** de routerings voorkeur van uw route-specifiek eind punt en klik vervolgens op **Opslaan**.

    In de volgende afbeelding ziet u de geselecteerde **micro soft-netwerk routering** optie.

    > [!div class="mx-imgBorder"]
    > ![Routerings optie van micro soft-netwerk](./media/configure-network-routing-preference/microsoft-network-routing-option.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Gebruik de opdracht [set-AzStorageAccount](/powershell/module/az.storage/set-azstorageaccount) om een route-specifiek eind punt te configureren. 

   - Als u een route-specifiek eind punt wilt maken dat gebruikmaakt van de voor keuren van micro soft-netwerk routering, stelt `-PublishMicrosoftEndpoint` u de para meter in op `true` . 

   - Als u een route-specifiek eind punt wilt maken dat gebruikmaakt van de voor keur voor Internet routering, stelt `-PublishInternetEndpointTo` u de para meter in op `true` .  

   In het volgende voor beeld wordt een route-specifiek eind punt gemaakt dat gebruikmaakt van de micro soft-netwerk routering voorkeur.

   ```powershell
   Set-AzStorageAccount -ResourceGroupName <resource-group-name> `
    -AccountName <storage-account-name> `
    -PublishMicrosoftEndpoint $true
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resource groep die het opslag account bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslag account.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Als u een route-specifiek eind punt wilt configureren, gebruikt u de opdracht [AZ Storage account update](/azure/storage/account#az-storage-account-update) . 

   - Als u een route-specifiek eind punt wilt maken dat gebruikmaakt van de voor keuren van micro soft-netwerk routering, stelt `--publish-microsoft-endpoints` u de para meter in op `true` . 

   - Als u een route-specifiek eind punt wilt maken dat gebruikmaakt van de voor keur voor Internet routering, stelt `--publish-internet-endpoints` u de para meter in op `true` .  

   In het volgende voor beeld wordt een route-specifiek eind punt gemaakt dat gebruikmaakt van de micro soft-netwerk routering voorkeur.

   ```azurecli
   az storage account update --name <storage-account-name> --publish-microsoft-endpoints true
   ```

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslag account.

---

## <a name="find-the-endpoint-name-for-a-route-specific-endpoint"></a>De naam van het eind punt voor een route-specifiek eind punt zoeken

Als u een route-specifiek eind punt hebt geconfigureerd, kunt u het eind punt vinden in de eigenschappen van uw opslag account.

### <a name="portal"></a>[Portal](#tab/azure-portal)

1.  Kies onder **instellingen** de optie **Eigenschappen**.

    > [!div class="mx-imgBorder"]
    > ![menu optie Eigenschappen](./media/configure-network-routing-preference/properties.png)

2.  Het eind punt van het **micro soft-netwerk routering** wordt weer gegeven voor elke service die ondersteuning biedt voor routerings voorkeuren. Deze afbeelding toont het eind punt voor de BLOB-en bestands Services.

    > [!div class="mx-imgBorder"]
    > ![Routerings optie van micro soft-netwerk voor route-specifieke eind punten](./media/configure-network-routing-preference/routing-url.png)

### <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

1. Gebruik de `PrimaryEndpoints` eigenschap van het object Storage-account om de eind punten af te drukken naar de-console.

   ```powershell
   Get-AzStorageAccount -ResourceGroupName <resource-group-name> -Name <storage-account-name>
   write-Output $StorageAccount.PrimaryEndpoints
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resource groep die het opslag account bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslag account.

### <a name="azure-cli"></a>[Azure-CLI](#tab/azure-cli)

1. Gebruik de eigenschap [AZ Storage account show](/cli/azure/storage/account#az_storage_account_show) van het object Storage-account om de eind punten af te drukken naar de-console.

   ```azurecli
   az storage account show -g <resource-group-name> -n <storage-account-name>
   ```

   Vervang de `<resource-group-name>` waarde van de tijdelijke aanduiding door de naam van de resource groep die het opslag account bevat.

   Vervang de `<storage-account-name>` waarde van de tijdelijke aanduiding door de naam van het opslag account.

---

## <a name="see-also"></a>Zie ook

- [Voorkeurs netwerk routering](network-routing-preference.md)
- [Azure Storage-firewalls en virtuele netwerken configureren](storage-network-security.md)
- [Beveiligings aanbevelingen voor Blob Storage](../blobs/security-recommendations.md)
