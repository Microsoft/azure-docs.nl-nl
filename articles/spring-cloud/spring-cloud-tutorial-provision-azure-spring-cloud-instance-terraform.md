---
title: Een Azure Spring Cloud-exemplaar inrichten met terraform
description: Richt een Azure Spring Cloud-exemplaar in met Terraform.
author: MikeDodaro
ms.author: brendm
ms.service: spring-cloud
ms.topic: how-to
ms.date: 06/26/2020
ms.custom: devx-track-java
ms.openlocfilehash: 060ef2d08b849706b47b24748142c608292971b5
ms.sourcegitcommit: 5b93010b69895f146b5afd637a42f17d780c165b
ms.translationtype: HT
ms.contentlocale: nl-NL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96533788"
---
# <a name="provision-an-azure-spring-cloud-instance-with-terraform"></a>Een Azure Spring Cloud-exemplaar inrichten met Terraform

**Dit artikel is van toepassing op:** ✔️ Java ✔️ C#

In dit voorbeeld maakt u een Azure Spring Cloud-exemplaar met behulp van Terraform. Aan de hand van de procedures maakt u de volgende resources:

> [!div class="checklist"]
> * Resourcegroep
> * Azure Spring Cloud-exemplaar
> * Azure Storage voor Log Analytics

> [!NOTE]
> Gebruik voor Terraform-specifieke ondersteuning een van de community-ondersteuningskanalen naar Terraform van HashiCorp:
>
> * Vragen, use-cases en handige patronen: [Terraform section of the HashiCorp community portal](https://discuss.hashicorp.com/c/terraform-core) (Sectie Terraform van de HashiCorp-communityportal)
> * Vragen met betrekking tot de provider: [Terraform Providers section of the HashiCorp community portal](https://discuss.hashicorp.com/c/terraform-providers) (Sectie Terraform-providers van de HashiCorp-communityportal)

## <a name="prerequisites"></a>Vereisten

- **Azure-abonnement**: Als u nog geen abonnement op Azure hebt, maak dan een [gratis account](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) aan voordat u begint.

## <a name="create-configuration-file"></a>Een configuratiebestand maken

1. Meld u aan bij de [Azure-portal](https://go.microsoft.com/fwlink/p/?LinkID=525040).

1. Open [Azure Cloud Shell](../app-service/quickstart-java.md#use-azure-cloud-shell).

1. Start de Cloud Shell-editor:

    ```bash
    code main.tf
    ```

1. Met de configuratie in deze stap worden Azure-resources gemodelleerd, inclusief een Azure-resourcegroep en een Azure Spring Cloud-exemplaar.

    ```hcl
    provider "azurerm" {
        features {}
    }

    resource "azurerm_resource_group" "example" {
      name     = "username"
      location = "eastus"
    }

    resource "azurerm_spring_cloud_service" "example" {
      name                = "usernamesp"
      resource_group_name = azurerm_resource_group.example.name
      location            = azurerm_resource_group.example.location

      config_server_git_setting {
        uri          = "https://github.com/Azure-Samples/piggymetrics-config"
        label        = "master"
        search_paths = ["."]
      }
    }
    ```

1. Sla het bestand ( **&lt;Ctrl+S**) op en sluit de editor af ( **&lt;Ctrl+Q**).

## <a name="apply-the-configuration"></a>De configuratie toepassen

In deze sectie gebruikt u verschillende Terraform-opdrachten om de configuratie uit te voeren.

1. Met de opdracht [terraform init](https://www.terraform.io/docs/commands/init.html) wordt de werkmap geïnitialiseerd. Voer de volgende opdracht in Cloud Shell uit:

    ```bash
    terraform init
    ```

1. De opdracht [terraform plan](https://www.terraform.io/docs/commands/plan.html) wordt gebruikt om de syntaxis van de configuratie te valideren. De parameter `-out` stuurt de resultaten naar een bestand. Het uitvoerbestand kan later worden gebruikt om de configuratie toe te passen. Voer de volgende opdracht in Cloud Shell uit:

    ```bash
    terraform plan --out plan.out
    ```

1. De opdracht [terraform apply](https://www.terraform.io/docs/commands/apply.html) wordt gebruikt om de configuratie toe te passen. Met de opdracht geeft u het uitvoerbestand van de vorige stap op. Met deze opdracht worden de Azure-resources gemaakt. Voer de volgende opdracht in Cloud Shell uit:

    ```bash
    terraform apply plan.out
    ```

1. Als u de resultaten in de Azure-portal wilt controleren, bladert u naar de nieuwe resourcegroep. Het nieuwe **Azure Spring Cloud-exemplaar** wordt weergegeven in de nieuwe resourcegroep.

## <a name="update-configuration-to-config-logs-and-metrics"></a>Configuratie bijwerken voor de configuratie van logboeken en metrische gegevens

In deze sectie wordt beschreven hoe u de configuratie bijwerkt om logboeken en metrische gegevens in te schakelen voor Azure Spring Cloud met een Azure Storage-account.

1. Start de Cloud Shell-editor:

    ```bash
    code main.tf
    ```

1. Voeg aan het einde van het bestand de volgende configuratie toe:

    ```hcl
    resource "azurerm_storage_account" "example" {
      name                     = "usernamest"
      resource_group_name      = azurerm_resource_group.example.name
      location                 = azurerm_resource_group.example.location
      account_tier             = "Standard"
      account_replication_type = "GRS"
    }

    resource "azurerm_monitor_diagnostic_setting" "example" {
      name               = "example"
      target_resource_id = "${azurerm_spring_cloud_service.example.id}"
      storage_account_id = "${azurerm_storage_account.example.id}"

      log {
        category = "SystemLogs"
        enabled  = true

        retention_policy {
          enabled = false
        }
      }

      metric {
        category = "AllMetrics"

        retention_policy {
          enabled = false
        }
      }
    }
    ```

1. Sla het bestand ( **&lt;Ctrl+S**) op en sluit de editor af ( **&lt;Ctrl+Q**).

1. Net als in de vorige sectie voert u de volgende opdracht uit om de wijzigingen aan te brengen:

    ```bash
    terraform plan --out plan.out
    ```

1. Voer de opdracht `terraform apply` uit om de configuratie toe te passen.

    ```bash
    terraform apply plan.out
    ```

## <a name="clean-up-resources"></a>Resources opschonen

Verwijder de resources die u in dit artikel hebt gemaakt als u ze niet meer nodig hebt.

Voer de opdracht [terraform destroy](https://www.terraform.io/docs/commands/destroy.html) uit om de Azure-resources te verwijderen die in deze oefening zijn gemaakt:

```bash
terraform destroy -auto-approve
```

## <a name="next-steps"></a>Volgende stappen

> [!div class="nextstepaction"]
> [Install and configure Terraform to provision Azure resources](/azure/developer/terraform/getting-started-cloud-shell) (Terraform installeren en configureren om Azure-resources in te richten).