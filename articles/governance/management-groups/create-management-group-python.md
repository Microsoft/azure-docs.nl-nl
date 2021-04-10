---
title: 'Snelstartgids: een beheer groep maken met python'
description: In deze Quick Start gebruikt u python om een beheer groep te maken om uw resources in een resource hiërarchie in te delen.
ms.date: 01/29/2021
ms.topic: quickstart
ms.custom: devx-track-python
ms.openlocfilehash: e3c55cc14a8ac980318fd0de9485a3e0ca31b582
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100101757"
---
# <a name="quickstart-create-a-management-group-with-python"></a>Snelstartgids: een beheer groep maken met python

Managementgroepen zijn containers die u helpen bij het beheren van toegang, beleid en naleving voor meerdere abonnementen. Maak deze containers om een doeltreffende en efficiënte hiërarchie te maken die kan worden gebruikt met [Azure Policy](../policy/overview.md) en [op rollen gebaseerd toegangsbeheer van Azure](../../role-based-access-control/overview.md). Zie [Uw resources indelen met Azure-beheergroepen](overview.md) voor meer informatie over beheergroepen.

Het kan tot vijftien minuten duren voordat de eerste beheergroep die in de map is gemaakt, is voltooid. Er zijn processen die de eerste keer worden uitgevoerd om de service voor beheergroepen in Azure in te stellen voor uw map. U ontvangt een melding wanneer het proces is voltooid. Zie [Eerste configuratie van beheergroepen](./overview.md#initial-setup-of-management-groups) voor meer informatie.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.

- Elke Azure AD-gebruiker in de tenant kan een beheergroep maken zonder dat de schrijfmachtiging voor beheergroepen is toegewezen als [Hiërarchie beschermen](./how-to/protect-resource-hierarchy.md#setting---require-authorization) niet is ingeschakeld. Deze nieuwe beheergroep wordt een onderliggend element van de hoofdbeheergroep of de [standaard beheergroep](./how-to/protect-resource-hierarchy.md#setting---default-management-group) en de maker krijgt de roltoewijzing "Eigenaar". Met de beheergroep-service kan deze functie worden toegewezen, zodat roltoewijzingen niet nodig zijn op hoofdmapniveau. Gebruikers hebben geen toegang tot de hoofdbeheergroep wanneer deze wordt gemaakt. Om te voorkomen dat de drempel van het vinden van de Azure AD Global Admins om beheergroepen te kunnen gebruiken te hoog is, wordt het maken van de eerste beheergroepen op hoofdmapniveau toegestaan.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="add-the-resource-graph-library"></a>De Resource Graph-bibliotheek toevoegen

Als u python wilt inschakelen om beheer groepen te beheren, moet u de bibliotheek toevoegen. Deze bibliotheek werkt waar Python kan worden gebruikt, inclusief [bash in Windows 10](/windows/wsl/install-win10) of lokaal geïnstalleerd.

1. Controleer of de meest recente versie van Python is geïnstalleerd (minimaal **3.8**). Als deze nog niet is geïnstalleerd, downloadt u deze op [Python.org](https://www.python.org/downloads/).

1. Controleer of de meest recente Azure CLI is geïnstalleerd (minimaal **2.5.1**). Als deze nog niet is geïnstalleerd, raadpleegt u [De Azure CLI installeren](/cli/azure/install-azure-cli).

   > [!NOTE]
   > Azure CLI is vereist om Python in te schakelen voor gebruik van de **op CLI gebaseerde verificatie** in de volgende voorbeelden. Zie [Authenticate using the Azure management libraries for Python](/azure/developer/python/azure-sdk-authenticate) (Verificatie met de Azure-beheerbibliotheken voor Python) voor meer informatie over andere opties.

1. Verifieer via Azure CLI.

   ```azurecli
   az login
   ```

1. Installeer de vereiste bibliotheken voor beheer groepen in de gewenste python-omgeving:

   ```bash
   # Add the management groups library for Python
   pip install azure-mgmt-managementgroups

   # Add the Resources library for Python
   pip install azure-mgmt-resource

   # Add the CLI Core library for Python for authentication (development only!)
   pip install azure-cli-core
   ```

   > [!NOTE]
   > Als Python is geïnstalleerd voor alle gebruikers, moet deze opdracht worden uitgevoerd vanaf een console met verhoogde bevoegdheden.

1. Controleer of de bibliotheken zijn geïnstalleerd. `azure-mgmt-managementgroups`**0.2.0** of hoger moet `azure-mgmt-resource` **9.0.0** of hoger zijn, en `azure-cli-core` moet **2.5.0** of hoger zijn.

   ```bash
   # Check each installed library
   pip show azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
   ```

## <a name="create-the-management-group"></a>De beheergroep maken

1. Maak het python-script en sla de volgende bron op als `mgCreate.py` :

   ```python
   # Import management group classes
   from azure.mgmt.managementgroups import ManagementGroupsAPI
   
   # Import specific methods and models from other libraries
   from azure.common.credentials import get_azure_cli_credentials
   from azure.common.client_factory import get_client_from_cli_profile
   from azure.mgmt.resource import ResourceManagementClient, SubscriptionClient
   
   # Wrap all the work in a function
   def createmanagementgroup( strName ):
       # Get your credentials from Azure CLI (development only!) and get your subscription list
       subsClient = get_client_from_cli_profile(SubscriptionClient)
       subsRaw = []
       for sub in subsClient.subscriptions.list():
           subsRaw.append(sub.as_dict())
       subsList = []
       for sub in subsRaw:
           subsList.append(sub.get('subscription_id'))
       
       # Create management group client and set options
       mgClient = get_client_from_cli_profile(ManagementGroupsAPI)
       mg_request = {'name': strName, 'display_name': strName}
       
       # Create management group
       mg = mgClient.management_groups.create_or_update(group_id=strName,create_management_group_request=mg_request)
       
       # Show results
       print(mg)
   
   createmanagementgroup("MyNewMG")
   ```

1. Verifieer met Azure CLI met `az login` .

1. Voer de volgende opdracht in de terminal in:

   ```bash
   py mgCreate.py
   ```

Het resultaat van het maken van de beheer groep wordt uitgevoerd naar de-console als een- `LROPoller` object.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de geïnstalleerde bibliotheken uit uw Python-omgeving wilt verwijderen, kunt u dit doen met de volgende opdracht:

```bash
# Remove the installed libraries from the Python environment
pip uninstall azure-mgmt-managementgroups azure-mgmt-resource azure-cli-core
```

## <a name="next-steps"></a>Volgende stappen

In deze quickstart hebt u een beheergroep gemaakt om uw resource-hiërarchie te organiseren. De beheergroep kan abonnementen of andere beheergroepen bevatten.

Ga verder met het volgende voor meer informatie over beheergroepen en het beheren van uw resource-hiërarchie:

> [!div class="nextstepaction"]
> [Uw resources beheren met beheergroepen](./manage.md)