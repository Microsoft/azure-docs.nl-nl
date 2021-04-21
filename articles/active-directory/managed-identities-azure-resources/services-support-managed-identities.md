---
title: Azure-services die beheerde identiteiten ondersteunen - Azure AD
description: Lijst met services die beheerde identiteiten voor Azure-resources en Azure AD-verificatie ondersteunen
services: active-directory
author: barclayn
ms.author: barclayn
ms.date: 01/28/2021
ms.topic: conceptual
ms.service: active-directory
ms.subservice: msi
manager: daveba
ms.collection: M365-identity-device-management
ms.custom: references_regions
ms.openlocfilehash: c4cd9140d03bba1f9d95ed64c3628da4fe32ecd9
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107771475"
---
# <a name="services-that-support-managed-identities-for-azure-resources"></a>Services die beheerde identiteiten voor Azure-resources ondersteunen

Beheerde identiteiten voor Azure-resources bieden Azure-services met een automatisch beheerde identiteit in Azure Active Directory. Met behulp van een beheerde identiteit kunt u zich verifiëren bij elke service die Azure AD-verificatie ondersteunt zonder referenties in uw code. We zijn bezig met het integreren van beheerde identiteiten voor Azure-resources en Azure AD-verificatie in Azure. Controleer regelmatig of er updates zijn.

> [!NOTE]
> Beheerde identiteiten voor Azure-resources is de nieuwe naam voor de service die eerder de naam Managed Service Identity (MSI) had.

## <a name="azure-services-that-support-managed-identities-for-azure-resources"></a>Azure-services die beheerde identiteiten voor Azure-resources ondersteunen

De volgende Azure-services ondersteunen beheerde identiteiten voor Azure-resources:


### <a name="azure-api-management"></a>Azure API Management

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Preview | Preview | Niet beschikbaar | Preview |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteiten voor Azure API Management (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](../../api-management/api-management-howto-use-managed-service-identity.md)

