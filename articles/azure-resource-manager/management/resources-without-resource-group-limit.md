---
title: Resources zonder limiet van 800 tellingen
description: Een lijst met de Azure-resourcetypen die meer dan 800 exemplaren in een resourcegroep kunnen hebben.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: d132773ff35d53dc373c759326efc8179f4993d6
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107366535"
---
# <a name="resources-not-limited-to-800-instances-per-resource-group"></a>Resources die niet beperkt zijn tot 800 exemplaren per resourcegroep

Standaard kunt u maximaal 800 exemplaren van een resourcetype implementeren in elke resourcegroep. Sommige resourcetypen zijn echter uitgesloten van de limiet van 800 exemplaren. In dit artikel worden de Azure-resourcetypen vermeld die meer dan 800 exemplaren in een resourcegroep kunnen hebben. Alle andere typen resources zijn beperkt tot 800 exemplaren.

Voor sommige resourcetypen moet u contact opnemen met de ondersteuning om de limiet van 800 exemplaren te verwijderen. Deze resourcetypen worden vermeld in dit artikel.


## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

* resourceHealthAlertRules
* smartDetectorAlertRules

## <a name="microsoftautomation"></a>Microsoft.Automation

* automationAccounts

## <a name="microsoftazurestack"></a>Microsoft.AzureStack

* edgeSubscriptions
* linkedSubscriptions
* Registraties
* registrations/customerSubscriptions
* registraties/producten

## <a name="microsoftbotservice"></a>Microsoft.BotService

* botServices: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.

## <a name="microsoftcompute"></a>Microsoft.Compute

* Schijven
* Galeries
* galerieën/afbeeldingen
* galerieën/afbeeldingen/versies
* images
* momentopnamen
* virtualMachineScaleSets: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.
* virtualMachines

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

* containerGroups

## <a name="microsoftcontainerregistry"></a>Microsoft.ContainerRegistry

* registers/buildTasks
* registers/buildTasks/listSourceRepositoryProperties
* registers/buildTasks/steps
* registers/buildTasks/steps/listBuildArguments
* registers/eventGridFilters
* registers/replicaties
* registers/taken
* registers/webhooks

## <a name="microsoftd365customerinsights"></a>Microsoft.D365CustomerInsights

* Exemplaren

## <a name="microsoftdbformariadb"></a>Microsoft.DBforMariaDB

* Servers

## <a name="microsoftdbformysql"></a>Microsoft.DBforMySQL

* flexibleServers
* Servers

## <a name="microsoftdbforpostgresql"></a>Microsoft.DBforPostgreSQL

* flexibleServers
* serverGroups
* serverGroupsv2
* Servers
* serversv2

## <a name="microsoftdevtestlab"></a>Microsoft.DevTestLab

* Planningen

## <a name="microsoftenterpriseknowledgegraph"></a>Microsoft.EnterpriseKnowledgeGraph

* services

## <a name="microsofteventhub"></a>Microsoft.EventHub

* Clusters
* Naamruimten

## <a name="microsoftexperimentation"></a>Microsoft.Experimentation

* experimentWorkspaces

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

* autoManagedVmConfigurationProfiles
* configurationProfileAssignments
* guestConfigurationAssignments
* Software
* softwareUpdateProfile
* softwareUpdates

## <a name="microsofthybridcompute"></a>Microsoft.HybridCompute

* machines - ondersteunt maximaal 5000 exemplaren
* machines/extensies - ondersteunt een onbeperkt aantal VM-extensie-exemplaren

## <a name="microsoftinsights"></a>microsoft.insights

* metricalerts

## <a name="microsoftlogic"></a>Microsoft.Logic

* integrationAccounts
* Werkstromen

## <a name="microsoftmedia"></a>Microsoft.Media

* mediaservices/liveEvents

## <a name="microsoftnetapp"></a>Microsoft.NetApp

* netAppAccounts
* netAppAccounts/capacityPools
* netAppAccounts/capacityPools/volumes
* netAppAccounts/capacityPools/volumes/mountTargets
* netAppAccounts/capacityPools/volumes/snapshots
* netAppAccounts/volumeGroups

## <a name="microsoftnetwork"></a>Microsoft.Network

* applicationGatewayWebApplicationFirewallPolicies
* applicationSecurityGroups
* bastionHosts
* ddosProtectionPlans
* dnszones
* dnszones/A
* dnszones/AAAA
* dnszones/CAA
* dnszones/CNAME
* dnszones/MX
* dnszones/NS
* dnszones/PTR
* dnszones/SOA
* dnszones/SRV
* dnszones/TXT
* dnszones/all
* dnszones/recordsets
* networkIntentPolicies
* networkInterfaces
* privateDnsZones
* privateDnsZones/A
* privateDnsZones/AAAA
* privateDnsZones/CNAME
* privateDnsZones/MX
* privateDnsZones/PTR
* privateDnsZones/SOA
* privateDnsZones/SRV
* privateDnsZones/TXT
* privateDnsZones/all
* privateDnsZones/virtualNetworkLinks
* privateEndpoints
* privateLinkServices
* publicIPAddresses: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.
* serviceEndpointPolicies
* trafficmanagerprofiles
* virtualNetworkTaps

## <a name="microsoftportalsdk"></a>Microsoft.PortalSdk

* rootResources

## <a name="microsoftpowerbi"></a>Microsoft.PowerBI

* workspaceCollections: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.

## <a name="microsoftpowerbidedicated"></a>Microsoft.PowerBIDedicated

* autoScaleVCores: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.
* capaciteiten: standaard beperkt tot 800 exemplaren. Deze limiet kan worden verhoogd door contact op te nemen met de ondersteuning.

## <a name="microsoftrelay"></a>Microsoft.Relay

* Naamruimten

## <a name="microsoftscheduler"></a>Microsoft.Scheduler

* jobcollections

## <a name="microsoftservicebus"></a>Microsoft.ServiceBus

* Naamruimten

## <a name="microsoftsingularity"></a>Microsoft.Singularity

* accounts
* accounts/accountQuotaPolicies
* accounts/groupPolicies
* accounts/taken
* accounts/storageContainers

## <a name="microsoftstorage"></a>Microsoft.Storage

* storageAccounts

## <a name="microsoftsql"></a>Microsoft.Sql

* servers/databases

## <a name="microsoftweb"></a>Microsoft.Web

* apiManagementAccounts/apis
* sites

## <a name="next-steps"></a>Volgende stappen

Zie Limieten, quota's en beperkingen voor Azure-abonnementen en -service voor een volledige lijst [met quota en limieten.](azure-subscription-service-limits.md)
