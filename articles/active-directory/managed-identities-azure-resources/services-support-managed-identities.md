---
title: Azure-Services die beheerde identiteiten ondersteunen-Azure AD
description: Lijst met services die beheerde identiteiten voor Azure-resources en Azure AD-verificatie ondersteunen
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 10/07/2020
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: 7ebe56b95b0c4b938c31c0ece04d27fd1b234b05
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98729909"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services die beheerde identiteiten voor Azure-resources ondersteunen

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. Met een beheerde identiteit kunt u zich verifiëren bij elke service die ondersteuning biedt voor Azure AD-verificatie zonder referenties in uw code. We zijn bezig met het integreren van beheerde identiteiten voor Azure-resources en Azure AD-verificatie in Azure. Controleer regel matig of er updates zijn.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-services die beheerde identiteiten voor Azure-resources ondersteunen

De volgende Azure-Services ondersteunen beheerde identiteiten voor Azure-resources:


### <a name="azure-api-management"></a>Azure API Management

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Preview | Preview | Niet beschikbaar | Preview |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure API Management te configureren (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | Niet beschikbaar | ![Beschikken][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikken][check]  | Niet beschikbaar  | ![Beschikken][check] |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure-app configuratie te configureren (in regio's waar beschikbaar):

- [Azure-CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikken][check]  | ![Beschikken][check]  | ![Beschikken][check] |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure App Service te configureren (in regio's waar beschikbaar):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sjabloon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes met Azure Arc

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Azure Arc enabled Kubernetes ondersteunt momenteel de [toegewezen identiteit](../../azure-arc/kubernetes/connect-cluster.md#azure-arc-agents-for-kubernetes)van het systeem. Het beheerde service-identiteits certificaat wordt gebruikt door alle Azure Arc enabled Kubernetes-agents voor communicatie met Azure.

### <a name="azure-automanage"></a>Azure Automanage

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg het volgende document als u een beheerde identiteit opnieuw wilt configureren als u uw abonnement hebt verplaatst naar een nieuwe Tenant:
* [Een gebroken account voor automanage herstellen](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het gebruik van een beheerde identiteit met [Azure-blauw drukken](../../governance/blueprints/overview.md):

- [Azure Portal-blauw druk toewijzen](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API-blauw druk toewijzen](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


### <a name="azure-container-instances"></a>Azure Container Instances

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Linux: preview<br>Windows: niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Linux: preview<br>Windows: niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Container Instances te configureren (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sjabloon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van een beheerde identiteit voor Azure Container Registry taken (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van een beheerde identiteit voor Azure Data Factory v2 (in regio's waar beschikbaar):

- [Azure-portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [SDK](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)



### <a name="azure-event-grid"></a>Azure Event Grid

Type beheerde identiteit |Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Preview | Niet beschikbaar | Preview |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar  | Niet beschikbaar  | Niet beschikbaar |









### <a name="azure-functions"></a>Azure Functions

Type beheerde identiteit |Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikken][check]  | ![Beschikken][check]  | ![Beschikken][check]  |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Functions te configureren (in regio's waar beschikbaar):

- [Azure Portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sjabloon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van een beheerde identiteit voor Azure Data Factory v2 (in regio's waar beschikbaar):

- [Azure Portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Door het systeem toegewezen | Beschikbaar in de regio waarin de Azure import-export service beschikbaar is | Preview | Beschikbaar | Beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


Zie [beheerde identiteiten gebruiken in azure Kubernetes service](../../aks/use-managed-identity.md)voor meer informatie.


### <a name="azure-logic-apps"></a>Azure Logic Apps

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |


Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Logic Apps te configureren (in regio's waar beschikbaar):

- [Azure Portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sjabloon](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Zie [Managed Identities met Azure machine learning](../../machine-learning/how-to-use-managed-identities.md)voor meer informatie.

### <a name="azure-policy"></a>Azure Policy

|Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] | ![Beschikken][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Policy te configureren (in regio's waar beschikbaar):

- [Azure-portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure-CLI](/cli/azure/policy/assignment#az-policy-assignment-create)
- [Azure Resource Manager-sjablonen](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/resources/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

De [beheerde identiteit voor service Fabric toepassingen](../../service-fabric/concepts-managed-identity.md) is in alle regio's beschikbaar.

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | Niet beschikbaar | Niet beschikbaar | niet beschikbaar |
| Door de gebruiker toegewezen | ![Beschikken][check] | Niet beschikbaar | Niet beschikbaar |Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteit voor Azure Service Fabric-toepassingen in alle regio's:

- [Azure Resource Manager-sjabloon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | Niet beschikbaar | Niet beschikbaar | ![Beschikken][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


Zie [How to Enable door het systeem toegewezen beheerde identiteit voor Azure lente-Cloud toepassing](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)voor meer informatie.

### <a name="azure-stack-edge"></a>Azure Stack Edge

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Door het systeem toegewezen | Beschikbaar in de regio waar Azure Stack Edge-service beschikbaar is | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-virtual-machine-scale-sets"></a>Microsoft Azure Virtual Machine Scale Sets

|Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | Preview | ![Beschikken][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikken][check] | Preview | ![Beschikken][check] |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Virtual Machine Scale Sets te configureren (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikken][check] | ![Beschikken][check] | Preview | ![Beschikken][check] |
| Door de gebruiker toegewezen | ![Beschikken][check] | ![Beschikken][check] | Preview | ![Beschikken][check] |

Raadpleeg de volgende lijst om de beheerde identiteit voor Azure Virtual Machines te configureren (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure-SDK's](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Opbouw functie voor installatie kopieën van Azure VM

| Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | [Beschikbaar in ondersteunde regio's](../../virtual-machines/image-builder-overview.md#regions) | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Voor meer informatie over het configureren van een beheerde identiteit voor de opbouw functie voor installatie kopieën van Azure VM (in regio's waar beschikbaar), raadpleegt u het [overzicht van Image Builder](../../virtual-machines/image-builder-overview.md#permissions).
### <a name="azure-signalr-service"></a>Azure SignalR-service

Type beheerde identiteit | Alles algemeen beschikbaar<br>Wereld wijde Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Preview | Niet beschikbaar | Preview |
| Door de gebruiker toegewezen | Preview | Preview | Niet beschikbaar | Preview |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteit voor de Azure signalerings service (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](../../azure-signalr/howto-use-managed-identity.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-Services die ondersteuning bieden voor Azure AD-verificatie

De volgende services ondersteunen Azure AD-verificatie en zijn getest met client services die beheerde identiteiten voor Azure-resources gebruiken.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Raadpleeg de volgende lijst om de toegang tot Azure Resource Manager te configureren:

- [Toegang toewijzen via Azure Portal](howto-assign-access-portal.md)
- [Toegang toewijzen via Power shell](howto-assign-access-powershell.md)
- [Toegang toewijzen via Azure CLI](howto-assign-access-CLI.md)
- [Toegang toewijzen via Azure Resource Manager sjabloon](../../role-based-access-control/role-assignments-template.md)

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Beschikken][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Beschikken][check] |
| Azure Duitsland | `https://management.microsoftazure.de/` | ![Beschikken][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Beschikken][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Beschikken][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Beschikken][check] |
| Azure Duitsland |  `https://vault.microsoftazure.de` | ![Beschikken][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Beschikken][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Beschikken][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-sql"></a>Azure SQL

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Beschikken][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Beschikken][check] |
| Azure Duitsland | `https://database.cloudapi.de/` | ![Beschikken][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Beschikken][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Beschikken][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Beschikken][check] |
| Azure Government |  | ![Beschikken][check] |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs en wacht rijen

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Beschikken][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Beschikken][check] |
| Azure Duitsland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Beschikken][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Beschikken][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Beschikken][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Beschikken][check] |
| Azure Duitsland | `https://*.asazure.cloudapi.de` | ![Beschikken][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Beschikken][check] |

> [!Note]
> Micro soft Power BI [biedt ook ondersteuning voor beheerde identiteiten](../../stream-analytics/powerbi-output-managed-identity.md).


[check]: media/services-support-managed-identities/check.png "Beschikken"