### <a name="azure-app-configuration"></a>Azure App Configuration

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check]  | Niet beschikbaar  | ![Beschikbaar][check] |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure App Configuration (in regio's waar beschikbaar):

- [Azure-CLI](../../azure-app-configuration/overview-managed-identity.md)

### <a name="azure-app-service"></a>Azure App Service

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check]  | ![Beschikbaar][check]  | ![Beschikbaar][check] |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure App Service (in regio's waar beschikbaar):

- [Azure-portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sjabloon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-arc-enabled-kubernetes"></a>Kubernetes met Azure Arc

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Azure Arc kubernetes ondersteunt momenteel door [het systeem toegewezen identiteit](../../azure-arc/kubernetes/quickstart-connect-cluster.md). Het beheerde service-identiteitscertificaat wordt gebruikt door Azure Arc kubernetes-agents voor communicatie met Azure.

### <a name="azure-arc-enabled-servers"></a>Servers met Azure Arc

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Alle Azure Arc servers hebben een door het systeem toegewezen identiteit. U kunt de door het systeem toegewezen identiteit op een Azure Arc server niet uitschakelen of wijzigen. Raadpleeg de volgende bronnen voor meer informatie over het gebruik van beheerde identiteiten op Azure Arc servers:

- [Verifiëren bij Azure-resources met Arc-servers](../../azure-arc/servers/managed-identity-authentication.md)
- [Een beheerde identiteit gebruiken met Arc-servers](../../azure-arc/servers/security-overview.md#using-a-managed-identity-with-arc-enabled-servers)

### <a name="azure-automanage"></a>Azure Automanage

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg het volgende document om een beheerde identiteit opnieuw te configureren als u uw abonnement naar een nieuwe tenant hebt verplaatst:
* [Een defect automanage-account herstellen](../../automanage/repair-automanage-account.md)

### <a name="azure-blueprints"></a>Azure Blueprints

|Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te gebruiken met [Azure Blueprints](../../governance/blueprints/overview.md):

- [Azure Portal - blauwdruktoewijzing](../../governance/blueprints/create-blueprint-portal.md#assign-a-blueprint)
- [REST API - blauwdruktoewijzing](../../governance/blueprints/create-blueprint-rest-api.md#assign-a-blueprint)


### <a name="azure-cognitive-search"></a>Azure Cognitive Search

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-cognitive-services"></a>Azure Cognitive Services

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


### <a name="azure-communication-services"></a>Azure Communication Services

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


### <a name="azure-container-instances"></a>Azure Container Instances

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Linux: Preview<br>Windows: Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Linux: Preview<br>Windows: Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure Container Instances (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-instances/container-instances-managed-identity.md)
- [Azure Resource Manager-sjabloon](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-resource-manager-template)
- [YAML](~/articles/container-instances/container-instances-managed-identity.md#enable-managed-identity-using-yaml-file)


### <a name="azure-container-registry-tasks"></a>Azure Container Registry Tasks

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te configureren voor Azure Container Registry-taken (in regio's waar beschikbaar):

- [Azure-CLI](~/articles/container-registry/container-registry-tasks-authentication-managed-identity.md)

### <a name="azure-data-explorer"></a>Azure Data Explorer

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-data-factory-v2"></a>Azure Data Factory V2

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te configureren voor Azure Data Factory V2 (in regio's waar beschikbaar):

- [Azure-portal](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity)
- [PowerShell](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-powershell)
- [REST](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-rest-api)
- [Sdk](~/articles/data-factory/data-factory-service-identity.md#generate-managed-identity-using-sdk)

### <a name="azure-digital-twins"></a>Azure Digital Twins

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te configureren voor Azure Digital Twins (in regio's waar beschikbaar):

- [Azure-portal](../../digital-twins/how-to-enable-managed-identities-portal.md)

### <a name="azure-event-grid"></a>Azure Event Grid

Type beheerde identiteit |Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Preview | Niet beschikbaar | Preview |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar  | Niet beschikbaar  | Niet beschikbaar |

### <a name="azure-firewall-policy"></a>Azure Firewall beleid

Type beheerde identiteit |Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar  | Niet beschikbaar  | Niet beschikbaar |

### <a name="azure-functions"></a>Azure Functions

Type beheerde identiteit |Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check]  | ![Beschikbaar][check]  | ![Beschikbaar][check]  |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure Functions (in regio's waar beschikbaar):

- [Azure-portal](../../app-service/overview-managed-identity.md#using-the-azure-portal)
- [Azure-CLI](../../app-service/overview-managed-identity.md#using-the-azure-cli)
- [Azure PowerShell](../../app-service/overview-managed-identity.md#using-azure-powershell)
- [Azure Resource Manager-sjabloon](../../app-service/overview-managed-identity.md#using-an-azure-resource-manager-template)

### <a name="azure-iot-hub"></a>Azure IoT Hub

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure IoT Hub (in regio's waar beschikbaar):

- [Azure-portal](../../iot-hub/virtual-network-support.md#turn-on-managed-identity-for-iot-hub)

### <a name="azure-importexport"></a>Azure Import/Export

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Door het systeem toegewezen | Beschikbaar in de regio waar de Azure Import Export-service beschikbaar is | Preview | Beschikbaar | Beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-kubernetes-service-aks"></a>Azure Kubernetes Service (AKS)

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


Zie Beheerde identiteiten gebruiken in Azure Kubernetes Service voor [meer Azure Kubernetes Service.](../../aks/use-managed-identity.md)

### <a name="azure-log-analytics-cluster"></a>Azure Log Analytics-cluster

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |

Zie how [identity works in Azure Monitor (Hoe identiteit werkt in](../../azure-monitor/logs/customer-managed-keys.md) het Azure Monitor) voor meer Azure Monitor

### <a name="azure-logic-apps"></a>Azure Logic Apps

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | Niet beschikbaar | ![Beschikbaar][check] |


Raadpleeg de volgende lijst om een beheerde identiteit te configureren voor Azure Logic Apps (in regio's waar beschikbaar):

- [Azure-portal](../../logic-apps/create-managed-service-identity.md#enable-system-assigned-identity-in-azure-portal)
- [Azure Resource Manager-sjabloon](../../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)

### <a name="azure-machine-learning"></a>Azure Machine Learning

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Preview | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Zie Beheerde identiteiten [gebruiken met een Azure Machine Learning voor Azure Machine Learning.](../../machine-learning/how-to-use-managed-identities.md)

### <a name="azure-policy"></a>Azure Policy

|Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg de volgende lijst om een beheerde identiteit te configureren voor Azure Policy (in regio's waar beschikbaar):

- [Azure-portal](../../governance/policy/tutorials/create-and-manage.md#assign-a-policy)
- [PowerShell](../../governance/policy/how-to/remediate-resources.md#create-managed-identity-with-powershell)
- [Azure-CLI](/cli/azure/policy/assignment#az_policy_assignment_create)
- [Azure Resource Manager-sjablonen](/azure/templates/microsoft.authorization/policyassignments)
- [REST](/rest/api/policy/policyassignments/create)


### <a name="azure-service-fabric"></a>Azure Service Fabric

[Beheerde identiteit voor Service Fabric toepassingen](../../service-fabric/concepts-managed-identity.md) is beschikbaar in alle regio's.

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | niet beschikbaar |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar |Niet beschikbaar |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteiten voor Azure Service Fabric in alle regio's:

- [Azure Resource Manager-sjabloon](https://github.com/Azure-Samples/service-fabric-managed-identity/tree/anmenard-docs)

### <a name="azure-spring-cloud"></a>Azure Spring Cloud

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | Niet beschikbaar | Niet beschikbaar | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |


Zie How [to enable system-assigned managed identity](~/articles/spring-cloud/spring-cloud-howto-enable-system-assigned-managed-identity.md)for Azure Spring Cloud application voor meer informatie.

### <a name="azure-stack-edge"></a>Azure Stack Edge

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | --- | --- | --- | --- |
| Door het systeem toegewezen | Beschikbaar in de regio waar Azure Stack Edge service beschikbaar is | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

### <a name="azure-virtual-machine-scale-sets"></a>Microsoft Azure Virtual Machine Scale Sets

|Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteiten voor Azure Virtual Machine Scale Sets (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)



### <a name="azure-virtual-machines"></a>Azure Virtual Machines

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |
| Door de gebruiker toegewezen | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] | ![Beschikbaar][check] |

Raadpleeg de volgende lijst voor het configureren van beheerde identiteiten voor Azure Virtual Machines (in regio's waar beschikbaar):

- [Azure-portal](qs-configure-portal-windows-vm.md)
- [PowerShell](qs-configure-powershell-windows-vm.md)
- [Azure-CLI](qs-configure-cli-windows-vm.md)
- [Azure Resource Manager-sjablonen](qs-configure-template-windows-vm.md)
- [REST](qs-configure-rest-vm.md)
- [Azure-SDK's](qs-configure-sdk-windows-vm.md)


### <a name="azure-vm-image-builder"></a>Azure VM Image Builder

| Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | [Beschikbaar in ondersteunde regio's](../../virtual-machines/image-builder-overview.md#regions) | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Zie overzicht van Image Builder voor meer informatie over het configureren [van beheerde identiteiten voor Azure VM Image Builder (in regio's Image Builder beschikbaar).](../../virtual-machines/image-builder-overview.md#permissions)
### <a name="azure-signalr-service"></a>Azure SignalR Service

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Preview | Preview | Niet beschikbaar | Preview |
| Door de gebruiker toegewezen | Preview | Preview | Niet beschikbaar | Preview |

Raadpleeg de volgende lijst om beheerde identiteiten te configureren voor Azure SignalR Service (in regio's waar beschikbaar):

- [Azure Resource Manager-sjabloon](../../azure-signalr/howto-use-managed-identity.md)

### <a name="azure-resource-mover"></a>Azure Resource Mover

Type beheerde identiteit | Alle algemeen beschikbaar<br>Globale Azure-regio's | Azure Government | Azure Duitsland | Azure China 21Vianet |
| --- | :-: | :-: | :-: | :-: |
| Door het systeem toegewezen | Beschikbaar in de regio's Azure Resource Mover service beschikbaar is | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |
| Door de gebruiker toegewezen | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar | Niet beschikbaar |

Raadpleeg het volgende document om de volgende Azure Resource Mover:

- [Azure Resource Mover](../../resource-mover/overview.md)

## <a name="azure-services-that-support-azure-ad-authentication"></a>Azure-services die Azure AD-verificatie ondersteunen

De volgende services ondersteunen Azure AD-verificatie en zijn getest met clientservices die gebruikmaken van beheerde identiteiten voor Azure-resources.

### <a name="azure-resource-manager"></a>Azure Resource Manager

Raadpleeg de volgende lijst om de toegang tot de Azure Resource Manager:

- [Toegang toewijzen via Azure Portal](howto-assign-access-portal.md)
- [Toegang toewijzen via PowerShell](howto-assign-access-powershell.md)
- [Toegang toewijzen via Azure CLI](howto-assign-access-CLI.md)
- [Toegang toewijzen via Azure Resource Manager sjabloon](../../role-based-access-control/role-assignments-template.md)

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://management.azure.com/`| ![Beschikbaar][check] |
| Azure Government | `https://management.usgovcloudapi.net/` | ![Beschikbaar][check] |
| Azure Duitsland | `https://management.microsoftazure.de/` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://management.chinacloudapi.cn` | ![Beschikbaar][check] |

### <a name="azure-key-vault"></a>Azure Key Vault

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://vault.azure.net`| ![Beschikbaar][check] |
| Azure Government | `https://vault.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland |  `https://vault.microsoftazure.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://vault.azure.cn` | ![Beschikbaar][check] |

### <a name="azure-data-lake"></a>Azure Data Lake

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://datalake.azure.net/` | ![Beschikbaar][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-sql"></a>Azure SQL

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://database.windows.net/` | ![Beschikbaar][check] |
| Azure Government | `https://database.usgovcloudapi.net/` | ![Beschikbaar][check] |
| Azure Duitsland | `https://database.cloudapi.de/` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://database.chinacloudapi.cn/` | ![Beschikbaar][check] |

### <a name="azure-data-explorer"></a>Azure Data Explorer

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://<account>.<region>.kusto.windows.net` | ![Beschikbaar][check] |
| Azure Government | `https://<account>.<region>.kusto.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland | `https://<account>.<region>.kusto.cloudapi.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://<account>.<region>.kusto.chinacloudapi.cn` | ![Beschikbaar][check] |

### <a name="azure-event-hubs"></a>Azure Event Hubs

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://eventhubs.azure.net` | ![Beschikbaar][check] |
| Azure Government |  | Niet beschikbaar |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |

### <a name="azure-service-bus"></a>Azure Service Bus

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://servicebus.azure.net`  | ![Beschikbaar][check] |
| Azure Government |  | ![Beschikbaar][check] |
| Azure Duitsland |   | Niet beschikbaar |
| Azure China 21Vianet |  | Niet beschikbaar |









### <a name="azure-storage-blobs-and-queues"></a>Azure Storage blobs en wachtrijen maken

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://storage.azure.com/` <br /><br />`https://<account>.blob.core.windows.net` <br /><br />`https://<account>.queue.core.windows.net` | ![Beschikbaar][check] |
| Azure Government | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.usgovcloudapi.net` <br /><br />`https://<account>.queue.core.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.cloudapi.de` <br /><br />`https://<account>.queue.core.cloudapi.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://storage.azure.com/`<br /><br />`https://<account>.blob.core.chinacloudapi.cn` <br /><br />`https://<account>.queue.core.chinacloudapi.cn` | ![Beschikbaar][check] |

### <a name="azure-analysis-services"></a>Azure Analysis Services

| Cloud | Resource-id | Status |
|--------|------------|:-:|
| Azure Global | `https://*.asazure.windows.net` | ![Beschikbaar][check] |
| Azure Government | `https://*.asazure.usgovcloudapi.net` | ![Beschikbaar][check] |
| Azure Duitsland | `https://*.asazure.cloudapi.de` | ![Beschikbaar][check] |
| Azure China 21Vianet | `https://*.asazure.chinacloudapi.cn` | ![Beschikbaar][check] |

> [!Note]
> Microsoft Power BI ook [beheerde identiteiten .](../../stream-analytics/powerbi-output-managed-identity.md)


[check]: media/services-support-managed-identities/check.png "Beschikbaar"
