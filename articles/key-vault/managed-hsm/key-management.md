---
title: Sleutels beheren in een beheerde HSM - Azure Key Vault | Microsoft Docs
description: Gebruik dit artikel voor het beheren van sleutels in een beheerde HSM
services: key-vault
author: amitbapat
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 8d0cbd35b53bc8460ac8a19e5197d1f560657263
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212039"
---
# <a name="manage-a-managed-hsm-using-the-azure-cli"></a>Een beheerde HSM beheren met de Azure CLI

> [!NOTE]
> Key Vault ondersteunt twee typen resources: kluizen en beheerde HSM's. Dit artikel gaat over **beheerde HSM**. Als u wilt weten hoe u een kluis beheert, raadpleegt u [Key Vault beheren met de Azure CLI](../general/manage-with-cli2.md).

Zie [Wat is beheerde HSM?](overview.md) voor een overzicht van beheerde HSM.

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet over de volgende items beschikken om de stappen in dit artikel uit te kunnen voeren:

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

> [!NOTE]
> Bij alle onderstaande opdrachten worden twee gebruiksmethoden weergegeven. Een met de parameters **--hsm-name** en **--name** (voor de sleutelnaam) en een andere met de parameter **--id**, waarin u de volledige URL kunt opgeven, inclusief de naam van de sleutel, indien van toepassing. De laatste methode is handig wanneer de oproepende functie (een gebruiker of een toepassing) geen leestoegang heeft tot het besturingsvlak en alleen beperkte toegang tot het gegevensvlak.

## <a name="create-an-hsm-key"></a>HSM-sleutel maken

Gebruik de opdracht `az keyvault key create` om een sleutel te maken.

### <a name="create-an-rsa-key"></a>Een RSA-sleutel maken

In het volgende voorbeeld ziet u hoe u een 3072-bits **RSA**-sleutel maakt die alleen wordt gebruikt voor **wrapKey, unwrapKey**-bewerkingen (--ops). 


```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072

## OR
# Note the key name (myrsakey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey --ops wrapKey unwrapKey --kty RSA-HSM --size 3072
```

Let op: de `get`-bewerking retourneert alleen de openbare sleutel en sleutelkenmerken. De persoonlijke sleutel (in het geval van een asymmetrische sleutel) of het sleutelmateriaal (in het geval van een symmetrische sleutel) wordt niet geretourneerd.

### <a name="create-an-ec-key"></a>EC-sleutel maken

In het onderstaande voorbeeld ziet u hoe u met de P-256-curve een **EC**-sleutel maakt die alleen wordt gebruikt voor de bewerkingen **ondertekenen en verifiëren** (--ops) en die twee labels heeft: **usage** en **appname**. Met tags kunt u aanvullende metagegevens aan de sleutel toevoegen voor bijhouden en beheren.

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256

## OR
# Note the key name (myec256key) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myec256key --ops sign verify  --tags ‘usage=signing] appname=myapp’ --kty EC-HSM --curve P-256
```

### <a name="create-a-256-bit-symmetric-key"></a>Een 256-bits symmetrische sleutel maken

In het volgende voorbeeld ziet u hoe u een 256-bits **symmetrische** sleutel maakt die alleen wordt gebruikt voor de bewerkingen **versleutelen en ontsleutelen** (--ops).

```azurecli-interactive
az keyvault key create --hsm-name ContosoMHSM --name myaeskey --ops encrypt decrypt  --tags --kty oct-HSM --size 256

## OR
# Note the key name (myaeskey) in the URI

az keyvault key create --id https://ContosoMHSM.managedhsm.azure.net/keys/myaeskey --ops encrypt decrypt  --tags ‘usage=signing] appname=myapp’ --kty oct-HSM --size 256
```

## <a name="view-key-attributes-and-tags"></a>Sleutelkenmerken en -tags weergeven

Gebruik de opdracht `az keyvault key show` om kenmerken, versies en tags voor een sleutel weer te geven.

```azurecli-interactive
az keyvault key show --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key show --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey

```

## <a name="list-keys"></a>Sleutels vermelden

Gebruik de opdracht `az keyvault key list` om alle sleutels in een beheerde HSM weer te geven.

```azurecli-interactive
az keyvault key list --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list --id https://ContosoMHSM.managedhsm.azure.net/

```

## <a name="delete-a-key"></a>Een sleutel verwijderen

Gebruik de opdracht `az keyvault key delete` om een sleutel uit een beheerde HSM te verwijderen. Let op: zacht verwijderen is altijd ingeschakeld. Daarom houdt een verwijderde sleutel de status Verwijderd en kan worden hersteld totdat het aantal retentiedagen is verstreken. Na die periode wordt de sleutel opgeschoond (permanent verwijderd) zonder dat herstel mogelijk is.

```azurecli-interactive
az keyvault key delete --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key delete --id https://ContosoMHSM.managedhsm.azure.net/keys/myrsakey
```

## <a name="list-deleted-keys"></a>Verwijderde sleutels vermelden

Gebruik de opdracht `az keyvault key list-deleted` om alle sleutels met de status Verwijderd in uw beheerde HSM te vermelden.

```azurecli-interactive
az keyvault key list-deleted --hsm-name ContosoHSM

