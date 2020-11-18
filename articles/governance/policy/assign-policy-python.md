---
title: 'Quickstart: Nieuwe beleidstoewijzing met Python'
description: In deze quickstart gebruikt u Python om een Azure Policy-toewijzing te maken om niet-compatibele resources te identificeren.
ms.date: 10/14/2020
ms.topic: quickstart
ms.custom: devx-track-python, devx-track-azurecli
ms.openlocfilehash: aa67b3a2bae2beb62daa6e64871411ee760f802b
ms.sourcegitcommit: 0a9df8ec14ab332d939b49f7b72dea217c8b3e1e
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/18/2020
ms.locfileid: "94832192"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-python"></a>Quickstart: Een beleidstoewijzing maken om niet-compatibele resources met Python te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources. In deze snelstart maakt u een beleidstoewijzing om te identificeren welke virtuele machines geen beheerde schijven gebruiken. Zodra de toewijzing is voltooid, kunt u de virtuele machines identificeren die _niet-compatibel_ zijn.

De Python-bibliotheek wordt gebruikt voor het beheren van Azure-resources vanaf de opdrachtregel of in scripts. In deze handleiding wordt uitgelegd hoe u de Python-bibliotheek gebruikt om een beleidstoewijzing te maken.

## <a name="prerequisites"></a>Vereisten

Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-policy-library"></a>De Policy-bibliotheek toevoegen

