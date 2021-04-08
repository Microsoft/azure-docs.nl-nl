---
title: Toegang tot een delegatie verwijderen
description: Meer informatie over het verwijderen van de toegang tot resources die aan een service provider voor Azure Lighthouse zijn overgedragen.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: c53b678ba6e37ece1bcaf2860abceb9eea980532
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100555783"
---
# <a name="remove-access-to-a-delegation"></a>Toegang tot een delegatie verwijderen

Nadat het abonnement of de resource groep van een klant is gedelegeerd aan een service provider voor [Azure Lighthouse](../overview.md), kan de delegatie zo nodig worden verwijderd. Zodra een overdracht is verwijderd, is de [Azure gedelegeerde resource beheer](../concepts/azure-delegated-resource-management.md) -toegang die eerder is verleend aan gebruikers in de service provider Tenant, niet meer van toepassing.

Het verwijderen van een overdracht kan worden uitgevoerd door een gebruiker in de Tenant van de klant of de Tenant van de service provider, zolang de gebruiker de juiste machtigingen heeft.

> [!TIP]
> Hoewel we in dit onderwerp naar service providers en klanten verwijzen, kunnen [bedrijven die meerdere tenants beheren](../concepts/enterprise.md) , dezelfde processen gebruiken.

## <a name="customers"></a>Customers

Gebruikers in de Tenant van de klant die een rol hebben met de `Microsoft.Authorization/roleAssignments/write` machtiging, zoals de [eigenaar](../../role-based-access-control/built-in-roles.md#owner), kunnen de toegang van de service provider tot dat abonnement (of de resource groepen in dat abonnement) verwijderen. De gebruiker kan dit doen door naar de [pagina service providers](view-manage-service-providers.md#add-or-remove-service-provider-offers) van de Azure portal te gaan, de aanbieding op het scherm voor de **service provider** te zoeken en het prullenbak pictogram in de rij voor die aanbieding te selecteren.

Nadat de verwijdering is bevestigd, hebben gebruikers in de Tenant van de service provider geen toegang meer tot de resources die eerder zijn gedelegeerd.

## <a name="service-providers"></a>Service providers

Gebruikers in een Tenant beheren kunnen de toegang tot gedelegeerde resources verwijderen als ze de [rol van beheerde services registratie toewijzing verwijderen](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) hebben gekregen voor de resources van de klant. Als deze rol niet aan gebruikers van een service provider is toegewezen, kan de overdracht alleen worden verwijderd door een gebruiker in de Tenant van de klant.

In het onderstaande voor beeld ziet u een toewijzing die de functie voor het verwijderen van de **registratie toewijzing van beheerde services** verleent die kan worden opgenomen in een parameter bestand tijdens het [voorbereidings proces](onboard-customer.md):

```json
    "authorizations": [ 
        { 
            "principalId": "cfa7496e-a619-4a14-a740-85c5ad2063bb", 
            "principalIdDisplayName": "MSP Operators", 
            "roleDefinitionId": "91c1777a-f3dc-4fae-b103-61d183457e46" 
        } 
    ] 
```

Deze rol kan ook worden geselecteerd in een **autorisatie** bij [het maken van een beheerde service aanbieding](../../marketplace/plan-managed-service-offer.md) om te publiceren op Azure Marketplace.

Een gebruiker met deze machtiging kan een delegering op een van de volgende manieren verwijderen.

### <a name="azure-portal"></a>Azure Portal

1. Navigeer naar de [pagina mijn klanten](view-manage-customers.md).
2. Selecteer **delegaties**.
3. Zoek de overdracht die u wilt verwijderen en selecteer vervolgens het prullenbak pictogram dat in de rij wordt weer gegeven.

### <a name="powershell"></a>PowerShell

```azurepowershell-interactive
# Log in first with Connect-AzAccount if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory 

Login-AzAccount

# Select the subscription that is delegated - or contains the delegated resource group(s)

Select-AzSubscription -SubscriptionName "<subscriptionName>"

# Get the registration assignment

Get-AzManagedServicesAssignment -Scope "/subscriptions/{delegatedSubscriptionId}"

# Delete the registration assignment

Remove-AzManagedServicesAssignment -ResourceId "/subscriptions/{delegatedSubscriptionId}/providers/Microsoft.ManagedServices/registrationAssignments/{assignmentGuid}"
```

### <a name="azure-cli"></a>Azure CLI

```azurecli-interactive
# Log in first with az login if you're not using Cloud Shell

# Sign in as a user from the managing tenant directory

az login

# Select the subscription that is delegated – or contains the delegated resource group(s)

az account set -s <subscriptionId/name>

# List registration assignments

az managedservices assignment list

# Delete the registration assignment

az managedservices assignment delete --assignment <id or full resourceId>
```

## <a name="next-steps"></a>Volgende stappen

- Meer informatie over [gedelegeerd Azure-resourcebeheer](../concepts/azure-delegated-resource-management.md).
- [Bekijk en beheer klanten](view-manage-customers.md) door naar **mijn klanten** te gaan in de Azure Portal.
- Meer informatie over het [bijwerken van een eerdere delegering](update-delegation.md).
