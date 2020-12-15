---
title: Een Azure Portal-dashboard maken met Azure CLI
description: 'Quickstart: Meer informatie over het maken van een dashboard in Azure Portal met behulp van de Azure CLI. Een dashboard is een gerichte en georganiseerde weergave van uw cloudresources.'
ms.topic: quickstart
ms.custom: devx-track-azurepowershell
ms.date: 12/4/2020
ms.openlocfilehash: ede915df5cd2967c3b6b700bcb9174c89af8f233
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96745652"
---
# <a name="quickstart-create-an-azure-portal-dashboard-with-azure-cli"></a>Quickstart: Een Azure Portal-dashboard maken met Azure CLI

Een dashboard in de Azure-portal is een gerichte en georganiseerde weergave van uw cloudresources.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Als u meerdere Azure-abonnementen hebt, kiest u het juiste abonnement waarin de resources moeten worden gefactureerd.
Selecteer een abonnement met behulp van de opdracht [az account set](/cli/azure/account#az_account_set):

  ```azurecli
  az account set --subscription 00000000-0000-0000-0000-000000000000
  ```

- Maak een [Azure-resourcegroep](../azure-resource-manager/management/overview.md) met behulp van de opdracht [az group create](/cli/azure/group#az_group_create) of gebruik een bestaande resourcegroep:

  ```azurecli
  az group create --name myResourceGroup --location centralus
  ```

   Een resourcegroep is een logische container waarin Azure-resources worden geïmplementeerd en groepsgewijs worden beheerd.

## <a name="create-a-virtual-machine"></a>Een virtuele machine maken

Maak een virtuele machine met de opdracht [az vm create](/cli/azure/vm#az_vm_create):

```azurecli
az vm create --resource-group myResourceGroup --name SimpleWinVM --image win2016datacenter \
   --admin-username azureuser --admin-password 1StrongPassword$
```

> [!Note]
> Het wachtwoord moet complex zijn.
> Dat is een nieuwe gebruikersnaam en nieuw wachtwoord.
> Het is bijvoorbeeld niet het account dat u gebruikt om u aan te melden bij Azure.
> Zie [gebruikersnaamvereisten](../virtual-machines/windows/faq.md#what-are-the-username-requirements-when-creating-a-vm) en [wachtwoordvereisten](../virtual-machines/windows/faq.md#what-are-the-password-requirements-when-creating-a-vm) voor meer informatie.

De implementatie wordt gestart en duurt doorgaans enkele minuten.
Nadat de implementatie is voltooid, gaat u verder met de volgende sectie.

## <a name="download-the-dashboard-template"></a>De dashboardsjabloon downloaden

Omdat Azure-dashboards resources zijn, kunnen ze als JSON worden weergegeven.
Zie [De structuur van Azure-dashboards](./azure-portal-dashboards-structure.md) voor meer informatie.

Download het volgende bestand: [portal-dashboard-template-testvm.json](https://raw.githubusercontent.com/Azure/azure-docs-powershell-samples/master/azure-portal/portal-dashboard-template-testvm.json).

Wijzig de volgende waarden in uw eigen waarden om de gedownloade sjabloon aan te passen:

* `<subscriptionID>`: Uw abonnement
* `<rgName>`: Resourcegroep, bijvoorbeeld `myResourceGroup`
* `<vmName>`: Naam van de virtuele machine, bijvoorbeeld `SimpleWinVM`
* `<dashboardTitle>`: De titel van het dashboard, bijvoorbeeld `Simple VM Dashboard`
* `<location>`: Uw Azure-regio, bijvoorbeeld `centralus`

Zie [Referentie voor Microsoft Portal-dashboardsjablonen](/azure/templates/microsoft.portal/dashboards) voor meer informatie.

## <a name="deploy-the-dashboard-template"></a>De dashboardsjabloon implementeren

U kunt de sjabloon nu implementeren vanuit Azure CLI.

1. Voer de opdracht [az portal dash board create](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_create) uit om de sjabloon te implementeren:

   ```azurecli
   az portal dashboard create --resource-group myResourceGroup --name 'Simple VM Dashboard' \
      --input-path portal-dashboard-template-testvm.json --location centralus
   ```

1. Controleer of het dashboard is gemaakt door de opdracht [az portal dash board show](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_show) uit te voeren:

   ```azurecli
   az portal dashboard show --resource-group myResourceGroup --name 'Simple VM Dashboard'
   ```

Als u alle dashboards voor het huidige abonnement wilt zien, gebruikt u [az portal dash board List](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_list):

```azurecli
az portal dashboard list
```

U kunt ook alle dashboards voor een resourcegroep bekijken:

```azurecli
az portal dashboard list --resource-group myResourceGroup
```

U kunt een dashboard bijwerken met behulp van de opdracht [az portal dash board update](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_update):

```azurecli
az portal dashboard update --resource-group myResourceGroup --name 'Simple VM Dashboard' \
   --input-path portal-dashboard-template-testvm.json --location centralus
```

Controleer of u in Azure Portal gegevens over de virtuele machine kunt zien.

1. In de Azure-portal selecteert u **Dashboard**.

   ![Navigatie in de Azure-portal naar dashboard](media/quickstart-portal-dashboard-powershell/navigate-to-dashboards.png)

1. Op de dashboardpagina selecteert u **Eenvoudig VM-dashboard**.

   ![Navigeren naar een eenvoudig VM-dashboard](media/quickstart-portal-dashboard-powershell/select-simple-vm-dashboard.png)

1. Bekijk het dashboard. U kunt zien dat een deel van de inhoud statisch is, maar er zijn ook grafieken waarin de prestaties van de virtuele machine worden weergegeven.

   ![Eenvoudig VM-dashboard bekijken](media/quickstart-portal-dashboard-powershell/review-simple-vm-dashboard.png)

## <a name="clean-up-resources"></a>Resources opschonen

Als u de virtuele machine en het bijbehorende dashboard wilt verwijderen, verwijdert u de resourcegroep waarin deze zich bevinden.

> [!CAUTION]
> In het volgende voorbeeld worden de opgegeven resourcegroep en alle resources erin verwijderd.
> Als resources buiten het bereik van dit artikel in de opgegeven resourcegroep bestaan, worden ze ook verwijderd.

```azurecli
az group delete --name myResourceGroup
```

Als u alleen het dashboard wilt verwijderen, gebruikt u de opdracht [az portal dash board delete](/cli/azure/ext/portal/portal/dashboard#ext_portal_az_portal_dashboard_delete):

```azurecli
az portal dashboard delete --resource-group myResourceGroup --name "Simple VM Dashboard"
```

## <a name="next-steps"></a>Volgende stappen

Zie [az portal dash board](/cli/azure/ext/portal/portal/dashboard) voor meer informatie over Azure CLI-ondersteuning voor dashboards.
