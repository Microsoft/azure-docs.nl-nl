---
title: Uw Azure Red Hat open Shift-ontwikkel omgeving instellen
description: Hier volgen de vereisten voor het werken met Microsoft Azure Red Hat open SHIFT.
keywords: Setup van Red Hat open Shift instellen
author: jimzim
ms.author: jzim
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-redhat-openshift
ms.custom: devx-track-azurecli
ms.openlocfilehash: c253c6bf81305b9b336525c20980cf9599463648
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102209863"
---
# <a name="set-up-your-azure-red-hat-openshift-dev-environment"></a>Een Azure Red Hat OpenShift-ontwikkelaarsomgeving instellen

> [!IMPORTANT]
> Azure Red Hat open Shift 3,11 wordt 30 juni 2022 buiten gebruik gesteld. Ondersteuning voor het maken van nieuwe Azure Red Hat open Shift 3,11-clusters loopt voort tot en met 30 november 2020. Na de buiten gebruiks telling worden de resterende Azure Red Hat open Shift 3,11-clusters afgesloten om beveiligings problemen te voor komen.
> 
> Volg deze hand leiding voor het [maken van een Azure Red Hat open Shift 4-cluster](tutorial-create-cluster.md).
> Als u specifieke vragen hebt, [kunt u contact met ons](mailto:arofeedback@microsoft.com)opnemen.

Als u Microsoft Azure Red Hat open Shift-toepassingen wilt bouwen en uitvoeren, moet u het volgende doen:

* Installeer versie 2.0.65 (of hoger) van de Azure CLI (of gebruik de Azure Cloud Shell).
* Registreer u voor de `AROGA` functie en de bijbehorende resource providers.
* Maak een Azure Active Directory-Tenant (Azure AD).
* Maak een Azure AD-toepassings object.
* Maak een Azure AD-gebruiker.

In de volgende instructies vindt u een overzicht van deze vereisten.

## <a name="install-the-azure-cli"></a>Azure-CLI installeren

Azure Red Hat open Shift vereist versie 2.0.65 of hoger van de Azure CLI. Als u de Azure CLI al hebt geïnstalleerd, kunt u controleren welke versie u hebt door uit te voeren:

```azurecli
az --version
```

De eerste regel van de uitvoer heeft bijvoorbeeld de CLI-versie `azure-cli (2.0.65)` .

Hier vindt u instructies voor [het installeren van de Azure cli](/cli/azure/install-azure-cli) als u een nieuwe installatie of een upgrade nodig hebt.

U kunt ook de [Azure Cloud shell](../cloud-shell/overview.md)gebruiken. Wanneer u de Azure Cloud Shell gebruikt, moet u ervoor zorgen dat u de **bash** -omgeving selecteert als u de reeks zelf studies voor het [maken en beheren van Azure Red Hat open Shift-cluster](tutorial-create-cluster.md) wilt volgen.

## <a name="register-providers-and-features"></a>Providers en functies registreren

De `Microsoft.ContainerService AROGA` functie,,, en `Microsoft.Solutions` `Microsoft.Compute` `Microsoft.Storage` `Microsoft.KeyVault` `Microsoft.Network` providers moeten hand matig worden geregistreerd bij uw abonnement voordat u uw eerste Azure Red Hat open Shift-cluster implementeert.

Als u deze providers en onderdelen hand matig wilt registreren, gebruikt u de volgende instructies van een bash-shell als u de CLI hebt geïnstalleerd of van de Azure Cloud Shell-sessie (bash) in uw Azure Portal:

1. Als u meerdere Azure-abonnementen hebt, geeft u de relevante abonnements-id op:

    ```azurecli
    az account set --subscription <SUBSCRIPTION ID>
    ```

1. Registreer de functie micro soft. container service AROGA:

    ```azurecli
    az feature register --namespace Microsoft.ContainerService -n AROGA
    ```

1. Registreer de micro soft. Storage-Provider:

    ```azurecli
    az provider register -n Microsoft.Storage --wait
    ```
    
1. Registreer de micro soft. Compute-provider:

    ```azurecli
    az provider register -n Microsoft.Compute --wait
    ```

1. De provider van micro soft. Solutions registreren:

    ```azurecli
    az provider register -n Microsoft.Solutions --wait
    ```

1. Registreer de micro soft. Network-Provider:

    ```azurecli
    az provider register -n Microsoft.Network --wait
    ```

1. Registreer de micro soft.-sleutel kluis provider:

    ```azurecli
    az provider register -n Microsoft.KeyVault --wait
    ```

1. De registratie van de resource provider micro soft. container service vernieuwen:

    ```azurecli
    az provider register -n Microsoft.ContainerService --wait
    ```

## <a name="create-an-azure-active-directory-azure-ad-tenant"></a>Een Azure Active Directory-Tenant (Azure AD) maken

De Azure Red Hat open Shift-service vereist een gekoppelde Azure Active Directory-Tenant (Azure AD) die uw organisatie en de relatie met micro soft vertegenwoordigt. Met uw Azure AD-Tenant kunt u apps registreren, bouwen en beheren, en andere Azure-Services gebruiken.

Als u geen Azure AD kunt gebruiken als Tenant voor uw Azure Red Hat open Shift-cluster of als u een Tenant wilt maken voor het testen, volgt u de instructies in [een Azure AD-Tenant maken voor uw Azure Red Hat open Shift-cluster](howto-create-tenant.md) voordat u doorgaat met deze hand leiding.

## <a name="create-an-azure-ad-user-security-group-and-application-object"></a>Een Azure AD-gebruiker, beveiligings groep en toepassings object maken

Azure Red Hat open Shift vereist machtigingen voor het uitvoeren van taken in uw cluster, zoals het configureren van opslag. Deze machtigingen worden weer gegeven via een [Service-Principal](../active-directory/develop/app-objects-and-service-principals.md#service-principal-object). U wilt ook een nieuwe Active Directory gebruiker maken voor het testen van apps die worden uitgevoerd op uw Azure Red Hat open Shift-cluster.

Volg de instructies in [een Azure AD-App-object en-gebruiker maken](howto-aad-app-configuration.md) om een service-principal te maken, een client geheim en een URL voor verificatie-call back voor uw app te genereren en een nieuwe Azure AD-beveiligings groep en-gebruiker te maken voor toegang tot het cluster.

## <a name="next-steps"></a>Volgende stappen

U bent nu klaar om Azure Red Hat open SHIFT te gebruiken.

Probeer de zelf studie:
> [!div class="nextstepaction"]
> [Een Azure Red Hat OpenShift-cluster maken](tutorial-create-cluster.md)

[azure-cli-install]: /cli/azure/install-azure-cli