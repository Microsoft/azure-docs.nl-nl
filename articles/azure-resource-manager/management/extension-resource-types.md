---
title: Resourcetypen van extensies
description: Een lijst met de Azure-resource typen wordt gebruikt om de mogelijkheden van andere resource typen uit te breiden.
ms.topic: conceptual
ms.date: 11/14/2020
ms.openlocfilehash: 5561c480dd5a2849588ed2288eb5bcc35fc1446c
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658448"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resource typen die de mogelijkheden van andere resources uitbreiden

Een extensie resource is een resource die wordt toegevoegd aan de mogelijkheden van een andere resource. Resource vergrendeling is bijvoorbeeld een uitbreidings resource. U past een resource vergrendeling toe op een andere resource om te voor komen dat deze wordt verwijderd of gewijzigd. Het is niet verstandig een resource vergrendeling zelf te maken. Een extensie resource wordt altijd toegepast op een andere resource.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Micro soft. Advisor/configuraties
- Micro soft. Advisor/aanbevelingen
- Micro soft. Advisor/onderdrukkingen

## <a name="microsoftalertsmanagement"></a>Micro soft. AlertsManagement

- Micro soft. AlertsManagement/Alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Micro soft. Authorization/denyAssignments
- Micro soft. autorisatie/vergren delingen
- Microsoft.Authorization/policyAssignments
- Micro soft. Authorization/policyDefinitions
- Micro soft. Authorization/policyExemptions
- Micro soft. Authorization/policySetDefinitions
- Micro soft. Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignments
- Micro soft. Authorization/roleDefinitions

## <a name="microsoftautomanage"></a>Micro soft. automanage

- Micro soft. automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Micro soft. facturering/billingPeriods
- Micro soft. facturering/billingPermissions
- Micro soft. facturering/billingRoleAssignments
- Micro soft. facturering/billingRoleDefinitions
- Micro soft. facturering/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Micro soft. blauw druk

- Micro soft. blauw druk/blueprintAssignments
- Micro soft. blauw druk/blauw drukken

## <a name="microsoftconsumption"></a>Micro soft. verbruik

- Micro soft. verbruik/AggregatedCost
- Micro soft. verbruik/saldi
- Micro soft. verbruik/budgetten
- Micro soft. verbruik/kosten
- Micro soft. verbruik/CostTags
- Micro soft. verbruik/tegoed
- Micro soft. verbruik/gebeurtenissen
- Micro soft. verbruik/prognoses
- Micro soft. verbruik/loten
- Micro soft. verbruik/markt plaatsen
- Micro soft. verbruik/Pricesheets
- Micro soft. verbruik/producten
- Micro soft. verbruik/ReservationDetails
- Micro soft. verbruik/ReservationRecommendationDetails
- Micro soft. verbruik/ReservationRecommendations
- Micro soft. verbruik/ReservationSummaries
- Micro soft. verbruik/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Micro soft. ContainerInstance

- Micro soft. ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Micro soft. CostManagement

- Micro soft. CostManagement/Alerts
- Micro soft. CostManagement/budgetten
- Micro soft. CostManagement/Dimensions
- Micro soft. CostManagement/exports
- Micro soft. CostManagement/ExternalSubscriptions
- Micro soft. CostManagement/Forecast
- Micro soft. CostManagement/Insights
- Micro soft. CostManagement/query
- Micro soft. CostManagement/Reportconfigs
- Micro soft. CostManagement/Reports
- Micro soft. CostManagement/views

## <a name="microsoftcustomproviders"></a>Micro soft. CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsofteventgrid"></a>Micro soft. EventGrid

- Micro soft. EventGrid/eventSubscriptions
- Micro soft. EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Micro soft. GuestConfiguration/configurationProfileAssignments
- Micro soft. GuestConfiguration/guestConfigurationAssignments
- Micro soft. GuestConfiguration/software

## <a name="microsoftinsights"></a>micro soft. Insights

