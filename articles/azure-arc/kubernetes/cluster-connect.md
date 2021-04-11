---
title: Gebruik cluster Connect om verbinding te maken met Kubernetes-clusters die zijn ingeschakeld voor Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Gebruik cluster Connect om veilig verbinding te maken met Kubernetes-clusters met Azure Arc.
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451028"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Gebruik cluster Connect om verbinding te maken met Kubernetes-clusters die zijn ingeschakeld voor Azure Arc

Met cluster Connect kunt u veilig verbinding maken met Kubernetes-clusters die zijn ingeschakeld voor Azure, zonder dat er een binnenkomende poort moet worden ingeschakeld op de firewall. Als u toegang hebt tot de `apiserver` van de Kubernetes-cluster met Arc, worden de volgende scenario's ingeschakeld:
* Interactieve fout opsporing en probleem oplossing inschakelen.
* Bied cluster toegang tot Azure-Services voor [aangepaste locaties](custom-locations.md) en andere resources die boven op de service zijn gemaakt.

Een conceptueel overzicht van deze functie is beschikbaar in [cluster Connect-Azure Arc enabled Kubernetes-](conceptual-cluster-connect.md) artikel.

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Vereisten   

- [Azure cli installeren of upgraden](https://docs.microsoft.com/cli/azure/install-azure-cli) naar versie >= 2.16.0

- Installeer de `connectedk8s` Azure cli-extensie van versie >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Als u de `connectedk8s` uitbrei ding al hebt geïnstalleerd, werkt u de extensie bij naar de nieuwste versie:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Er is een bestaand Kubernetes-verbonden Azure-Arc-cluster ingeschakeld.
    - Als u nog geen cluster hebt verbonden, gebruikt u onze [Quick](quickstart-connect-cluster.md)start.
    - [Werk uw agents](agent-upgrade.md#manually-upgrade-agents) bij naar versie >= 1.1.0.

- Schakel de cluster verbinding in op een Azure Arc enabled Kubernetes-cluster door de volgende opdracht uit te voeren op een computer waarop het `kubeconfig` bestand naar het cluster van bezorgdheid wordt gewijzen:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Schakel de onderstaande eind punten voor uitgaande toegang in naast de waarden die worden vermeld onder [een Kubernetes-cluster verbinden met Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

    | Eindpunt | Poort |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Gebruik

Er worden twee verificatie opties ondersteund met de functie cluster Connect: 
* Azure Active Directory (Azure AD) 
* Token voor service account

### <a name="option-1-azure-active-directory"></a>Optie 1: Azure Active Directory

1. Maak met het `kubeconfig` bestand dat verwijst naar het `apiserver` van uw Kubernetes-cluster, een ClusterRoleBinding of RoleBinding naar de Azure AD-entiteit (Service-Principal of gebruiker) die toegang vereist:

    **Voor gebruiker:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Voor Azure AD-toepassing:**

    1. Haal de `objectId` koppeling op met uw Azure AD-toepassing:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Maak een ClusterRoleBinding-of RoleBinding naar de Azure AD-entiteit (Service-Principal of gebruiker) die toegang moet hebben tot dit cluster:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Nadat u zich hebt aangemeld bij Azure CLI met behulp van de Azure AD-entiteit, haalt u de cluster verbinding `kubeconfig` op die u nodig hebt voor communicatie met het cluster vanaf elke locatie (vanaf zelfs buiten de firewall rond het cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Gebruiken `kubectl` voor het verzenden van aanvragen naar het cluster:

    ```console
    kubectl get pods
    ```
    
    U ziet nu een antwoord van het cluster met de lijst met alle peulen onder de `default` naam ruimte.

### <a name="option-2-service-account-bearer-token"></a>Optie 2: Service account Bearer-token

1. `kubeconfig` `apiserver` Maak een service account in een naam ruimte met het bestand dat aan het van uw Kubernetes-cluster is gekoppeld (met de volgende opdracht maakt u het in de standaard naam ruimte):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Maak ClusterRoleBinding of RoleBinding om dit [Service account de juiste machtigingen voor het cluster](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding)te geven:

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Het token van het service account ophalen met behulp van de volgende opdrachten

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Haal de cluster verbinding `kubeconfig` die nodig is voor communicatie met het cluster vanaf elke locatie (van zelfs buiten de firewall rond het cluster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Gebruiken `kubectl` voor het verzenden van aanvragen naar het cluster:

    ```console
    kubectl get pods
    ```

    U ziet nu een antwoord van het cluster met de lijst met alle peulen onder de `default` naam ruimte.

## <a name="known-limitations"></a>Bekende beperkingen

Als de Azure AD-entiteit die wordt gebruikt deel uitmaakt van meer dan 200 groepen, wordt de volgende fout geconstateerd wanneer er aanvragen worden gedaan naar het Kubernetes-cluster:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

U kunt deze fout als volgt verhelpen:
1. Maak een [Service-Principal](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli)die minder waarschijnlijk lid is van meer dan 200 groepen.
1. [Meld](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) u aan bij Azure CLI met de Service-Principal voordat u de `az connectedk8s proxy` opdracht uitvoert.

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Azure AD RBAC](azure-rbac.md) instellen voor uw clusters