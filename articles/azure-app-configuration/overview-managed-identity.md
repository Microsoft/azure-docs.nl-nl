---
title: Beheerde identiteiten configureren met Azure App Configuration
description: Meer informatie over hoe beheerde identiteiten werken in Azure App Configuration en hoe u een beheerde identiteit configureert
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e4fdff2515dde941b2e9037a21ad931ac27b6fef
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764221"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Beheerde identiteiten gebruiken voor Azure App Configuration

In dit onderwerp ziet u hoe u een beheerde identiteit maakt voor Azure App Configuration. Een beheerde identiteit van Azure Active Directory (AAD) biedt Azure App Configuration eenvoudig toegang tot andere met AAD beveiligde resources, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform. U hoeft geen geheimen in terichten of te draaien. Zie Beheerde identiteiten voor [Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in AAD.

Aan uw toepassing kunnen twee typen identiteiten worden verleend:

- Een **door het systeem toegewezen identiteit** is gekoppeld aan uw configuratie-opslag. Deze wordt verwijderd als uw configuratie-opslag wordt verwijderd. Een configuratie-opslag kan slechts één door het systeem toegewezen identiteit hebben.
- Een **door de gebruiker toegewezen identiteit** is een zelfstandige Azure-resource die kan worden toegewezen aan uw configuratieopslag. Een configuratie-opslag kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Voor het App Configuration opslag met een door het systeem toegewezen identiteit moet een extra eigenschap worden ingesteld in het winkel.

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, gebruikt u de opdracht [az appconfig identity assign] voor een bestaand configuratie-opslag. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop 'Probeer het' in de rechterbovenhoek van elk onderstaand codeblok.
- [Installeer de nieuwste versie van Azure CLI](/cli/azure/install-azure-cli) (2.1 of hoger) als u liever een lokale CLI-console gebruikt.

De volgende stappen helpen u bij het maken van een App Configuration store en het toewijzen van een identiteit met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een App Configuration store met behulp van de CLI. Voor meer voorbeelden van het gebruik van de CLI met Azure App Configuration, zie [App Configuration CLI-voorbeelden:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Voer de [opdracht az appconfig identity assign] uit om de door het systeem toegewezen identiteit voor dit configuratie-opslag te maken:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Als u App Configuration winkel met een door de gebruiker toegewezen identiteit maakt, moet u de identiteit maken en vervolgens de resource-id aan uw winkel toewijzen.

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, gebruikt u de opdracht [az appconfig identity assign] voor een bestaand configuratie-opslag. U hebt drie opties voor het uitvoeren van de voorbeelden in deze sectie:

- Gebruik [Azure Cloud Shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de ingesloten Azure Cloud Shell via de knop Uitproberen in de rechterbovenhoek van elk onderstaand codeblok.
- [Installeer de nieuwste versie van Azure CLI](/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale CLI-console gebruikt.

De volgende stappen helpen u bij het maken van een door de gebruiker toegewezen identiteit en een App Configuration-winkel en vervolgens bij het toewijzen van de identiteit aan de winkel met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een App Configuration store met behulp van de CLI. Voor meer voorbeelden van het gebruik van de CLI met Azure App Configuration, zie [App Configuration CLI-voorbeelden:](scripts/cli-create-service.md)

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Maak een door de gebruiker toegewezen identiteit met de naam `myUserAssignedIdentity` met behulp van de CLI.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    Noteer de waarde van de eigenschap in de uitvoer van deze `id` opdracht.

1. Voer de [opdracht az appconfig identity assign] uit om de nieuwe door de gebruiker toegewezen identiteit toe te wijzen aan dit configuratie-opslagaccount. Gebruik de waarde van de `id` eigenschap die u in de vorige stap hebt genoteerd.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te voeren met behulp van de opdracht [az appconfig identity remove](/cli/azure/appconfig/identity#az_appconfig_identity_remove) in de Azure CLI. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit AAD wanneer de app-resource wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)

[az appconfig identity assign]: /cli/azure/appconfig/identity#az_appconfig_identity_assign
[az login]: /cli/azure/reference-index#az_login
