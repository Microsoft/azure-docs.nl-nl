---
title: Beheerde identiteiten configureren met Azure-app configuratie
description: Meer informatie over het werken met beheerde identiteiten in Azure-app configuratie en het configureren van een beheerde identiteit
author: barbkess
ms.topic: article
ms.date: 02/25/2020
ms.author: barbkess
ms.reviewer: lcozzens
ms.service: azure-app-configuration
ms.openlocfilehash: e6ced30e8c8c5eb60eea3806d9c8080df7c2a12d
ms.sourcegitcommit: 706e7d3eaa27f242312d3d8e3ff072d2ae685956
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/09/2021
ms.locfileid: "99979586"
---
# <a name="how-to-use-managed-identities-for-azure-app-configuration"></a>Beheerde identiteiten gebruiken voor Azure-app configuratie

In dit onderwerp wordt beschreven hoe u een beheerde identiteit maakt voor Azure-app configuratie. Met een beheerde identiteit van Azure Active Directory (AAD) kan Azure-app configuratie eenvoudig toegang krijgen tot andere met AAD beveiligde resources, zoals Azure Key Vault. De identiteit wordt beheerd door het Azure-platform. U hoeft geen geheimen in te richten of te draaien. Zie [beheerde identiteiten voor Azure-resources](../active-directory/managed-identities-azure-resources/overview.md)voor meer informatie over beheerde identiteiten in Aad.

Aan uw toepassing kunnen twee typen identiteiten worden verleend:

- Een door het **systeem toegewezen identiteit** is gekoppeld aan uw configuratie opslag. Deze wordt verwijderd als uw configuratie archief wordt verwijderd. Een configuratie archief kan slechts één door het systeem toegewezen identiteit hebben.
- Een door de **gebruiker toegewezen identiteit** is een zelfstandige Azure-resource die kan worden toegewezen aan uw configuratie-archief. Een configuratie archief kan meerdere door de gebruiker toegewezen identiteiten hebben.

## <a name="adding-a-system-assigned-identity"></a>Een door het systeem toegewezen identiteit toevoegen

Het maken van een app-configuratie archief met een door het systeem toegewezen identiteit vereist een extra eigenschap die moet worden ingesteld in de Store.

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, gebruikt u de opdracht [AZ appconfig Identity Assign] voor een bestaande configuratie opslag. Er zijn drie opties voor het uitvoeren van de voor beelden in deze sectie:

- Gebruik [Azure Cloud shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk hieronder opgenomen code blok.
- [Installeer de nieuwste versie van Azure cli](/cli/azure/install-azure-cli) (2,1 of hoger) als u liever een lokale cli-console gebruikt.

De volgende stappen helpen u bij het maken van een app-configuratie archief en het toewijzen van een identiteit met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een app-configuratie archief met behulp van de CLI. Zie voor meer voor beelden van het gebruik van de CLI met Azure-app configuratie [app Configuration cli samples](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Voer de opdracht [AZ appconfig Identity Assign] uit om de door het systeem toegewezen identiteit voor deze configuratie opslag te maken:

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup
    ```

## <a name="adding-a-user-assigned-identity"></a>Een door de gebruiker toegewezen identiteit toevoegen

Het maken van een app-configuratie archief met een door de gebruiker toegewezen identiteit vereist dat u de identiteit maakt en vervolgens de resource-id aan uw Store toewijst.

### <a name="using-the-azure-cli"></a>Met behulp van de Azure CLI

Als u een beheerde identiteit wilt instellen met behulp van de Azure CLI, gebruikt u de opdracht [AZ appconfig Identity Assign] voor een bestaande configuratie opslag. Er zijn drie opties voor het uitvoeren van de voor beelden in deze sectie:

- Gebruik [Azure Cloud shell](../cloud-shell/overview.md) van de Azure Portal.
- Gebruik de Inge sloten Azure Cloud Shell via de knop ' Probeer het ', in de rechter bovenhoek van elk hieronder opgenomen code blok.
- [Installeer de nieuwste versie van Azure cli](/cli/azure/install-azure-cli) (2.0.31 of hoger) als u liever een lokale cli-console gebruikt.

Met de volgende stappen wordt u begeleid bij het maken van een door de gebruiker toegewezen identiteit en een app-configuratie archief. vervolgens wijst u de identiteit toe aan de Store met behulp van de CLI:

1. Als u de Azure CLI in een lokale console gebruikt, meldt u zich eerst aan bij Azure met [az login]. Gebruik een account dat is gekoppeld aan uw Azure-abonnement:

    ```azurecli-interactive
    az login
    ```

1. Maak een app-configuratie archief met behulp van de CLI. Zie voor meer voor beelden van het gebruik van de CLI met Azure-app configuratie [app Configuration cli samples](scripts/cli-create-service.md):

    ```azurecli-interactive
    az group create --name myResourceGroup --location eastus
    az appconfig create --name myTestAppConfigStore --location eastus --resource-group myResourceGroup --sku Free
    ```

1. Maak een door de gebruiker toegewezen identiteit `myUserAssignedIdentity` die wordt aangeroepen met behulp van de cli.

    ```azurecli-interactive
    az identity create -resource-group myResourceGroup --name myUserAssignedIdentity
    ```

    In de uitvoer van deze opdracht noteert u de waarde van de `id` eigenschap.

1. Voer de opdracht [AZ appconfig Identity Assign] uit om de nieuwe door de gebruiker toegewezen identiteit toe te wijzen aan deze configuratie opslag. Gebruik de waarde van de `id` eigenschap die u in de vorige stap hebt genoteerd.

    ```azurecli-interactive
    az appconfig identity assign --name myTestAppConfigStore --resource-group myResourceGroup --identities /subscriptions/[subscription id]/resourcegroups/myResourceGroup/providers/Microsoft.ManagedIdentity/userAssignedIdentities/myUserAssignedIdentity
    ```

## <a name="removing-an-identity"></a>Een identiteit verwijderen

Een door het systeem toegewezen identiteit kan worden verwijderd door de functie uit te scha kelen met behulp van de opdracht [AZ appconfig Identity Remove](/cli/azure/appconfig/identity#az-appconfig-identity-remove) in de Azure cli. Door de gebruiker toegewezen identiteiten kunnen afzonderlijk worden verwijderd. Als u een door het systeem toegewezen identiteit op deze manier verwijdert, wordt deze ook uit AAD verwijderd. Door het systeem toegewezen identiteiten worden ook automatisch verwijderd uit AAD wanneer de app-resource wordt verwijderd.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Een ASP.NET Core-app maken met Azure-app-configuratie](quickstart-aspnet-core-app.md)

[AZ appconfig Identity Assign]: /cli/azure/appconfig/identity?view=azure-cli-latest#az-appconfig-identity-assign
[az login]: /cli/azure/reference-index#az-login