## OR
# use full URI

az keyvault key list-deleted --id https://ContosoMHSM.managedhsm.azure.net/
```

## <a name="recover-undelete-a-deleted-key"></a>Een verwijderde sleutel herstellen (verwijderen ongedaan maken)

Gebruik de opdracht `az keyvault key list-deleted` om alle sleutels met de status Verwijderd in uw beheerde HSM te vermelden. Als u een sleutel wilt herstellen (verwijderen ongedaan maken) met de parameter --id terwijl een verwijderde sleutel wordt hersteld, moet u de `recoveryId`-waarde noteren van de verwijderde sleutel die u via de opdracht `az keyvault key list-deleted` hebt verkregen.

```azurecli-interactive
az keyvault key recover --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey
```

## <a name="purge-permanently-delete-a-key"></a>Een sleutel opschonen (permanent verwijderen)

Gebruik de opdracht `az keyvault key purge` voor het opschonen (permanent verwijderen) van een sleutel.

> [!NOTE]
> Als voor de beheerde HSM beveiliging tegen opschonen is ingeschakeld, wordt de opschoonbewerking niet toegestaan. De sleutel wordt automatisch opgeschoond wanneer de retentieperiode is verstreken.

```azurecli-interactive
az keyvault key purge --hsm-name ContosoHSM --name myrsakey

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey

```

## <a name="create-a-single-key-backup"></a>Back-up van één sleutel maken

Gebruik `az keyvault key backup` om een back-up van een sleutel te maken. Het back-upbestand is een versleutelde blob die cryptografisch is gebonden aan het beveiligingsdomein van de bron-HSM. Het bestand kan alleen worden hersteld in HSM's die hetzelfde beveiligingsdomein hebben. Meer informatie over het [beveiligingsdomein](security-domain.md).

```azurecli-interactive
az keyvault key backup --hsm-name ContosoHSM --name myrsakey --file myrsakey.backup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key backup --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey  --file myrsakey.backup

```

## <a name="restore-a-single-key-from-backup"></a>Eén sleutel herstellen vanuit een back-up

Gebruik `az keyvault key restore` om één sleutel te herstellen. De bron-HSM waar de back-up is gemaakt, moet hetzelfde beveiligingsdomein hebben als de doel-HSM waar de sleutel wordt hersteld.

> [!NOTE]
> Het herstellen mislukt als er een sleutel met dezelfde naam in een actieve of verwijderde status is.

```azurecli-interactive
az keyvault key restore --hsm-name ContosoHSM --name myrsakey --file myrsakey.bakup

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --file myrsakey.bakup

```

## <a name="import-a-key-from-a-file"></a>Een sleutel uit een bestand importeren

Gebruik de opdracht `az keyvault key import` om een sleutel (alleen RSA en EC) uit een bestand te importeren. Het certificaatbestand moet een persoonlijke sleutel hebben en moet PEM-codering gebruiken (zoals gedefinieerd in RFC's [1421](https://tools.ietf.org/html/rfc1421), [1422](https://tools.ietf.org/html/rfc1422), [1423](https://tools.ietf.org/html/rfc1423) en [1424](https://tools.ietf.org/html/rfc1424)).

```azurecli-interactive
az keyvault key import --hsm-name ContosoHSM --name myrsakey --pem-file mycert.key --password 'mypassword'

## OR
# Note the key name (myaeskey) in the URI

az keyvault key recover --id https://ContosoMHSM.managedhsm.azure.net/deletedKeys/myrsakey --pem-file mycert.key --password 'mypassword'
```

Zie [Met HSM beveiligde sleutels importeren in beheerde HSM (BYOK)](hsm-protected-keys-byok.md) als u een sleutel van uw on-premises HSM wilt importeren in beheerde HSM

## <a name="next-steps"></a>Volgende stappen

- Raadpleeg [Naslaginformatie voor Key Vault CLI](/cli/azure/keyvault) voor de volledige naslaginformatie van Azure CLI over sleutelkluisopdrachten.
- Raadpleeg [de Ontwikkelaarshandleiding voor Azure Key Vault](../general/developers-guide.md) voor naslaginformatie over programmeren.
- Meer informatie over [Rolbeheer van beheerde HSM](role-management.md)
- Meer informatie over [Best practices voor beheerde HSM](best-practices.md)
