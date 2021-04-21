---
title: Een Azure Key Vault-toegangsbeleid toewijzen (CLI)
description: De Azure CLI gebruiken om een Key Vault toe te wijzen aan een beveiligingsprincipaal of toepassings-id.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 349d7453962a736c9f15bb7d31d5a44098f463a4
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107791945"
---
# <a name="assign-a-key-vault-access-policy"></a>Een Key Vault-toegangsbeleid toewijzen

Een Key Vault-toegangsbeleid bepaalt of een bepaalde beveiligingsprincipaal, [namelijk](../secrets/index.yml)een gebruiker, toepassing of gebruikersgroep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen,](../keys/index.yml)sleutels en [certificaten.](../certificates/index.yml) U kunt toegangsbeleid toewijzen met behulp [van de Azure Portal,](assign-access-policy-portal.md)de Azure CLI (dit artikel) [of Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie az [ad group create](/cli/azure/ad/group#az_ad_group_create) en az ad group member [add](/cli/azure/ad/group/member#az_ad_group_member_add)voor meer informatie over het maken van groepen in Azure Active Directory azure CLI.

## <a name="configure-the-azure-cli-and-sign-in"></a>De Azure CLI configureren en aanmelden

1. Als u Azure CLI-opdrachten lokaal wilt uitvoeren, installeert u [de Azure CLI](/cli/azure/install-azure-cli).
 
    Als u opdrachten rechtstreeks in de cloud wilt uitvoeren, gebruikt u [de Azure Cloud Shell](../../cloud-shell/overview.md).

1. Alleen lokale CLI: meld u aan bij Azure met behulp van `az login` :

    ```bash
    az login
    ```

    Met `az login` de opdracht wordt een browservenster geopend om referenties te verzamelen, indien nodig.

## <a name="acquire-the-object-id"></a>De object-id verkrijgen

Bepaal de object-id van de toepassing, groep of gebruiker waaraan u het toegangsbeleid wilt toewijzen:

- Toepassingen en andere service-principals: gebruik de [opdracht az ad sp list om](/cli/azure/ad/sp#az_ad_sp_list) uw service-principals op te halen. Bekijk de uitvoer van de opdracht om de object-id te bepalen van de beveiligingsprincipaal waaraan u het toegangsbeleid wilt toewijzen.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Groepen: gebruik de [opdracht az ad group list](/cli/azure/ad/group#az_ad_group_list) om de resultaten te filteren met de parameter `--display-name` :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Gebruikers: gebruik de [opdracht az ad user show,](/cli/azure/ad/user#az_ad_user_show) en voer het e-mailadres van de gebruiker in de parameter `--id` in:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Het toegangsbeleid toewijzen
    
Gebruik de [opdracht az keyvault set-policy](/cli/azure/keyvault#az_keyvault_set_policy) om de gewenste machtigingen toe te wijzen:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Vervang `<object-id>` door de object-id van uw beveiligingsprincipaal.

U hoeft alleen `--secret-permissions` , en op te nemen wanneer u `--key-permissions` `--certificate-permissions` machtigingen toewijst aan deze specifieke typen. De toegestane waarden voor `<secret-permissions>` , en worden opgegeven in de documentatie az `<key-permissions>` `<certificate-permissions>` [keyvault set-policy.](/cli/azure/keyvault#az_keyvault_set_policy)

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutelkluis.](security-overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)