---
title: Een service-principal voor het voorbereiden van Azure-Arc maken (preview)
services: azure-arc
ms.service: azure-arc
ms.date: 02/09/2021
ms.topic: article
author: mlearned
ms.author: mlearned
description: 'Een service-principal maken die geschikt is voor Azure-Arc '
keywords: Kubernetes, Arc, Azure, containers
ms.openlocfilehash: 8772cf7634d9a833af120784e3e7868b41d202c4
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 02/14/2021
ms.locfileid: "100390484"
---
# <a name="create-an-azure-arc-enabled-onboarding-service-principal-preview"></a>Een service-principal voor het voorbereiden van Azure-Arc maken (preview)

## <a name="overview"></a>Overzicht

U kunt Kubernetes-clusters aan Azure Arc toevoegen met Service-principals met beperkte bevoegdheden voor roltoewijzingen. Deze mogelijkheid is handig voor continue integratie en doorlopende implementatie-pijp lijnen (CI/CD), zoals Azure-pijp lijnen en GitHub acties.

Volg de volgende stappen voor informatie over het gebruik van service-principals voor onboarding van Kubernetes-clusters naar Azure Arc.

## <a name="create-a-new-service-principal"></a>Een nieuwe service-principal maken

Maak een nieuwe service-principal met een informatieve naam die uniek is voor uw Azure Active Directory-Tenant.

```console
az ad sp create-for-RBAC --skip-assignment --name "https://azure-arc-for-k8s-onboarding"
```

**Uitvoer**

```console
{
  "appId": "22cc2695-54b9-49c1-9a73-2269592103d8",
  "displayName": "azure-arc-for-k8s-onboarding",
  "name": "https://azure-arc-for-k8s-onboarding",
  "password": "09d3a928-b223-4dfe-80e8-fed13baa3b3d",
  "tenant": "72f988bf-86f1-41af-91ab-2d7cd011db47"
}
```

## <a name="assign-permissions"></a>Machtigingen toewijzen

Wijs de rol ' Kubernetes cluster-Azure Arc-onboarding ' toe aan de zojuist gemaakte service-principal. Met deze ingebouwde Azure-rol met beperkte machtigingen kan de principal geen clusters naar Azure registreren. De principal met deze toegewezen rol kan geen andere clusters of resources binnen het abonnement bijwerken, verwijderen of wijzigen.

Op basis van de beperkte mogelijkheden kunnen klanten deze principal eenvoudig opnieuw gebruiken om meerdere clusters uit te breiden.

U kunt de machtigingen verder beperken door het juiste argument door te geven `--scope` bij het toewijzen van de rol. Hierdoor kunnen klanten de cluster registratie beperken. De volgende scenario's worden ondersteund door verschillende `--scope` para meters:

| Resource  | Argument voor `scope`| Effect |
| ------------- | ------------- | ------------- |
| Abonnement | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333` | Service-Principal kan elk cluster in een bestaande resource groep in het opgegeven abonnement registreren. |
| Resourcegroep | `--scope /subscriptions/0b1f6471-1bf0-4dda-aec3-111122223333/resourceGroups/myGroup`  | Service-Principal kan clusters __alleen__ registreren in de resource groep `myGroup` . |

```console
az role assignment create \
    --role 34e09817-6cbe-4d01-b1a2-e0eac5743d41 \      # this is the id for the built-in role
    --assignee 22cc2695-54b9-49c1-9a73-2269592103d8 \  # use the appId from the new SP
    --scope /subscriptions/<<SUBSCRIPTION_ID>>         # apply the appropriate scope
```

**Uitvoer**

```console
{
  "canDelegate": null,
  "id": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleAssignments/fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "name": "fbd819a9-01e8-486b-9eb9-f177ba400ba6",
  "principalId": "ddb0ddb4-ba84-4cde-b936-affc272a4b90",
  "principalType": "ServicePrincipal",
  "roleDefinitionId": "/subscriptions/<<SUBSCRIPTION_ID>>/providers/Microsoft.Authorization/roleDefinitions/34e09817-6cbe-4d01-b1a2-e0eac5743d41",
  "scope": "/subscriptions/<<SUBSCRIPTION_ID>>",
  "type": "Microsoft.Authorization/roleAssignments"
}
```

## <a name="use-service-principal-with-the-azure-cli"></a>De Service-Principal gebruiken met de Azure CLI

Raadpleeg de zojuist gemaakte service-principal met de volgende opdrachten:

```azurecli
az login --service-principal -u mySpnClientId -p mySpnClientSecret --tenant myTenantID
az connectedk8s connect -n myConnectedClusterName -g myResoureGroupName
```

## <a name="next-steps"></a>Volgende stappen

* [Azure Policy gebruiken om de cluster configuratie te bepalen](./use-azure-policy.md)