- micro soft. Insights/basis lijn
- micro soft. Insights/dataCollectionRuleAssociations
- micro soft. Insights/diagnosticSettings
- micro soft. Insights/diagnosticSettingsCategories
- micro soft. Insights/eventtypes
- micro soft. Insights/extendedDiagnosticSettings
- micro soft. Insights/guestDiagnosticSettingsAssociation
- micro soft. Insights/logDefinitions
- micro soft. Insights/logboeken
- micro soft. Insights/metricbaselines
- micro soft. Insights/metricDefinitions
- micro soft. Insights/metricNamespaces
- micro soft. Insights/metrische gegevens
- micro soft. Insights/myWorkbooks
- micro soft. Insights/topologie
- micro soft. Insights/trans acties

## <a name="microsoftkubernetesconfiguration"></a>Micro soft. KubernetesConfiguration

- Micro soft. KubernetesConfiguration/Extensions
- Micro soft. KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Micro soft. onderhoud

- Micro soft. Maintenance/applyUpdates
- Micro soft. Maintenance/configurationAssignments
- Micro soft. onderhoud/updates

## <a name="microsoftmanagedidentity"></a>Micro soft. ManagedIdentity

- Micro soft. ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Micro soft. ManagedServices

- Micro soft. ManagedServices/registrationAssignments
- Micro soft. ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Micro soft. OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Micro soft. OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Micro soft. PolicyInsights/verklaringen
- Micro soft. PolicyInsights/policyEvents
- Micro soft. PolicyInsights/policyStates
- Micro soft. PolicyInsights/policyTrackedResources
- Micro soft. PolicyInsights/herstel bewerkingen

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Micro soft. Recovery Services/backupProtectedItems
- Micro soft. Recovery Services/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Micro soft. ResourceHealth/childResources
- Micro soft. ResourceHealth/Events
- Micro soft. ResourceHealth/impactedResources
- Micro soft. ResourceHealth/meldingen

## <a name="microsoftresources"></a>Microsoft.Resources

- Micro soft. resources/koppelingen
- Micro soft. resources/Tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Micro soft. Security/adaptiveNetworkHardenings
- Micro soft. Security/advancedThreatProtectionSettings
- Micro soft. Security/assessmentMetadata
- Micro soft. Security/beoordelingen
- Micro soft. Security/Compliances
- Micro soft. Security/dataCollectionAgents
- Micro soft. Security/apparaten
- Micro soft. Security/deviceSecurityGroups
- Micro soft. Security/InformationProtectionPolicies
- Micro soft. Security/iotSensors
- Micro soft. Security/jitPolicies
- Micro soft. Security/serverVulnerabilityAssessments
- Micro soft. Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Micro soft. SecurityInsights

- Micro soft. SecurityInsights/aggregaties
- Micro soft. SecurityInsights/alertRules
- Micro soft. SecurityInsights/alertRuleTemplates
- Micro soft. SecurityInsights/automationRules
- Micro soft. SecurityInsights/blad wijzers
- Micro soft. SecurityInsights/cases
- Micro soft. SecurityInsights/dataConnectors
- Micro soft. SecurityInsights/dataConnectorsCheckRequirements
- Micro soft. SecurityInsights/entities
- Micro soft. SecurityInsights/incidenten
- Micro soft. SecurityInsights/Settings
- Micro soft. SecurityInsights/threatIntelligence
- Micro soft. SecurityInsights/Watchlists

## <a name="microsoftserialconsoleppe"></a>Micro soft. SerialConsole. BESCHERMINGs

- Micro soft. SerialConsole. BESCHERMINGs-serialPorts

## <a name="microsoftsoftwareplan"></a>Micro soft. SoftwarePlan

- Micro soft. SoftwarePlan/hybridUseBenefits

## <a name="microsoftsupport"></a>micro soft. ondersteuning

- micro soft. support/Supporttickets

## <a name="microsoftworkloadmonitor"></a>Micro soft. WorkloadMonitor

- Micro soft. WorkloadMonitor/-onderdelen
- Micro soft. WorkloadMonitor/monitorInstances
- Micro soft. WorkloadMonitor/monitors
- Micro soft. WorkloadMonitor/notificationSettings

## <a name="next-steps"></a>Volgende stappen

- Gebruik de [extensionResourceId](../templates/template-functions-resource.md#extensionresourceid)om de resource-id voor een extensie bron in een Azure Resource Manager sjabloon op te halen.
- Zie [Event grid gebeurtenis abonnementen](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)voor een voor beeld van het maken van een extensie bron in een sjabloon.
