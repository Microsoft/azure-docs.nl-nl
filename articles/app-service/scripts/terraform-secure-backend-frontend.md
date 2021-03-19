---
title: 'Terraform: Een front-end-web-app en back-end-web-app implementeren die veilig zijn verbonden met VNet-integratie en privé-eindpunt'
description: Ontdek hoe u de Terraform-provider voor App Service kunt gebruiken om twee web-apps te implementeren die veilig zijn verbonden met het privé-eindpunt en de VNet-integratie
author: ericgre
ms.assetid: 3e5d1bbd-5581-40cc-8f65-bc74f1802156
ms.topic: sample
ms.date: 08/10/2020
ms.author: ericg
ms.service: app-service
ms.workload: web
ms.openlocfilehash: c1de8ebbd9ad381628cfeb19413baa295b42b3db
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91739830"
---
# <a name="create-two-web-apps-connected-securely-with-private-endpoint-and-vnet-integration"></a>Twee web-apps maken en veilig verbinden met een privé-eindpunt en een VNet-integratie

In dit artikel wordt een voorbeeld gegeven van het gebruik van een [privé-eindpunt](../networking/private-endpoint.md) en een regionale [VNet-integratie](../web-sites-integrate-with-vnet.md) om twee web-apps (front-end en back-end) veilig te koppelen aan de hand van de volgende stappen:
- Implementeer een VNet.
- Maak het eerste subnet voor de integratie.
- Maak het tweede subnet voor het privé-eindpunt. U moet een specifieke parameter instellen om netwerkbeleid uit te schakelen.
- Implementeer een App Service-abonnement van het type PremiumV2 of PremiumV3, vereist voor de functie Privé-eindpunt.
- Maak de front-end-web-app met specifieke app-instellingen om de privé-DNS-zone te gebruiken. [Meer informatie](../web-sites-integrate-with-vnet.md#azure-dns-private-zones)
- Verbind de front-end-web-app met het integratiesubnet.
- Maak de back-end-web-app.
- Maak de DNS-privézone en geef deze de naam van de Private Link-zone voor web-apps: privatelink.azurewebsites.net.
- Koppel deze zone aan het VNet.
- Maak het privé-eindpunt voor de back-end-web-app in het subnet van het eindpunt en registreer DNS-namen (website en SCM) in de eerder gemaakte DNS-privézone.

## <a name="how-to-use-terraform-in-azure"></a>Terraform gebruiken in Azure

Ga naar de [Azure-documentatie](/azure/developer/terraform/) voor meer informatie over het gebruik van Terraform met Azure.

## <a name="the-complete-terraform-file"></a>Het voltooide Terraform-bestand

Als u dit bestand wilt gebruiken, moet u de naameigenschap voor front-web-app- en back-web-app-resources wijzigen (de naam van de web-app moet wereldwijd een unieke DNS-naam zijn). 

```hcl
provider "azurerm" {
  version = "~>2.0"
  features {}
}

resource "azurerm_resource_group" "rg" {
  name     = "appservice-rg"
  location = "francecentral"
}

resource "azurerm_virtual_network" "vnet" {
  name                = "vnet"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  address_space       = ["10.0.0.0/16"]
}

resource "azurerm_subnet" "integrationsubnet" {
  name                 = "integrationsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.1.0/24"]
  delegation {
    name = "delegation"
    service_delegation {
      name = "Microsoft.Web/serverFarms"
    }
  }
}

resource "azurerm_subnet" "endpointsubnet" {
  name                 = "endpointsubnet"
  resource_group_name  = azurerm_resource_group.rg.name
  virtual_network_name = azurerm_virtual_network.vnet.name
  address_prefixes     = ["10.0.2.0/24"]
  enforce_private_link_endpoint_network_policies = true
}

resource "azurerm_app_service_plan" "appserviceplan" {
  name                = "appserviceplan"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name

  sku {
    tier = "Premiumv2"
    size = "P1v2"
  }
}

resource "azurerm_app_service" "frontwebapp" {
  name                = "frontwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id

  app_settings = {
    "WEBSITE_DNS_SERVER": "168.63.129.16",
    "WEBSITE_VNET_ROUTE_ALL": "1"
  }
}

resource "azurerm_app_service_virtual_network_swift_connection" "vnetintegrationconnection" {
  app_service_id  = azurerm_app_service.frontwebapp.id
  subnet_id       = azurerm_subnet.integrationsubnet.id
}

resource "azurerm_app_service" "backwebapp" {
  name                = "backwebapp20200810"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  app_service_plan_id = azurerm_app_service_plan.appserviceplan.id
}

resource "azurerm_private_dns_zone" "dnsprivatezone" {
  name                = "privatelink.azurewebsites.net"
  resource_group_name = azurerm_resource_group.rg.name
}

resource "azurerm_private_dns_zone_virtual_network_link" "dnszonelink" {
  name = "dnszonelink"
  resource_group_name = azurerm_resource_group.rg.name
  private_dns_zone_name = azurerm_private_dns_zone.dnsprivatezone.name
  virtual_network_id = azurerm_virtual_network.vnet.id
}

resource "azurerm_private_endpoint" "privateendpoint" {
  name                = "backwebappprivateendpoint"
  location            = azurerm_resource_group.rg.location
  resource_group_name = azurerm_resource_group.rg.name
  subnet_id           = azurerm_subnet.endpointsubnet.id

  private_dns_zone_group {
    name = "privatednszonegroup"
    private_dns_zone_ids = [azurerm_private_dns_zone.dnsprivatezone.id]
  }

  private_service_connection {
    name = "privateendpointconnection"
    private_connection_resource_id = azurerm_app_service.backwebapp.id
    subresource_names = ["sites"]
    is_manual_connection = false
  }
}
```




## <a name="next-steps"></a>Volgende stappen


> [Meer informatie over het gebruik van Terraform in Azure](/azure/developer/terraform/)