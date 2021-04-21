---
title: Een toegangsbeleid voor Azure Key Vault toewijzen (CLI)
description: De Azure CLI gebruiken om een Key Vault toegangsbeleid toe te wijzen aan een beveiligingsprincipaal of toepassings-id.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 08/27/2020
ms.author: mbaldwin
ms.openlocfilehash: 96b4daa027871201a201b253721114372e58f377
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751432"
---
# <a name="assign-a-key-vault-access-policy"></a>Een Key Vault-toegangsbeleid toewijzen

Een Key Vault-toegangsbeleid bepaalt of een bepaalde beveiligingsprincipaal, [namelijk](../secrets/index.yml)een gebruiker, toepassing of gebruikersgroep, verschillende bewerkingen kan uitvoeren op Key Vault [geheimen,](../keys/index.yml)sleutels en [certificaten.](../certificates/index.yml) U kunt toegangsbeleid toewijzen met behulp [van de Azure Portal,](assign-access-policy-portal.md)de Azure CLI (dit artikel) [of Azure PowerShell](assign-access-policy-powershell.md).

[!INCLUDE [key-vault-access-policy-limits.md](../../../includes/key-vault-access-policy-limits.md)]

Zie [az ad group create](/cli/azure/ad/group#az-ad-group-create) en az ad group member [add](/cli/azure/ad/group/member#az-ad-group-member-add)voor meer informatie over het maken van groepen in Azure Active Directory met behulp van de Azure CLI.

## <a name="configure-the-azure-cli-and-sign-in"></a>De Azure CLI configureren en aanmelden

1. Als u Azure CLI-opdrachten lokaal wilt uitvoeren, installeert u [de Azure CLI.](/cli/azure/install-azure-cli)
 
    Als u opdrachten rechtstreeks in de cloud wilt uitvoeren, gebruikt u [de Azure Cloud Shell](../../cloud-shell/overview.md).

1. Alleen lokale CLI: meld u aan bij Azure met `az login` behulp van :

    ```bash
    az login
    ```

    Met `az login` de opdracht wordt een browservenster geopend om referenties te verzamelen, indien nodig.

## <a name="acquire-the-object-id"></a>De object-id verkrijgen

Bepaal de object-id van de toepassing, groep of gebruiker waaraan u het toegangsbeleid wilt toewijzen:

- Toepassingen en andere service-principals: gebruik de [opdracht az ad sp list om](/cli/azure/ad/sp#az-ad-sp-list) uw service-principals op te halen. Bekijk de uitvoer van de opdracht om de object-id te bepalen van de beveiligingsprincipaal waaraan u het toegangsbeleid wilt toewijzen.

    ```azurecli-interactive
    az ad sp list --show-mine
    ```

- Groepen: gebruik de [opdracht az ad group list](/cli/azure/ad/group#az-ad-group-list) en filter de resultaten met de parameter `--display-name` :

     ```azurecli-interactive
    az ad group list --display-name <search-string>
    ```

- Gebruikers: gebruik de [opdracht az ad user show,](/cli/azure/ad/user#az-ad-user-show) en voer het e-mailadres van de gebruiker in de parameter `--id` in:

    ```azurecli-interactive
    az ad user show --id <email-address-of-user>
    ```

## <a name="assign-the-access-policy"></a>Het toegangsbeleid toewijzen
    
Gebruik de [opdracht az keyvault set-policy](/cli/azure/keyvault#az-keyvault-set-policy) om de gewenste machtigingen toe te wijzen:

```azurecli-interactive
az keyvault set-policy --name myKeyVault --object-id <object-id> --secret-permissions <secret-permissions> --key-permissions <key-permissions> --certificate-permissions <certificate-permissions>
```

Vervang `<object-id>` door de object-id van uw beveiligingsprincipaal.

U hoeft alleen `--secret-permissions` , en op te nemen wanneer u `--key-permissions` `--certificate-permissions` machtigingen toewijst aan deze specifieke typen. De toegestane waarden voor `<secret-permissions>` , en worden opgegeven in de documentatie az `<key-permissions>` `<certificate-permissions>` [keyvault set-policy.](/cli/azure/keyvault#az-keyvault-set-policy)

## <a name="next-steps"></a>Volgende stappen

- [Azure Key Vault-beveiliging: Identiteits- en toegangsbeheer](security-overview.md#identity-management)
- [Beveilig uw sleutelkluis.](security-overview.md)
- [Gids voor Azure Key Vault-ontwikkelaars](developers-guide.md)