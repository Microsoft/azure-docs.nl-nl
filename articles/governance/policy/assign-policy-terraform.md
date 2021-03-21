---
title: 'Quickstart: Nieuwe beleidstoewijzing met Terraform'
description: In deze quickstart gebruikt u Terraform en HCL-syntax om een beleidstoewijzing te maken om niet-conforme resources te identificeren.
ms.date: 10/27/2020
ms.topic: quickstart
ms.openlocfilehash: dc4dae2dc6e43e7532117bf64af3ce97ddc7c496
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "93104906"
---
# <a name="quickstart-create-a-policy-assignment-to-identify-non-compliant-resources-using-terraform"></a>Quickstart: Een beleidstoewijzing maken om niet-conforme resources met Terraform te identificeren

De eerste stap in het begrijpen van naleving in Azure is het identificeren van de status van uw resources.
In deze quickstart gaat u een beleidstoewijzing maken voor het identificeren van virtuele machines die geen beheerde schijven gebruiken.

Als u dit proces helemaal hebt doorlopen, kunt u virtuele machines identificeren die geen beheerde schijven gebruiken. Ze zijn _niet-compatibel_ met de beleidstoewijzing.

## <a name="prerequisites"></a>Vereisten

- Als u nog geen Azure-abonnement hebt, maakt u een [gratis account](https://azure.microsoft.com/free/) voordat u begint.
- [Terraform](https://www.terraform.io/) versie 0.12.0 of hoger geconfigureerd in uw omgeving.
  Zie [Terraform configureren met behulp van Azure Cloud Shell](/azure/developer/terraform/get-started-cloud-shell)voor instructies.
- Voor deze quickstart moet u versie 2.13.0 of hoger van Azure CLI uitvoeren. Voer `az --version` uit om de versie te bekijken. Zie [Azure CLI installeren](/cli/azure/install-azure-cli) als u de CLI wilt installeren of een upgrade wilt uitvoeren.

## <a name="create-the-terraform-configuration-variable-and-output-file"></a>De configuratie, de variabele en het uitvoerbestand van Terraform maken

In deze quickstart maakt u een beleidstoewijzing en wijst u de definitie **Controleer virtuele machines die niet gebruikmaken van beheerde schijven** (`06a78e20-9358-41c9-923c-fb736d382a4d`) toe. Deze beleidsdefinitie identificeert resources die niet voldoen aan de voorwaarden die zijn vastgelegd in de beleidsdefinitie.

Configureer eerst de configuratie, de variabele en het uitvoerbestand van Terraform De Terraform-resources voor Azure Policy gebruiken de [Azure-provider](https://www.terraform.io/docs/providers/azurerm/index.html).

1. Maak een nieuwe map met de naam `policy-assignment` en zet de mappen hierin.

1. Maak `main.tf` met de volgende code:

   ```hcl
   provider "azurerm" {
       version = "~>2.0"
       features {}
   }
   
   resource "azurerm_policy_assignment" "auditvms" {
       name = "audit-vm-manageddisks"
       scope = var.cust_scope
       policy_definition_id = "/providers/Microsoft.Authorization/policyDefinitions/06a78e20-9358-41c9-923c-fb736d382a4d"
       description = "Shows all virtual machines not using managed disks"
       display_name = "Audit VMs without managed disks Assignment"
   }
   ```
1. Maak `variables.tf` met de volgende code:

   ```hcl
   variable "cust_scope" {
       default = "{scope}"
   }
   ```

   Het bereik bepaalt op welke resources of groep resources de beleidstoewijzing wordt afgedwongen. Het kan variëren van een beheergroep tot een afzonderlijke resource. Zorg dat u `{scope}` vervangt door een van de volgende patronen:

   - Abonnement: `/subscriptions/{subscriptionId}`
   - Resourcegroep: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}`
   - Resource: `/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/{resourceProviderNamespace}/[{parentResourcePath}/]`

1. Maak `output.tf` met de volgende code:

   ```hcl
   output "assignment_id" {
       value = azurerm_policy_assignment.auditvms.id
   }
   ```

## <a name="initialize-terraform-and-create-plan"></a>Terraform initialiseren en een plan maken

Initialiseer vervolgens Terraform om de benodigde providers te downloaden en maak vervolgens een plan.

1. Voer de opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) uit. Met deze opdracht worden de Azure-modules gedownload die vereist zijn voor het maken van de Azure-resources in de Terraform-configuratie.

   ```bash
   terraform init
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-initialize.png" alt-text="Schermopname van het uitvoeren van de terraform init-opdracht waarin het downloaden van de azurerm-module en het succesbericht wordt weergegeven.":::

1. Verifieer met [Azure CLI](/cli/azure/) voor Terraform. Zie voor meer informatie [Azure-provider: Verifiëren met behulp van de Azure CLI](https://www.terraform.io/docs/providers/azurerm/guides/azure_cli.html).

   ```bash
   az login
   ```

1. Maak het uitvoeringsplan met de opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) en de parameter **out**.

   ```bash
   terraform plan -out assignment.tfplan
   ```

   :::image type="content" source="./media/assign-policy-terraform/terraform-plan-out.png" alt-text="Schermopname van het uitvoeren van de opdracht ‘terraform plan’ en parameter ‘out’ om de Azure-resource weer te geven die zou worden gemaakt.":::

   > [!NOTE]
   > Zie voor informatie over permanente uitvoeringsplannen en beveiliging [Terraform Plan: Beveiligingswaarschuwing](https://www.terraform.io/docs/commands/plan.html#security-warning).

## <a name="apply-the-terraform-execution-plan"></a>Een Terraform-uitvoeringsplan toepassen

Pas het uitvoeringsplan als laatste toe.

Voer de opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) uit en geef het `assignment.tfplan` op dat al is gemaakt.

```bash
terraform apply assignment.tfplan
```

:::image type="content" source="./media/assign-policy-terraform/terraform-apply.png" alt-text="Schermopname van het uitvoeren van de opdracht ‘terraform apply’ en vervolgens het maken van de resource.":::

Met de 'Apply voltooid! Resources: 1 toegevoegd, 0 gewijzigd, 0 vernietigd.’ wordt de beleid toewijzing nu gemaakt. Omdat we het bestand `outputs.tf` hebben gedefinieerd, wordt de _toewijzings-\_id_ ook geretourneerd.

## <a name="identify-non-compliant-resources"></a>Niet-compatibele resources identificeren

Als u de resources wilt weergeven die niet conform zijn onder deze nieuwe toewijzing, gebruikt u de _toewijzings-\_id_ geretourneerd door `terraform apply`. Voer daarbij de volgende opdracht uit om de resource-id's te verkrijgen van de niet-conforme resources. Deze worden uitgevoerd naar een JSON-bestand:

```console
armclient post "/subscriptions/<subscriptionID>/resourceGroups/<rgName>/providers/Microsoft.PolicyInsights/policyStates/latest/queryResults?api-version=2019-10-01&$filter=IsCompliant eq false and PolicyAssignmentId eq '<policyAssignmentID>'&$apply=groupby((ResourceId))" > <json file to direct the output with the resource IDs into>
```

De resultaten zien er ongeveer als volgt uit:

```json
{
    "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest",
    "@odata.count": 3,
    "value": [{
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachineId>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionId>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine2Id>"
        },
        {
            "@odata.id": null,
            "@odata.context": "https://management.azure.com/subscriptions/<subscriptionId>/providers/Microsoft.PolicyInsights/policyStates/$metadata#latest/$entity",
            "ResourceId": "/subscriptions/<subscriptionName>/resourcegroups/<rgname>/providers/microsoft.compute/virtualmachines/<virtualmachine3ID>"
        }

    ]
}
```

De resultaten zijn vergelijkbaar met wat in de weergave van de Azure-portal meestal wordt vermeld onder **Niet-compatibele resources**.

## <a name="clean-up-resources"></a>Resources opschonen

Als u de gemaakte toewijzing wilt verwijderen, gebruikt u Azure CLI of draait u het Terraform-uitvoeringsplan om met `terraform destroy`.

- Azure CLI

  ```azurecli-interactive
  az policy assignment delete --name 'audit-vm-manageddisks' --scope '/subscriptions/<subscriptionID>/<resourceGroupName>'
  ```

- Terraform

  ```bash
  terraform destroy assignment.tfplan
  ```

## <a name="next-steps"></a>Volgende stappen

In deze Quick Start hebt u een beleidsdefinitie toegewezen om niet-compatibele resources in uw Azure-omgeving te identificeren.

Ga voor meer informatie over het toewijzen van beleid om te controleren of nieuwe resources conform zijn verder met de zelfstudie voor:

> [!div class="nextstepaction"]
> [Beleid maken en beheren](./tutorials/create-and-manage.md)
