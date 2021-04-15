---
title: Resourcetypen van extensies
description: Een lijst met de Azure-resourcetypen die worden gebruikt om de mogelijkheden van andere resourcetypen uit te breiden.
ms.topic: conceptual
ms.date: 04/12/2021
ms.openlocfilehash: 7085c0894fbf3bd56b56e4187d56f9af54916962
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: nl-NL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363985"
---
# <a name="resource-types-that-extend-capabilities-of-other-resources"></a>Resourcetypen die mogelijkheden van andere resources uitbreiden

Een extensieresource is een resource die wordt toegevoegd aan de mogelijkheden van een andere resource. Resourcevergrendeling is bijvoorbeeld een extensieresource. U kunt een resourcevergrendeling toepassen op een andere resource om te voorkomen dat deze wordt verwijderd of gewijzigd. Het is niet logisch om zelf een resourcevergrendeling te maken. Een extensieresource wordt altijd toegepast op een andere resource.

## <a name="microsoftadvisor"></a>Microsoft.Advisor

- Microsoft.Advisor/configurations
- Microsoft.Advisor/recommendations
- Microsoft.Advisor/suppressions

## <a name="microsoftalertsmanagement"></a>Microsoft.AlertsManagement

- Microsoft.AlertsManagement/alerts

## <a name="microsoftauthorization"></a>Microsoft.Authorization

- Microsoft.Authorization/denyAssignments
- Microsoft.Authorization/locks
- Microsoft.Authorization/policyAssignments
- Microsoft.Authorization/policyDefinitions
- Microsoft.Authorization/policyExemptions
- Microsoft.Authorization/policySetDefinitions
- Microsoft.Authorization/privateLinkAssociations
- Microsoft.Authorization/roleAssignmentApprovals
- Microsoft.Authorization/roleAssignments
- Microsoft.Authorization/roleAssignmentScheduleInstances
- Microsoft.Authorization/roleAssignmentScheduleRequests
- Microsoft.Authorization/roleAssignmentSchedules
- Microsoft.Authorization/roleDefinitions
- Microsoft.Authorization/roleEligibilityScheduleInstances
- Microsoft.Authorization/roleEligibilityScheduleRequests
- Microsoft.Authorization/roleEligibilitySchedules
- Microsoft.Authorization/roleManagementPolicies
- Microsoft.Authorization/roleManagementPolicyAssignments

## <a name="microsoftautomanage"></a>Microsoft.Automanage

- Microsoft.Automanage/configurationProfileAssignments

## <a name="microsoftbilling"></a>Microsoft.Billing

- Microsoft.Billing/billingPeriods
- Microsoft.Billing/billingPermissions
- Microsoft.Billing/billingRoleAssignments
- Microsoft.Billing/billingRoleDefinitions
- Microsoft.Billing/createBillingRoleAssignment

## <a name="microsoftblueprint"></a>Microsoft.Blueprint

- Microsoft.Blueprint/blueprintAssignments
- Microsoft.Blueprint/blueprints

## <a name="microsoftcapacity"></a>Microsoft.Capacity

- Microsoft.Capacity/listSkus

## <a name="microsoftchangeanalysis"></a>Microsoft.ChangeAnalysis

- Microsoft.ChangeAnalysis/changes

## <a name="microsoftconsumption"></a>Microsoft.Consumption

- Microsoft.Consumption/AggregatedCost
- Microsoft.Consumption/Balances
- Microsoft.Consumption/Budgets
- Microsoft.Consumption/Charges
- Microsoft.Consumption/CostTags
- Microsoft.Consumption/credits
- Microsoft.Consumption/events
- Microsoft.Consumption/Forecasts
- Microsoft.Consumption/lots
- Microsoft.Consumption/Marketplaces
- Microsoft.Consumption/Pricesheets
- Microsoft.Consumption/products
- Microsoft.Consumption/ReservationDetails
- Microsoft.Consumption/ReservationRecommendationDetails
- Microsoft.Consumption/ReservationRecommendations
- Microsoft.Consumption/ReservationSummaries
- Microsoft.Consumption/ReservationTransactions

## <a name="microsoftcontainerinstance"></a>Microsoft.ContainerInstance

- Microsoft.ContainerInstance/serviceAssociationLinks

## <a name="microsoftcostmanagement"></a>Microsoft.CostManagement

- Microsoft.CostManagement/Alerts
- Microsoft.CostManagement/Budgets
- Microsoft.CostManagement/CheckNameAvailability
- Microsoft.CostManagement/Dimensions
- Microsoft.CostManagement/Exports
- Microsoft.CostManagement/ExternalSubscriptions
- Microsoft.CostManagement/Forecast
- Microsoft.CostManagement/GenerateDetailedCostReport
- Microsoft.CostManagement/Insights
- Microsoft.CostManagement/OperationResults
- Microsoft.CostManagement/OperationStatus
- Microsoft.CostManagement/Query
- Microsoft.CostManagement/Reportconfigs
- Microsoft.CostManagement/Reports
- Microsoft.CostManagement/ScheduledActions
- Microsoft.CostManagement/Views

## <a name="microsoftcustomproviders"></a>Microsoft.CustomProviders

- Microsoft.CustomProviders/associations

## <a name="microsoftdatamigration"></a>Microsoft.DataMigration

- Microsoft.DataMigration/DatabaseMigrations

## <a name="microsofteventgrid"></a>Microsoft.EventGrid

- Microsoft.EventGrid/eventSubscriptions
- Microsoft.EventGrid/extensionTopics

## <a name="microsoftguestconfiguration"></a>Microsoft.GuestConfiguration

- Microsoft.GuestConfiguration/configurationProfileAssignments
- Microsoft.GuestConfiguration/guestConfigurationAssignments
- Microsoft.GuestConfiguration/software

