---
title: 'Azure-quickstart: een beheerde HSM maken met behulp van een Azure Resource Manager-sjabloon'
description: In deze quickstart wordt getoond hoe u een beheerde HSM van Azure Key Vault maakt met behulp van een Azure Resource Manager-sjabloon
services: key-vault
author: msmbaldwin
ms.author: mbaldwin
ms.date: 09/15/2020
ms.topic: quickstart
ms.service: key-vault
ms.subservice: managed-hsm
tags:
- azure-resource-manager
ms.custom:
- mvc
- devx-track-azurecli
- mode-arm
ms.openlocfilehash: 33c262c61d50b45663a627e40ea186f1f0dcde41
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107532969"
---
# <a name="quickstart-create-an-key-vault-managed-hsm-using-an-azure-resource-manager-template"></a>Quickstart: Een beheerde Key Vault HSM maken met behulp van een Azure Resource Manager-sjabloon

Managed HSM is een cloudservice van hoge beschikbaarheid en één tenant, die volledig beheerd is en aan de standaarden voldoet. Met deze cloudservice kunt u cryptografische sleutels voor uw cloudtoepassingen waarborgen met behulp van gevalideerde HSM's met standaard **FIPS 140-2 Level 3**.  

Deze quickstart is gericht op het implementeren van een Resource Manager-sjabloon om een beheerde HSM te maken.  [Resource Manager-sjabloon](../../azure-resource-manager/templates/overview.md) is een JavaScript Object Notation-bestand (JSON) dat de infrastructuur en configuratie van uw project definieert. De sjabloon gebruikt een declaratieve syntaxis. Dit is een syntaxis waarmee u kunt aangeven wat u wilt implementeren zonder hiervoor de nodige reeks programmeeropdrachten te hoeven maken. Als u meer wilt weten over het ontwikkelen van Resource Manager-sjablonen, raadpleegt u de [Resource Manager-documentatie](../../azure-resource-manager/index.yml) en het [referentiemateriaal over sjablonen](/azure/templates/microsoft.keyvault/allversions).

Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) aan voordat u begint.

## <a name="prerequisites"></a>Vereisten

U moet over de volgende items beschikken om de stappen in dit artikel uit te kunnen voeren:

- Een abonnement op Microsoft Azure Als u nog geen abonnement hebt, kunt u zich aanmelden voor een [gratis proefabonnement](https://azure.microsoft.com/pricing/free-trial).
- Azure CLI versie 2.12.0 of hoger. Voer `az --version` uit om de versie te bekijken. Als u de CLI wilt installeren of upgraden, raadpleegt u [De Azure CLI installeren]( /cli/azure/install-azure-cli).


[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Aanmelden bij Azure

Als u zich wilt aanmelden bij Azure met behulp van de CLI, typt u:

```azurecli
az login
```

Zie [Aanmelden met de Azure CLI](/cli/azure/authenticate-azure-cli) voor meer informatie over opties voor aanmelding via de CLI

## <a name="create-a-manage-hsm"></a>Een beheerde HSM maken

De sjabloon die in deze quickstart wordt gebruikt, komt uit [Azure Quick Start-sjablonen](https://azure.microsoft.com/resources/templates/101-managed-hsm-create/).

De Azure-resource die in de sjabloon is gedefinieerd:

* **Microsoft.KeyVault/managedHSMs**: maak een beheerde HSM van Azure Key Vault.

[Hier](https://azure.microsoft.com/resources/templates/?resourceType=Microsoft.Keyvault) vindt u meer voorbeelden van Azure Key Vault-sjablonen.

Voor de sjabloon is de object-id vereist die aan uw account is gekoppeld. U vindt deze met de opdracht [az ad user show](/cli/azure/ad/user#az_ad_user_show) in de Azure CLI, waarbij u uw e-mail aan de parameter `--id` doorgeeft. U kunt de uitvoer alleen tot de object-id beperken met de parameter `--query`.

```azurecli-interactive
az ad user show --id <your-email-address> --query "objectId"
```

Mogelijk hebt u ook uw tenant-id nodig. U vindt deze met de opdracht [az ad user show](/cli/azure/account#az_account_show) in de Azure CLI. U kunt de uitvoer alleen tot de tenant-id beperken met de parameter `--query`.

 ```azurecli-interactive
 az account show --query "tenantId"
 ```

1. Selecteer de volgende afbeelding om u aan te melden bij Azure en een sjabloon te openen. Met de sjabloon worden een sleutelkluis en een geheim gemaakt.

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F101-managed-hsm-create%2Fazuredeploy.json"><img src="../media/deploy-to-azure.svg" alt="deploy to azure"/></a>

2. Typ of selecteer de volgende waarden.

    Gebruik de standaardwaarde om de sleutelkluis en een geheim te maken, tenzij er iets anders is aangegeven.

    - **Abonnement**: Selecteer een Azure-abonnement.
    - **Resourcegroep**: Selecteer **Nieuwe maken**, geef een unieke naam op voor de resourcegroep en klik vervolgens op **OK**.
    - **Locatie**: Selecteer een locatie. Bijvoorbeeld **VS - zuid-centraal**.
    - **managedHSMName**: Voer een naam in voor de beheerde HSM.
    - **Tenant-id**: Met de sjabloonfunctie wordt uw tenant-id automatisch opgehaald; wijzig de standaardwaarde niet.  Als er geen waarde is, voert u de tenant-id in die u uit [Vereisten](#prerequisites) hebt opgehaald.
    * **initialAdminObjectIds**: Voer de object-id in die u in [Vereisten](#prerequisites) hebt opgehaald.

3. Selecteer **Aankoop**. Nadat de sleutelkluis is geïmplementeerd, ontvangt u een melding:

Voor het implementeren van de sjabloon wordt de Azure-portal gebruikt. Naast Azure Portal kunt u ook de Azure PowerShell, Azure CLI en REST API gebruiken. Zie [Sjablonen implementeren](../../azure-resource-manager/templates/deploy-powershell.md) voor meer informatie over andere implementatiemethoden.

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheerde HSM gemaakt. Deze beheerde HSM is pas volledig functioneel als deze is geactiveerd. Zie [De beheerde HSM activeren](quick-create-cli.md#activate-your-managed-hsm) voor meer informatie over het activeren van uw HSM.

- Bekijk een [overzicht over beheerde HSM](overview.md)
- Meer informatie over het [Beheren van sleutels in een beheerde HSM](key-management.md)
- Beoordeel [Best practices voor beheerde HSM's](best-practices.md)
