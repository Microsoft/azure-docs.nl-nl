---
title: Implementeren met terraform vanuit Azure Cloud Shell | Microsoft Docs
description: Implementeren met terraform vanuit Azure Cloud Shell
services: Azure
documentationcenter: ''
author: tomarchermsft
manager: routlaw
tags: azure-cloud-shell
ms.service: azure
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/15/2017
ms.author: tarcher
ms.custom: devx-track-terraform
ms.openlocfilehash: e13e6d9ac2f4600e41f221efd23997f712dffc54
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92745442"
---
# <a name="deploy-with-terraform-from-bash-in-azure-cloud-shell"></a>Implementeren met terraform vanuit bash in Azure Cloud Shell
Dit artikel helpt u bij het maken van een resource groep met de [terraform AzureRM-provider](https://www.terraform.io/docs/providers/azurerm/index.html).

[Hashicorp terraform](https://www.terraform.io/) is een open source-hulp programma dat api's in declaratieve configuratie bestanden die kunnen worden gedeeld door team leden kan worden bewerkt, gecontroleerd en geversied. De provider van micro soft AzureRM wordt gebruikt om te communiceren met bronnen die door Azure Resource Manager worden ondersteund via de AzureRM-Api's.

## <a name="automatic-authentication"></a>Automatische verificatie
Terraform is standaard geïnstalleerd in bash in Cloud Shell. Daarnaast verifieert Cloud Shell automatisch uw standaard Azure CLI-abonnement om resources te implementeren via de terraform Azure-modules.

Terraform maakt gebruik van het standaard Azure CLI-abonnement dat is ingesteld. Voer de volgende handelingen uit om de standaard abonnementen bij te werken:

```azurecli-interactive
az account set --subscription mySubscriptionName
```

## <a name="walkthrough"></a>Walkthrough
### <a name="launch-bash-in-cloud-shell"></a>Bash starten in Cloud Shell
1. Start Cloud Shell vanaf uw voorkeurs locatie
2. Controleren of uw voorkeurs abonnement is ingesteld

```azurecli-interactive
az account show
```

### <a name="create-a-terraform-template"></a>Een terraform-sjabloon maken
Maak een nieuwe terraform-sjabloon met de naam main.tf met de gewenste tekst editor.

```
vim main.tf
```

Kopieer/Plak de volgende code in Cloud Shell.

```
resource "azurerm_resource_group" "myterraformgroup" {
    name = "myRgName"
    location = "West US"
}
```

Sla het bestand op en sluit de tekst editor.

### <a name="terraform-init"></a>Terraform init
Begin door uit te voeren `terraform init` .

```
justin@Azure:~$ terraform init

Initializing provider plugins...

The following providers do not have any version constraints in configuration,
so the latest version was installed.

To prevent automatic upgrades to new major versions that may contain breaking
changes, it is recommended to add version = "..." constraints to the
corresponding provider blocks in configuration, with the constraint strings
suggested below.

* provider.azurerm: version = "~> 0.2"

Terraform has been successfully initialized!

You may now begin working with Terraform. Try running "terraform plan" to see
any changes that are required for your infrastructure. All Terraform commands
should now work.

If you ever set or change modules or backend configuration for Terraform,
rerun this command to reinitialize your working directory. If you forget, other
commands will detect it and remind you to do so if necessary.
```

De [opdracht terraform init](https://www.terraform.io/docs/commands/init.html) wordt gebruikt voor het initialiseren van een werkmap met terraform-configuratie bestanden. De `terraform init` opdracht is de eerste opdracht die moet worden uitgevoerd na het schrijven van een nieuwe terraform-configuratie of het klonen van een bestaande-versie beheer. Het is veilig om deze opdracht meerdere keren uit te voeren.

### <a name="terraform-plan"></a>Terraform plan
Bekijk een voor beeld van de resources die moeten worden gemaakt door de terraform-sjabloon met `terraform plan` .

```
justin@Azure:~$ terraform plan
Refreshing Terraform state in-memory prior to plan...
The refreshed state will be used to calculate this plan, but will not be
persisted to local or remote state storage.


------------------------------------------------------------------------

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  + create

Terraform will perform the following actions:

  + azurerm_resource_group.demo
      id:       <computed>
      location: "westus"
      name:     "myRGName"
      tags.%:   <computed>


Plan: 1 to add, 0 to change, 0 to destroy.

------------------------------------------------------------------------

Note: You didn't specify an "-out" parameter to save this plan, so Terraform
can't guarantee that exactly these actions will be performed if
"terraform apply" is subsequently run.
```

De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om een uitvoeringsplan te maken. Terraform voert een vernieuwing uit, tenzij expliciet is uitgeschakeld, en vervolgens bepaalt u welke acties nodig zijn om de gewenste status te krijgen die is opgegeven in de configuratie bestanden. Het plan kan worden opgeslagen met behulp van en wordt vervolgens terraform toegepast om ervoor te zorgen dat alleen de vooraf geplande acties worden uitgevoerd.

### <a name="terraform-apply"></a>Terraform apply
Richt de Azure-resources in met `terraform apply` .

```
justin@Azure:~$ terraform apply
azurerm_resource_group.demo: Creating...
  location: "" => "westus"
  name:     "" => "myRGName"
  tags.%:   "" => "<computed>"
azurerm_resource_group.demo: Creation complete after 0s (ID: /subscriptions/mySubIDmysub/resourceGroups/myRGName)

Apply complete! Resources: 1 added, 0 changed, 0 destroyed.
```

De [opdracht terraform apply](https://www.terraform.io/docs/commands/apply.html) wordt gebruikt om de wijzigingen toe te passen die nodig zijn om de gewenste status van de configuratie te bereiken.

### <a name="verify-deployment-with-azure-cli"></a>Implementatie controleren met Azure CLI
Uitvoeren `az group show -n myRgName` om te controleren of de resource is ingericht.

```azurecli-interactive
az group show -n myRgName
```

### <a name="clean-up-with-terraform-destroy"></a>Opschonen met terraform vernietigen
Opschonen van de resource groep die is gemaakt met de [opdracht terraform Destroy](https://www.terraform.io/docs/commands/destroy.html) om een terraform-gemaakte infra structuur op te schonen.

```
justin@Azure:~$ terraform destroy
azurerm_resource_group.demo: Refreshing state... (ID: /subscriptions/mySubID/resourceGroups/myRGName)

An execution plan has been generated and is shown below.
Resource actions are indicated with the following symbols:
  - destroy

Terraform will perform the following actions:

  - azurerm_resource_group.demo


Plan: 0 to add, 0 to change, 1 to destroy.

Do you really want to destroy?
  Terraform will destroy all your managed infrastructure, as shown above.
  There is no undo. Only 'yes' will be accepted to confirm.

  Enter a value: yes

azurerm_resource_group.demo: Destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 10s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 20s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 30s elapsed)
azurerm_resource_group.demo: Still destroying... (ID: /subscriptions/mySubID/resourceGroups/myRGName, 40s elapsed)
azurerm_resource_group.demo: Destruction complete after 45s

Destroy complete! Resources: 1 destroyed.
```

U hebt een Azure-resource gemaakt via terraform. Ga naar volgende stappen om over Cloud Shell te blijven leren.

## <a name="next-steps"></a>Volgende stappen
[Meer informatie over de terraform Azure-provider](https://www.terraform.io/docs/providers/azurerm/#)<br>
[Bash in Cloud Shell Quick Start](quickstart.md)
