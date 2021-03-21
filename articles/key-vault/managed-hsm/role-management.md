---
title: Rolbeheer voor beheerd HSM-gegevensvlak - Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel om roltoewijzingen voor uw beheerde HSM te beheren
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: a4cc898744109475bc119f37350d1b689c550f58
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102209557"
---
# <a name="managed-hsm-role-management"></a>Rolbeheer van beheerde HSM

> [!NOTE]
> Key Vault ondersteunt twee typen resources: kluizen en beheerde HSM's. Dit artikel gaat over **beheerde HSM**. Als u wilt weten hoe u een kluis beheert, raadpleegt u [Key Vault beheren met de Azure CLI](../general/manage-with-cli2.md).

Zie [Wat is beheerde HSM?](overview.md) voor een overzicht van beheerde HSM. Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

Dit artikel laat u zien hoe u rollen beheert voor een beheerd HSM-gegevensvlak. Zie [Toegangsbeheer van beheerde HSM](access-control.md) voor meer informatie over het toegangsbeheermodel voor beheerde HSM's.

Als u een beveiligingsprincipal (zoals een gebruiker, een service-principal, groep of een beheerde identiteit) wilt toestaan om bewerkingen op het beheerde HSM-gegevensvlak uit te voeren, moet aan hen een rol worden toegewezen die het uitvoeren van deze bewerkingen toestaat. Als u bijvoorbeeld een toepassing wilt toestaan een tekenbewerking uit te voeren met een sleutel, moet er een rol worden toegewezen die de gegevensactie Microsoft.KeyVault/managedHSM/keys/sign/action bevat. Een rol kan worden toegewezen aan een specifiek bereik. Lokaal op rollen gebaseerd toegangsbeheer voor beheerde HSM ondersteunt twee bereiken: HSM-breed (`/` of `/keys`) en per sleutel (`/keys/<keyname>`).

Zie [Ingebouwde rollen van de beheerde HSM](built-in-roles.md) voor een lijst met alle ingebouwde rollen van de beheerde HSM en de bewerkingen die met deze rollen zijn toestaan.

## <a name="prerequisites"></a>Vereisten

Als u de Azure CLI-opdrachten in dit artikel wilt gebruiken, hebt u het volgende nodig:

* Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
* Azure CLI versie 2.12.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u uw CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).
* Een beheerde HSM in uw abonnement. Zie [Quickstart: Een beheerde HSM inrichten en activeren met behulp van de Azure CLI](quick-create-cli.md) om een beheerde HSM in te richten en te activeren.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich wilt aanmelden bij Azure met behulp van de CLI, typt u:

```azurecli
az login
```

Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over opties voor aanmelding via de CLI

## <a name="create-a-new-role-assignment"></a>Een nieuwe roltoewijzing maken

### <a name="assign-roles-for-all-keys"></a>Rollen toewijzen voor alle sleutels

Gebruik de opdracht `az keyvault role assignment create` om de rol **Crypto Officer van beheerde HSM** toe te wijzen aan de gebruiker die wordt geïdentificeerd door de naam voor de user principal name **user2\@contoso.com** voor alle **sleutels** (bereik `/keys`) in ContosoHSM.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys
```

### <a name="assign-role-for-a-specific-key"></a>Rol toewijzen voor een specifieke sleutel

Gebruik de opdracht `az keyvault role assignment create` om de rol **Crypto Officer van beheerde HSM** toe te wijzen aan de gebruiker die wordt geïdentificeerd door de naam voor de user principal **user2\@contoso.com** voor een specifieke sleutel met de naam **myrsakey**.

```azurecli-interactive
az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey
```

## <a name="list-existing-role-assignments"></a>Bestaande roltoewijzingen weergeven

Gebruik `az keyvault role assignment list` om een lijst met roltoewijzingen weer te geven.

Alle roltoewijzingen in het bereik / (standaard als er geen bereik is opgegeven) voor alle gebruikers (standaard als er geen gebruiker is opgegeven)

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM
```

Alle roltoewijzingen op het HSM-niveau voor de specifieke gebruiker **user1@contoso.com** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user@contoso.com
```

> [!NOTE]
> Wanneer bereik/(of/keys) de lijst opdracht alleen de roltoewijzingen op het hoogste niveau vermeldt, worden er geen roltoewijzingen weer gegeven op het niveau van afzonderlijke sleutels.

Alle roltoewijzingen voor de specifieke gebruiker **user2@contoso.com** voor de specifieke sleutel **myrsakey-** .

```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey
```

Een specifieke roltoewijzing voor de rol **Crypto Officer van beheerde HSM** voor de specifieke gebruiker **user2@contoso.com** voor de specifieke sleutel **myrsakey**


```azurecli-interactive
az keyvault role assignment list --hsm-name ContosoMHSM --assignee user2@contoso.com --scope /keys/myrsakey --role "Managed HSM Crypto Officer"
```

## <a name="delete-a-role-assignment"></a>Een roltoewijzing verwijderen

Gebruik de opdracht `az keyvault role assignment delete` om de rol **Crypto Officer van beheerde HSM** die is toegewezen aan de gebruiker **user2\@contoso.com** voor de sleutel **user2contoso** te verwijderen.

```azurecli-interactive
az keyvault role assignment delete --hsm-name ContosoMHSM --role "Managed HSM Crypto Officer" --assignee user2@contoso.com  --scope /keys/myrsakey2
```

## <a name="list-all-available-role-definitions"></a>Alle beschikbare roldefinities weergeven

Gebruik de opdracht `az keyvault role definition list` om alle roldefinities weer te geven.

```azurecli-interactive
az keyvault role definition list --hsm-name ContosoMHSM
```

## <a name="next-steps"></a>Volgende stappen

- Bekijk een overzicht van [Azure RBAC (op rollen gebaseerd toegangsbeheer van Azure)](../../role-based-access-control/overview.md).
- Bekijk een zelfstudie over [Rolbeheer van beheerde HSM](role-management.md)
- Meer informatie over het [toegangsbeheermodel voor beheerde HSM's](access-control.md)
- Bekijk alle ingebouwde rollen voor [lokaal op rollen gebaseerd toegangsbeheer voor beheerde HSM](built-in-roles.md)