## <a name="microsoftinsights"></a>microsoft.insights

- microsoft.insights/baseline
- microsoft.insights/dataCollectionRuleAssociations
- microsoft.insights/diagnosticSettings
- microsoft.insights/diagnosticSettingsCategories
- microsoft.insights/eventtypes
- microsoft.insights/extendedDiagnosticSettings
- microsoft.insights/guestDiagnosticSettingsAssociation
- microsoft.insights/logDefinitions
- microsoft.insights/logs
- microsoft.insights/metricbaselines
- microsoft.insights/metricDefinitions
- microsoft.insights/metricNamespaces
- microsoft.insights/metrics
- microsoft.insights/myWorkbooks
- microsoft.insights/topology
- microsoft.insights/transactions

## <a name="microsoftkubernetesconfiguration"></a>Microsoft.KubernetesConfiguration

- Microsoft.KubernetesConfiguration/extensions
- Microsoft.KubernetesConfiguration/sourceControlConfigurations

## <a name="microsoftmaintenance"></a>Microsoft.Maintenance

- Microsoft.Maintenance/applyUpdates
- Microsoft.Maintenance/configurationAssignments
- Microsoft.Maintenance/updates

## <a name="microsoftmanagedidentity"></a>Microsoft.ManagedIdentity

- Microsoft.ManagedIdentity/Identities

## <a name="microsoftmanagedservices"></a>Microsoft.ManagedServices

- Microsoft.ManagedServices/registrationAssignments
- Microsoft.ManagedServices/registrationDefinitions

## <a name="microsoftoperationalinsights"></a>Microsoft.OperationalInsights

- Microsoft.OperationalInsights/storageInsightConfigs

## <a name="microsoftoperationsmanagement"></a>Microsoft.OperationsManagement

- Microsoft.OperationsManagement/managementassociations

## <a name="microsoftpolicyinsights"></a>Microsoft.PolicyInsights

- Microsoft.PolicyInsights/attestations
- Microsoft.PolicyInsights/eventGridFilters
- Microsoft.PolicyInsights/policyEvents
- Microsoft.PolicyInsights/policyStates
- Microsoft.PolicyInsights/policyTrackedResources
- Microsoft.PolicyInsights/remediations

## <a name="microsoftrecoveryservices"></a>Microsoft.RecoveryServices

- Microsoft.RecoveryServices/backupProtectedItems
- Microsoft.RecoveryServices/replicationEligibilityResults

## <a name="microsoftresourcehealth"></a>Microsoft.ResourceHealth

- Microsoft.ResourceHealth/childResources
- Microsoft.ResourceHealth/events
- Microsoft.ResourceHealth/impactedResources

## <a name="microsoftresources"></a>Microsoft.Resources

- Microsoft.Resources/links
- Microsoft.Resources/tags

## <a name="microsoftsecurity"></a>Microsoft.Security

- Microsoft.Security/adaptiveNetworkHardenings
- Microsoft.Security/advancedThreatProtectionSettings
- Microsoft.Security/assessmentMetadata
- Microsoft.Security/assessments
- Microsoft.Security/Compliances
- Microsoft.Security/dataCollectionAgents
- Microsoft.Security/devices
- Microsoft.Security/deviceSecurityGroups
- Microsoft.Security/InformationProtectionPolicies
- Microsoft.Security/insights
- Microsoft.Security/iotAlerts
- Microsoft.Security/iotRecommendations
- Microsoft.Security/iotSensors
- Microsoft.Security/iotSites
- Microsoft.Security/jitPolicies
- Microsoft.Security/serverVulnerabilityAssessments
- Microsoft.Security/sqlVulnerabilityAssessments

## <a name="microsoftsecurityinsights"></a>Microsoft.SecurityInsights

- Microsoft.SecurityInsights/aggregaties
- Microsoft.SecurityInsights/alertRules
- Microsoft.SecurityInsights/alertRuleTemplates
- Microsoft.SecurityInsights/automationRules
- Microsoft.SecurityInsights/bookmarks
- Microsoft.SecurityInsights/cases
- Microsoft.SecurityInsights/dataConnectors
- Microsoft.SecurityInsights/dataConnectorsCheckRequirements
- Microsoft.SecurityInsights/enrichment
- Microsoft.SecurityInsights/entiteiten
- Microsoft.SecurityInsights/entityQueryTemplates
- Microsoft.SecurityInsights/incidents
- Microsoft.SecurityInsights/settings
- Microsoft.SecurityInsights/threatIntelligence
- Microsoft.SecurityInsights/watchlists

## <a name="microsoftserialconsole"></a>Microsoft.SerialConsole

- Microsoft.SerialConsole/serialPorts

## <a name="microsoftservicelinker"></a>Microsoft.ServiceLinker

- Microsoft.ServiceLinker/linkers

## <a name="microsoftsoftwareplan"></a>Microsoft.SoftwarePlan

- Microsoft.SoftwarePlan/hybridUseBenefits

## <a name="microsoftsubscription"></a>Microsoft.Subscription

- Microsoft.Subscription/policies

## <a name="microsoftsupport"></a>microsoft.support

- microsoft.support/supporttickets

## <a name="microsoftworkloadmonitor"></a>Microsoft.WorkloadMonitor

- Microsoft.WorkloadMonitor/monitors

## <a name="next-steps"></a>Volgende stappen

- Als u de resource-id voor een extensieresource in een Azure Resource Manager sjabloon wilt op halen, gebruikt u [de extensionResourceId](../templates/template-functions-resource.md#extensionresourceid).
- Zie Gebeurtenisabonnementen voor een voorbeeld van het maken van een extensieresource in [Event Grid sjabloon.](/azure/templates/microsoft.eventgrid/2019-06-01/eventsubscriptions)