Om Python te laten werken met Azure Policy, moet de bibliotheek worden toegevoegd. Deze bibliotheek werkt waar Python kan worden gebruikt, inclusief [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Python is geïnstalleerd (minimaal **3.8**). Als deze nog niet is geïnstalleerd, downloadt u deze op [Python.org](https://www.python.org/downloads/).

1. Controleer of de meest recente Azure CLI is geïnstalleerd (minimaal **2.5.1**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI is vereist om Python in te schakelen voor gebruik van de **op CLI gebaseerde verificatie** in de volgende voorbeelden. Zie [Authenticate using the Azure management libraries for Python](/azure/developer/python/azure-sdk-authenticate) (Verificatie met de Azure-beheerbibliotheken voor Python) voor meer informatie over andere opties.

1. Verifieer via Azure CLI.

   ```azurecli
   az login
   ```

1. Installeer in de gewenste Python-omgeving de vereiste bibliotheken voor Azure Resource Graph:

   ```bash
   # Add the Python library for Python
   pip install azure-mgmt-policyinsights

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Als Python is geïnstalleerd voor alle gebruikers, moet deze opdracht worden uitgevoerd vanaf een console met verhoogde bevoegdheden.

1. Controleer of de bibliotheken zijn geïnstalleerd. `azure-mgmt-policyinsights` moet **0.5.0** of hoger zijn, `azure-mgmt-resource` moet **9.0.0** of hoger zijn en `azure-cli-core` moet **2.5.0** of hoger zijn.

   ```bash
   # Check each installed library
   pip show azure-mgmt-policyinsights azure-mgmt-resource azure-cli-core
   ```

## <a name="create-a-policy-assignment"></a>Een beleidstoewijzing maken

In deze quickstart maakt u een beleidstoewijzing en wijst u de definitie **VM's controleren die niet gebruikmaken van beheerde schijven** (`06a78e20-9358-41c9-923c-fb736d382a4d`) toe. Deze beleidsdefinitie identificeert resources die niet voldoen aan de voorwaarden die zijn vastgelegd in de beleidsdefinitie.

Voer de volgende code uit om een nieuwe beleidstoewijzing te maken:

```python
# Import specific methods and models from other libraries
from azure.common.credentials import get_azure_cli_credentials
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient
from azure.mgmt.resource.policy.models import PolicyAssignment

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Create details for the assignment
policyAssignmentDetails = PolicyAssignment(display_name="Audit VMs without managed disks Assignment", policy_definition_id="/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d", scope="{scope}", description="Shows all virtual machines not using managed disks")

# Create new policy assignment
policyAssignment = policyClient.policy_assignments.create("{scope}", "audit-vm-manageddisks", policyAssignmentDetails)

# Show results
print(policyAssignment)
```

In de voorgaande opdrachten wordt de volgende informatie gebruikt:

Toewijzingsdetails:
- **display_name**: de weergavenaam voor de beleidstoewijzing. In dit geval gebruikt u de toewijzing _Virtuele machines zonder beheerde schijven controleren_.
- **policy_definition_id**: het pad van de beleidsdefinitie, op basis waarvan u de toewijzing maakt. In dit geval is het de id van de beleidsdefinitie _Virtuele machines zonder beheerde schijven controleren_. In dit voorbeeld is de beleidsdefinitie een ingebouwde module en bevat het pad geen gegevens over de beheergroep of het abonnement.
- **scope**: een bereik bepaalt voor welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Het kan variëren van een beheergroep tot een afzonderlijke resource. Zorg dat u `{scope}` vervangt door een van de volgende patronen:
  - Beheergroep: `/providers/Microsoft.Management/managementGroups/{managementGroup}`
  - Abonnement: `/subscriptions/{subscriptionId}`
  - Resourcegroep: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
  - Resource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`
- **description**: een grondigere uitleg van wat het beleid doet of waarom het aan dit bereik is toegewezen.

Toewijzing maken:

- Bereik: dit bereik bepaalt waar de beleidstoewijzing wordt opgeslagen. Het bereik dat in de toewijzingsdetails is ingesteld, moet binnen dit bereik bestaan.
- Naam: de werkelijke naam van de toewijzing. Voor dit voorbeeld is _audit-vm-manageddisks_ gebruikt.
- Beleidstoewijzing: het Python-object **PolicyAssignment** dat in de vorige stap is gemaakt.

U kunt nu niet-compatibele resources identificeren om inzicht te krijgen in de nalevingsstatus van uw omgeving.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Gebruik de volgende informatie om te identificeren welke resources niet compatibel zijn met de beleidstoewijzing die u hebt gemaakt. Voer de volgende code uit:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.policyinsights._policy_insights_client import PolicyInsightsClient
from azure.mgmt.policyinsights.models import QueryOptions

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyInsightsClient = get_client_from_cli_profile(PolicyInsightsClient)

# Set the query options
queryOptions = QueryOptions(filter="IsCompliant eq false and PolicyAssignmentId eq 'audit-vm-manageddisks'",apply="groupby((ResourceId))")

# Fetch 'latest' results for the subscription
results = policyInsightsClient.policy_states.list_query_results_for_subscription(policy_states_resource="latest", subscription_id="{subscriptionId}", query_options=queryOptions)

# Show results
print(results)
```

Vervang `{subscriptionId}` door het abonnement waarvoor u de complianceresultaten voor deze beleidstoewijzing wilt zien. Zie [Beleidsstatusmethoden](/python/api/azure-mgmt-policyinsights/azure.mgmt.policyinsights.operations.policystatesoperations#methods) voor een lijst met andere bereiken en manieren om de gegevens samen te vatten.

De resultaten zien er ongeveer als volgt uit:

```output
{
    'additional_properties': {
        '@odata.nextLink': None
    },
    'odatacontext': 'https://management.azure.com/subscriptions/{subscriptionId}/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest',
    'odatacount': 12,
    'value': [{data}]
}
```

De resultaten komen overeen met wat u ziet op het tabblad **Resourcenaleving** van een beleidstoewijzing in de Azure Portal-weergave.

## <a name="clean-up-resources"></a>Resources opschonen

Voer de volgende opdracht uit om de beleidstoewijzing te verwijderen:

```python
# Import specific methods and models from other libraries
from azure.common.client_factory import get_client_from_cli_profile
from azure.mgmt.resource.policy import PolicyClient

# Get your credentials from Azure CLI (development only!) and get your subscription list
policyClient = get_client_from_cli_profile(PolicyClient)

# Delete the policy assignment
policyAssignment = policyClient.policy_assignments.delete("{scope}", "audit-vm-manageddisks")

# Show results
print(policyAssignment)
```

Vervang `{scope}` door hetzelfde bereik dat u hebt gebruikt om de beleidstoewijzing te maken.

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Voor meer informatie over het toewijzen van beleidsdefinities om te controleren of nieuwe resources compatibel zijn gaat u verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)
